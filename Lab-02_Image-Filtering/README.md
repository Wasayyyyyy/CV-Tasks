# Lab Task 02 — Effect of Image Filtering on Skin-Lesion Classification

## Objective
Investigate how spatial-domain image-processing filters affect the performance of pretrained deep-learning models for skin-lesion classification.

## Dataset
HAM10000. Subset used: 300 images/class cap (7 classes: akiec, bcc, bkl, df, mel, nv, vasc; classes with fewer than 300 available used their full count). 80/10/10 stratified split.

## Models
- Model 1: **DenseNet121**
- Model 2: **ResNet18**
- Model 3: **EfficientNet-B0**

## Required Experimental Results

| Model | Filter | Accuracy | Precision | Recall | F1-score | Macro-F1 | AUC |
|---|---|---:|---:|---:|---:|---:|---:|
| DenseNet121 | No Filter | 71.59 | 71.77 | 71.59 | 71.19 | 73.20 | 93.84 |
| DenseNet121 | Average | 67.05 | 66.92 | 67.05 | 66.52 | 68.78 | 91.92 |
| DenseNet121 | Gaussian | 69.32 | 69.45 | 69.32 | 69.15 | 72.20 | 92.57 |
| DenseNet121 | Median | 66.48 | 66.15 | 66.48 | 66.04 | 69.50 | 91.33 |
| DenseNet121 | Sharpening | 68.18 | 68.05 | 68.18 | 67.98 | 69.23 | 94.03 |
| DenseNet121 | Sobel | 46.59 | 50.51 | 46.59 | 46.81 | 46.71 | 81.30 |
| ResNet18 | No Filter | 69.89 | 69.95 | 69.89 | 69.45 | 70.02 | 93.14 |
| ResNet18 | Average | 71.59 | 72.73 | 71.59 | 71.28 | 72.02 | 93.42 |
| ResNet18 | Gaussian | 72.73 | 72.37 | 72.73 | 72.38 | 72.91 | 93.71 |
| ResNet18 | Median | 71.02 | 71.42 | 71.02 | 71.00 | 71.99 | 93.52 |
| ResNet18 | Sharpening | 71.59 | 72.15 | 71.59 | 71.07 | 71.58 | 93.97 |
| ResNet18 | Sobel | 48.86 | 50.77 | 48.86 | 48.48 | 48.07 | 81.20 |
| EfficientNet-B0 | No Filter | 68.75 | 68.94 | 68.75 | 68.34 | 69.19 | 93.26 |
| EfficientNet-B0 | Average | 67.05 | 66.70 | 67.05 | 66.67 | 67.69 | 91.59 |
| EfficientNet-B0 | Gaussian | 67.05 | 67.37 | 67.05 | 66.69 | 67.36 | 93.27 |
| EfficientNet-B0 | Median | 63.64 | 63.95 | 63.64 | 63.50 | 64.26 | 92.67 |
| EfficientNet-B0 | Sharpening | 68.18 | 68.56 | 68.18 | 67.91 | 69.16 | 92.91 |
| EfficientNet-B0 | Sobel | 52.84 | 55.61 | 52.84 | 52.87 | 50.88 | 84.48 |

Full CSVs: `lab02_outputs/tables/`

## Additional Analysis
- HAM10000 class distribution → `figures/class_distribution.png`
- Original and filtered image examples → `figures/filter_examples.png`
- Confusion matrices → `figures/cm_<model>_<filter>.png`
- Training/validation accuracy curves → `figures/curves_<model>_<filter>.png`
- Training/validation loss curves → `figures/curves_<model>_<filter>.png`
- Per-class precision, recall, F1-score → `tables/lab02_per_class_metrics.csv`
- Macro-F1, balanced accuracy → main table above / `tables/lab02_results.csv`
- AUC/ROC → main table above; `figures/roc_<model>_<filter>.png`

## Questions to Answer

**Which three pretrained models performed best in Lab 1?**
DenseNet121, ResNet18, EfficientNet-B0.

**How does filtering affect each model?**
DenseNet121: every filter lowers accuracy vs. baseline. ResNet18: every filter except Sobel improves on baseline. EfficientNet-B0: mostly flat-to-negative, Sharpening ≈ baseline.

**Which filter produces the greatest change?**
Sobel — largest drop for all three models (15–25 accuracy points).

**Is the effect consistent across models?**
No. Smoothing/sharpening hurt DenseNet121 and EfficientNet-B0 but help ResNet18; Sobel hurts all three.

**Does filtering improve or decrease macro-F1 and balanced accuracy?**
Decreases for DenseNet121 and EfficientNet-B0 (Sharpening ≈ neutral); increases for ResNet18 (except Sobel, which decreases it sharply for all three).

**Which lesion classes are most affected?**
See `tables/lab02_per_class_metrics.csv`, comparing No Filter vs. Sobel per class. *(Fill in specific class names from your run.)*

**Why might smoothing remove useful lesion texture/morphology?**
Average/Gaussian/Median are low-pass filters that suppress the high-frequency detail (pigment texture, border irregularity) diagnosis depends on.

**Why might sharpening or edge detection help or hurt?**
Sharpening enhances existing detail without discarding information, which can help. Sobel discards color and texture entirely, keeping only edges — removing cues the models rely on, which is why it hurts.

**Difference between convolution and correlation?**
Convolution flips the kernel 180° before sliding it over the input; correlation does not. Equivalent for symmetric kernels — which is why deep learning frameworks implement "convolution" layers as correlation, since kernel weights are learned rather than fixed.

**Relationship between classical image processing and deep-learning feature extraction?**
CNNs learn their own filters end-to-end, functioning like automatic hand-crafted filters. Results show this isn't architecture-independent: ResNet18 tolerates/benefits from smoothing, while DenseNet121 and EfficientNet-B0 rely more on fine texture that smoothing destroys — so classical preprocessing and learned features aren't interchangeable; their interaction is model-specific.

## Code Submission
Notebook: `Lab02_Image_Filtering_Effect.ipynb`, organized into Dataset preparation, Model loading, Baseline experiment, Image filtering, Training, Evaluation, Visualization, and Comparative analysis sections. Run instructions: `Lab02_README.md`.
