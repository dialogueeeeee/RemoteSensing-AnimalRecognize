# Recognition-RemoteSensingAnimal
基于深度学习的遥感影像牛羊马识别。

## 概述
- 根据数据类型和格式（采用 *lableIme* 标注的语义分割格式），采用 **目标检测** 和 **实例分割** 方法尝试；
- 由于数据量较少，采用 **预训练** 和 **数据增强** 是非常必须的手段；
- 预测结果示例（目标检测）。

![image](https://user-images.githubusercontent.com/59753705/160855029-c45cbb0f-9f70-436d-b6b1-3526f729ecbf.png)

## 用自己数据集训练
由于使用自己的遥感图像数据集，应按以下步骤修改例程文件。
- （可选）修改 `configs/_base_/datasets/coco_detection.py` 中的 image_scale 建议改成 (800,500) ，*（可根据训练条件选择）* 这里是多尺度训练；
- 修改 `configs/_base_/models/faster_rcnn_r50_fpn.py` 中的 **num_classes=x** ，x 为自己数据中的目标类别个数；
- 修改 `mmdet/core/evaluation/class_names.py` 中的 `def coco_classes` 中 `return` 的类别，修改为自己数据集中的类别；
- （不建议）修改官方库 ``mmdet/datasets/coco.py`` 中的 `class CocoDataset` 中的 **CLASSES=** 改为自己数据集中的类别；
- （建议）新建自己的 ``self_dataset.py`` 文件，其中自己的 `dataset` 类需要继承官方的 `CustomDataset` 类。

## Usage
- `json2xml.py` 用于 `.json` 和 `.xml` 格式转化；
- `labelIme2coco.py` 用于 **lableIme** 标注生成的 `.json` 文件和 **coco 格式** 转化；
- 按照官网 https://mmdetection.readthedocs.io/en/v2.21.0/get_started.html 配置 **mmdet** 框架；
- 按照 **VSCode debugger launch config** 配置运行文件。

## VSCode debugger launch config
- instance segmentation
``` json
{
    "env": {"PYTHONPATH" : "${workspaceRoot}"},
    "name": "train:debug",
    "type": "python",
    "request": "launch",
    "program": "${workspaceRoot}/tools/train.py",
    "console": "integratedTerminal",
    "justMyCode": false,
    "args": ["configs/det/decoupled_solo_r50_fpn_1x_coco.py","--gpus", "1"]
},
```
- object detection
``` json
{
    "env": {"PYTHONPATH" : "${workspaceRoot}"},
    "name": "train:debug",
    "type": "python",
    "request": "launch",
    "program": "${workspaceRoot}/tools/train.py",
    "console": "integratedTerminal",
    "justMyCode": false,
    "args": ["configs/det/faster_rcnn_r101_fpn_1x_coco.py.py","--gpus", "1"]
},
```
