# Copyright VMware, Inc.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bookworm

ARG TARGETARCH

LABEL com.vmware.cp.artifact.flavor="sha256:c50c90cfd9d12b445b011e6ad529f1ad3daea45c26d20b00732fae3cd71f6a83" \
      org.opencontainers.image.base.name="docker.io/bitnami/minideb:bookworm" \
      org.opencontainers.image.created="2024-02-20T05:46:21Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="4.0.2-debian-12-r24" \
      org.opencontainers.image.title="jupyterhub" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="4.0.2"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-12" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl libbrotli1 libbz2-1.0 libcom-err2 libcrypt1 libcurl4 libffi8 libgcc-s1 libgmp10 libgnutls30 libgssapi-krb5-2 libhogweed6 libicu72 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-2.5-0 liblz4-1 liblzma5 libncursesw6 libnettle8 libnghttp2-14 libp11-kit0 libpsl5 libreadline8 librtmp1 libsasl2-2 libssh2-1 libssl3 libstdc++6 libtasn1-6 libtinfo6 libunistring2 libxml2 libzstd1 procps zlib1g
RUN mkdir -p /tmp/bitnami/pkg/cache/ ; cd /tmp/bitnami/pkg/cache/ ; \
    COMPONENTS=( \
      "miniconda-23.11.0-1-linux-${OS_ARCH}-debian-12" \
      "jupyterhub-4.0.2-14-linux-${OS_ARCH}-debian-12" \
    ) ; \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz.sha256" -O ; \
      fi ; \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" ; \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner --wildcards '*/files' ; \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done
RUN apt-get autoremove --purge -y curl && \
    apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true

ENV APP_VERSION="4.0.2" \
    BITNAMI_APP_NAME="jupyterhub" \
    PATH="/opt/bitnami/miniconda/bin:/opt/bitnami/miniconda/bin/:$PATH"

USER 1001
ENTRYPOINT [ "jupyterhub" ]
