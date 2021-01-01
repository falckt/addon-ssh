ARG BUILD_FROM=hassioaddons/debian-base:3.2.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Add env
ENV TERM="xterm-256color"

# Copy Python requirements file
COPY requirements.txt /tmp/

# Setup base
ARG BUILD_ARCH=amd64
# hadolint ignore=DL3003
RUN \
    set -x \
    && apt-get update && apt-get install -y --no-install-recommends \
    git \
    htop \
    openssh-server \
    zsh \
    vim \
    tmux \
    network-manager \
    mosh \
    sudo \
    wget \
    python3 \
    python3-pip \
    python3-setuptools \
    python3-dev \
    bash-completion \
    build-essential \
    cmake \
    libjson-c-dev \
    libuv1-dev \
    libssl-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/* \
    \
    && git clone --depth 1 \
    git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh \
    \
    && curl -L -s -o /usr/bin/ha \
    "https://github.com/home-assistant/cli/releases/download/4.9.0/ha_${BUILD_ARCH}" \
    \
    && chmod a+x /usr/bin/ha \
    && ha completion > /usr/share/bash-completion/completions/ha \
    \
    && sed -i -e "s#bin/ash#bin/zsh#" /etc/passwd \
    \
    && git clone --branch "v3.2.3" --depth=1 \
    https://github.com/warmcat/libwebsockets.git /tmp/libwebsockets \
    \
    && mkdir -p /tmp/libwebsockets/build \
    && cd /tmp/libwebsockets/build \
    && cmake .. \
    -DCMAKE_BUILD_TYPE=MinSizeRel \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_VERBOSE_MAKEFILE=TRUE \
    -DLWS_IPV6=ON \
    -DLWS_STATIC_PIC=ON \
    -DLWS_UNIX_SOCK=OFF \
    -DLWS_WITH_LIBUV=ON \
    -DLWS_WITH_SHARED=ON \
    -DLWS_WITHOUT_TESTAPPS=ON \
    && make \
    && make install \
    \
    && git clone --branch master --single-branch \
    https://github.com/tsl0922/ttyd.git /tmp/ttyd \
    && git -C /tmp/ttyd checkout 1.6.1 \
    \
    && mkdir -p /tmp/ttyd/build \
    && cd /tmp/ttyd/build \
    && cmake .. \
    -DCMAKE_BUILD_TYPE=MinSizeRel \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_VERBOSE_MAKEFILE=TRUE \
    && make \
    && make install \
    \
    && pip3 install \
    --no-cache-dir \
    -r /tmp/requirements.txt \
    \
    && hass-cli completion bash > /usr/share/bash-completion/completions/hass-cli \
    \
    && find /usr/local \
    \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
    -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
    -exec rm -rf '{}' + \
    \
    && rm -f -r \
    /root/.cache \
    /root/.cmake \
    /tmp/*

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="SSH & Web Terminal - Debian based" \
    io.hass.description="SSH & Web Terminal access to your Home Assistant instance based on Debian" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    org.opencontainers.image.title="SSH & Web Terminal - Debian based" \
    org.opencontainers.image.description="SSH & Web Terminal access to your Home Assistant instance based on Debian" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.source="https://github.com/falckt/addon-ssh" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
