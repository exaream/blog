---
date: 2021-07-31
lastmod: ["lastmod"]
title: Python OCR
description: "How to get text from an image using Tesseract which is an OCR (Optical Character Recognition) engine and Python."
tags: ["Python", "OCR", "Tesseract"]
---

# Python OCR

## Overview
How to get text from an image using **Tesseract which is an OCR (Optical Character Recognition) engine** and Python.

## Precondition

### Version
* macOS: Big Sur 11.4
* Docker: 20.10.5, build 55c4c88
* docker-compose: 1.29.0, build 07737305
## Simple sample

### Version
* Debian GNU/Linux: 10 (buster)
* Python: 3.9.6
* pyocr: 0.8
* opencv-python: 4.5.3.56

### Directory structure
```text
python-ocr/
├── README.md
├── img
│   ├── .gitkeep
│   └── sample.jpg
├── setup
│   ├── Dockerfile
│   └── docker-compose.yml
└── simple.py
```

### Source
* https://github.com/exaream/python-ocr

```Dockerfile```
```dockerfile
FROM python:latest

RUN apt-get update

RUN apt-get -y install \
    tesseract-ocr* \
    libgl1-mesa-dev \
    vim

RUN apt-get clean

RUN pip install --upgrade pip

RUN pip install \
    pillow \
    pyocr \
    # cv2
    opencv-python \
    opencv-contrib-python

WORKDIR /opt/

ENTRYPOINT ["/usr/bin/tail", "-f", "/dev/null"]

```

```docker-compose.yml```
```yaml
version: '3'

services:
  python-ocr:
    build:
      context: ../
      dockerfile: setup/Dockerfile
    container_name: python-ocr
    volumes:
      - ../:/opt

```

```simple.py```
```python
import os
import pyocr
import sys
from PIL import Image

if len(sys.argv) != 2:
    print("Please input an image file name in the first argument.")
    exit()

img_file_name = os.path.basename(sys.argv[1])
img_path = '/opt/img/' + img_file_name
img_obj = Image.open(img_path)
builder = pyocr.builders.TextBuilder(tesseract_layout = 6)
ocr_engine = pyocr.get_available_tools()[0]
langs = ocr_engine.get_available_languages()
text = ocr_engine.image_to_string(img_obj, lang='jpn', builder=builder)

print(langs)
print(text)

```

### Preparation

```pytyon-ocr``` directory on your host machine
```bash
docker-compose -f setup/docker-compose.yml up -d --build
docker exec -it python-ocr bash
```

### Run

```docker container```
```bash
cd /opt/
python simple.py sample.jpg
```

## Advanced sample

* grayscale
* threshold processing.
* named argument
### Version

* Same as "simple sample" above.
### Directory structure
```text {hl_lines=["7-11"]}
python-ocr/
├── README.md
├── img
│   ├── .gitkeep
│   └── sample.jpg
├── library
│   ├── base.py
│   ├── cv.py
│   └── ocr.py
├── main.py
├── setup
│   ├── Dockerfile
│   └── docker-compose.yml
└── simple.py
```

### Source
* https://github.com/exaream/python-ocr

```base.py```
```python
import os

from typing import NoReturn

class Base:

    arg_parser = None
    img_dir = ''

    def __init__(self) -> NoReturn:
        self.set_img_dir()

    def get_file_name(self, file_path: str) -> str:
        return os.path.basename(file_path)

    # Get file name without extension.
    def get_file_stem(self, file_path: str) -> str:
        file_name = self.get_file_name(file_path)
        return os.path.splitext(file_name)[0]

    def is_file(self, file_path: str) -> bool:
        return os.path.isfile(file_path)

    def set_img_dir(self, img_dir: str = '') -> NoReturn:
        if img_dir != '':
            self.img_dir = img_dir
        else:
            dir = os.path.dirname(os.path.abspath(__file__)) + '/../img/'
            self.img_dir = os.path.abspath(dir)

    def get_img_dir(self) -> str:
        return self.img_dir

```

