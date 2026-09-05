# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23
ARG OTELCOL_VERSION=0.160.0
ARG SPIFFE_HELPER_VERSION=0.11.0

FROM ghcr.io/spiffe/spiffe-helper:${SPIFFE_HELPER_VERSION} AS spiffe-helper
FROM otel/opentelemetry-collector-contrib:${OTELCOL_VERSION} AS otelcol

FROM ${BUILD_FROM}

ARG BUILD_ARCH
ARG SPIRE_VERSION=1.15.3

RUN apk add --no-cache wget
RUN set -eux; \
    case "${BUILD_ARCH}" in \
    amd64) spire_arch=amd64 ;; \
    aarch64) spire_arch=arm64 ;; \
    *) echo "Unsupported architecture: ${BUILD_ARCH}" >&2; exit 1 ;; \
    esac; \
    archive="spire-${SPIRE_VERSION}-linux-${spire_arch}-musl.tar.gz"; \
    checksum="spire-${SPIRE_VERSION}-linux-${spire_arch}-musl_sha256sum.txt"; \
    base_url="https://github.com/spiffe/spire/releases/download/v${SPIRE_VERSION}"; \
    wget -q "${base_url}/${archive}"; \
    wget -q "${base_url}/${checksum}"; \
    sha256sum -c "${checksum}"; \
    tar -xzf "${archive}"; \
    install -m 0755 "spire-${SPIRE_VERSION}/bin/spire-agent" /usr/local/bin/spire-agent; \
    rm -rf "${archive}" "${checksum}" "spire-${SPIRE_VERSION}"

COPY --from=spiffe-helper /spiffe-helper /usr/local/bin/spiffe-helper
COPY --from=otelcol /otelcol-contrib /usr/local/bin/otelcol-contrib
COPY rootfs /
