# ==============================================================================
# A Makefile for building and running deep-text-recognition-benchmark images.
# https://github.com/clovaai/deep-text-recognition-benchmark
# ==============================================================================


# -----------------------------------------------------------------------------
# Resources (models and packages etc)
# -----------------------------------------------------------------------------

# Text recognition
DTRB_MODEL_FILE := models/TPS-ResNet-BiLSTM-Attn.pth

# Text detection
CRAFT_MODEL_FILE := models/craft_mlt_25k.pth

# Build-Args
MINICONDA_URL := "https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh"

# -----------------------------------------------------------------------------
# The actual targets
# -----------------------------------------------------------------------------

.phony: help
help:
	@echo "A Makefile for building containers for Clova AI's Deep Text Recognition Benchmark."
	@echo "Try running:"
	@echo "\t make build-dtrb-container && make run-dtrb-container"


# Building containers
# -----------------------------------------------------------------------------

.PHONY: build-dtrb-container
build-dtrb-container: dtrb-model
	DOCKER_BUILDKIT=1 docker build . \
		-t clovaai:dtrb \
		-f docker/DTRB/Dockerfile \
		--build-arg MINICONDA_URL=${MINICONDA_URL} \
		--build-arg MODEL_PATH=${DTRB_MODEL_FILE} \
		--build-arg MODEL_NAME="$(notdir ${DTRB_MODEL_FILE})"


.PHONY: build-craft-container
build-craft-container: craft-model
	DOCKER_BUILDKIT=1 docker build . \
		-t clovaai:craft \
		-f docker/CRAFT/Dockerfile \
		--build-arg MINICONDA_URL=${MINICONDA_URL} \
		--build-arg MODEL_PATH=${CRAFT_MODEL_FILE} \
		--build-arg MODEL_NAME="$(notdir ${CRAFT_MODEL_FILE})"


# Running the containers
# -----------------------------------------------------------------------------

.PHONY: run-dtrb-container
run-dtrb-container: build-dtrb-container
	docker run -it --rm --gpus all \
		-v $(realpath ./images):/workdir/images \
		--name clovaai_dtrb \
		clovaai:dtrb bash

.PHONY: run-craft-container
run-craft-container: build-craft-container
	docker run -it --rm --gpus all \
		-v $(realpath ./images):/workdir/images \
		--name clovaai_craft \
		clovaai:craft bash


# Model file rules
# -----------------------------------------------------------------------------
.PHONY: dtrb-model
dtrb-model: ${DTRB_MODEL_FILE}
	@echo "$@:" "$<"

.PHONY: craft-model
craft-model: ${CRAFT_MODEL_FILE}
	@echo "$@:" "$<"

# Display message about how to acquire models
models/%:
	@echo "********************************************************************"
	@echo "As of 2021/05/01, the models are hosted on Google Drive and Dropbox."
	@echo "It's probably easiest to just download them yourself and save them"
	@echo "in the proper directory (./models/)"
	@echo ""
	@echo "The model you're looking for should be named:"
	@echo "\t$(notdir $@)"
	@echo "And should be saved to:"
	@echo "\t$@"
	@echo ""
	@echo "Once the required model is present, this message should go away."
	@echo ""
	@echo "For details and links, see the repos:"
	@echo "https://github.com/clovaai/deep-text-recognition-benchmark"
	@echo "https://github.com/clovaai/CRAFT-pytorch"
	@echo "********************************************************************"

# -----------------------------------------------------------------------------
# Trick for downloading from Google Drive
# -----------------------------------------------------------------------------
# A possibly fragile hack for downloading the models
# It didn't seem to work for me, so I'd be wary of using it...

# Model URLs
TRBA_URL := https://drive.google.com/open?id=1b59rXuGGmKne1AuHnkgDzoYgKeETNMv9
TRBA_ID := 1b59rXuGGmKne1AuHnkgDzoYgKeETNMv9

CRAFT_MLT_URL := https://drive.google.com/file/d/1Jk4eGD7crsqCCg9C9VjCLkMN3ze8kutZ/view
CRAFT_MLT_ID := 1Jk4eGD7crsqCCg9C9VjCLkMN3ze8kutZ


# Not working? File too small.
download-models:
	curl -c /tmp/cookies "https://drive.google.com/uc?export=download&id=${TRBA_ID}" > /tmp/intermezzo.html
	curl -L -b /tmp/cookies \
		"https://drive.google.com$(cat /tmp/intermezzo.html | grep -Po 'uc-download-link" [^>]* href="\K[^"]*' | sed 's/\&amp;/\&/g')" \
		> ${TRBA_MODEL_FILE}

# Not working? File too small.
download-craft:
	curl -c /tmp/cookies "https://drive.google.com/uc?export=download&id=${CRAFT_MLT_ID}" > /tmp/intermezzo.html \
	&& curl -L -b /tmp/cookies \
		"https://drive.google.com$(cat /tmp/intermezzo.html | grep -Po 'uc-download-link" [^>]* href="\K[^"]*' | sed 's/\&amp;/\&/g')" \
		> ${CRAFT_MODEL_FILE}

