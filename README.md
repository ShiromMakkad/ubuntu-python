# ubuntu-python

[![Docker Stars](https://img.shields.io/docker/stars/rcarmo/ubuntu-python.svg)](https://hub.docker.com/r/rcarmo/ubuntu-python)
[![Docker Pulls](https://img.shields.io/docker/pulls/rcarmo/ubuntu-python.svg)](https://hub.docker.com/r/rcarmo/ubuntu-python)
[![](https://images.microbadger.com/badges/image/rcarmo/ubuntu-python.svg)](https://microbadger.com/images/rcarmo/ubuntu-python "Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/rcarmo/ubuntu-python.svg)](https://microbadger.com/images/rcarmo/ubuntu-python "Get your own version badge on microbadger.com")

A base Docker container for running Python apps with an Ubuntu userland, based on [alpine-python](https://github.com/rcarmo/alpine-python) and build for three different architectures:

* `amd64`
* `arm32v7` (Pi 2+ or other ARM 32-bit boards like the ODROID U2+)
* `arm64v8` (Pi 4 or other ARM 64-bit boards, as well as the WSL images for Windows on ARM)

## Why

`alpine-python` makes for awesome small containers, but it's a pain to deal with all the binary wheels related to machine/deep learning stuff like Tensorflow, `numpy`, etc., so I decided to bite the bullet and take on the extra bloat that comes with an Ubuntu distro.

## Status & Roadmap

* [ ] Multi-stage, "stripped" builds (requires some tuning of the `onbuild` images)
* [x] Multi-arch builds
* [ ] CI builds (WIP due to long build times - Travis errors out)
* [x] Python 3.8.5
* [x] Python 3.7.5
* [x] Python 3.7.3
* [x] Normalize architecture tags (`arm32v7`, `amd64`, etc. to match Docker naming conventions)
* [x] Python 3.7.0
* [x] Move to Ubuntu 18.04 base image
* [x] LTO (experimental) optimizations, inspired by [revsys](https://github.com/revsys/optimized-python-docker)
* [x] Initial `armhf` containers
* [x] Initial `amd64` containers, plain + `onbuild`
* [x] Python 3.6.3 (`amd64`)
* [x] Scaffolding for multiarch builds
* [x] Base userland with required libraries for building Python 3.6


## Usage

This image runs the `python` command on `docker run`. You can either specify your own command, e.g:
```shell
docker run --rm -ti rcarmo/ubuntu-python python hello.py
```

Or extend this image using your custom `Dockerfile`, e.g:
```dockerfile
FROM rcarmo/ubuntu-python:onbuild
# or FROM rcarmo/ubuntu-python:3.8-onbuild-amd64 if you prefer to specify your architecture

# for a flask server
EXPOSE 5000
CMD python manage.py runserver
```

Dont' forget to build _your_ image:
```shell
docker build --rm=true -t rcarmo/app .
```

You can also access `bash` inside the container:
```shell
docker run --rm -ti rcarmo/ubuntu-python /bin/bash
```

Another option is to build an extended `Dockerfile` version (like shown above), and mount your application inside the container:
```shell
docker run --rm -v "$(pwd)":/home/app -w /home/app -p 5000:5000 -ti rcarmo/app
```

## Details

* Builds the Python interpreter with optimizations enabled, for a significant performance boost
* You can use the `latest` or `onbuild` tags, but every variant is tagged with its architecture and build step purpose
* Uses `make altinstall` to have Python 3.8 coexist with the built-in Ubuntu Python (which is nearly impossible to eradicate anyway)
* Just like the main `python` docker image, it creates useful symlinks that are expected to exist, e.g. `python3.8` > `python`, `pip3.8` > `pip`, etc.)
