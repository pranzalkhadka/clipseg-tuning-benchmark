# Benchmarking Fine-Tuning Strategies for ClipSeg for Abdominal Organ Segmentation

## Overview

This repository presents a **comparative analysis of fine-tuning strategies for ClipSeg**, a zero-shot image segmentation model, applied to **abdominal organ segmentation in CT scans**. The project evaluates three fine-tuning approaches:

- **Full Fine-Tuning**
- **Decoder-Only Tuning**
- **Adapter-Based Tuning**

The focus is on understanding the **trade-off between segmentation performance (Dice score)** and **training efficiency** (e.g., computational cost and training time).

ClipSeg is built on top of CLIP (Contrastive Language-Image Pretraining) and performs segmentation using **textual prompts**. The objective of this project is to adapt ClipSeg for the **medical imaging domain**, specifically targeting segmentation of **abdominal organs** like the **liver, spleen, and kidneys** in CT scans.

> This is a personal research effort inspired by [Lüddecke and Ecker (2022)](https://arxiv.org/abs/2204.13406), aimed at exploring and benchmarking various fine-tuning strategies for ClipSeg in a healthcare context.

---

## Project Description

The project explores three distinct fine-tuning strategies for adapting ClipSeg to medical image segmentation:

###  Full Fine-Tuning

- **Description:** Updates **all parameters** of the ClipSeg model — including CLIP’s vision encoder, text encoder, and the decoder.
- **Pros:** Maximizes model flexibility and adaptability.
- **Cons:** Computationally expensive and prone to overfitting on limited medical datasets.

---

### Decoder-Only Tuning

- **Description:** Fine-tunes only the **transformer decoder**, keeping CLIP's vision and text encoders frozen.
- **Pros:** Lower computational cost and preserves CLIP’s pre-trained features.
- **Cons:** Less adaptable to domain-specific visual semantics.

---

###  Adapter-Based Tuning

- **Description:** Inserts lightweight **adapter layers** into the architecture, training only these adapters.
- **Pros:** Efficient in terms of parameters and training time while maintaining reasonable performance.
- **Cons:** May not match the performance of full fine-tuning.

---

##  Data Preparation

The original FLARE 2022 CT scan volumes and corresponding segmentation masks were processed into 2D slices and structured for prompt-based segmentation with CLIPSeg. The key steps included:

###  Dataset Slicing

- Each 3D CT volume (`.nii`) was sliced along the **z-axis** into 2D axial images.
- Corresponding segmentation masks were sliced in the same way and saved as 2D label maps.
- Intensity normalization was applied to the CT images to scale pixel values to [0, 255].

###  Label Mapping
  - `1 → 20` (Liver)
  - `2 → 40` (Right Kidney)
  - ...
  - `13 → 255` (Right Adrenal Gland)

###  Prompt Engineering

- Each organ was associated with the **organ name** to guide the CLIPSeg model.

##  Results after 20 Epochs

| Fine-Tuning Strategy   | Train Dice ↑ | Val Dice ↑ | Training Time ↓ |
|-----------------------|--------------|------------|-----------------|
| Full Fine-Tuning       | 0.7527        | 0.6189      | 1.58 hrs         |
| Decoder-Only Tuning    | 0.7761        | 0.6335      | 2.13 hrs         |
| Adapter-Based Tuning   | 0.6524        | 0.5318      | 2.15 hrs         |



##  Observations

During experimentation, unexpected results were seen:

> **Full fine-tuning** outperformed both decoder-only and adapter-based tuning in terms of **training efficiency** (lower total training time).

This was surprising, as full fine-tuning is generally expected to be more computationally expensive due to the larger number of trainable parameters.

This result highlights that **parameter efficiency (fewer trainable weights)** doesn't always correlate with **training efficiency**.


## References

- Lüddecke, T., & Ecker, A. (2022). *Image Segmentation Using Text and Image Prompts*. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 7086–7096. [arXiv:2112.10003](https://arxiv.org/abs/2112.10003)

- FLARE 2022: Fast and Low-resource Abdominal Organ Segmentation Challenge [https://flare22.grand-challenge.org](https://flare22.grand-challenge.org)