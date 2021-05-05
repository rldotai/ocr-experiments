# ==============================================================================
# A Makefile for building and running `handwriting-ocr` containers.
# https://github.com/Breta01/handwriting-ocr
# ==============================================================================


# -----------------------------------------------------------------------------
# Resources (models and packages etc)
# -----------------------------------------------------------------------------

# Directory containing models
MODEL_DIR := ./models

# Build-Args
MINICONDA_URL := "https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh"
USER_ID := $(shell id -u)
GROUP_ID := $(shell id -g)

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

.PHONY: build-handwriting-ocr-container
build-handwriting-ocr-container:
	DOCKER_BUILDKIT=1 docker build . \
		-t handwriting-ocr:infer \
		-f docker/Dockerfile \
		--build-arg USER_ID=${USER_ID} \
		--build-arg GROUP_ID=${GROUP_ID} \
		--build-arg MINICONDA_URL=${MINICONDA_URL} \
		--build-arg MODEL_DIR=${MODEL_DIR}


# Running the containers
# -----------------------------------------------------------------------------

.PHONY: run-handwriting-ocr-container
run-handwriting-ocr-container: build-handwriting-ocr-container
	docker run -it --rm --gpus all \
		-v $(realpath ./images):/workdir/images \
		--name handwriting_ocr \
		-p 8877:8877 \
		handwriting-ocr:infer \
		bash


# Model file rules
# -----------------------------------------------------------------------------
.PHONY: handwriting-ocr-models
handwriting-ocr-models:
	@echo "$@:" "$<"
	@echo "********************************************************************"
	@echo "As of 2021/05/01, the models are hosted on Google Drive."
	@echo "It's probably easiest to just download them yourself and save them"
	@echo "in the proper directory (./models/)"
	@echo ""
	@echo "Once the required model is present, this message should go away."
	@echo ""
	@echo "For details and links, see the repo:"
	@echo "https://github.com/Breta01/handwriting-ocr/tree/master/models"
	@echo "********************************************************************"
