# DBPN

```tex
@inproceedings{haris2018deep,
  title={Deep back-projection networks for super-resolution},
  author={Haris, Muhammad and Shakhnarovich, Gregory and Ukita, Norimichi},
  booktitle={Proceedings of the IEEE conference on computer vision and pattern recognition},
  pages={1664--1673},
  year={2018}
}
```

* 利用迭代的上、下采样层，为每个阶段的投影误差提供误差反馈机制。我们构建相互连接的上采样和下采样阶段，每个阶段代表不同类型的图像质量下降和高分辨率分量。
* 主要贡献：
	* 提出了一种用于SR的迭代纠错反馈机制，该机制可以计算上下投影误差，以指导重建以获得更好的结果。此处，投影误差用于表征或约束早期图层中的特征。
	* 相互连接的上采样和下采样阶段