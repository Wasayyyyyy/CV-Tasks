# Lab Mid Exam: HOG vs SIFT on Blood-Cell Images

**Name:** Abdul Wasay | **Reg. No:** XXX-043 | **Course:** Computer Vision Lab

Executed notebook: `043_AbdulWasay_LabMid.ipynb` (all outputs below are copied from it).

## My personal settings (reg. no. 043, last digit 3)

| Setting | Rule | My value |
|---|---|---|
| Random seed | last two digits | **43** |
| HOG cell size | last digit odd | **16 x 16** |
| SIFT grid | last digit 0-4 | **4 x 4 (16 keypoints)** |

---

## Q1. Data Setup

Load BloodMNIST (size 64), keep classes eosinophil (1), monocyte (5), neutrophil (6), platelet (7), pick 100/30/30 images per class with seed 43, convert to grayscale, resize to 128 x 128.

```
Train shape: (400, 128, 128)  counts: {'eosinophil': 100, 'monocyte': 100, 'neutrophil': 100, 'platelet': 100}
Val   shape: (120, 128, 128)  counts: {'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}
Test  shape: (120, 128, 128)  counts: {'eosinophil': 30, 'monocyte': 30, 'neutrophil': 30, 'platelet': 30}
```

---

## Q2. HOG + SVM

### Q2(a) HOG features

```
HOG feature length: 1764
HOG train matrix: (400, 1764)  val matrix: (120, 1764)
```

**Feature length by hand (cell 16 x 16):**
- Cells per side = 128 / 16 = **8**, so 8 x 8 cells
- A block is 2 x 2 cells and slides one cell at a time, so blocks per side = 8 - 2 + 1 = **7**, giving 7 x 7 = **49 blocks**
- Values per block = 2 x 2 cells x 9 orientations = **36**
- Total = 49 x 36 = **1764** (matches the printed length)

### Q2(b) HOG pictures (one image per class)

### Q2(c) SVM, C chosen on validation

```
HOG + SVM:
  C = 0.1  -> validation accuracy = 0.7250
  C = 1    -> validation accuracy = 0.7250
  C = 10   -> validation accuracy = 0.7250
Best C: 0.1
HOG + SVM | Validation accuracy: 0.7250 | Macro-F1: 0.7175
```

All three C values give the same accuracy because the standardised training data is linearly separable, so C has no effect. Ties keep the smallest C (0.1).

---

## Q3. SIFT + SVM

### Q3(a) Normal SIFT detection (training images)

```
Average keypoints per image: 72.97
Minimum keypoints: 8
Maximum keypoints: 178
Images with zero keypoints: 0
```

**Why is a different number of keypoints a problem?** An SVM needs every image to be a feature vector of the same length, but each keypoint adds 128 values, so my image with 8 keypoints gives 1024 values while the one with 178 gives 22,784.

### Q3(b) Fixed grid of keypoints

```
Grid centres: [16.0, 48.0, 80.0, 112.0] -> number of keypoints: 16
SIFT feature length: 2048
SIFT train matrix: (400, 2048)  val matrix: (120, 2048)
```

**Feature length by hand:** grid points x 128 = (4 x 4) x 128 = 16 x 128 = **2048** (matches the printed length). Every image now has exactly 16 keypoints, so every image gives a 2048-long vector.

### Q3(c) SVM, C chosen on validation

```
SIFT + SVM:
  C = 0.1  -> validation accuracy = 0.7417
  C = 1    -> validation accuracy = 0.7417
  C = 10   -> validation accuracy = 0.7417
Best C: 0.1
SIFT + SVM | Validation accuracy: 0.7417 | Macro-F1: 0.7404
```

---

## Q4. Robustness

### Q4(a) Prediction (written before testing)

- **Rotation:** HOG will lose more accuracy because rotation shifts edges across its fixed cells and orientation bins.
- **Noise:** SIFT will lose more accuracy because its finer descriptors are more sensitive to random noise, while HOG's block normalisation helps reduce its effects.

### Q4(b) Test results

Both models are frozen (no retraining). The change is applied to the image first, then features are extracted.

| Model | Clean accuracy | Rotated accuracy | Noisy accuracy |
|---|---|---|---|
| HOG + SVM | 0.7000 | 0.6583 | 0.2667 |
| SIFT + SVM | 0.7583 | 0.7250 | 0.7417 |

```
HOG + SVM: drop under rotation = 0.0417, drop under noise = 0.4333
SIFT + SVM: drop under rotation = 0.0333, drop under noise = 0.0167
```

### Q4(c) Was my prediction correct?

My rotation prediction was correct, but only by one test image (HOG dropped 0.0417, SIFT 0.0333), so the gap is too small to be conclusive. My noise prediction was wrong: HOG fell from 0.7000 to 0.2667 (near chance for 4 classes), while SIFT only fell from 0.7583 to 0.7417. So SIFT was more reliable under noise, and neither model lost much under rotation.

---

## Q5. Short Answers and AI Log

### Q5(a) Theory

**(a) Why does HOG use block normalisation?** Lighting and contrast change the size of gradients across an image, so the same cell can look very different in a bright or dark photo. Normalising each 2 x 2 block makes the histograms depend on the *shape* of the edges rather than how bright they are.

**(b) Why is it wrong to choose C using the test set?** If C is picked because it scores best on the test set, the test set has helped train the model, so its accuracy is no longer an honest estimate on unseen data. The validation set is for tuning; the test set must be touched only once, at the end.

### Q5(b) Most confused classes

```
Most confused pair: eosinophil <-> monocyte  (27 mistakes in total)
```

The models confused *eosinophil and monocyte* the most (27 mistakes, summed over both models' validation confusion matrices). Their main difference is colour (orange-red granules vs blue-grey cytoplasm), and grayscale turns both into similar grey, so they look alike. (The colour explanation is from domain knowledge, not from a notebook output.)

### Q5(c) AI log

| Prompt | What I changed | How I checked |
|---|---|---|
| "Solve this HOG vs SIFT blood-cell lab mid using my reg. no. 043 settings." | Verified my settings (seed 43, 16x16 HOG, 4x4 grid), ran everything in Colab, and wrote Q4(c) and Q5(b) from my own results. | Checked class counts (100/30/30), matched hand-calculated lengths (1764, 2048) to the output, and viewed the example grid, HOG pictures, keypoints, and clean/rotated/noisy images. |
