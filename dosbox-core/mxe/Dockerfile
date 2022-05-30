# syntax=docker/dockerfile:1.4
FROM amd64/ubuntu:22.04 AS build-stage

ENV DEBIAN_FRONTEND=noninteractive

# Install deps.
RUN <<END_RUN
    set -ex

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
        python-is-python3 \
        python3 \
        python3-mako \
        python3-pip \
        ruby \
        sed \
        texinfo \
        unzip \
        wget \
        xz-utils \
        zip \
    ;
END_RUN

RUN <<END_RUN
    set -ex
    git config --global --add safe.directory '*'
    git config --global init.defaultBranch master
    git config --global advice.detachedHead false
END_RUN

# Install MXE.
ARG DOCKER_MXE_TARGET
ARG MXE_COMMIT=c3ac37bc7e09a55219c4671fd856edb1973a9c42
RUN <<END_RUN
    set -ex

    cd /opt
    mkdir mxe
    cd mxe
    git init
    git remote add origin "https://github.com/mxe/mxe.git"
    git fetch --depth=1 origin ${MXE_COMMIT}
    git checkout ${MXE_COMMIT}
    make check-requirements

    for settings_line in \
        "" \
        'JOBS := $(shell nproc)' \
        "MXE_TMP := /tmp/mxe" \
        "MXE_TARGETS := ${DOCKER_MXE_TARGET}" \
        "MXE_USE_CCACHE=" \
        "" \
        "gcc_CONFIGURE_OPTS := --enable-languages=c,c++" \
    ; do echo "$settings_line" >> settings.mk; done
END_RUN

# Build MXE packages
RUN <<END_RUN
    set -ex

    cd /opt/mxe
    make \
        cc \
        cmake \
        dlfcn-win32 \
        mman-win32 \
    ;

    make clean-junk
    strip usr/x86_64-pc-linux-gnu/bin/* || true
    strip usr/*/qt5/bin/* || true
    find -iname "*.exe" -exec rm '{}' \;
    rm -rf usr/*/share/doc
    rm -rf usr/*/share/locale
    rm -rf usr/*/share/man
    rm -rf pkg
    rm -rf /tmp/*
END_RUN

FROM amd64/ubuntu:22.04

COPY --from=build-stage /opt/mxe /opt/mxe/
COPY --from=build-stage /root/.gitconfig /root/

ENV DEBIAN_FRONTEND=noninteractive

RUN <<END_RUN
    set -ex

    apt-get -y update
    apt-get -y --no-install-recommends install apt-utils
    apt-get -y dist-upgrade
    apt-get -y --no-install-recommends install \
        autoconf \
        automake \
        ca-certificates \
        gettext \
        git \
        libtool-bin \
        make \
        ninja-build \
        pkg-config \
        wget \
        zip \
    ;

    apt-get -y autoremove
    apt-get -y clean
    rm -rf /usr/share/doc/*
    rm -rf /usr/share/man/*
    rm -rf /var/lib/apt/lists/*
    rm -rf /tmp/*
END_RUN

ENV PATH="/opt/mxe/usr/bin:$PATH"

USER root
CMD ["/bin/bash", "--login"]
