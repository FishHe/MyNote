# COLMAP

## 编译

在COLMAP根目录中，用python脚本编译：

python scripts/python/build.py --build_path "H:/ProgramDesign/COLMAP/colmap-3.5/vs2017x64/"  --colmap_path "H:/ProgramDesign/COLMAP/colmap-3.5/" --boost_path "H:/ProgramDesign/Boost/boost_1_68_0/" --qt_path "H:/ProgramDesign/Qt/5.11.2/msvc2017_64/" --cuda_path "H:/ProgramDesign/CUDA/v10.0/" --cgal_path "H:/ProgramDesign/CGAL/CGAL-4.13/vs2017x64_static" --cmake_generator "Visual Studio 15"

注意CUDA的路径，如果只用一个斜杠会出错。

在今后的编程中，都使用除号，不用斜杠。
