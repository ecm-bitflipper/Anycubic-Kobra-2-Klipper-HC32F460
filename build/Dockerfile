FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    gcc-arm-none-eabi \
    binutils-arm-none-eabi \
    make \
    gcc \
    g++ \
    python3 \
    python3-pip \
    python3-dev \
    libffi-dev \
    libncurses-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /root/klipper

COPY . .

RUN python3 -m pip install -r scripts/klippy-requirements.txt
