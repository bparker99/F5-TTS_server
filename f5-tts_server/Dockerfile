FROM nvidia/cuda:12.8.0-cudnn-devel-ubuntu22.04

USER root

ARG DEBIAN_FRONTEND=noninteractive

LABEL github_repo="https://github.com/SWivid/F5-TTS"

RUN set -x \
    && apt-get update \
    && apt-get -y install wget curl man git less openssl libssl-dev unzip unar build-essential aria2 tmux vim \
    && apt-get install -y openssh-server sox libsox-fmt-all libsox-fmt-mp3 libsndfile1-dev ffmpeg \
    && apt-get install -y librdmacm1 libibumad3 librdmacm-dev libibverbs1 libibverbs-dev ibverbs-utils ibverbs-providers \
    && apt-get install -y libmagic1 \
    && apt-get install -y python3 python3-pip \
    && python3 -m pip install --upgrade pip \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean
    
WORKDIR /workspace


# Install PyTorch and other common deep learning libraries compatible with CUDA 12.8
# This is crucial for F5-TTS
RUN pip3 install --no-cache-dir \
    torch \
    torchvision \
    torchaudio \
    --index-url https://download.pytorch.org/whl/cu128

RUN git clone https://github.com/ValyrianTech/F5-TTS_server.git \
    && cd F5-TTS_server \
    && git submodule update --init --recursive \
    && pip install -e . --no-cache-dir

# Install FastAPI and other dependencies
RUN pip install fastapi uvicorn python-multipart python-magic pydub

ENV SHELL=/bin/bash

WORKDIR /workspace/F5-TTS

# Create directory for the server files
RUN mkdir -p /workspace/F5-TTS/server

# Create necessary directories
RUN mkdir -p /workspace/F5-TTS/server/outputs /workspace/F5-TTS/server/resources

# Copy the server files
COPY server.py /workspace/F5-TTS/server/
COPY start.sh /workspace/F5-TTS/server/
COPY demo_speaker0.mp3 /workspace/F5-TTS/server/resources/
RUN chmod +x /workspace/F5-TTS/server/start.sh

# Set the working directory to the server directory
WORKDIR /workspace/F5-TTS/server

# Set the entrypoint to our start script
ENTRYPOINT ["./start.sh"]
