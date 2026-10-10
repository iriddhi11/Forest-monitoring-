# Forest Monitoring — Beta Version
## Product Requirements Document (PRD)

| Metadata | Details |
| :--- | :--- |
| **Author** | Aashay Chahande |
| **Version** | Beta |
| **Date** | October 10, 2026 |
| **Status** | In Development |

---

## 1. Problem Statement

### 1.1 Context
- **Government of Maharashtra Problem Statement**
- **Core Mission**: Build technology to detect, prevent, and combat wildlife crimes including poaching, illegal wildlife trade, and cyber-based wildlife trafficking.

### 1.2 The Problem in Simple Words
Wildlife crimes occur rapidly in remote forests, predominantly under cover of night, and increasingly across cyber marketplaces. Forest departments often discover incidents days after they occur, resulting in compromised evidence and delayed responses.

### 1.3 Why It Is Hard
- **Vast Geographic Scale**: Forests span thousands of hectares with limited ranger coverage.
- **Nocturnal Activity**: Poachers predominantly operate during night hours.
- **Connectivity Gaps**: Remote forest tracts suffer from low or non-existent cellular coverage.
- **Weak Chains of Custody**: Late discovery leads to degraded on-ground physical evidence.
- **Cyber Anonymity**: Wildlife trade syndicates use coded vocabulary and throwaway accounts on digital platforms.

### 1.4 High-Impact Focus Areas
- **Maharashtra Tiger Reserves & Sanctuaries**: Tadoba-Andhari, Melghat, Pench, Navegaon-Nagzira.
- **Broader Vision**: Extensible across tiger reserves and protected wildlife corridors across India.

---

## 2. What We Are Asked to Build

A unified operational technology solution that can:
1. **Detect**: Identify wildlife crime and unauthorized intrusions in real time.
2. **Prevent**: Enable proactive dispatch and rapid containment using live data.
3. **Combat**: Preserve tamper-proof digital evidence and connect forest incidents directly to illegal trade supply chains.

The solution spans on-ground poaching prevention, physical trade monitoring, and cyber wildlife crime intelligence.

---

## 3. Proposed Solution

The **Forest Monitoring Beta** platform establishes a closed-loop detection and response ecosystem:
- Field cameras monitor sensitive wilderness corridors.
- A centralized command center visualizes alerts on a live geospatial map.
- The nearest on-duty forest officer is automatically assigned the incident via a low-bandwidth mobile web interface.
- Verified incident logs feed directly into a centralized **Wildlife Crime Intelligence Hub**, bridging field enforcement with cyber investigators and police authorities.

```
[ Camera Feed (Webcam / IP / IR) ]
               │
               ▼
   [ Motion Pre-Filter (OpenCV) ]
               │ (Motion Detected)
               ▼
      [ YOLOv8 Inference ]
  (Person / Vehicle / Animal)
               │ (Filtered & Confirmed)
               ▼
      [ FastAPI Backend ] ──────────────► [ Supabase DB & Realtime ]
         │            │                                  │
         │            ▼                                  ▼
         │   [ Officer Auto-Dispatch ]           [ Command Dashboard ]
         │   (Nearest Ranger Assignment)       (Map Pin, Snapshot, Logs)
         │            │
         ▼            ▼
[ Wildlife Crime Intelligence Hub ] ◄─── [ Mobile Web (Officer Field Status) ]
  (Case Dossier & Bulletin Export)
```

### 3.1 Operational Flow
1. **Camera Placement**: Positioned along arterial entry tracks, water holes, buffer zones, and sensitive poaching corridors.
2. **AI Monitoring**: Video streams are analyzed continuously for humans, unauthorized vehicles, and animals across day and night conditions.
3. **Live Alerting**: Triggered camera pins flash red on the command dashboard map with instant snapshots and detection metadata.
4. **Automated Field Dispatch**: The platform calculates proximity and assigns the nearest active ranger.
5. **Field Response (Zero-Install)**: Rangers access tasks and submit field updates via a lightweight mobile web application.
6. **Chain of Evidence**: Every intrusion snapshot, timestamp, and response record is preserved as an immutable log.
7. **Intelligence Sharing**: Confirmed crimes are syndicated to the Wildlife Crime Intelligence Hub to facilitate inter-departmental alerts and cyber trade monitoring.

### 3.2 Technical Architecture
- **Stream Ingestion**: Python OpenCV pipeline captures RTSP / video feeds.
- **Motion Pre-Filter**: Frame-differencing gatekeeper runs first; neural network inference triggers only upon motion to conserve compute resources.
- **Inference & Temporal Filtering**: YOLOv8 detects targets (`person`, `vehicle`, `animal`). Detections must sustain across consecutive frames above confidence thresholds to suppress environmental noise (leaves, wind, shadows).
- **Backend Orchestration**: FastAPI processes incoming detections, correlates camera coordinates against ranger GPS heartbeats, and dispatches tasks.
- **Day & Night Pipeline**:
  - *Day Mode*: Standard visible-spectrum cameras running forest-fine-tuned YOLOv8 models.
  - *Night Mode*: Active IR cameras processed through IR-optimized YOLOv8 weights.
  - *Future Enhancements*: Acoustic sensors (gunshot/chainsaw acoustic classifiers) and thermal imaging.

---

