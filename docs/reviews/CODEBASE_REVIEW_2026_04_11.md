# Codebase Security & Quality Audit

**Repository:** addon-netbird (Home Assistant Add-on for NetBird VPN)
**Date:** 2026-04-11
**Scope:** Full audit -- security, code quality, CI/CD, container, AppArmor
**Prior Reviews:** None

---

## Executive Summary

This audit examined the NetBird Home Assistant add-on, which packages a WireGuard-based mesh VPN client into a Supervisor container with privileged network capabilities. The codebase is small (~10 substantive files) but handles secrets, privileged operations, and automated CI/CD.

**Finding counts:** 5 High, 11 Medium, 5 Low, 2 Informational (23 total)

The most critical issues are: **setup key exposure** in process list and logs (SEC-01), **GitHub Actions expression injection** vectors (SEC-02, CI-04), **unpinned CI actions** creating supply chain risk (SEC-03), and **AppArmor gaps** that silently break iptables-based routing (AA-03).

**Positive aspects:**
- Proper shell variable quoting throughout
- Version pinning for Docker images and Alpine packages
- Well-structured AppArmor profile (despite gaps)
- Excellent security controls in update-changelog.yaml workflow
- Minimal CI permissions on build job
- Correct S6-overlay service structure

---

## Findings Table

| ID | Sev | File | Line(s) | Description |
|----|-----|------|---------|-------------|
| SEC-01 | **H** | `netbird/rootfs/.../run` | 49, 103 | Setup key passed as CLI arg (visible in `ps`) and logged via options array |
| SEC-02 | **H** | `.github/workflows/builder.yaml` | 38-41 | Expression injection: `${{ steps.changed_files.outputs.all }}` in bash |
| SEC-03 | **H** | `builder.yaml`, `lint.yaml` | 32, 78, 25 | `home-assistant/actions@master` unpinned -- supply chain risk |
| CI-04 | **H** | `.github/workflows/builder.yaml` | 85-90 | Expression injection: `${{ steps.info.outputs.* }}` in build check step |
| AA-03 | **H** | `netbird/apparmor.txt` | -- | `/sbin/` not covered: iptables/xtables-nft-multi execution denied |
| SEC-04 | **M** | `netbird/rootfs/.../run` | 91 | Env var values logged in plaintext (may contain secrets) |
| SEC-05 | **M** | `netbird/config.yaml` | 33-36 | No URL/hostname format validation (bare `str` type) |
| SEC-06 | **M** | `netbird/config.yaml` + `run` | 40, 86 | env_vars: no name blocklist (PATH, LD_PRELOAD allowed) |
| SEC-07 | **M** | `netbird/Dockerfile` | 3 | No binary integrity verification (tag only, no digest) |
| CQ-01 | **M** | `netbird/rootfs/.../run` | 16 | Config migration `mv` -- no error handling, silent overwrite |
| CQ-02 | **M** | `netbird/rootfs/.../run` | 98-100 | `/etc/resolv.conf` rewrite non-atomic, no error checking |
| CQ-03 | **M** | `netbird/rootfs/.../run` | 104 | Missing `exec` before `netbird up` -- breaks signal delivery |
| AA-04 | **M** | `netbird/apparmor.txt` | -- | `/config/**` path missing -- NetBird state file access denied |
| AA-05 | **M** | `netbird/apparmor.txt` | -- | `network inet raw` / `network inet6 raw` missing for ICMP |
| AA-06 | **M** | `netbird/apparmor.txt` | -- | `network unix stream` missing for daemon socket |
| CI-05 | **M** | `.github/workflows/update-changelog.yaml` | 86, 115 | Expression injection pattern (mitigated by upstream regex validation) |
| CI-01 | **L** | `.github/workflows/builder.yaml` | 28 | `jitterbit/get-changed-files@v1` is archived/unmaintained |
| CI-02 | **L** | `.github/workflows/builder.yaml` | -- | No container vulnerability scanning in CI |
| CI-03 | **L** | `builder.yaml`, `lint.yaml` | -- | `init`/`find` jobs missing explicit `permissions` block |
| AA-01 | **L** | `netbird/apparmor.txt` | 21 | `/tmp/** rwk` broad but cannot be safely narrowed (accepted) |
| AA-07 | **L** | `netbird/apparmor.txt` | 17 | `finish` script path `/run/s6-linux-init-container-results/` not writable |
| AA-02 | **I** | `netbird/config.yaml` | 15-19 | Capabilities justified but threat model undocumented |
| CQ-04 | **I** | `netbird/rootfs/.../finish` | 6 | Unused `declare exit_code` variable (dead code) |

