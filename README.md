# GCNgrasp自用
Same Object, Different Grasps: Data and Semantic Knowledge for Task-Oriented Grasping
原项目传送门：[ori object](https://github.com/adithyamurali/TaskGrasp.git)

碎碎念：
正在试6D-graspnet+GCNgrasp能不能简单串联一下。

其实基本就是顺着原项目的走，只是毕竟我能用的GPU全是30系列的，原本的有的部分有点不太适用，做个小记录。
据说用docker以后30系列也可以跑cuda10的东西，但是我试了，pytorch还是会报错cuda和显卡不匹配，不晓得是不是需要多的一些操作。

### 假如你要用docker
创建完容器之后
```
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com A4B469963BF863CC
apt update
apt upgrade
apt-get dist-upgrade
apt-get install wget git vim unzip dialog
apt-get install -y gcc python-dev python3-dev libevent-dev
apt-get install ffmpeg libsm6 libxext6  -y
apt-get update && apt-get install libgl1
```
可视化（每个不同的镜像拉出来的容器安装这个玩意儿都能有完全不同的问题，大家具体问题具体搜索吧= =）
```
apt install xfce4 --fix-missing
vim ~/.bashrc
	export  DISPLAY=XX.XX.XX.XX:0.0
source ~/.bashrc
# 启动时用：
startxfce4
```

### 正片开始
1. 创建虚拟环境
安装anaconda，然后（也可以直接用基环境。python版本不要太低，至少3.7）：
```
conda create --name taskgrasp_37 python=3.7
conda activate taskgrasp_37
```

2. 下载
后面顺着原仓库的操作，我改了一部分源码，所以clone自己的：
```
mkdir ~/taskgrasp_ws && cd ~/taskgrasp_ws
https://github.com/a1534812/TaskGrasp.git
cd TaskGrasp
pip install torch==1.8.0+cu111 torchvision==0.9.0+cu111 torchaudio==0.8.0 -f https://download.pytorch.org/whl/torch_stable.html
# 这两个包我只能单独下，不知道什么原因
conda install -c conda-forge pygraphviz
conda install -c conda-forge gensim
pip install -r requirements.txt
cd ~/taskgrasp_ws
# 这个放心用，v3.0可以在cuda11用的
git clone https://github.com/erikwijmans/Pointnet2_PyTorch.git
cd Pointnet2_PyTorch
pip install -r requirements.txt
pip install -e .
```
3. 首先要安装几个torch-geometric的前置：在该页面找到配合自己pytorch版本的包，下载到本地然后pip，里头每一个名字不一样的都给他下载下来装上：
[https://data.pyg.org/whl/](https://data.pyg.org/whl/)
然后再：
```
pip install torch-geometric
```
4. 下载解压缩数据集
```
pip install gdown
# 这里用gdown在命令行下载，也可以自己手动去谷歌网盘下载
cd ~/taskgrasp_ws/TaskGrasp
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
### 开跑
```
python visualize.py --data_and_grasps --obj_name 124_paint_roller
python visualize.py --visualize_labels  --visualize_labels_blacklist_object 124_paint_roller
python gcngrasp/train.py --cfg_file cfg/train/gcngrasp/gcngrasp_split_mode_t_split_idx_3_.yml
python gcngrasp/eval.py cfg/eval/gcngrasp/gcngrasp_split_mode_t_split_idx_3_.yml --save --visualize
python gcngrasp/infer.py cfg/eval/gcngrasp/gcngrasp_split_mode_t_split_idx_3_.yml --obj_name pan --obj_class frying_pan.n.01 --task flip
```
现在的问题：
我想看一眼ap，结果跑gcngrasp的plot_ap.py的时候，居然发现有的obj_class的个数是0……正在看什么鬼问题
