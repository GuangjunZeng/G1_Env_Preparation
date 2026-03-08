# G1_Env_Preparation
G1(SONIC)刷机后环境配置

查看Jetback版本指令：jetson_release
Jetback版本: 6.2
python版本：3.10

## 1. Install system packages required by PyTorch:
sudo apt-get -y update; 
sudo apt-get install -y  python3-pip libopenblas-dev;

## 2. 安装 cusparselt ：
官方文档以下指令不适用于较新的cuda版本
wget raw.githubusercontent.com/pytorch/pytorch/5c6af2b583709f6176898c017424dc9981023c28/.ci/docker/
wget https://raw.githubusercontent.com/pytorch/pytorch/5c6af2b583709f6176898c017424dc9981023c28/.ci/docker/common/install_cusparselt.sh
export CUDA_VERSION=12.6  
bash ./install_cusparselt.sh

替换方法（https://developer.nvidia.com/cusparselt-downloads?target_os=Linux&target_arch=aarch64-jetson&Compilation=Native&Distribution=Ubuntu&target_version=22.04&target_type=deb_local）：
  wget https://developer.download.nvidia.com/compute/cusparselt/0.8.1/local_installers/cusparselt-local-tegra-repo-ubuntu2204-0.8.1_0.8.1-1_arm64.deb
  sudo dpkg -i cusparselt-local-tegra-repo-ubuntu2204-0.8.1_0.8.1-1_arm64.deb
  sudo cp /var/cusparselt-local-tegra-repo-ubuntu2204-0.8.1/cusparselt-*-keyring.gpg /usr/share/keyrings/
  sudo apt-get update
  sudo apt-get -y install cusparselt
  sudo apt-get -y install cusparselt-cuda-12 （for CUDA 12 specific package）

## 3. 安装conda
  wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-aarch64.sh
  bash Miniconda3-latest-Linux-aarch64.sh
  source ~/.bashrc
  conda create -n ams python=3.10

## 4. install PyTorch
注意：NVIDIA 官方没有为 JetPack 6.2 提供 .whl 安装包，因此以下指令不可行：
export TORCH_INSTALL=https://developer.download.nvidia.com/compute/redist/jp/v62/pytorch/torch-2.5.0a0+872d972e41.nv24.08.17622132-cp310-cp310-linux_aarch64.whl
python3 -m pip install --upgrade pip
python3 -m pip install numpy=='1.26.1'
python3 -m pip install --no-cache $TORCH_INSTALL
访问 NVIDIA 的官方下载目录进行验证：
wget -q -O- https://developer.download.nvidia.com/compute/redist/jp/v62/pytorch/ 2>&1 | head -50
返回结果为none！

平替（jetpack 6.1)
  pip install https://developer.download.nvidia.com/compute/redist/jp/v61/pytorch/torch-2.5.0a0+872d972e41.nv24.08.17622132-cp310-cp310-linux_aarch64.whl
  python3 -c "import torch; print(torch.version); print(torch.cuda.is_available())"  验证兼容成功

## 5. 
  pip3 install -e legged_gym
  pip3 install -e phc
  pip3 install -e poselib
  pip3 install -e g1_gym_deploy
  pip3 install mujoco tqdm joblib smplx easydict websocket loguru lcm onnxruntime

## 6. 
  sudo apt-get install -y liblcm-dev
  cd unitree_sdk2
  rm -rf build
  mkdir build && cd build
  cmake ..
  make
