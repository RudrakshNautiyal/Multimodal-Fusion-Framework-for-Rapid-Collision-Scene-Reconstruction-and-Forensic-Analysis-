# Multimodal Fusion Framework for Rapid Collision Scene Reconstruction and Forensic Analysis

**Project codename: CrashTwin3D**

Major Project – I (18B19CI791) | AY 2026-27
Department of CSE & IT, Jaypee University of Information Technology

**Supervisor:** Lt. Dr. Amit Kumar Jakhar (Associate Professor)

**Team Members:**

| Name | Roll No. |
|------|----------|
| Pranshul Sharma | 231030177 |
| Ritik Tomar | 231030028 |
| Rudraksh Nautiyal | 231030191 |
| Yuvraj | 231030068 |

## Overview

CrashTwin3D fuses in-vehicle IoT telemetry with feed-forward 3D reconstruction of a crash scene captured on an uncalibrated smartphone, then computes forensic metrics and produces a PDF report.

## Modules

| # | Module | Folder | Stack |
|---|--------|--------|-------|
| 1 | Accident detection & IoT telemetry | `firmware/` | ESP32, MPU6050, NEO-6M GPS, Arduino C++, Wokwi |
| 2 | 3D reconstruction | `reconstruction/` | OpenCV, DUSt3R / MASt3R, PyTorch |
| 3 | Damage segmentation & forensic analysis | `forensics/` | SAM-2, Open3D |
| 4 | Visualization & forensic reporting | `backend/`, `dashboard/`, `reports/` | FastAPI, PostgreSQL, Streamlit, Three.js, ReportLab |
| – | Dataset curation | `dataset/` | Kaggle / Zenodo |

## Repository layout

```
├── firmware/crashtwin_node/   # ESP32 impact-detection firmware
├── reconstruction/            # frame extraction + DUSt3R/MASt3R pipeline
├── forensics/                 # SAM-2 segmentation, crush depth, debris metrics
├── backend/                   # FastAPI telemetry + results API
├── dashboard/                 # Streamlit / Three.js dashboard
├── reports/                   # ReportLab PDF generation
├── dataset/                   # dataset curation scripts and metadata
├── docs/                      # slides, weekly logs, architecture notes
├── requirements.txt
└── README.md
```

## Telemetry design (Module 1)

- MPU6050 sampled at 100 Hz; vector magnitude `a = sqrt(ax² + ay² + az²)`
- Collision interrupt when magnitude exceeds **3.5 g** (normal driving ~1.0 g, braking/potholes ~1.5–2.1 g)
- NEO-6M provides location and UTC timestamp on trigger
- ESP32 sends a JSON payload to the backend over Wi-Fi/4G

```json
{
  "device_id": "ESP32_CRASHTWIN_01",
  "event_type": "COLLISION_DETECTED",
  "peak_g_force": 4.00,
  "gps": {"lat": 28.6139, "lon": 77.2090},
  "timestamp": "2026-07-15T14:32:00Z"
}
```

## Getting started

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# run the telemetry backend
uvicorn backend.main:app --reload
```

DUSt3R/MASt3R and SAM-2 need a CUDA-enabled PyTorch install and are cloned/installed separately.

## Datasets used for validation

- **CarDD** – car damage segmentation (Module 3 benchmark)
- **MAD-CARS** – multi-view car videos (Module 2 validation)
- **CCD** – real dash-cam crash videos (scenario context)

## Status

- [x] Module 1 firmware validated in Wokwi simulation
- [ ] Physical ESP32 + MPU6050 + NEO-6M assembly
- [ ] Telemetry storage (PostgreSQL)
- [ ] DUSt3R/MASt3R reconstruction
- [ ] SAM-2 damage segmentation
- [ ] Dashboard and PDF report
- [ ] Dataset publication

## License

Released under the [MIT License](LICENSE).
