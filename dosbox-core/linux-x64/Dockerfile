FROM amd64/ubuntu:16.04

# Install deps.
RUN \
    set -eux; \
    cd /tmp; \
    export DEBIAN_FRONTEND='noninteractive'; \
    apt-get -y update; \
    apt-get -y dist-upgrade; \
    apt-get -y --no-install-recommends install \
        software-properties-common \
        wget \
    ; \
    add-apt-repository -y --update ppa:ubuntu-toolchain-r/test; \
    apt-get -y --no-install-recommends install \
        autoconf \
        automake \
        build-essential \
        g++-9 \
        gcc-9 \
        git \
        libasound2-dev \
        libglu1-mesa-dev \
        libtool \
        mesa-common-dev \
        pkg-config \
        python3-pip \
        unzip \
    ; \
    apt-get -y autoremove; \
    apt-get -y clean; \
    rm -rf /var/lib/apt/lists/*; \
    wget -q 'https://github.com/Kitware/CMake/releases/download/v3.18.4/cmake-3.18.4-Linux-x86_64.sh'; \
    sh ./cmake-3.18.4-Linux-x86_64.sh --skip-license --prefix=/usr/local; \
    rm cmake-3.18.4-Linux-x86_64.sh; \
    python3 -m pip install --upgrade --no-cache-dir pip; \
    python3 -m pip install --no-cache-dir meson; \
    wget -q 'https://github.com/ninja-build/ninja/releases/download/v1.10.1/ninja-linux.zip';\
    unzip ninja-linux.zip; \
    rm ninja-linux.zip; \
    mv ninja /usr/local/bin/;

# Libretro's CI expects these.
RUN \
    set -eux; \
    useradd -d /developer -m developer; \
    chown -R developer:developer /developer; \
    echo 'developer:developer' | chpasswd && adduser developer sudo;

ENV HOME=/developer

USER root
WORKDIR /developer
VOLUME /developer

CMD /bin/bash
