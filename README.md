# MeetLog — AI Meeting Decision-Log Copilot

An AI copilot that turns meeting recordings into structured decision logs — separating what was **decided**, what's **still open**, and **action items** with owners, so teams stop arguing about what actually got agreed on.

## The Problem

Most meeting tools (Otter, Fireflies, Fathom) give you a transcript and a generic summary. They don't clearly distinguish between *"we decided X"* and *"we just talked about X"* — and that distinction is exactly what causes teams to disagree after the fact about what was actually committed to.

## What It Does

1. A Chrome extension records audio from your meeting tab (Google Meet, Zoom, Teams) — plus your microphone, so both sides of the conversation are captured.
2. A visible on-page banner notifies all participants that recording is active.
3. The recording is transcribed locally using OpenAI's Whisper model (via `faster-whisper`) — no audio ever leaves your machine at this stage.
4. The transcript is passed to an LLM (Gemini) with a structured prompt that classifies the discussion into:
   - **Decided** — firm outcomes
   - **Open** — raised but unresolved
   - **Action Items** — tasks with named owners
5. You get a short, structured report instead of a wall of transcript text you'd never reread.

## Tech Stack

- **Extension:** JavaScript (Manifest V3), `chrome.tabCapture`, `MediaRecorder`, offscreen documents
- **Backend:** Python, FastAPI
- **Transcription:** `faster-whisper` (local, free)
- **AI classification:** Google Gemini API (`gemini-2.5-flash`)

## Project Status

This is an early-stage MVP built to test one core hypothesis: **can an AI reliably tell the difference between "decided" and "discussed but not settled" in a real conversation, well enough that people trust and act on it?**

Deliberately deferred for a later phase:
- Real-time (live) summary — deferred because live classification risks showing an unstable "decided" status mid-meeting, which undermines the trust the product depends on
- Bot-joins-the-meeting architecture (like Otter/Fireflies) — a much larger infrastructure investment, only worth pursuing after the core classification hypothesis is validated
- Speaker name attribution — currently no diarization, so the report doesn't yet attribute items to specific named speakers

## Setup

### 1. Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Create a `.env` file in the backend folder:
```
GEMINI_API_KEY=your-key-here
```

Run the server:
```bash
uvicorn main:app --reload
```

Test it at `http://127.0.0.1:8000/docs`.

### 2. Chrome Extension

1. Go to `chrome://extensions`
2. Enable **Developer mode** (top right)
3. Click **Load unpacked**
4. Select the `extension/` folder
5. Join a Google Meet call, click the extension icon, hit **Start**

## Known Limitations

- Speaker names aren't captured — reports classify content but don't currently attribute it to named individuals
- AI classification isn't perfect — the report is meant to be reviewed and corrected, not treated as an automatic source of truth
- Requires the extension to be actively running during the call (no server-side bot join)

## License

MIT — see [LICENSE](LICENSE) for details.
