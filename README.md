## 自用复现taskgrasp

### 原项目环境：
The code has been tested on **Ubuntu 16.04** and with **CUDA 10.0**.
如果是20系列显卡直接按原仓库readme走就行了。
#### 原仓库传送门：[[github page]](https://github.com/adithyamurali/TaskGrasp)

我是30系列显卡，所以改了点。另外不知道是不是版本不一样的原因，读取权重的时候也要稍微该点东西。

# 如果使用docker
## 1. 拉image，创建容器，进入容器
```shell
docker pull nvidia/cuda:11.1.1-cudnn8-devel-ubuntu18.04
docker run -itd --name cuda111 --gpus all --network host -v <要挂载的本地文件夹>:/mnt <刚刚拉的imageID> /bin/bash
docker exec -it <刚刚创建的容器id> /bin/bash
```

## 2. 配置容器软件环境（apt-get是应用的意思吗？不是非常清楚，只会用233）
```shell
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com A4B469963BF863CC
apt update
apt upgrade
apt-get dist-upgrade
apt-get install wget git vim unzip dialog
apt-get install -y gcc python-dev python3-dev libevent-dev
apt-get update
apt-get install ffmpeg libsm6 libxext6  -y
apt-get update && apt-get install libgl1
```
安装可视化
```shell
apt install xfce4 --fix-missing
vim ~/.bashrc
	export  DISPLAY<自己的ipv4地址>:0.0
source ~/.bashrc
```

## 3. Installation
1) Create a virtual env or conda environment with python3，我用的python3.7，应该影响不大？
```shell
conda create --name taskgrasp_37 python=3.7
conda activate taskgrasp_37
```
2) Make a workspace, clone the repo
```shell
mkdir ~/taskgrasp_ws && cd ~/taskgrasp_ws
# clone我自己的，嘻嘻
git clone https://github.com/a1534812/TaskGrasp.git
```
3) Install dependencies
```shell
cd TaskGrasp
# 安装pytorch，按自己显卡和cuda版本来就行了
pip install torch==1.9.1+cu111 torchvision==0.10.1+cu111 torchaudio==0.9.1 -f https://download.pytorch.org/whl/torch_stable.html
# requirement删除了pygraphviz，gensim，不知道为什么这两个包pip不了
conda install -c conda-forge pygraphviz
conda install -c conda-forge gensim
pip install -r requirements.txt
```
4) Compile and install [PointNet ops](https://github.com/erikwijmans/Pointnet2_PyTorch)
```shell
cd ~/taskgrasp_ws
# 原版：
git clone https://github.com/erikwijmans/Pointnet2_PyTorch.git
# 或者clone为自己的，之前以为cuda11系列要该setup，但是那个似乎是针对pointnet_ops 2.0的，v3.0作者好像改了一些东西，不需要改了，虽然改了也不影响
# 我自己的：
git clone https://github.com/a1534812/Pointnet2_PyTorch.git

cd Pointnet2_PyTorch
pip install -r requirements.txt
pip install -e .
```
5) Install [Pytorch Geometric](https://github.com/rusty1s/pytorch_geometric) (only tested on v1.5.0)
这里我直接装了最新版的Pytorch Geometric，没报错。另外原版很方便的pip用不了，咱也不懂，只能用最笨的办法本地pip了。具体方法是：去[https://pytorch-geometric.com/whl/](https://pytorch-geometric.com/whl/) ，找自己对应的版本wget，然后顺着pip，一共四个包，torch_cluster，torch_scatter，torch_sparse，torch_spline，先pip这四个包，然后：
```shell
pip install torch-geometric
```

## Dataset
因为用的服务器，懒得一个个慢慢下载再上传了，直接gdown
```shell
cd ~/taskgrasp_ws/TaskGrasp
pip install gdown

gdown https://drive.google.com/uc?id=1aZ0k43fBIZZQPPPraV-z6itpqCHuDiUU
gdown https://drive.google.com/uc?id=1fasm-8MV6zBjdnbAHLbU8_8TZOkeABkR
gdown https://drive.google.com/uc?id=1vJfkaCCLJmvT8i5OB-qx_pOojgx2ouPf
unzip ./data.zip -d ./
unzip ./cfg.zip -d ./
unzip ./checkpoints.zip -d ./

rm ./data.zip
rm ./cfg.zip
rm ./checkpoints.zip
```

## 一点小注意
可能会报错：
```shell
AttributeError: module 'distutils' has no attribute 'version'
```
似乎是tensorboard和setuptools版本问题。解决方法：
```shell
pip uninstall setuptools
conda install setuptools==58.0.4
```
