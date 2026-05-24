# Skin Cancer Classification with EfficientNet-B0 on HAM10000

This repository contains an academic final project for the Image Processing course. The project explores multiclass skin lesion classification using EfficientNet-B0 on the HAM10000 dataset.

The system classifies dermoscopic skin lesion images into 7 diagnostic classes:

```text
AKIEC
BCC
BKL
DF
MEL
NV
VASC
```

This is a student project developed for learning, experimentation, and model evaluation. The implementation focuses on building a complete medical image classification pipeline rather than a production-ready clinical diagnosis system.

## Overview

Skin cancer is a common and potentially dangerous disease if not detected early. Traditional diagnosis depends heavily on clinical expertise and may be difficult to scale for large screening workflows.

This project applies deep learning to classify skin lesion images using a lightweight but effective convolutional neural network architecture.

The main model is EfficientNet-B0, combined with:

- Transfer Learning
- Fine-tuning
- Data augmentation
- Class balancing
- Test-Time Augmentation (TTA)
- MC Dropout for uncertainty-aware inference

## Dataset

The project uses the HAM10000 dataset.

The original dataset contains:

- 10,015 dermoscopic images
- 7 skin lesion classes
- Strong class imbalance, especially the majority class NV

The 7 classes are:

| Class | Meaning |
|---|---|
| AKIEC | Actinic keratoses and intraepithelial carcinoma |
| BCC | Basal cell carcinoma |
| BKL | Benign keratosis-like lesions |
| DF | Dermatofibroma |
| MEL | Melanoma |
| NV | Melanocytic nevi |
| VASC | Vascular lesions |

## Data Balancing

The original HAM10000 dataset is highly imbalanced. The NV class dominates the dataset, while classes such as DF and VASC contain far fewer samples.

To reduce imbalance, this project applies:

- Oversampling for minority classes: AKIEC, BCC, DF, VASC
- Downsampling for the majority class: NV
- Keeping BKL and MEL near their original counts

After balancing, the dataset contains approximately:

```text
7,512 images
```

The balanced dataset is split into:

| Split | Ratio |
|---|---:|
| Training | 70% |
| Validation | 15% |
| Test | 15% |

## Preprocessing

The preprocessing pipeline includes:

- Reading image paths from metadata
- Mapping each image to its diagnostic label
- Resizing images to `224 × 224`
- Converting images to RGB tensors
- Applying EfficientNet preprocessing
- Building efficient `tf.data.Dataset` pipelines
- Using batching and prefetching for faster training

## Model

The main model is EfficientNet-B0 pretrained on ImageNet.

EfficientNet-B0 was selected because it provides a good balance between classification performance and computational efficiency.

The model architecture includes:

- EfficientNet-B0 backbone
- Random augmentation layers
- Global Average Pooling
- Batch Normalization
- Dropout
- Dense softmax output layer for 7 classes

## Training Strategy

The model is trained in two phases.

### Phase 1: Transfer Learning

In the first phase, the EfficientNet-B0 backbone is frozen. Only the custom classification head is trained.

Purpose:

- Preserve general visual features learned from ImageNet
- Allow the new classification head to adapt to HAM10000
- Reduce instability during early training

### Phase 2: Fine-tuning

In the second phase, the full model is unfrozen and trained with a smaller learning rate.

Purpose:

- Adapt deeper visual features to dermoscopic images
- Improve class-specific representation
- Increase validation and test accuracy

## Inference Strategy

The project uses enhanced inference to improve stability and reliability.

### Test-Time Augmentation

Instead of predicting once on the original image, the model predicts on multiple augmented versions of the same image, then averages the predicted probabilities.

This reduces random prediction variance.

### MC Dropout

Dropout is kept active during inference to generate multiple probabilistic predictions.

This helps estimate model uncertainty. If predictions vary strongly across runs, the model is less confident.

### Final Prediction

The final output is computed by averaging the probability distributions from multiple inference passes.

The predicted class is the one with the highest mean probability.

