# 🎬 AI Video Assistant

**Turn any long video or meeting recording into a short summary, a to-do list, and a chat buddy that answers your questions about it.**

Paste a YouTube link (or pick a video/audio file from your computer), click one button, and in a few minutes you get everything you need, without watching the whole thing.

<!-- 📸 Add a screenshot of the app here, e.g.:
![App screenshot](docs/app-screenshot.png)
-->

---

## 🤔 What problem does this solve?

We all have long meetings, lectures, and videos that we don't have time to sit through again. Scrubbing through a 1-hour recording to find "what did we decide about the deadline?" is painful.

**AI Video Assistant does the boring part for you.** It listens to the video, writes down everything that was said, and then uses AI to pick out what actually matters.

---

## ✨ What do you get?

| You get | In plain words |
|---|---|
| 📝 **Full transcript** | Everything that was said, written out as text |
| 🏷️ **Smart title** | A short, professional title for the video |
| 📋 **Summary** | The whole video boiled down to a few bullet points |
| ✅ **Action items** | Who has to do what, and by when (if it was mentioned) |
| 🔑 **Key decisions** | What the group agreed on |
| ❓ **Open questions** | Things nobody answered yet and need follow-up |
| 💬 **Chat with your video** | Ask questions like *"What was said about the budget?"* and get an answer based only on the video |

It understands **English** and **Hinglish** (Hindi mixed with English). Hinglish speech is automatically converted into English text.

---

## 🧠 How does it work? (The simple version)

Think of it like hiring a very fast assistant who does five things in order:

1. **Gets the audio.** Downloads it from YouTube, or takes your file and converts it into a standard audio format. Long audio is cut into 10-minute pieces so it's easier to handle.
2. **Listens and types.** A speech-to-text AI turns the audio into written text (the transcript).
3. **Reads and thinks.** A language AI reads the transcript and writes the title, summary, action items, decisions, and open questions.
4. **Builds a "memory".** The transcript is chopped into small pieces and stored in a searchable database, like an index at the back of a book.
5. **Answers your questions.** When you ask something, the app finds the 4 most relevant pieces from the memory and gives them to the AI, which answers using *only* that information. If the answer isn't in the video, it honestly says so.

That last idea, "look things up first, then answer", is called **RAG** (Retrieval-Augmented Generation). It keeps the AI from making things up.

---

## 🗺️ Flow Diagram

<!--
👇 PLACEHOLDER: add your flow diagram image here.
Suggested: save the image as docs/flow-diagram.png and keep the line below.
Idea for what to show:
YouTube link / File -> yt-dlp + FFmpeg + pydub -> 10-min audio chunks
-> Whisper (English) or Sarvam AI (Hinglish) -> Transcript
-> Mistral AI (Title, Summary, Action items, Decisions, Questions)
-> Transcript chunks -> MiniLM embeddings -> ChromaDB -> Mistral AI -> Chat answers
-> Streamlit UI
-->

![Flow Diagram](docs/flow-diagram.png)

*Diagram: how a video travels through the app, from the link you paste to the answers you read.*

---

## 🧰 Tech Stack

In plain words, here is what each tool does and why it's here.

| Part | Tool used | What it does |
|---|---|---|
| Website / screen you click on | **Streamlit** | Builds the web page with the input box, buttons, and result cards |
| YouTube download | **yt-dlp** | Grabs the audio from a YouTube link |
| Audio handling | **FFmpeg + pydub** | Converts audio to a standard format and cuts it into pieces |
| Speech to text (English) | **OpenAI Whisper** (runs on your computer) | Listens and writes the transcript, no API key needed |
| Speech to text (Hinglish) | **Sarvam AI** (online service) | Listens to Hindi and English mixed together and gives English text |
| The "brain" | **Mistral AI** (`mistral-small-latest`) | Writes the title, summary, action items, decisions, questions, and chat answers |
| AI workflow glue | **LangChain** | Connects the prompts, the AI, and the database into one smooth pipeline |
| Searchable memory | **ChromaDB** | Stores transcript pieces so the right ones can be found fast |
| Meaning finder | **Sentence-Transformers** (`all-MiniLM-L6-v2`) | Turns text into numbers (embeddings) so the app can find pieces with similar meaning |
| Secret keys | **python-dotenv** | Keeps your API keys in a private `.env` file |
| Language | **Python** | Everything is written in Python |

---

## 📁 Project Structure

