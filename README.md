# COMP 9130 - Mini Project 7: Blood Cell Object Detection

---

## Problem Description

### Business Context
Manual complete blood count (CBC) analysis is time-consuming and error-prone, creating bottlenecks in diagnostic workflows. This project demonstrates a proof-of-concept automated system to detect and classify blood cells in microscopy images.

### Project Objective
Build and evaluate a YOLOv8 object detection model capable of:
- **Detecting and localizing** three blood cell types: red blood cells (RBC), white blood cells (WBC), and platelets
- **Achieving clinical-grade accuracy** suitable for AI-assisted screening workflows
- **Analyzing performance trade-offs** between detection sensitivity and specificity
- **Providing business recommendations** for deployment viability

### Medical Significance
Accurate automated cell counting reduces:
- **Diagnostic turnaround time** (from hours to minutes per sample)
- **Human analyst fatigue** on routine samples
- **Transcription errors** from manual counting
- **Cost per analysis** through process automation

---

## Dataset Source

### BCCD (Blood Cell Count and Detection)
- **Source:** [Roboflow Universe](https://universe.roboflow.com/roboflow-100/bccd)
- **Format:** YOLO bounding box annotations (normalized center coordinates)
- **Total Images:** 874 blood smear microscopy images
- **Class Distribution:**
  - Red Blood Cells (RBC): 8,814 annotations (85.2%)
  - White Blood Cells (WBC): 789 annotations (7.6%)
  - Platelets: 739 annotations (7.1%)
- **Data Split:**
  - Training: 765 images
  - Validation: 73 images
  - Test: 36 images

### Dataset Characteristics
- **Class Imbalance:** RBCs heavily overrepresented (realistic for blood smears)
- **Image Resolution:** Variable; resized to 640×640 for model input
- **Annotation Type:** Bounding boxes with cell-level granularity
- **Challenge:** Small objects (platelets), overlapping cells, variable lighting

---

## Setup and Run Instructions

### Prerequisites
- Python 3.8 or higher
- CUDA 11.8+ (for GPU acceleration, optional but recommended)
- Git
- ~5 GB disk space (dataset + model weights)

### Installation

#### 1. Clone Repository
```bash
git clone https://github.com/bing-er/mini-project-7
cd mini-project-7
```

#### 2. Create Virtual Environment (Recommended)
```bash
# Using conda
conda create -n mp7 python=3.10
conda activate mp7

# OR using venv
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

#### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

**Key Dependencies:**
- `ultralytics>=8.0.0` - YOLOv8 implementation
- `torch>=2.0.0` - Deep learning framework
- `opencv-python>=4.5.0` - Image processing
- `jupyter>=1.0.0` - Notebook environment
- `matplotlib>=3.5.0` - Visualization
- `pandas>=2.0.0` - Data analysis

### Running the Project

```bash
jupyter notebook notebooks/MP7_Yu_Tan.ipynb
```

### Dataset Download (Automated in Notebook)
The notebook automatically downloads BCCD from Roboflow using the private dataset API. For manual download:

```bash
# Via Roboflow API (requires account)
pip install roboflow
from roboflow import Roboflow
rf = Roboflow(api_key="YOUR_API_KEY")
project = rf.workspace("roboflow-100").project("bccd")
dataset = project.versions(1).download("yolov8")
```

---

## Results Summary

### Overall Performance Metrics

| Metric | Score | Interpretation |
|--------|-------|-----------------|
| **mAP@50** | 0.9186 | Excellent detection at standard threshold |
| **mAP@50-95** | 0.6552 | Good detection; localization needs improvement |
| **Precision** | 0.8583 | 85.8% of predicted boxes are correct |
| **Recall** | 0.8958 | 89.6% of ground-truth cells detected |

**Interpretation:** The model achieves strong performance on cell detection but struggles with precise bounding box localization, especially for small objects (platelets).

### Per-Class Performance

| Class | mAP@50 | mAP@50-95 | Instances | Notes |
|-------|--------|-----------|-----------|-------|
| **WBC** | 0.9795 | 0.8188 | 261 | Best performance; larger, distinct cells |
| **RBC** | 0.8879 | 0.6331 | 2,042 | Majority class; moderate localization |
| **Platelets** | 0.8883 | 0.5136 | 182 | Poorest; small size limits precision |

### Confidence Threshold Analysis

**Recommended Operating Point: 0.50–0.60 confidence threshold**

| Threshold | Precision | Recall | Use Case |
|-----------|-----------|--------|----------|
| 0.25 | 0.78 | 0.94 | High sensitivity (catch all cells) |
| 0.50 | 0.87 | 0.88 | Balanced |
| 0.75 | 0.95 | 0.72 | High specificity (minimize false alarms) |

Rationale: In clinical blood analysis, Mmissing cells (false negatives) is more dangerous than false positives, since pathologists can rapidly dismiss low-confidence detections but cannot catch missed abnormalities. Threshold 0.2 is recommended.

---

## Team Member Contributions

### Binger Yu
- Dataset exploration and class distribution analysis
- YOLOv8 model training and hyperparameter optimization
- Per-class performance evaluation and confusion matrix analysis
- Confidence threshold analysis and clinical viability assessment
- Python notebook code development and testing

---

### Timothy Tan
- Report writing and document structure
- README.md and requirements.txt
- False Negative Visualizations
- Clinical context analysis (false positive/negative trade-offs)
- Business recommendation and deployment viability assessment

---

### Collaborative Efforts
- **Joint:** Initial project scoping, dataset selection, result interpretation
- **Code Review:** Peer review of Jupyter notebook cells
- **Report Review:** Cross-validation of findings and claims

---
## Project Structure

```
MP7_BloodCellDetection/
├── MP7_Yu_Tan.ipynb                      # Main Jupyter notebook (all analysis)
├── requirements.txt                       # Python dependencies (pip install)
├── README.md                              # This file
├── MP7_Report_BloodCellDetection.pdf      # Complete technical report
├── main.tex                               # LaTeX source for report
├── figs/                                  # Generated visualizations
│   ├── class_distribution.png
│   ├── training_curves.png
│   ├── per_class_performance.png
│   ├── confidence_threshold_comparison.png
│   ├── predictions_validation.png
│   └── hyperparameter_comparison.png
└── BCCD-1/                                # Dataset (auto-downloaded by notebook)
    ├── data.yaml                          # YOLO format configuration
    ├── train/
    │   ├── images/                        # 765 training images
    │   └── labels/                        # YOLO annotations
    ├── valid/
    │   ├── images/                        # 73 validation images
    │   └── labels/
    └── test/
        ├── images/                        # 36 test images
        └── labels/
```

