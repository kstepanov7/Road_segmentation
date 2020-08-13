# Roads segmentation with Unet

This repository contains code related to the project of automatic road extraction from satellite images.

# Setup and Dependencies
* Python3
* Numpy
* PyTorch
* Weights & Biases [(https://pypi.org/project/wandb/)](https://pypi.org/project/wandb/)
* Imagecodecs [(https://pypi.org/project/imagecodecs/)](https://pypi.org/project/imagecodecs/)

Setup:
Dowload dataset and weights from [https://drive.google.com/drive/folders/1cngBCqf0ag7rx9Rtehkc_1J1vbZQAeE3?usp=sharing](https://drive.google.com/drive/folders/1cngBCqf0ag7rx9Rtehkc_1J1vbZQAeE3?usp=sharing) and change data path.


# Content

1. Folder 'models' contains UNet model target functions, used in this work.
2. Folder 'data_preparation' contains two Jupyter notebooks - 'maploader.ipynb' and 'VectorToRaster.ipynb' showing how data for the custom dataset was collected and processed.
3. 'dataset_wrapper.py' - PyTorch Dataset wrappers around the data.
4. 'train_deep.py' - training and testing code for the UNet model.
5. 'UNet_exp.ipynb' - experiments for Unet with different losses.

# Results
During experiments with different loss functions, it appears that using a per-pixel loss (such as Jaccard or Dice loss) between the output and ground-truth images can't provide predicted mask with a structure similar to road structure. For example, it can't extract road surface in situations when it blocked with the tree or shadow. Using perceptual loss with pre-trained VGG-16 model can help us to extract high-level features from both ground-truth and predicted images and minimize the difference between the content of these images. As the result we get more smooth and continuous road mask. 

Comparison of metrics is provided in the table below.

|               | f1   | recall | precision | IoU  | APLS |
|---------------|------|--------|-----------|------|------|
| Cross Entropy | 0,13 | 0,32   | 0,15      | 0,13 | -    |
| Jaccard loss  | 0,84 | 0,81   | 0,88      | 0,59 | 0,23 |
| Dice loss     | 0,83 | 0,80   | 0,88      | 0,56 | -    |
| Focal loss    | 0,80 | 0,77   | 0,86      | 0,51 | -    |
| DiceBCE       | 0,82 | 0,79   | 0,88      | 0,55 | -    |
| LovaszSoftmax | 0,11 | 0,24   | 0,13      | 0,10 | -    |
| DiceFocal     | 0,81 | 0,78   | 0,86      | 0,52 | -    |
| VGG+IoU       | 0,86 | 0,84   | 0,88      | 0,63 | 0,41 |

[APLS metric](https://medium.com/the-downlinq/spacenet-road-detection-and-routing-challenge-part-i-d4f59d55bfce), which reflects logical topology and physical topology of predicted masks by comparing the difference between paths lengths on ground truth and predicted images, also was calculated.
 
![alt text](https://github.com/kstepanov7/Roads-segmetation-with-Unet/blob/master/images/result_vgg.jpg?raw=true)
