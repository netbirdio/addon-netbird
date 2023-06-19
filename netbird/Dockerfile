ARG BUILD_FROM=ghcr.io/hassio-addons/base:14.0.2
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
ARG BUILD_ARCH=amd64
ARG BUILD_VERSION
# hadolint ignore=DL3003
RUN \
    apk add --no-cache \
    nftables=1.0.7-r2 \
    \
    && ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    && if [ "${BUILD_ARCH}" = "armhf" ]; then ARCH="armv6"; fi \
    && curl -L -s "https://github.com/netbirdio/netbird/releases/download/${BUILD_VERSION}/netbird_${BUILD_VERSION#v}_linux_${ARCH}.tar.gz" \
    | tar xzvpf - -C /usr/bin/ netbird
#    openresolv=3.13.1-r0 \
#    \
#    && ln -sf /sbin/xtables-nft-multi /sbin/ip6tables \
#    && ln -sf /sbin/xtables-nft-multi /sbin/iptables \

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Levente Farkas <lfarkas@lfarekas.org>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Levente Farkas <lfarkas@lfarekas.org>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
