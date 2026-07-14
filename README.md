# رقيب | Raqeeb — AI-Powered Drone Detection & Defense Platform

**Turning Saudi Arabia's existing camera infrastructure into a real-time hostile-drone defense network — no new hardware required.**

Developed as a capstone project for the AI Solutions Development Bootcamp at **Tuwaiq Academy**, in collaboration with the **General Authority for Survey and Geospatial Information (GEOSA)**.

---

## The Problem

Saudi Arabia has faced a rising pattern of hostile drone incursions targeting sensitive sites — oil infrastructure, borders, airports, and sovereign facilities. Traditional counter-drone systems (e.g. PAC-3 MSE interceptors) are expensive, closed-source, and require entirely new infrastructure to deploy — with per-missile costs running into the millions of dollars.

Raqeeb takes a different approach: instead of building a new sensor network, it turns the cameras that already exist across the Kingdom into an intelligent, real-time detection grid.

## What Raqeeb Does

1. **Detects** hostile drones in live video and distinguishes them from birds and civilian aircraft, using a computer vision model trained on 6 target classes.
2. **Tracks** each target continuously, assigning it a persistent identity across frames.
3. **Projects** its pixel position onto real-world GPS coordinates.
4. **Calculates** speed, heading, predicted path, estimated time of arrival, and the optimal interception point.
5. **Classifies threat level** (critical / high / low) using a model that combines drone type, speed, heading, time of day, and proximity to critical infrastructure.
6. **Alerts** operators before impact, through a live dashboard with historical analysis and forecasting.

All of this is built to run entirely within the Kingdom, on commercially available hardware, with an Arabic-first interface.

---

## Key Features

- 📊 **Live Dashboard** — real-time overview of active threats, alerts, and affected regions
- 🎥 **Live & Recorded Detection** — per-target readout (type, speed, heading, GPS, confidence, ETA, threat level) over a live camera feed or recorded clip
- 🗺️ **Historical Attack Map** — filterable by date, region, and drone type, with geographic clustering
- 📈 **Analytics & Forecasting** — historical trend analysis and 30-day attack forecasting powered by XGBoost
- 📷 **Optimal Camera Placement** — KMeans-based suggestions for new camera locations based on attack density
- 🤖 **Sanad (سند)** — a bilingual (Arabic/English) AI decision-support assistant that answers natural-language questions about the data and forecasts future patterns, with a switch between a fine-tuned local Qwen 2.5 model and Gemini
- 🌤️ **Weather-Aware Detection** — accounts for weather conditions in detection confidence

---

## Screenshots

**Live Dashboard**
![Dashboard Overview](assets/dashboard-overview.png)

**Live Detection & Threat Assessment**
![Live Detection](assets/live-detection.png)

**Historical Attack Map**
![Attack Map](assets/attack-map.png)

**Analytics & Forecasting**
![Analytics](assets/analytics-forecast.png)

**Sanad — AI Assistant**
![Sanad Assistant](assets/sanad-assistant.png)

## Demo Video

📺 [Full system walkthrough on YouTube]({})

---

## Model Performance

The detection model was validated across 6 classes — 4 drone/UAV types plus birds and civilian aircraft to minimize false positives:

| Class | Images | Instances | Precision | Recall | mAP50 | mAP50-95 |
|---|---|---|---|---|---|---|
| **All** | 1447 | 1519 | 0.966 | 0.945 | 0.968 | **0.822** |
| Bird | 156 | 181 | 0.966 | 0.961 | 0.988 | 0.804 |
| shahed_136 | 415 | 420 | 0.981 | 0.997 | 0.994 | 0.955 |
| orlan | 308 | 308 | 0.976 | 0.997 | 0.995 | 0.978 |
| Airplane | 100 | 100 | 0.927 | 0.99 | 0.982 | 0.842 |
| Helicopter | 125 | 157 | 0.987 | 0.948 | 0.993 | 0.714 |
| dji | 352 | 353 | 0.961 | 0.775 | 0.854 | 0.641 |

Inference speed: ~1.9ms preprocess, ~4.4ms inference, ~0.1ms postprocess per image.

---

## Tech Stack

**Computer Vision & ML**
- YOLO26 (Ultralytics) for detection — NMS-free, DFL-free architecture chosen for stronger CPU inference on edge hardware
- ByteTrack (tuned `track_buffer`) for multi-object tracking — chosen over BoT-SORT/StrongSORT because downstream speed, heading, and ETA calculations depend on smooth motion history
- XGBoost for attack forecasting
- KMeans for optimal camera placement suggestions

**Backend**
- FastAPI
- Supabase (PostgreSQL)
- Alembic migrations
- Python managed via `uv`

**Frontend**
- React + TypeScript + Vite

**AI Assistant (Sanad)**
- Fine-tuned Qwen 2.5 (via Ollama, local inference) with a live switch to Gemini for faster responses on constrained hardware

---

## Engineering Notes

A few decisions that shaped the system, worth calling out for anyone reviewing the code:

- **ONNX export showed no inference speedup** on ARM-based hardware (CPUExecutionProvider is optimized for x86), so the models run in native PyTorch/Ultralytics form instead.
- **Detection confidence threshold** was tuned to balance sensitivity against false positives across the 6 target classes.
- **ByteTrack over more complex trackers**: since the system's core value is in the derived metrics (speed, direction, ETA) rather than re-identification across long occlusions, a lighter, faster tracker with a well-tuned buffer outperformed heavier alternatives on constrained hardware.

---

## Team

Built by **Rana Almohaimeed**, **Leen**, and **Abdullah Almudayfir** as part of Tuwaiq Academy's AI Solutions Development Bootcamp, in collaboration with GEOSA.

---

## Acknowledgments

General Authority for Survey and Geospatial Information (GEOSA) and Tuwaiq Academy, for the opportunity to build this project.
