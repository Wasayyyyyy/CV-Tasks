# Lab Mid: HOG vs SIFT on Blood-Cell Images

**Abdul Wasay** | Reg. No: FA23-BAI-043 | Seed **43** | HOG cell **16x16** | SIFT grid **4x4**

---

## Headline Results

| Model | Validation Acc | Macro-F1 | Test: Clean | Test: Rotated 20 deg | Test: Noisy (sigma 0.05) |
|---|---|---|---|---|---|
| HOG + SVM | **72.5%** | **0.7175** | **70.0%** | **65.8%** | **26.7%** |
| SIFT + SVM | **74.2%** | **0.7404** | **75.8%** | **72.5%** | **74.2%** |

## Winner: SIFT + SVM is more reliable

- **Rotation:** both models lose little. HOG drops 4.2 points, SIFT drops 3.3 points (a one-image difference).
- **Noise:** HOG collapses from 70.0% to 26.7% (a drop of 43.3 points, near chance for 4 classes). SIFT only drops 1.7 points.

---

## Q1. Data

| Split | Shape | Per class |
|---|---|---|
| Train | (400, 128, 128) | 100 each |
| Val | (120, 128, 128) | 30 each |
| Test | (120, 128, 128) | 30 each |

Classes: eosinophil, monocyte, neutrophil, platelet.

---

## Q2. HOG + SVM

### Feature length: **1764**

- Cells per side = 128 / 16 = 8
- Blocks per side = 8 - 2 + 1 = 7, so 7 x 7 = 49 blocks
- Values per block = 2 x 2 x 9 = 36
- Total = 49 x 36 = **1764**

### Validation: Accuracy **0.7250** | Macro-F1 **0.7175** | Best C = 0.1

All C in {0.1, 1, 10} gave 0.7250 (training data is linearly separable, so C has no effect; ties keep the smallest C).

---

## Q3. SIFT + SVM

### Normal SIFT keypoints (train)

| Average | Min | Max | Images with 0 keypoints |
|---|---|---|---|
| 72.97 | 8 | 178 | 0 |

A different number of keypoints is a problem because an SVM needs same-length vectors: my 8-keypoint image gives 1024 values while the 178-keypoint one gives 22,784.

### Grid SIFT feature length: **2048**

Grid points x 128 = (4 x 4) x 128 = 16 x 128 = **2048**

### Validation: Accuracy **0.7417** | Macro-F1 **0.7404** | Best C = 0.1

All C in {0.1, 1, 10} gave 0.7417.

---

## Q4. Robustness

**Prediction (before testing):** HOG loses more under rotation (rotation shifts edges across its fixed cells and bins). SIFT loses more under noise (its fine descriptors are more sensitive to noise).

| Model | Clean | Rotated | Noisy | Drop (rotation) | Drop (noise) |
|---|---|---|---|---|---|
| HOG + SVM | 0.7000 | 0.6583 | 0.2667 | 0.0417 | 0.4333 |
| SIFT + SVM | 0.7583 | 0.7250 | 0.7417 | 0.0333 | 0.0167 |

**Was I right?** My rotation prediction was correct, but only by one test image (HOG dropped 0.0417, SIFT 0.0333), so the gap is too small to be conclusive. My noise prediction was wrong: HOG fell from 0.7000 to 0.2667 (near chance), while SIFT only fell from 0.7583 to 0.7417. So SIFT was more reliable under noise, and neither model lost much under rotation.

---

## Q5. Short Answers

**Why block normalisation?** Lighting and contrast change gradient sizes, so the same cell can look very different in a bright or dark photo. Normalising each 2x2 block makes the histograms depend on edge shape rather than brightness.

**Why not choose C on the test set?** Choosing C by test score lets the test set influence the model, so its accuracy is no longer an honest estimate on unseen data. Validation is for tuning; test is touched only once, at the end.

**Most confused classes:** eosinophil and monocyte (27 mistakes over both models' validation matrices). Their main difference is colour, and grayscale turns both into similar grey, so they look alike.

### AI log

| Prompt | What I changed | How I checked |
|---|---|---|
| "Solve this HOG vs SIFT blood-cell lab mid using my reg. no. 043 settings." | Verified my settings (seed 43, 16x16 HOG, 4x4 grid), ran everything in Colab, and wrote Q4(c) and Q5(b) from my own results. | Checked class counts (100/30/30), matched hand-calculated lengths (1764, 2048) to the output, and viewed the example grid, HOG pictures, keypoints, and clean/rotated/noisy images. |
