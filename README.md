# BackdoorBench: a comprehensive benchmark of backdoor attack and defense methods

![Pytorch 1.11.0](https://img.shields.io/badge/PyTorch-1.11-brightgreen) [![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC_BY--NC_4.0-brightgreen.svg)](https://creativecommons.org/licenses/by-nc/4.0/) ![Release .20](https://img.shields.io/badge/Release-2.0-brightgreen)

<p align="center">
<br>
  <a href="http://backdoorbench.com" target="_blank"> Website </a >  •  <a href="https://openreview.net/pdf?id=31_U7n18gM7"> Paper </a > •  <a href="http://backdoorbench.com/leader_cifar10"> Leaderboard </a > <br>
<br>
</p >

BackdoorBench is a comprehensive benchmark of backdoor learning, which studies the adversarial vulnerablity of deep learning models in the training stage. It aims to provide **easy implementations** of mainstream backdoor attack and defense methods.

### ❗Model and Data Updates

We disclose the backdoor model we used and the corresponding backdoor attack image in the link below. Each zip file contains the following things: 

- **bd_train_dataset**: train backdoor data
- **bd_test_dataset**: test backdoor data
- **attack_result.py**: the backdoor model and the module that reads data
- **cross_test_dataset**: cross mode data during training(for some special backdoor attack: wanet, inputaware and so on)

If you want to use the backdoor model, you can download the zip file and unzip in your own workspace. Then you can use the function load_attack_result in the file [save_load_attack.py](./utils/save_load_attack.py) to load the backdoor model, the poisoned train data and the poisoned test data.
### ❗ For V1.0 please check [here](https://github.com/SCLBD/BackdoorBench/tree/v1)

<font size=5><center><b> Table of Contents </b> </center></font>

* [Features](#features)

* [Installation](#Installation)

* [Quick Start](#quick-start)

  * [Attack](#attack)
  
  * [Defense](#defense)

* [Supported attacks](#supported-attacks)

* [Supported defenses](#supported-defsense)

* [Analysis Tools](#analysis-tools)

* [Citation](#citation)

* [Copyright](#copyright)

---


## Features
<a href="#top">[Back to top]</a>

BackdoorBench has the following features:


## Installation

<a href="#top">[Back to top]</a>

You can run the following script to configurate necessary environment

```
git clone git@github.com:SCLBD/BackdoorBench.git
cd BackdoorBench
conda create -n backdoorbench python=3.8
conda activate backdoorbench
sh ./sh/install.sh
sh ./sh/init_folders.sh
```

## Quick Start

### Attack

<a href="#top">[Back to top]</a>

This is a example for BadNets

1. Generate trigger

If you want to change the trigger for BadNets, you should go to the `./resource/badnet`, and follow the readme there to generate new trigger pattern.
```shell
python ./resource/badnet/generate_white_square.py --image_size 32 --square_size 3 --distance_to_right 0 --distance_to_bottom 0 --output_path ./resource/badnet/trigger_image.png
```
Note that for data-poisoning-based attacks (BadNets, Blended, Label Consistent, Low Frequency, SSBA). 
Our scripts in `./attack` are just for training, they do not include the data generation process.(Because they are time-comsuming, and we do not want to waste your time.) 
You should go to the `./resource` folder to generate the trigger for training.

2. Backdoor training
```
python ./attack/badnet.py --yaml_path ../config/attack/prototype/cifar10.yaml --patch_mask_path ../resource/badnet/trigger_image.png  --save_folder_name badnet_0_1
```
After attack you will get a folder with all files saved in `./record/<folder name in record>`, including `attack_result.pt` for attack model and backdoored data, which will be used by following defense methods.
If you want to change the args, you can both specify them in command line and in corresponding YAML config file (eg. [default.yaml](./config/attack/badnet/default.yaml)).(They are the defaults we used if no args are specified in command line.)
The detailed descriptions for each attack may be put into the `add_args` function in each script.

Note that for some attacks, they may need pretrained models to generate backdoored data. For your ease, we provide various data/trigger/models we generated in google drive. You can download them at [here](https://drive.google.com/drive/folders/1lnCObVBIUTSlLWIBQtfs_zi7W8yuvR-2?usp=share_link)




### Defense

<a href="#top">[Back to top]</a>

This is a demo script of running abl defense on cifar-10 for badnet attack. Before defense you need to run badnet attack on cifar-10 at first. Then you use the folder name as result_file.

```
python ./defense/abl.py --result_file badnet_0_1 --yaml_path ./config/defense/abl/cifar10.yaml --dataset cifar10
```


If you want to change the args, you can both specify them in command line and in corresponding YAML config file (eg. [default.yaml](./config/defense/abl/default.yaml)).(They are the defaults we used if no args are specified in command line.)
The detailed descriptions for each attack may be put into the `add_args` function in each script.


<a href="#top">[Back to top]</a>
### Analysis Tools


|        File name                                    |          Method                                                                 |             Category           |
|:----------------------------------------------------|:--------------------------------------------------------------------------------|:--------------------------------|
| [visual_tsne.py](analysis/visual_tsne.py)           | T-SNE, the T-SNE of features                                                    |            Data Analysis       |
| [visual_umap.py](analysis/visual_umap.py)           | UMAP, the UMAP of features                                                      |            Data Analysis       |
| [visual_quality.py](./analysis/visual_quality.py)   | Image Quality, evaluating the given results using some image quality metrics    |            Data Analysis       |
| [visual_na.py](analysis/visual_na.py)               | Neuron Activation, the activation value of a given layer of Neurons             |            Model Analysis      |
| [visual_shap.py](analysis/visual_shap.py)           | Shapely Value, the Shapely Value for given inputs and a given layer             |            Model Analysis      |
| [visual_gradcam.py](analysis/visual_gradcam.py)     | Grad-CAM, the Grad-CAM for given inputs and a given layer                       |            Model Analysis      |
| [visualize_fre.py](analysis/visualize_fre.py)       | Frequency Map, the Frequency Saliency Map for given inputs and a given layer    |            Model Analysis      |
| [visual_act.py](analysis/visual_act.py)             | Activated Image, the top images who activate the given layer of Neurons most    |            Model Analysis      |
| [visual_fv.py](analysis/visual_fv.py)               | Feature Visualization, the synthetic images which activate the given Neurons    |            Model Analysis      |
| [visual_fm.py](analysis/visual_fm.py)               | Feature Map, the output of a given layer of CNNs for a given image              |            Model Analysis      |
| [visual_actdist.py](analysis/visual_actdist.py)     | Activation Distribution, the class distribution of Top-k images which activate the Neuron most    |            Model Analysis      |
| [visual_tac.py](analysis/visual_tac.py)             | Trigger Activation Change, the average (absolute) activation change between images with and without triggers   |            Model Analysis      |
| [visual_lips.py](analysis/visual_lips.py)           | Lipschitz Constant, the lipschitz constant of each neuron                       |            Model Analysis      |
| [visual_landscape.py](analysis/visual_landscape.py) | Loss Landscape, the loss landscape of given results with two random directions  |            Model Analysis      |
| [visual_network.py](analysis/visual_network.py)     | Network Structure, the Network Structure of given model                         |            Model Analysis      |
| [visual_hessian.py](analysis/visual_hessian.py)     | Eigenvalues of Hessian, the dense plot of hessian matrix for a batch of data    |            Model Analysis      |
| [visual_metric.py](analysis/visual_metric.py)       | Metrics, evaluating the given results using some metrics                        |              Evaluation        |
| [visual_cm.py](analysis/visual_cm.py)               | Confusion Matrix          | |








































## Citation

<a href="#top">[Back to top]</a>

If interested, you can read our recent works about backdoor learning, and more works about trustworthy AI can be found [here](https://sites.google.com/site/baoyuanwu2015/home).

```
@inproceedings{backdoorbench,
  title={BackdoorBench: A Comprehensive Benchmark of Backdoor Learning},
  author={Wu, Baoyuan and Chen, Hongrui and Zhang, Mingda and Zhu, Zihao and Wei, Shaokui and Yuan, Danni and Shen, Chao},
  booktitle={Thirty-sixth Conference on Neural Information Processing Systems Datasets and Benchmarks Track},
  year={2022}
}

@article{wu2023adversarial,
  title={Adversarial Machine Learning: A Systematic Survey of Backdoor Attack, Weight Attack and Adversarial Example},
  author={Wu, Baoyuan and Liu, Li and Zhu, Zihao and Liu, Qingshan and He, Zhaofeng and Lyu, Siwei},
  journal={arXiv preprint arXiv:2302.09457},
  year={2023}
}

@article{cheng2023tat,
  title={TAT: Targeted backdoor attacks against visual object tracking},
  author={Cheng, Ziyi and Wu, Baoyuan and Zhang, Zhenya and Zhao, Jianjun},
  journal={Pattern Recognition},
  volume={142},
  pages={109629},
  year={2023},
  publisher={Elsevier}
}

@inproceedings{sensitivity-backdoor-defense-nips2022,
 title = {Effective Backdoor Defense by Exploiting Sensitivity of Poisoned Samples},
 author = {Chen, Weixin and Wu, Baoyuan and Wang, Haoqian},
 booktitle = {Advances in Neural Information Processing Systems},
 volume = {35},
 pages = {9727--9737},
 year = {2022}
}

@inproceedings{dbd-backdoor-defense-iclr2022,
    title={Backdoor Defense via Decoupling the Training Process},
    author={Huang, Kunzhe and Li, Yiming and Wu, Baoyuan and Qin, Zhan and Ren, Kui},
    booktitle={International Conference on Learning Representations},
    year={2022}
}

@inproceedings{ssba-backdoor-attack-iccv2021,
    title={Invisible backdoor attack with sample-specific triggers},
    author={Li, Yuezun and Li, Yiming and Wu, Baoyuan and Li, Longkang and He, Ran and Lyu, Siwei},
    booktitle={Proceedings of the IEEE/CVF International Conference on Computer Vision},
    pages={16463--16472},
    year={2021}
}
```


## Copyright

<a href="#top">[Back to top]</a>


This repository is licensed by [The Chinese University of Hong Kong, Shenzhen](https://www.cuhk.edu.cn/en) and [Shenzhen Research Institute of Big Data](http://www.sribd.cn/en) under Creative Commons Attribution-NonCommercial 4.0 International Public License (identified as [CC BY-NC-4.0 in SPDX](https://spdx.org/licenses/)). More details about the license could be found in [LICENSE](./LICENSE).

This project is built by the Secure Computing Lab of Big Data ([SCLBD](http://scl.sribd.cn/index.html)) at The Chinese University of Hong Kong, Shenzhen and Shenzhen Research Institute of Big Data, directed by Professor [Baoyuan Wu](https://sites.google.com/site/baoyuanwu2015/home). SCLBD focuses on research of trustworthy AI, including backdoor learning, adversarial examples, federated learning, fairness, etc.

If any suggestion or comment, please contact us at <wubaoyuan@cuhk.edu.cn>.
