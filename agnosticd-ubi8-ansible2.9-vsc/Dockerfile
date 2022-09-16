FROM registry.access.redhat.com/ubi8/ubi:${RHEL_VERSION:-latest}

# Big thanks and credit to the coder crew and https://github.com/coder/code-server
# coder use ubuntu hence the need for a RHEL/CentOS/Fedora based OCI Image

ARG TARGETPLATFORM

ENV PASSWORD=${PASSWORD:-changeme}
ENV CODE_SERVER_VERSION="4.5.0"

LABEL maintainer="Tok - Tony Kay tony.g.kay@gmail.com"

COPY entrypoint.sh /usr/bin/entrypoint.sh
COPY requirements.txt /tmp/requirements.txt

RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then \
      ARCHITECTURE=amd64; \
    elif [ "$TARGETPLATFORM" = "linux/arm64" ]; then \
      ARCHITECTURE=arm64; \
    fi \
    && dnf install -y https://github.com/coder/code-server/releases/download/v${CODE_SERVER_VERSION}/code-server-${CODE_SERVER_VERSION}-${ARCHITECTURE}.rpm \
      bind-utils \
      binutils \
      file \
      gcc \
      git \
      hardlink \
      hostname \
      iputils \
      iproute \
      less \
      libedit \
      make \
      ncurses \
      openssh \
      openssh-clients \
      python3.8 \
      python3-devel \
      sudo \
    && useradd devops \
    && echo "devops ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
    && chmod +x /usr/bin/entrypoint.sh \
    && dnf clean all \
    && rm -rf /var/cache/dnf /root/.cache

# TODO: Combine RUNs into a single layer, for now keep seperate for speedy rebuilds

RUN dnf install -y make binutils python3-devel \
  && python3 -m pip install --no-cache -U pip wheel setuptools \ 
  && python3 -m pip install --no-cache --no-use-pep517 pynacl \
  && python3 -m pip install --no-cache -r /tmp/requirements.txt \
  && rm -rf /var/cache/dnf /root/.cache

USER devops
WORKDIR /home/devops

EXPOSE 8080

ENTRYPOINT ["/usr/bin/entrypoint.sh"]

CMD ["code-server", "--bind-addr", "0.0.0.0:8080"]
