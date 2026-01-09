# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Home Assistant add-on for NetBird, a WireGuard-based mesh VPN client. The add-on packages the NetBird client binary into a Home Assistant Supervisor add-on container.

## Repository Structure

```
addon-netbird/
├── netbird/                  # Add-on directory
│   ├── config.yaml           # Add-on configuration schema
│   ├── build.yaml            # Build configuration (base images per architecture)
│   ├── Dockerfile            # Multi-stage build pulling from netbirdio/netbird
│   ├── DOCS.md               # User documentation
│   ├── rootfs/               # Container filesystem overlay
│   │   └── etc/s6-overlay/s6-rc.d/netbird/
│   │       ├── run           # Main service script (netbird up)
│   │       └── finish        # Service exit handler
│   └── translations/en.yaml  # UI translations
├── repository.json           # Home Assistant add-on repository metadata
└── .github/workflows/
    ├── builder.yaml          # CI build workflow
    └── lint.yaml             # Add-on linting workflow
```

## Build System

The add-on uses the Home Assistant Builder system. Builds are triggered automatically via GitHub Actions when changes are pushed to `main` affecting monitored files: `build.yaml`, `config.yaml`, `Dockerfile`, or `rootfs/`.

### Local Testing

There is no local build command. The CI system uses `home-assistant/builder` action. For testing, push to a PR branch - the workflow builds with `--test` flag on PRs and publishes on merge to main.

## Key Files

- **netbird/config.yaml**: Defines add-on metadata, supported architectures, required privileges (NET_ADMIN, SYS_ADMIN, etc.), and user-configurable options schema
- **netbird/Dockerfile**: Copies `netbird` binary from upstream `netbirdio/netbird` image into `hassio-addons/base` image
- **netbird/rootfs/etc/s6-overlay/s6-rc.d/netbird/run**: Service startup script using bashio for config parsing, runs `netbird up` with options from Home Assistant config

## Configuration Options

User-configurable options (defined in config.yaml schema):
- `admin_url` / `management_url`: Custom NetBird server URLs
- `setup_key`: Registration key (optional if using login URL from logs)
- `hostname`: Custom peer hostname
- `rosenpass` / `rosenpass_permissive`: Post-quantum encryption settings
- `env_vars`: Additional `NB_*` environment variables
- `log_level`: trace/debug/info/notice/warning/error/fatal

## Linting

The CI runs `frenck/action-addon-linter` on all add-on directories. YAML linting follows rules in `.yamllint`.

## Version Updates

NetBird version is pinned in two places that must stay in sync:
1. `netbird/config.yaml` - `version` field
2. `netbird/Dockerfile` - `FROM netbirdio/netbird:<version>` tag

Renovate bot handles automated version updates via `.github/renovate.json`.
