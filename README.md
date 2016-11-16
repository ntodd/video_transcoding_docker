# Video Transcoding for Docker

Docker support for [https://github.com/donmelton/video_transcoding](https://github.com/donmelton/video_transcoding)

The Docker image is [available on Docker Hub](https://hub.docker.com/r/ntodd/video-transcoding/).

## Prerequisites

You must be running [Docker for Mac](https://docs.docker.com/engine/installation/mac/) or [Docker for Linux](https://docs.docker.com/engine/installation/linux/).  Docker for Windows does not (as of Nov 16, 2016) support the interactive shell used in the run command.

## Usage

To run the video_transcoding gem in docker, execute the following command:

```
docker run -itv `pwd`:/data ntodd/video-transcoding /bin/bash
```

This will:
1. Download the `ntodd/video-transcoding` docker image (unless already downloaded)
2. Mount the current working directory on your host machine as a shared volume inside the container
3. Run an interactive bash shell with access to your current directory and the video_transcoding cli tools

For best results on Docker for Mac, set your CPU count in preferences to the maximum available for your machine.
