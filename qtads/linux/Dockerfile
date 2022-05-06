# syntax=docker/dockerfile:1.4
ARG FROM_ARCH
FROM ${FROM_ARCH}/ubuntu:16.04

ARG FSYNTH_VERSION=2.2.7
ARG LINUXDEPLOY_ARCH
ARG OPENSSL_VERSION=1.1.1o
ARG SHELL_WRAPPER

ENV APPIMAGE_EXTRACT_AND_RUN=1
ENV DEBIAN_FRONTEND=noninteractive

RUN <<END_RUN
    set -ex
    echo '#! /bin/sh' > /bin/shell_wrapper
    echo exec ${SHELL_WRAPPER} '"$@"' >> /bin/shell_wrapper
    chmod +x /bin/shell_wrapper
END_RUN

SHELL ["/bin/shell_wrapper", "/bin/bash", "-c"]
WORKDIR /tmp

# Deps.
RUN <<END_RUN
    set -ex
    apt-get -y update
    apt-get -y --no-install-recommends install \
        apt-utils \
        software-properties-common\
    ;
    add-apt-repository -y ppa:git-core/ppa
    apt-get -y update
    apt-get -y dist-upgrade
    apt-get -y --no-install-recommends install \
        '^libxcb.*-dev' \
        autoconf \
        automake \
        build-essential \
        git \
        libasound2-dev \
        libatspi2.0-dev \
        libdbus-1-dev \
        libfontconfig1-dev \
        libfreetype6-dev \
        libfuse2 \
        libglib2.0-dev \
        libglu1-mesa-dev \
        libglu1-mesa-dev \
        libharfbuzz-dev \
        libinput-dev \
        libjpeg-turbo8-dev \
        libltdl-dev \
        libmng-dev \
        libmpg123-dev \
        libpcre2-dev \
        libpng-dev \
        libpulse-dev \
        libsdl2-dev \
        libsndfile1-dev \
        libtool \
        libx11-dev \
        libx11-xcb-dev \
        libxcb1-dev \
        libxext-dev \
        libxfixes-dev \
        libxi-dev \
        libxkbcommon-dev \
        libxkbcommon-x11-dev \
        libxrender-dev \
        mesa-common-dev \
        pkg-config \
        python3-pip \
        unzip \
        wget \
        zlib1g-dev \
    ;
    apt-get -y autoremove
    apt-get -y clean
    rm -rf /var/lib/apt/lists/*
    rm -rf /tmp/*
END_RUN

# Pip has more recent versions of some stuff.
RUN <<END_RUN
    set -ex
    python3 -m pip install --upgrade --no-cache-dir "pip<20"
    python3 -m pip install --no-cache-dir \
        cmake \
    ;
END_RUN

# OpenSSL. Ubuntu 16.04's version is too old.
RUN <<END_RUN
    set -ex
    wget -nv "https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz" -O- | tar xz
    cd openssl-${OPENSSL_VERSION}
    ./config --prefix=/usr/local
    make -j`nproc`
    make install
    cd ..
    rm -rf *
    ldconfig
END_RUN

# qtbase.
RUN <<END_RUN
    set -ex
    git clone --depth=1 --branch "kde/5.15" "https://invent.kde.org/qt/qt/qtbase.git"
    cd qtbase
    ./configure \
        -prefix /usr/local \
        -opensource \
        -confirm-license \
        -c++std c++14 \
        -nomake examples \
        -nomake tests \
        -accessibility \
        -glib \
        -system-zlib \
        -system-libpng \
        -system-libjpeg \
        -fontconfig \
        -system-freetype \
        -qt-harfbuzz \
        -qt-pcre \
        -ssl \
        -openssl-runtime \
        -no-cups \
        -xcb \
        -no-sql-db2 \
        -no-sql-ibase \
        -no-sql-mysql \
        -no-sql-oci \
        -no-sql-odbc \
        -no-sql-psql \
        -no-sql-sqlite2 \
        -no-sql-sqlite \
        -no-sql-tds \
    ;
    make -j`nproc`
    make install
    cd ..
    rm -rf *
    ldconfig
END_RUN

# qtsvg
RUN <<END_RUN
    set -ex
    git clone --depth=1 --branch "kde/5.15" "https://invent.kde.org/qt/qt/qtsvg.git"
    cd qtsvg
    qmake
    make -j`nproc`
    make install
    cd ..
    rm -rf *
    ldconfig
END_RUN

# qtimageformats.
RUN <<END_RUN
    set -ex
    git clone --depth=1 --branch "kde/5.15" "https://invent.kde.org/qt/qt/qtimageformats.git"
    cd qtimageformats
    qmake
    make -j`nproc`
    make install
    cd ..
    rm -rf *
    ldconfig
END_RUN

# Fluidsynth.
RUN <<END_RUN
    set -ex
    wget -nv "https://github.com/FluidSynth/fluidsynth/archive/v${FSYNTH_VERSION}.tar.gz" -O- | tar xz
    mkdir fluidsynth-${FSYNTH_VERSION}/build
    cd fluidsynth-${FSYNTH_VERSION}/build
    cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_SHARED_LIBS=ON \
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DLIB_INSTALL_DIR=lib \
        -Dbuild-docs=OFF \
        -Dbuild-tests=OFF \
        -Denable-dbus=OFF \
        -Denable-jack=OFF \
        -Denable-libsndfile=OFF \
        -Denable-portaudio=OFF \
        -Denable-readline=OFF \
        -Denable-aufile=OFF \
        -Denable-ipv6=OFF \
        -Denable-ladspa=OFF \
        -Denable-libinstpatch=OFF \
        -Denable-midishare=OFF \
        -Denable-opensles=OFF \
        -Denable-network=OFF \
        -Denable-dsound=OFF \
        -Denable-oss=OFF \
        -Denable-lash=OFF \
        -Denable-alsa=OFF \
        -Denable-systemd=OFF \
        -Denable-wasapi=OFF \
        -Denable-waveout=OFF \
        -Denable-winmidi=OFF \
        -Denable-sdl2=OFF \
        -Denable-pulseaudio=OFF \
        -Denable-threads=ON \
        .. \
    ;
    make -j`nproc`
    make install
    cd ../..
    rm -rf *
    ldconfig
END_RUN

# linuxdeploy
RUN <<END_RUN
    set -ex
    cd /usr/local/bin
    wget -nv "https://github.com/linuxdeploy/linuxdeploy/releases/download/continuous/linuxdeploy-${LINUXDEPLOY_ARCH}.AppImage"
    wget -nv "https://github.com/linuxdeploy/linuxdeploy-plugin-appimage/releases/download/continuous/linuxdeploy-plugin-appimage-${LINUXDEPLOY_ARCH}.AppImage"
    wget -nv "https://github.com/linuxdeploy/linuxdeploy-plugin-qt/releases/download/continuous/linuxdeploy-plugin-qt-${LINUXDEPLOY_ARCH}.AppImage"
    chmod +x linuxdeploy-*
    ln -s linuxdeploy-${LINUXDEPLOY_ARCH}.AppImage linuxdeploy
END_RUN

USER root
ENTRYPOINT ["/bin/shell_wrapper"]
CMD ["/bin/bash", "--login"]
