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

# Permit specifying user and group for avoiding permissions issues
# See: https://vsupalov.com/docker-shared-permissions/
ARG USER_ID=1000
ARG GROUP_ID=1000

# Add user and group
RUN addgroup --gid $GROUP_ID user
RUN adduser --disabled-password --gecos '' --uid ${USER_ID} --gid ${GROUP_ID} user

# Work directory
WORKDIR /home/user

# Switch to user account
USER user

# Download the repo
RUN git clone https://github.com/Breta01/handwriting-ocr.git handwriting-ocr \
    && cd handwriting-ocr \
    && git checkout 61c028859ee55a53e03280b0982527905fb41abf


WORKDIR /home/user/handwriting-ocr

# Create an environment from the project's provided environment.yml
RUN conda create --name app --file environment.yml

# Make RUN commands use the new environment
# SHELL ["conda", "run", "-n", "app", "/bin/bash", "-c"]

# Copy files over
ARG MODEL_DIR="models"
COPY ${MODEL_DIR} "models"




