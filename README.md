<div align="center">

<img src="https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&size=28&pause=1000&color=3B82F6&center=true&vCenter=true&width=700&lines=SleepGuard+Pro+%E2%80%94+Extended+Edition;Real-time+Drowsiness+Detection+%F0%9F%9B%A1%EF%B8%8F;5+Signals+%C2%B7+3+Alarm+Levels+%C2%B7+AI+Report;Zero+Game+Engine+%C2%B7+Pure+Python+%F0%9F%90%8D" alt="Typing SVG" />

<br/>

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-4.8%2B-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![MediaPipe](https://img.shields.io/badge/MediaPipe-0.10%2B-FF6F00?style=for-the-badge&logo=google&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.0%2B-000000?style=for-the-badge&logo=flask&logoColor=white)
![Claude AI](https://img.shields.io/badge/Claude_AI-Anthropic-D97706?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-22C55E?style=for-the-badge)
![Stars](https://img.shields.io/github/stars/upendra8690/sleepguard-pro?style=for-the-badge&color=FFD700)

<br/>

> **5 physiological signals · 3 escalating alarm levels · AI-powered fatigue report**
>
> *Built for drivers, factory workers, and security personnel who cannot afford to fall asleep.*

<br/>

**[🚀 Quick Start](#-quick-start) · [🧠 How It Works](#-how-it-works) · [🎮 Features](#-features) · [📊 Dashboard](#-live-dashboard) · [🤖 AI Report](#-ai-powered-report)**

</div>

---

## 🎬 Demo

> **Record a 2-minute screen capture and replace this line with your GIF.**
> Use `Win + Alt + R` to record, then convert at [ezgif.com/video-to-gif](https://ezgif.com/video-to-gif)

| 🎥 Live HUD | 📊 Web Dashboard | 📄 AI Report |
|:-----------:|:----------------:|:------------:|
| Real-time EAR · MAR · PERCLOS overlaid on webcam | Flask + SSE at `localhost:5050` | Auto-generated HTML with AI insights + PDF export |

---

## 🛡️ The Problem This Solves

> **20% of road accidents** are caused by drowsy driving (NHTSA).
> **37% of adults** report unintentionally falling asleep during the day.

Existing solutions require expensive hardware. SleepGuard Pro runs on **any standard webcam** — no sensors, no wearables, no cloud dependency.

---

## 🧠 How It Works

```
📷  Webcam Frame  (30 fps)
         │
         ▼
🤖  MediaPipe FaceMesh  →  468 facial landmarks in real time
         │
         ├─── 👁  EAR   Eye Aspect Ratio         eye closure detection
         ├─── 😮  MAR   Mouth Aspect Ratio        yawn detection
         ├─── 📐  Pitch  Head-Pose (solvePnP)     nodding / microsleep
         ├─── 📊  PERCLOS  rolling 60-sec window  % time eyes closed
         └─── 💡  Blink Rate  rising-edge count   fatigue vs stress
                   │
                   ▼
         ┌─────────────────────────────────────────┐
         │   Composite Drowsiness Score  (0 – 100) │
         │                                         │
         │   EAR component    ×  40%               │
         │   PERCLOS          ×  30%               │
         │   Yawn count       ×  20%               │
         │   Head nod         ×  10%               │
         └─────────────────────────────────────────┘
                   │
         ┌─────────┼──────────┬───────────┐
         ▼         ▼          ▼           ▼
      Level 0   Level 1    Level 2     Level 3
      AWAKE     DROWSY     ALERT!    WAKE UP!
                1.5 sec    3.0 sec    4.5 sec
               gentle    urgent    critical
               alarm   alarm+voice  alarm+voice
                                   +notification
```

### 📐 Core Algorithms

**Eye Aspect Ratio (EAR)**
```
        ‖ p₁–p₅ ‖  +  ‖ p₂–p₄ ‖
EAR  =  ─────────────────────────
               2 × ‖ p₀–p₃ ‖
```
A drop below the user's personal calibrated threshold indicates eye closure.  
EAR < threshold for **1.5 s → Level 1**, **3.0 s → Level 2**, **4.5 s → Level 3**.

**PERCLOS** — Percentage Eye Closure over a rolling window.  
The NHTSA clinically validated **PERCLOS > 15%** as a drowsiness marker.

**Adaptive Calibration** — The system spends the first ~3 seconds learning your natural open-eye EAR, correcting for glasses, lighting, and facial geometry. No manual configuration needed.

**Face-ID Lock** — A 12-pair landmark embedding (cosine similarity) prevents a different person from taking over mid-session. Single-user session enforcement without any deep learning model.

---

## ✨ Features

```
┌──────────────────────────────────────────────────────────────────┐
│  DETECTION                      │  ALERTS                        │
├─────────────────────────────────┼────────────────────────────────┤
│  ✅ EAR — eye closure           │  🔊 3-level synthesised alarms │
│  ✅ MAR — yawn detection        │  🗣  TTS voice alerts (offline) │
│  ✅ PERCLOS — 60-sec window     │  🖥  Desktop notifications      │
│  ✅ Head pitch — nod detection  │  📱 Flash red overlay          │
│  ✅ Blink rate — fatigue signal │                                │
├─────────────────────────────────┼────────────────────────────────┤
│  INTELLIGENCE                   │  OUTPUT                        │
├─────────────────────────────────┼────────────────────────────────┤
│  ✅ Adaptive calibration        │  📊 Live Flask SSE dashboard   │
│  ✅ Face-ID session lock        │  📄 Auto HTML fatigue report   │
│  ✅ Claude AI analysis          │  🤖 AI-powered insights panel  │
│  ✅ EMA score smoothing         │  📈 Historical trend chart     │
│  ✅ Multi-language HUD          │  🖨  One-click PDF export      │
│  ✅ PERCLOS sparkline HUD       │  💾 CSV session log            │
└─────────────────────────────────┴────────────────────────────────┘
```

---

## 📁 Project Structure

```
SleepGuard-Pro-Extended/
│
├── 🐍 sleep_guard.py           ← Main entry point
├── ⚙️  config.py                ← All thresholds & settings
├── 📦 requirements.txt
│
├── core/
│   ├── detector.py             EAR · MAR · PERCLOS · Head-Pose · Score
│   ├── alarm_engine.py         Synthesised tones — no audio files needed
│   ├── blink_counter.py        Rolling blink rate (blinks/min)
│   ├── face_id.py              Landmark-embedding face lock
│   ├── notifications.py        Desktop push notifications
│   ├── session_tracker.py      Episode tracking · CSV · per-minute buckets
│   ├── ui_renderer.py          All OpenCV HUD drawing
│   ├── voice_alerts.py         ★ NEW — offline TTS voice warnings
│   ├── ai_analyst.py           ★ NEW — Claude API session analysis
│   └── report_gen_v2.py        ★ NEW — HTML report + AI panel + history
│
├── dashboard/
│   └── server.py               Flask + SSE real-time dashboard
│
└── logs/
    ├── sleepguard_session.csv   Persistent session history
    └── sleepguard_report.html   Latest auto-generated report
```

---

## 🚀 Quick Start

```bash
# 1. Clone
git clone https://github.com/upendra8690/sleepguard-pro.git
cd sleepguard-pro

# 2. Virtual environment (recommended)
python -m venv venv
.\venv\Scripts\Activate.ps1     # Windows
source venv/bin/activate         # macOS / Linux

# 3. Install
pip install -r requirements.txt

# 4. (Optional) AI-powered report — set your API key
set ANTHROPIC_API_KEY=sk-ant-your-key-here     # Windows
export ANTHROPIC_API_KEY=sk-ant-your-key-here  # macOS / Linux

# 5. Run
python sleep_guard.py
```

> ⚠️ **Webcam required.** If you have multiple cameras, try `--camera 1`

### Run Options

| Flag | Effect |
|------|--------|
| `--muted` | Silence alarm tones |
| `--no-voice` | Disable TTS voice alerts |
| `--no-ai` | Skip Claude AI analysis |
| `--camera N` | Use camera index N |

---

## 🎮 Controls

```
┌──────────────────────────────────────────────────────────────────┐
│  KEY / EVENT              │  ACTION                              │
├───────────────────────────┼──────────────────────────────────────┤
│  Eyes closed > 1.5 s      │  Level 1 — gentle alarm + voice     │
│  Eyes closed > 3.0 s      │  Level 2 — urgent alarm + voice     │
│  Eyes closed > 4.5 s      │  Level 3 — critical + notification  │
│  Yawn detected            │  Voice: "Yawning detected…"         │
│  Head nod detected        │  Voice: "Head nodding detected…"    │
├───────────────────────────┼──────────────────────────────────────┤
│  Q                        │  Quit and save session              │
│  Browser: localhost:5050  │  Live web dashboard                 │
└──────────────────────────────────────────────────────────────────┘
```

---

## 📊 Live Dashboard

Open **`http://localhost:5050`** in any browser while the system is running.

- Real-time drowsiness score ring (animated)
- EAR · MAR · Pitch · PERCLOS live values
- Active signal indicators (eyes / yawn / nod / stress)
- Blink rate progress bar with normal-range markers
- Live score timeline chart (last 300 samples, SSE-streamed)
- Session elapsed time · episode count · head nod counter

---

## 🤖 AI-Powered Report

After each session, an HTML report is auto-saved to `logs/sleepguard_report.html`.

**With `ANTHROPIC_API_KEY` set, the report includes:**

```
┌──────────────────────────────────────────────────┐
│  🤖 AI ANALYSIS  —  Powered by Claude · Anthropic │
├──────────────────────────────────────────────────┤
│  Risk Level      LOW / MODERATE / HIGH / CRITICAL │
│  Alertness Score  out of 10                       │
│  Key Findings    3 data-backed observations       │
│  Root Cause      personalised fatigue explanation │
│  Trend Analysis  vs your historical sessions      │
│  Action Plan     IMMEDIATE / SHORT / LONG TERM    │
│  Medical Note    PERCLOS, EAR, microsleep science │
└──────────────────────────────────────────────────┘
```

Without an API key, the system falls back to rule-based recommendations automatically — no crash, no error.

---

## ⚙️ Configuration

All parameters in `config.py` — no code changes needed:

```python
# Detection thresholds
EAR_THRESHOLD        = 0.22      # auto-calibrated per user
MAR_THRESHOLD        = 0.55      # yawn detection
HEAD_PITCH_THRESHOLD = 15.0      # degrees of nod
PERCLOS_WINDOW_SEC   = 60        # rolling window

# Alarm timing (seconds of eye closure)
ALARM_LEVEL_SECONDS  = [1.5, 3.0, 4.5]   # Level 1 / 2 / 3

# Blink rate
BLINK_WINDOW_SEC     = 60
NORMAL_BLINK_RATE    = (12, 20)  # blinks/min

# Security
FACE_ID_ENABLED      = True
FACE_ID_THRESHOLD    = 0.92      # cosine similarity

# Features
DESKTOP_NOTIFICATIONS = True
ENABLE_LOGGING        = True
```

---

## 📊 Signal Reference

| Signal | What It Measures | Normal Range | Drowsy Threshold |
|--------|-----------------|--------------|-----------------|
| EAR | Eye openness ratio | 0.25 – 0.35 | < personal baseline |
| MAR | Mouth opening ratio | < 0.45 | > 0.55 |
| Head Pitch | Forward head tilt | –5° to +5° | < –15° |
| PERCLOS | % eyes closed / 60 s | < 10% | > 15% (NHTSA) |
| Blink Rate | Blinks per minute | 12 – 20 | < 12 or > 20 |

---

## 🔧 Troubleshooting

| Problem | Solution |
|---------|----------|
| Camera not found | Try `--camera 1` (multiple cameras) |
| `mediapipe` attribute error | `pip install mediapipe==0.10.8 protobuf==3.20.3` |
| No voice alerts | `pip install pyttsx3` |
| No AI report panel | Set `ANTHROPIC_API_KEY` environment variable |
| Low performance | Reduce `FRAME_WIDTH` in `config.py` |

---

## 🛣️ Roadmap

- [ ] Mobile companion app — push alerts to phone
- [ ] GPS + speed integration for vehicles
- [ ] Multi-face / fleet monitoring view
- [ ] Edge deployment — Raspberry Pi / Jetson Nano
- [ ] REST API for enterprise integration
- [ ] Server-side PDF export (WeasyPrint)

---

## 🧪 Tech Stack

| Layer | Technology |
|-------|-----------|
| Face landmark detection | MediaPipe FaceMesh (468 points) |
| Computer vision | OpenCV 4.8 |
| Head pose estimation | OpenCV `solvePnP` |
| Numerics | NumPy |
| Alarm synthesis | pygame (no audio files) |
| Voice alerts | pyttsx3 (offline TTS) |
| Live dashboard | Flask + Server-Sent Events |
| Charts | Chart.js |
| AI analysis | Anthropic Claude API |
| Notifications | plyer |

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgements

- [MediaPipe](https://mediapipe.dev/) by Google — real-time landmark detection
- [OpenCV](https://opencv.org/) — computer vision framework
- [Anthropic](https://www.anthropic.com/) — Claude AI API
- NHTSA PERCLOS Research — clinical drowsiness benchmarks

---

## 👨‍💻 Author

<div align="center">

**Mopuru Upendra Reddy**

[![GitHub](https://img.shields.io/badge/GitHub-upendra8690-181717?style=for-the-badge&logo=github)](https://github.com/upendra8690)

*"Built with Python, signal processing math, and zero shortcuts."*

</div>

---

<div align="center">

⭐ **If this project helped you, drop a star — it helps others find it too!** ⭐

*Built as an Infosys Capstone 2026 project (Upendra).*

</div>
