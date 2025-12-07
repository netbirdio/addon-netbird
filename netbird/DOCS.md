# Home Assistant Community Add-on: NetBird

[NetBird][netbird] is an open-source VPN management platform built on top of WireGuard® making it easy to create secure private networks for your organization or home.

It requires zero configuration effort leaving behind the hassle of opening ports, complex firewall rules, VPN gateways, and so forth.

NetBird uses NAT traversal techniques to automatically create an overlay peer-to-peer network connecting machines regardless of location (home, office, data center, container, cloud, or edge environments and now HASSIO with this add-on), unifying virtual private network management experience.

## Installation

The installation of this add-on is pretty straightforward and not different in
comparison to installing any other Home Assistant add-on.

1. Add my Hass.io add-ons repository (**<https://github.com/netbirdio/addon-netbird>**) to your Hass.io instance.
1. Click the Home Assistant My button below to open the add-on on your Home
   Assistant instance.

   [![Open this add-on in your Home Assistant instance.][addon-badge]][addon]

1. Install the "NetBird" add-on.
1. If you are using the central NetBird instance you can either use the URL generated in the log or you can use a `setup_key`. If hosting your own then you'll want to set `admin_url` & `management_url` and again only need to set up the `setup_key` if you don't want to login via the log generated URL.
1. If you would like to change the auto generated hostname (which is the docker container id in case of hassio) set the `hostname`.
1. If you would like to enable Rosenpass set the `rosenpass` to `true`.
1. If you would like to enable Rosenpass in permissive way set the `rosenpass_permissive` to true.
1. Start the "NetBird" add-on.
1. Feels free to check the logs for `NetBird` to make sure its booted correctly.
1. This client will show up in your NetBird dashboard.

## Configuration

You'll see the config file at `/addon_config/*_netbird/config.json` after first boot.

### Option: `log_level`

The `log_level` option controls the level of log output by the addon and can
be changed to be more or less verbose, which might be useful when you are
dealing with an unknown issue. Possible values are:

- `trace`: Show every detail, like all called internal functions.
- `debug`: Shows detailed debug information.
- `info`: Normal (usually) interesting events.
- `notice`: Normal but significant events.
- `warning`: Exceptional occurrences that are not errors.
- `error`: Runtime errors that do not require immediate action.
- `fatal`: Something went terribly wrong. Add-on becomes unusable.

Please note that each level automatically includes log messages from a
more severe level, e.g., `debug` also shows `info` messages. By default,
the `log_level` is set to `info`, which is the recommended setting unless
you are troubleshooting.

### Option: `admin_url`

Admin Panel URL [http|https]://[host]:[port] (default "<https://app.netbird.io>")

Tells NetBird the URL through which you administrate your NetBird clients.
You'll find that the log generates a login URL you can use instead of configuring a `setup_key`. (If you're not already connected to a NetBird instance.)

### Option: `management_url`

Management Service URL [http|https]://[host]:[port] (default "<https://api.netbird.io/>")

The client will use this URL to communicate with your NetBird instance api.

### Option: `setup_key`

Setup key obtained from the Management Service Dashboard (used to register peer)

This token is like a password for connecting your client to NetBird, you can leave this
option empty if you would prefer to login via a URL generated in the log with the `admin_url`.

### Option: `hostname`

Hostname in the NetBird network (used to during registration)

This hostname will be used in the Peers to identify your machine.

### Option: `rosenpass`

Rosenpass can be enabled by setting a flag on client start-up.

Rosenpass is a post-quantum secure key-exchange protocol that enhances WireGuard
VPNs against quantum computer attacks. It employs advanced cryptographic methods
Classic McEliece and Kyber.

### Option: `rosenpass_permissive`

Rosenpass can be enabled in permissive way.

Enabling Rosenpass on one peer assumes that all peers have Rosenpass enabled. If one of the peers does not enable this feature or run an older version that lacks Rosenpass, the connection won't work. To allow non-Rosenpass enabled peers to connect to a Rosenpass peer, the permissive mode can be activated. In this case, the NetBird client will default to a standard WireGuard connection without pre-shared keys for those connections that don't support Rosenpass. It will continue negotiating PSKs with Rosenpass for the rest, ensuring enhanced security wherever possible.

### Option: `env_vars`

Extra environment variables

Extra environment variables to pass to the NetBird client
This is a list of environment variables that will be passed to the NetBird client.
You can use this to configure the client further.

## Changelog & Releases

This repository keeps a change log using [GitHub's releases][releases]
functionality.

Releases are based on [Semantic Versioning][semver], and use the format
of `MAJOR.MINOR.PATCH`. In a nutshell, the version will be incremented
based on the following:

- `MAJOR`: Incompatible or major changes.
- `MINOR`: Backwards-compatible new features and enhancements.
- `PATCH`: Backwards-compatible bugfixes and package updates.

## Support

Got questions?

You have several options to get them answered:

- The [Home Assistant Community Add-ons Discord chat server][discord] for add-on
  support and feature requests.
- The [Home Assistant Discord chat server][discord-ha] for general Home
  Assistant discussions and questions.
- The Home Assistant [Community Forum][forum].
- Join the [Reddit subreddit][reddit] in [/r/homeassistant][reddit]

You could also [open an issue here][issue] GitHub.

## Authors & contributors

The original setup of this repository was by [Levente Farkas][lfarkas].

For a full list of all authors and contributors,
check [the contributor's page][contributors].

## License

MIT License

Copyright (c) 2019-2022 Franck Nijhof
Copyright (c) 2022-2023 Daniel Burgess
Copyright (c) 2023-2023 Levente Farkas

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[addon-badge]: https://my.home-assistant.io/badges/supervisor_addon.svg
[addon]: https://my.home-assistant.io/redirect/supervisor_addon/?addon=a0d7b954_netbird&repository_url=https%3A%2F%2Fgithub.com%2Fnetbirdio%2Faddon-netbird
[contributors]: https://github.com/netbirdio/addon-netbird/graphs/contributors
[discord-ha]: https://discord.gg/c5DvZ4e
[discord]: https://discord.me/hassioaddons
[forum]: https://community.home-assistant.io/t/repository-community-hass-io-add-ons/24705
[issue]: https://github.com/netbirdio/addon-netbird/issues
[reddit]: https://reddit.com/r/homeassistant
[releases]: https://github.com/netbirdio/addon-netbird/releases
[semver]: http://semver.org/spec/v2.0.0.html
[netbird]: https://github.com/netbirdio/netbird
[lfarkas]: https://github.com/lfarkas
