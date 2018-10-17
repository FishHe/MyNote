# 多视深度估计

**概念**

> **多视深度估计（MVDE）**
利用一族具有重叠度的影像来建立像素的深度信息。许多应用都需要高质量的深度图：**三维密集重建**、**分类与识别**、**基于影像的渲染**。本文介绍MVDE中在视点选择方面的进展。

> **MVS(Multi View Stereo)** 多视立体，主要指从影像恢复立体模型的方法，包括**影像选择**、**深度图重建**、**点云生成**和**格网生成**。

> **MVG(Multi View Geomery)** 多视几何学，主要论述多视影像、像素（二维）、体素（三维）间的**几何关系**。

> **SfM(Structure from Motion)** 从运动到结构，主要指从影像恢复**位姿**的方法。

> **参考影像(Reference Image)** 用来创建深度图的影像，它的视点就是深度图的视点。

> **源影像(Source Image)** 也称次级影像（Secondary Image），一张参考影像至少需要一张匹配的源影像来完成深度估计。

## 多视立体与多视几何

> 多视深度估计只是MVS系统中的一个中间步骤，这里先介绍行业知名的MVS系统。

> **参考文献**：
> <br>[1] 《State of the Art in High Density Image Matching》
> <br>[2] 《Multi View Geomery in Computer Vison》 **MVG推荐教程**
> <br>[3] 《Multi-View Stereo: A Tutorial》 **MVS推荐教程**

在MVS系统中，通常先估计所有视点的深度图，然后将深度图融合成点云，一些知名的开源MVS系统如下表：

