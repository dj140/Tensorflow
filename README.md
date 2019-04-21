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

## 第三步：下载cuDD

Download cuDNN 4.0, adding it's contents to your CUDA director <br>
需要注册一个帐号才可以下载,需要下载runtime和develop两个deb文件

	https://developer.nvidia.com/cudnn
	sudo dpkg -i libcudnn7*.deb

##最后一步Install GPU TensorFlow

	sudo pip3 install tensorflow-gpu



