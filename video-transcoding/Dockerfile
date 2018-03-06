# ntodd/video-transcoding:0.18.0

FROM ntodd/ruby-xenial:2.4.0
LABEL maintainer="Nate Todd <nate@pixelauthorityllc.com>"

ENV GEM_VERSION 0.19.0

# install build dependencies to compile ffmpeg from master
RUN set -ex \
  && buildDeps=' \
    autoconf \
    automake \
    build-essential \
    git \
    libass-dev \
    libbz2-dev \
    libfontconfig1-dev \
    libfreetype6-dev \
    libfribidi-dev \
    libharfbuzz-dev \
    libjansson-dev \
    libogg-dev \
    libsamplerate-dev \
    libtheora-dev \
    libtool \
    libvorbis-dev \
    libxml2-dev \
    m4 \
    make \
    patch \
    pkg-config \
    python \
    tar \
    libtool-bin \
    texinfo \
    wget \
    zlib1g-dev \
    yasm \
    cmake \
    cmake-curses-gui \
    mercurial \
    libmp3lame-dev \
    libopus-dev \
    libvpx-dev \
    libx264-dev \
    unzip \
    mkvtoolnix \
    mp4v2-utils \
    mpv \
  ' \
  && apt-get update \
  && apt-get install -y --no-install-recommends $buildDeps \
  && mkdir -p /usr/src/ffmpeg/bin \
  && mkdir -p /usr/src/ffmpeg/build \
  && PATH="/usr/src/ffmpeg/bin:$PATH" \
  && cd /usr/src/ffmpeg \

  # NASM 2.13.01
  && wget http://www.nasm.us/pub/nasm/releasebuilds/2.13.01/nasm-2.13.01.tar.bz2 \
  && tar xjvf nasm-2.13.01.tar.bz2 \
  && cd nasm-2.13.01 \
  && ./autogen.sh \
  && PATH="/usr/src/ffmpeg/bin:$PATH" ./configure --prefix="/usr/src/ffmpeg/build" --bindir="/usr/src/ffmpeg/bin" \
  && PATH="/usr/src/ffmpeg/bin:$PATH" make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf nasm-2.13.01 \

  # libx264 stable
  && git clone -b stable http://git.videolan.org/git/x264.git x264 \
  && cd x264 \
  && PATH="/usr/src/ffmpeg/bin:$PATH" ./configure --prefix="/usr/src/ffmpeg/build" --bindir="/usr/src/ffmpeg/bin" --enable-static --disable-opencl \
  && PATH="/usr/src/ffmpeg/bin:$PATH" make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf x264-snapshot* \

  # libfdk-aac master
  && wget -O fdk-aac.tar.gz https://github.com/mstorsjo/fdk-aac/tarball/master \
  && tar xzvf fdk-aac.tar.gz \
  && cd mstorsjo-fdk-aac* \
  && autoreconf -fiv \
  && ./configure --prefix="/usr/src/ffmpeg/build" --disable-shared \
  && make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf mstorsjo-fdk-aac* \

  # libx265 2.7
  && wget -O x265.tar.gz https://bitbucket.org/multicoreware/x265/downloads/x265_2.7.tar.gz \
  && tar xzvf x265.tar.gz \
  && cd x265_2.7/build/linux \
  && PATH="/usr/src/ffmpeg/bin:$PATH" cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="/usr/src/ffmpeg/build" -DENABLE_SHARED:bool=off ../../source \
  && PATH="/usr/src/ffmpeg/bin:$PATH" make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf x265 \

  # HandbrakeCli master
  && git clone https://github.com/HandBrake/HandBrake.git \
  && cd HandBrake \
  && ./configure --launch-jobs=$(nproc) --disable-gtk --launch \
  && cd build && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf HandBrake \

  # FFmpeg 3.3.5
  && wget -O ffmpeg.zip https://github.com/FFmpeg/FFmpeg/archive/n3.3.5.zip \
  && unzip ffmpeg.zip \
  && mv FFmpeg* ffmpeg_src \
  && cd ffmpeg_src \
  && PATH="/usr/src/ffmpeg/bin:$PATH" PKG_CONFIG_PATH="/usr/src/ffmpeg/build/lib/pkgconfig" ./configure \
    --prefix="/usr/src/ffmpeg/build" \
    --pkg-config-flags="--static" \
    --extra-cflags="-I/usr/src/ffmpeg/build/include" \
    --extra-ldflags="-L/usr/src/ffmpeg/build/lib" \
    --bindir="/usr/src/ffmpeg/bin" \
    --extra-libs=-lpthread \
    --enable-gpl \
    --enable-libass \
    --enable-libfdk-aac \
    --enable-libfreetype \
    --enable-libmp3lame \
    --enable-libopus \
    --enable-libtheora \
    --enable-libvorbis \
    --enable-libvpx \
    --enable-libx264 \
    --enable-libx265 \
    --enable-nonfree \
  && PATH="/usr/src/ffmpeg/bin:$PATH" make -j"$(nproc)" \
  && make install \
  && hash -r \
  && cd / \
  && mv /usr/src/ffmpeg/bin/ff* /usr/local/bin \
  && rm -rf /usr/src/ffmpeg

RUN set -ex \
  # Install application dependencies
  && apt-get purge -y --auto-remove $buildDeps \
  && rm -rf /var/lib/apt/lists/* \
  && gem install video_transcoding -v "$GEM_VERSION" \
  && mkdir /data

WORKDIR /data

CMD [ "/bin/bash" ]