---

## Detailed Analysis

### SEC-01 [HIGH]: Setup Key Exposed via CLI Arg and Logs

**File:** `netbird/rootfs/etc/s6-overlay/s6-rc.d/netbird/run`, lines 49, 103

The setup key is passed as `--setup-key "${setup_key}"` (line 49), making it visible in `/proc/<pid>/cmdline` and `ps aux` to any process in the container or any add-on with `host_pid`. Line 48 claims "hidden for security" but line 103 logs the entire options array:

```bash
bashio::log.info "netbird up " "${options[@]}"
```

This dumps the setup key to HA logs, which are persistent and visible in the UI.

**Risk:** Any co-located add-on or SSH session can read the setup key, enabling unauthorized device registration.

**Fix:** NetBird officially supports `NB_SETUP_KEY` environment variable. Replace the CLI argument with an env var export and filter the log output:

```bash
# Replace lines 48-49:
export NB_SETUP_KEY="${setup_key}"

# Replace line 103:
bashio::log.info "Starting NetBird with ${#options[@]} options"
```

---

### SEC-02 [HIGH]: GitHub Actions Expression Injection (init job)

**File:** `.github/workflows/builder.yaml`, lines 38-41

Step outputs are interpolated directly into bash via `${{ }}`:

```yaml
for addon in ${{ steps.addons.outputs.addons }}; do
    if [[ "${{ steps.changed_files.outputs.all }}" =~ $addon ]]; then
```

Filenames from `jitterbit/get-changed-files` are attacker-controlled in PRs. A crafted filename containing shell metacharacters breaks out of the `[[` context.

**Risk:** Runner compromise on PR builds. Limited by read-only fork PR token, but escalates if workflow ever uses `pull_request_target`.

**Fix:** Use environment variables:

```yaml
env:
  ALL_CHANGED: ${{ steps.changed_files.outputs.all }}
  ALL_ADDONS: ${{ steps.addons.outputs.addons }}
run: |
  for addon in $ALL_ADDONS; do
    if [[ "$ALL_CHANGED" =~ $addon ]]; then
```

---

### SEC-03 [HIGH]: Unpinned GitHub Actions @master

**File:** `builder.yaml` lines 32, 78; `lint.yaml` line 25

Three references use `@master`:
- `home-assistant/actions/helpers/find-addons@master`
- `home-assistant/actions/helpers/info@master`

Any push to upstream `master` immediately affects all workflow runs. The `info` action runs in the `build` job which has `packages: write` permission.

**Fix:** Pin to commit SHA with date comment. Configure Renovate for `github-actions` manager with `pinDigests: true`.

---

### CI-04 [HIGH]: Expression Injection (build job check step)

**File:** `.github/workflows/builder.yaml`, lines 85-90

The `check` step interpolates `steps.info.outputs.image` and `steps.info.outputs.architectures` directly into bash. Since `home-assistant/actions/helpers/info@master` is unpinned (SEC-03), a compromised version could return values that inject shell commands in this step, which runs with `packages: write`.

**Fix:** Same pattern as SEC-02 -- pass through `env:` block.

---

### AA-03 [HIGH]: /sbin/ Binaries Not Executable Under AppArmor

**File:** `netbird/apparmor.txt`

The Dockerfile installs iptables and creates symlinks:

