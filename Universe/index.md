# Universe

Install

aws deep learning ami
c5.4xlarge
install docker

```python
keys = ['up', 'left up', 'right up', 'up x']
```
44206
5900

Dockerfile:

```bash
FROM ubuntu:16.04

RUN apt-get update \
    && apt-get install -y libav-tools \
    python3-numpy \
    python3-scipy \
    python3-setuptools \
    python3-pip \
    libpq-dev \
    libjpeg-dev \
    curl \
    cmake \
    swig \
    python3-opengl \
    libboost-all-dev \
    libsdl2-dev \
    wget \
    unzip \
    git \
    golang \
    net-tools \
    iptables \
    libvncserver-dev \
    software-properties-common \
    htop \
    tmux \
    lsof \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN ln -sf /usr/bin/pip3 /usr/local/bin/pip \
    && ln -sf /usr/bin/python3 /usr/local/bin/python \
    && pip install -U pip

# Install gym
RUN pip install gym==0.9.5
RUN pip install opencv-python tensorflow

# Get the faster VNC driver
RUN pip install go-vncdriver>=0.4.0

# Install pytest (for running test cases)
RUN pip install pytest

# Force the container to use the go vnc driver
ENV UNIVERSE_VNCDRIVER='go'

WORKDIR /app
RUN git clone https://github.com/openai/universe.git
RUN pip install -e /app/universe


# Just in case any python cache files were carried over from the source directory, remove them
RUN py3clean .
```

```bash
docker build -t universe .
git clone https://github.com/openai/universe-starter-agent.git

docker run --privileged -it -e DOCKER_NET_HOST=172.17.0.1 -p 12345:12345 -v $PWD/logs:/app/logs -v $PWD/universe-starter-agent:/app/universe-starter-agent  -v /var/run/docker.sock:/var/run/docker.sock universe bash
python train.py --num-workers 32 --env-id flashgames.FormulaRacer2012-v0 --log-dir /app/logs/formularacer
```
