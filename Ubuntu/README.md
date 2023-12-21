# Ubuntu Set


1. 우분투 GUI설치
```
sudo apt update
sudo apt ubuntu-desktop
sudo reboot
```

\
2. 파이썬 설치
```
sudo apt update && sudo apt upgrade python3
sudo apt install python3-pip
sudo add-apt-repository ppa:deadsnakes/ppa
```

\
3. 아나콘다 설치
```
sudo apt-get install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6
cd ~

# https://repo.anaconda.com/archive/ 여기서 OS 및 아키텍쳐에 따라 다르게 설치
wget https://repo.anaconda.com/archive/Anaconda3-2023.09-0-Linux-aarch64.sh

echo $SHELL

# $SHELL이 zsh인지 bash인지 확인 후 아래 명령어 실행 | 위에서 설치한 쉘의 파일을 입력
bash Anaconda3-2023.09-0-Linux-aarch64.sh

source ~/.bashrc
conda init bash
```
> `source ~/.bashrc` 이후 `conda`명령어가 먹히지 않으면 아래 멍령어를 사용
> ```
> # 터미널이 ~/.zshrc 인지 ~/.bashrc 인지 구분해서 사용
> echo -e "\nexport PATH=$HOME/anaconda3/bin:$PATH" >> ~/.bashrc
> ```


## sudo 권한 실행 시 비밀번호 무시

```
echo '{사용자명} ALL=NOPASSWD: ALL' | sudo tee -a /etc/sudoers
```


## Hierarchical-Localization 설치

```
conda create -n localization python=3.10
conda activate localization

```


1. colmap 설치
```
sudo apt-get install \
    git \
    cmake \
    ninja-build \
    build-essential \
    libboost-program-options-dev \
    libboost-filesystem-dev \
    libboost-graph-dev \
    libboost-system-dev \
    libeigen3-dev \
    libflann-dev \
    libfreeimage-dev \
    libmetis-dev \
    libgoogle-glog-dev \
    libgtest-dev \
    libsqlite3-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev \
    libceres-dev

# libceres-dev에러 대책
git clone https://github.com/google/glog.git
cd glog/
git checkout v0.5.0-rc2
mkdir build && cd build
cmake ..
sudo make -j12 install

# colmap 3.8 설치(3.8버전은 AMD64만 지원되나봄 ARM64에서 진행하니 진행이 안됨)
git clone -b 3.8 --recursive https://github.com/colmap/colmap.git
cd colmap
mkdir build
cd build
cmake .. -GNinja
ninja
sudo ninja install

# 확인용
colmap -h
colmap gui

# 프로젝트 안에 CMakeLists.txt를 찾아서 cmake_minimum_required(VERSION 3.5)의 버전을 3.5로 맞춰준다
find / -name 'CMakeLists.txt'
sudo vi {찾은 경로}
```

2. pycolmap 설치
```
git clone -b v0.4.0 --recursive https://github.com/colmap/pycolmap.git
cd pycolmap
pip install .
```

---


## pyslam 설치

> AMD64 아키텍처로만 실행할 수 있음
```
git clone --recursive https://github.com/luigifreda/pyslam.git
cd pyslam
git checkout ubuntu20
. pyenv-create.sh 
. install_basic.sh

# numpy 버전이 맞지 않아 실행이 안되므로 버전 업데이트를 해줌
pip install numpy==1.21

# 샘플 실행
python3 -O main_vo.py

# 메인 테스트 데이터 설치
. install_all.sh

# 메인 실행
python3 -O main_slam.py
```
