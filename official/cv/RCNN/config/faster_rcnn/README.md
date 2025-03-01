# FasterRCNN

***

Paper: [Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](https://arxiv.org/abs/1506.01497)

label: object detection

***

## Introduction

Faster R-CNN is a two-stage object detection network that uses RPN and can share the convolutional features of the entire image with the detection network, 
allowing for almost cost free region candidate calculations. 
The entire network further combines RPN and Fast R-CNN into one network by sharing convolutional features.

<img src="https://user-images.githubusercontent.com/40661020/143881188-ab87720f-5059-4b4e-a928-b540fb8fb84d.png"/>

## Dataset

Dataset: [COCO2017](https://cocodataset.org/)

After downloading on the official website, organize the dataset into the following structure:

```text
└── coco2017
     ├── train2017
     ├── val2017
     └── annotation
          ├── captions_train2017.json
          ├── captions_val2017.json
          ├── instances_train2017.json
          ├── instances_val2017.json
          ├── person_keypoints_train2017.json
          └── person_keypoints_val2017.json
```

## Environment Requirements

- Hardware（Ascend/GPU/CPU）
  - Prepare hardware environment with Ascend processor. Reference [MindSpore](https://www.mindspore.cn/install/en) Installation and operation environment
- Dependency: MindSpore >= 2.0

```shell
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## BenchMark

### mAP

| Model | pretrained Model | config | Device Num | Epoch | mAP(0.5~0.95) | FPS |CheckPoint | Graph Train Log |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| FasterRCNN R50-FPN | [R50](https://github.com/mindspore-lab/mindcv) | [cfg](config/faster_rcnn/faster_rcnn_resnet50_fpn_1x.yml) | 8 | 12 | 37.3 | 123.01 |[download](https://download.mindspore.cn/model_zoo/official/cv/rcnn/FasterRCNN_det_resnet50_epoch12_rank0.ckpt) | [download](https://download.mindspore.cn/model_zoo/official/cv/rcnn/FasterRCNN_det_resnet50_epoch12_rank0.log) |

## Quick Start

change path of the Coco dataset in:`cconfig/coco.yml`:

```text
dataset_dir: "your coco"
```

### Training

```shell
# single card
python train.py --config [CONFIG_PATH] --device_target [DEVICE_TARGET] --mix True
```

```shell
# multiple cards using openmpi
mpirun --allow-run-as-root -n [DEVICE_NUM] --merge-stderr-to-stdout \
    python train.py --config [CONFIG_PATH] --device_target [DEVICE_TARGET] --mix True
```

If you want to use breakpoint training function, add resume_ckpt, such as:

```shell
python train.py --config [CONFIG_PATH] --device_target [DEVICE_TARGET] --mix True --resume_ckpt [CHECKPOINT_PATH]
```

### Evaluation：

```shell
# single card
python eval.py --config [CONFIG_PATH] --device_target [DEVICE_TARGET] --mix True --ckpt_path [CHECKPOINT_PATH]

# multiple cards using openmpi
mpirun --allow-run-as-root -n [DEVICE_NUM] --merge-stderr-to-stdout 、
    python eval.py --config [CONFIG_PATH] --device_target [DEVICE_TARGET] --mix True --ckpt_path [CHECKPOINT_PATH]
```

### Training on ModelArts

1. Configure the ModelArts parameter in the config file:

- set enable_modelarts=True
- set OBS dataset path data_url: <the path of the dataset in OBS>
- set OBS output results path train_url: <The path of output results in OBS>

2. Refer to [ModelArts](https://support.huaweicloud.com/modelarts/index.html) start training.

## FAQ

Refer to [Models FAQ](https://gitee.com/mindspore/models#FAQ) Firstly to get some common FAQs.

Q: What to do when encountering a WARNING with insufficient memory or too many threads?

A: Modify `num_workers` which is parallel number, `prefetch_size` which is dataset cache queue length, `max_rowsize` which is Maximum memory usage of a piece of data in config`.

Generally, excessive CPU usage requires reduction `num_workers`; Excessive memory usage needs to be reduced `num_workers`, `prefetch_size` and `max_rowsize`.

Q: What should I do if loss does not converge on GPU?

A: set`mix` to `False`.

## Citation

```latex
@article{Ren_2017,
   title={Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks},
   journal={IEEE Transactions on Pattern Analysis and Machine Intelligence},
   publisher={Institute of Electrical and Electronics Engineers (IEEE)},
   author={Ren, Shaoqing and He, Kaiming and Girshick, Ross and Sun, Jian},
   year={2017},
   month={Jun},
}
```
