# Multimodal Fusion Framework for Rapid Collision Scene Reconstruction and Forensic Analysis

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)

**CrashTwin3D** fuses in-vehicle IoT telemetry with feed-forward 3D reconstruction of a crash scene captured on an uncalibrated smartphone. It then computes forensic metrics and generates a PDF report.

## Features

- **Collision detection:** an ESP32 edge node with an MPU6050 detects impacts above a 3.5 g threshold and logs geotagged, timestamped events from a NEO-6M GPS.
- **3D reconstruction:** a feed-forward DUSt3R/MASt3R pipeline builds a point cloud or mesh of the scene from smartphone video, with no camera calibration.
- **Forensic analysis:** SAM-2 segmentation and Open3D processing yield crush depth, damage dimensions, debris dispersion and impact angle.
- **Dashboard and reporting:** a web dashboard combines telemetry with the 3D scene and exports a forensic PDF report.
- **Dataset:** a curated multimodal crash dataset, to be published on Kaggle / Zenodo.

## System Overview

| Module | Folder | Stack |
|--------|--------|-------|
| Accident detection & IoT telemetry | `firmware/` | ESP32, MPU6050, NEO-6M GPS, Arduino C++, Wokwi |
| 3D reconstruction | `reconstruction/` | OpenCV, DUSt3R / MASt3R, PyTorch |
| Damage segmentation & forensic analysis | `forensics/` | SAM-2, Open3D |
| API, dashboard & reporting | `backend/`, `dashboard/`, `reports/` | FastAPI, PostgreSQL, Streamlit, Three.js, ReportLab |
| Dataset curation | `dataset/` | Kaggle / Zenodo |

## Repository Structure

```
├── firmware/crashtwin_node/   # ESP32 impact-detection firmware
├── reconstruction/            # Frame extraction + DUSt3R/MASt3R pipeline
├── forensics/                 # SAM-2 segmentation, crush depth, debris metrics
├── backend/                   # FastAPI telemetry and results API
├── dashboard/                 # Streamlit / Three.js dashboard
├── reports/                   # ReportLab PDF generation
├── dataset/                   # Dataset curation scripts and metadata
├── docs/                      # Architecture notes and documentation
├── requirements.txt
└── README.md
```

## Telemetry Design

- MPU6050 sampled at 100 Hz; vector magnitude `a = sqrt(ax² + ay² + az²)`
- Collision trigger when magnitude exceeds **3.5 g** (normal driving ~1.0 g, hard braking and potholes ~1.5–2.1 g)
- NEO-6M provides location and UTC timestamp on trigger
- The ESP32 sends a JSON payload to the backend over Wi-Fi/4G

```json
{
  "device_id": "ESP32_CRASHTWIN_01",
  "event_type": "COLLISION_DETECTED",
  "peak_g_force": 4.00,
  "gps": {"lat": 28.6139, "lon": 77.2090},
  "timestamp": "2026-07-15T14:32:00Z"
}
```

## Getting Started

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# Run the telemetry backend
uvicorn backend.main:app --reload
```

DUSt3R/MASt3R and SAM-2 require a CUDA-enabled PyTorch installation and are installed separately.

## Validation Datasets

- **CarDD:** car damage segmentation
- **MAD-CARS:** multi-view car videos for reconstruction testing
- **CCD:** real dash-cam crash videos for scenario context

## Roadmap

- [x] Edge-node firmware validated in Wokwi simulation
- [ ] Physical ESP32 + MPU6050 + NEO-6M assembly
- [ ] Telemetry storage (PostgreSQL)
- [ ] DUSt3R/MASt3R reconstruction
- [ ] SAM-2 damage segmentation
- [ ] Dashboard and PDF report
- [ ] Dataset publication

## Team

- Pranshul Sharma
- Ritik Tomar
- Rudraksh Nautiyal
- Yuvraj

## License

Released under the [MIT License](LICENSE).
