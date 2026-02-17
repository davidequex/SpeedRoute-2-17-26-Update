# SpeedRoute — Automated Curb Extraction from Satellite Imagery

SpeedRoute is a computer vision pipeline that converts raw satellite imagery into clean, CAD-ready curb lines. The goal is to eliminate manual tracing when producing traffic control plans and replace it with a deterministic, reproducible workflow.

Instead of using black-box ML segmentation, SpeedRoute uses a statistical color analysis approach that is fast, explainable, and tunable for engineering workflows.

---

## Overview

SpeedRoute detects streets by analyzing saturation patterns in satellite imagery. Asphalt tends to have lower color saturation than vegetation and rooftops, allowing roads to be isolated mathematically.

The pipeline converts imagery into structured curb geometry and exports it directly into AutoCAD-compatible DXF files.

---

## Pipeline

High level flow:

1. Convert RGB image → HSV color space  
2. Extract saturation channel  
3. Compute row + column mean saturation  
4. Smooth signal using Gaussian filtering  
5. Detect valleys (low saturation = roads)  
6. Expand valleys into full road widths  
7. Merge adjacent detections  
8. Generate curb edges  
9. Export CAD polylines to DXF  

---


## Core Technology

### Saturation-Based Road Detection

SpeedRoute identifies roads as low-saturation regions.

- HSV conversion isolates color intensity
- Roads appear as measurable dips in saturation
- 1D Gaussian smoothing removes noise
- Gradient analysis detects valley positions

This avoids edge detectors and ML models entirely.

Implementation entry point:  
`run_curb_detection.py`

Core detection logic:  
`curb_detect_final.py`

---

### Valley Expansion

Each detected valley is expanded outward until saturation rises above a threshold.

This estimates:

- road top edge
- road bottom edge
- road width

Width constraints filter out noise and non-road artifacts.

---

### Road Merging

Nearby detections are merged when separated by small gaps.

This handles:

- medians
- parked cars
- shadows
- driveway interruptions

Result: unified street bands instead of fragmented segments.

---

### Curb Line Generation

Each road produces two curb edges:

- top boundary
- bottom boundary

Horizontal and vertical streets are processed independently and merged into a full curb grid.

---

### CAD Export

Final curb lines are exported as DXF polylines.

The result opens directly in AutoCAD and behaves like manually drawn geometry.

No post-processing required.

---

## Visual Progress

### Raw Detection Overlay

Algorithm output directly on satellite imagery.

Red = horizontal curbs  
Blue = vertical curbs

<img width="1054" height="893" alt="Screenshot 2026-02-17 at 12 53 47 PM" src="https://github.com/user-attachments/assets/961a8ab3-7c5d-4cb0-bd64-32f311d7e159" />


---

### Manual Cleanup / Evaluation

Some detections are manually removed or adjusted to evaluate edge cases and refine parameters.

<img width="1056" height="919" alt="Screenshot 2026-02-17 at 12 55 54 PM" src="https://github.com/user-attachments/assets/333c0f18-e10d-4fd7-a6dc-6415f34f27b8" />

---

### Final CAD Schematic

Clean curb network ready for drafting workflows.

<img width="1054" height="876" alt="Screenshot 2026-02-17 at 12 58 20 PM" src="https://github.com/user-attachments/assets/b128798c-f4ee-45d7-a3fa-7271ec1c7b15" />
<img width="1055" height="883" alt="Screenshot 2026-02-17 at 12 59 05 PM" src="https://github.com/user-attachments/assets/0b54e05f-d791-4d54-9ca9-738ad81515e6" />


---

## Example Usage

```bash
python run_curb_detection.py
```

Outputs:

- `curbs_detected.dxf`
- visualization overlay
- schematic curb map

---

## Why This Approach

SpeedRoute intentionally avoids deep learning.

The system is:

- deterministic
- fast
- explainable
- tunable
- lightweight
- reproducible

For engineering workflows, control and predictability matter more than black-box accuracy.

---

## Future Work

- curved road support
- intersection classification
- sidewalk extraction
- batch city-scale processing
- GIS integration
- interactive review UI

---

## Project Goal

SpeedRoute exists to remove hours of manual tracing from traffic plan production and replace it with a reliable automation pipeline that engineers can trust and audit.

---

## License

MIT License
