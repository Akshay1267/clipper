# ⚡ Viral Clipper — AI 9:16 Shorts & Reels Generator

> Automatically convert long videos, live streams, and podcasts into viral 9:16 portrait Shorts/Reels with word-by-word animated captions (Hinglish / English), AI moment selection, split-webcam reframing, and click-worthy titles.

- **Developer & Maintainer**: Akshay Jain ([@Akshay1267](https://github.com/Akshay1267))
- **Original Creator**: Mr BitBloX ([YouTube @MrBitBloX](https://www.youtube.com/@MrBitBloX) · [Instagram @ps5_pov_](https://www.instagram.com/ps5_pov_/))

---

## 🌟 Key Features

- **Multi-source Ingestion**: Paste any YouTube link, paste a live stream URL, or upload local video files (`.mp4`, `.mov`, `.mkv`).
- **AI Moment Scoring**: Uses Google Gemini to analyze transcript context, identify 3-second opening hooks, rate virality from 0 to 100, and filter out intro/outro filler and sponsor reads.
- **Smart 9:16 Reframing**:
  - **Split-cam layout**: Automatically detects facecams and places webcam on top with gameplay/screen below.
  - **Center fit**: Dynamic blurred-background portrait mode for non-facecam videos.
- **Animated Word-by-Word Captions**:
  - Supports **Hinglish** (Hindi in English letters) and **English** with auto-language detection.
  - 9 animated caption presets (*Streamer, Hormozi, Beast, Neon, Boxed, Karaoke, Fire, Meme, Clean*).
  - 8 embedded creator fonts with customizable size, colors, and vertical placement.
- **Local & Fast**:
  - Runs speech recognition via `faster-whisper` (CTranslate2).
  - Acceleration on NVIDIA GPUs with CUDA & NVENC encoding; graceful fallback to CPU.
  - Web dashboard runs on Next.js 14 and auto-manages the background Python backend.

---

## 🏗️ Architecture & Tech Stack

```text
┌─────────────────────────────────┐
│     Next.js 14 Web Frontend     │  (Port 3000)
│   React 18 + Tailwind + Lucide  │
└────────────────┬────────────────┘
                 │ HTTP / REST
┌────────────────▼────────────────┐
│      Python Backend Server      │  (Port 8765)
│  Faster-Whisper (Audio & Words) │
│  OpenCV (Face / Camera Split)   │
│  FFmpeg (Video Slicing & Sub)   │
│  Google Gemini (Virality Judge) │
└─────────────────────────────────┘
```

- **Frontend**: Next.js 14, React 18, Tailwind CSS, Lucide Icons, TanStack Query.
- **Backend & ML**: Python 3.11, Faster-Whisper, Google Generative AI (Gemini), OpenCV (YuNet face detection), FFmpeg, yt-dlp, Librosa, Pydantic.

---

## 📋 Prerequisites

| Requirement | Supported Versions | Installation Command (Windows) | Installation Command (macOS) |
|---|---|---|---|
| **Python** | 3.10 – 3.12 (3.11 recommended) | `winget install Python.Python.3.11` | `brew install python@3.11` |
| **Node.js** | 18+ (LTS recommended) | `winget install OpenJS.NodeJS.LTS` | `brew install node` |
| **FFmpeg** | Full build with libraries | `winget install Gyan.FFmpeg` | `brew install ffmpeg` |
| **NVIDIA GPU** | *(Optional)* CUDA 12 support | GeForce Game Ready / Studio Driver | N/A (runs on CPU) |

---

## 🚀 Quick Start Guide

### 1. Clone the Repository
```bash
git clone https://github.com/Akshay1267/clipper.git
cd clipper
```

### 2. Run Setup (One-time)
This script sets up the Python virtual environment (`.venv`), installs ML dependencies, installs frontend packages, and generates `backend/.env`.

- **Windows**:
  ```powershell
  powershell -ExecutionPolicy Bypass -File setup_windows.ps1
  ```
- **macOS / Linux**:
  ```bash
  bash setup_mac_linux.sh
  ```

### 3. Add Your Free Gemini Key (Recommended)
Get a free Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey). Open `backend/.env` and paste:
```env
GEMINI_API_KEY=your_actual_gemini_api_key_here
```

### 4. Start the Application
- **Windows**:
  Double-click `start_windows.bat` (or execute `.\start_windows.bat` in terminal).
- **macOS / Linux**:
  ```bash
  ./start_mac_linux.sh
  ```

Open your browser at **[http://localhost:3000](http://localhost:3000)**.

---

## ⚙️ Configuration (`backend/.env`)

| Key | Default | Description |
|---|---|---|
| `GEMINI_API_KEY` | *empty* | Google Gemini API key for virality judging, titles, and Hinglish spelling. |
| `GEMINI_MODEL` | `gemini-flash-lite-latest` | Primary Gemini model for clip evaluation. |
| `WHISPER_MODEL` | `auto` | Speech recognition model size (`small`, `medium`, `large-v3`, `large-v3-turbo`). |
| `WHISPER_DEVICE` | `auto` | Execution device: `cuda` (NVIDIA GPU), `cpu`, or `auto`. |
| `TRANSCRIBE_LANGUAGE` | `auto` | Primary spoken language: `auto`, `hi` (Hindi), or `en` (English). |
| `CAPTION_SCRIPT` | `roman` | `roman` for Hinglish (Latin letters) or `native` for Devanagari script. |
| `DEFAULT_CAPTION_STYLE` | `streamer` | Caption aesthetic (`streamer`, `hormozi`, `beast`, `neon`, `boxed`, `karaoke`, `fire`, `meme`, `clean`). |
| `VIDEO_ENCODER` | `auto` | Hardware encoding: `auto` (uses NVENC if present, else `libx264`). |
| `TITLE_OVERLAY` | `1` | `1` to burn title banner at the top of the short, `0` to disable. |

---

## 💻 CLI Mode (Alternative)

You can also run the clipper directly from the command line without the web interface:

```bash
# Activate virtual environment
.\.venv\Scripts\activate      # Windows
source .venv/bin/activate     # macOS/Linux

cd backend
python app.py "https://www.youtube.com/watch?v=VIDEO_ID" --clips 5 --min 20 --max 60 --style streamer --language auto
```

### Useful CLI Flags:
- `--clips N`: Number of clips to generate (minimum 2).
- `--min SEC` / `--max SEC`: Clip duration constraints in seconds.
- `--start SEC` / `--end SEC`: Time range slice for long streams.
- `--style STYLE`: Caption style preset.
- `--no-title`: Disable the title overlay banner.

---

## 📁 Project & Output Structure

```text
clipper/
├── backend/
│   ├── app.py                 # Core clipping pipeline orchestrator
│   ├── server.py              # Always-on local background server (port 8765)
│   ├── config.py              # Settings loader
│   ├── llm.py                 # Gemini API integration & viral scoring
│   ├── pipeline/              # Audio extraction, transcription, reframing, export
│   ├── data/                  # Temporary cache & logs (git-ignored)
│   └── output/                # Finished 9:16 MP4 shorts & JSON metadata
├── frontend/
│   ├── app/                   # Next.js App Router (pages & API routes)
│   ├── components/            # UI components & query providers
│   └── lib/                   # Client-side API & server process bindings
├── setup_windows.ps1          # Windows setup automation
├── start_windows.bat          # 1-click Windows launcher
└── README.md
```

- **Rendered Shorts**: Stored in `backend/output/<job-id>/` as `.mp4` files alongside `clips.json`.
- **Logs**: Backend activity is logged in `backend/data/server.log`.

---

## 📄 License & Attribution

- **Developed & Maintained by**: Akshay Jain
- **Originally Created by**: [Mr BitBloX](https://www.youtube.com/@MrBitBloX)
- Free for personal and creator use.