```dockerfile
ln -sf /sbin/xtables-nft-multi /sbin/ip6tables
ln -sf /sbin/xtables-nft-multi /sbin/iptables
```

The AppArmor profile covers `/bin/**` and `/usr/bin/**` but NOT `/sbin/**`. On HAOS hosts with AppArmor enforcement, NetBird's iptables-based routing rules silently fail.

**Fix:** Add to apparmor.txt:

```
/sbin/** ix,
/usr/sbin/** ix,
```

Or, for minimal scope:

```
/sbin/xtables-nft-multi ix,
/sbin/iptables ix,
/sbin/ip6tables ix,
```

---

### SEC-04 [MEDIUM]: Env Var Values Logged in Plaintext

**File:** `netbird/rootfs/.../run`, line 91

```bash
bashio::log.info "Setting ${name} to ${value}"
```

Users setting `NB_SETUP_KEY` or other credentials via `env_vars` have values logged.

**Fix:** Pattern-match sensitive names and redact:

```bash
case "${name}" in
    *KEY*|*SECRET*|*TOKEN*|*PASSWORD*)
        bashio::log.info "Setting ${name} to [REDACTED]" ;;
    *)
        bashio::log.info "Setting ${name} to ${value}" ;;
esac
```

---

### SEC-05 [MEDIUM]: No URL/Hostname Format Validation

**File:** `netbird/config.yaml`, lines 33-36

All four fields use bare `str` type. HA schema supports `url` and `match()`.

**Fix:**

```yaml
admin_url: url?
management_url: url?
setup_key: str?
hostname: match(^[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?$)?
```

**Caveat:** Verify `url` type accepts `grpc://` if self-hosted NetBird uses it.

---

### SEC-06 [MEDIUM]: env_vars No Name Blocklist

**File:** `netbird/config.yaml` line 40; `run` line 86

The regex `^[A-Z_][A-Z0-9_]*$` allows `PATH`, `LD_PRELOAD`, etc. Since the field is for NetBird-specific variables, enforce `NB_` prefix.

**Fix (config.yaml):**

```yaml
- name: match(^NB_[A-Z0-9_]+$)
```

**Fix (run script defense-in-depth):**

```bash
if [[ ! "${name}" =~ ^NB_[A-Z0-9_]+$ ]]; then
```

**Caveat:** Breaking change -- requires changelog mention.

---

### SEC-07 [MEDIUM]: No Binary Integrity Verification

**File:** `netbird/Dockerfile`, line 3

Tag-only reference `netbirdio/netbird:0.68.1` is mutable.

**Fix:** Pin by digest:

```dockerfile
FROM netbirdio/netbird:0.68.1@sha256:<digest> as netbird-container
```

Configure Renovate with `pinDigests: true` to keep it updated.

---

### CQ-01 [MEDIUM]: Config Migration No Error Handling

**File:** `netbird/rootfs/.../run`, line 16

```bash
[ -f "${CONFIG_OLD_PATH}" ] && mv "${CONFIG_OLD_PATH}" "${CONFIG_PATH}"
```

Silent overwrite if target exists; no error handling on `mv` failure.

**Fix:**

```bash
if [ -f "${CONFIG_OLD_PATH}" ]; then
    if [ -f "${CONFIG_PATH}" ]; then
        bashio::log.warning "Migration skipped: ${CONFIG_PATH} already exists"
    else
        bashio::log.info "Migrating config from ${CONFIG_OLD_PATH} to ${CONFIG_PATH}"
        mv "${CONFIG_OLD_PATH}" "${CONFIG_PATH}" || bashio::exit.nok
    fi
fi
```

---

### CQ-02 [MEDIUM]: resolv.conf Rewrite Non-Atomic

**File:** `netbird/rootfs/.../run`, lines 98-100

The `>` truncates before `>>` appends. Kill between the two = broken DNS. On supervised restart, the damage compounds (duplicate headers, no nameservers).

**Fix:**

