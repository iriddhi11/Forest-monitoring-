# Forest Monitoring (Beta)

> **Real-time AI surveillance, automated ranger dispatch, and wildlife crime intelligence platform for forest departments.**

---

## 📌 Overview

Forest departments in India manage vast wilderness areas with limited ranger personnel. Wildlife crimes—ranging from illegal intrusion and nocturnal poaching to contraband trafficking—occur swiftly in remote locations with poor connectivity, often leaving authorities with compromised evidence discovered days late.

Developed in response to the **Government of Maharashtra Problem Statement**, **Forest Monitoring Beta** provides a unified command, surveillance, and response system tailored for high-priority conservation zones such as **Tadoba-Andhari, Melghat, Pench, and Navegaon-Nagzira**.

The platform bridges real-time camera computer vision, instant officer dispatch, and a shared **Wildlife Crime Intelligence Hub** to link physical forest intrusions with cyber wildlife crime investigations.

---

## ⚡ How It Works

```
[ Camera Trap / Stream ]
          │
          ▼
 [ Motion Pre-Filter ]  ──(Motion Detected)──► [ YOLOv8 Detection ]
                                                      │ (Person / Vehicle / Animal)
                                                      ▼
                                              [ FastAPI Backend ]
                                                 │         │
                   ┌─────────────────────────────┘         └─────────────────────────────┐
                   ▼                                                                     ▼
       [ Command Center Map ]                                                 [ Nearest Ranger Dispatch ]
(Live Blinking Pin, Snapshot & Video)                                           (Assigned in < 3 sec)
                   │                                                                     │
                   └─────────────────────────────┬───────────────────────────────────────┘
                                                 ▼
                               [ Wildlife Crime Intelligence Hub ]
                                (Dossier, Market Aliases & Bulletins)
```

1. **Edge Detection**: Cameras placed at sensitive forest routes and waterholes continuously monitor feeds using OpenCV and YOLOv8.
2. **False Alarm Filtering**: A motion pre-filter reduces compute overhead, while temporal frame confirmation filters wind, swaying vegetation, and shadows.
3. **Live Command Dashboard**: Detected events blink instantly on an interactive forest map with photographic snapshots and metadata.
4. **Smart Dispatch**: The system calculates ranger proximity and assigns the nearest active officer via a zero-install mobile web page. If unaccepted within 2–3 minutes, the task escalates to the next nearest officer.
5. **Intelligence Syndication**: Incidents are converted with one click into structured case dossiers and intelligence bulletins for cyber cells and law enforcement.

---

## 🚀 Core Modules

| Module | Description |
| :--- | :--- |
| **1. Live Detection Engine** | Multi-class detection (`person`, `vehicle`, `animal`), motion pre-filtering, day & IR night modes. |
| **2. Command Center Dashboard** | Interactive Leaflet map with live camera pins, real-time alert popups, bounding-box video feeds, and historical logs. |
| **3. Officer Dispatch Engine** | Live ranger tracking, nearest-officer automated routing, 2–3 minute timeout auto-escalation, and response-time auditing. |
| **4. Ranger Mobile Web Portal** | Lightweight mobile portal for field officers—shares GPS location, receives alert snapshots, and logs status (`Accepted`, `Reached`, `Resolved`). |
| **5. Wildlife Crime Intelligence Hub** | Centralized case repository, contraband tagging, auto-generated intelligence bulletins (PDF/link), and poaching hotspot heatmaps. |

---

## 🛠️ Tech Stack

- **Computer Vision**: Python, OpenCV, YOLOv8 (Ultralytics)
- **Model Training & Datasets**: Google Colab / Kaggle, LILA BC Camera Traps, COCO, Roboflow
- **Backend API**: FastAPI (Python), deployed on Render
- **Frontend Dashboard**: React, Leaflet, OpenStreetMap, deployed on Vercel
- **Database & Realtime**: Supabase (PostgreSQL) + Supabase Realtime
- **Authentication & RBAC**: Supabase Auth (Forest Officer, Control Room Admin, Cyber Cell)
- **Bulletins & Reporting**: LLM API for descriptive bulletin drafting, PDF export engine

---

## 👥 User Roles

- **Field Forest Officer**: Broadcasts location, receives incident assignments, updates response stages, and logs field evidence.
- **Control Room Admin / Senior Officer**: Oversees full forest map, manages camera hardware configurations, and authorizes intelligence bulletins.
- **Cyber Cell & Police Liaison**: Accesses published intelligence bulletins to cross-reference seized contraband with online black-market trafficking networks.

---

## 🎯 Success Metrics

- **Sub-3-second Alert Latency**: Instant notification on the command dashboard from the moment of detection.
- **Low False Alarm Rate**: Verified multi-frame persistence to eliminate environmental noise.
- **Deterministic Ranger Dispatch**: Automated assignment to the nearest active patrol officer.
- **Closed-Loop Workflow**: Seamless pipeline from detection ➔ alert ➔ dispatch ➔ resolution ➔ intelligence dossier.

---

## 📄 Documentation

For full technical specifications, operational workflows, and future roadmap, refer to the complete [Product Requirements Document (PRD)](./prd.md).