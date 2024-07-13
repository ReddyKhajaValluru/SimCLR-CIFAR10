# SimCLR-CIFAR10
Self-Supervised Contrastive Learning with [SimCLR](https://arxiv.org/pdf/2002.05709) on CIFAR-10 for Image Classification

## Overview
This project explores the application of SimCLR (Simple Framework for Contrastive Learning of Visual Representations) on the CIFAR-10 dataset. The goal is to leverage self-supervised learning (specifically, contrastive representation learning) to enhance image classification performance using limited labeled data.

## Dataset
I used the CIFAR-10 dataset for this project. The dataset contains 50,000 training images and 10,000 test images, all 32x32 color images in 10 classes, with 5,000 training images per class. I used 100% of the training data for pre-training the SimCLR model. For training the baseline model and fine-tuning the SimCLR model, I purposely used only 30% of the labeled training data (i.e., 15,000 samples) to evaluate how contrastive learning benefits scenarios with limited labeled data and abundant unlabeled data.

## Non-SSL Baseline Model
- **Architecture:** ResNet-18 customized for CIFAR-10.
- **Customizations:** Adapted as per Section B.9 of the [SimCLR paper](https://arxiv.org/pdf/2002.05709) (replaced first 7x7 Conv with 3x3 Conv, removed first max pooling).
- **Training Details:**
  - **Training Data:** Utilized only 30% of the CIFAR-10 training data (i.e., 15,000 samples).
  - **Cross-validation:** 5-fold.
  - **Epochs:** 20.
  - **Batch Size:** 256.
  - **Optimizer:** Adam with initial LR of 0.0008.
  - **LR Schedule:** Exponential decay (gamma 0.9 for first 15 epochs, 0.6 after that).
  - **Loss Function:** Cross Entropy Loss.
- **Performance:** Validation accuracy ~76%, final loss ~0.46 after 20 epochs.

## SimCLR Model
### SimCLR Pretraining
- **Architecture:** Encoder + Projection Head
- **Encoder:** Utilized the encoder part of the Baseline Model (i.e., without final FC layers).
- **Projection Head:** A 2-layer MLP to project image features into a 128-dimensional latent space.
- **Training Details:**
  - **Training Data:** Entire CIFAR-10 training dataset.
  - **Epochs:** 50.
  - **Batch Size:** 256.
  - **Optimizer:** Adam with LR 0.001 and weight decay \(10^{-6}\).
  - **LR Schedule:** Step decay (step size 10 epochs, gamma 0.6).
  - **Loss Function:** NT-Xent Loss.
- **Objective:** Learn meaningful representations from unlabeled data.


### SimCLR Fine-tuning
- **Procedure:** After the pre-training phase, take the trained encoder and attach a classifier to it for fine-tuning on the image classification task.
- **Training Details:** Mirrored non-SSL baseline model training.
  - **Training Data:** Utilized only 30% of the CIFAR-10 training data (i.e., 15,000 samples).
  - **Cross-validation:** 5-fold.
  - **Epochs, Batch Size, Optimizer, LR, LR Schedule, Loss Function:** Same as non-SSL baseline model training.
- **Performance:** Validation accuracy ~82%, final loss ~0.22 after 20 epochs.

## Results and Conclusion
- **Performance Comparison:** 
  - The baseline model achieved approximately 76% validation accuracy and a final loss of 0.46 after 20 epochs. In contrast, SimCLR achieved 76% validation accuracy and the same final loss of 0.46 in just 6 epochs, demonstrating its faster convergence and initial performance advantage over the baseline.
  - After 20 epochs, SimCLR further improved to approximately 82% validation accuracy and reduced the loss to 0.22. These results underscore the effectiveness of contrastive learning in enhancing performance in scenarios with limited labeled data.
