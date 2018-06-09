# SmartCrop

 Edit and create special effects in video using deep learning.<br>
 It will blow your mind!

#### What

THE company in deep learning workload processing for video:
- a web app to edit video
- endpoint offering video editing services to other apps

#### How

Staffing:
- adrien => full magic time
- cyprien => on ne sait pas encore

Meta tools:
- web app
- cloud
- gpu
- container
- deep learning
- container orchestration


#### Why

We have the knowledge and this is a good opportunity.

Today a 5000$ GPU is cropping in image in 0.1 second. A typical movie is 25 frames by second.

Professionals video editor can leverage deep learning to increase productivity  but they don't because it is not available at the moment in the standard software suites. We bet that movie editing will shift to the cloud because state of the art models represent (and alway will) a very high compute workload.<br>

Clip edition is also part of modern apps. We can offer high quality services to other apps and do much better than false mustaches if the compute workload is moved to the cloud.



## Spec

Hardware:
- google cloud engine (gce) for web serving
- google cloud engine for compute
- google cloud storage (gcs) to store videos
- GPU P100 added to some compute gce instances

Software:
- detectron
- moviepy
- flask
- vuejs
- uWSGI/Nginx
- docker
- kubernetes


## Typical workflow

Given:
- Kubernetes cluster with three nodes
- One master, two workers
- Two services: back and front
- Front exposes a web app
- Back computes


A possible workflow is:
- User upload clip on the front
- clip is saved in storage by the front
- front notifies back to process the clip
- front tells back where to find clip
- back processes clip
- back saves clip in storage
- back notifies front that clip is processed
- back tells front where to find process output
- front exposes process output
