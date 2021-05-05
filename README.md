# OCR Experiments

My experiments with some neural Optical Character Recognition OCR models.
At the moment, I'm really just referencing other people's code, although eventually I'll have some more details on my own extensions.

The main novelty at the moment is that I'm trying to get everything running in containers so that *other* people might be able to use them as well.
Currently, my interest is mostly due to needing to process reams of notes written in the course of [my research](https://rl.ai/pages/research/), rather than anything more cutting edge.
However, if that sounds like the sort of thing that might be useful to you, I've created a few utilities [in my `pdf-stuff` repo](https://github.com/rldotai/pdf-stuff) which might be of interest.

## Functional Models

I've been looking at projects based on reported metrics, papers, and GitHub activity.
The ones I've managed to get running so far are:

- [Clova AI Research's `CRAFT-PyTorch` and Deep Text Recognition Benchmark](./clova-ai)
- [PaddleOCR v2.0](./paddle-ocr)
- [Handwriting OCR](./handwriting-ocr)
    - Semi-functional, I think the pinned version of OpenCV needs to be updated.

Each of these has corresponding Dockerfiles and a Makefile, which means anyone should be able to get something running fairly easily.

# TODO

- [ ] Sample code and results for each listed repo.
- [ ] Change entrypoints so they can be used as one-off utilities.
- [ ] Fix user/groupid for containers.
- [ ] Ensure that containers leverage GPU/CUDA where possible.
- [ ] In general, modify Dockerfiles to obey "best practices".
- [ ] Script or `make` rule for downloading models from Google Drive.
