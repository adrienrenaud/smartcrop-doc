# From detectron to smartcrop

We have a detectron image: `docker build -t detectron:c2-cuda9-cudnn7 .`


## Build new images

Dockerfile:
````docker
FROM detectron:c2-cuda9-cudnn7

RUN mkdir /smartcrop
WORKDIR /smartcrop

RUN pip install -e .

RUN pip install jupyterlab==0.32.1
RUN pip install moviepy==0.2.3.5
```

setup.py:
```python
from setuptools import setup

setup(name='smartcrop',
      version='0.1',
      description='Smart croping of videos',
      # url='http://github.com/storborg/funniest',
      author='Adrien Renaud',
      author_email='adrien.renaud@gmail.com',
      license='MIT',
      packages=['smartcrop'],
      zip_safe=False)
```

```bash
# create project structure
$ cd ~/projects/smartcrop
$ mkdir smartcrop
$ cd smartcrop
$ mkdir smartcrop
$ touch smartcrop/__init__.py
$ touch smartcrop/helpers.py
$ touch smartcrop/task.py
$ mkdir docker
$ touch docker/Dockerfile
$ touch run_task.py
$ cp -r  ../lib/Detectron/configs/12_2017_baselines/ ./configs/
$ wget https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl \
  -P weights/e2e_mask_rcnn_R-101-FPN_2x/

# create data dir and put a clip in input
$ mkdir data/input
$ mkdir data/output


# run container
$ docker build -t smartcrop:latest .
$ nvidia-docker run -dit --name smartdude -p 8888:8888 -v $PWD:/smartcrop smartcrop:latest
$ docker exec smartdude pip install -e .
$ docker exec -it smartdude bash -c "jupyter-lab --allow-root  --ip=0.0.0.0 --no-browser"
```
