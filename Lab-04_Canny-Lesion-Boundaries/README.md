**FA23-BAI-043** · Skin Lesion Boundary Detection Using Canny Edge Detection · Dataset: HAM10000 (first 5 images, 450×600 px each)

## Table 1: Lesion metrics (Gaussian filter + Canny 100–200)

| Image | Filename | Area (px) | Perimeter (px) |
|---|---|---|---|
| Image 1 | ISIC_0028933.jpg | 2232.00 | 1342.79 |
| Image 2 | ISIC_0028394.jpg | 0.00 | 0.00 |
| Image 3 | ISIC_0027799.jpg | 265.50 | 197.46 |
| Image 4 | ISIC_0028100.jpg | 121.50 | 67.98 |
| Image 5 | ISIC_0027960.jpg | 0.00 | 0.00 |

## Summary statistics (5 images)

| Statistic | Area (px) | Perimeter (px) |
|---|---|---|
| Mean | 523.80 | 321.65 |
| Median | 121.50 | 67.98 |
| Std Dev | 859.68 | 515.64 |
| Min | 0.00 | 0.00 |
| Max | 2232.00 | 1342.79 |

## Table 2: Filter and edge-method comparison (Image 1)

| Filter | Method | Edge pixels | Edge % of image | Area | Perimeter |
|---|---|---|---|---|---|
| Gaussian | Canny | 2677 | 0.99% | 2232 | 1343 |
| Gaussian | Sobel | 248254 | 91.95% | 268951 | 2096 |
| Median | Canny | 3468 | 1.28% | 2858 | 1451 |
| Median | Sobel | 235295 | 87.15% | 268822 | 2107 |
| Average | Canny | 358 | 0.13% | 142 | 567 |
| Average | Sobel | 239525 | 88.71% | 268719 | 2132 |

## Canny threshold comparison (Image 1, Gaussian filter)

| Threshold | Edge pixels |
|---|---|
| 50–100 | 9479 |
| 100–200 | 2677 |
| 150–250 | 585 |

Higher thresholds keep fewer edges. Plots for all five images are in the figures below.

## Table 3: Method performance evaluation (Image 1)

| Method | Noise handling | Edge quality | Boundary detection | Overall |
|---|---|---|---|---|
| Original + Sobel | None | Good | Excellent | Excellent |
| Original + Canny | None | Excellent | Excellent | Excellent |
| Average + Sobel | Fair | Good | Excellent | Excellent |
| Average + Canny | Fair | Excellent | Excellent | Poor |
| Gaussian + Sobel | Good | Good | Excellent | Excellent |
| Gaussian + Canny | Good | Excellent | Excellent | Excellent |
| Median + Sobel | Good | Good | Excellent | Excellent |
| Median + Canny | Good | Excellent | Excellent | Excellent |

*These ratings are assigned by fixed rules in the code (filter name and contour area), not measured against ground truth.*

## Observations from the figures

- In Images 1 and 3, the detected "boundary" follows a **hair**, not the lesion, because the lesion edge is soft and the strongest edges are hairs.
- In Images 2 and 5, Canny (100–200) produced no usable contour, so area and perimeter are 0.
- Sobel contours cover about 99.6% of the 270,000 px image (area ≈ 268,900), so they trace the whole frame rather than the lesion.
- No ground-truth masks were used, so these numbers show what the pipeline detected, not segmentation accuracy.

## Figures

### Full pipeline (original → grayscale → Gaussian → Canny → boundary)

| | |
|---|---|
| ![Image 1 pipeline](results/image1_pipeline.png) | ![Image 2 pipeline](results/image2_pipeline.png) |
| ![Image 3 pipeline](results/image3_pipeline.png) | ![Image 4 pipeline](results/image4_pipeline.png) |
| ![Image 5 pipeline](results/image5_pipeline.png) | |

### Canny threshold comparison (50–100 / 100–200 / 150–250)

![Image 1 thresholds](results/image1_threshold_comparison.png)
![Image 2 thresholds](results/image2_threshold_comparison.png)
![Image 3 thresholds](results/image3_threshold_comparison.png)
![Image 4 thresholds](results/image4_threshold_comparison.png)
![Image 5 thresholds](results/image5_threshold_comparison.png)
