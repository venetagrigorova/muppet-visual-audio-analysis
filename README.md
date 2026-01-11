# Muppet Audio–Visual Analysis

### Authors

- **Veneta Grigorova**: 12332287
- **Johannes Oster**: 11720343

This project performs audio-visual frame classification of Muppet characters. The dataset consists of three _The Muppet Show_ episodes in AVI format with frame-level ground truth labels.

### Timesheet

| Date       | Time Spent | Person            | Activity                                                           |
| ---------- | ---------- | ----------------- | ------------------------------------------------------------------ |
| 2025-10-11 | 2h         | Veneta            | Initial project setup, explored dataset structure and video files  |
| 2025-10-12 | 1h         | Veneta & Johannes | Discussed project                                                  |
| 2025-10-13 | 2h         | Johannes          | Do some initial data exploration (watching videos)                 |
| 2025-10-13 | 0,5h       | Veneta            | Parsed ground truth Excel files, created label mapping functions   |
| 2025-10-17 | 0,5h       | Johannes          | Implemented video frame iteration utility                          |
| 2025-10-18 | 1h         | Johannes          | Explored HOG feature extraction on sample frames                   |
| 2025-10-21 | 2h         | Veneta            | Implemented LBP and GLCM feature extraction functions              |
| 2025-10-22 | 2h         | Veneta            | Added SIFT keypoint detection and descriptor averaging             |
| 2025-10-22 | 1h         | Veneta & Johannes | Reviewed visual feature implementations, fixed grayscale issues    |
| 2025-10-28 | 1h         | Veneta            | Created feature visualization plots for HOG and LBP                |
| 2025-10-29 | 1h         | Veneta & Johannes | Benchmarked visual features, decided to exclude GLCM (F1 < 0.70)   |
| 2025-11-01 | 1h         | Veneta            | Extracted audio tracks from video files using moviepy              |
| 2025-11-01 | 0,5h       | Johannes          | Implemented MFCC extraction aligned to video FPS                   |
| 2025-11-10 | 2h         | Veneta            | Added spectral features (centroid, bandwidth, contrast)            |
| 2025-11-10 | 2h         | Johannes          | Implemented chroma and spectral flux extraction                    |
| 2025-11-14 | 2h         | Veneta & Johannes | Debugged audio-video alignment issues with hop_length              |
| 2025-11-14 | 3h         | Veneta & Johannes | Built full feature extraction pipeline with caching (npz)          |
| 2025-11-18 | 1h         | Veneta            | Created audio feature exploration visualizations                   |
| 2025-11-18 | 1h         | Veneta & Johannes | Ranked audio features, excluded flux due to poor performance       |
| 2025-12-03 | 2h         | Veneta            | Implemented MLP, SVM, and Random Forest classifiers                |
| 2025-12-03 | 4h         | Johannes          | Discovered data leakage, implemented GroupShuffleSplit by video    |
| 2025-12-08 | 4h         | Veneta            | Finalize mid-term hand in                                          |
| 2025-12-14 | 4h         | Veneta            | Evaluated classifiers on visual-only features, analyzed results    |
| 2025-12-18 | 2h         | Johannes          | Trained audio-only models, investigated poor generalization        |
| 2025-12-19 | 0,5h       | Veneta            | Created ROC curve plots and confusion matrix visualizations        |
| 2025-12-21 | 1h         | Veneta            | Wrote discussion section for audio notebook                        |
| 2025-12-23 | 2h         | Johannes          | Implemented combined feature fusion (visual + audio concatenation) |
| 2025-12-23 | 1h         | Veneta            | Analyzed feature importance, identified modality imbalance issue   |
| 2025-12-28 | 1h         | Johannes          | Investigated Chef-Piggy confusion in grayscale visual features     |
| 2026-01-02 | 2h         | Johannes          | Optimized feature caching and notebook execution time              |
| 2026-01-04 | 2h         | Veneta & Johannes | Reviewed combined results, discussed PCA dimensionality reduction  |
| 2026-01-06 | 3h         | Johannes          | Refactored notebooks into modular structure (1_video, 2_audio)     |
| 2026-01-07 | 1h         | Veneta & Johannes | Integrated combined notebook (3_combined) with cached features     |
| 2026-01-08 | 4h         | Veneta            | Wrote detailed discussion sections for all three notebooks         |
| 2026-01-09 | 4h         | Veneta & Johannes | Created final evaluation plots and feature importance analysis     |
| 2026-01-09 | 4h         | Veneta            | Documented critical findings: overfitting, modality imbalance      |
| 2026-01-11 | 2h         | Veneta & Johannes | Finalized `4_summary.ipynb`, cleaned up code, and updated README.  |

### Dataset

We use three episodes of _The Muppet Show_, extracting all video frames and their corresponding audio tracks. Ground truth (Excel files) indicates which characters appear in each frame. We focus on four specific classes:

- **Miss Piggy**
- **Other Pigs**
- **Swedish Chef**
- **Rowlf**

A frame is included in the dataset only if a valid label can be assigned (priority is given to Miss Piggy if multiple characters appear, to resolve ambiguities).

**Data Splitting Strategy:**
We utilize a **Group Shuffle Split** based on `video_id`. This ensures that all frames from a specific video/episode appear _either_ in the training set _or_ the test set, but never both. This prevents data leakage (where the model memorizes specific background scenes rather than learning character features).

- **Training Split:** ~80%
- **Test Split:** ~20%

### Feature Extraction Methods

We extract features separately from video frames and the audio track, aligning them so each frame index corresponds to a specific visual and audio vector.

#### Visual Features

Each frame is resized to 128×128, converted to grayscale, and processed with:

- **HOG (Histogram of Oriented Gradients):** Captures edges and silhouettes (e.g., character outlines).
- **LBP (Local Binary Patterns):** Captures local texture.
- **SIFT (Scale-Invariant Feature Transform):** Averaged keypoint descriptors for structural details.

_Note: GLCM (Gray-Level Co-occurrence Matrix) was evaluated but discarded due to poor performance (F1 < 0.70) compared to HOG (F1 > 0.90)._

#### Audio Features

Audio is aligned to the video frame rate (25 FPS), resulting in ~40ms audio windows per frame.

- **MFCCs:** The primary feature for voice identity (timbre).
- **Spectral Features:** Centroid, bandwidth, and contrast.
- **Chroma:** Pitch-class energy.

_Note: Spectral Flux was evaluated but discarded as it primarily detects onsets rather than character identity._

### Classifiers

We train three classical classifiers on three different feature sets: **Visual-Only**, **Audio-Only**, and **Combined** (Visual + Audio). All features are standardized (zero mean, unit variance) before training.

#### Models Evaluated

1.  **MLP (Multi-Layer Perceptron):** Shallow neural network with early stopping.
2.  **SVM (Support Vector Machine):** RBF kernel for non-linear separation.
3.  **Random Forest:** Ensemble of 200 decision trees.
