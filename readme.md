


## # Automated Weld Radiography AI & Reporting Pipeline

## 🚀 Overview
This project is a high-value Computer Vision application designed for **Non-Destructive Testing (NDT)** in the Oil & Gas and Power industries. It automates the interpretation of digital radiography (RT) images, detecting welding defects and validating them against international engineering standards (ASME B31.3).

The goal is to reduce human error in X-ray interpretation and accelerate the "Review-to-Repair" cycle for critical assets like pressure vessels and process piping.

## 🏗️ Project Architecture
The system is built with a modular "Phase-based" approach:

1.  **Phase 1: The Digital Eye (Pre-processing)**
    * Uses **CLAHE** (Contrast Limited Adaptive Histogram Equalization) to enhance low-contrast RT images.
    * Implements automated **IQI (Image Quality Indicator)** verification using Hough Line Transforms to ensure legal sensitivity requirements.
    
2.  **Phase 2: The Defect Engine (AI Inference)**
    * Powered by **YOLO11x** (56.8M parameters) for high-precision detection.
    * Trained to identify: `Cracks`, `Slag Inclusions`, `Porosity`, and `Lack of Penetration`.

3.  **Phase 3: The Engineering Brain (Rule Engine)**
    * A consolidated validation pipeline implementing **ASME B31.3** acceptance criteria.
    * Automatically maps AI detections to "Accept/Reject" status based on wall thickness ($T$).

4.  **Phase 4: Reporting (In Progress)**
    * Generation of PDF inspection reports with audit trails and P.Eng seal placeholders.

## 🛠️ Technical Stack
* **Frontend:** Streamlit (Web Dashboard)
* **AI Engine:** Ultralytics YOLO11x
* **Vision Logic:** OpenCV (Image Enhancement)
* **Language:** Python 3.10+
* **Standards Logic:** Custom ASME Rule Engine

### Thorough Documentation

- .gitignore: This is a configuration file that tells Git which files or folders to intentionally untrack. Keeping your weld_ai_env out of GitHub makes your repository clean and prevents "pre-receive hook" rejections.  

- Git LFS: This replaces large files in your repository with tiny "pointer" files. The actual 109MB data is stored on a separate server, allowing you to bypass the 100MB limit.  
- requirements.txt: Instead of pushing the environment, ensure you have run pip freeze > requirements.txt. This allows a colleague (or your future self) to rebuild the exact same environment using pip install -r requirements.txt.


## Development Steps:

- Calibration Logic: We will use the IQI (Image Quality Indicator) wires identified in Phase 1 as a physical reference. Since wire diameters are standardized, we can calculate a pixels_per_mm ratio.

- Visualization Logic: We will overlay the calculated "mm" measurements directly onto the image using OpenCV.

- Reporting Logic: We will create a reporter.py module to compile the project metadata, the annotated image, and the ASME B31.3 "Accept/Reject" results into a professional PDF.

- Assumptions:The first IQI wire detected has a known physical diameter (e.g., $0.8$mm).The fpdf library is used for report generation due to its lightweight nature.

## 📂 Project Structure
```text


ai_weld_rt/
│
├── main.py                         # Streamlit Orchestrator (UI + Pipeline Controller)
├── requirements.txt                # Python dependencies
├── .env                            # API keys, secrets
│
├── weights/                        # Model weights (.pt files)
│   └── yolov11_weld.pt
│
├── data/
│   ├── raw/                        # Original RT images (RIAWELC, GDXray, field data)
│   ├── processed/                  # CLAHE-enhanced images
│   ├── iqis/                       # Cropped IQI regions, wire detection samples
│   ├── annotations/                # YOLO labels, JSON metadata
│   └── assets/                     # Logos, P.Eng seal, templates
│
├── src/                            # Core Logic Package
│   ├── __init__.py
│   │
│   ├── preprocessing/              # Phase 1: Image Pre-processing
│   │   ├── __init__.py
│   │   ├── processor.py            # CLAHE, denoising, normalization
│   │   └── iqi_detector.py         # IQI wire detection + pixel/mm scaling
│   │
│   ├── detection/                  # Phase 2: Vision Models
│   │   ├── __init__.py
│   │   ├── yolo_detector.py        # YOLOv11 inference logic
│   │   └── postprocess.py          # NMS, confidence filtering
│   │
│   ├── verification/               # Phase 3: Explainability / Validation
│   │   ├── __init__.py
│   │   └── gradcam_explainer.py    # Grad-CAM++ heatmaps
│   │
│   ├── rule_engine/                # Phase 4: ASME/API Acceptance Logic
│   │   ├── __init__.py
│   │   ├── asme_b31_3_engine.py
│   │   ├── api_1104_engine.py
│   │   └── aws_d1_1_engine.py
│   │
│   ├── reporting/                  # Phase 5: Automated Reporting
│   │   ├── __init__.py
│   │   ├── pdf_reporter.py         # ReportLab/Jinja2 PDF generator
│   │   └── audit_trail.py          # JSON + image audit logs
│   │
│   └── utils/                      # Shared utilities
│       ├── __init__.py
│       ├── file_io.py
│       ├── image_ops.py
│       └── metadata_loader.py
│
├── models/
│   ├── yolo/                       # YOLO model configs
│   ├── gradcam/                    # Grad-CAM model hooks
│   └── rule_engine/                # Lookup tables, thresholds
│
├── rules/                          # Digitized Standards
│   ├── asme_b31_3.json
│   ├── api_1104.json
│   ├── aws_d1_1.json
│   └── client_specs/
│       ├── shell_dep.json
│       ├── bp_gp.json
│       └── adnoc_ts.json
│
├── reports/
│   ├── pdf/                        # Final generated reports
│   └── audit_trails/               # JSON logs, heatmaps, metadata
│
├── logs/                           # System logs, pipeline logs
│
├── tests/                          # Unit tests for each module
│   ├── test_preprocessing.py
│   ├── test_detection.py
│   ├── test_rule_engine.py
│   └── test_reporting.py
│
├── configs/
│   ├── training.yaml               # YOLO training config
│   ├── inference.yaml              # Thresholds, NMS, confidence
│   └── metadata_schema.json        # JSON schema for RT metadata
│
├── docs/
│   ├── architecture.md             # System architecture
│   ├── ontology.md                 # Classification ontology
│   └── api_reference.md            # Module-level documentation
│
└── notebooks/
    └── training_v11.ipynb          # Experimental training scripts



