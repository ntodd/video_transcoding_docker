# ntodd/video-transcoding:0.17.3

FROM ntodd/ruby-xenial:2.4.0
LABEL maintainer="Nate Todd"

ENV GEM_VERSION 0.17.3

# install build dependencies to compile ffmpeg from master
RUN set -ex \
	&& buildDeps=' \
    autoconf \
    automake \
    build-essential \
    libass-dev \
    libfreetype6-dev \
    libsdl2-dev \
    libtheora-dev \
    libtool \
    libva-dev \
    libvdpau-dev \
    libvorbis-dev \
    libxcb1-dev \
    libxcb-shm0-dev \
    libxcb-xfixes0-dev \
    pkg-config \
    texinfo \
    wget \
    zlib1g-dev \
    yasm \
    libx264-dev \
    cmake \
    mercurial \
    libmp3lame-dev \
    libopus-dev \
    libvpx-dev \
    libx264-dev \
    unzip \
  ' \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends $buildDeps \

# set up build dir
  && mkdir -p /usr/src/ffmpeg/bin \
  && mkdir -p /usr/src/ffmpeg/build \
  && PATH="/usr/src/ffmpeg/bin:$PATH" \
  && cd /usr/src/ffmpeg \

# NASM
  && wget http://www.nasm.us/pub/nasm/releasebuilds/2.13.01/nasm-2.13.01.tar.bz2 \
  && tar xjvf nasm-2.13.01.tar.bz2 \
  && cd nasm-2.13.01 \
  && ./autogen.sh \
  # && ./configure \
  && PATH="/usr/src/ffmpeg/bin:$PATH" ./configure --prefix="/usr/src/ffmpeg/build" --bindir="/usr/src/ffmpeg/bin" \
  && PATH="/usr/src/ffmpeg/bin:$PATH" make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf nasm-2.13.01 \

# libx265
  && hg clone https://bitbucket.org/multicoreware/x265 \
  && cd x265/build/linux \
  && PATH="/usr/src/ffmpeg/bin:$PATH" cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="/usr/src/ffmpeg/build" -DENABLE_SHARED:bool=off ../../source \
  # && cmake -G "Unix Makefiles" -DENABLE_SHARED:bool=off ../../source \
  && make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf x265 \

# libfdk-aac
  && wget -O fdk-aac.tar.gz https://github.com/mstorsjo/fdk-aac/tarball/master \
  && tar xzvf fdk-aac.tar.gz \
  && cd mstorsjo-fdk-aac* \
  && autoreconf -fiv \
  && ./configure --prefix="/usr/src/ffmpeg/build" --disable-shared \
  # && ./configure --disable-shared \
  && make -j"$(nproc)" \
  && make install \
  && cd /usr/src/ffmpeg \
  && rm -rf mstorsjo-fdk-aac* \

# ffmpeg
  # Snapshot of ffmpeg master https://github.com/FFmpeg/FFmpeg/tree/d8269519e4a338fa9521612db9b68c04a0fb2deb
  && wget -O ffmpeg.zip https://github.com/FFmpeg/FFmpeg/archive/d8269519e4a338fa9521612db9b68c04a0fb2deb.zip \
  && unzip ffmpeg.zip \
  && mv FFmpeg* ffmpeg_src \
  && cd ffmpeg_src \
  && PATH="/usr/src/ffmpeg/bin:$PATH" PKG_CONFIG_PATH="/usr/src/ffmpeg/build/lib/pkgconfig" ./configure \
    --prefix="/usr/src/ffmpeg/build" \
    --pkg-config-flags="--static" \
    --extra-cflags="-I/usr/src/ffmpeg/build/include" \
    --extra-ldflags="-L/usr/src/ffmpeg/build/lib" \
    --bindir="/usr/src/ffmpeg/bin" \
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

# Move to /usr/local/bin
  && mv /usr/src/ffmpeg/bin/ff* /usr/local/bin \
  && rm -rf /usr/src/ffmpeg \

# Install video_transcoding dependencies
  && apt-get update -qq && apt-get install -y --no-install-recommends software-properties-common \
  && add-apt-repository ppa:stebbins/handbrake-releases \
  && apt-get update -qq \
  && apt-get install -y --no-install-recommends \
    mkvtoolnix \
    mp4v2-utils \
    mpv \
    handbrake-cli \

# cleanup
  && apt-get remove -y software-properties-common \
  && apt-get purge -y --auto-remove $buildDeps \
  && rm -rf /var/lib/apt/lists/* \

# Install video_transcoding gem
  && gem install video_transcoding -v "$GEM_VERSION" \

# create data dir
  && mkdir /data

WORKDIR /data

CMD [ "/bin/bash" ]
