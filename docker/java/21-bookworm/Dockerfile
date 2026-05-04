# Java developer image based on DevRunner base (Debian bookworm)
ARG BASE_IMAGE=ghcr.io/chaitin/monkeycode-runner/base:bookworm
FROM ${BASE_IMAGE}

LABEL org.opencontainers.image.title="DevRunner Java" \
      org.opencontainers.image.source="https://github.com/chaitin/DevRunner" \
      org.opencontainers.image.description="Java developer toolbox image for DevRunner stacks"

ARG DEBIAN_FRONTEND=noninteractive
ARG JAVA_VERSION=21.0.2
ARG JAVA_SHA256_AMD64=a2def047a73941e01a73739f92755f86b895811afb1f91243db214cff5bdac3f
ARG JAVA_SHA256_ARM64=08db1392a48d4eb5ea5315cf8f18b89dbaf36cda663ba882cf03c704c9257ec2
ARG MAVEN_VERSION=3.9.14
ARG MAVEN_SHA512=d50af8ab5e6005b46a07f0ce9d3719e67cfdf898da988a84871304cd59fb1af0fef2f99dea709e6e66f21f732f905979b5c2dce6b6860406f60a70e84d9cf0b8
ARG GRADLE_VERSION=9.4.1
ARG GRADLE_SHA256=2ab2958f2a1e51120c326cad6f385153bb11ee93b3c216c5fccebfdfbb7ec6cb

ARG TARGETARCH

ENV JAVA_HOME=/usr/local/java \
    MAVEN_HOME=/opt/maven \
    GRADLE_HOME=/opt/gradle \
    PATH=/usr/local/java/bin:/opt/maven/bin:/opt/gradle/bin:${PATH}

RUN set -eux; \
    case "${TARGETARCH:-amd64}" in \
      amd64) java_arch="x64"; java_sha256="${JAVA_SHA256_AMD64}" ;; \
      arm64) java_arch="aarch64"; java_sha256="${JAVA_SHA256_ARM64}" ;; \
      "") java_arch="x64"; java_sha256="${JAVA_SHA256_AMD64}" ;; \
      *) echo "Unsupported architecture: ${TARGETARCH}" >&2; exit 1 ;; \
    esac; \
    curl -fsSL "https://download.java.net/java/GA/jdk21.0.2/f2283984656d49d69e91c558476027ac/13/GPL/openjdk-${JAVA_VERSION}_linux-${java_arch}_bin.tar.gz" -o /tmp/jdk.tar.gz; \
    echo "${java_sha256}  /tmp/jdk.tar.gz" | sha256sum -c -; \
    mkdir -p /usr/local/java; \
    tar -xzf /tmp/jdk.tar.gz -C /usr/local/java --strip-components=1; \
    rm /tmp/jdk.tar.gz; \
    java --version; \
    javac --version

RUN set -eux; \
    curl -fsSL "https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" -o /tmp/maven.tar.gz; \
    echo "${MAVEN_SHA512}  /tmp/maven.tar.gz" | sha512sum -c -; \
    mkdir -p /opt/maven; \
    tar -xzf /tmp/maven.tar.gz -C /opt/maven --strip-components=1; \
    rm /tmp/maven.tar.gz; \
    mvn --version

RUN set -eux; \
    curl -fsSL "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" -o /tmp/gradle.zip; \
    echo "${GRADLE_SHA256}  /tmp/gradle.zip" | sha256sum -c -; \
    mkdir -p /opt/gradle; \
    unzip -q /tmp/gradle.zip -d /tmp; \
    cp -a "/tmp/gradle-${GRADLE_VERSION}/." /opt/gradle/; \
    rm -rf "/tmp/gradle-${GRADLE_VERSION}" /tmp/gradle.zip; \
    gradle --version

ENV JAVA_VERSION=${JAVA_VERSION} \
    MAVEN_VERSION=${MAVEN_VERSION} \
    GRADLE_VERSION=${GRADLE_VERSION}

WORKDIR /workspace
