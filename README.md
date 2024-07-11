# HRNet with Custom Dataset

This repository is a mirror of the original High-Resolution Network (HRNet) repository, which can be found [here](https://github.com/leoxiaobin/deep-high-resolution-net.pytorch). The primary purpose of this repository is to provide instructions and code modifications for training HRNet on your own custom dataset.

## Getting Started

To get started with training your own custom dataset using HRNet, follow these steps:

### 1. Clone the Repository

First, clone this repository to your local machine:

```bash
git clone https://github.com/MaxRondelli/HRNet-with-Custom-Dataset.git
```
To complete the installation refer to the original repository of [HRNet](https://github.com/leoxiaobin/deep-high-resolution-net.pytorch)

### 2. Prepare Your Custom Dataset
Your custom dataset should be organized in a specific structure. In my experiment I use the structure of COCO. 

Create a data folder and the structure must look like that: 
```
data/
  custom_dataset/
    annotations/
        train.json
        val.json
    images/
        train/
        val/
```
The json structure follows COCO structure as well. This is an example of mine `train.json`.
```json
{
  "images": [
    {
      "id": 2,
      "file_name": "train_image.png",
      "width": 1280,
      "height": 720
    }, 
  ],
  "annotations": [
    {
      "id": 2,
      "image_id": 2,
      "category_id": 1,
      "keypoints": [
          0,
          0,
          0,
          0,
          0,
          0,
          559,
          253,
          2,
          0,
          0,
          0,
          433,
          300,
          2,
          0,
          0,
          0,
          481,
          244,
          2,
          0,
          0,
          0,
          483,
          196,
          2,
          0,
          0,
          0,
          368,
          158,
          2,
          0,
          0,
          0
      ],
      "num_keypoints": 5,
      "bbox": [
          368,
          158,
          191,
          142
      ],
      "area": 27122,
      "iscrowd": 0
    },
  ],
  "categories": [
          {
            "id": 1,
            "name": "person",
            "supercategory": "person",
            "keypoints": [ your keypoints ],
            "skeleton": []
          }
  ]
}
```
Note: the structure of the dataset is critical. Both of the json files and how the folders are organized. If not respected, the network has difficulty to learn.

### 3. Configure the Model
Modify the configuration files located in the experiments directory to suit your dataset and training parameters. 

Update the DATASET section to point to your custom dataset:

```yaml
DATASET:
  COLOR_RGB: true
  DATASET: 'custom_dataset'
  DATA_FORMAT: png
  FLIP: false
  NUM_JOINTS_HALF_BODY: 6
  PROB_HALF_BODY: 0.3
  ROOT: 'HRNet-with-Custom-Dataset/data/custom_dataset/'
  ROT_FACTOR: 45
  SCALE_FACTOR: 0.35
  TRAIN_SET: 'train'
  TEST_SET: 'val'
```
### 4. Adjust Keypoints
In the original COCO dataset, there are 17 keypoints. However, in mine custom dataset, there are only 12 keypoints. You must modify the keypoint configurations accordingly to your dataset.

In the configuration file (.yaml) update the `NUM_JOINTS` parameter:
```yaml
MODEL:
  NUM_JOINTS: 12
```
Note: you will definitely have to change other values during the experiment. Whenever you see some mismatch where a shape is highlighted (12,) is different from (17,) it means you have to change something because the number of keypoints is different.

### 5. Train the Model
To start training, run the following command:
```bash
python3 tools/train.py --cfg HRNet-Human-Pose-Estimation/experiments/coco/hrnet/w48_384x288_adam_lr1e-3.yaml
```

### 6. Inference on image and video
In the `Demo` folder you can run tests on images and videos of the model trained with your custom dataset. Edit the yaml file specifically with the custom model checkpoint.
You can run it in this way: 

```bash
python3 demo.py --video video.mkv --write
```
or
```bash
python3 inference.py --cfg inference-config.yaml --videoFile video.mkv --writeBoxFrames --outputDir /output TEST.MODEL_FILE tools/output/custom_dataset/pose_hrnet/w48_384x288_adam_lr1e-3/model_best.pth
```
## Results for my Custom Dataset
The dataset has 374 images in total divided in 296 for training and 78 for validation.  

Lower results are obtained than the state-of-the-art presented in the paper. However, good human-pose estimation can be achieved at the inference time, resulting in a practical product that can be used for the purposes of the experiment with your custom dataset.

| Method    | Backbone|Pretrain| Input size | #Params | GFLOPs | AP    | AP^50 | AP^75 | AP^M  | AP^L  | AR    |
|-----------|---------|--------|-----------|---------|--------|-------|-------|-------|-------|-------|-------|
| HRNet-W32 | HRNet-W32 | N | 256×192| 28.5M   | 7.1    | 0.384 | 0.761 | 0.321 | 0.103 | 0.398 | 0.473 |
| HRNet-W32 | HRNet-W32 | N | 384×288| 28.5M   | 16.0   | 0.384 | 0.795 | 0.278 | 0.129 | 0.397 | 0.492 |
| HRNet-W48 | HRNet-W48 | N | 256×192| 63.6M   | 14.6   | 0.501 | **0.863** | 0.452 | **0.252** | 0.515 | 0.560 |
| HRNet-W48 | HRNet-W48 | N | 384×288| 63.6M   | 32.8   | **0.526** | 0.831 | **0.537** | 0.077 | **0.548** | **0.581** |
## Acknowledgments
This repository is based on the original HRNet repository. I extend our gratitude for their pioneering work in high-resolution visual recognition.

For detailed documentation and advanced usage, please refer to the original HRNet repository.

