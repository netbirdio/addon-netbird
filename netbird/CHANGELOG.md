# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [v0.59.13] - 2025-11-17

### Changed
- Updated to NetBird v0.59.13
- **BREAKING**: Removed support for armhf, armv7, and i386 architectures
- Only aarch64 and amd64 architectures are now supported

## [v0.54.2] - 2025-11-17

### Changed
- Updated to NetBird v0.54.2
- Improved security by masking sensitive setup key in logs
- Enhanced error handling in service startup script
- Better documentation for DNS resolver workaround

### Fixed
- Fixed inconsistent addon slug in documentation
- Fixed broken repository links
- Updated maintenance year to 2025
- Corrected default management URL across documentation

### Added
- AppArmor security profile
- Healthcheck configuration for monitoring addon status
- Improved file migration logic with better error handling
- Binary existence check before execution

### Security
- Removed sensitive credential logging
- Added AppArmor profile for enhanced security

## [Unreleased]

### Notes
- Based on hassio-addons base image 18.2.1
- Supports aarch64 and amd64 architectures only
- Requires privileged capabilities for VPN functionality