```
AI-Video-Assistant/
├── app.py                  # The web app (Streamlit), start here to use the project
├── main.py                 # Same pipeline, but runs in the terminal
├── test.py                 # Quick test script on a sample YouTube video
├── Requirements.txt        # List of Python packages needed
├── .env                    # Your private API keys (you create this, never upload it)
│
├── core/                   # The "brains"
│   ├── transcriber.py      # Audio -> text (Whisper or Sarvam)
│   ├── summarizer.py       # Title and summary
│   ├── extractor.py        # Action items, decisions, open questions
│   ├── vector_store.py     # Stores transcript in ChromaDB
│   └── rag_engine.py       # "Chat with your video" logic
│
└── utils/
    └── audio_processor.py  # Downloads, converts, and chops audio
```

---

## 🚀 How to Run This Project

### Step 0: What you need before starting

- **Python 3.10 or newer** (3.11 works well). Check with `python --version`
- **Git** to download the project
- **FFmpeg** (a free tool for handling audio and video)
- **A Mistral API key** (free to create, required)
- **A Sarvam API key** (only needed if you want to use **Hinglish**)
- Around **2 GB of free disk space** and an internet connection (the first run downloads some AI models)

### Step 1: Download the project

```powershell
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
```

### Step 2: Install FFmpeg

FFmpeg is what converts and cuts the audio, so the app will not work without it.

**Windows** (PowerShell):
```powershell
winget install Gyan.FFmpeg
```
**Mac:** `brew install ffmpeg`  
**Linux:** `sudo apt install ffmpeg`

> ⚠️ After installing on Windows, **close PowerShell and open it again**, then check it worked with `ffmpeg -version`.

### Step 3: Create a virtual environment (a clean, separate box for this project)

**Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

> If PowerShell says *"running scripts is disabled"*, run this once and try again:
> `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`

**Mac / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

You'll see `(venv)` at the start of your terminal line when it's active.

### Step 4: Install the packages

```powershell
pip install -r Requirements.txt
```

This can take several minutes because it installs PyTorch and Whisper. That's normal.

### Step 5: Add your API keys

Create a new file named **`.env`** in the main project folder (same place as `app.py`) and paste this inside:

```env
MISTRAL_API_KEY=your_mistral_key_here
SARVAM_API_KEY=your_sarvam_key_here
```

- Get a Mistral key at **https://console.mistral.ai**
- Get a Sarvam key at **https://www.sarvam.ai** (skip this line if you only use English)
- *Optional:* choose a different Whisper size by adding `WHISPER_MODEL=base`. Options are `tiny`, `base`, `small` (default), `medium`, `large`. Smaller is faster, bigger is more accurate.

> 🔒 Never share your `.env` file or upload it to GitHub. It is already listed in `.gitignore`.

### Step 6: Start the app

```powershell
streamlit run app.py
```

Your browser opens automatically at **http://localhost:8501**.

### Step 7: Use it

1. In the left sidebar, paste a **YouTube link** or type the **path of a video/audio file** on your computer (for example `C:\Users\you\Videos\meeting.mp4`).
2. Pick the language: **english** or **hinglish**.
3. Click **⚡ Analyse** and wait. The sidebar shows which step is running.
4. Read your title, summary, action items, decisions, and open questions.
5. Scroll down to **💬 Chat with your Meeting** and ask anything.

### Prefer the terminal? (optional)

```powershell
python main.py
```

It will ask for a YouTube link or file path, then the language, print the results, and let you chat by typing questions. Type `exit` to quit.

---

## 🛠️ Troubleshooting

| Problem | What to do |
|---|---|
| `ffmpeg not found` or audio errors | FFmpeg isn't installed, or the terminal needs a restart. See Step 2 |
| `MISTRAL_API_KEY` errors / authentication failed | Check the `.env` file: correct key, no spaces, no quotes, saved in the main folder |
| `SARVAM_API_KEY is not set` | You chose Hinglish but didn't add the Sarvam key. Add it to `.env` |
| First run seems stuck | The AI models are downloading (a few hundred MB). Wait for it to finish |
| Very slow transcription | Use a smaller Whisper model: `WHISPER_MODEL=base` in `.env` |
| YouTube download fails | Update the downloader: `pip install -U yt-dlp` |
| `No module named ...` | Make sure the virtual environment is active (you see `(venv)`) and re-run Step 4 |

---

## 📝 Good to Know

- **Internet is required** because the summary and chat use Mistral's online AI, and Hinglish uses Sarvam's online service.
- **English transcription runs on your own computer**, so the audio is not sent anywhere for that step.
- Longer videos take longer. A rough idea: audio is processed in 10-minute pieces.
- The chat answers **only from the video**. If something wasn't said, the assistant will tell you it couldn't find it.
- The searchable memory is saved in a local `vector_db` folder. To start completely fresh, close the app and delete that folder.

---

## 🤝 Contributing

Found a bug or have an idea? Feel free to open an **Issue** or send a **Pull Request**. All help is welcome.
---

⭐ If this project helped you, consider giving it a star on GitHub!
