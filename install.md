##  环境依赖

-  tensorflow=1.xx（这里采用conda安装，安装tensorflow-gpu=1.15.0）

    ```
    conda create -n tensorflow python=3.7
    conda activate tensorflow
    conda install tensorflow-gpu=1.15.0
    ```
    
-  查看tensorflow版本
   ```
   python
   import tensorflow as tf
   tf.__version__
   查询tensorflow安装路径为:
   tf.__path__
   ```

-  相关模块安装（全部在tensorflow环境下）
	```
	pip install joblib
	pip install easydict
	pip install rospkg
	pip install pyyaml
	pip install pillow
	```
##  数据集
-  数据集下载：
   https://www.dropbox.com/s/pnzgcitvppmwfuf/lidar_2d.tgz?dl=0 
   https://pan.baidu.com/s/1kxZxrjGHDmTt-9QRMd_kOA
   解压至：SqueezeSeg_Ros/script/data/lidar_2d

##  程序运行
-  创建ros工作空间，将此功能包拷贝至src/目录下
   ```
   catkin_make
   source devel/setup.bash
   ```
-  运行
   ```
   roslaunch squeezeseg_ros squeeze_seg_ros.launch
   ```

