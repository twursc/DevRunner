# Devbox image derived from the DevRunner base stack
ARG BASE_IMAGE=ghcr.io/chaitin/monkeycode-runner/base:bookworm
FROM ${BASE_IMAGE}

LABEL org.opencontainers.image.title="DevRunner Devbox" \
      org.opencontainers.image.source="https://github.com/chaitin/DevRunner" \
      org.opencontainers.image.description="Devbox toolbox image for DevRunner stacks with common diagnostics"

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        htop \
        iputils-ping \
        iproute2 \
        wget \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace
