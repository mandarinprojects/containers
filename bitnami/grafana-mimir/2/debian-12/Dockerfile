# Copyright VMware, Inc.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bookworm

ARG TARGETARCH

LABEL com.vmware.cp.artifact.flavor="sha256:c50c90cfd9d12b445b011e6ad529f1ad3daea45c26d20b00732fae3cd71f6a83" \
      org.opencontainers.image.base.name="docker.io/bitnami/minideb:bookworm" \
      org.opencontainers.image.created="2024-02-20T03:22:32Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="2.11.0-debian-12-r10" \
      org.opencontainers.image.title="grafana-mimir" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="2.11.0"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-12" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl procps
RUN mkdir -p /tmp/bitnami/pkg/cache/ ; cd /tmp/bitnami/pkg/cache/ ; \
    COMPONENTS=( \
      "grafana-mimir-2.11.0-5-linux-${OS_ARCH}-debian-12" \
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
RUN mkdir -p /bitnami/grafana-mimir/data /bitnami/grafana-mimir/mimir && chmod -R g+rwX /bitnami/grafana-mimir && ln -s  /bitnami/grafana-mimir/mimir /mimir && ln -s /bitnami/grafana-mimir/data /data
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true

ENV APP_VERSION="2.11.0" \
    BITNAMI_APP_NAME="grafana-mimir" \
    PATH="/opt/bitnami/grafana-mimir/bin:$PATH"

EXPOSE 8080 9095

WORKDIR /opt/bitnami/grafana-mimir
USER 1001
ENTRYPOINT [ "mimir" ]
CMD [ "-config.file=/bitnami/grafana-mimir/conf/mimir.yaml" ]
