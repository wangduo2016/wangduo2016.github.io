---
title: Ubuntu 16.04 + CUDA 8.0 + cuDNN v5.1 + TensorFlow(GPU support)安装配置详解
date: 2017-08-17 10:40:42
author: wangduo
cover: http://ovu6dymqj.bkt.clouddn.com/20170817-cover1-VCG21gic13789852.jpg
tags: [TensorFlow, GPU support, Ubuntu 16.04, CUDA 8.0, cuDNN v5.1]
categories: Deep learning
---

随着图像识别和深度学习领域的迅猛发展，GPU时代即将来临。由于GPU处理深度学习算法的高效性，使得配置一台搭载有GPU的服务器变得尤为必要。
本文主要介绍在Ubuntu 16.04环境下如何配置TensorFlow(GPU support)框架，实验所用的显卡为GeForce GTX 1080ti(OC)，显存11G，频率1569-1708MHz，CUDA核心3584个，Compute Capability为6.1。下面详细介绍安装配置的各个步骤。

关于本人实验室所用硬件的配置清单，请访问[深度学习硬件购买指南](http://www.cnblogs.com/wangduo/p/7383860.html)。

### 1.安装Ubuntu 16.04
本文中，我们将安装Win 10 pro+Ubuntu 16.04 LTS双系统。系统安装的教程网上铺天盖地，在此不再详述。给出两个教程链接供大家参考：
http://jingyan.baidu.com/article/d5c4b52bc0ae1fda560dc5ac.html
http://jingyan.baidu.com/article/dca1fa6fa3b905f1a44052bd.html
建议使用Universal USB Installer，简单小巧易用。本实验的Ubuntu 16.04的分区大小及设置如下（仅供参考）：
```
boot 主分区 Ext4 10240MB
/ 逻辑分区 Ext4 20480MB
/home 逻辑分区 Ext4 20480MB
swap 逻辑分区 10240MB
```


安装完成后，登录Ubuntu 16.04系统，若进入grub rescue模式，请参考如下方法修复grub：
http://yhz61010.iteye.com/blog/2302418
修复完grub后，若无法开机或开机黑屏，请参考如下方法解决：
http://blog.csdn.net/Good_Day_Day/article/details/74352534

#### 1.1安装NVIDIA驱动程序
NVIDIA 驱动程序下载链接：
http://www.nvidia.cn/Download/index.aspx
##### 1.1.1 For Win 10 pro：
点击上面链接进入下载页面，选择合适版本下载并安装，或直接安装驱动精灵更新系统驱动。安装过程中若出现兼容性问题，请按win+i进入设置中更新系统。
驱动安装完成后，可以在Windows系统中检测硬件性能，检测软件有：AS SSD Benchmark; cpu-z; techpowerup gpu-z; 3d mark; Furamrk等。

##### 1.1.2 For Ubuntu 16.04：
点击上面链接进入下载页面，选择合适版本下载。安装方法请参考如下两个链接：
http://blog.csdn.net/tianrolin/article/details/52830422
http://blog.csdn.net/u012581999/article/details/52433609

### 2.Requirements to run TensorFlow with GPU support
#### 2.1安装CUDA 8.0
下载CUDA 8.0：
https://developer.nvidia.com/cuda-downloads
Installing from a deb File:
```
# 1.Install kernel headers and development packages for the currently running kernel
$ sudo apt-get install linux-headers-$(uname -r)
# 2.Install repository meta-data
$ sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb
# 3.Update the Apt repository cache
$ sudo apt-get update
# 4.Install CUDA
$ sudo apt-get install cuda
```

测试CUDA 8.0是否安装成功，请参考CUDA 8.0官方教程：
http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#recommended-post
请在*2.3配置环境变量*后进行测试。

##### 2.1.1 gcc版本降级
Ubuntu 16.04的gcc编译器是5.4.0，然而CUDA 8.0不支持5.0以上的编译器，因此需要降级，把编译器版本降到4.9。命令如下：
```
sudo apt-get install g++-4.9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 20
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 10
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 20
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 10
sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 30
sudo update-alternatives --set cc /usr/bin/gcc
sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 30
sudo update-alternatives --set c++ /usr/bin/g++
```

顺便科普一下如何查看Ubuntu 16.04的Kernel，GCC和GLIBC的版本信息。
```
# Kernel: 
$ uname -sr #或
$ uname -r
# GCC
$ gcc -v #或
$ gcc --version
# GLIBC
$ ldd --version
```

#### 2.2.安装cuDNN v5.1
下载cuDNN v5.1（需要注册并填写一个简单的问卷调查）：
https://developer.nvidia.com/cudnn
Installing from a Tar File:
```
# 1.Navigate to your <installpath> directory containing cuDNN.
# 2.Unzip the cuDNN package.
$ tar -xzvf cudnn-9.0-linux-x64-v7.tgz
# 3.Copy the following files into the CUDA Toolkit directory.
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
# 4.更新软连接
$ cd /usr/local/cuda/lib64/
$ sudo rm -rf libcudnn.so libcudnn.so.5 #删除原有动态文件
$ sudo ln -s libcudnn.so.5.1.5 libcudnn.so.5 #生成软衔接
$ sudo ln -s libcudnn.so.5 libcudnn.so #生成软链接
```

#### 2.3 配置环境变量
在终端输入： 
`$ sudo gedit ~/.bashrc #打开.bashrc文件`
在此文件末尾加入如下环境变量设置语句：
```
export CUDA_HOME=/usr/local/cuda-8.0
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda-8.0/lib64:/usr/local/cuda-8.0/extras/CUPTI/lib64:~/cuDNN_installpath"
```
使设置立即生效，在终端执行：
`$ source ~/.bashrc`
或者重启电脑即可。

#### 2.4 Install Bazel
If bazel is not installed on your system, install it now by following these directions.
```
# 1. Install JDK 8
$ sudo apt-get install openjdk-8-jdk
# 2. Add Bazel distribution URI as a package source (one time setup)
$ echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
$ curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
# 3.Install and update Bazel
$ sudo apt-get update && sudo apt-get install bazel
# Once installed, you can upgrade to a newer version of Bazel with:
$ sudo apt-get upgrade bazel
```

参考链接：
https://bazel.build/versions/master/docs/install.html
若需要安装curl，请按提示安装：
`$ sudo apt-get install curl`

#### 2.5 Install TensorFlow Python dependencies and libcupti-dev
To install these packages for Python 3.n, issue the following command:
`$ sudo apt-get install python3-numpy python3-dev python3-pip python3-wheel`
You must also install libcupti-dev by invoking the following command:
`$ sudo apt-get install libcupti-dev`

Ubuntu 16.04的默认python版本是2.\*，关于如何将python从2.\*升级到3.\*，请自行查资料解决。

### 3.安装TensorFlow(GPU support)
#### 3.1 Clone the TensorFlow repository
To clone the latest TensorFlow repository, issue the following command:
`$ git clone https://github.com/tensorflow/tensorflow`

关于git的使用方法，请参考[Git学习笔记](http://www.cnblogs.com/wangduo/p/6627924.html)。

#### 3.2 Configure the installation
配置安装命令如下：
```
$ cd tensorflow  # cd to the top-level directory created
$ ./configure
```
Here is an example execution of the configure script. Note that your own input will likely differ from our sample input：
```
Please specify the location of python. [Default is /usr/bin/python]:
Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]:
Do you wish to use jemalloc as the malloc implementation? [Y/n]
jemalloc enabled
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N]
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with Hadoop File System support? [y/N]
No Hadoop File System support will be enabled for TensorFlow
Do you wish to build TensorFlow with the XLA just-in-time compiler (experimental)? [y/N]
No XLA JIT support will be enabled for TensorFlow
Found possible Python library paths:
  /usr/local/lib/python2.7/dist-packages
  /usr/lib/python2.7/dist-packages
Please input the desired Python library path to use. Default is [/usr/local/lib/python2.7/dist-packages]
Using python library path: /usr/local/lib/python2.7/dist-packages
Do you wish to build TensorFlow with OpenCL support? [y/N] N
No OpenCL support will be enabled for TensorFlow
Do you wish to build TensorFlow with CUDA support? [y/N] Y
CUDA support will be enabled for TensorFlow
Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]:
Please specify the Cuda SDK version you want to use, e.g. 7.0. [Leave empty to use system default]: 8.0
Please specify the location where CUDA 8.0 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:
Please specify the cuDNN version you want to use. [Leave empty to use system default]: 5
Please specify the location where cuDNN 5 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:
Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size.
[Default is: "3.5,5.2"]: 6.1
Setting up Cuda include
Setting up Cuda lib
Setting up Cuda bin
Setting up Cuda nvvm
Setting up CUPTI include
Setting up CUPTI lib64
Configuration finished
```

可在配置前先执行`bazel clean`，避免与其他配置冲突。

#### 3.3 Build the pip package
To build a pip package for TensorFlow with GPU support, invoke the following command:
`$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package`

若出现error load....错误，请在bazel build前添加sudo。执行这一步需要有耐心，如果是因为网络原因导致所需的包未成功下载，请重复执行此句。

The bazel build command builds a script named build_pip_package. Running this script as follows will build a .whl file within the /tmp/tensorflow_pkg directory:
`$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg`

#### 3.4 Install the pip package
Invoke pip install to install that pip package. The filename of the .whl file depends on your platform. For example, the following command will install the pip package.
```
# for TensorFlow 1.x.x on Linux:
$ sudo pip install /tmp/tensorflow_pkg/tensorflow-1.x.x-xxx....whl
```

#### 3.5 Validate your installation
Validate your TensorFlow installation by doing the following:
Start a terminal. Change directory (cd) to any directory on your system other than the tensorflow subdirectory from which you invoked the configure command.
Invoke python:
`$ python`
Enter the following short program inside the python interactive shell:
```
# Python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```
If the system outputs the following, then you are ready to begin writing TensorFlow programs:
`Hello, TensorFlow!`
执行上述命令时，终端中会显示关于显卡的一些信息，如此则表示TensorFlow(GPU support)已正确安装。

另外，再给出一个验证程序。可将该程序保存为xxx.py文件，用`python xxx.py`在终端中执行。如下：
```
import tensorflow as tf
# Creates a graph.
a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
c = tf.matmul(a, b)
# Creates a session with log_device_placement set to True.
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
# Runs the op.
print(sess.run(c))
```
其中，sess中的log_device_placement参数用来设置显示Ops所用设备的名称。如果出现错误信息，说明安装出现问题。此时，一定要有一颗坚韧不拔，坚强勇敢的心。休息一下，理清思路，再来。

### 4.总结
至此，本文已接近尾声。回顾配置该环境的过程，主要有：双系统和显卡驱动的安装，CUDA 8.0和cuDNN v5.1的安装，环境变量的配置，Bazel的安装，TensorFlow的安装配置，bazel build命令的执行等。正确配置以上步骤，即可成功地搭建所需环境。

如您遇到问题，可以在下方留言或发消息与博主交流。 ^_^ 

一本正经的整理了这么多内容，下面要放纵一下自己，开始自由发挥了。 ^_^ 说实话，第一次配置这个环境很消耗内力（哥哥我也是有武功的），会遇到很多坑，会遇到各种网上都搜不到的红色的Errors。也不知道从什么时候开始，甚至有些喜欢黄色的Warning了，因为再怎么说它也不是Errors啊。当然绿色的Info是最好的了，代表安装successfully。

在这几天期间，无数次崩溃、无语、想抓狂，感觉一切乱如牛毛。大早上醒来就开始想流程走到了哪一步了，可能是哪里的问题，大晚上睡不着觉还在想。 (哭笑哭笑) 看官网教程看的眼快se’ia了，放眼望去，世界一片朦胧。所以准备花三万块钱治好我的近视眼，只为大老远就能看见你和你say hello（此句来自个不愿戴眼镜的近视眼）。哈哈哈，越扯越远，偏离主题了啊，停住停住，赶快停住(偷笑偷笑)……

所幸，最后终于成功地配置了这个环境，也学到了许多新知识。一句话，坚持不懈，坚韧不拔，坚定信念，自强不息。

### 5.致谢
首先，感谢实验室各位老师在精神、物质以及其他方面的支持，特别是王老师(抱拳抱拳)。
王老师曾说过：人生啊，肯定是彩色的。不要总羡慕别人。不管你上的是清华北大，还是普通学校，都要有计划，不要整天不知道自己该干什么。首先要克服的就是自己，不嫉妒别人，也不嘲笑别人，有一个开阔的心胸，大学生要有这样的品质。
你看你看，多有诗意，多有氛围。(鼓掌鼓掌热烈鼓掌)

其次，感谢师兄师姐们，很想念你们。此一别，不知何时再见。每每想起，不禁泪盈满眶，泪湿满襟，泪眼盈眶。（大哭）愿安好……
哎哎哎，别入戏太深啊，镜头快转回来。哈哈哈。发现我表演能力较强，其实我很想往这方面发展的。
另外，师兄师姐们，我霸占了你们所有人的位置(坏笑)，有用作午休的位置，有用作吃饭的位置，有用作装机的位置，有用作放配件的位置……哈哈，不过等到新学期，新师弟师妹们一来，我的自由空间就没了。

再次，感谢实验室的所用同学们，感谢大家在此期间的包容和关怀。特别是yiqi师弟，没有yiqi师弟和我的互帮互助，就不会这么顺利地配置好所需的软硬件环境。

最后，感谢自己，感谢自己的坚持和努力。还是那句话：坚持不懈，坚韧不拔，坚定信念，自强不息。

### 6.参考文献
1.https://www.tensorflow.org/install/install_sources
2.http://docs.nvidia.com/cuda/cuda-installation-guide-linux/
3.http://blog.csdn.net/zhaoyu106/article/details/52793183/
4.https://developer.nvidia.com/cudnn
5.http://www.cnblogs.com/xujianqing/p/6142963.html
6.https://docs.bazel.build/versions/master/install-ubuntu.html
7.https://www.tensorflow.org/tutorials/using_gpu

(更新于20170816)
