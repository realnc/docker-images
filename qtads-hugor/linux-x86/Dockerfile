FROM i386/ubuntu:16.04

ARG FSYNTH_VERSION=2.2.3
ARG OPENSSL_VERSION=1.1.1l
ARG QT_LONG_VERSION=5.15.2
ARG QT_SHORT_VERSION=5.15

ENV APPIMAGE_EXTRACT_AND_RUN=1
ENV DEBIAN_FRONTEND=noninteractive

# Deps.
RUN \
    set -eux; \
    cd /tmp; \
    apt-get -y update; \
    apt-get -y dist-upgrade; \
    apt-get -y --no-install-recommends install apt-utils; \
    apt-get -y --no-install-recommends install \
        '^libxcb.*-dev' \
        autoconf \
        automake \
        build-essential \
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
    ; \
    apt-get -y autoremove; \
    apt-get -y clean; \
    rm -rf /var/lib/apt/lists/*; \
    rm -rf /tmp/*;

# Pip has more recent versions of some stuff.
RUN \
    set -eux; \
    cd /tmp; \
    python3 -m pip install --upgrade --no-cache-dir "pip<20"; \
    python3 -m pip install --no-cache-dir \
        cmake \
    ;

# OpenSSL. Ubuntu 16.04's version is too old.
RUN \
    set -eux; \
    cd /tmp; \
    wget -nv "https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz" -O- | tar xz; \
    cd openssl-${OPENSSL_VERSION}; \
    ./config --prefix=/usr/local; \
    make -j`nproc`; \
    make install; \
    cd ..; \
    rm -rf openssl-${OPENSSL_VERSION}; \
    ldconfig;

# qtbase.
RUN \
    set -eux; \
    cd /tmp; \
    wget -nv "https://download.qt.io/official_releases/qt/${QT_SHORT_VERSION}/${QT_LONG_VERSION}/submodules/qtbase-everywhere-src-${QT_LONG_VERSION}.tar.xz" -O- | tar xJ; \
    cd qtbase-everywhere-src-${QT_LONG_VERSION}; \
    linux32 ./configure \
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
    ; \
    linux32 make -j`nproc`; \
    linux32 make install; \
    cd ..; \
    rm -rf qtbase-everywhere-src-${QT_LONG_VERSION}; \
    ldconfig;

# qtimageformats.
RUN \
    set -eux; \
    cd /tmp; \
    wget -nv "https://download.qt.io/official_releases/qt/${QT_SHORT_VERSION}/${QT_LONG_VERSION}/submodules/qtimageformats-everywhere-src-${QT_LONG_VERSION}.tar.xz" -O- | tar xJ; \
    cd qtimageformats-everywhere-src-${QT_LONG_VERSION}; \
    linux32 qmake; \
    linux32 make -j`nproc`; \
    linux32 make install; \
    cd ..; \
    rm -rf qtimageformats-everywhere-src-${QT_LONG_VERSION}; \
    ldconfig;

# Fluidsynth.
RUN \
    set -eux; \
    cd /tmp; \
    wget -nv "https://github.com/FluidSynth/fluidsynth/archive/v${FSYNTH_VERSION}.tar.gz" -O- | tar xz; \
    mkdir fluidsynth-${FSYNTH_VERSION}/build; \
    cd fluidsynth-${FSYNTH_VERSION}/build; \
    linux32 cmake \
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
    ; \
    linux32 make -j`nproc`; \
    linux32 make install; \
    cd ../..; \
    rm -rf fluidsynth-${FSYNTH_VERSION}; \
    ldconfig;

# linuxdeploy
RUN \
    set -eux; \
    cd /usr/local/bin; \
    wget -nv "https://github.com/linuxdeploy/linuxdeploy/releases/download/continuous/linuxdeploy-i386.AppImage"; \
    wget -nv "https://github.com/linuxdeploy/linuxdeploy-plugin-appimage/releases/download/continuous/linuxdeploy-plugin-appimage-i386.AppImage"; \
    wget -nv "https://github.com/linuxdeploy/linuxdeploy-plugin-qt/releases/download/continuous/linuxdeploy-plugin-qt-i386.AppImage"; \
    chmod +x linuxdeploy-*; \
    ln -s linuxdeploy-i386.AppImage linuxdeploy;

USER root
CMD /usr/bin/linux32 /bin/bash
