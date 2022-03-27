# Recognition-RemoteSensingAnimal
这里是遥感影像牛羊马识别。

## 概述
- 根据数据类型和格式（采用 *lableIme* 标注的语义分割格式），采用 **语义分割** 方法尝试；
- 经分析，原标注中没有 *背景 background* 语义标签，改用 **实例分割** 方法尝试； 
- 由于数据量较少，采用**预训练**和**数据增强**是非常必须的手段。

## Usage
- `json2xml.py` 用于 `.json` 和 `.xml` 格式转化；
- `labelIme2coco.py` 用于 **lableIme** 标注生成的 `.json` 文件和 **coco 格式** 转化（语义分割）；

## 计划
- 根据输入数据类型，先将 ``.json`` 文件转化为语义分割的标签 Mask 形式文件 *（已完成：将 lableIme 格式转化为 coco 格式）* ；
- 采用 **mmdet** 框架，调用官方 **实例分割** 代码进行尝试。
