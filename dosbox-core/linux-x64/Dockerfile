# syntax=docker/dockerfile:1.3-labs
FROM amd64/ubuntu:16.04

ENV DEBIAN_FRONTEND=noninteractive

# Install deps.
RUN <<END_RUN
    set -eux
    cd /tmp
    apt-get -y update
    apt-get -y dist-upgrade
    apt-get -y --no-install-recommends install \
        software-properties-common \
        wget \
    ;
    add-apt-repository -y --update ppa:ubuntu-toolchain-r/test
    apt-get -y --no-install-recommends install \
        autoconf \
        automake \
        build-essential \
        g++-9 \
        gcc-9 \
        git \
        libasound2-dev \
        libglu1-mesa-dev \
        libltdl-dev \
        libtool \
        mesa-common-dev \
        pkg-config \
        python3-pip \
        unzip \
    ;
    apt-get -y autoremove
    apt-get -y clean
    rm -rf /var/lib/apt/lists/*
    python3 -m pip install --upgrade --no-cache-dir "pip<20"
    python3 -m pip install --no-cache-dir "meson<0.59" "cmake<3.22"
    wget -nv "https://github.com/ninja-build/ninja/releases/download/v1.10.2/ninja-linux.zip"
    unzip ninja-linux.zip
    rm ninja-linux.zip
    mv ninja /usr/local/bin/
END_RUN

# Libretro's CI expects these.
RUN <<END_RUN
    set -eux
    useradd -d /developer -m developer
    chown -R developer:developer /developer
    echo "developer:developer" | chpasswd && adduser developer sudo
END_RUN

ENV HOME=/developer

USER root
WORKDIR /developer
VOLUME /developer

CMD /bin/bash
