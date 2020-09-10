# Scanpath Prediction Using Inverse Reinforcement Learning

Offical PyTorch implementation of the paper [Predicting Goal-directed Human Attention Using Inverse Reinforcement Learning](http://openaccess.thecvf.com/content_CVPR_2020/html/Yang_Predicting_Goal-Directed_Human_Attention_Using_Inverse_Reinforcement_Learning_CVPR_2020_paper.html) (CVPR2020, oral)

We propose the first inverse reinforcement learning (IRL) model to learn the internal reward function and policy used by humans during visual search. The viewer's internal belief states were modeled as dynamic contextual belief maps of object locations. These maps were learned by IRL and then used to predict behavioral scanpaths for multiple target categories. To train and evaluate our IRL model we created COCO-Search18, which is now the largest dataset of high-quality search fixations in existence. COCO-Search18 has 10 participants searching for each of 18 target-object categories in 6202 images, making about 300,000 goal-directed fixations. When trained and evaluated on COCO-Search18, the IRL model outperformed baseline models in predicting search fixation scanpaths, both in terms of similarity to human search behavior and search efficiency.

If you are using this work, please cite:
```bibtex
@InProceedings{Yang_2020_CVPR_predicting,
author = {Yang, Zhibo and Huang, Lihan and Chen, Yupei and Wei, Zijun and Ahn, Seoyoung and Samaras, Dimitris and Zelinsky, Gregory and and Hoai, Minh},
title = {Predicting Goal-directed Human Attention Using Inverse Reinforcement Learning},
booktitle = {The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
month = {June},
year = {2020}
}
```

## Scripts
- Train a model with
    ```
    python train.py <hparams> <dataset_root> [--cuda=<id>]
    ```
- Model evaluation
    - We are working with [MIT/Tuebingen Saliency Benchmark](https://saliency.tuebingen.ai/datasets/COCO-Search18/index_new.html) to set up an evaluation server, stay tuned!
    
## Data Preparation
The dataset consists of two parts: image stimuli and fixations. For computational efficiency, we pre-compute the low- and high-resolution belief maps using the pretrained Panoptic FPN (with ResNet50 backbone) from [Detectron2](https://github.com/facebookresearch/detectron2).
For each image, we extract 134 beliefs maps for both low- and high-resolution and resize them to 20x32. Hence, for each image, we have two 134x20x32 tensors. Please refer to the [paper](https://arxiv.org/pdf/2005.14310.pdf) for more details.
Fixations come in the form of invidual scanpaths which mainly consists of a list of (x, y) locations in the image coordinate (see below for an example). Note that in the raw fixations there might be fixations out of the image boundaries, we remove them from the scanpaths.

The typical `<dataset_root>` should be structured as follows
```
<dataset_root>
    -- bbox_annos.npy                                # bounding box annotation for each image (available at COCO)
    -- coco_search18_fixations_TP_train.json         # train split of human scanpaths (ground-truth)
    -- coco_search18_fixations_TP_validation.json    # validation split of human scanpaths (ground-truth)
    -- ./DCBs
        -- ./HR                                      # high-resolution belief maps of each input image (pre-computed)
        -- ./LR                                      # low-resolution belief maps of each input image (pre-computed)
```
The `.json` file is a list of human scanpaths each of which is a `dict` object formated as follows
```
{
     'name': '000000400966.jpg',            # image name
     'subject': 2,                          # subject id (10 subjects from 1~10 in total)
     'task': 'microwave',                   # target name (18 target categories in total)
     'condition': 'present',                # target-present or target-absent
     'bbox': [67, 114, 78, 42],             # bounding box of the target object in the image
     'X': array([245.54666667, ...]),       # x-axis of each fixation
     'Y': array([128.03047619, ...]),       # y-axis of each fixation
     'T': array([190,  63, 180, 543]),      # duration of each fixation
     'length': 4,                           # length of the scanpath (i.e., number of fixations)
     'fixOnTarget': True,                   # if the scanpath lands on the target object
     'correct': 1,                          # 1 if the subject correctly located the target; 0 otherwise
     'split': 'train'                       # split of the image {'train', 'valid', 'test'}
 }
```
A sample `<dataset_root>` folder used and the computed belief maps in this paper can be found at this [link](https://drive.google.com/open?id=1spD2_Eya5S5zOBO3NKILlAjMEC3_gKWc). Note that in this paper we resaled the images to 512x320 as well as the fixation locations. The original COCO-Search18 dataset was collected on a 1680x1050 display.

## COCO-Search18 Dataset
![coco-search18](./coco_search18_logo.png)

**COCO-Search18** dataset is available at https://sites.google.com/view/cocosearch/home. COCO-Search18 is also part of the [MIT/Tuebingen Saliency Benchmark](https://saliency.tuebingen.ai/datasets/COCO-Search18/index_new.html).
