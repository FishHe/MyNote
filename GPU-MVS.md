# GPU加速的MVS

> 选取了两个生命力较强的开源MVS系统，COLMAP、MicMac；这两个系统有完整的运行框架，资料充足，可供编程学习参考。</br>
> MicMac https://github.com/micmacIGN/micmac </br>
> COLMAP https://github.com/colmap/colmap

## MicMac
* MicMac是一套MVS命令行工具集，其UI模块尚未完善。
* MicMacd仅在密集匹配阶段使用GPU加速。
* 图片名称不能出现空格
* 对给定的数据集，MicMac定向失败
* MicMac只提供All（穷举匹配）、MulScale（降分辨率预匹配、高分辨率匹配）、Line（序列匹配）、File（自定义方案）

## COLMAP
* COLMAP是一套具有UI的完整MVS系统。
* COLMAP全过程都可以GPU加速，但GPU加速仅在密集匹配阶段有突出表现。
* COLMAP在低端显卡上有Bug，不能进行密集匹配。
* COLMAP的UI提供的命令，只使用一张显卡。