## Results

### Training Performance

During the frozen-base stage, validation accuracy improved from approximately 58% to around 70%.

After fine-tuning, validation accuracy increased to approximately 82%.

### Test Performance

EfficientNet-B0 with TTA achieved the best reported test performance among the evaluated models.

| Model | Accuracy without TTA | Accuracy with TTA |
|---|---:|---:|
| DenseNet121 | 78.00% | 81.21% |
| InceptionV3 | 81.00% | 82.33% |
| MobileNetV2 | 82.27% | 84.50% |
| ResNet50 | 87.22% | 85.00% |
| EfficientNet-B0 | 86.69% | 87.49% |

### EfficientNet-B0 + TTA Classification Report

| Class | Precision | Recall | F1-score |
|---|---:|---:|---:|
| MEL | 0.86 | 0.57 | 0.69 |
| NV | 0.74 | 0.92 | 0.82 |
| BCC | 0.89 | 0.94 | 0.92 |
| AKIEC | 0.97 | 0.97 | 0.97 |
| BKL | 0.78 | 0.75 | 0.76 |
| DF | 0.99 | 1.00 | 1.00 |
| VASC | 0.99 | 1.00 | 0.99 |

Overall test accuracy:

```text
87.49%
```

## Error Analysis

The confusion matrix shows that most predictions are concentrated on the main diagonal, indicating strong classification performance.

However, some class pairs remain difficult:

- MEL ↔ NV: high visual similarity between melanoma and melanocytic nevi
- AKIEC ↔ BCC: both are epithelial skin lesions and can share visual patterns

The ROC-AUC analysis shows strong class separability overall, with weaker performance for MEL compared with easier classes such as DF and VASC.

## Web Demo

The project includes a simple web-based clinical decision support demo using Streamlit.

The demo is designed to:

- Upload a skin lesion image
- Predict the lesion class
- Display class probabilities
- Show the confidence score
- Provide a risk warning level

This demo is for educational purposes only and must not be used as a real medical diagnosis system.

## Limitations

This project is an academic prototype and has several limitations:

- The dataset is limited to HAM10000 and may not generalize to all clinical settings.
- Some lesion classes remain visually difficult to distinguish.
- The model may still be affected by dataset bias.
- The system has not been clinically validated.
- The web demo is only a proof of concept.
- Hyperparameter tuning was not exhaustive.
- Medical decisions should always be made by qualified healthcare professionals.

## Future Work

Potential improvements include:

- Using larger and more diverse dermatology datasets
- Applying stronger data augmentation strategies
- Adding Grad-CAM for visual explainability
- Improving uncertainty estimation
- Testing newer lightweight architectures
- Exploring super-resolution preprocessing
- Deploying a more complete web application
- Performing clinical validation with expert review

## Repository Structure

```text
skin-cancer-classification-efficientnetb0-ham10000/
├── app/
├── assets/
├── data/
│   └── README.md
├── notebooks/
│   ├── skin_cancer_efficientnetb0_ham10000.ipynb
│   └── Cop_of_SkinCancer_EfficientNetB0_HAM10000.pdf
├── reports/
│   ├── PTXLA_BaoCao_1 (4).pdf
│   └── Idea - PTXLA.pdf
├── slides/
│   └── slidePTXLA_final.pdf
├── README.md
├── requirements.txt
└── .gitignore
```

## How to Run

Clone the repository:

```bash
git clone https://github.com/nhvunguyen02/skin-cancer-classification-efficientnetb0-ham10000.git
cd skin-cancer-classification-efficientnetb0-ham10000
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Open the notebook:

```bash
jupyter notebook notebooks/skin_cancer_efficientnetb0_ham10000.ipynb
```

Or upload the notebook to Google Colab and run it there.

## Reports and Slides

The final report, idea document, notebook PDF, and presentation slides are included in this repository.

## Authors

- Nguyen Hoang Vu Nguyen
- Nguyen Thuan Phat
- Nguyen Ngoc Duc