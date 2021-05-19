# End-to-End Object Detection with Adaptive Clustering Transformer

> Zheng M, Gao P, Wang X, et al. End-to-End Object Detection with Adaptive Clustering Transformer[J]. arXiv preprint arXiv:2011.09315, 2020.

## 1 Abstract

* 使用Transformer进行端到端对象检测（DETR）建议使用Transformer执行对象检测，并通过Faster-RCNN之类的两阶段对象检测实现可比的性能。但是，DETR需要算力要求高并且要求高分辨率空间输入。
* 为了**降低高分辨率输入**的计算成本，已提出了一种新型变压器，称为自适应聚类变压器（ACT）。 
	* ACT使用局部敏感哈希（LSH）自适应地对查询特征进行聚类，并使用原型键交互在查询键交互附近进行聚类。
	*  ACT可以将自我注意内的二次$O(N^2)$复杂度降低为$O(NK)$，其中K是每一层中的原型数量：
	* ACT可以替换DETR中原始的自我注意模块，而不会影响性能训练的DETR模型。 



## 2 Introduction

