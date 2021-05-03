# Dockerfile for Text Recognition Benchmark
FROM nvidia/cuda:11.3.0-runtime-ubuntu18.04
LABEL maintainer="rldotai@rl.ai"

# From the miniconda Dockerfile
# https://hub.docker.com/r/continuumio/miniconda3/dockerfile
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && \
    apt-get install -y wget bzip2 ca-certificates curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ARG MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh"
RUN wget --quiet ${MINICONDA_URL} -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -tipsy && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

# Update conda
RUN conda update -n base -c defaults conda

# # Install additional dependencies
RUN --mount=type=cache,id=apt-dev,target=/var/cache/apt \
    apt-get update && apt-get install -y --no-install-recommends \
        ffmpeg \
        libsm6 \
        libxext6 \
        && rm -rf /var/lib/apt/lists/*


WORKDIR /workdir

# # Install Python dependencies
# RUN conda create -y --name app \
#     --channel pytorch \
#     --channel nvidia \
#     --channel conda-forge \
#     python=3.8 \
#     pip \
#     ipython \
#     lmdb python-lmdb \
#     natsort \
#     opencv=3 \
#     pillow \
#     pytorch \
#     torchvision \
#     cudatoolkit=11.1 \
#     scipy \
#     scikit-image

# From `requirements.txt`
# torch==0.4.1.post2
# torchvision==0.2.1
# opencv-python==3.4.2.17
# scikit-image==0.14.2
# scipy==1.1.0


# Copy conda env file
ARG CONDA_ENV_FILE="environment.yml"
COPY ${CONDA_ENV_FILE} .
RUN conda env create -f environment.yml


# Download the repo
RUN git clone https://github.com/clovaai/CRAFT-pytorch.git /workdir/CRAFT-pytorch \
    && cd /workdir/CRAFT-pytorch \
    && git checkout e332dd8b718e291f51b66ff8f9ef2c98ee4474c8


# Make RUN commands use the new environment:
SHELL ["conda", "run", "-n", "app", "/bin/bash", "-c"]

RUN python -c "import torch; print(torch.cuda.is_available())" > cuda_check.txt

# Copy files over
ARG MODEL_PATH="models/craft_mlt_25k.pth"
ARG MODEL_NAME="craft_mlt_25k.pth"
COPY ${MODEL_PATH} "/workdir/models/${MODEL_NAME}"
