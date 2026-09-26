ARG BUILD_FROM

# Official multi-arch Grafana Alloy image, pinned by tag and index digest.
# To update: change the tag and digest together, bump config.yaml's version,
# and add a CHANGELOG entry.
FROM grafana/alloy:v1.19.2@sha256:b8ec653c44235fbe910879145dac3597d66b0aaecf60bcbbe82580767771a839 AS alloy

FROM ${BUILD_FROM}

# jq reads the add-on options; ca-certificates for an https Loki URL.
# Debian base: Alloy is glibc-linked and reads the journal via libsystemd.
RUN apt-get update && apt-get install -y --no-install-recommends \
    jq ca-certificates \
    && rm -rf /var/lib/apt/lists/*

COPY --from=alloy /bin/alloy /usr/bin/alloy

COPY rootfs /

# Set here rather than relying on git file modes.
RUN chmod a+x /etc/s6-overlay/s6-rc.d/*/run
