# Muppet Audio–Visual Analysis

### Authors
Veneta Grigorova: 12332287
Johannes Oster: 11720343

This project does audio-visual frame classification of muppets. The dataset consists of three Muppet Show episodes in AVI format and frame-level ground truth labels.

### Dataset

We use three episodes of The Muppet Show, extracting all video frames and their audio. Ground truth (2025 Excel files) tells us which characters appear in each frame. We focus on four classes: **Miss Piggy**, **Other Pigs**, **Swedish Chef**, and **Rowlf**.

A frame is kept only if exactly one of these characters is present. After filtering, we have 19,877 labeled frames.

We split the data 80% for training and 20% for testing, using the same split for visual, audio, and combined features. Labels are encoded once to stay consistent.

We do not use a separate validation set, because the classical models we use do not need one, and the MLP handles early stopping automatically.


### Feature Extraction Methods

We extract features separately from the video frames and the audio track, then align them so each frame has both visual and audio information.

#### Visual Features

Each frame is resized to 128×128, converted to grayscale, and processed with:
- HOG – captures edges and shapes
- LBP – captures local texture patterns
- SIFT – averaged keypoint descriptors for character structure

These are combined into one visual feature vector.

#### Audio Features

Audio is aligned to the video using the frame rate. For each frame, we extract:
- MFCCs – the most informative features for voice identity
- Spectral features – centroid, bandwidth, and contrast
- Chroma – pitch-related energy
- Flux – changes in the audio signal
- MFCC + spectral + chroma form the main audio feature vector.

All features are scaled before training to ensure fair comparison across models.

First, we extract these features and train an SVM with them to compare their f1 score. Afterwards, we train three classifiers with the top-performing ones (HOG, SIFT, LBP for visual and MFCC, spectral, chroma for audio). Weaker features (GLCM, flux) were discarded as they did not improve performance.

### Classifiers
We train three classical classifiers on three different feature sets: visual-only, audio-only, and combined (visual + audio). All models use the same 80/20 train–test split and standardized features.

#### Training process:
1. Labels are encoded once for consistency.
2. Visual, audio, and combined datasets are sliced using the same indices.
3. Each classifier is trained on the training portion of its feature set.
4. Performance is measured on the test set using macro F1-score.

#### Models evaluated:
- MLP (shallow neural network with early stopping)
- SVM (RBF kernel)
- Random Forest (200 trees)

#### Results:
- Visual features produce almost perfect classification (F1 ≈ 0.99–1.00).
- Audio features perform moderately well, with MFCCs being the strongest.
- Combined audio–visual models perform similarly to visual-only, since visuals already separate the classes extremely well.

Overall, SVM achieves the best performance, while MLP and Random Forest also perform strongly.


