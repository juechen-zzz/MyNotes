# Deep learning object detection

## 1 R-CNN

* 使用CNN（ConvNet）对 region proposals 计算 feature vectors。**从经验驱动特征（SIFT、HOG）到数据驱动特征（CNN feature map）**，提高特征对样本的表示能力。
* 采用大样本下（ILSVRC/ImageNet）有监督预训练和小样本（PASCAL）微调（fine-tuning）的方法解决小样本难以训练甚至过拟合等问题。
* 方案：

<img src="../images/image-20200731172320539.png" alt="image-20200731172320539" style="zoom: 50%;" />



## 2 OverFeat

* 基于类似CNN架构的滑动窗口检测器

## 3 MultiBox

* 优点
	* 将物体检测问题定义为输出为多个bounding box 的回归问题。每个bounding box 同时输出坐标和置信度，使得模型更加紧凑和高效
	* 利用DNN 同时学习数据的表示和bounding box 检测器
	* 在无类别监督的条件下训练box 检测器，使得该方法的计算复杂度几乎不受物体类别的影响，因此适合于大规模物体检测问题。该方法还可以推广到未知的类别。
* Deformable Part Model (DPM)

![image-20200807144227176](../images/image-20200807144227176.png)

## 4 **SPP-Net**

* Spatial Pyramid Pooling
* 解决了输入图片尺寸可以不固定的问题，在全连接层之前加上一层SPP layer
	* 1）SPP能够生成固定长度的输出，而与输入大小无关
	* 2）SPP使用多层空间分档
	* 3）由于输入比例尺的灵活性，SPP可以合并以可变比例尺提取的要素