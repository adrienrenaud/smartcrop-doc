# Faceit

https://github.com/goberoi/faceit


## Install

Run docker from faceswap. Copy faceit.pt script and modify.


## Run

apt-get install ffmpeg

pip install requests
pip install moviepy
pip install youtube-dl

import imageio
imageio.plugins.ffmpeg.download()



python tools.py effmpeg -a extract -i data/video/fallon_mom.mp4 -o data/frame/fallon/
python tools.py effmpeg -a extract -i data/video/oliver_trumpcard.mp4 -o data/frame/oliver/
python tools.py effmpeg -a gen-vid -i data/frame/oliver/ -o data/process/oliver_trumpcard.mp4 -r data/video/oliver_trumpcard.mp4
python tools.py effmpeg -a gen-vid -i data/frame/oliver -o data/process/oliver_trumpcard.mp4 -fps 60000/1001

python tools.py effmpeg -a get-fps -i data/video/oliver_trumpcard.mp4
ffmpeg -f image2  -framerate 60000/1001 -i data/frame/oliver/oliver_trumpcard.mp4_%00004d.png -vcodec libx264 -profile:v high444 -refs 16 -crf 0 -preset ultrafast input_0.mp4



ffmpeg -i data/video/fallon_mom.mp4 data/frame/fallon/movie%d.jpg -vcodec libx264 -profile:v high444 -refs 16 -crf 0 -preset ultrafast

ffmpeg -i data/video/fallon_mom.mp4 -y -vf fps="60000/1001"  data/frame/oliver/movie%d.png



ffmpeg -hide_banner -i /faceswap/data/frame/oliver/oliver_trumpcard.mp4_%05d.png -y -c:v libx264 -vf fps=60000/1001 /faceswap/data/process/oliver_trumpcard.mp4



python tools.py effmpeg -a get-fps -i data/video/cpp.mp4
python tools.py effmpeg -a extract -i data/video/cpp.mp4 -o data/frame/cpp/ -fps 25/1
python tools.py effmpeg -a gen-vid -i data/frame/cpp -o data/process/cpp.mp4 -fps 25/1
python tools.py effmpeg -a mux-audio -i data/process/cpp.mp4 -o data/process/cpp_audio.mp4 -r data/video/cpp.mp4 -fps 25/1




python tools.py effmpeg -a get-fps -i data/video/oliver_trumpcard.mp4
python tools.py effmpeg -a get-fps -i data/video/fallon_mom.mp4
python tools.py effmpeg -a get-fps -i data/video/cpp.mp4


### Good


ffmpeg -i /faceswap/data/frame/fallon/movie%d.png -y -c:v libx264 -vf fps="30000/1001" /faceswap/data/process/fallon_mom.mp4



ffmpeg -i /faceswap/data/process/fallon_mom.mp4 -i data/video/fallon_mom.mp4 -c copy -map 0:0 -map 1:1 -shortest /faceswap/data/process/fallon_mom_audio.mp4



## Workflow

Video to frame:

ffmpeg -i data/video/fallon_mom.mp4 -vf fps="30000/1001" -t 10 data/frame/fallon/movie%d.png
ffmpeg -i data/video/oliver_trumpcard.mp4 -vf fps="60000/1001" -t 5 data/frame/oliver/movie%d.png



Frame to face:

python faceswap.py extract -i /faceswap/data/frame/fallon -o /faceswap/data/face/fallon -f /faceswap/data/person/fallon.jpg
python faceswap.py extract -i /faceswap/data/frame/oliver -o /faceswap/data/face/oliver -f /faceswap/data/person/oliver.jpg


Face to model:

python faceswap.py train -A /faceswap/data/face/oliver -B /faceswap/data/face/fallon -m /faceswap/models/ -g 1



Swap faces:

python faceswap.py convert -i /faceswap/data/frame/oliver -o /faceswap/data/frameswap/oliver -m /faceswap/models/ \
      --blur-size 40 \
      --converter Masked \
      --match-histogram \
      --erosion-kernel-size 4 \
      --mask-type facehullandrect


python faceswap.py convert -i /faceswap/data/frame/fallon -o /faceswap/data/frameswap/fallon -m /faceswap/models/ \
      --blur-size 40 \
      --converter Masked \
      --match-histogram \
      --erosion-kernel-size 4 \
      --mask-type facehullandrect


Reconstruct video:


ffmpeg -framerate 60000/1001 -i /faceswap/data/frameswap/oliver/movie%d.png -y /faceswap/data/process/oliver_trumpcard.mp4
ffmpeg -framerate 30000/1001 -i /faceswap/data/frameswap/fallon/movie%d.png -y  /faceswap/data/process/fallon_mom.mp4


Add Audio:

ffmpeg -i /faceswap/data/process/oliver_trumpcard.mp4 -i data/video/oliver_trumpcard.mp4 -y -c copy -map 0:0 -map 1:1 -shortest /faceswap/data/process/oliver_trumpcard_audio.mp4


ffmpeg -i /faceswap/data/process/fallon_mom.mp4 -i data/video/fallon_mom.mp4-y -c copy -map 0:0 -map 1:1 -shortest /faceswap/data/process/fallon_mom_audio.mp4




## Workflow cpp


python tools.py effmpeg -a get-fps -i data/video/josties.mp4
ffmpeg -i data/video/cpp.mp4 -vf fps="25/1" -t 10 data/frame/cpp/movie%d.png
ffmpeg -i data/video/josties.mp4 -vf fps="1199/50" -t 10 data/frame/josties/movie%d.png

python faceswap.py extract -i /faceswap/data/frame/cpp -o /faceswap/data/face/cpp -f /faceswap/data/person/cpp.png
python faceswap.py extract -i /faceswap/data/frame/josties -o /faceswap/data/face/josties -f /faceswap/data/person/josties.png


python faceswap.py train -A /faceswap/data/face/josties -B /faceswap/data/face/cpp -m /faceswap/models/model_v2/ -g 1

python faceswap.py convert -i /faceswap/data/frame/josties -o /faceswap/data/frameswap/josties_cpp -m /faceswap/models/model_v2/ \
      --blur-size 20 \
      --converter Masked \
      --match-histogram \
      --erosion-kernel-size 40 \
      --mask-type facehullandrect

      --seamless \


      ffmpeg -framerate 1199/50  -i /faceswap/data/frameswap/josties_cpp/movie%d.png -y /faceswap/data/process/josties_cpp.mp4
      ffmpeg -framerate 1199/50  -i /faceswap/data/frameswap/josties_cpp/movie%d.png  -c:v libx264 -y /faceswap/data/process/josties_cpp.mp4


ffmpeg -i /faceswap/data/process/josties_cpp.mp4 -i data/video/josties.mp4 -y -c copy -map 0:0 -map 1:1 -shortest /faceswap/data/process/josties_cpp_audio.mp4
