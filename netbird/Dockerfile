ARG BUILD_FROM=ghcr.io/hassio-addons/base:20.1.0

FROM netbirdio/netbird:0.70.3 as netbird-container

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

RUN apk add --no-cache \
    iptables=1.8.11-r1 \
    nftables=1.1.5-r2 \
    && ln -sf /sbin/xtables-nft-multi /sbin/ip6tables \
    && ln -sf /sbin/xtables-nft-multi /sbin/iptables

# Copy root filesystem
COPY rootfs /
COPY --from=netbird-container /usr/local/bin/netbird /usr/local/bin/netbird

# Build arguments
ARG BUILD_VERSION
ARG BUILD_ARCH
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
    maintainer="Levente Farkas <lfarkas@lfarkas.org>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Levente Farkas <lfarkas@lfarkas.org>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
