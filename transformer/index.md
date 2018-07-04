# Transformer

## Intro

Attention Is All You Need: https://arxiv.org/abs/1706.03762

The dominant sequence transduction models are based on complex recurrent or convolutional neural networks in an encoder-decoder configuration. The best performing models also connect the encoder and decoder through an attention mechanism. We propose a new simple network architecture, the Transformer, based solely on attention mechanisms, dispensing with recurrence and convolutions entirely. Experiments on two machine translation tasks show these models to be superior in quality while being more parallelizable and requiring significantly less time to train. Our model achieves 28.4 BLEU on the WMT 2014 English-to-German translation task, improving over the existing best results, including ensembles by over 2 BLEU. On the WMT 2014 English-to-French translation task, our model establishes a new single-model state-of-the-art BLEU score of 41.8 after training for 3.5 days on eight GPUs, a small fraction of the training costs of the best models from the literature. We show that the Transformer generalizes well to other tasks by applying it successfully to English constituency parsing both with large and limited training data.

## Install

On a GCP ubuntu 18.04 with P100 GPU and docker/nvidia-docker.

```bash
# pull the container
docker pull tensorflow/tensorflow:1.9.0-rc2-gpu-py3

# start the container
nvidia-docker run --name transformer -d -it -p 8888:8888 -v $PWD:/app tensorflow/tensorflow:1.9.0-rc2-gpu-py3  bash
# nvidia-docker run --name transformer -d -it -p 8888:8888 -v $PWD:/app tensorflow/tensorflow:latest-gpu bash

# log to it
nvidia-docker exec -it transformer bash

# install transformer
cd /app
apt-get update
apt-get install python-tk
pip install tensor2tensor[tensorflow_gpu]
pip install jupyterlab
pip install gutenberg
jupyter-lab --allow-root --ip 0.0.0.0  --no-browser
```
Start an ssh tunnel on port 8888 and see notebook at http://localhost:8888/lab



```bash
t2t-trainer \
  --generate_data \
  --data_dir=/app/t2t_data \
  --output_dir=/app/t2t_train/mnist \
  --problem=sentiment_imdb \
  --model=transformer_encoder \
  --hparams_set=transformer_tiny \
  --train_steps=2000 \
  --eval_steps=100
```

## Py2 or py3

We should use py3, right? especially for NLP.
Unfortunately gutenberg install fails on my tensorflow gpu py3 docker.


## Our own dataset

We explore the example where a new problem/dataset is added to t2t.

https://tensorflow.github.io/tensor2tensor/new_problem.html

```bash
USR_DIR=/app/test/poetry
DATA_DIR=/app/test/t2t_data
TMP_DIR=/app/test/t2t_datagen
OUTPUT_DIR=/app/test/t2t_train/poetry2
MODEL=transformer
HPARAMS=transformer_base_single_gpu
PROBLEM=poetry_lines
mkdir -p $DATA_DIR $TMP_DIR

t2t-datagen \
  --t2t_usr_dir=$USR_DIR \
  --data_dir=$DATA_DIR \
  --tmp_dir=$TMP_DIR \
  --problem=$PROBLEM

t2t-trainer \
  --data_dir=$DATA_DIR \
  --t2t_usr_dir=$USR_DIR \
  --output_dir=$OUTPUT_DIR \
  --problem=$PROBLEM \
  --model=$MODEL \
  --hparams_set=$HPARAMS \
  --train_steps=2000 \
  --eval_steps=100


DECODE_FILE=$DATA_DIR/decode_this.txt
echo "Two roads diverged in a yellow wood," >> $DECO
BEAM_SIZE=4
ALPHA=0.6

t2t-decoder \
  --data_dir=$DATA_DIR \
  --t2t_usr_dir=$USR_DIR \
  --problem=$PROBLEM \
  --model=$MODEL \
  --hparams_set=$HPARAMS \
  --output_dir=$OUTPUT_DIR \
  --decode_hparams="beam_size=$BEAM_SIZE,alpha=$ALPHA" \
  --decode_from_file=$DECODE_FILE \
  --decode_to_file=poetry_next_line.txt
```
