# Backdoor Attacks and Neural Cleanse defense on Deep Neural Networks
## Overview
Backdoor attacks in deep learning are a form of adversarial attack where a model is manipulated to respond to certain trigger inputs in a predetermined way, often while performing normally on standard inputs.

Similarily, backdoor defense in Deep Neural Networks is a methodology to evade such attacks.

In this repository, the [Neural Cleanse](https://ieeexplore.ieee.org/document/8835365) defense is implemented on the CIFAR-10 dataset.
There are also implementations of [BadNets](https://arxiv.org/abs/1708.06733) and [Data Poisoning](https://arxiv.org/abs/1712.05526) attacks on the CIFAR-10 dataset.

## Dataset

All experiments will be conducted on CIFAR-10 dataset.
The CIFAR-10 dataset consists of 60000 32x32 color images in 10 classes, with 6000 images per class. There are 50000 training images and 10000 test images.

The dataset is divided into five training batches and one test batch, each with 10000 images. The test batch contains exactly 1000 randomly-selected images from each class. The training batches contain the remaining images in random order, but some training batches may contain more images from one class than another. Between them, the training batches contain exactly 5000 images from each class.

10 classes present in the dataset are: airplane, automobile, bird, cat, deer, dog, frog, horse, ship, and truck.

## Setup
You can run the following scripts to configure the environment:
```
git@github.com:JuraH32/Neural_Cleanse.git
cd Nerual_Cleanse
conda create -n backdoorbench python=3.8
conda activate backdoorbench
sh ./sh/install.sh (alternative is pip install -r requirements.txt but is not recommended)
sh ./sh/init_folders.sh
```
The alternative way is by installing the dependencies using requirements.txt file, but the above method is recommended.
```
pip install -r requirements.txt
```

Also you can manually install everything from the install.sh file on a windows machine.

## Usage
### Attack
To run the attack, run the following command for BadNets attack:
```
python ./attack/badnet.py --yaml_path ../config/attack/prototype/cifar10.yaml --patch_mask_path ../resource/badnet/trigger_image.png  --save_folder_name badnet_0_1
```
or for Data Poisoning attack:
```
python ./attack/blended.py --attack_trigger_img_path ../resource/blended/hello_kitty_pattern.png  --save_folder_name poisoning_kitty_pattern
```

You can also run the attack with different parameters, like different trigger image.
After attack you will get a folder with all files saved in ./record/<folder name in record>, 
including attack_result.pt for attack model and backdoored data, which will be used by following defense methods. 

### Defense
To run the defense, run the following command:
```
python ./defense/abl.py --result_file badnet_0_1 --yaml_path ./config/defense/abl/cifar10.yaml --dataset cifar10
```

### Original repository
This repository is based on the [BackdoorBench](https://github.com/SCLBD/BackdoorBench) repository.