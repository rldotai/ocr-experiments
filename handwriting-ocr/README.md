
# [Handwriting OCR](https://github.com/Breta01/handwriting-ocr)

This project is nicely modular, with many example Jupyter Notebooks illustrating the details of how an OCR system can be trained and used.

The pre-trained model files are [stored in Google Drive](https://drive.google.com/file/d/1YbmsiJK3Wclfm6K8PrJuz-QROEKX1qis/view?usp=sharing), as is tradition, apparently.

Download those and save/unpack them in the `./models` directory (or elsewhere, but then you'd have to modify either the Makefile or the Dockerfile.

**Currently there seems to be an [issue](https://github.com/Breta01/handwriting-ocr/pull/133) due to the version of OpenCV being used.**
The `cv.findContours()` function used to return three values but that got changed at some point, so now you get an exception due to having the wrong number of values to unpack.


## Running the notebooks

Much of the code is provided in the form of Jupyter notebooks, but if you want to access those from *outside* the container then you have to do something like the following:

```bash
# Inside the container
jupyter notebook --no-browser --port=8877 --ip=0.0.0.0
```

Then, on the host, point your browser to the provided URL, potentially substituting the host IP for that of the container.
