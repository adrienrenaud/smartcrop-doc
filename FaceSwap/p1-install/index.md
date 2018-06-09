# Install Faceswap


```bash
$ cd ~/projects/smartcrop/lib/
$ git clone https://github.com/deepfakes/faceswap.git
$ cd faceswap/
$ docker build -t deepfakes-gpu -f Dockerfile.gpu .
$ sudo apt-get install xorg
$ sudo apt install x11-xserver-utils
$ xhost +local:
$ nvidia-docker run -p 8888:8888  \
  --name faceswap-gpu   \
  -v $PWD:/srv   \
  deepfakes-gpu


python faceswap.py train -A /srv/photo/trump -B /srv/photo/cage -m /srv/models/
```
