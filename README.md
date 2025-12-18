# DevRunner

Base container images for Chaitin MonkeyCode developer workflows.

## Base image (bookworm)
- Dockerfile: `docker/base/bookworm/Dockerfile` (Debian bookworm-slim, git/curl/build-essential/python3, en_US.UTF-8 locale, default user root).
- Build locally: `STACK=base VERSION=bookworm ./scripts/build.sh`
- Push to GHCR: `PUSH=true REGISTRY=ghcr.io/chaitin/monkeycode-runner STACK=base VERSION=bookworm ./scripts/build.sh` (needs `docker login ghcr.io`).
- Override apt mirrors by setting `DEBIAN_MIRROR` / `DEBIAN_SECURITY_MIRROR` before building.
- Example (TUNA mirror in mainland China):

  ```bash
  DEBIAN_MIRROR=https://mirrors.tuna.tsinghua.edu.cn/debian \
  DEBIAN_SECURITY_MIRROR=https://mirrors.tuna.tsinghua.edu.cn/debian-security \
  STACK=base VERSION=bookworm ./scripts/build.sh
- Run: `docker run --rm -it ghcr.io/chaitin/monkeycode-runner/base:bookworm bash`

## Devbox image (bookworm)
- Dockerfile: `docker/devbox/bookworm/Dockerfile` (extends base image and adds diagnostic packages: htop, iputils-ping, iproute2 (ip/ss), wget).
- Build locally: `STACK=devbox VERSION=bookworm ./scripts/build.sh`
- Push to GHCR: `PUSH=true REGISTRY=ghcr.io/chaitin/monkeycode-runner STACK=devbox VERSION=bookworm ./scripts/build.sh`
- Run: `docker run --rm -it ghcr.io/chaitin/monkeycode-runner/devbox:bookworm htop`

## Frontend image (node20)
- Dockerfile: `docker/frontend/node20/Dockerfile` (extends base image with Node.js 20 and Corepack for frontend tooling).
- Build locally: `STACK=frontend VERSION=node20 ./scripts/build.sh`
- Push to GHCR: `PUSH=true REGISTRY=ghcr.io/chaitin/monkeycode-runner STACK=frontend VERSION=node20 ./scripts/build.sh`
- Run: `docker run --rm -it ghcr.io/chaitin/monkeycode-runner/frontend:node20 node --version`

## Go image (1.25-bookworm)
- Dockerfile: `docker/golang/1.25-bookworm/Dockerfile` (extends the base image, installs Go 1.25.5 plus `staticcheck`, `gofumpt`, `swag`, `golangci-lint`).
- Build locally: `STACK=golang VERSION=1.25-bookworm ./scripts/build.sh`
- Push to GHCR: `PUSH=true REGISTRY=ghcr.io/chaitin/monkeycode-runner STACK=golang VERSION=1.25-bookworm ./scripts/build.sh`
- Run: `docker run --rm -it ghcr.io/chaitin/monkeycode-runner/golang:1.25-bookworm bash`

## Rust image (1.91-bookworm)
- Dockerfile: `docker/rust/1.91-bookworm/Dockerfile` (extends the base image, installs Rust 1.91.1 via the official tarball with checksum verification).
- Build locally: `STACK=rust VERSION=1.91-bookworm ./scripts/build.sh`
- Push to GHCR: `PUSH=true REGISTRY=ghcr.io/chaitin/monkeycode-runner STACK=rust VERSION=1.91-bookworm ./scripts/build.sh`
- Run: `docker run --rm -it ghcr.io/chaitin/monkeycode-runner/rust:1.91-bookworm rustc --version`

## Layout
- `docker/base/bookworm/Dockerfile` – base image definition.
- `docker/devbox/bookworm/Dockerfile` – devbox image with common diagnostic tools.
- `docker/frontend/node20/Dockerfile` – Node.js frontend developer image.
- `docker/golang/1.25-bookworm/Dockerfile` – Go developer image (bookworm + Go 1.25).
- `docker/rust/1.91-bookworm/Dockerfile` – Rust developer image (bookworm + Rust 1.91).
- `scripts/build.sh` – helper to build/push images (env-driven: STACK, VERSION, REGISTRY, PUSH).
- `docs/` – docs placeholder for future stacks and CI notes.

## CI/CD
- Workflow: `.github/workflows/ci.yaml`
  - PR: build only (no push).
  - Push to `main` branch: login to GHCR with `GITHUB_TOKEN` and push tags from metadata (`bookworm`, `latest`, branch/tag-derived). Non-main branches/tags build only. Target registry: `ghcr.io/chaitin/monkeycode-runner`.
- No personal access token needed; workflow requests `packages: write` and `contents: read` via `GITHUB_TOKEN` (ensure Actions permissions allow this if repository is restricted).
