# Tensorflow-install-tutorial

相关环境：<br>
笔记本asus A441U <br>
cpu:intel-i5-7200u <br>
gpu:NVIDIA GEFORCE 920MX <br>

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

## 验证安装

	python3
	import tensorflow as tf
	tf.__version__

## 没有英伟达GPU的可以安装CPU版

	sudo pip3 install tensorflow

# 源码编译安装Tensorflow，适合多个平台运行

若需要在树莓派上安装，官网有详细[安装教程](https://www.tensorflow.org/install/source_rpi)

## 在arm版上编译安装Tensorflow(python3)

## 安装相关依赖(pyhton,pip,bazel)

	sudo apt install python3-dev python3-pip
	pip3 install -U --user pip six numpy wheel setuptools mock
	pip3 install -U --user keras_applications==1.0.6 --no-deps
	pip3 install -U --user keras_preprocessing==1.0.5 --no-deps
	sudo apt-get install build-essential openjdk-8-jdk python zip unzip

## 安装Bazel

从github里下载bazel源码，注意要下载dist版本的，bazel的版本和你安装tensorflow的版本也要对应，官网有相应的表格

Download bazel-<version>-dist.zip from[Github] (https://github.com/bazelbuild/bazel/releases),for example bazel-0.19.2-dist.zip.

bazel源码没有在外层目录打包，unzip的时候先建一个文件夹把源码发进去在解压
	
	mkdir bazel-0.19.2
	mv bazel-0.19.2-dist.zip ./bazel-0.19.2
	cd bazel-0.19.2
	unzip bazel-0.19.2.zip

接下来修改/scripts/bootstrap/compile.sh文件，否则执行compile.sh时Java会内存溢出

	vim scripts/bootstrap/compile.sh

将第122行修改成

	run "${JAVAC}" -J-Xms256m -J-Xmx384m -classpath "${classpath}" -sourcepath "${sourcepath}"

若执行后还是内存溢出则加多一级 –J-Xms512m

	run "${JAVAC}" -J-Xms256m -J-Xmx384m –J-Xms512m -classpath "${classpath}" -sourcepath "${sourcepath}
	
改完后执行.compile.sh
	
	cd ~/bazel-0.19.2
	./compile.sh

完成之后将生成的文件copy到/usr/local/bin

	cp output/bazel /usr/local/bin/bazel

测试

	bazel version

## 下载tensorflow源码

	git clone https://github.com/tensorflow/tensorflow.git

代码库默认为 master 开发分支。您也可以检出要编译的版本分支：
    git checkout branch_name  # r1.9, r1.10, etc.

	cd tensorflow

执行./configure后，若报无法下载xx包的错误，bazel源码src/main/cpp/blaze.cc
第1256行，忽略代理

	static void PrepareEnvironmentForJvm() {
	  if (!blaze::GetEnv("http_proxy").empty()) {
		PrintWarning("ignoring http_proxy in environment.");
		blaze::UnsetEnv("http_proxy");
		}
	}

解决方法打开chrome下载这两个网址的证书

	http://mirror.bazel.build/
	https://github.com/

保存为bazel.build.cer  github.com.cer

	cd /usr/lib/jvm/java-1.8.0-openjdk-armhf/jre/lib/security/

执行keytool需要输入密码，默认密码为changeit

	keytool -import -alias bazel.build  -keystore cacerts -file ./bazel.build.cer -trustcacerts
	keytool -import -alias github.com  -keystore cacerts -file ./github.com.cer -trustcacerts

修改tensorflow/core/platform/platform.h

	vim tensorflow/core/platform/platform.h

第48行

	#elif defined(_WIN32)
	#define PLATFORM_WINDOWS

	#elif defined(__arm__)
	#define PLATFORM_POSIX

	// Require an outside macro to tell us if we're building for Raspberry Pi.
	#if !defined(RASPBERRY_PI)
	#define IS_MOBILE_PLATFORM   //就是这行
	#endif  // !defined(RASPBERRY_PI)

	#else
	// If no platform specified, use:
	#define PLATFORM_POSIX

针对树莓派做了特殊判断，不是树莓派的设备一律按移动设备处理。所以把这句改成#define PLATFORM_POSIX，保存退出。

执行./configure
	
	cd ~/tensorflow
	./configure
	
	Please specify the location of python. [Default is /usr/bin/python]:
	/usr/bin/python3 # 填写系统中python安装位置，python2.7默认回车，python3需对其手动输入以作修改
	Please input the desired Python library path to use. Default is [/usr/local/lib/python2.7/dist-packages]:
	/usr/local/lib/python3/dist-packages # 同上，2.7可回车默认
	其他选项无默认需求，都选n。
	
编译
	
	bazel build -c opt --copt="-funsafe-math-optimizations" --copt="-ftree-vectorize" --copt="-fomit-frame-pointer" --local_resources 1024,1.0,1.0 --verbose_failures tensorflow/tools/pip_package:build_pip_package

如果过程中出现问题，需要重新执行上述语句，需要先执行bazel clean!

	$ bazel build -c opt //tensorflow/tools/pip_package:build_pip_package
	
#若cpu不支持avx而支持sse指令集，使用如下命令

	$ bazel build -c opt --copt=-msse4.1 --copt=-msse4.2 //tensorflow/tools/pip_package:build_pip_package

n小时后，若出现下面信息则编译完成

	Target //tensorflow/tools/pip_package:build_pip_package up-to-date:
	bazel-bin/tensorflow/tools/pip_package/build_pip_package

接着来执行
	
	bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

这时将会把用于pip安装的whl文件生成到/tmp/tensorflow_pkg目录

	sudo pip3 install /tmp/tensorflow_pkg/xxxxx.whl
	sudo ldconfig

测试
 
	python3
	import tensorflow as tf
	tf.__version__

参考链接：

https://github.com/bazelbuild/bazel/issues/1308

https://github.com/bazelbuild/bazel/releases

https://www.tensorflow.org/install/source

https://veevv.com/2017/12/12/how-to-install-tensorflow-on-arm/#

https://docs.bazel.build/versions/master/install-compile-source.html
