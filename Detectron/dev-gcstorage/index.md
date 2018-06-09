# Flask deploy on docker


## Ref

https://ianlondon.github.io/blog/deploy-flask-docker-nginx/

https://github.com/tiangolo/uwsgi-nginx-flask-docker

## Install

Install 2 flask dockers, one for web server (myflask1) and one for the compute (myflask2).
Both docker communicate on a docker network.
Say from flask1 we can query flask2 as ``curl -X GET myflask2:5000``.

At the end we want a docker build from ``uwsgi-nginx-flask`` to be our web server and another docker build from ``detectron`` for the compute.




```Bash
docker network create flask-net
cd app1
docker build -t my_flask_app1 .
cd app2
docker build -t my_flask_app2 .
docker run -p 80:80 -t -d --restart=always --name myflask1 --network flask-net my_flask_app1
docker run -t -d --restart=always --name myflask2 --network flask-net my_flask_app2
docker exec -it myflask1 /bin/bash
curl -X GET myflask2:5000
```

Dockerfile1:

```dockerfile
FROM tiangolo/uwsgi-nginx-flask:python2.7
COPY ./app /app
```

Dockerfile2:
```dockerfile
FROM tiangolo/uwsgi-nginx-flask:python2.7
ENV LISTEN_PORT 5000
EXPOSE 5000
COPY ./app /app
```


## Google cloud storage

We use Google cloud storage to store input and output videos.

https://cloud.google.com/storage/getting-started/

https://cloud.google.com/storage/docs/reference/libraries

#### Instal

```bash
# create service account, get a json file, set an env var
export GOOGLE_APPLICATION_CREDENTIALS="/home/arenaud/.gcp_perso/auth/Sentiment-fde461470682.json"
gsutil ls gs://<mybucket>/<myfloder>
pip install --upgrade google-cloud-storage
```

#### Download clip from gcs and diplay in notebook

```python
import base64

from IPython.display import HTML
from google.cloud import storage

# Instantiates a client
storage_client = storage.Client()
bu = storage_client.get_bucket('perfide-ptoleme')
f = bu.get_blob('output/VID_20170811_170943.mp4')

video = f.download_as_string()
encoded = base64.b64encode(video)

HTML(data='''<video alt="test" controls>
                <source src="data:video/mp4;base64,{0}" type="video/mp4" />
             </video>'''.format(encoded.decode('ascii')))
```


#### Download clip from gcs, process it with moviepy and upload it back to gcs

```python
import moviepy.editor as me
from google.cloud import storage

storage_client = storage.Client()

## Download video from gcp and convert to moviepy

bu = storage_client.get_bucket('perfide-ptoleme')
f = bu.get_blob('output/VID_20170811_170943.mp4')

# temporary files FIXME
file_in = '/tmp/test_in.mp4'
file_out = '/tmp/test_out.mp4'

with open(file_in, 'w+') as file_obj:
    f.download_to_file(file_obj)

v = me.VideoFileClip(file_in)

# here goes the proccessing

v.write_videofile(file_out)


# Upload video to gcp

blob = bu.blob('my_bucket_folder/test.mp4')
with open(file_out, 'r') as f:
    blob.upload_from_file(f)
```
