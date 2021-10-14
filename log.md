@[toc]

> CUDA（Compute Unified Device Architecture），是显卡厂商NVIDIA推出的运算平台。 CUDA™是一种由NVIDIA推出的通用并行计算架构，该架构使GPU能够解决复杂的计算问题。 它包含了CUDA指令集架构（ISA）以及GPU内部的并行计算引擎。
> TensorFlow是一个强大的面向数据流的机器学习库，由谷歌的Brain Team创建，于2015年开源。它被设计成易于使用和广泛应用于数字和神经网络的问题以及其他领域。本质上，TensorFlow是一个用于处理复杂数学问题的低级工具包，它针对的是那些知道自己在做什么的研究人员，以构建实验学习体系结构，并将其转化为运行中的软件。它可以被认为是一个编程系统，在这个系统中，你将计算表示为图形。图中的节点表示数学运算，边缘表示它们之间通信的多维数据数组(张量)。
> TensorFlow是一个很好的库，可以在创建深度学习网络时对数据进行数值和图形计算，是谷歌Search、谷歌Translate Translate、谷歌Photos等应用最广泛的库

# NVIDIA Driver + CUDA 10.0 + cudnn 7.6.5 + tensorflow2.0(Ubuntu 16.04)

## 1.0 安装NVIDIA Driver
 - 下载驱动
    - https://www.nvidia.cn/geforce/drivers
    - 下载NVIDIA-Linux-x86_64-440.100.run文件，大小为144MB
 - 安装
   ```
   1.禁用nouveau
   sudo gedit /etc/modprobe.d/blacklist.conf
   # 打开文件，在最后添加如下两行
   blacklist nouveau
   options nouveau modeset=0
   
   2.更新系统修改
   sudo update-initramfs -u
   重启电脑
   
   3.验证nouveau已禁用
   lsmod | grep nouveau
   无输出则表示已禁用
   
   4.按ctrl+alt+f1进入命令行界面，此时需要login（电脑账户名称），password（密码）
   
   5.关闭图形界面
   sudo service lightdm stop
   
   6.卸载系统中存在的驱动
   sudo apt-get remove nvidia-*
   
   7.运行.run文件
   sudo chmod  a+x NVIDIA-Linux-x86_64-440.100.run
   sudo ./NVIDIA-Linux-x86_64-440.100.run -no-x-check -no-nouveau-check -no-opengl-files
   在弹出的提示窗口中均保持默认选择
   
   8.重启图形界面
   sudo service lightdm start
   ```
 - 验证安装成功
   ```
   nvidia-smi
   ```

## 2.0 安装cuda 10.0
 - 安装前提
    - 已安装显卡驱动
 - 下载驱动
    - https://developer.nvidia.com/cuda-toolkit-archive
    - 下载cuda_10.0.130_410.48_linux.run文件[CUDA Toolkit 10.0 Archive -> Linux -> x86_64 -> Ubuntu -> 16.04 -> runfile(local)]，大小为2.0GB
   ```
   sudo sh cuda_10.0.130_410.48_linux.run
   ```
 - 如果`/tmp`显示空间不足，则执行
   ```
   sudo mkdir /opt/tmp
   sudo sh cuda_10.0.130_410.48_linux.run --tmpdir=/opt/tmp/
   ```
 - 安装
   ```
   # Do you accept the previously read EULA?
   # accept/decline/quit: accept
 
   # Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 361.62?
   # (y)es/(n)o/(q)uit: n
 
   # Install the CUDA 10.0 Toolkit?
   # (y)es/(n)o/(q)uit: y
 
   # Enter Toolkit Location
   # [ default is /usr/local/cuda-10.0 ]:
 
   # Do you want to install a symbolic link at /usr/local/cuda?
   # (y)es/(n)o/(q)uit: y
 
   # Install the CUDA 10.0 Samples?
   # (y)es/(n)o/(q)uit: y
 
   # Enter CUDA Samples Location
   # [ default is /home/lishangjie ]:
   ```
 - 安装结束后，在`~/.bashrc`最后添加
   ```
   export PATH=/usr/local/cuda-10.0/bin:$PATH
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
   ```
 - 验证安装成功
   ```
   cd /usr/local/cuda-10.0/samples/1_Utilities/deviceQuery
   sudo make
   ./deviceQuery
   # 不报错即证明安装成功
   ```
 - 查看cuda版本（CUDA Version 10.0.130）
   ```
   cat /usr/local/cuda-10.0/version.txt
   ```
 - 多版本cuda切换
   ```
   删除之前的cuda软链接，并根据版本需要重新创建
   sudo rm -rf /usr/local/cuda
   sudo ln -s /usr/local/cuda-10.0 /usr/local/cuda
   在~/.bashrc中使用cuda软链接（无需指明版本号）
   ```
   
   
