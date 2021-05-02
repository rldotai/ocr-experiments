


# wget function -- there's some certificate issue w/ the hosting server
wget = wget --no-check-certificate

# The available models, current as of May 1st, 2021
# https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.1/doc/doc_en/models_list_en.md
# They're multilingual, but emphasize Chinese and English text.

# Text detection
DET_SERVER    := ch/ch_ppocr_server_v2.0_det_infer.tar
DET_MOBILE    := ch/ch_ppocr_mobile_v2.0_det_infer.tar
# DET_SLIM      := # not available

# Text angle classification
# CLS_SERVER    := # N/A
CLS_MOBILE    := ch/ch_ppocr_mobile_v2.0_cls_infer.tar
CLS_SLIM      := ch/ch_ppocr_mobile_v2.0_cls_slim_infer.tar

# Text recognition
REC_SERVER    := ch/ch_ppocr_server_v2.0_rec_infer.tar
REC_MOBILE    := ch/ch_ppocr_mobile_v2.0_rec_infer.tar
REC_SLIM      := ch/ch_ppocr_mobile_v2.0_rec_slim_infer.tar

# English alphanumeric English text recognition
REC_MOBILE_EN := en/en_number_mobile_v2.0_rec_infer.tar
REC_SLIM_EN   := multilingual/en_number_mobile_v2.0_rec_slim_infer.tar

# The models used for various configurations
SERVER_MODELS := ${DET_SERVER} ${CLS_MOBILE} ${REC_SERVER}
MOBILE_MODELS := ${DET_MOBILE} ${CLS_MOBILE} ${REC_MOBILE}
SLIM_MODELS   := ${DET_MOBILE} ${CLS_SLIM} ${REC_SLIM}

# Model files will be stored in the `models` subdirectory
SERVER_MODEL_FILES := $(addprefix models/, ${SERVER_MODELS})
MOBILE_MODEL_FILES := $(addprefix models/, ${MOBILE_MODELS})
SLIM_MODEL_FILES   := $(addprefix models/, ${SLIM_MODELS})

HOST_CH := https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch
HOST_EN := https://paddleocr.bj.bcebos.com/dygraph_v2.0/en
# HOST_ML := https://paddleocr.bj.bcebos.com/dygraph_v2.0/multilingual

.phony: help
help:
	@echo "A Makefile for building containers for PaddleOCR."
	@echo ${SERVER_MODELS}
	@echo ${SERVER_MODEL_FILES}
	@echo ${SERVER_MODEL_URLS}


# Rules for building the containers
.PHONY: build-mobile-container-2.1
build-mobile-container-2.1:
	docker build . \
		-t paddleocr:2.1-mobile-gpu \
		-f docker/2.1/Dockerfile


.PHONY: build-mobile-container-2.0
build-mobile-container-2.0: mobile-inference-models
	docker build . \
		-t paddleocr:2.0-mobile-gpu \
		-f docker/2.0/Dockerfile \
		--build-arg CLS_MODEL=${CLS_MOBILE} \
		--build-arg DET_MODEL=${DET_MOBILE} \
		--build-arg REC_MODEL=${REC_MOBILE} \
		--build-arg OCR_SYSTEM_PARAMS="params_mobile.py"

.PHONY: build-server-container-2.0
build-server-container-2.0: server-inference-models
	docker build . \
		-t paddleocr:2.0-server-gpu \
		-f docker/2.0/Dockerfile \
		--build-arg CLS_MODEL=${CLS_MOBILE} \
		--build-arg DET_MODEL=${DET_SERVER} \
		--build-arg REC_MODEL=${REC_SERVER} \
		--build-arg OCR_SYSTEM_PARAMS="params_server.py"

# Running the HubServing models
.PHONY: run-mobile-container-2.0
run-mobile-container-2.0:
	docker run -it --rm -p 8866:8866 --gpus all --name paddle_ocr paddleocr:2.0-mobile-gpu

.PHONY: run-server-container-2.0
run-server-container-2.0:
	docker run -it --rm -p 8866:8866 --gpus all --name paddle_ocr paddleocr:2.0-server-gpu


# Rules for downloading the models
.PHONY: server-inference-models
server-inference-models: ${SERVER_MODEL_FILES}
	@echo "Server models:" "$<"

.PHONY: mobile-inference-models
mobile-inference-models: ${MOBILE_MODEL_FILES}
	@echo "Mobile models:" "$<"

.PHONY: slim-inference-models
slim-inference-models: ${SLIM_MODEL_FILES}
	@echo "Slim models:" "$<"

.PHONY: english-inference-models
english-inference-models: $(addprefix models/, ${REC_MOBILE_EN})
	@echo "English inference models present..."
	@echo "$@" ":" "$<"

# The actual download rules
models/ch/%.tar:
	@echo "Downloading model..."
	$(wget) ${HOST_CH}/$(notdir $@) -O $@

models/en/%.tar:
	@echo "Downloading model..."
	$(wget) ${HOST_EN}/$(notdir $@) -O $@
