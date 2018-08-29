# NOAA Fish Finding

## Table of Contents
- <a href='##Introduction'>Introduction</a>
- <a href='#Prerequisites'>Prerequisites</a>
- <a href='#Installation'>Installation</a>
- <a href='#Setup Data'>Setup Data</a>
- <a href='#Training'>Training</a>
- <a href='#Testing'>Testing</a>
- <a href='#Demo'>Demo</a>
- <a href='#references'>Reference</a>

## Introduction
### Overview
This data challenge is a workshop in 2018 CVPR. Large amounts of image data have been collected and annotated by the National Oceanic and Atmospheric Administration (NOAA) from a a variety of image and video underwater.

[workshop website](http://www.viametoolkit.org/cvpr-2018-workshop-data-challenge/)
### Datasets
The data releases are comprised of images and annotations from five different data sources, with six datasets in total.

- HabCam: abcam_seq0
- MOUSS: mouss_seq0, mouss_seq1
- AFSC DropCam: afsc_seq0
- MBARI: mbari_seq0
- NWFSC: nwfsc_seq0

Each dataset contains different types of imagery — different lighting conditions, camera angles, and wildlife. The data released depends on the nature of the data in the entire dataset.

[Datasets detail](http://www.viametoolkit.org/cvpr-2018-workshop-data-challenge/challenge-data-description/)

### Scoring
The challenge will evaluate accuracy in detection and classification, following the format in the [MSCOCO Detection Challenge](http://cocodataset.org/#detection-2017), for **bounding box** output. 
The annotations for scoring are bounding boxes around every animal, with a species classification label for each.

## Prerequisites
- Python 3+
- Tensorflow >= 1.6.0
- pytorch
- Python package `cython`, `opencv-python`, `easydict`

## Installation
1. Clone the repository
  ```
  git clone https://github.com/endernewton/tf-faster-rcnn.git
  ```
2. Download Pre-trained model
```
sh data/scripts/setModel.sh
```

3. Update GPU arch

Update your -arch in setup script to match your GPU
  ```
  cd tf-faster-rcnn/lib
  # Change the GPU architecture (-arch) if necessary
  vim setup.py
  ```

  | GPU model    | Architecture |
  | ------------- | ------------- |
  | TitanX (Maxwell/Pascal) | sm_52 |
  | GTX 960M | sm_50 |
  | GTX 1080 (Ti) | sm_61 |
  | Grid K520 (AWS g2.2xlarge) | sm_30 |
  | Tesla K80 (AWS p2.xlarge) | sm_37 |

4. bulid Faster-RCNN
Build the Cython modules
  ```Shell
  make clean
  make
  cd ..
  ```


## Setup Data
Download [Training data](https://challenge.kitware.com/girder#collection/5a722b2c56357d621cd46c22/folder/5ada227756357d4ff856f54d) (270.7 GB)

Download [Testing data](https://challenge.kitware.com/girder#item/5af21e0f56357d4ff85723d6) (272.1 GB)


  
## Training
- for Faster-RCNN
```
./experiments/scripts/train_faster_rcnn.sh [GPU_ID] [DATASET] [NET]

# GPU_ID is the GPU you want to test on
# NET in {vgg16, res50, res101, res152} is the network arch to use
# DATASET {mouss_seq0, mouss_seq1, mbari_seq0, habcam_seq0} is defined in train_faster_rcnn.sh

# Examples:
./experiments/scripts/train_faster_rcnn.sh 0 mouss_seq1 vgg16
./experiments/scripts/train_faster_rcnn.sh 1 mbari_seq0 res101
```
- for SSD 300/512
```
$ python3 train.py --dataset [DATASET] --ssd_size [300/512]

# DATASET {mouss_seq0, mouss_seq1, mbari_seq0, habcam_seq0}
```

## Testing
before testing, remember to remove cache of last predict
```
$ rm data/cache/*
$ rm data/VOCdevkit2007/annotations_cache/*
```

- for Faster-RCNN
```
./experiments/scripts/test_faster_rcnn.sh [GPU_ID] [DATASET] [NET]

# GPU_ID is the GPU you want to test on
# NET in {vgg16, res50, res101, res152} is the network arch to use
# DATASET {mouss_seq0, mouss_seq1, mbari_seq0, habcam_seq0} is defined in test_faster_rcnn.sh

# Examples:
./experiments/scripts/test_faster_rcnn.sh 0 mouss_seq1 vgg16
./experiments/scripts/test_faster_rcnn.sh 1 mbari_seq0 res101
```

- for SSD 300/512
```
python eval.py --dataset [DATASET] --ssd_size [300/512]

# DATASET {mouss_seq0, mouss_seq1, mbari_seq0, habcam_seq0}
```
## Demo
- for Faster RCNN
```
python3 tools/demo.py --net [NET] --train_set [TRAIN]--test_set [TEST] --mode [predict/ both]

# NET {VGG16/ResNet101}
# TRAIN {mouss_seq0, mouss_seq1, mbari_seq0, habcam_seq0}
# TEST {mouss1/2/3/4/5, mbari1, habcam}
# MODE: 
predict: only plot prediction
both: ground truth and prediction
```

Detection Snapshot:
![Imgur](https://i.imgur.com/taxCKzh.png)
![Imgur](https://i.imgur.com/11R8K7i.png)
![Imgur](https://i.imgur.com/oaev5Kr.png)
![Imgur](https://i.imgur.com/8hu6HQD.png)

## Reference
[1] Faster-RCNN: https://arxiv.org/abs/1506.01497

[2] SSD: Single Shot MultiBox Detector: https://arxiv.org/abs/1512.02325

[3] tf-faster-RCNN: https://github.com/endernewton/tf-faster-rcnn

[4] ssd.pytorch: https://github.com/amdegroot/ssd.pytorch