## 3.0 安装cudnn 7.6.5
 - 安装前提
    - 已安装cuda
 - 下载驱动（首次登录需注册并填问卷）
    - https://developer.nvidia.com/rdp/cudnn-archive
    - 下载cudnn-10.0-linux-x64-v7.6.5.32.tgz压缩包[Download cuDNN v7.6.5 (November 5th, 2019), for CUDA 10.0 -> cuDNN Library for Linux]，大小为486MB
 - 解压后生成cuda文件夹
   ```
   sudo cp cuda/include/cudnn.h /usr/local/cuda/include
   sudo cp cuda/lib64/*.* /usr/local/cuda/lib64
   sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
   ```
 - 查看cudnn版本（CUDNN_VERSION 7.6.5）
   ```
   cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
   ```

## 4.0 切换网络源
 - 设置软件和更新
    - 在软件和更新中，选择阿里的服务器（http://mirrors.aliyun.com/ubuntu）
 - 若重新载入时出错，修改`/etc/apt/sources.list`

   ```
   # 删除文档原内容，并添加
   deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe 
   deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe
   deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe
   deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe
   deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe
   ```
   
## 5.0 安装tensorflow2.0
> 对应以上版本CUDA10.0，cudnn7.6.5,安装tensorflow2.0.0版本
* 1.查看系统安装的python版本
   ```
   打开终端输入指令：python，默认安装的python版本为2.7.12
   ```
* 2.安装python对应版本的pip和依赖包
   ```
   若python版本为2.7，则输入如下命令：
   sudo apt-get install python-pip python-dev
   若python版本为3.x，则输入如下命令：
   sudo apt-get install python3-pip python3-dev
   ```
* 3.升级pip版本
    ```
    python 2.7版本：sudo pip install --upgrade pip
    python 3.x版本：sudo pip3 install --upgrade pip
    ```
 * 4.安装tensorflow
 -  重新安装pip，保证以下操作时不会出错
    ```
    curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py  #下载get-pip
    sudo python get-pip.py  #安装pip
    ```
 -  镜像源安装tensorflow，保证速度
    ```
    sudo python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow-gpu==2.0.0
    ```
 -  报错：Cannot uninstall ‘enum34’
    ```
    sudo pip install --ignore-installed enum34
    sudo python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow-gpu==2.0.0
    ```
 -  报错：Cannot uninstall 'pyasn1-modules'
    ```
    sudo pip install --ignore-installed pyasn1-modules
    sudo python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow-gpu==2.0.0
    ```
 -  报错：launchpadlib 1.10.2 requires testresources, which is not installed.
    ```
    sudo pip install launchpadlib
    sudo python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow-gpu==2.0.0
    安装成功，但是import tensorflow一直报错
    ```
 -  卸载
    ```
    查看通过pip安装的模块
    sudo pip list
    卸载
    sudo pip uninstall tensorflow-gpu
    ```
### 5.0.1
 -  以上全部没有成功，改用conda安装tensorflow
    ```
    conda create -n tensorflowgpu python=3.7
    conda activate tensorflowgpu
    conda install tensorflow-gpu
    ```
    **成功**
## 6.0 查看各个软件版本

 - 查看显卡驱动
   ```
   nvidia-smi
   ```
 - 查看cuda版本
   ```
   cat /usr/local/cuda/version.txt
   nvcc -V
   ```
 - 查看cudnn版本
   ```
   cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
   ```
