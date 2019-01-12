---
layout: post
title: Install Caffe on Ubuntu
tags: [Caffe, Ubuntu, Anaconda]
---

Have installed [Caffe](http://caffe.berkeleyvision.org/) both on Ubuntu and Mac, but the Ubuntu one broke after some other installations recently, so decided to reinstall Ubuntu and Caffe, just write down the whole process for future reference and also help out with people having issues with Caffe installation on Ubuntu. Let's start!

<br />

## Specs  
Hardware: `GEFORCE GTX 1080`  
Software: `Ubuntu 16.04`, `NVIDIA DRIVER 375.26`, `CUDA 8.0`, `CuDNN 5.1`, `Anaconda 4.3.1`, `Caffe 1.0.0-rc5`

<!-- <br /> -->
<!--  -->
<!-- ## Fix '/grub/i386-pc/normal.mod' not found -->
<!-- ``` -->
<!-- sudo grub-install /dev/sda --root-directory=/ -->
<!-- ``` -->
<!--  -->
<!-- <br /> -->
<!--  -->
<!-- ## Install wifi driver for Ubuntu -->
<!--  -->
<!-- 1. copy files from installation disk or USB to Home directory -->
<!-- ``` -->
<!-- pool/main/d/dkms/dkms_XXXXX.deb -->
<!-- pool/restricted/b/bcmwl/bcmwl-kernel-source_XXXXX.deb -->
<!-- ``` -->
<!-- 2. run the following in terminal -->
<!-- ``` -->
<!-- sudo dpkg -i *.deb -->
<!-- ``` -->
<!--  -->
<br />

## Disable Nouveau Kernel Driver

1. remove all nvidia packages
```
sudo apt-get remove nvidia* && sudo apt autoremove
```
2. install some packages for build kernel
```
sudo apt-get install dkms build-essential linux-headers-generic
```
3. block and disable nouveau kernel driver
```
sudo vi /etc/modprobe.d/blacklist.conf
```
insert follow lines to the blacklist.conf
```
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```
4. disable the kernel nouveau
```
echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
```
5. build the new kernel
```
sudo update-initramfs -u
```
6. reboot

<br />

## [Install GPU Driver](http://www.geforce.com/drivers)

1. press `Ctrl+Alt+F3` and then login with your credentials
2. stop lightdm service
```
sudo service lightdm stop
```
3. start driver installer
```
sudo ~/Downloads/NVIDIA-Linux-x86_64-375.26.run
```
4. reboot and check
```
nvidia-smi
```

<br />

## [Install CUDA](https://developer.nvidia.com/cuda-downloads)

1. run the file and skip driver installation
```
sudo sh ~/Downloads/cuda_8.0.61_375.26_linux.run
```
2. modify ~/.bashrc to include the following at the end
```
export PATH=/usr/local/cuda-8.0/bin:$PATH  
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64/$LD_LIBRARY_PATH
```
3. source and verify
```
source ~/.bashrc
nvcc
```

<br />

## [Install CuDNN](https://developer.nvidia.com/rdp/cudnn-download)
```
tar -xvf cudnn-8.0-linux-x64-v5.1.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

<br />

## [Install Anaconda](https://www.continuum.io/downloads)
```
bash Anaconda2-4.3.1-Linux-x86_64.sh
```

<br />

## Install OpenCV
```
conda update conda
conda install -c menpo opencv3
```

<br />

## Install Dependencies
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install -y build-essential cmake git pkg-config
sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev protobuf-compiler
sudo apt-get install -y libatlas-base-dev 
sudo apt-get install -y --no-install-recommends libboost-all-dev
sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev
```

<br />

## Clone Caffe
```
git clone https://github.com/BVLC/caffe
```

<br />

## Modify Config File
```
cp Makefile.config.example Makefile.config
```
1. uncomment to build with cuDNN
```
USE_CUDNN := 1
```
2. uncomment if you're using OpenCV 3
```
OPENCV_VERSION := 3
```
3. CUDA directory contains bin/ and lib/ directories that we need
```
CUDA_DIR := /usr/local/cuda-8.0
```
4. comment if you're using Anaconda
```
# PYTHON_INCLUDE := /usr/include/python2.7 \
		/usr/lib/python2.7/dist-packages/numpy/core/include
# PYTHON_LIB := /usr/lib
```
5. verify anaconda location
```
ANACONDA_HOME := $(HOME)/anaconda2
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		$(ANACONDA_HOME)/include/python2.7 \
		$(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include
PYTHON_LIB := $(ANACONDA_HOME)/lib
```
6. uncomment to support layers written in Python
```
WITH_PYTHON_LAYER := 1
```

<br />

## Build Caffe
```
mkdir build
cd build
cmake ..
make all
make install
make runtest
make pycaffe
```

<br />

## Install Protobuf
```
pip install protobuf
```

<br />


Now Caffe is ready! Go to examples folder under Caffe root and Enjoy!

<br />