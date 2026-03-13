# ODM Orthomosaic Integration Test

This repository contains scripts and dataset structures used to perform **integration testing of orthomosaic outputs** produced by different versions of OpenDroneMap (ODM).

The goal is to verify that **orthophoto outputs remain consistent across ODM releases** by automatically comparing the generated `odm_orthophoto.tif` files.

This helps detect unintended algorithmic or pipeline changes between versions.

---

# Overview

When upgrading ODM versions, subtle changes in reconstruction algorithms may lead to variations in the generated orthomosaic.

This repository provides:

- A **standardized dataset structure**
- A **Python comparison script**
- A **method to compare orthophotos generated from different ODM versions**

The comparison script uses two complementary image similarity approaches:

1. **ORB feature matching (OpenCV)**  
   Measures similarity by comparing keypoint features between orthophotos.

2. **Structural Similarity Index (SSIM)**  
   Measures perceptual similarity based on luminance, contrast, and structure.

Using both approaches provides a **robust indicator of whether orthomosaics differ significantly between versions**.

---

# Repository Structure

Example project layout:

```
oats/
│
├── datasets/
│   ├── brighton_masked/
│   ├── brighton_no_exif/
│   ├── brighton_spaces/
│   └── brighton_split_merge/
│
├── results/
│   ├── 3_0_0/
│   │   └── <dataset>/odm_orthophoto/odm_orthophoto.tif
│   │
│   └── 3_5_6/
│       └── <dataset>/odm_orthophoto/odm_orthophoto.tif
│
└── compare_orthophotos.py
```

Each ODM version processes the **same datasets**, producing orthomosaics that are later compared.

---

# Workflow

## 1. Prepare datasets

Place test datasets in the `datasets/` directory.

Each dataset should follow the standard ODM dataset structure:

```
dataset_name/
└── images/
    ├── image1.jpg
    ├── image2.jpg
    └── ...
```

---

## 2. Run different ODM versions

Run each version of OpenDroneMap using Docker.

Example:

```bash
docker run -ti --rm \
-v /home/hubert/oats/datasets:/datasets \
-v /home/hubert/oats/results/3_0_0:/results \
opendronemap/odm:3.0.0 \
--project-path /datasets
```

Repeat for the newer version:

```bash
docker run -ti --rm \
-v /home/hubert/oats/datasets:/datasets \
-v /home/hubert/oats/results/3_5_6:/results \
opendronemap/odm:3.5.6 \
--project-path /datasets
```

This produces orthomosaics for each dataset in separate version directories.

---

## 3. Run the comparison script

The script searches both result directories for:

```
odm_orthophoto.tif
```

Files are matched using their **relative dataset paths** to ensure the same datasets are compared.

Example:

```bash
python compare_orthophotos.py
```

---

# Comparison Method

## 1. ORB Feature Similarity

ORB (Oriented FAST and Rotated BRIEF) detects keypoints in both images and measures how many features match.

Output:

```
Similarity using ORB: 0.0 – 1.0
```

Where:

- **1.0** = identical features
- **0.8+** = highly similar
- **<0.5** = significant difference

---

## 2. Structural Similarity Index (SSIM)

SSIM compares image structure, brightness, and contrast.

Output:

```
Similarity using SSIM: 0.0 – 1.0
```

Where:

- **1.0** = identical images
- **0.95+** = nearly identical orthomosaics

---

# Image Processing Steps

The script performs the following preprocessing:

1. Reads `.tif` orthomosaics
2. Converts multiband images to grayscale
3. Normalizes pixel values
4. Resizes images to match dimensions
5. Computes:
   - ORB feature similarity
   - SSIM similarity
6. Displays visual difference images

---

# Example Output

```
brighton_masked/odm_orthophoto/odm_orthophoto.tif

Similarity using SSIM is: 0.987
Similarity using ORB is: 0.91
```

These values indicate the orthomosaic outputs are **highly consistent between ODM versions**.

---

# Dependencies

Install required Python packages:

```bash
pip install pillow scikit-image opencv-python tifffile numpy
```

---

# Notes

- Orthomosaics may have **different dimensions** between ODM versions.
- The script automatically resizes images before computing SSIM.
- ORB similarity is robust to **small geometric differences**.

---

# Purpose

This repository supports:

- **ODM regression testing**
- **algorithm change detection**
- **research benchmarking of photogrammetry pipelines**

It provides a lightweight way to verify that new releases of OpenDroneMap do not unintentionally alter orthomosaic outputs.
