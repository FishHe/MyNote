# 多视深度重建中的视点选择

**前言**

> 多视深度估计（MVDE）利用一族具有重叠度的影像来建立像素的深度信息。许多应用都需要高质量的深度图：**三维密集重建**、**分类与识别**、**基于影像的渲染**。本文介绍MVDE中在视点选择方面的进展。

# 多视立体视觉

> 多视深度估计只是MVS系统中的一个中间步骤，这里先介绍行业知名的MVS系统。

> **参考文献**：<br>[1] 《STATE OF THE ART IN HIGH DENSITY IMAGE MATCHING》


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

在这些MVS系统中，都包括深度图重建这一过程，这是产生密集点云所必须的。在整个密集点云产生流程中，深度图重建算法在系统中的地位至关重，它最困难且最耗时。

# 深度图重建

> **参考文献**：
> <br>[2] 《Scale Robust Multi View Stereo》
>


# 视点选择

> **参考文献**：
> <br>[2] 《PatchMatch Based Joint View Selection and Depthmap Estimation》
> <br>[3] 《Pixelwise View Selection for Unstructured Multi-View Stereo》

文章[3]的绝大部分工作基于文章[2]，先介绍[2]的主要内容。

文章[2]提出了一种像素级视点选择的方案，对于给定参考影像的每一张源影像，都给出一个

![](assets/markdown-img-paste-20181016174125167.png)
