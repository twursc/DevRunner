# Flutter developer image derived from the DevRunner base stack
ARG BASE_IMAGE=ghcr.io/chaitin/monkeycode-runner/base:bookworm
FROM ${BASE_IMAGE}

LABEL org.opencontainers.image.title="DevRunner Flutter" \
      org.opencontainers.image.source="https://github.com/chaitin/DevRunner" \
      org.opencontainers.image.description="Flutter + Android toolbox image for DevRunner stacks"

ARG DEBIAN_FRONTEND=noninteractive
ARG FLUTTER_VERSION=3.24.0
ARG FLUTTER_CHANNEL=stable
ARG ANDROID_CMDLINE_VERSION=11076708
ARG ANDROID_BUILD_TOOLS=34.0.0
ARG ANDROID_PLATFORM=android-34
ARG TARGETARCH

ENV FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn \
    PUB_HOSTED_URL=https://pub.flutter-io.cn \
    ANDROID_SDK_ROOT=/opt/android-sdk \
    ANDROID_HOME=/opt/android-sdk \
    FLUTTER_HOME=/usr/local/flutter

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-${TARGETARCH}
ENV PATH=${FLUTTER_HOME}/bin:${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin:${ANDROID_SDK_ROOT}/platform-tools:${JAVA_HOME}/bin:${PATH}

# Install Flutter/Android build dependencies
RUN set -eux; \
    arch="$(dpkg --print-architecture)"; \
    if [ "${arch}" = "amd64" ]; then \
        dpkg --add-architecture i386; \
    fi; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        openjdk-17-jdk \
        clang \
        cmake \
        ninja-build \
        libgtk-3-0 \
        libgtk-3-dev \
        libglu1-mesa \
        libgl1 \
        libpulse0 \
        libsqlite3-0 \
        libgstreamer1.0-0 \
        libgstreamer-plugins-base1.0-0 \
        libxi6 \
        libxrender1 \
        libxtst6 \
        libfreetype6 \
        xz-utils \
        wget; \
    if [ "${arch}" = "amd64" ]; then \
        apt-get install -y --no-install-recommends \
            libc6:i386 \
            libgcc-s1:i386 \
            libstdc++6:i386 \
            zlib1g:i386; \
    fi; \
    rm -rf /var/lib/apt/lists/*

# Install Flutter SDK from the storage.flutter-io.cn mirror with checksum verification
RUN set -eux; \
    arch="$(dpkg --print-architecture)"; \
    case "${arch}" in \
        amd64) flutter_archive="flutter_linux_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz" ;; \
        arm64) flutter_archive="flutter_linux_arm64_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz" ;; \
        *) echo "Unsupported architecture: ${arch}" >&2; exit 1 ;; \
    esac; \
    tmpdir="$(mktemp -d)"; \
    curl -fsSLo "${tmpdir}/releases.json" "${FLUTTER_STORAGE_BASE_URL}/flutter_infra_release/releases/releases_linux.json"; \
    sha256="$(FLUTTER_ARCHIVE="${flutter_archive}" RELEASES_FILE="${tmpdir}/releases.json" python3 - <<'PY'
import json
import os
with open(os.environ['RELEASES_FILE'], encoding='utf-8') as fh:
    releases = json.load(fh)['releases']
archive = os.environ['FLUTTER_ARCHIVE']
for rel in releases:
    if rel.get('archive') == archive:
        print(rel['sha256'])
        break
else:
    raise SystemExit(f"Missing sha256 for {archive}")
PY
)"; \
    curl -fsSLo "${tmpdir}/${flutter_archive}" "${FLUTTER_STORAGE_BASE_URL}/flutter_infra_release/releases/${FLUTTER_CHANNEL}/linux/${flutter_archive}"; \
    echo "${sha256}  ${tmpdir}/${flutter_archive}" | sha256sum -c -; \
    tar -xJf "${tmpdir}/${flutter_archive}" -C /usr/local; \
    rm -rf "${tmpdir}"

# Install Android command line tools and required SDK components
RUN set -eux; \
    mkdir -p "${ANDROID_SDK_ROOT}/cmdline-tools"; \
    curl -fsSLo /tmp/cmdline-tools.zip "https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_CMDLINE_VERSION}_latest.zip"; \
    unzip -q /tmp/cmdline-tools.zip -d "${ANDROID_SDK_ROOT}/cmdline-tools"; \
    rm -f /tmp/cmdline-tools.zip; \
    mv "${ANDROID_SDK_ROOT}/cmdline-tools/cmdline-tools" "${ANDROID_SDK_ROOT}/cmdline-tools/latest"

RUN set -eux; \
    mkdir -p /root/.android; \
    touch /root/.android/repositories.cfg; \
    yes | sdkmanager --sdk_root="${ANDROID_SDK_ROOT}" --licenses > /dev/null; \
    sdkmanager --sdk_root="${ANDROID_SDK_ROOT}" \
        "platform-tools" \
        "platforms;${ANDROID_PLATFORM}" \
        "build-tools;${ANDROID_BUILD_TOOLS}"

# Configure Flutter defaults and pre-cache Android artifacts
RUN set -eux; \
    flutter config --no-analytics; \
    flutter config --android-sdk "${ANDROID_SDK_ROOT}"; \
    flutter precache --android

WORKDIR /workspace
