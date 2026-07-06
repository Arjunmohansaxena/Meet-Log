# Meet-Log

Meet-Log is a Chrome extension plus FastAPI backend project for recording meeting audio, transcribing it, and extracting decisions, open issues, and action items via Google Gemini/GenAI.

## Project structure

```
Meet-Log/
├── backend/
│   ├── main.py
│   ├── requirements.txt
│   ├── .env                 # local env file, ignored by git
│   ├── uploads/             # saved uploads, ignored by git
│   └── .venv/               # Python virtual environment, ignored by git
├── extension/
│   ├── manifest.json
│   ├── popup.html
│   ├── popup.js
│   ├── popup.css
│   ├── background.js
│   ├── offscreen.html
│   ├── offscreen.js
│   ├── content.js
│   └── icon.png
├── LICENSE
├── .gitignore
└── README.md
## Backend setup

Open PowerShell and change into the backend folder:

cd E:\CSE\Meet-Log\backend
Activate the virtual environment:

.\.venv\Scripts\Activate.ps1
Install dependencies:

python -m pip install -r requirements.txt
Create a .env file in backend/ or set the environment variable directly.

Example .env content:

GEMINI_API_KEY=your_api_key_here
Start the backend server:

python -m uvicorn main:app --reload --port 8000
### Notes

If uvicorn fails because the Windows executable wrapper is broken, use the python -m uvicorn form from the active virtual environment.
The backend saves uploaded audio files in backend/uploads/ and generates a PDF report for each upload.
## Extension setup

Open chrome://extensions in Chrome.
Enable Developer mode.
Click **Load unpacked** and select the extension/ folder.
Open the popup and enter the backend upload URL:

http://127.0.0.1:8000/upload
Start recording and stop when the meeting is finished.

## How it works

The Chrome extension records tab audio using an offscreen document.
When recording stops, the extension uploads the audio file to the backend.
The backend transcribes audio with faster_whisper.
It then sends the transcript to the Google model and returns a JSON report plus a PDF.
## Troubleshooting

If VS Code cannot resolve imports, ensure the workspace uses backend/.venv as the Python interpreter.
If pip install -r requirements.txt fails, run:

.\.venv\Scripts\python.exe -m pip install -r requirements.txt
If uvicorn fails with a launcher error, run:

python -m uvicorn main:app --reload --port 8000
## Security

Do not commit backend/.env.
Do not commit backend/uploads/.
Keep API keys and other secrets out of source control.
## License

This repository is licensed under the terms of the LICENSE file.
