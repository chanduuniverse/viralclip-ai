# ViralClip AI

AI-assisted highlight clipping starter app for authorized YouTube livestreams and uploaded videos.

## What it does
- Accepts a YouTube URL or a local video upload.
- Downloads public, accessible videos with `yt-dlp` (subject to YouTube restrictions and rights).
- Extracts audio and transcribes it with faster-whisper.
- Detects candidate moments using transcript cues, speech activity, and optional Gemini ranking.
- Produces up to 20 vertical MP4 clips with burned-in captions.
- Provides a simple browser dashboard and downloadable results.

> Use only videos you own or have permission to process. This app does not bypass private, age-restricted, DRM-protected, or otherwise inaccessible content. YouTube livestreams that are still live may not be fully downloadable; process the replay/VOD after it is available.

## Requirements
- Python 3.10+
- FFmpeg installed and available on PATH
- For best performance: NVIDIA GPU optional; CPU works but transcription can be slow.
- Gemini API key optional (for smarter highlight ranking).

## Run locally
```bash
# 1) Create and activate a virtual environment
python -m venv .venv
# Windows:
.venv\\Scripts\\activate
# macOS/Linux:
source .venv/bin/activate

# 2) Install dependencies
pip install -r requirements.txt

# 3) Configure environment
cp .env.example .env
# Add GEMINI_API_KEY if you want Gemini ranking.

# 4) Start the app
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```
Open http://localhost:8000

## GitHub
Create an empty repository on GitHub, then:
```bash
git init
git add .
git commit -m "Initial ViralClip AI project"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/viralclip-ai.git
git push -u origin main
```
Do not commit `.env`, API keys, source videos, or generated clips.

## How it works
1. The API creates a job.
2. A background worker downloads/loads the video.
3. FFmpeg extracts audio.
4. faster-whisper generates timestamped transcript segments.
5. Candidate windows are scored; Gemini can re-rank candidates when configured.
6. FFmpeg exports 9:16 clips with subtitles.
7. The dashboard polls job status and lists downloadable MP4 files.

## Current MVP limitations
- This is a local single-user starter, not a production multi-user service.
- Jobs run in the FastAPI process; use a queue (Celery/RQ) and object storage for production.
- "Viral score" is a heuristic estimate, not a prediction of actual views.
- Face tracking / speaker-aware reframing is not included yet; vertical crop uses a centered frame.
- For long videos, processing time and disk usage can be substantial.
