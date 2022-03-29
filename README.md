# Recognition-RemoteSensingAnimal
这里是遥感影像牛羊马识别。

## 概述
- 根据数据类型和格式（采用 *lableIme* 标注的语义分割格式），采用 **语义分割** 方法尝试；
- 经分析，原标注中没有 *背景 background* 语义标签，改用 **实例分割** 方法尝试； 
- 由于数据量较少，采用**预训练**和**数据增强**是非常必须的手段。

## 按需求修改相关文件 
由于使用自己的遥感图像数据集，应按以下步骤修改例程文件。
- （可选）修改 `configs/_base_/datasets/coco_detection.py` 中的 image_scale 建议改成 (800,500) ，*（可根据训练条件选择）* 这里是多尺度训练；
- 修改 `configs/_base_/models/faster_rcnn_r50_fpn.py` 中的 **num_classes=x** ，x 为自己数据中的目标类别个数；
- 修改 `mmdet/core/evaluation/class_names.py` 中的 `def coco_classes` 中 `return` 的类别，修改为自己数据集中的类别；
- 修改官方库 ``mmdet/datasets/coco.py`` 中的 `class CocoDataset` 中的 **CLASSES=** 改为自己数据集中的类别。

## Usage
- `json2xml.py` 用于 `.json` 和 `.xml` 格式转化；
- `labelIme2coco.py` 用于 **lableIme** 标注生成的 `.json` 文件和 **coco 格式** 转化（*实例分割* 和 *目标检测*）；
- 按以下命令运行。

## 计划
- 根据输入数据类型，先将 ``.json`` 文件转化为语义分割的标签 Mask 形式文件 *（已完成：将 lableIme 格式转化为 coco 格式）* ；
- 采用 **mmdet** 框架，调用官方 **实例分割** 代码进行尝试。；
- **实例分割** 和 **目标检测** 代码出现都出现训练时 IoU 计算结果始终为 0 而且不变的问题，推测是由于数据集或者参数设置的原因；
- 将官方代码中的  ``mmdet/datasets/coco.py`` 中的 `class CocoDataset` 中的 **CLASSES=** 进行更改，解决上述问题。

## VSCode debugger launch config
- instance segmentation
``` json
{
    "env": {"PYTHONPATH" : "${workspaceRoot}"},
    "name": "knet:debug",
    "type": "python",
    "request": "launch",
    "program": "${workspaceRoot}/tools/train.py",
    "console": "integratedTerminal",
    "justMyCode": false,
    "args": ["configs/det/knet/knet_s3_r50_fpn_1x_ctw1500.py","--gpus", "1"]
},
```
- object detection
``` json
{
    "env": {"PYTHONPATH" : "${workspaceRoot}"},
    "name": "knet:debug",
    "type": "python",
    "request": "launch",
    "program": "${workspaceRoot}/tools/train.py",
    "console": "integratedTerminal",
    "justMyCode": false,
    "args": ["configs/det/knet/knet_s3_r50_fpn_1x_ctw1500.py","--gpus", "1"]
},
```
