# ODM_intergration_test
This repo includes unit and intergration test that are created to optimize the already existing ODM quality control

Overview

When upgrading ODM versions, subtle changes in reconstruction algorithms may lead to variations in the generated orthomosaic.

This repository provides:

1. A standardized dataset structure

2. A Python comparison script

3. A method to compare orthophotos generated from different ODM versions

The comparison script uses two complementary image similarity approaches:

ORB feature matching (OpenCV)
Measures similarity by comparing keypoint features between orthophotos.

Structural Similarity Index (SSIM)
Measures perceptual similarity based on luminance, contrast, and structure.

Using both approaches provides a robust indicator of whether orthomosaics differ significantly between versions.
