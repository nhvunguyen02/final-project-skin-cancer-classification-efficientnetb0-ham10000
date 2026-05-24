# Dataset

This project uses the HAM10000 skin lesion dataset for multiclass skin lesion classification.

## Dataset

HAM10000 contains dermoscopic images of 7 skin lesion classes:

```text
AKIEC
BCC
BKL
DF
MEL
NV
VASC
```

The original dataset contains 10,015 images. In this project, the dataset is balanced using oversampling for minority classes and downsampling for the majority class.

## Expected Local Structure

The dataset is not included in this repository because of its large file size.

Expected local structure:

```text
data/
└── raw/
    └── HAM10000/
        ├── images/
        ├── masks/
        └── GroundTruth.csv
```

Or, if using the original ZIP file:

```text
data/
└── raw/
    └── Kaggle_Skin_Cancer_MNIST_HAM10000.zip
```

## Notes

The notebook downloads the dataset from Google Drive using `gdown`, then extracts it inside the Colab environment.

The dataset file is approximately several gigabytes, so it is excluded from Git tracking.