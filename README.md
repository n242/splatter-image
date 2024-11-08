# Baseline branch of our modified splatter-image

Forked from main branch of splatter image, see [original splatter image git]((https://szymanowiczs.github.io/splatter-image) (https://szymanowiczs.github.io/splatter-image))

As well as official implementation of **"Splatter Image: Ultra-Fast Single-View 3D Reconstruction" (CVPR 2024)**

# Installation

For Data preprocessing - run notebook notebook **"generate_depth_images"**, because we assume the folders are organized after pre-processing.

For running in google colab simply run notebook **"baseline_with_reduced_dim"**. 

All requirements and installation instructions are already set in the notebook, as well as train and eval. 


# Data

## ShapeNet cars
For training / evaluating on ShapeNet-SRN cars please download the srn_cars.zip from [PixelNeRF data folder](https://drive.google.com/drive/folders/1PsT3uKwqHHD2bEEHkIXB99AlIjtmrEiR?usp=sharing). Unzip the data file and change `SHAPENET_DATASET_ROOT` in `datasets/srn.py` to the parent folder of the unzipped folder. For example, if your folder structure is: `/home/user/SRN/srn_cars/cars_train`, in `datasets/srn.py` set  `SHAPENET_DATASET_ROOT="/home/user/SRN"`. 

## Evaluation

Once you downloaded the relevant dataset, evaluation can be run with 
```
python eval.py cars
```

The code will automatically download the relevant model for the requested dataset.

You can also train your own models and evaluate it with 
```
python eval.py cars --experiment_path $experiment_path
```
`$experiment_path` should hold a `model_latest.pth` file and a `.hydra` folder with `config.yaml` inside it.

To evaluate on the validation split, call with option `--split val`.

To save renders of the objects with the camera moving in a loop, call with option `--split vis`. With this option the quantitative scores are not returned since ground truth images are not available in all datasets.

You can set for how many objects to save renders with option `--save_vis`.
You can set where to save the renders with option `--out_folder`.

## Training

Single-view models are trained in two stages, first without LPIPS (most of the training), followed by fine-tuning with LPIPS.
1. The first stage is ran with:
      ```
      python train_network.py +dataset=cars
      ```
   Once it is completed, place the output directory path in configs/experiment/lpips_100k.yaml in the option `opt.pretrained_ckpt` (by default set to null).
2. Run second stage with:
      ```
      python train_network.py +dataset=$dataset_name +experiment=lpips_100k.yaml
      ```
      Remember to place the directory of the model from the first stage in the appropriate .yaml file before launching the second stage.

To train a 2-view model run:
```
python train_network.py +dataset=cars cam_embd=pose_pos data.input_images=2 opt.imgs_per_obj=5
```

## Code structure

Training loop is implemented in `train_network.py` and evaluation code is in `eval.py`. Datasets are implemented in `datasets/srn.py` and `datasets/co3d.py`. Model is implemented in `scene/gaussian_predictor.py`. The call to renderer can be found in `gaussian_renderer/__init__.py`.

## Project Scope

This project was applied as part of Computer Vision Lab in Haifa University, by [Liran Eliav](https://github.com/liraneliav) and [Neta Oren](https://github.com/n242).
