# 🛡️ SleepGuard Pro — AI-Powered Drowsiness Detection System

> Real-time fatigue monitoring using computer vision, signal processing, and AI-generated session analysis. Built as an Infosys Capstone 2026 project.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat-square&logo=python)
![OpenCV](https://img.shields.io/badge/OpenCV-4.8%2B-green?style=flat-square&logo=opencv)
![MediaPipe](https://img.shields.io/badge/MediaPipe-0.10%2B-orange?style=flat-square)
![Flask](https://img.shields.io/badge/Flask-3.0%2B-lightgrey?style=flat-square&logo=flask)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

---

## 📌 What It Does

SleepGuard Pro monitors a person's face in real time through any webcam and detects drowsiness before it becomes dangerous. It is designed for drivers, factory workers, security personnel, and anyone who must stay alert during extended tasks.

The system computes five independent physiological signals every frame and fuses them into a single **Drowsiness Score (0–100)**. When the score crosses configurable thresholds, it escalates through three alarm levels — visual, audio, and voice — and sends desktop notifications.

At session end it auto-generates a detailed HTML fatigue report, optionally enriched with AI-powered analysis from the Claude API.

---

## 🎥 Demo

| Live HUD | Web Dashboard | Fatigue Report |
|----------|--------------|----------------|
| Real-time EAR/MAR/PERCLOS overlaid on webcam | Flask SSE dashboard at `localhost:5050` | Auto-generated HTML with charts and AI insights |

---

## 🧠 How It Works — Detection Pipeline

```
Webcam Frame
     │
     ▼
MediaPipe FaceMesh  (468 landmarks, 30 fps)
     │
     ├──► EAR  (Eye Aspect Ratio)          — eye closure detection
     ├──► MAR  (Mouth Aspect Ratio)        — yawn detection
     ├──► Head Pitch  (solvePnP)           — nodding / microsleep
     ├──► PERCLOS  (rolling window)        — % time eyes closed
     └──► Blink Rate  (rising-edge count)  — fatigue / stress indicator
          │
          ▼
     Composite Score  =  EAR(40%) + PERCLOS(30%) + Yawns(20%) + Nod(10%)
          │
          ├──► Level 0  — AWAKE
          ├──► Level 1  — DROWSY   (eyes closed > 1.5 s)   → gentle alarm
          ├──► Level 2  — ALERT!   (eyes closed > 3.0 s)   → urgent alarm + voice
          └──► Level 3  — WAKE UP! (eyes closed > 4.5 s)   → critical alarm + notification
```

### Key Algorithms

**EAR (Eye Aspect Ratio)**
```
EAR = (‖p1–p5‖ + ‖p2–p4‖) / (2 × ‖p0–p3‖)
```
A drop below the calibrated personal threshold indicates eye closure.

**PERCLOS** — Percentage of Eye Closure over a rolling 60-second window. The NHTSA validated PERCLOS > 15% as a clinical drowsiness marker.

**Adaptive Calibration** — The system spends the first ~3 seconds learning each user's natural open-eye EAR, including glasses wearers, so false positives are minimised.

**Face-ID Lock** — A lightweight 12-pair landmark embedding prevents the system from being "fooled" by a different person sitting down mid-session.

---

## 🚀 Features

- **Real-time detection** at 30 fps on standard webcam hardware
- **5-signal fusion** — EAR, MAR, Head Pitch, PERCLOS, Blink Rate
- **3-level escalating alarms** — synthesised in code (no audio files required)
- **Voice alerts** — offline TTS (pyttsx3), speaks warnings aloud
- **Adaptive personal calibration** — works with glasses
- **Face-ID lock** — single-user session enforcement
- **Live web dashboard** — Flask + SSE at `http://localhost:5050`
- **Auto-generated HTML fatigue report** — charts, rings, episode table
- **AI-powered report panel** — Claude API analyses your session vs history
- **Historical trend chart** — compares all past sessions
- **Export to PDF** — one-click browser print from the report
- **CSV session log** — persistent history across sessions
- **Desktop notifications** — plyer integration
- **Multi-language UI messages** — configurable in `config.py`

---

## 📁 Project Structure

```
SleepGuard-Pro-Extended/
│
├── sleep_guard.py          ← Main entry point
├── config.py               ← All thresholds and settings
├── requirements.txt
│
├── core/
│   ├── detector.py         ← EAR · MAR · PERCLOS · Head-Pose · Score
│   ├── alarm_engine.py     ← Synthesised escalating tones (pygame)
│   ├── blink_counter.py    ← Rolling blink rate (blinks/min)
│   ├── face_id.py          ← Landmark-embedding face lock
│   ├── notifications.py    ← Desktop push notifications (plyer)
│   ├── session_tracker.py  ← Episode tracking · CSV log · summary
│   ├── ui_renderer.py      ← All OpenCV HUD drawing
│   ├── voice_alerts.py     ← Offline TTS voice warnings (pyttsx3)
│   ├── ai_analyst.py       ← Claude API session analysis
│   └── report_gen_v2.py    ← HTML report with AI panel + history chart
│
├── dashboard/
│   └── server.py           ← Flask + SSE live dashboard
│
└── logs/
    ├── sleepguard_session.csv   ← Session history
    └── sleepguard_report.html   ← Latest fatigue report
```

---

## ⚙️ Installation

### Prerequisites
- Python 3.10 or higher
- A webcam
- Windows / macOS / Linux

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/sleepguard-pro.git
cd sleepguard-pro

# 2. Create a virtual environment (recommended)
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # macOS / Linux

# 3. Install dependencies
pip install -r requirements.txt

# 4. (Optional) Set API key for AI-powered report
set ANTHROPIC_API_KEY=sk-ant-your-key-here      # Windows
export ANTHROPIC_API_KEY=sk-ant-your-key-here   # macOS / Linux

# 5. Run
python sleep_guard.py
```

### Run Options

| Flag | Effect |
|------|--------|
| `--muted` | Silence alarm tones |
| `--no-voice` | Disable TTS voice alerts |
| `--no-ai` | Skip Claude AI analysis (faster report) |
| `--camera 1` | Use a different camera index |

---

## 🔧 Configuration

All tunable parameters are in `config.py`:

```python
EAR_THRESHOLD        = 0.22     # Starting EAR threshold (auto-calibrated)
EAR_CONFIDENCE_BAND  = 0.03     # Calibration tolerance band
MAR_THRESHOLD        = 0.55     # Mouth open = yawn
HEAD_PITCH_THRESHOLD = 15.0     # Degrees of nod
PERCLOS_WINDOW_SEC   = 60       # PERCLOS rolling window
ALARM_LEVEL_SECONDS  = [1.5, 3.0, 4.5]   # Thresholds for levels 1/2/3
BLINK_WINDOW_SEC     = 60       # Blink rate window
NORMAL_BLINK_RATE    = (12, 20) # Normal blinks/min range
FACE_ID_ENABLED      = True     # Face-ID lock on/off
FACE_ID_THRESHOLD    = 0.92     # Cosine similarity threshold
DESKTOP_NOTIFICATIONS = True
ENABLE_LOGGING       = True
```

---

## 📊 Signal Reference

| Signal | Metric | Normal Range | Drowsy Threshold |
|--------|--------|-------------|-----------------|
| EAR | Eye Aspect Ratio | 0.25 – 0.35 | < calibrated baseline |
| MAR | Mouth Aspect Ratio | < 0.45 | > 0.55 |
| Head Pitch | Degrees | -5° to +5° | < -15° |
| PERCLOS | % closed / 60 sec | < 10% | > 15% (NHTSA) |
| Blink Rate | blinks/min | 12 – 20 | < 12 or > 20 |

---

## 🤖 AI Analysis (Claude API)

When `ANTHROPIC_API_KEY` is set, the session report includes:

- **Risk Level** — LOW / MODERATE / HIGH / CRITICAL with animated badge
- **Alertness Score** — out of 10
- **Key Findings** — three data-backed observations
- **Root Cause** — personalised explanation of the fatigue pattern
- **Trend Analysis** — comparison against your historical sessions
- **Action Plan** — IMMEDIATE / SHORT-TERM / LONG-TERM recommendations
- **Medical Note** — references PERCLOS, EAR, and microsleep science

Without an API key the system falls back to rule-based recommendations automatically.

---

## 🛣️ Roadmap

- [ ] Multi-face / fleet monitoring mode
- [ ] Mobile companion app (push alerts to phone)
- [ ] GPS + speed integration for vehicles
- [ ] Edge deployment (Raspberry Pi / Jetson Nano)
- [ ] Exportable PDF via WeasyPrint (server-side)
- [ ] REST API for enterprise integration

---

## 🧪 Tech Stack

| Layer | Technology |
|-------|-----------|
| Vision | OpenCV 4.8, MediaPipe FaceMesh |
| Numerics | NumPy, SciPy |
| Audio | pygame (synthesised tones) |
| Voice | pyttsx3 (offline TTS) |
| Web dashboard | Flask, SSE, Chart.js |
| AI analysis | Anthropic Claude API |
| Notifications | plyer |
| Reporting | Jinja-style f-strings → HTML |

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgements

- [MediaPipe](https://mediapipe.dev/) — FaceMesh landmark detection
- [OpenCV](https://opencv.org/) — Computer vision framework
- [Anthropic](https://www.anthropic.com/) — Claude AI API
- NHTSA PERCLOS research — drowsiness clinical benchmarks
- Infosys Capstone Program 2026

---

*Built with ❤️ as an Infosys Capstone 2026 project.*