```cv.py```
```python
import cv2 # OpenCV (Open Source Computer Vision Library)

from typing import NoReturn

class ComputerVision:

    def get_obj(self, img_path: str) -> cv2:
        return cv2.imread(img_path)

    def get_grayscale_obj(self, obj: cv2) -> cv2:
        return cv2.cvtColor(obj, cv2.COLOR_BGR2GRAY)

    # Thresholding
    # Convert colors other than black (if a RBG value is greater than a threshold) to white RGB(255,255,255).
    def get_threshold_obj(self, obj: cv2, threshold: int = 170) -> cv2:
        return cv2.threshold(obj, threshold, 255, cv2.THRESH_BINARY)[1]

    def write_obj_to_img(self, obj: cv2, img_path: str) -> NoReturn:
        cv2.imwrite(img_path, obj)

```

```ocr.py```
```python
import pyocr

from PIL import Image
from pyocr.builders import TextBuilder
from typing import NoReturn

class OpticalCharacterRecognition:

    ocr_engine = None

    def __init__(self) -> NoReturn:
        self.__set_ocr_engine()

    def get_img_obj(self, img_path: str) -> Image:
        return Image.open(img_path)

    def get_available_languages(self) -> list:
        return self.ocr_engine.get_available_languages()

    def get_text_by_img_obj(self, img_obj: Image, builder: TextBuilder,
            lang: str = 'eng') -> str:
        return self.ocr_engine.image_to_string(img_obj, lang=lang,
            builder=builder)

    def get_builder(self, tesseract_layout: int = 3) -> TextBuilder:
        return TextBuilder(tesseract_layout=tesseract_layout)

    def __set_ocr_engine(self) -> NoReturn:
        self.ocr_engine = pyocr.get_available_tools()[0]

```

```main.py```
```python
import argparse
from library import base, cv, ocr

# Initialize classes.
sample_base = base.Base()
sample_cv = cv.ComputerVision()
sample_ocr = ocr.OpticalCharacterRecognition()

langs = sample_ocr.get_available_languages()

# Get arguments.
arg_parser = argparse.ArgumentParser()
arg_parser.add_argument('--img', type=str, required=True)
args = arg_parser.parse_args()

# Set image paths.
img_file_name = sample_base.get_file_name(args.img)
img_file_stem = sample_base.get_file_stem(args.img)
img_dir = sample_base.get_img_dir() + '/'

img_org_path = img_dir + img_file_name
img_gray_path = img_dir + img_file_stem + '-gray.png'
img_threshold_path = img_dir + img_file_stem + '-threshold.png'
if sample_base.is_file(img_org_path) == False:
    print("The file doesn't exist: " + img_org_path)
    exit()

# Get CV (Computer Vision Library) object.
cv_obj = sample_cv.get_obj(img_org_path)

# Apply grayscale to the image.
cv_obj = sample_cv.get_grayscale_obj(cv_obj)
sample_cv.write_obj_to_img(cv_obj, img_gray_path)

# Apply Threshold processing to the image.
cv_obj = sample_cv.get_threshold_obj(cv_obj)
sample_cv.write_obj_to_img(cv_obj, img_threshold_path)

# Get text by the processed image.
img_obj = sample_ocr.get_img_obj(img_threshold_path)
builder = sample_ocr.get_builder()
text = sample_ocr.get_text_by_img_obj(img_obj, builder=builder, lang='jpn')

print(langs)
print(text)

```

### Run
Run below if the docker container is stopped.  
```pytyon-ocr``` directory on your host machine
```bash
docker-compose -f setup/docker-compose.yml up -d
docker exec -it python-ocr bash
```

```docker container```
```bash
cd /opt/
python main.py --img sample.jp
```

## References
* [dockerhub python](https://hub.docker.com/_/python)
* [pyocr](https://gitlab.gnome.org/World/OpenPaperwork/pyocr)
* [Tesseract](https://en.wikipedia.org/wiki/Tesseract)
* [tesseract-ocr](https://github.com/tesseract-ocr/tesseract)