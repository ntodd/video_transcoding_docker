# ntodd/video-transcoding:0.15.0

FROM ntodd/ruby-xenial:2.3.1
MAINTAINER Nate Todd

RUN apt-get update -qq && apt-get install -y software-properties-common
RUN add-apt-repository ppa:stebbins/handbrake-releases

# Install dependencies
RUN apt-get update -qq && apt-get install -y \
  ffmpeg \
  mkvtoolnix \
  mp4v2-utils \
  mplayer \
  handbrake-cli

# Install video_transcoding gem
RUN gem install video_transcoding -v 0.15.0

# Set work directory
RUN mkdir /data
WORKDIR /data

CMD [ "/bin/bash" ]
