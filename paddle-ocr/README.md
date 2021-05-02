
# Paddle OCR

The [PaddleOCR project](https://github.com/PaddlePaddle/PaddleOCR) has released a number of useful tools, in particular their text detection/recognition models.
They're also [wildly productive with deep learning in general](https://github.com/PaddlePaddle), judging from the projects on GitHub.

The models are modular, in the sense that you need text detection, direction classification, and recognition models to get something to perform OCR.
There are ["server sized", "mobile sized", and "slim" models provided](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.1/doc/doc_en/models_list_en.md); the server models are about 150M altogether, while the mobile models are around 7.5M in total.
The slim models are a bit smaller than the mobile ones, but there currently isn't a full set of them.

# Using PaddleOCR

If you have a CUDA-capable GPU:

1. First, you need to have Docker and the NVIDIA extension to support CUDA.
    This is actually pretty easy to get working; I [recently wrote a post](https://rl.ai/posts/ubuntu-docker-gpu-cuda/) about how to get everything installed on Ubuntu 20.04.
1. Then you need to download the models and build the container.
    I setup the Makefile with rules for both the "mobile" and "server" versions, so building the server image can be done via:
    ```bash
    build-server-container-2.0
    ```
1. Run the container:
    ```bash
    run-server-container-2.0
    ```
    The model will run until you terminate it (e.g., `Ctrl-C`)
1. Feed it images via the JSON API

You can do this with Bash via:

```bash
curl -H "Content-Type:application/json" \
    -X POST \
    --data "{\"images\": [\"<IMAGE>, \"]}" http://localhost:8866/predict/ocr_system
```
where `<IMAGE>` is the Base64 encoded string for your image.

Or, with Python:
```python
import base64
import io
import requests
from PIL import Image

# Load the image, save it to a buffer, and encode
path = "/path/to/your/image.jpeg"
imag = Image.open(path)
buff = io.BytesIO()
imag.save(buff, format="jpeg")
data = base64.b64encode(buff.getvalue()).decode("ascii")

# Send the image to the server
res = requests.post(
    "http://localhost:8866/predict/ocr_system",
    json={"images": [data]}
)
print(res.json())
```


# Notes and remarks

## Building the containers

They [provide some starter code] to get PaddleOCR running in a container, but I had to modify the Dockerfiles to get everything working.
These models can be run from HubServing and communicated with via a JSON API.

In the [`Makefile`](./Makefile) there are a bunch of rules just for downloading the models into a reasonable directory structure.
Building the images can be done via specifying which models (and parameters) to use via `--build-args`.

The 2.0 versions, which the starter code is based off of, are pretty recondite as far as containers go, and although I'm not a Docker expert, I feel like they could be streamlined somewhat.
It seems like it starts with a standard NVIDIA container but then kinda goes off into the weeds, and at the end it installs a bunch of Python wheels of strange provenance for multiple versions of Python; I don't know what's going on there.
They're also using an older version of CUDA and CuDNN, so I'm trying to get the 2.1 version working, since they've apparently added a bunch of new models.

# Reference

- [PaddleOCR text recognition documentation](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.1/doc/doc_en/recognition_en.md)
- [PaddleOCR and Docker](https://github.com/PaddlePaddle/PaddleOCR/tree/release/2.1/deploy/docker/hubserving)
- [PaddleOCR HubServing documentation](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.1/deploy/hubserving/readme_en.md)
