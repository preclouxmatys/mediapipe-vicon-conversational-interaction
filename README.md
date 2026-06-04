# MediaPipe–Vicon Validation During Conversational Interaction

## Overview

This repository contains the complete processing and statistical analysis pipeline used to validate MediaPipe BlazePose against a Vicon motion capture system during spontaneous face-to-face dyadic conversations.

Three movement metrics were evaluated:

- Quantity of Movement (QoM)
- Wrist movement amplitude
- Elbow joint angle

The workflow combines marker-based motion capture (Vicon) and markerless pose estimation (MediaPipe BlazePose).

---

## Required Input Data

The only required input data are the raw Vicon CSV files exported from Vicon Nexus.

Expected structure:

```
DATA/
└── VICON_CSV/
    ├── D01/
    ├── D02/
    └── ...
```

MediaPipe landmark files (`*_pose.xlsx`) are generated automatically by the pipeline and do not need to be provided manually.

Raw RGB videos are not included in this repository due to storage limitations.

---

## Processing Pipeline

### Step 1 – MediaPipe Landmark Extraction

Run:

```
mediapipe_batch_export.ipynb
```

This notebook processes the RGB recordings and generates MediaPipe landmark files (`*_pose.xlsx`) for each participant and condition.

---

### Step 2 – Vicon Processing

Run the following notebooks:

```
Shoulder_size_VICON.ipynb
QDM_VICON_lastversion.ipynb
QDM_SHOULDER_VICON.ipynb
```

These notebooks:

- compute shoulder width
- compute Quantity of Movement (QoM)
- normalize movement measures by shoulder width
- generate the Vicon summary dataset

Output: `vicon_QDM_for_validation_long.xlsx`

---

### Step 3 – MediaPipe Processing

Run:

```
QdM_mediaPipe.ipynb
shoulder_norm_MediaPipe.ipynb
```

These notebooks:

- compute Quantity of Movement (QoM)
- estimate shoulder width
- scale MediaPipe measurements to millimeters

Output: `mediapipe_QDM_filtered_to_mm.xlsx`

---

### Step 4 – QoM Validation Dataset

Combine `vicon_QDM_for_validation_long.xlsx` and `mediapipe_QDM_filtered_to_mm.xlsx` into:

```
Stats.xlsx
```

This file serves as the input for all QoM validation analyses.

---

### Step 5 – QoM Validation

Run:

```
QoM MpVicon.Rmd
```

Input: `Stats.xlsx`

Analyses include:

- Pearson correlations
- Coefficients of determination (R²)
- Intraclass Correlation Coefficients (ICC)
- Root Mean Square Errors (RMSE)
- Bland–Altman analyses
- Constrained permutation tests

for Wrist QoM and Head QoM.

---

### Step 6 – Wrist Amplitude Validation

Run:

```
Ampl_MP_VICON.ipynb
```

Output: `amplitude_WRISTS_MP_vs_VICON_noInterp.xlsx`

Then run:

```
Amplitude MpVICON.Rmd
```

to compute correlation and agreement metrics.

---

### Step 7 – Elbow Angle Validation

Run:

```
Angl.ipynb
```

Execute separately for each projection plane (`XY`, `XZ`, `YZ`).

Outputs:

```
ANGLE_elbow_MP_vs_VICON_planeXY_noInterp.xlsx
ANGLE_elbow_MP_vs_VICON_planeXZ_noInterp.xlsx
ANGLE_elbow_MP_vs_VICON_planeYZ_noInterp.xlsx
```

Then run:

```
AngleelbowMpVicon.Rmd
```

to compute Pearson correlations, RMSE, and descriptive statistics for each projection plane.

---

## Repository Structure

```
Python/
├── mediapipe_batch_export.ipynb
├── QdM_mediaPipe.ipynb
├── shoulder_norm_MediaPipe.ipynb
├── Shoulder_size_VICON.ipynb
├── QDM_VICON_lastversion.ipynb
├── QDM_SHOULDER_VICON.ipynb
├── Ampl_MP_VICON.ipynb
└── Angl.ipynb

R/
├── QoM MpVicon.Rmd
├── Amplitude MpVICON.Rmd
└── AngleelbowMpVicon.Rmd

example_output/
├── Stats.xlsx
├── mediapipe_QDM_filtered_to_mm.xlsx
├── vicon_QDM_for_validation_long.xlsx
├── amplitude_WRISTS_MP_vs_VICON_noInterp.xlsx
├── ANGLE_elbow_MP_vs_VICON_planeXY_noInterp.xlsx
├── ANGLE_elbow_MP_vs_VICON_planeXZ_noInterp.xlsx
└── ANGLE_elbow_MP_vs_VICON_planeYZ_noInterp.xlsx
```

---

## Notes

This repository is intended to provide a reproducible workflow for the analyses reported in the manuscript.

The original Vicon recordings and RGB videos are not distributed due to file size limitations and participant privacy considerations.

Processed example outputs are provided to allow users to reproduce the statistical analyses independently.