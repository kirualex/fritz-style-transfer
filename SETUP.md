
# Install

### Connect

`ssh monoqle@monoqle-beast.local`

### Install Cuda 8.0 :
More info here : `https://gist.github.com/zhanwenchen/e520767a409325d9961072f666815bb8`
```
cd ~/Downloads
sudo ./cuda_8.0.61_375.26_linux.run
sudo ./cuda-linux64-rel-8.0.61-21551265.run
sudo ./cuda-samples-linux-8.0.61-21551265.run
sudo bash -c "echo /usr/local/cuda/lib64/ > /etc/ld.so.conf.d/cuda.conf"
sudo ldconfig
```

### Add `/usr/local/cuda/bin` to $PATH, then test install :
```
cd /usr/local/cuda-8.0/samples
sudo make
...
cd /usr/local/cuda/samples/bin/x86_64/linux/release
./deviceQuery
```

### Install Cudnn 5.1:
(The tgz installer should be at `/usr/local`, else DL it again there)
```
sudo cp ~/Downloads/cudnn-8.0-linux-x64-v5.1.tgz /usr/local
cd /usr/local
sudo tar -xvzf cudnn-8.0-linux-x64-v5.1.tgz
```

### Install Git, Pip, Virtualenv
```
sudo apt-get install python-pip
sudo pip install virtualenv
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
