# RemoteSensing-AnimalRecognize
基于深度学习的遥感影像牛羊马识别。

## 概述
- 根据数据类型和格式（采用 *lableIme* 标注的语义分割格式），采用 **目标检测** 和 **实例分割** 方法尝试；
- 由于数据量较少，采用 **预训练** 和 **数据增强** 是非常必须的手段；
- 基于 mmdet 的目标检测和实例分割模型训练和部署，参考 https://github.com/lifuguan/SOLOv2-mmdet 实现；
- 采用 FPN 和 ResNet50 作为 backbone ，分别使用 **Faster R-CNN** 和 **SOLOV2** 实现**目标检测** 和 **实例分割** 。
- 预测结果示例（目标检测）。

<div align=center><img src="https://user-images.githubusercontent.com/59753705/160855029-c45cbb0f-9f70-436d-b6b1-3526f729ecbf.png" width="600px"></div>

## Usage
- `json2xml.py` 用于 `.json` 和 `.xml` 格式转化；
- `labelIme2coco.py` 用于 **lableIme** 标注生成的 `.json` 文件和 **coco 格式** 转化；
- 按照官网 https://mmdetection.readthedocs.io/en/v2.21.0/get_started.html 配置 **mmdet** 框架；
- 按照 **VSCode debugger launch config** 配置运行文件。

## 用自己数据集训练
由于使用自己的遥感图像数据集，应按以下步骤修改例程文件。
- （可选）修改 `configs/_base_/datasets/coco_detection.py` 中的 image_scale 建议改成 (800,500) ，*（可根据训练条件选择）* 这里是多尺度训练；
- 修改 `configs/_base_/models/faster_rcnn_r50_fpn.py` 中的 **num_classes=x** ，x 为自己数据中的目标类别个数；
- 修改 `mmdet/core/evaluation/class_names.py` 中的 `def coco_classes` 中 `return` 的类别，修改为自己数据集中的类别；
- （不建议）修改官方库 ``mmdet/datasets/coco.py`` 中的 `class CocoDataset` 中的 **CLASSES=** 改为自己数据集中的类别；
- （建议）新建自己的 ``self_dataset.py`` 文件，其中自己的 `dataset` 类需要继承官方的 `CustomDataset` 类。

### 补充：新建自己的数据集配置文件
需要准备一个配置文件来成功加载数据集。假设我们想要用 *remotesense-animal dataset* 来训练配备了 `FPN` 的 `Mask R-CNN` ，如下是我们的配置文件。假设配置文件命名为 `mask_rcnn_r50_caffe_fpn_mstrain-poly_1x_remotesense_animal.py` ，相应保存路径为 `configs/remotesense-animal/` ，配置文件内容如下所示，更多内容详见[官网教程](https://github.com/open-mmlab/mmdetection/blob/master/docs/zh_cn/2_new_data_model.md)。
``` python
# 这个新的配置文件继承自一个原始配置文件，只需要突出必要的修改部分即可
_base_ = 'mask_rcnn/mask_rcnn_r50_caffe_fpn_mstrain-poly_1x_coco.py'

# 我们需要对头中的类别数量进行修改来匹配数据集的标注
model = dict(
    roi_head=dict(
        bbox_head=dict(num_classes=1),
        mask_head=dict(num_classes=1)))

# 修改数据集相关设置
dataset_type = 'remotesense-animal-Dataset'
classes = ('remotesense-animal',)
data = dict(
    train=dict(
        img_prefix='remotesense-animal/train/',
        classes=classes,
        ann_file='remotesense-animal/train/annotation_coco.json'),
    val=dict(
        img_prefix='remotesense-animal/val/',
        classes=classes,
        ann_file='remotesense-animal/val/annotation_coco.json'),
    test=dict(
        img_prefix='remotesense-animal/val/',
        classes=classes,
        ann_file='remotesense-animal/val/annotation_coco.json'))

# 我们可以使用预训练的 Mask R-CNN 来获取更好的性能
load_from = 'checkpoints/mask_rcnn_r50_caffe_fpn_mstrain-poly_3x_coco_bbox_mAP-0.408__segm_mAP-0.37_20200504_163245-42aa3d00.pth'
```


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
