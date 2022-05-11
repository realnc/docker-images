# syntax=docker/dockerfile:1.3-labs
ARG FROM_ARCH
FROM ${FROM_ARCH}/ubuntu:16.04

ARG SHELL_WRAPPER

ENV DEBIAN_FRONTEND=noninteractive

RUN <<END_RUN
    set -ex
    echo '#! /bin/sh' > /bin/shell_wrapper
    echo exec ${SHELL_WRAPPER} '"$@"' >> /bin/shell_wrapper
    chmod +x /bin/shell_wrapper
END_RUN

SHELL ["/bin/shell_wrapper", "/bin/bash", "-c"]

# Install deps.
RUN <<END_RUN
    set -ex
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
        gettext \
        git \
        libasound2-dev \
        libglu1-mesa-dev \
        libltdl-dev \
        libtool \
        libtool-bin \
        mesa-common-dev \
        pkg-config \
        python3-pip \
        unzip \
    ;
    apt-get -y autoremove
    apt-get -y clean
    rm -rf /var/lib/apt/lists/*
    python3 -m pip install --upgrade --no-cache-dir "pip<20"
    python3 -m pip install --no-cache-dir \
        "meson<0.59" \
        "cmake<3.22" \
        "ninja" \
    ;
END_RUN

# Libretro's CI expects these.
RUN <<END_RUN
    set -ex
    useradd -d /developer -m developer
    chown -R developer:developer /developer
    echo "developer:developer" | chpasswd && adduser developer sudo
END_RUN

ENV HOME=/developer

USER root
WORKDIR /developer
VOLUME /developer

ENTRYPOINT ["/bin/shell_wrapper"]
CMD ["/bin/bash", "--login"]