| Project |  Language | License |
| ---  | --- | --- |
[GPUIma + fusibile](https://github.com/kysucix) | C++ CUDA | GNU General Public License - contamination|
[HPMVS](https://github.com/alexlocher/hpmvs) | C++ | GNU General Public License - contamination|
|[MICMAC](http://logiciels.ign.fr/?Micmac) | C++ | CeCILL-B |
[MVE](https://github.com/simonfuhrmann/mve) | C++ | BSD 3-Clause license + parts under the GPL 3 license|
[OpenMVS](https://github.com/cdcseacave/openMVS/) | C++  (CUDA optional) | AGPL3|
[PMVS](https://github.com/pmoulon/CMVS-PMVS) | C++ CUDA | GNU General Public License - contamination|
[SMVS Shading-aware Multi-view Stereo](https://github.com/flanggut/smvs) | C++ | BSD-3-Clause license |

一些代表性软件：

|项目|版权|精度|速度|
| --------- | -------- | ------------------------------ | ------------------------ |
| SURE      | 闭源免费 | &star;&star;                   | &star;&star;&star;&star; |
| MicMac    | 开源免费 | &star;&star;&star;&star;&star; | &star;&star;             |
| PMVS      | 开源免费 | &star;                         | &star;                   |
| PhotoScan | 商业收费 | &star;&star;&star;&star;       | &star;&star;&star;&star; |
| Smart3D | 商业收费 | &star;&star;&star;       | &star;&star;&star;&star;&star; |

应当特别说明的是：

* **MICMAC**是一套内容丰富的工具集，可操作性强，但建立较好的模型，需要较强的专业知识和一定的软件使用技巧。它的密集匹配过程利用了GPU进行加速，但实测时，加速效果不明显。
* **PhotoScan**作为商业软件，稳定性强，操作简单，产生的点云丰富，速度快。

![](assets/markdown-img-paste-20181016171808123.png)

![](assets/markdown-img-paste-20181016171832788.png)

在这些MVS系统中，都包括深度图重建这一过程，这是产生密集点云所必须的。在整个密集点云产生流程中，深度图重建算法在系统中的地位至关重，它最困难且最耗时。MVS系统需要为深度图重建提供一些必要的前处理流程，在进行深度重建以前，应有以下数据：

* 去畸变的影像
* 每张影像的姿态
* 相机的绝对位置或相对位置
* 焦距或内参数矩阵

这些数据均可由SfM过程产生，SfM所运用的MVG可参考文献[2]，这里总结了常用的SfM库，（较完整的MVS库也包含了SfM模块）：

| Project |  Language | License |
| ---  | --- | --- |
|[Bundler](https://github.com/snavely/bundler_sfm) | C++ | GNU General Public License - contamination|
|[Colmap](https://github.com/colmap/colmap) | C++ | GNU General Public License - contamination|
|[MAP-Tk](https://github.com/Kitware/maptk) | C++ | BSD 3-Clause license - Permissive |
|[MicMac](https://github.com/micmacIGN) | C++ | CeCILL-B |
|[MVE](https://github.com/simonfuhrmann/mve) | C++ | BSD 3-Clause license + parts under the GPL 3 license|
|[OpenMVG](https://github.com/openMVG/openMVG) | C++ |  MPL2 - Permissive|
|[OpenSfM](https://github.com/mapillary/OpenSfM/) |  Python | Simplified BSD license - Permissive|
|[TheiaSfM](https://github.com/sweeneychris/TheiaSfM) | C++ |   New BSD license - Permissive|


## 深度图重建

> **参考文献**：
> <br>[4] 《Scale Robust Multi View Stereo》

> [5] 《Using Multiple Hypotheses to Improve Depth-Maps for Multi-View Stereo》
> <br>[6] 《Real-time Plane-sweeping Stereo with Multiple Sweeping Directions》
> <br>[7] 《Multi-View Stereo: Redundancy Benefits for 3D Reconstruction》

> [8] 《Accurate, Dense, and Robust Multi-View Stereopsis》
> <br>[9] 《Progressive Prioritized Multi-view Stereo》
> <br>[10] 《Multi-View Stereo for Community Photo Collections》

> [11] 《Accurate and Efficient Stereo Processing by Semi-Global Matching and Mutual Information》
> <br>[12] 《Stereo Vision in Structured Environments by Consistent Semi-Global Matching》
> <br>[13] 《Stereo Processing by Semiglobal Matching and Mutual Information》

> [14] 《PatchMatch: A Randomized Correspondence Algorithm for Structural Image Editing》
> <br>[15] 《PatchMatch Stereo - Stereo Matching with Slanted Support Windows》

![](assets/markdown-img-paste-20181017111604492.png)

深度图是一种简单的表示3D曲面的方法，它的数据结构是2D影像而不是3D曲面。为了创建一张深度图，需要一张**参考影像**以及至少一张**源影像**，在他们之间形成配对，并通过三角测量来获得像素的深度。当前，有两大类深度图重建的方法：

* **逐像素** 为每一个像素都计算其深度值（文献[5-7][11-13]）。
* **区域生长** 从一些已知深度的特征点向其领域扩散（文献[4][8-10][15]）。

其中，文献[8]是PMVS的论文，文献[11-13]是SGM的论文。这里详细介绍文献[4]所用的深度图重建算法，包括下面几个步骤：

* **深度图初始化** 计算特征点的深度。
* **深度传播** 将特征点的深度扩散到它的领域。
* **随机浮动** 深度图随机改变一些值，如果测量误差值减小，新值会被保留。
* **法线计算** 利用深度图计算法线，法线能将影像块从**参考影像**变换到**源影像**，从而优化**深度传播**。
* **深度图优化** 利用梯度下降法，搜索每一像素的最优深度。
* **深度图精化** 利用双边滤波器对深度图进行平滑，设置每个深度值的最低支持数来剔除粗差。

![](assets/markdown-img-paste-20181017114127341.png)

文献[3]所使用的算法是基于块匹配的（块匹配算法参考文献[14]，快匹配的立体视觉算法参考文献[15]），并且适用于GPU。要了解更多文献[3]的内容，阅读我的笔记：

> 


## 视点选择

> **参考文献**：
> <br>[2] 《PatchMatch Based Joint View Selection and Depthmap Estimation》
> <br>[3] 《Pixelwise View Selection for Unstructured Multi-View Stereo》

文章[3]的绝大部分工作基于文章[2]，先介绍[2]的主要内容。

文章[2]提出了一种像素级视点选择的方案，对于给定参考影像的每一张源影像，都给出一个

![](assets/markdown-img-paste-20181016174125167.png)
