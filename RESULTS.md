# Skin Lesion Classification — Results

Dataset: [`nodoubttome/skin-cancer9-classesisic`](https://www.kaggle.com/datasets/nodoubttome/skin-cancer9-classesisic) (9-class ISIC skin cancer dataset)

**Setup:** frozen-backbone transfer learning (pretrained ImageNet weights, only the classification head trained), 224×224 input, up to 8 epochs with early stopping (patience 3), batch size 64, T4 GPU.

> **Note:** these results use frozen backbones as a fast baseline. Accuracy across all architectures clustered in the 33–44% range, which indicates generic ImageNet features aren't discriminative enough for these fine-grained lesion classes. A follow-up run with full fine-tuning (differential learning rates: 1e-5 backbone / 1e-3 head, 20 epochs) is expected to improve on these numbers substantially — update this file once that run completes.

## Table 1. Comparison of Transfer Learning Models

| Model | Accuracy (%) | Precision (%) | Recall (%) | F1-Score (%) | AUC (%) |
|---|---:|---:|---:|---:|---:|
| AlexNet | 37.29 | 25.36 | 37.29 | 28.81 | 82.21 |
| VGG16 | 36.44 | 44.71 | 36.44 | 36.05 | 78.60 |
| VGG19 | 34.75 | 25.72 | 34.75 | 27.17 | 77.92 |
| ResNet18 | 40.68 | 49.21 | 40.68 | 34.63 | 82.25 |
| ResNet50 | 33.90 | 43.65 | 33.90 | 27.86 | 82.16 |
| ResNet101 | 39.83 | 48.94 | 39.83 | 33.60 | 81.36 |
| DenseNet121 | **44.07** | **56.72** | **44.07** | **41.56** | **83.90** |
| EfficientNet-B0 | 40.68 | 43.94 | 40.68 | 36.92 | 80.63 |

## Table 2. Comparison of Different Classifiers (Deep Features)

Features extracted from DenseNet121 (best-performing backbone in Table 1).

| Feature Extractor | Classifier | Accuracy (%) | Precision (%) | Recall (%) | F1-Score (%) | AUC (%) |
|---|---|---:|---:|---:|---:|---:|
| Deep Features | Logistic Regression | 47.46 | 54.54 | 47.46 | 45.18 | 84.12 |
| Deep Features | Decision Tree | 25.42 | 25.46 | 25.42 | 23.78 | 56.94 |
| Deep Features | Random Forest | 35.59 | 43.43 | 35.59 | 25.09 | 80.35 |
| Deep Features | K-Nearest Neighbors (KNN) | 39.83 | 42.94 | 39.83 | 38.34 | 75.52 |
| Deep Features | Linear SVM | 45.76 | 39.38 | 45.76 | 38.61 | 85.18 |
| Deep Features | RBF-SVM | **49.15** | 53.84 | **49.15** | **45.20** | **87.08** |
| Deep Features | XGBoost | 42.37 | 42.97 | 42.37 | 37.46 | 78.98 |

## Table 3. Computational Efficiency Comparison

| Model | Parameters (M) | Model Size (MB) | FLOPs (G) | Inference Time (ms) | Accuracy (%) |
|---|---:|---:|---:|---:|---:|
| AlexNet | 57.04 | 217.59 | 0.71 | 2.05 | 37.29 |
| VGG16 | 134.30 | 512.30 | 15.47 | 9.60 | 36.44 |
| VGG19 | 139.61 | 532.56 | 19.63 | 11.42 | 34.75 |
| ResNet18 | 11.18 | 42.69 | 1.82 | **2.06** | 40.68 |
| ResNet50 | 23.53 | 89.95 | 4.13 | 7.52 | 33.90 |
| DenseNet121 | **6.96** | **26.88** | 2.90 | 20.66 | **44.07** |
| EfficientNet-B0 | **4.02** | **15.49** | **0.41** | 7.65 | 40.68 |

## Summary

- **Best overall accuracy:** DenseNet121 (44.07%), also highest across precision/recall/F1/AUC in Table 1.
- **Best deep-feature classifier:** RBF-SVM on DenseNet121 features (49.15% accuracy, 87.08% AUC) — outperformed the end-to-end DenseNet121 classifier itself.
- **Best efficiency trade-off:** EfficientNet-B0 — smallest model (4.02M params, 15.49MB) with accuracy on par with the larger ResNet18/ResNet101.
- **Least efficient:** VGG16/VGG19 — largest parameter counts and FLOPs, without a corresponding accuracy advantage.
