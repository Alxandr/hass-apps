# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23

ARG SPIRE_VERSION=1.15.3
ARG OTELCOL_VERSION=0.160.0
ARG SPIFFE_HELPER_VERSION=0.11.0
ARG NODE_EXPORTER_VERSION=1.12.1

FROM ghcr.io/spiffe/spire-agent:${SPIRE_VERSION} AS spire-agent
FROM ghcr.io/spiffe/spiffe-helper:${SPIFFE_HELPER_VERSION} AS spiffe-helper
FROM otel/opentelemetry-collector-contrib:${OTELCOL_VERSION} AS otelcol
FROM quay.io/prometheus/node-exporter:v${NODE_EXPORTER_VERSION} AS node-exporter

FROM ${BUILD_FROM}

ARG BUILD_ARCH

COPY --from=spire-agent /opt/spire/bin/spire-agent /usr/local/bin/spire-agent
COPY --from=spiffe-helper /spiffe-helper /usr/local/bin/spiffe-helper
COPY --from=otelcol /otelcol-contrib /usr/local/bin/otelcol-contrib
COPY --from=node-exporter /bin/node_exporter /usr/local/bin/node_exporter
COPY rootfs /

RUN update-ca-certificates
