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

RUN git config --global --add safe.directory '*'

# Install MXE.
ARG DOCKER_MXE_TARGETS
RUN <<END_RUN
    set -ex

    cd /opt
    git clone --depth=1 -b qtads 'https://github.com/realnc/mxe.git' mxe
    cd mxe
    make check-requirements

    for settings_line in \
        'JOBS := $(shell nproc)' \
        "MXE_TMP := /tmp/mxe" \
        "MXE_TARGETS := ${DOCKER_MXE_TARGETS}" \
        "MXE_USE_CCACHE=" \
        "MXE_PLUGIN_DIRS += plugins/gcc12" \
    ; do echo "$settings_line" >> settings.mk; done
END_RUN

# Build MXE packages
RUN <<END_RUN
    set -ex

    cd /opt/mxe
    make \
        fluidsynth \
        mpg123 \
        vorbis \
        libsndfile \
        sdl2 \
        qtbase \
        qtsvg \
        qtimageformats \
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
        ca-certificates \
        make \
        wget \
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
