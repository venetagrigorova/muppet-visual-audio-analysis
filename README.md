# Muppet Audio–Visual Analysis

### Authors
Veneta Grigorova: 12332287
Johannes Oster: 11720343

This project does audio-visual frame classification of muppets. The dataset consists of three Muppet Show episodes in AVI format and frame-level ground truth labels.

Fames with multiple target characters are excluded to have single-label classification. This makes it compatible with classical models (SVM, RF, MLP).

First, we extract these features and train an SVM with them to compare their f1 score:

Visual Feautres
- ⁠Histogram of Oriented Gradients
- ⁠SIFT/SURF
- ⁠⁠Local Binary Patterns
- ⁠Gray-Level Co-occurrence Matrix

Audio Features
- ⁠MFCC
- ⁠Spectral Features 
- ⁠⁠Chroma Features
- ⁠⁠Zero Crossing Rate / Spectral Flux

Afterwards, we train three classifiers with the most effective ones.

