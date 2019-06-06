# LiteMVSNet
> A Lightweight Multi-View Stereo Network with Cascade Depth Searching Scope and Recurrent Pseudo-3D CNNs

# Abstract

Various neural networks have shown that depth estimation using multi-view stereopsis can be well simulated by deep learning. The competition between running time and memory cost steps on stage. R-MVSNet, one of the state-of-the-art project, provides a gated recurrent unit (GRU) architecture to decompose 3D CNNs to 2D RNN for cost regularization, at the expense of inherent parallelism. The conflict between space and time for matching cost regularization is alliviated by our method: (1)We present an alternative network named LiteMVSNet that ourperforms R-MVSNet on the ETH3D benchmark, and requires less memory space. (2)We present a more effective depth regularization architecture with cascade depth searching scope. (3)Our networks own an efficient structure for feature extraction and cost regularization with pseudo-2D/3D CNNs. The proposed approach is compared with classic and learning-based algorithms.
