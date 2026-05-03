# PS-4 · Hybrid Predictive Traffic Congestion Intelligence Model

<div align="center">

**A real-time hybrid traffic intelligence system that fuses CCTV computer vision data with Google Maps GPS telemetry through a cross-modal validation layer, then forecasts standstill jam probability using LSTM temporal modelling — eliminating false positives and enabling 15–45 minute predictive response.**

</div>

---

## Live Demo

🔗 **[https://devikapavithran.github.io/Predictive-Traffic-Congestion-Modeling/](https://devikapavithran.github.io/Predictive-Traffic-Congestion-Modeling/)**

Open in any modern browser — zero installation, fully client-side.

---

## Problem Statement

Current traffic management systems are **reactive** — they identify jams only after they have already formed. There is no unified predictive model that fuses probabilistic GPS data (macro-view) with computer vision-based visual feeds (micro-view). The specific challenges are:

- **Inconsistent Data Latency** — Google Maps data lags several minutes while CCTV feeds are near-instant but geographically restricted
- **The Context Gap** — GPS shows a red line but cannot distinguish a temporary signal delay from a long-term obstruction without visual confirmation
- **Predictive Blind Spots** — Existing models cannot predict how a bottleneck seen on camera will impact adjacent arterial roads 30 minutes ahead

> **The objective:** Develop a Hybrid Predictive Traffic Intelligence Model integrating live video analytics and geospatial API data to provide cross-modal congestion validation and temporal standstill forecasting across 15–45 minute horizons.

## Features

### 🗺️ Live City Traffic Map
- 12 monitored junctions rendered on a dark tactical city grid using HTML5 Canvas
- Road network color-coded by real-time jam level (L1 green → L5 red)
- Pulsing rings on L4/L5 nodes indicating active congestion events
- Incident overlay markers showing the exact cause and epicenter of a jam
- Click any junction to drill into full analytics in the right panel

### 📹 CCTV Feed Strip (Computer Vision Layer)
- 6 live camera feed simulations with YOLO-style bounding box detection
- Each feed shows: vehicle density count, detected objects, green/red bounding boxes per vehicle
- CRT scanline effect with live timestamp overlay and `⬤ REC` indicator
- Per-feed density badge: `L5 HEAVY` / `MOD` / `CLEAR`
- Directly implements the PS-4 requirement for computer vision vehicle density and flow rate extraction

### 🔀 4 Toggleable Map Layers
| Layer | What It Shows |
|---|---|
| `CONGESTION` | Road network colored by jam level — real-time GPS ground truth |
| `PREDICTION` | Dashed purple LSTM-predicted jam propagation corridors |
| `CCTV ZONES` | Camera coverage radii showing visual ground-truth footprint |
| `FLOW VECTORS` | Animated directional arrows showing vehicle inflow/outflow per junction |

### 🔗 Cross-Modal Fusion Layer
- Per-junction fusion score combining CCTV computer vision confidence and GPS Maps confidence
- Three validation states per junction alert:
  - ✅ `CONFIRMED` — GPS congestion validated by CCTV vehicle density count
  - ❌ `FALSE POSITIVE ELIMINATED` — GPS alert invalidated by CCTV showing clear flow
  - ⚠️ `UNVERIFIED` — Insufficient CCTV evidence to confirm or deny
- Directly implements the PS-4 cross-modal correlation requirement to eliminate GPS false positives

### 📉 LSTM Standstill Probability Forecast
- Per-junction LSTM temporal forecast chart with historical line + predicted dashed overlay
- Horizon selector: **15 min / 30 min / 45 min** — matching PS-4 specification exactly
- Visual split between historical data and forecast region with `NOW` marker
- Color-coded probability output: green (safe) → amber (warning) → red (imminent L5)
- Implements the PS-4 requirement for RNN/LSTM-based standstill jam probability forecasting

### ⚡ Incident Injection
- `+ INJECT INCIDENT` button simulates a real traffic event at a clear junction
- Instantly updates jam level, density, speed, and ETA delay for that node
- Triggers a critical alert in the system feed with timestamp
- Auto-recovers to estimated state after 6 seconds — models the full incident lifecycle

### 📡 GPS Data Pipeline Monitor
- Live pipeline panel showing per-route GPS data latency in milliseconds
- Visualizes the data freshness problem described in PS-4 (Maps lag vs CCTV near-instant)
- 5 simulated GPS routes with real-time fill bars and latency readings

---

## Architecture

```
┌─────────────────────────┐     ┌──────────────────────────┐
│   CCTV / IP Cameras      │     │   Google Maps API         │
│   (Micro-View)           │     │   Traffic Layer (Macro)   │
│                          │     │                           │
│  YOLO / Faster R-CNN     │     │  Routes / Distance Matrix │
│  Vehicle Density Count   │     │  Real-Time ETAs           │
│  Flow Rate Extraction    │     │  Speed Data               │
│  Anomaly Detection       │     │  Congestion Alerts        │
└──────────┬───────────────┘     └──────────┬────────────────┘
           │                                │
           │  CV Score (%)                  │  GPS Score (%)
           │                                │
           └────────────┬───────────────────┘
                        ▼
           ┌────────────────────────┐
           │  Cross-Modal Fusion     │
           │  Validation Layer       │
           │                        │
           │  Fusion Score =        │
           │  (CV + GPS) / 2        │
           │                        │
           │  CONFIRMED /           │
           │  FALSE POSITIVE /      │
           │  UNVERIFIED            │
           └────────────┬───────────┘
                        │
                        ▼
           ┌────────────────────────┐
           │  LSTM Temporal Engine   │
           │                        │
           │  Input: t-1, t-2       │
           │  inflow/outflow trends  │
           │                        │
           │  Output: P(L5 jam)     │
           │  at 15 / 30 / 45 min   │
           └────────────┬───────────┘
                        │
                        ▼
           ┌────────────────────────┐
           │  Interactive GIS        │
           │  Dashboard              │
           │                        │
           │  City Map · CCTV Strip  │
           │  Fusion Meter · Alerts  │
           │  LSTM Forecast Chart    │
           └────────────────────────┘
```

### Jam Level Classification

| Level | Label | Speed | Vehicle Density | Response |
|---|---|---|---|---|
| L1 | Free Flow | > 50 km/h | < 20 /km² | Monitor |
| L2 | Light | 35–50 km/h | 20–40 /km² | Watch |
| L3 | Moderate | 20–35 km/h | 40–60 /km² | Alert |
| L4 | Heavy | 8–20 km/h | 60–80 /km² | Dispatch |
| L5 | Standstill | < 8 km/h | > 80 /km² | Emergency |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Rendering | HTML5 Canvas API — city grid, junction nodes, CCTV feeds |
| Frontend | Vanilla JavaScript — zero framework, zero dependencies |
| Fonts | Share Tech Mono + Barlow Condensed (Google Fonts) |
| Vision Simulation | Canvas-rendered YOLO bounding box detection per feed |
| LSTM Visualization | Canvas-rendered temporal probability chart |
| Fusion Engine | Cross-modal CV + GPS score validation layer |
| Deployment | GitHub Pages — single file, instant load |

---

## Problem Statement Coverage

| PS-4 Requirement | Implementation |
|---|---|
| CCTV computer vision — vehicle density and flow rate | YOLO-style bounding box simulation per camera feed |
| Google Maps geospatial data ingestion | GPS pipeline panel with per-route latency monitoring |
| Cross-modal correlation — eliminate false positives | Fusion layer with CONFIRMED / FALSE POSITIVE / UNVERIFIED states |
| LSTM temporal forecasting — 15–45 min standstill probability | LSTM chart with 3-horizon selector and dynamic probability output |
| Interactive GIS dashboard with clickable CCTV thumbnails | City map + CCTV strip + junction drill-down panel |
| Jam level classification L1–L5 | Enforced across all junction nodes with color coding and severity escalation |

---


## Author

**Devika Pavithran**
Built as part of a senior-level intelligence systems and AI engineering challenge.

---

<div align="center">
