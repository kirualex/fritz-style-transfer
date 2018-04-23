# keras-fns


### Create dataset (needed once)

```
python create_training_dataset.py \
--coco-image-dir ~/Documents/data/ \
--output ~/Documents/data/datasets/fritz_dataset.h5 \
--img-width 256 \
--num-images 20000
```

### Train

```
python train_network.py \
--training-image-dset ~/Documents/data/datasets/fritz_dataset.h5 \
--style-images ~/Documents/data/images/styles/filter_outrun.jpg \
--weights-checkpoint ~/Documents/data/models/fritz_checkpoint.h5 \
--img-width 480 \
--checkpoint-interval 250 \
--content-weight 1 \
--style-weight 5 \
--learning-rate 1e-3 \
--batch-size 1 \
--log-interval 2 \
--num-iterations 10000 \
--fine-tune
```

### Stylize test

```
python stylize_image.py \
--weights-checkpoint ~/Documents/data/models/fritz_checkpoint.h5 \
--input-image ~/Documents/data/images/test.jpg \
--output-image ~/Documents/data/images/stylized-test.jpg \
--img-height 720 \
--img-width 720 
```

### Convert to CoreML

```
python convert_to_coreml.py \
--weights-checkpoint ~/Documents/data/models/fritz_checkpoint.h5 \
--coreml-model ~/Documents/data/models/model.mlmodel \
--img-height 720 \
--img-width 720 
```