```bash
if ! grep -q '# systemd-resolved' /etc/resolv.conf 2>/dev/null; then
    RESOLV_TMP=$(mktemp /tmp/resolv.conf.XXXXXX)
    { printf '# systemd-resolved\n'; cat /etc/resolv.conf; } > "${RESOLV_TMP}" \
        && mv -f "${RESOLV_TMP}" /etc/resolv.conf \
        || bashio::log.error "Failed to update /etc/resolv.conf"
    rm -f "${RESOLV_TMP}" 2>/dev/null
fi
```

---

### CQ-03 [MEDIUM]: Missing `exec` Before `netbird up`

**File:** `netbird/rootfs/.../run`, line 104

Without `exec`, bash lingers as parent. S6 sends SIGTERM to bash (not netbird), which may not forward signals, leaving WireGuard interfaces dirty.

**Fix:**

```bash
exec netbird up "${options[@]}"
```

One-word fix with high correctness impact.

---

### AA-04 [MEDIUM]: /config/ Path Missing in AppArmor

**File:** `netbird/apparmor.txt`

The `run` script uses `/config/config.json` (CONFIG_PATH). The AppArmor profile has `/data/** rw` but HA Supervisor maps `addon_config` to `/config/`, not `/data/`.

**Fix:** Add to apparmor.txt:

```
/config/** rw,
```

---

### AA-05 [MEDIUM]: Raw Network Rules Missing

**File:** `netbird/apparmor.txt`

The `net_raw` capability is granted but `network inet raw` is not listed. AppArmor enforces both independently -- ICMP raw sockets will be denied.

**Fix:** Add:

```
network inet raw,
network inet6 raw,
```

---

### AA-06 [MEDIUM]: Unix Socket Rules Missing

**File:** `netbird/apparmor.txt`

NetBird CLI communicates with the daemon via Unix domain socket. No `network unix` rule is present.

**Fix:** Add:

```
network unix stream,
network unix dgram,
```

---

### CI-05 [MEDIUM]: update-changelog Expression Injection (Mitigated)

**File:** `.github/workflows/update-changelog.yaml`, lines 86, 115

Version and date are interpolated directly into bash. However, version is validated by `^[0-9]+\.[0-9]+\.[0-9]+$` regex and date comes from `gh api --jq`. Functionally safe but pattern is incorrect.

**Fix:** Use `env:` variables for correctness.

---

### CI-01 [LOW]: Archived Action

**File:** `.github/workflows/builder.yaml`, line 28

`jitterbit/get-changed-files@v1` is archived. No security patches.

**Fix:** Replace with native git diff or `dorny/paths-filter`.

---

### CI-02 [LOW]: No Vulnerability Scanning

No Trivy/Grype scan in CI. Built images with `NET_ADMIN` + `SYS_ADMIN` are pushed without CVE checks.

**Fix:** Add `aquasecurity/trivy-action` step after build.

---

### CI-03 [LOW]: Missing Explicit Permissions

`init` job in builder.yaml and both jobs in lint.yaml have no `permissions` block.

**Fix:** Add `permissions: { contents: read }` to all jobs.

---

### AA-01 [LOW]: /tmp/** Broad but Acceptable

Cannot be safely narrowed. Bash process substitution and `netbird` may use unpredictable temp paths.

**Status:** Reviewed, accepted risk.

---

### AA-07 [LOW]: finish Script Path Not Writable

The `finish` script writes to `/run/s6-linux-init-container-results/exitcode`. The profile covers `/run/{s6,s6-rc*,service}/**` with `ix` only, which doesn't match `s6-linux-init-container-results`.

**Fix:** Add: `/run/s6-linux-init-container-results/** rw,`

---

### AA-02 [INFO]: Capabilities Undocumented

All five capabilities (SYS_ADMIN, SYS_RESOURCE, NET_ADMIN, NET_RAW, BPF) plus host_network and host_dbus are justified but not documented. Add justification comments to DOCS.md.

---

### CQ-04 [INFO]: Dead Code in finish Script

Line 6: `declare exit_code` is declared but never used. Template remnant.

**Fix:** Remove the line.
