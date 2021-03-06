# introduction

Multi-View Stereo(MVS)旨在通过具有重叠度的影像，恢复三维空间的几何结构。通常MVS流程在SfM流程之后进行，SfM为MVS提供了影像的可视图(view graph)和内外参数，以用来确定配对影像及其相对关系。深度图估计是大多数MVS方法的最主要和最具挑战性的步骤。在完成深度估计任务之后，利用融合算法[Gipuma][COLMAP][MicMac]能将局部的深度图（对于每一张参考影像）转换为全局点云。

在一些高精度Benchmark[eth3d][TsAndTs]的推动下，MVS方法在精度和内容完整性上，有了全新的突破[14][DeMoN][DeepMVS][R-MVSNet]。基于学习的算法名列前茅，但在大尺度影像的深度估计流程中，对显存提出了极高要求。

在本项目中，我们提出了一种基于3D CNN的轻量级多视立体视觉网络，在有效控制算法空间成本的同时，提高深度估计精度。


# related work

## 传统MVS

随着MVS算法的发展，传统方法陷入瓶颈，基于PatchMatch的COLMAP[]是当前(state of the art)适用于大规模并行处理的有效算法，但其在弱纹理区域表现较差；TAPA-MVS[]通过超像素分割潜在的弱纹理平面，引入了大量额外计算；虽然一些工作，将焦距、光照条件、纹理、几何结构作为先验信息引入传统算法，但要融合并综合地处理它们，任然是一项艰巨的任务。

深度学习在低级视觉问题的推理中，如光流、相机运动参数估计，逐渐展现其优异之处，特别是深度估计领域。2015年后，许多基于学习的深度估计网络提出，论文[1][2][3][4][5]从单张影像中提取信息恢复影像的深度，提取的深度信息虽然没有绝对单位意义，但证实单幅影像确实隐含着三维信息，并能通过卷积神经网络加以利用；论文[6][7][8][9][10]通过估计成对影像的光流来估计深度，许多基于学习的SLAM系统都使用了类似方法；论文[11][12][13][15][16]使用空间扫描法，将神经网络应用于多视立体视觉。

一些算法将


## 基于学习的双目立体

通过光流法（或视差）来估计深度的网络已经非常常见（DeMoN、GeoNet、DispNet、CRL）。但从双目视觉扩展到多目视觉，除建立全新的模型外，还需限制计算和时间成本的增长。

## 基于学习的MVS

在基于学习的MVS方法中，SurfaceNet建立着色体素立方体(Colored Voxel Cubes, CVC)，使用空间扫描法来估计匹配代价；MVSNet在SurfaceNet的基础上，给出可微的单应变换结构，改用学习的特征来计算代价立方体；RayNet通过MRF描述光线势，在深度估计的同时，考虑了不同源影像的可视性；DeepMVS通过深层编码解码结构，对深度进行估计；DeepTAM建立了自适应的深度搜索域，完成SLAM系统中深度的快速估计；R-MVSNet使用GRU结构，将代价立方体沿深度方向切片，迭代地推理代价立方体；

基于学习的深度估计网络，较传统算法具有更高的精度，特别是在弱纹理、非朗伯区域；得益于深度学习的良好并行特性，基于学习的网络具有更快的速度。

## 大规模MVS

无论是传统算法（如SGM、PatchMatch）还是基于学习的方法，正则化匹配代价或其概率分布，需要耗费大量的内存。随着影像尺度的增大，内存需求与影像宽度、高度、深度搜索范围成正比。对于大多数经济的GPU而言，只能处理小于512^3个体素组成的3D CNNs。

传统方法通过添加平滑项、深度传播来进行正则化。R-MVSNet使用GRU代替MVSNet中使用的3D CNN，对代价立方体进行正则化，GRU将3D CNNs转换为循环的2D结构，减少了内存消耗。DeepTAM提出了一种基于自适应深度搜索域的SLAM网络，通过迭代的方式，逐步减小深度搜索范围，从而限制代价立方体中深度维度的大小。


## 数据集



# network architecture

本章描述LiteMVSNet的详细网络结构。我们的方法在MVSNet的基础上，引入了自适应的深度搜索机制，使用三步推理结构（Boot、Iter and Refine）。我们使用DeepTAM提出的Adaptive Depth Band方法，并采用3D CNN进行正则化。

## Networks



### BootNet

两个CNN网络（特征提取2D CNN、代价正则化3D CNN）
(该部分应当包含输入、特征提取、DepthVolume的构建（逆深度）、代价正则化)


### IterNet

三个CNN网络（特征提取2D CNN、代价正则化3D CNN、深度图精化2D CNN）


### FinalNet

一个2D CNN，将4倍降采样的深度图恢复到原始大小

## Features

对所有影像，通过Encoder-Decoder结构提取特征。


## Adaptive Depth Searching Scope

Space Sweeping Method的主要问题是算法复杂度与影像尺寸、深度切片数成正比，这不仅限制了输入影像的大小，还影响了深度估计的精度；因为最终的结果精度，与深度方向切片的厚度有关。

受到DeepTAM文章的启发，我们迭代地对深度进行估计，并在每一次估计过程中自适应地调整深度搜索范围。


## Warping Volumes


### depth volume

将源影像上所有像素的深度搜索域组合，构成了深度立方体。深度立方体会在每一次深度估计后重新计算。


### flow volume


## 3D CNN for Regularization

DeepTAM提出了自适应深度搜索域，将匹配代价和影像的RGB值作为特征输入2D CNN。这种方式充分利用了相邻像素在整个深度搜索域上具有的信息，但增加了计算量且提高了建模的难度。本文在3D CNN中附加深度和影像特征，只对相邻体素的匹配代价进行正则化，在减小计算量的同时，保持了深度的连续性。

3D CNN的特征由体素的匹配代价、体素的深度信息和体素的RGB信息组成。其中，体素的深度信息包括局部深度分位点和全局深度分位点。局部分为点由当前深度搜索域计算：

(B,N,H,W)

# Experiments
