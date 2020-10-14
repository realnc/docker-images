FROM amd64/ubuntu:20.04

ARG DOCKER_MXE_TARGETS

# Install deps.
RUN \
    set -eux; \
    export DEBIAN_FRONTEND='noninteractive'; \
    apt-get -y update; \
    apt-get -y dist-upgrade; \
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
    ; \
    apt-get -y autoremove; \
    apt-get -y clean; \
    rm -rf /var/lib/apt/lists/*;

# Install MXE.
RUN \
    set -eux; \
    cd /opt; \
    git clone --depth=1 https://github.com/mxe/mxe.git; \
    cd mxe; \
    make check-requirements; \
    echo 'JOBS := `nproc`' >> settings.mk; \
    echo 'MXE_TMP := /tmp/mxe' >> settings.mk; \
    echo 'MXE_TARGETS :=' ${DOCKER_MXE_TARGETS} >> settings.mk; \
    echo 'override MXE_PLUGIN_DIRS += plugins/gcc10' >> settings.mk; \
    echo 'MXE_USE_CCACHE=' >> settings.mk; \
    echo 'gcc_CONFIGURE_OPTS := --enable-languages=c,c++' >> settings.mk; \
    make \
        cc \
        cmake \
        dlfcn-win32 \
        glib \
        libsndfile \
        sdl_net \
    ; \
    make clean-junk; \
    rm -rf pkg/*;

# Libretro's CI expects these.
RUN \
    set -eux; \
    useradd -d /developer -m developer; \
    chown -R developer:developer /developer; \
    echo 'developer:developer' | chpasswd && adduser developer sudo;

ENV HOME=/developer
ENV PATH=/opt/mxe/usr/bin:$PATH

USER root
WORKDIR /developer
VOLUME /developer

CMD /bin/bash
