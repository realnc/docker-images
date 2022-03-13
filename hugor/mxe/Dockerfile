# syntax=docker/dockerfile:1.4
ARG FROM_ARCH
FROM realnc/qtads-build:${FROM_ARCH}

ARG ADLMIDI_VERSION=1.5.0.1-1
ARG MXE_TARGET
ARG OPENMPT_VERSION=0.4.29
ARG VLC_VERSION=3.0.17.3

ENV CMAKE_GENERATOR=Ninja

WORKDIR /tmp

RUN <<END_RUN
    set -eux

    wget -nv "https://get.videolan.org/vlc/${VLC_VERSION}/win32/vlc-${VLC_VERSION}-win32.7z"
    7z x vlc-*
    cd vlc-${VLC_VERSION}
    sed -i'' 's/^prefix=.*/prefix=\/opt\/mxe\/usr\/${MXE_TARGET}/g' sdk/lib/pkgconfig/*.pc
    cp -a sdk/* /opt/mxe/usr/${MXE_TARGET}/
    mkdir -p /opt/vlc/plugins
    mv libvlc.dll libvlccore.dll /opt/vlc/

    cd plugins/demux
    mkdir /opt/vlc/plugins/demux
    mv \
        libreal_plugin.dll \
        libplaylist_plugin.dll \
        libsmf_plugin.dll \
        libimage_plugin.dll \
        libflacsys_plugin.dll \
        libsubtitle_plugin.dll \
        libnuv_plugin.dll \
        libdemux_stl_plugin.dll \
        libps_plugin.dll \
        libasf_plugin.dll \
        librawaud_plugin.dll \
        libpva_plugin.dll \
        libavi_plugin.dll \
        libdirectory_demux_plugin.dll \
        libogg_plugin.dll \
        libmjpeg_plugin.dll \
        libvc1_plugin.dll \
        libdemuxdump_plugin.dll \
        libau_plugin.dll \
        libcaf_plugin.dll \
        libmp4_plugin.dll \
        librawvid_plugin.dll \
        libh26x_plugin.dll \
        libxa_plugin.dll \
        libes_plugin.dll \
        libwav_plugin.dll \
        librawdv_plugin.dll \
        libadaptive_plugin.dll \
        libtta_plugin.dll \
        libnoseek_plugin.dll \
        libnsc_plugin.dll \
        libdemux_cdg_plugin.dll \
        libvoc_plugin.dll \
        libmkv_plugin.dll \
        libdiracsys_plugin.dll \
        libvobsub_plugin.dll \
        libnsv_plugin.dll \
        libty_plugin.dll \
        libaiff_plugin.dll \
        libmpgv_plugin.dll \
        /opt/vlc/plugins/demux/

    cd ../video_chroma
    mkdir /opt/vlc/plugins/video_chroma
    mv \
        libi420_10_p010_plugin.dll \
        librv32_plugin.dll \
        libi420_yuy2_plugin.dll \
        libgrey_yuv_plugin.dll \
        libi422_yuy2_sse2_plugin.dll \
        libi420_yuy2_sse2_plugin.dll \
        libyuvp_plugin.dll \
        libi422_yuy2_plugin.dll \
        libchain_plugin.dll \
        libi420_nv12_plugin.dll \
        libi422_i420_plugin.dll \
        libswscale_plugin.dll \
        libi420_rgb_mmx_plugin.dll \
        libi420_rgb_plugin.dll \
        libi420_rgb_sse2_plugin.dll \
        libyuy2_i422_plugin.dll \
        libi420_yuy2_mmx_plugin.dll \
        libi422_yuy2_mmx_plugin.dll \
        libyuy2_i420_plugin.dll \
        /opt/vlc/plugins/video_chroma/

    cd ../audio_filter
    mkdir /opt/vlc/plugins/audio_filter
    mv \
        libparam_eq_plugin.dll \
        libequalizer_plugin.dll \
        libchorus_flanger_plugin.dll \
        libscaletempo_plugin.dll \
        libdolby_surround_decoder_plugin.dll \
        libremap_plugin.dll \
        libstereo_widen_plugin.dll \
        libcompressor_plugin.dll \
        libtrivial_channel_mixer_plugin.dll \
        libgain_plugin.dll \
        libspatializer_plugin.dll \
        libscaletempo_pitch_plugin.dll \
        libugly_resampler_plugin.dll \
        libaudiobargraph_a_plugin.dll \
        libkaraoke_plugin.dll \
        libaudio_format_plugin.dll \
        libnormvol_plugin.dll \
        libsamplerate_plugin.dll \
        libsimple_channel_mixer_plugin.dll \
        libmono_plugin.dll \
        libheadphone_channel_mixer_plugin.dll \
        libtospdif_plugin.dll \
        /opt/vlc/plugins/audio_filter/

    cd ../video_output
    mkdir /opt/vlc/plugins/video_output
    mv \
        libglwin32_plugin.dll \
        libdirect3d11_plugin.dll \
        libdirectdraw_plugin.dll \
        libvmem_plugin.dll \
        libgl_plugin.dll \
        libyuv_plugin.dll \
        libvdummy_plugin.dll \
        libdirect3d9_plugin.dll \
        libwgl_plugin.dll \
        libwinhibit_plugin.dll \
        libwingdi_plugin.dll \
        libdrawable_plugin.dll \
        libflaschen_plugin.dll \
        /opt/vlc/plugins/video_output/

    cd ../visualization
    mkdir /opt/vlc/plugins/visualization
    mv \
        libglspectrum_plugin.dll \
        libvisual_plugin.dll \
        /opt/vlc/plugins/visualization/

    cd ../spu
    mkdir /opt/vlc/plugins/spu
    mv \
        libmosaic_plugin.dll \
        libmarq_plugin.dll \
        libaudiobargraph_v_plugin.dll \
        librss_plugin.dll \
        libsubsdelay_plugin.dll \
        liblogo_plugin.dll \
        /opt/vlc/plugins/spu/

    cd ../packetizer
    mkdir /opt/vlc/plugins/packetizer
    mv \
        libpacketizer_dirac_plugin.dll \
        libpacketizer_hevc_plugin.dll \
        libpacketizer_av1_plugin.dll \
        libpacketizer_mpeg4video_plugin.dll \
        libpacketizer_vc1_plugin.dll \
        libpacketizer_mpeg4audio_plugin.dll \
        libpacketizer_copy_plugin.dll \
        libpacketizer_mlp_plugin.dll \
        libpacketizer_a52_plugin.dll \
        libpacketizer_flac_plugin.dll \
        libpacketizer_mpegaudio_plugin.dll \
        libpacketizer_dts_plugin.dll \
        libpacketizer_mpegvideo_plugin.dll \
        libpacketizer_h264_plugin.dll \
        /opt/vlc/plugins/packetizer/

    cd ../access
    mkdir /opt/vlc/plugins/access
    mv \
        libaccess_concat_plugin.dll \
        libaccess_mms_plugin.dll \
        libimem_plugin.dll \
        librtp_plugin.dll \
        libcdda_plugin.dll \
        libsmb_plugin.dll \
        libsdp_plugin.dll \
        libaccess_imem_plugin.dll \
        libdshow_plugin.dll \
        libshm_plugin.dll \
        libaccess_realrtsp_plugin.dll \
        libattachment_plugin.dll \
        libvcd_plugin.dll \
        libdtv_plugin.dll \
        libtimecode_plugin.dll \
        libsatip_plugin.dll \
        libudp_plugin.dll \
        libaccess_wasapi_plugin.dll \
        libfilesystem_plugin.dll \
        libhttps_plugin.dll \
        libhttp_plugin.dll \
        libftp_plugin.dll \
        libtcp_plugin.dll \
        libscreen_plugin.dll \
        libvdr_plugin.dll \
        libidummy_plugin.dll \
        /opt/vlc/plugins/access/

    cd ../audio_mixer
    mkdir /opt/vlc/plugins/audio_mixer
    mv \
        libinteger_mixer_plugin.dll \
        libfloat_mixer_plugin.dll \
        /opt/vlc/plugins/audio_mixer/

    cd ../video_splitter
    mkdir /opt/vlc/plugins/video_splitter
    mv \
        libpanoramix_plugin.dll \
        libwall_plugin.dll \
        libclone_plugin.dll \
        /opt/vlc/plugins/video_splitter/

    cd ../control
    mkdir /opt/vlc/plugins/control
    mv \
        libwin_hotkeys_plugin.dll \
        libdummy_plugin.dll \
        libnetsync_plugin.dll \
        libntservice_plugin.dll \
        libwin_msg_plugin.dll \
        liboldrc_plugin.dll \
        libgestures_plugin.dll \
        libhotkeys_plugin.dll \
        /opt/vlc/plugins/control/

    cd ../meta_engine
    mkdir /opt/vlc/plugins/meta_engine
    mv \
        libfolder_plugin.dll \
        /opt/vlc/plugins/meta_engine/

    cd ../codec
    mkdir /opt/vlc/plugins/codec
    mv \
        libvorbis_plugin.dll \
        libd3d11va_plugin.dll \
        libstl_plugin.dll \
        libcc_plugin.dll \
        liblpcm_plugin.dll \
        libsvcdsub_plugin.dll \
        libtextst_plugin.dll \
        libsubsusf_plugin.dll \
        librawvideo_plugin.dll \
        libqsv_plugin.dll \
        liboggspots_plugin.dll \
        libttml_plugin.dll \
        libdmo_plugin.dll \
        libspudec_plugin.dll \
        libaes3_plugin.dll \
        libscte27_plugin.dll \
        libsubsdec_plugin.dll \
        libdvbsub_plugin.dll \
        libuleaddvaudio_plugin.dll \
        libmft_plugin.dll \
        libavcodec_plugin.dll \
        libddummy_plugin.dll \
        libjpeg_plugin.dll \
        libsubstx3g_plugin.dll \
        libspdif_plugin.dll \
        libscte18_plugin.dll \
        libg711_plugin.dll \
        libaraw_plugin.dll \
        libcdg_plugin.dll \
        libmpg123_plugin.dll \
        libwebvtt_plugin.dll \
        libpng_plugin.dll \
        libcvdsub_plugin.dll \
        libdxva2_plugin.dll \
        libadpcm_plugin.dll \
        libflac_plugin.dll \
        /opt/vlc/plugins/codec/

    cd ../video_filter
    mkdir /opt/vlc/plugins/video_filter
    mv \
        libmagnify_plugin.dll \
        libsharpen_plugin.dll \
        libfps_plugin.dll \
        liberase_plugin.dll \
        libalphamask_plugin.dll \
        libsepia_plugin.dll \
        libripple_plugin.dll \
        libmotiondetect_plugin.dll \
        libpostproc_plugin.dll \
        libextract_plugin.dll \
        libedgedetection_plugin.dll \
        libinvert_plugin.dll \
        libpuzzle_plugin.dll \
        libcroppadd_plugin.dll \
        libgradient_plugin.dll \
        libgrain_plugin.dll \
        libblend_plugin.dll \
        libposterize_plugin.dll \
        libcolorthres_plugin.dll \
        libvhs_plugin.dll \
        libwave_plugin.dll \
        libcanvas_plugin.dll \
        libpsychedelic_plugin.dll \
        libmotionblur_plugin.dll \
        libfreeze_plugin.dll \
        librotate_plugin.dll \
        libdeinterlace_plugin.dll \
        libblendbench_plugin.dll \
        liboldmovie_plugin.dll \
        libgradfun_plugin.dll \
        libscene_plugin.dll \
        libball_plugin.dll \
        libmirror_plugin.dll \
        libbluescreen_plugin.dll \
        libtransform_plugin.dll \
        libhqdn3d_plugin.dll \
        libadjust_plugin.dll \
        libanaglyph_plugin.dll \
        libgaussianblur_plugin.dll \
        libantiflicker_plugin.dll \
        libscale_plugin.dll \
        /opt/vlc/plugins/video_filter/

    cd ../services_discovery
    mkdir /opt/vlc/plugins/services_discovery
    mv \
        libwindrive_plugin.dll \
        libmediadirs_plugin.dll \
        libsap_plugin.dll \
        libpodcast_plugin.dll \
        /opt/vlc/plugins/services_discovery/

    cd ../text_renderer
    mkdir /opt/vlc/plugins/text_renderer
    mv \
        libfreetype_plugin.dll \
        libsapi_plugin.dll \
        libtdummy_plugin.dll \
        /opt/vlc/plugins/text_renderer/

    cd ../stream_filter
    mkdir /opt/vlc/plugins/stream_filter
    mv \
        libcache_read_plugin.dll \
        libhds_plugin.dll \
        libskiptags_plugin.dll \
        libcache_block_plugin.dll \
        librecord_plugin.dll \
        libinflate_plugin.dll \
        libprefetch_plugin.dll \
        libadf_plugin.dll \
        /opt/vlc/plugins/stream_filter/

    cd ../misc
    mkdir /opt/vlc/plugins/misc
    mv \
        libaudioscrobbler_plugin.dll \
        libfingerprinter_plugin.dll \
        liblogger_plugin.dll \
        libstats_plugin.dll \
        libexport_plugin.dll \
        /opt/vlc/plugins/misc/

    cd ../audio_output
    mkdir /opt/vlc/plugins/audio_output
    mv \
        libdirectsound_plugin.dll \
        libmmdevice_plugin.dll \
        libafile_plugin.dll \
        libamem_plugin.dll \
        libwaveout_plugin.dll \
        libwasapi_plugin.dll \
        libadummy_plugin.dll \
        /opt/vlc/plugins/audio_output/

    cd ../d3d11
    mkdir /opt/vlc/plugins/d3d11
    mv \
        libdirect3d11_filters_plugin.dll \
        /opt/vlc/plugins/d3d11/

    cd ../logger
    mkdir /opt/vlc/plugins/logger
    mv \
        libconsole_logger_plugin.dll \
        libfile_logger_plugin.dll \
        /opt/vlc/plugins/logger/

    cd ../keystore
    mkdir /opt/vlc/plugins/keystore
    mv \
        libfile_keystore_plugin.dll \
        libmemory_keystore_plugin.dll \
        /opt/vlc/plugins/keystore/

    cd /tmp
    rm -rf *
END_RUN

# libopenmpt
RUN <<END_RUN
    set -eux

    wget -nv "https://lib.openmpt.org/files/libopenmpt/src/libopenmpt-${OPENMPT_VERSION}+release.autotools.tar.gz" -O- | tar xz
    cd libopenmpt-${OPENMPT_VERSION}+release.autotools
    ./configure \
        --host=${MXE_TARGET} \
        --prefix=/opt/mxe/usr/${MXE_TARGET} \
        --enable-silent-rules \
        --disable-dependency-tracking \
        --enable-static \
        --disable-shared \
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
END_RUN

# libADLMIDI
RUN <<END_RUN
    set -eux

    wget -nv "https://github.com/Wohlstand/libADLMIDI/archive/refs/tags/v${ADLMIDI_VERSION}.tar.gz" -O- | tar xz
    mkdir libADLMIDI-${ADLMIDI_VERSION}/build
    cd libADLMIDI-${ADLMIDI_VERSION}/build
    ${MXE_TARGET}-cmake \
        -DlibADLMIDI_STATIC=ON \
        -DlibADLMIDI_SHARED=OFF \
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
    ${MXE_TARGET}-cmake --build . --target install -j `nproc` -v
    cd ../..
    rm -rf *
END_RUN
