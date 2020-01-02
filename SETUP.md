
# Install

OS: Ubuntu 16.04 x86_64

### Connect

`ssh monoqle@monoqle-beast.local`

### Install Cuda 8.0 :

Uninstall old version CUDA Toolkit 
```
sudo apt-get purge cuda
sudo apt-get purge libcudnn6
sudo apt-get purge libcudnn6-dev
```

Install CUDA Toolkit 9.0 and cuDNN 7.0
```
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libcudnn7_7.0.5.15-1+cuda9.0_amd64.deb
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libcudnn7-dev_7.0.5.15-1+cuda9.0_amd64.deb
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libnccl2_2.1.4-1+cuda9.0_amd64.deb
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libnccl-dev_2.1.4-1+cuda9.0_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_9.0.176-1_amd64.deb
sudo dpkg -i libcudnn7_7.0.5.15-1+cuda9.0_amd64.deb
sudo dpkg -i libcudnn7-dev_7.0.5.15-1+cuda9.0_amd64.deb
sudo dpkg -i libnccl2_2.1.4-1+cuda9.0_amd64.deb
sudo dpkg -i libnccl-dev_2.1.4-1+cuda9.0_amd64.deb
sudo apt-get update
sudo apt-get install cuda=9.0.176-1
sudo apt-get install libcudnn7-dev
sudo apt-get install libnccl-dev
```

Reboot the system to load the NVIDIA drivers

### Setup PATH :
```
export PATH=/usr/local/cuda-9.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### Install Torch
```
cd ~
git clone https://github.com/torch/distro.git ~/torch --recursive
cd ~/torch; bash install-deps;
./install.sh
source ~/.bashrc
```

### Install the actual ML library
```
git clone https://github.com/kirualex/fritz-style-transfer
cd fritz-style-transfer
virtualenv -p /usr/bin/python2.7 virtual
source virtual/bin/activate
pip install -r requirements.txt
```

# ML commands

### Create dataset

```
python create_training_dataset.py \
--output ~/Documents/data/training_images.h5 \
--coco-image-dir ~/Documents/data/ \
--img-height 640 \
--img-width 640 \
--num-images 2000
```

### Train dataset
 
Change `content-weight` and  `style-weight` in order to check different results.
Good read : `https://github.com/anishathalye/neural-style`

```
python train_network.py \
--training-image-dset ~/Documents/data/training_images.h5 \
--img-height 640 \
--img-width 640 \
--style-images ~/Documents/data/style.jpg \
--weights-checkpoint ~/Documents/data/models/checkpoint.h5 \
--checkpoint-interval 100 \
--content-weight 2 \
--style-weight 8 \
--total-variation-weight 1e-4 \
--learning-rate 1e-3 \
--batch-size 1 \
--num-iterations 10000 \
--fine-tune
```


Add  `--fine-tune` to resume on an existing model

### Stylize test

```
python stylize_image.py \
--weights-checkpoint ~/Documents/data/models/checkpoint.h5 \
--img-height 640 \
--img-width 640 \
--input-image ~/Documents/data/test.jpg \
--output-image ~/Documents/data/stylized_test.jpg
```

### CoreML

```
python convert_to_coreml.py \
--weights-checkpoint ~/Documents/data/models/checkpoint.h5 \
--img-height 640 \
--img-width 640 \
--coreml-model ~/Documents/data/models/model.mlmodel
```

# Utils

### Check GPU processes

`nvidia-smi`

### Uploads
```
scp ~/Development/Data/style.jpg monoqle@monoqle-beast.local:~/Documents/data/
```

### Downloads
```
scp monoqle@monoqle-beast.local:~/Documents/data/stylized_test.jpg ~/Desktop/
scp monoqle@monoqle-beast.local:~/Documents/data/checkpoint.h5 ~/Desktop/
scp monoqle@monoqle-beast.local:~/Documents/data/model.mlmodel ~/Desktop/
```

### Add SSH key to remote
```
ssh-copy-id -i ~/.ssh/id_rsa.pub monoqle@monoqle-beast.local
ssh-add ~/.ssh/id_rsa
```
