# syntax=docker/dockerfile:1.4
ARG FROM_ARCH
FROM realnc/qtads-build:${FROM_ARCH}

ARG ADLMIDI_VERSION=1.5.0.1-1
ARG APPIMAGE_ARCH
ARG NASM_VERSION=2.15.05
ARG OPENMPT_VERSION=0.4.29
# Don't use patchelf >=0.14 as it requires std::optional.
ARG PATCHELF_VERSION=0.13.1
ARG VLC_VERSION=3.0.17.3

WORKDIR /tmp

# Deps.
RUN <<END_RUN
    set -eux
    apt-get -y update
    apt-get -y dist-upgrade
    apt-get -y --no-install-recommends install \
        git \
        libsystemd-dev \
    ;
    apt-get -y autoremove
    apt-get -y clean
    rm -rf /var/lib/apt/lists/*
    rm -rf /tmp/*
END_RUN

# NASM
RUN <<END_RUN
    set -eux
    wget -nv "https://www.nasm.us/pub/nasm/releasebuilds/${NASM_VERSION}/nasm-${NASM_VERSION}.tar.xz" -O- | tar xJ
    cd nasm-${NASM_VERSION}
    ./configure --prefix=/usr/local
    make -j`nproc`
    make install
    cd ..
    rm -rf *
END_RUN

# LibVLC
RUN <<END_RUN
    set -eux
    wget -nv "http://get.videolan.org/vlc/${VLC_VERSION}/vlc-${VLC_VERSION}.tar.xz" -O- | tar xJ
    mkdir vlc-${VLC_VERSION}/contrib/native
    cd vlc-${VLC_VERSION}/contrib/native
    ../bootstrap \
        --disable-disc \
        --disable-net \
        --disable-sout \
    ;
    make -j`nproc` .samplerate
    make -j`nproc` .libplacebo
    make -j`nproc` .ebml
    make -j`nproc` .matroska
    make -j`nproc` .ffmpeg
    make -j`nproc` .soxr
    make -j`nproc` .postproc
    cd ../..
    # kludge around build error on 32-bit ubuntu
    sed -i'' 's/ -lplacebo / -lplacebo -lm /' contrib/*-linux-gnu/lib/pkgconfig/libplacebo.pc
    ./configure \
        --prefix=/usr/local \
        --disable-debug \
        --enable-shared \
        --disable-static \
        --disable-nls \
        --disable-rpath \
        --disable-dbus \
        --disable-sout \
        --disable-vlm \
        --disable-addonmanagermodules \
        --disable-live555 \
        --disable-lua \
        --disable-a52 \
        --disable-dc1394 \
        --disable-dv1394 \
        --disable-linsys \
        --disable-dvdread \
        --disable-dvdnav \
        --disable-bluray \
        --disable-opencv \
        --disable-smbclient \
        --disable-sftp \
        --disable-screen \
        --disable-mod \
        --disable-mpc \
        --disable-fluidsynth \
        --disable-fluidlite \
        --disable-vlc \
        --disable-x264 \
        --disable-gnutls \
        --disable-projectm \
        --disable-goom \
        --disable-vsxu \
        --disable-chromecast \
        --disable-archive \
        --disable-dsm \
        --disable-nfs \
        --disable-decklink \
        --disable-libcddb \
        --disable-vnc \
        --disable-freerdp \
        --disable-sid \
        --disable-shine \
        --disable-gst-decode \
        --disable-aom \
        --disable-dav1d \
        --disable-twolame \
        --disable-libmpeg2 \
        --disable-speex \
        --disable-opus \
        --disable-spatialaudio \
        --disable-theora \
        --disable-schroedinger \
        --disable-x265 \
        --disable-zvbi \
        --disable-telx \
        --disable-libass \
        --disable-aribsub \
        --disable-aribb25 \
        --disable-kate \
        --disable-dca \
        --disable-secret \
        --disable-kwallet \
        --disable-sdl-image \
        --disable-fribidi \
        --disable-svg \
        --disable-svgdec \
        --disable-caca \
        --disable-mmal \
        --disable-jack \
        --disable-chromaprint \
        --disable-qt \
        --disable-skins2 \
        --disable-srt \
        --disable-avahi \
        --disable-mtp \
        --disable-upnp \
        --disable-microdns \
        --disable-libxml2 \
        --disable-taglib \
        --disable-secret \
        --disable-notify \
        --disable-mpg123 \
        --disable-sparkle \
        --disable-ncurses \
        --disable-macosx \
        --disable-macosx-avfoundation \
        --enable-samplerate \
        --enable-soxr \
        --enable-merge-ffmpeg \
    ;
    make -j`nproc`
    touch share/vlc.appdata.xml
    make install-strip
    cd ..
    rm -rf *
    ldconfig
END_RUN

# libopenmpt
RUN <<END_RUN
    set -eux
    wget -nv "https://lib.openmpt.org/files/libopenmpt/src/libopenmpt-${OPENMPT_VERSION}+release.autotools.tar.gz" -O- | tar xz
    cd libopenmpt-${OPENMPT_VERSION}+release.autotools
    ./configure \
        --enable-silent-rules \
        --disable-dependency-tracking \
        --enable-shared \
        --disable-static \
        --disable-openmpt123 \
        --disable-examples \
        --disable-tests \
        --disable-doxygen-doc \
        --without-zlib \
        --without-mpg123 \
        --without-ogg \
        --without-vorbis \
        --without-vorbisfile \
        --without-pulseaudio \
        --without-portaudio \
        --without-portaudiocpp \
        --without-sdl2 \
        --without-sdl \
        --without-sndfile \
        --without-flac \
    ;
    make -j`nproc`
    make install-strip
    cd ..
    rm -rf *
    ldconfig
END_RUN

# libADLMIDI
RUN <<END_RUN
    set -eux
    wget -nv "https://github.com/Wohlstand/libADLMIDI/archive/refs/tags/v${ADLMIDI_VERSION}.tar.gz" -O- | tar xz
    mkdir libADLMIDI-${ADLMIDI_VERSION}/build
    cd libADLMIDI-${ADLMIDI_VERSION}/build
    cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DlibADLMIDI_SHARED=ON \
        -DlibADLMIDI_STATIC=OFF \
        -DEXAMPLE_SDL2_AUDIO=OFF \
        -DUSE_DOSBOX_EMULATOR=ON \
        -DUSE_JAVA_EMULATOR=OFF \
        -DUSE_NUKED_EMULATOR=ON \
        -DUSE_OPAL_EMULATOR=OFF \
        -DVLC_PLUGIN_NOINSTALL=ON \
        -DWITH_ADLMIDI2=OFF \
        -DWITH_EMBEDDED_BANKS=ON \
        -DWITH_MIDIPLAY=OFF \
        -DWITH_MIDI_SEQUENCER=ON \
        -DWITH_MUS2MID=OFF \
        -DWITH_OLD_UTILS=OFF \
        -DWITH_UNIT_TESTS=OFF \
        -DWITH_VLC_PLUGIN=OFF \
        -DWITH_XMI2MID=OFF \
        .. \
    ;
    cmake --build . --target install -j `nproc` -v
    cd ../..
    rm -rf *
    ldconfig
END_RUN

# patchelf
RUN <<END_RUN
    set -eux
    wget -nv "https://github.com/NixOS/patchelf/releases/download/${PATCHELF_VERSION}/patchelf-${PATCHELF_VERSION}.tar.bz2" -O- | tar xj
    cd patchelf-*
    ./configure --prefix=/usr/local
    make -j`nproc`
    make install-strip
    cd ..
    rm -rf *
END_RUN

# appimagetool, linuxdeployqt
RUN <<END_RUN
    set -eux
    wget -nv "https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-${APPIMAGE_ARCH}.AppImage" -O /usr/local/bin/appimagetool
    chmod +x /usr/local/bin/appimagetool
    wget -nv "https://github.com/probonopd/linuxdeployqt/archive/refs/tags/continuous.tar.gz" -O- | tar xz
    cd linuxdeployqt-continuous
    qmake
    make -j`nproc`
    make install
    cd ..
    rm -rf *
END_RUN
