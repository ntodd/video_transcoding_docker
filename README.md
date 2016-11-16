# Video Transcoding for Docker

Docker support for [https://github.com/donmelton/video_transcoding](https://github.com/donmelton/video_transcoding)

The Docker image is [available on Docker Hub](https://hub.docker.com/r/ntodd/video-transcoding/).

## Prerequisites

You must be running [Docker for Mac](https://docs.docker.com/engine/installation/mac/), [Docker for Linux](https://docs.docker.com/engine/installation/linux/), or [Docker for Windows](https://docs.docker.com/engine/installation/windows/).

## Usage

To run the video_transcoding gem in Docker, execute the following:

```
# Docker for Mac & Linux
docker run -itv `pwd`:/data ntodd/video-transcoding /bin/bash

# Docker for Windows
docker run -itv C:\My\Current\Path:/data ntodd/video-transcoding /bin/bash
```

This will:
1. Download the `ntodd/video-transcoding` Docker image (unless already downloaded)
2. Mount the current working directory on your host machine as a shared volume inside the container
3. Run an interactive bash shell with access to your current directory and the video_transcoding cli tools

For best results on Docker for Mac or Windows, set your CPU count in preferences to the maximum available for your machine.
