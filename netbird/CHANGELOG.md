# Changelog

## [v0.69.0] - 2026-04-20

### Changed
- Updated to NetBird v0.69.0

### Upstream Release Notes
## Release Notes for v0.69.0

### What's New
**Reverse Proxy IP Reputation Integration** 
Now you can use CrowdSec to block malicious traffic based on IP reputation on your exposed service in the reverse proxy.

This feature requires self-hosted installations to add another container to their deployment. See instructions in the [reverse proxy migration documentation](https://docs.netbird.io/selfhosted/migration/enable-reverse-proxy#step-7-optional-enable-crowd-sec-ip-reputation).

> For Cloud users, support is coming soon.

Learn more about [here](https://docs.netbird.io/docs/guides/reverse-proxy/).


**macOS p2p connectivity improvements**
We've improved macOS p2p connectivity with a better routing exclusion mechanism to avoid loops. Now the client doesn't add /32 routes per remote candidate addresses avoiding limitations on accessing remote peer's local addresses via tunnel connections. Learn more about [this change](https://github.com/netbirdio/netbird/pull/5918).
> To use the old behavior run:
> 
> `sudo netbird service reconfigure --service-env "NB_USE_LEGACY_ROUTING=true"`

#### Client Improvements
- Added **PCP support**.  This change adds support for the PCP protocol to the client to improve the rate of P2P connectivity. 
  https://github.com/netbirdio/netbird/pull/5219
- Added **--disable-networks flag** to block network selection for users.  
  https://github.com/netbirdio/netbird/pull/5896
- Fixed **clearing service env vars with --service-env ""**.  
  https://github.com/netbirdio/netbird/pull/5893
- Guarded against **container DNAT bypass of ACL rules in iptables**.  
  https://github.com/netbirdio/netbird/pull/5697
- Populated **NetworkAddresses on iOS for posture checks**.  
  https://github.com/netbirdio/netbird/pull/5900
- Reconnected **conntrack netlink listener on error**.  
  https://github.com/netbirdio/netbird/pull/5885
- Replaced **exclusion routes with scoped default + IP_BOUND_IF on macOS**.  
  https://github.com/netbirdio/netbird/pull/5918
- Fixed **incorrect SSH client config combining Host and Match directives**.  
  https://github.com/netbirdio/netbird/pull/5903
- Fixed **WGIface.Close deadlock when DNS filter hook re-enters GetDevice**.  
  https://github.com/netbirdio/netbird/pull/5916

#### Management Improvements
- Enforced **peer or peer groups requirement for network routers**.  
  https://github.com/netbirdio/netbird/pull/5894
- Reused **single cache store across all management server consumers**.  
  https://github.com/netbirdio/netbird/pull/5889
- Fixed **lint error on Google Workspace integration**.  
  https://github.com/netbirdio/netbird/pull/5907

#### Proxy Enhancements
- Added **CrowdSec IP reputation integration for reverse proxy**.  
  https://github.com/netbirdio/netbird/pull/5722
- Added **direct redirect to SSO**.  
  https://github.com/netbirdio/netbird/pull/5874

#### Infrastructure Improvements
- Updated **sign pipeline version to v0.1.2**.  
  https://github.com/netbirdio/netbird/pull/5884
- Added **CrowdSec LAPI container to self-hosted setup script**.  
  https://github.com/netbirdio/netbird/pull/5880

### New Contributors
- @MichaelUray made their first contribution in https://github.com/netbirdio/netbird/pull/5900
- @jnfrati made their first contribution in https://github.com/netbirdio/netbird/pull/5907

**Full Changelog**: https://github.com/netbirdio/netbird/compare/v0.68.3...v0.69.0
