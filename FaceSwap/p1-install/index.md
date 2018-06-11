# Install Faceswap


```bash
$ cd ~/projects/smartcrop/lib/
$ git clone https://github.com/deepfakes/faceswap.git
$ cd faceswap/
$ docker build -t deepfakes-gpu -f Dockerfile.gpu .
$ nvidia-docker run -p 8888:8888  \
  --name faceswap   \
  -v $PWD:/srv   \
  deepfakes-gpu


python faceswap.py train -A /faceswap/photo/trump -B /faceswap/photo/cage -m /faceswap/models/ -g 1
```


python faceswap.py convert -i /srv/photo/trump/ -o /srv/photo/output/ -m /srv/models/ --alignments /srv/photo/trump/alignments.json
python faceswap.py convert -i /srv/photo/trump_light/ -o /srv/photo/output2/ -m /srv/models/ \
       --alignments /srv/photo/trump/alignments.json


python faceswap.py convert -i /srv/photo/trump/ -o /srv/photo/output2/ -m /srv/models/ \
      --blur-size 40 \
      --converter Masked \
      --erosion-kernel-size -100 \
      --seamless \
      --mask-type facehullandrect \
      --sharpen bsharpen


python faceswap.py convert -i /srv/photo/trump_light/ -o /srv/photo/output2/ -m /srv/models/ \
      --blur-size 0 \
      --converter Masked \
      --match-histogram \
      --erosion-kernel-size 0 \
      --mask-type facehullandrect


    python faceswap.py convert -i /srv/photo/trump_light/ -o /srv/photo/output2/ -m /srv/models/ \
          --blur-size 0 \
          --converter Masked \
          --match-histogram \
          --erosion-kernel-size 0 \
          --mask-type facehullandrect
