# ntodd/video-transcoding:0.16.0

FROM ntodd/ruby-xenial:2.4.0
MAINTAINER Nate Todd

ENV GEM_VERSION 0.16.0

RUN set -ex \
  && apt-get update -qq && apt-get install -y --no-install-recommends software-properties-common \
  && add-apt-repository ppa:stebbins/handbrake-releases \
  && apt-get update -qq \
  && apt-get install -y --no-install-recommends \
    ffmpeg \
    mkvtoolnix \
    mp4v2-utils \
    mplayer \
    handbrake-cli \
  && apt-get remove -y software-properties-common && apt-get autoremove -y \
  && rm -rf /var/lib/apt/lists/* \
  && gem install video_transcoding -v "$GEM_VERSION" \
  && mkdir /data

WORKDIR /data

CMD [ "/bin/bash" ]
