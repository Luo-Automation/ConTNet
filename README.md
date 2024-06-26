# ConTNet

## Introduction

<!-- **ConTNet** (**Con**vlution-**T**ranformer Network) is proposed mainly in response to the following two issues: (1) ConvNets lack a large receptive field, limiting the performance of ConvNets on downstream tasks. (2) Transformer-based model is not robust enough and requires special training settings or hundreds of millions of images as the pretrain dataset, thereby limiting their adoption. **ConTNet** combines convolution and transformer alternately, which is very robust and can be optimized like ResNet unlike the recently-proposed transformer-based models (e.g., ViT.py, DeiT) that are sensitive to hyper-parameters and need many tricks when trained from scratch on a midsize dataset (e.g., ImageNet).
  -->

**ConTNet** (**Con**vlution-**T**ranformer Network) is a neural network built by stacking convolutional layers and transformers alternately. This architecture is proposed in response to the following two issues: **(1)** The receptive field of convolution is limited by a local window (3x3), which potentially impairs the performance of ConvNets on downstream tasks. **(2)** Transformer-based models suffers from insufficient robustness, as a result, the training course requires multiple training tricks and tons of regularization strategies. In our ConTNet, these drawbacks are alleviated through the combination of convolution and transformer. Two perspectives are offered to understand the motivation. **From the view of ConvNet**, the transformer sub-layer is inserted between any two conv layers to enhance the non-local interactions of ConvNet. **From the view of Transformer**, the presence of convolution layers reintroduces the inductive bias as a cause of under-fitting. Through numerical experiments, we find that ConTNet achieves competitive performance on image recognition and downstream tasks. More notably, ConTNet can be optimized easily even in the same way as ResNet.
<!-- ![image](https://user-images.githubusercontent.com/81896692/119272384-2b904e00-bc38-11eb-87a5-193275cc8be2.png) -->
![image](https://github.com/yan-hao-tian/ConTNet/blob/main/arch5.png)
![image](https://github.com/yan-hao-tian/ConTNet/blob/main/block2.png)
![image](https://github.com/yan-hao-tian/ConTNet/blob/main/block3.png)
## Training & Validation with this Repo
We give an example of one machine multi-gpus training.
```
CUDA_VISIBLE_DEVICES=0,1,2,3 python3 -m torch.distributed.launch --nproc_per_node=4 --master_port 29501 main.py --arch ConT-M --batch_size 256 --save_path debug_trial_cont_m --save_best True 
```
To validate a model, please add the arg ```--eval ```.
```
CUDA_VISIBLE_DEVICES=0 python3 -m torch.distributed.launch --nproc_per_node=1 --master_port 29501 main.py --arch ConT-M --batch_size 256 --save_path debug_trial --eval ./debug_trial_cont_m/checkpoint_bestTop1.pth
```
To implement resume training, please add the arg ```--resume```.
```
CUDA_VISIBLE_DEVICES=0,1,2,3 python3 -m torch.distributed.launch --nproc_per_node=4 --master_port 29501 main.py --arch ConT-M --batch_size 256 --save_path debug_trial --save_best True --resume ./debug_trial_cont_m/checkpoint_bestTop1.pth
```
## Pretrained Weights on ImageNet
ImageNet-pretrained weights are available from [Google Drive][1] or [Baidu Cloud][2](the code is 3k3s).

## Main Results on ImageNet

|  name   |   resolution  |   acc@1   |   #params(M) |   FLOPs(G)   |   model   |
| ----  |   ----    |   ----    |   ----    |   ----    |   ----    |
|   Res-18  |   224x224 |  71.5     |   11.7    |   1.8 |       |
|   ConT-S  |   224x224 |  **74.9** |   10.1    |   1.5 |       |
|   Res-50  |   224x224 |  77.1     |   25.6    |   4.0 |       |
|   ConT-M  |   224x224 |  **77.6** |   19.2    |   3.1 |       |
|   Res-101 |   224x224 |  **78.2** |   44.5    |   7.6 |       |
|   ConT-B  |   224x224 |   77.9    |   39.6    |   6.4 |       |
|   DeiT-Ti<sup>*</sup>  |   224x224 |  72.2    |   5.7    |   1.3 |       |
|   ConT-Ti<sup>*</sup>  |   224x224 |  **74.9**|   5.8    |   0.8 |       |
|   Res-18<sup>*</sup>  |   224x224 |  73.2     |   11.7    |   1.8 |       |
|   ConT-S<sup>*</sup>  |   224x224 |  **76.5** |   10.1    |   1.5 |       |
|   Res-50<sup>*</sup>  |   224x224 |  78.6     |   25.6    |   4.0 |       |
|   DeiT-S<sup>*</sup>  |   224x224 |  79.8     |   22.1    |   4.6 |       |
|   ConT-M<sup>*</sup>  |   224x224 |  **80.2** |   19.2    |   3.1 |       |
|   Res-101<sup>*</sup> |   224x224 |  80.0     |   44.5    |   7.6 |       |
|   DeiT-B<sup>*</sup>  |   224x224 |  **81.8** |   86.6    |   17.6|       |
|   ConT-B<sup>*</sup>  |   224x224 |  **81.8** |   39.6    |   6.4 |       |

Note: <sup>*</sup> indicates training with strong augmentations(auto-augmentation and mixup).

## Main Results on Downstream Tasks

Object detection results on COCO.

| method  | backbone  | #params(M)  | FLOPs(G)  | AP    | AP</sup>s<sup>  | AP</sup>m<sup>  | AP</sup>l<sup>  |
| ----    | ----      | ----        | ----      | ----  | --------        | -----           | -----           |
|RetinaNet| Res-50 <br> ConTNet-M|  32.0 <br> 27.0  | 235.6 <br> 217.2  | 36.5 <br> **37.9**  | 20.4 <br> **23.0** | 40.3 <br> **40.6** | 48.1 <br> **50.4** |
| FCOS    | Res-50 <br> ConTNet-M|  32.2 <br> 27.2  | 242.9 <br> 228.4  | 38.7 <br> **40.8**  | 22.9 <br> **25.1** | 42.5 <br> **44.6** | 50.1 <br> **53.0** |
| faster rcnn | Res-50 <br> ConTNet-M|  41.5 <br> 36.6  | 241.0 <br> 225.6  | 37.4 <br> **40.0**  | 21.2 <br> **25.4** | 41.0 <br> **43.0** | 48.1 <br> **52.0** |
  
Instance segmentation results on Cityscapes based on Mask-RCNN.
| backbone  | AP<sup>bb</sup> | AP<sub>s</sub><sup>bb</sup> | AP<sub>m</sub><sup>bb</sup> | AP<sub>l</sub><sup>bb</sup> | AP<sup>mk</sup> | AP<sub>s</sub><sup>mk</sup> | AP<sub>m</sub><sup>mk</sup> | AP<sub>l</sub><sup>mk</sup> |
| ----      | ----    | ----  | ----  | ----  | ----  | ----  | ----  | ----  |
| Res-50 <br> ConT-M  | 38.2 <br> **40.5**  | 21.9 <br> **25.1**  | 40.9 <br> **44.4** | 49.5 <br> **52.7** | 34.7 <br> **38.1** | 18.3 <br> **20.9** | 37.4 <br> **41.0** | 47.2 <br> **50.3** |

Semantic segmentation results on cityscapes.
| model | mIOU  |
| ----- | ----  |
|PSP-Res50| 77.12 |
|PSP-ConTM| **78.28** |

## Bib Citing 
```
@article{yan2021contnet,
    title={ConTNet: Why not use convolution and transformer at the same time?},
    author={Haotian Yan and Zhe Li and Weijian Li and Changhu Wang and Ming Wu and Chuang Zhang},
    year={2021},
    journal={arXiv preprint arXiv:2104.13497}
}
```

[1]: https://drive.google.com/drive/folders/1ZXu--Bis3LTYLjf2pkmDtZH0TjuWWamO?usp=sharing
[2]: https://pan.baidu.com/s/1thKK36jTFln1KcAuEkzleg
