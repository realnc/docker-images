# syntax=docker/dockerfile:1.3-labs
FROM amd64/ubuntu:20.04

ARG DOCKER_MXE_TARGETS
ARG MXE_COMMIT=8838ac3938cd8e47424a4cb5d3676d1ae9a4d670

ENV DEBIAN_FRONTEND=noninteractive

# Install deps.
RUN <<END_RUN
    set -eux
    apt-get -y update
    apt-get -y --no-install-recommends install apt-utils
    apt-get -y dist-upgrade
    apt-get -y --no-install-recommends install \
        autoconf \
        automake \
        autopoint \
        bash \
        bison \
        bzip2 \
        flex \
        g++ \
        g++-multilib \
        gettext \
        git \
        gperf \
        intltool \
        less \
        libc6-dev-i386 \
        libgdk-pixbuf2.0-dev \
        libltdl-dev \
        libssl-dev \
        libtool \
        libtool-bin \
        libxml-parser-perl \
        lzip \
        make \
        meson \
        ninja-build \
        openssl \
        p7zip-full \
        patch \
        perl \
        pkg-config \
        python \
        python3-pip \
        ruby \
        sed \
        unzip \
        wget \
        xz-utils \
        zip \
    ;
    apt-get -y autoremove
    apt-get -y clean
    rm -rf /var/lib/apt/lists/*
    rm -rf /tmp/*
END_RUN

# Install MXE.
RUN <<END_RUN
    set -eux
    cd /opt
    mkdir mxe
    cd mxe
    git init
    git remote add origin "https://github.com/mxe/mxe.git"
    git fetch --depth=1 origin ${MXE_COMMIT}
    git checkout ${MXE_COMMIT}
    make check-requirements
    echo "" >> settings.mk
    echo "JOBS := \\$(shell nproc)" >> settings.mk
    echo "MXE_TMP := /tmp/mxe" >> settings.mk
    echo "MXE_TARGETS := ${DOCKER_MXE_TARGETS}" >> settings.mk
    echo "override MXE_PLUGIN_DIRS += plugins/gcc11" >> settings.mk
    echo "MXE_USE_CCACHE=" >> settings.mk
    echo "gcc_CONFIGURE_OPTS := --enable-languages=c,c++" >> settings.mk
    make \
        cc \
        cmake \
        dlfcn-win32 \
        glib \
        libsndfile \
        sdl_net \
    ;
    make clean-junk
    rm -rf pkg/*
    rm -rf /tmp/*
END_RUN

# Libretro's CI expects these.
RUN <<END_RUN
    set -eux
    useradd -d /developer -m developer
    chown -R developer:developer /developer
    echo "developer:developer" | chpasswd && adduser developer sudo
END_RUN

ENV HOME=/developer
ENV PATH=/opt/mxe/usr/bin:$PATH

USER root
WORKDIR /developer
VOLUME /developer

CMD /bin/bash
