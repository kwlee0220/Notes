# DNA 과제 DL SW 플랫폼 설치

## Ubuntu 환경 (20.4)
### 시스템 update
`sudo apt-get update -y && sudo apt-get upgrade -y`

### 개발을 위한 필수 프로그램 설치 (gcc 같은 프로그램)
`sudo apt-get install build-essential`

### Nvidia driver 설치 (참고: https://webnautes.tistory.com/1428)
* 그래픽 카드 정보 확인
`ubuntu-drivers devices`
* apt로 설치하는 방법
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt-get install nvidia-driver-470 -y
```
* 설치 후 reboot 실시
* 여기까지만 설치해도 PyTorch의 inference는 사용 가능

### Nvidia CUDA Toolkit 설치
```
wget https://developer.download.nvidia.com/compute/cuda/11.4.1/local_installers/cuda_11.4.1_470.57.02_linux.run
sudo sh cuda_11.4.1_470.57.02_linux.run
```
* 설치 과정에서 "Driver" 항목을 제외시킴
* 환경변수 설정
```
sudo sh -c "echo 'export PATH=$PATH:/usr/local/cuda-11.4/bin' >> /etc/profile"
sudo sh -c "echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.4/lib64' >> /etc/profile"
sudo sh -c "echo 'export CUDADIR=/usr/local/cuda-11.4' >> /etc/profile"
source /etc/profile
```

### cuDNN 8.0.4 설치
* Library 복사
```
tar xvzf cudnn-11.2-linux-x64-v8.1.1.33.tgz
sudo cp cuda/include/cudnn* /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

* cuDNN symbolic link 설정 (필요한지 모르겠음 -> link가 이미 있는지 확인 필요)
```
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_ops_train.so.8.1.1  /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_ops_train.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8.1.1  /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8.1.1  /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8.1.1 /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_adv_train.so.8.1.1 /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_adv_train.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8.1.1  /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8
sudo ln -sf /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn.so.8.1.1  /usr/local/cuda-11.2/targets/x86_64-linux/lib/libcudnn.so.8
```

* Library reload
`sudo ldconfig`

* Library link 확인
`ldconfig -N -v $(sed 's/:/ /' <<< $LD_LIBRARY_PATH) 2>/dev/null | grep libcudnn`

### Nvidia driver 및 CUDA 삭제 방법
* 참고: https://romillion.tistory.com/93
* Nvidia driver 삭제
```
sudo apt-get remove -y --purge 'nvidia-.*' 
sudo apt-get autoremove -y
sudo apt-get autoclean 
sudo rm -rf /usr/local/cuda*
```
* CUDA 삭제
```
sudo apt-get --purge remove 'cuda*'
sudo apt-get autoremove --purge 'cuda*'
sudo rm -rf /usr/local/cuda*
```
* 환경변수 삭제: ~/.bashrc, /etc/profile
```
export PATH=$PATH:/usr/local/cuda-11.0/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.0/lib64
export CUDADIR=/usr/local/cuda-11.0
```
