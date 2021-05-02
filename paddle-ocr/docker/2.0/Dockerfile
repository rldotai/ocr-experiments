# # Version: 2.0.0
FROM registry.baidubce.com/paddlepaddle/paddle:2.0.0-gpu-cuda10.1-cudnn7
LABEL maintainer="rldotai@rl.ai"

# PaddleOCR base on Python3.7
RUN pip3.7 install --upgrade pip -i https://mirror.baidu.com/pypi/simple

RUN pip3.7 install paddlehub --upgrade -i https://mirror.baidu.com/pypi/simple

RUN git clone https://github.com/PaddlePaddle/PaddleOCR.git /PaddleOCR

WORKDIR /PaddleOCR

RUN pip3.7 install -r requirements.txt -i https://mirror.baidu.com/pypi/simple

RUN mkdir -p /PaddleOCR/inference/

# The names of the various models
ARG CLS_MODEL="ch/ch_ppocr_mobile_v2.0_cls_infer"
ARG DET_MODEL="ch/ch_ppocr_mobile_v2.0_det_infer"
ARG REC_MODEL="ch/ch_ppocr_mobile_v2.0_rec_infer"

# Copy models over
COPY "models/${CLS_MODEL}" /PaddleOCR/inference/
COPY "models/${DET_MODEL}" /PaddleOCR/inference/
COPY "models/${REC_MODEL}" /PaddleOCR/inference/

# Extract the models
RUN tar xf "/PaddleOCR/inference/$(basename ${CLS_MODEL})" -C /PaddleOCR/inference/
RUN tar xf "/PaddleOCR/inference/$(basename ${DET_MODEL})" -C /PaddleOCR/inference/
RUN tar xf "/PaddleOCR/inference/$(basename ${REC_MODEL})" -C /PaddleOCR/inference/

# Copy the parameters over
ARG OCR_SYSTEM_PARAMS="params_mobile.py"
COPY "${OCR_SYSTEM_PARAMS}" "deploy/hubserving/ocr_system/params.py"

# Port used by HubServing
EXPOSE 8866

CMD ["/bin/bash","-c","hub install deploy/hubserving/ocr_system/ && hub serving start -m ocr_system"]
