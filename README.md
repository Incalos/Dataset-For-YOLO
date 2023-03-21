# YOLO Datasets And Training Methods

本项目主要涉及YOLO算法的数据集制作以及简要的训练方法，并且提供了YOLO算法官方代码以及对应的目标检测预训练权重，详细信息可进入对应的文件夹中查看。

## 一、数据集制作

### 1. 数据采集

* 如果使用 **Intel RealSense**
  深度相机采集图像制作数据集，详细情况可以查看 [Images-Acquisition-With-RealSense](https://github.com/Incalos/Images-Acquisition-With-RealSense)
  。

### 2. 数据标注

* 安装 **labelimg** 标注工具
  ```shell
  pip install labelimg -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```

* 启动 **labelimg** 进行标注

    * 在命令行窗口输入 `labelimg`，标注界面如下所示。

  ![](assets/labelimg.png)

    * 在 **View** 菜单内找到 **Auto Save mode**，将标注过程设置成 **自动保存模式**。

  ![](assets/1.png)

    * 一般推荐数据集保存格式设置成 **PascalVOC** 格式。

  ![](assets/2.png)

    * YOLO算法的标注文件夹设置成如下结构。其中 **images** 文件夹为待标注的图片文件夹，**Annotations** 为标注完成后标签的保存位置。
  ```
  YoloDataSets 
   |——————images
   |        └——————1.jpg
   |        └——————2.jpg  
   |        └——————3.jpg
   |        └——————...
   └——————Annotations
   |        └——————1.xml
   |        └——————2.xml  
   |        └——————3.xml
   |        └——————...
  ```

    * 点击 **Open Dir** 选择 **images** 文件夹。

    * 点击 **Change Save Dir** 选择 **Annotations** 文件夹。

    * 开始进行标图。

  ![](assets/video.gif)

### 3. 转换成YOLO数据集

* 将待转化的已标注数据按以下结构保存。

  ```
  YoloDataSets 
   |——————images
   |        └——————1.jpg
   |        └——————2.jpg  
   |        └——————3.jpg
   |        └——————...
   └——————Annotations
   |        └——————1.xml
   |        └——————2.xml  
   |        └——————3.xml
   |        └——————...
  ```

* 运行 **DataSet.py** 程序。

  ```
  python DataSet.py --yoloversion yolov5 --trainval_percent 0.9 --train_percent 0.9 --mainpath YoloDataSets --classes ['dog','man']
                                  yolov6                    ···                ···             ····                   ['','',···]
                                  yolov7                    ···                ···             ····                   ['','',···]
                                  yolov8                    ···                ···             ····                   ['','',···]
  ```

  **yoloversion** : 表示 YOLO 的版本，可选择 YOLOv5、YOLOv6、YOLOv7、YOLOv8

  **trainval_percent** : 训练集和验证集的总占比，1-trainval_percent 为测试集占比

  **train_percent** : 训练集在训练集和验证集中的占比

  **mainpath** : YoloDataSets 的路径，当然数据集名 YoloDataSets 也可以换成其他的

  **classes** : 标签的类别，请按照示例的格式填写

  ```python
  import argparse
   
  def parse_opt():
    parser = argparse.ArgumentParser()
    parser.add_argument("--yoloversion", type=str, default='', help="optional: yolov5\yolov6\yolov7\yolov8")
    parser.add_argument("--trainval_percent", type=float, default=0.9, help="percentage of training set and validation set")
    parser.add_argument("--train_percent", type=float, default=0.9, help="percentage of training set")
    parser.add_argument("--mainpath", type=str, default=720, help="the path of the dataset")
    parser.add_argument("--classes", type=str, default='[]', help="Categories of annotations")
    opt = parser.parse_args()
    return opt
  ```

## 二、训练YOLO算法

### 1. YOLOv5训练方法

* 进入 **yolov5** 文件夹。

  ```
  cd yolov5
  ```

* 将 **YoloDataSets** 数据集放在 **yolov5** 文件夹中。

* 在 **YoloDataSets** 目录下添加 **yaml** 配置文件 **data.yaml** ，内容格式如下。

  ```
  path : YoloDataSets
  train: train.txt
  val: val.txt
  test: test.txt
  
  # number of classes
  nc: 2
  
  # class names
  names: ['dog','man']
  ```

* 在命令行窗口运行如下命令，参数可换。

  ```
  python train.py --data YoloDataSets/data.yaml --epochs 300 --weights yolov5n.pt --cfg model/yolov5n.yaml  --batch-size 128
                                                                       yolov5s.pt       model/yolov5s.yaml               64
                                                                       yolov5m.pt       model/yolov5m.yaml               40
                                                                       yolov5l.pt       model/yolov5l.yaml               24
                                                                       yolov5x.pt       model/yolov5x.yaml               16
  ```

* 目标检测预训练权重。

| Model                                                                                           | size<br><sup>(pixels) | mAP<sup>val<br>50-95 | mAP<sup>val<br>50 | Speed<br><sup>CPU b1<br>(ms) | Speed<br><sup>V100 b1<br>(ms) | Speed<br><sup>V100 b32<br>(ms) | params<br><sup>(M) | FLOPs<br><sup>@640 (B) |
| ----------------------------------------------------------------------------------------------- | --------------------- | -------------------- | ----------------- | ---------------------------- | ----------------------------- | ------------------------------ | ------------------ | ---------------------- |
| [YOLOv5n](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5n.pt)              | 640                   | 28.0                 | 45.7              | **45**                       | **6.3**                       | **0.6**                        | **1.9**            | **4.5**                |
| [YOLOv5s](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5s.pt)              | 640                   | 37.4                 | 56.8              | 98                           | 6.4                           | 0.9                            | 7.2                | 16.5                   |
| [YOLOv5m](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5m.pt)              | 640                   | 45.4                 | 64.1              | 224                          | 8.2                           | 1.7                            | 21.2               | 49.0                   |
| [YOLOv5l](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5l.pt)              | 640                   | 49.0                 | 67.3              | 430                          | 10.1                          | 2.7                            | 46.5               | 109.1                  |
| [YOLOv5x](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5x.pt)              | 640                   | 50.7                 | 68.9              | 766                          | 12.1                          | 4.8                            | 86.7               | 205.7                  |
|                                                                                                 |                       |                      |                   |                              |                               |                                |                    |                        |
| [YOLOv5n6](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5n6.pt)            | 1280                  | 36.0                 | 54.4              | 153                          | 8.1                           | 2.1                            | 3.2                | 4.6                    |
| [YOLOv5s6](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5s6.pt)            | 1280                  | 44.8                 | 63.7              | 385                          | 8.2                           | 3.6                            | 12.6               | 16.8                   |
| [YOLOv5m6](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5m6.pt)            | 1280                  | 51.3                 | 69.3              | 887                          | 11.1                          | 6.8                            | 35.7               | 50.0                   |
| [YOLOv5l6](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5l6.pt)            | 1280                  | 53.7                 | 71.3              | 1784                         | 15.8                          | 10.5                           | 76.8               | 111.4                  |
| [YOLOv5x6](https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5x6.pt)<br>+ [TTA] | 1280<br>1536          | 55.0<br>**55.8**     | 72.7<br>**72.7**  | 3136<br>-                    | 26.2<br>-                     | 19.4<br>-                      | 140.7<br>-         | 209.8<br>-             |


### 2. YOLOv6训练方法

* 进入 **yolov6** 文件夹。

  ```
  cd yolov6
  ```

* 将 **YoloDataSets** 数据集放在 **yolov6** 文件夹中。

* 在 **YoloDataSets** 目录下添加 **yaml** 配置文件 **data.yaml** ，内容格式如下。

  ```
  train: YoloDataSets/images/train       # train images
  val: YoloDataSets/images/val           # val images
  test: YoloDataSets/images/test
  is_coco: False
  
  # number of classes
  nc: 2
  
  # class names
  names: ['dog','man']
  ```

* 在命令行窗口运行如下命令，参数可换。

  ```
  python tools/train.py --batch 64 --conf configs/yolov6s6_finetune.py --data YoloDataSets/data.yaml --epochs 300  --device 0                 
  ```

* 目标检测预训练权重。

| Model                                                        | Size | mAP<sup>val<br/>0.5:0.95 | Speed<sup>T4<br/>trt fp16 b1 <br/>(fps) | Speed<sup>T4<br/>trt fp16 b32 <br/>(fps) | Params<br/><sup> (M) | FLOPs<br/><sup> (G) |
| :----------------------------------------------------------- | ---- | :----------------------- | --------------------------------------- | ---------------------------------------- | -------------------- | ------------------- |
| [**YOLOv6-N**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6n.pt) | 640  | 37.5                     | 779                                     | 1187                                     | 4.7                  | 11.4                |
| [**YOLOv6-S**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6s.pt) | 640  | 45.0                     | 339                                     | 484                                      | 18.5                 | 45.3                |
| [**YOLOv6-M**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6m.pt) | 640  | 50.0                     | 175                                     | 226                                      | 34.9                 | 85.8                |
| [**YOLOv6-L**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6l.pt) | 640  | 52.8                     | 98                                      | 116                                      | 59.6                 | 150.7               |
|                              |                               |                                |                    |                        |
| [**YOLOv6-N6**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6n6.pt) | 1280 | 44.9                     | 228                                     | 281                                      | 10.4                 | 49.8                |
| [**YOLOv6-S6**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6s6.pt) | 1280 | 50.3                     | 98                                      | 108                                      | 41.4                 | 198.0               |
| [**YOLOv6-M6**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6m6.pt) | 1280 | 55.2                     | 47                                      | 55                                       | 79.6                 | 379.5               |
| [**YOLOv6-L6**](https://github.com/meituan/YOLOv6/releases/download/0.3.0/yolov6l6.pt) | 1280 | 57.2                     | 26                                      | 29                                       | 140.4                | 673.4               |

### 3. YOLOv7训练方法

* 进入 **yolov7** 文件夹。

  ```
  cd yolov7
  ```

* 将 **YoloDataSets** 数据集放在 **yolov7** 文件夹中。

* 在 **YoloDataSets** 目录下添加 **yaml** 配置文件 **data.yaml** ，内容格式如下。

  ```
  train: YoloDataSets/train.txt
  val: YoloDataSets/val.txt
  test: YoloDataSets/test.txt
  
  # number of classes
  nc: 2
  
  # class names
  names: ['dog','man']
  ```

* 在命令行窗口运行如下命令，参数可换。

  ```
  # finetune p5 models
  python train.py --workers 8 --device 0 --batch-size 32 --data YoloDataSets/data.yaml --img 640 640 --cfg cfg/training/yolov7-custom.yaml --weights 'yolov7_training.pt' --name yolov7-custom --hyp data/hyp.scratch.custom.yaml
  
  # finetune p6 models
  python train_aux.py --workers 8 --device 0 --batch-size 16 --data YoloDataSets/data.yaml --img 1280 1280 --cfg cfg/training/yolov7-w6-custom.yaml --weights 'yolov7-w6_training.pt' --name yolov7-w6-custom --hyp data/hyp.scratch.custom.yaml                
  ```

* 目标检测预训练权重。

| Model | Test Size | AP<sup>test</sup> | AP<sub>50</sub><sup>test</sup> | AP<sub>75</sub><sup>test</sup> | batch 1 fps | batch 32 average time  |
| :----: | :----: | :----: | :----: | :----: | :----: |:----------------------:|
| [**YOLOv7**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7.pt) | 640 | **51.4%** | **69.7%** | **55.9%** | 161 *fps* |        2.8 *ms*        |
| [**YOLOv7-X**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7x.pt) | 640 | **53.1%** | **71.2%** | **57.8%** | 114 *fps* |        4.3 *ms*        |
|  |  |  |  |  |  |                        |
| [**YOLOv7-W6**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-w6.pt) | 1280 | **54.9%** | **72.6%** | **60.1%** | 84 *fps* |        7.6 *ms*        |
| [**YOLOv7-E6**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-e6.pt) | 1280 | **56.0%** | **73.5%** | **61.2%** | 56 *fps* |       12.3 *ms*        |
| [**YOLOv7-D6**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-d6.pt) | 1280 | **56.6%** | **74.0%** | **61.8%** | 44 *fps* |       15.0 *ms*        |
| [**YOLOv7-E6E**](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-e6e.pt) | 1280 | **56.8%** | **74.4%** | **62.1%** | 36 *fps* |       18.7 *ms*        |



### 4. YOLOv8训练方法

* 进入 **yolov8** 文件夹。

  ```
  cd yolov8
  ```

* 将 **YoloDataSets** 数据集放在 **yolov8** 文件夹中。

* 在 **YoloDataSets** 目录下添加 **yaml** 配置文件 **data.yaml** ，内容格式如下。

  ```
  path : ../YoloDataSets
  train: train.txt
  val: val.txt
  test: test.txt
  
  # number of classes
  nc: 2
  
  # class names
  names: ['dog','man']
  ```

* 在命令行窗口运行如下命令，参数可换。

  ```
  yolo task=detect mode=train model=yolov8n.yaml data=YoloDataSets/data.yaml batch=28 epochs=300 imgsz=640 workers=32 device=0
                                    yolov8s.yaml
                                    yolov8m.yaml
                                    yolov8l.yaml
                                    yolov8x.yaml
  ```

* 目标检测预训练权重。

| 模型                                                                                   | 尺寸<br><sup>（像素） | mAP<sup>val<br>50-95 | 推理速度<br><sup>CPU ONNX<br>(ms) | 推理速度<br><sup>A100 TensorRT<br>(ms) | 参数量<br><sup>(M) | FLOPs<br><sup>(B) |
| ------------------------------------------------------------------------------------ | --------------- | -------------------- | ----------------------------- | ---------------------------------- | --------------- | ----------------- |
| [YOLOv8n](https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8n.pt) | 640             | 37.3                 | 80.4                          | 0.99                               | 3.2             | 8.7               |
| [YOLOv8s](https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8s.pt) | 640             | 44.9                 | 128.4                         | 1.20                               | 11.2            | 28.6              |
| [YOLOv8m](https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8m.pt) | 640             | 50.2                 | 234.7                         | 1.83                               | 25.9            | 78.9              |
| [YOLOv8l](https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8l.pt) | 640             | 52.9                 | 375.2                         | 2.39                               | 43.7            | 165.2             |
| [YOLOv8x](https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8x.pt) | 640             | 53.9                 | 479.1                         | 3.53                               | 68.2            | 257.8             |





