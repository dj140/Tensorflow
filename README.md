# Tensorflow

## Install Tensorflow GPU
	
	Frist you need an NVIDIA GPU with a compute capability > 3.0.
	
	There are threes major steps that need to be taken, in order for all of this to work.

## 第一步：在英伟达官网下载适合你电脑的显卡驱动

	https://www.nvidia.com/Download/index.aspx?lang=en-us

选择.run文件下载，下载好后执行下面指令给文件权限

	sudo chmod +x xxx.run

接下来按键盘的ctrl + alt + F1进入终端界面，在终端界面输入下面指令关闭x server
	
	sudo server lightdm stop
	sudo ./xxx.run –no-x-check –no-nouveau-check –no-opengl-files

## Install CUDA Toolkit 
## 第二步：下载CUDA Toolkit

选择合适自己系统的安装包，建议选择.run文件下载

	https://developer.nvidia.com/cuda-downloads

下载好后，赋予文件权限，执行安装，所有参数保持默认，除了驱动不需要安装，一定不要选择安装驱动
	
	sudo chmod +x xxx.run
	sudo ./xxx.run
## ubuntu16.04通过apt的方式安装CUDA10
	
Add NVIDIA package repositories

## Add HTTPS support for apt-key
	
	sudo apt-get install gnupg-curl
	wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
	sudo dpkg -i cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
	sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
	sudo apt-get update
	wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
	sudo apt install ./nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
	sudo apt-get update

## Install NVIDIA Driver
## Issue with driver install requires creating /usr/lib/nvidia

	sudo mkdir /usr/lib/nvidia
	sudo apt-get install --no-install-recommends nvidia-410

## Reboot. Check that GPUs are visible using the command: nvidia-smi

## Install development and runtime libraries (~4GB)

	sudo apt-get install --no-install-recommends \
    	cuda-10-0 \
    	libcudnn7=7.4.1.5-1+cuda10.0  \
    	libcudnn7-dev=7.4.1.5-1+cuda10.0


# Install TensorRT. Requires that libcudnn7 is installed above.
	
	sudo apt-get update && \
        	sudo apt-get install nvinfer-runtime-trt-repo-ubuntu1604-5.0.2-ga-cuda10.0 \
        	&& sudo apt-get update \
        	&& sudo apt-get install -y --no-install-recommends libnvinfer-dev=5.0.2-1+cuda10.0

## 第三步：下载cuDD

Download cuDNN 4.0, adding it's contents to your CUDA director <br>
需要注册一个帐号才可以下载,需要下载runtime和develop两个deb文件

	https://developer.nvidia.com/cudnn
	sudo dpkg -i libcudnn7*.deb

## 最后一步Install GPU TensorFlow

	sudo pip3 install tensorflow-gpu

##验证安装

	python3
	import tensorflow as tf
	tf.__version__

## 没有英伟达GPU的可以安装CPU版

	sudo pip3 install tensorflow

## 其他的python依赖包

	pip install pillow
	pip install lxml
	pip install jupyter
	pip install matplotlib

## 下载tensorflow模型

	git clone https://github.com/tensorflow/models.git

## 添加环境变量（注意当重启或关闭终端时需要重新添加）

	protoc object_detection/protos/*.proto --python_out=.
	export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim

If you get an error on the protoc command on Ubuntu, check the version you are running with protoc --version, <br>
if it's not the latest version, you might want to update. As of my writing of this, we're using 3.4.0. <br>
In order to update or get protoc, head to the protoc releases page. Download the python version, extract, <br>
navigate into the directory and then do: <br>

	sudo ./configure
	sudo make check
	sudo make install
	sudo ldconfig





