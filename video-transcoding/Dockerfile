# ntodd/video-transcoding:0.12.3

FROM ntodd/ruby-xenial:2.3.1
MAINTAINER Nate Todd

# Install dependencies
RUN apt-get update -qq && apt-get install -y \
  ffmpeg \
  mkvtoolnix \
  mp4v2-utils \
  mplayer \
  handbrake-cli

# Install video_transcoding gem
RUN gem install video_transcoding -v 0.12.3

# Set work directory
RUN mkdir /data
WORKDIR /data

CMD [ "/bin/bash" ]