## 4. Technology Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Detection Engine** | Python, OpenCV, YOLOv8 (Ultralytics) | Video ingestion, motion filtering, real-time object detection |
| **Model Training** | Google Colab / Kaggle | Model fine-tuning and experiment tracking |
| **Training Datasets** | LILA BC (Camera Traps), COCO, Open Images | Forest and wildlife domain datasets annotated via Roboflow |
| **Backend API** | FastAPI (Python) | Alert ingestion, spatial assignment logic, REST endpoints (Deployed on Render) |
| **Frontend Dashboard** | React, Leaflet, OpenStreetMap | Interactive command center map, live alert feed (Deployed on Vercel) |
| **Database & Realtime** | Supabase (PostgreSQL) + Realtime | Incident persistence, officer positions, real-time state pub/sub |
| **Authentication** | Supabase Auth | Role-based access control (RBAC) |
| **Field Devices / Cameras** | Webcams & smartphones (demo); IP & IR camera traps (field) | Video feed input |
| **Intelligence Bulletins** | LLM API + PDF generation library | Automated law enforcement intelligence bulletins and export |

> **Deployment Note for Demo**: The vision inference pipeline executes locally on a dedicated workstation, as free-tier cloud instances lack adequate GPU/CPU throughput for real-time video processing.

---

## 5. Feature Modules

### Module 1: Live Detection Engine
1. Connects to diverse stream inputs (webcam, smartphone stream, RTSP/IP camera).
2. Real-time multi-class detection: `person`, `vehicle`, `animal`.
3. Motion-based pre-filter to reduce redundant inference cycles.
4. Temporal persistence and confidence thresholds to eliminate false positives from environmental movement.
5. Automatic Day / Night (Infrared) profile switching.
6. Structured alert persistence containing camera identifier, timestamp, bounding boxes, and snapshot imagery.

### Module 2: Command Center Dashboard
1. Virtual forest map plotting all deployed camera coordinates.
2. Real-time visual alert indicators: pulsating red pins displaying detection class, timestamp, and visual snapshot.
3. Live video monitoring feed with bounding-box overlays.
4. Historical incident log with multi-parameter filtering (by date range, camera, severity, and detection type).
5. Configurable detection polygons (geofencing specific camera zones to ignore benign background motion).
6. Camera management CRUD interface (add, reposition, and deactivate nodes).

### Module 3: Officer Tracking & Automated Dispatch
1. Secure officer authentication.
2. Live ranger GPS locations rendered on the central map.
3. Automated dispatch engine: assigns alerts to the geographically closest available officer.
4. Escalation timer: if the assigned officer does not acknowledge within 2–3 minutes, the task automatically routes to the next closest ranger.
5. Officer task progression status: `Accepted` ➔ `Reached` ➔ `Resolved`.
6. Complete response-time telemetry recorded for operational audits.

### Module 4: Ranger Mobile Web Portal
1. Mobile-optimized web portal (no app store installation needed for V1).
2. Secure GPS coordinate broadcasting and task inbox.
3. Alert details screen: incident snapshot, camera coordinates, and turn-by-turn navigation link.
4. Status update controls to log on-site arrival and case resolution.

### Module 5: Wildlife Crime Intelligence Hub
1. Comprehensive case docket: target species, seized contraband (claws, horn, skin, teeth), geo-coordinates, timestamp, and evidentiary photos.
2. Single-click case generation directly from an active camera alert.
3. Automated intelligence bulletin generator: creates structured dossiers containing contraband details, regional trade aliases, suspected smuggling routes, and exportable PDF summaries.
4. Centralized incident archive searchable by species, contraband type, administrative division, and date.
5. Spatial crime heatmaps highlighting recurring vulnerability zones.
6. Senior officer approval workflow to review and authorize intelligence before distribution.
7. Strict role-based permissions ensuring law enforcement data confidentiality.

---

## 6. User Roles & Access Control

| Role | Key Capabilities |
| :--- | :--- |
| **Forest Officer (Field Ranger)** | Broadcast live location, accept/update dispatched incidents, submit field case evidence. |
| **Control Room Admin / Senior Officer** | Full map oversight, camera management, override dispatch, review and approve intelligence bulletins. |
| **Cyber Cell & Police Liaison** | Read-only access to published intelligence bulletins, search contraband trends, append external investigation leads. |

---

## 7. Scope & Roadmap

### 7.1 Beta Scope (Current Deliverable)
- End-to-end functionality across Modules 1 through 5.
- Multi-camera demonstration setup (2–3 concurrent feeds).
- Core automated dispatch cycle with timeout re-assignment.
- Supabase-backed real-time alert sync and intelligence bulletin generation.

### 7.2 Future Roadmap (Post-Beta)
- **Acoustic & Thermal Sensing**: Integrated gunshot/chainsaw sound detection and thermal camera arrays.
- **Edge Deployment**: Solar-powered low-power edge compute units with offline LoRa / satellite SMS telemetry.
- **Poaching Weapon Detection**: Specialized computer vision models for firearms, axes, traps, and snares.
- **Native Ranger App**: Native Android/iOS applications with offline geocaching and background telemetry.
- **Cyber Lead Ingestion**: Bi-directional integration allowing cyber units to append market intelligence back to field dockets.
- **Predictive Analytics**: Spatial-temporal poaching heatmaps and trend forecasting per species and seasonal migration.
- **Inter-Agency Data Exchange**: Secure federation with National Wildlife Crime Control Bureau (WCCB), Customs, and neighboring state divisions.

---

## 8. Key Success Metrics

- **Latency**: End-to-end alert delivery onto command dashboard within **< 3 seconds** of physical event.
- **Accuracy**: Minimal false alarm rates under challenging foliage, wind, and shadow conditions.
- **Dispatch Reliability**: 100% deterministic assignment of alerts to the nearest active ranger.
- **Complete Verification Loop**: Unbroken pipeline from camera detection ➔ alert trigger ➔ officer dispatch ➔ field resolution ➔ intelligence bulletin generation.
