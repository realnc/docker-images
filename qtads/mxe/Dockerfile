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
ARG FSYNTH_CHECKSUM=cd610810f30566e28fb98c36501f00446a06fa6bae3dc562c8cd3868fe1c0fc7
ARG FSYNTH_VERSION=2.3.2
ARG LIBSNDFILE_CHECKSUM=a8cfb1c09ea6e90eff4ca87322d4168cdbe5035cb48717b40bf77e751cc02163
ARG LIBSNDFILE_VERSION=1.0.31
ARG MXE_COMMIT=f3206a381994bbbed40929889437069bde8ec05a
RUN <<END_RUN
    set -ex

    git config --global init.defaultBranch master
    git config --global advice.detachedHead false
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
        "MXE_TARGETS := ${DOCKER_MXE_TARGETS}" \
        "MXE_USE_CCACHE=" \
        "" \
        "gcc_CONFIGURE_OPTS := --enable-languages=c,c++" \
        "" \
        "override libsndfile_VERSION := ${LIBSNDFILE_VERSION}" \
        "override libsndfile_CHECKSUM := ${LIBSNDFILE_CHECKSUM}" \
        "override libsndfile_GH_CONF := libsndfile/libsndfile/releases,,,,,.tar.bz2" \
        "override libsndfile_DEPS := cc" \
        "" \
        "override mpg123_DEPS := cc" \
        "" \
        "override fluidsynth_VERSION := ${FSYNTH_VERSION}" \
        "override fluidsynth_CHECKSUM := ${FSYNTH_CHECKSUM}" \
        "override fluidsynth_DEPS := cc glib mman-win32" \
        "override fluidsynth_CONFIGURE_OPTS := \\" \
        "    -Denable-dbus=OFF \\" \
        "    -Denable-jack=OFF \\" \
        "    -Denable-libsndfile=OFF \\" \
        "    -Denable-portaudio=OFF \\" \
        "    -Denable-readline=OFF \\" \
        "    -Denable-aufile=OFF \\" \
        "    -Denable-ipv6=OFF \\" \
        "    -Denable-ladspa=OFF \\" \
        "    -Denable-libinstpatch=OFF \\" \
        "    -Denable-midishare=OFF \\" \
        "    -Denable-opensles=OFF \\" \
        "    -Denable-network=OFF \\" \
        "    -Denable-dsound=OFF \\" \
        "    -Denable-oss=OFF \\" \
        "    -Denable-wasapi=OFF \\" \
        "    -Denable-waveout=OFF \\" \
        "    -Denable-winmidi=OFF \\" \
        "    -Denable-sdl2=OFF \\" \
        "    -Denable-pulseaudio=OFF \\" \
        "    -Denable-threads=ON" \
        "" \
        'override qtbase_DEPS := cc fontconfig freetype harfbuzz jpeg libpng openssl pcre2 zlib zstd $(BUILD)~zstd' \
        "override qtbase_CONFIGURE_OPTS := \\" \
        "    -gc-binaries \\" \
        "    -c++std c++17 \\" \
        "    -no-dbus \\" \
        "    -no-opengl \\" \
        "    -no-sql-db2 \\" \
        "    -no-sql-ibase \\" \
        "    -no-sql-mysql \\" \
        "    -no-sql-oci \\" \
        "    -no-sql-odbc \\" \
        "    -no-sql-psql \\" \
        "    -no-sql-sqlite2 \\" \
        "    -no-sql-sqlite \\" \
        "    -no-sql-tds" \
    ; do echo "$settings_line" >> settings.mk; done

    sed -i'' 's/--enable-external-libs /--disable-external-libs --disable-full-suite /g' src/libsndfile.mk

    sed -i'' '/--with-default-audio=/d' src/mpg123.mk
    sed -i'' '/--with-audio=/d' src/mpg123.mk
    sed -i'' 's/$(MXE_CONFIGURE_OPTS) /$(MXE_CONFIGURE_OPTS) --with-default-audio=dummy --with-audio=dummy /g' src/mpg123.mk

    rm src/fluidsynth-*.patch
    sed -i'' '/--libs fluidsynth/ s/$/ -luuid/' src/fluidsynth.mk

    sed -i'' '/PSQL_LIBS=/d' src/qtbase.mk
    sed -i'' '/SYBASE_LIBS=/d' src/qtbase.mk
    sed -i'' '/-opengl dynamic /d' src/qtbase.mk
    sed -i'' '/-plugin-sql-mysql /d' src/qtbase.mk
    sed -i'' '/-mysql_config /d' src/qtbase.mk
    sed -i'' '/-plugin-sql-/d' src/qtbase.mk
    sed -i'' '/-system-sqlite /d' src/qtbase.mk
    sed -i'' '/-dbus-linked /d' src/qtbase.mk
    sed -i'' 's:./configure :./configure -c++std c++17 :g' src/qtbase.mk
    sed -i'' 's/std=c++0x/std=c++17/g' src/qtbase.mk
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
