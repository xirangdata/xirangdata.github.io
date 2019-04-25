---
layout: post
title: Build Deep Learning Machine and Set Up Environment
tags: [Deep Learning, Build Machine, TITAN RTX, Tensorflow, Keras, Anaconda]
---

Always excited to build a new Deep Learning machine! Recently got budget around CNY¥60000, and it's time to have an upgrade. After searching around and compare different hardwares for days, I finally figured out the following build:

<br />

## [Specs](https://pcpartpicker.com/list/xFDjTB)
GPU: `NVIDIA TITAN RTX × 2`  
CPU: `Intel i9 7900X`  
Motherboard: `ASUS ROG STRIX X299-E GAMING`
Memory: `USCORSAIR DDR4 3200 32GB 16G x 2`
Storage: `SAMSUNG 970 PRO 512G NVMe M.2`
Case: `USCORSAIR AIR540`
Power Supply: `USCORSAIR HX1000i`
CPU Cooler: `Cooler Master T400 Pro CPU`

<br />

## Build Explained

The first thing is to pick up a GPU, with enough budget two TITAN RTX would be a good choice, and there's no need for SLI if only for deep learning purpose. Based on the choosen GPU, select motherboard ASUS X299-E, which support 2 x 16 PCIe with 44 lanes CPU, and thus decide Intel i9 7900X. Memory should be bigger than the max of one GPU's memory, and pick up USCORSAIR DDR4 3200 32GB which suitable with motherboard's XMP function. SAMSUNG 512G SSD is useful for speeding up data processing. USCORSAIR AIR540 is a famous and elegant case for the pickups. At last, calculate the total power, and get HX1000i.

<br />

## [Install Ubuntu](https://www.ubuntu.com/download/desktop)

To maximize the usage of GPUs, Ubuntu is a better operating system for it's less resource occupation. I used the latest version 18.04.2 LTS to make a bootable USB, and [installation](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-desktop#0) is quite straightforward. But I have manually configured disk partition, like 500M for ESP, 32G for swap, and the rest is file system to kill some weird bugs confronted before.

<br />

## Deep Learning

1. [Install CUDA 10](https://www.tensorflow.org/install/gpu)  
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb  
sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb  
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub  
sudo apt-get update  
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb  
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb  
sudo apt-get update  
sudo apt-get install --no-install-recommends nvidia-driver-410
```  
Followed the instructions on Tensorflow website to here, and I got `failed to start user manager for uid 121` after reboot, and the solution is, press `Ctrl+Alt+F3` and login with your credentials, then  
```
sudo apt install lightdm  
sudo dpkg-reconfigure lightdm  
sudo reboot
```  
Now use `nvidia-smi` to check! But it's not finish yet.  
```
sudo apt-get install --no-install-recommends cuda-10-0 libcudnn7=7.4.1.5-1+cuda10.0 libcudnn7-dev=7.4.1.5-1+cuda10.0
```  
Then we also need some improvise.  
```
sudo apt-get update  
sudo apt-get install nvinfer-runtime-trt-repo-ubuntu1804-5.0.2-ga-cuda10.0  
sudo apt-get update
apt-mark hold libcudnn7  
apt-mark hold cuda*  
apt-get update  
apt-get install -y --no-install-recommends libnvinfer5=5.0.2-1+cuda10.0  
apt-get install -y --no-install-recommends libnvinfer-dev=5.0.2-1+cuda10.0  
```  

2. Install Keras  
[Anaconda](https://www.anaconda.com/) is the best choice to save your ass.  
```
conda create -n dl python=3.6 anaconda  
conda activate dl  
pip install tensorflow-gpu
pip install keras
```  
<br />

And that's it! Love you 3000 times.
