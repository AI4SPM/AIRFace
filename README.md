# Anonymous Yet Identifiable: A Recoverable Face Anonymization Framework for Privacy-Preserving Authentication


## Overview

Face images are exposed to growing privacy risks during collection, transmission,
and storage, yet most existing anonymization methods cannot balance privacy
protection with data utility: de-identification destroys the discriminative
features needed downstream, while reversible approaches depend on key management
and image reconstruction. We propose an edge–server collaborative framework that
combines **identity feature transformation** with **background information
hiding**. On the client side, the image is decomposed into a facial region and a
background region; the facial information is reversibly embedded into the
background by a hiding network, and the original ArcFace identity feature is
mapped into an anonymous feature space by a feature transformer. Only the
protected background and the anonymous feature are transmitted to the server,
which synthesizes the anonymized face with a feature-injection generator. Because
different images of the same person are mapped to a **consistent anonymous
identity**, authentication can be performed directly in the anonymous domain
without keys, image reconstruction, or auxiliary authentication messages. A
recovery network can additionally reconstruct the original face from the hidden
background for forensic analysis and accountability tracing.

## Environment

- Python 3.9
- PyTorch 2.1.1 + CUDA 12.2, 4090 NVIDIA GPU 
- `dlib` (only for preparing the face masks, see [Dataset](#dataset))



## Dataset

Both the images and the masks are stored in two mirrored trees:

```
<data_path>/
├── img/<identity_id>/<image>.jpg
└── mask/<identity_id>/<image>.jpg
```

Images are resized to `224 × 224`, and masks are binarized at `> 0.5`. Each
identity folder needs at least two images. The masks are extracted with `dlib` in
the paper, but any binary face-region mask works.



## Training


```bash
cd train

# Stage 1: hiding and recovery networks
python -m train_netHR

# Stage 2: anonymization generator
python -m train_netG

# Stage 3: anonymized feature converter
python -m train_netFC
```
Please train CompressNet before training the whole framework. Training CompressNet requires two datasets to be prepared: the original images and their corresponding JPEG-compressed images. 

The three stages are trained sequentially, each freezing the modules trained
before it. 


