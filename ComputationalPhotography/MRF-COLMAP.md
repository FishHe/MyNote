# 使用MRF代替直线传播

关键问题：

1.建立视点选择的无向图模型
2.设法加速MRF求解

加速设想：
1.采用自然计算来加速MRF求解

由于《Massively Parallel Multiview Stereopsis by Surface Normal Diffusion》文章提出的新传播方式，COLMAP所提出的传播方式在并行计算框架下被放弃。

现在我将以另一个题目开始研究：

GPUMVS：附有鲁棒的像素级视点估计的大规模并行多视立体

《GPUMVS: Massively Parallel Multi-View Stereo with Rubost Pixelwise View Selection》
