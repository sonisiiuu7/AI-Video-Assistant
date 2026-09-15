# 🎥 AI Video Assistant

An end-to-end AI system that turns long-form video or audio into a **searchable, conversational knowledge source**.

Instead of manually watching an entire meeting, lecture, interview, or tutorial, the application processes the media, generates a transcript, produces an intelligent summary, extracts actionable information, and creates a **RAG-powered chat interface** for asking questions about the content.

---

## ✨ What This Project Does

The AI Video Assistant transforms:

```text
Video / Audio
      ↓
   Transcript
      ↓
 ┌────┼───────────────┬────────────────┐
 ↓    ↓               ↓                ↓
Title Summary     Action Items   Decisions / Questions
                        │
                        ↓
                 Vector Database
                        │
                        ↓
                 Semantic Search
                        │
                        ↓
                  RAG Assistant
                        │
                        ↓
                  User Questions
```

The goal is simple:

> **Convert unstructured video/audio into structured, searchable, and conversational knowledge.**

---

# 🧠 Core Features

### 🎙️ Audio & Video Processing

The application accepts:

* YouTube URLs
* Local audio files
* Local video files

The media is converted into a standardized audio format before transcription.

The processing pipeline converts audio to:

* WAV
* Mono channel
* 16 kHz sample rate

This standardization makes the downstream speech-recognition pipeline more predictable.

---

### 📝 Automatic Transcription

The project supports two transcription paths.

**English**

Uses OpenAI Whisper locally.

```text
English
   ↓
Whisper
   ↓
Transcript
```

**Hinglish**

Uses Sarvam AI's speech-to-text translation API.

```text
Hinglish
   ↓
Sarvam AI
   ↓
English Transcript
```

The project processes long recordings in chunks rather than attempting to process the entire recording at once.

---

### 🧾 AI Summary

The transcript is split into smaller sections using a recursive text splitter.

Each section is summarized first, and the partial summaries are then combined into a final meeting-style summary.

```text
Long Transcript
      ↓
 Text Splitting
      ↓
┌─────┼─────┬─────┐
↓     ↓     ↓     ↓
C1    C2    C3   ...
↓     ↓     ↓
S1    S2    S3
└─────┼─────┘
      ↓
Combined Summary
```

### Why use this approach?

Large transcripts may be too long to send to an LLM in a single request.

The map → combine strategy allows the application to handle larger transcripts while maintaining a coherent final summary.

---

### 🎯 Action Item Extraction

The system identifies tasks mentioned in the transcript and attempts to extract:

* Task description
* Responsible person
* Deadline

Example:

```text
1. Prepare the project proposal
   Owner: Rahul
   Deadline: Friday
```

This transforms a transcript from a passive record into an actionable work document.

---

### 🔑 Key Decision Extraction

The application identifies important decisions made during the meeting or discussion.

Example:

```text
1. The team decided to use PostgreSQL.
2. The launch was moved to next Monday.
```

This is useful because decisions are often buried inside long conversations.

---

### ❓ Open Question Extraction

The system also identifies unresolved questions or subjects that require follow-up.

Example:

```text
1. Who will handle the deployment?
2. What is the final API cost?
3. When will the testing phase begin?
```

This creates a quick "what still needs to be answered?" view of the conversation.

---

# 🔎 RAG-Powered Video Chat

One of the main features of this project is the ability to **ask questions about the processed video**.

Instead of sending the entire transcript to the language model every time, the transcript is converted into vector embeddings and stored in ChromaDB.

```text
Transcript
    ↓
Text Chunking
    ↓
Embeddings
    ↓
ChromaDB
    ↓
Similarity Search
    ↓
Relevant Transcript Chunks
    ↓
Mistral LLM
    ↓
Answer
```

### Why RAG?

Suppose a two-hour meeting contains thousands of words.

A user might ask:

> "What did the team decide about deployment?"

Searching the vector database allows the system to retrieve only the transcript sections related to deployment.

The language model then answers the question using that retrieved context.

This is the core **Retrieval-Augmented Generation (RAG)** pattern.

---

# 🏗️ System Architecture

```text
                         ┌──────────────────────┐
                         │        User          │
                         │ YouTube / Local File │
                         └──────────┬───────────┘
                                    │
                                    ▼
                     ┌─────────────────────────┐
                     │    Audio Processor      │
                     │          utils/         │
                     │                         │
                     │ • Download media        │
                     │ • Convert to WAV        │
                     │ • Normalize audio       │
                     │ • Chunk audio           │
                     └────────────┬────────────┘
                                  │
                                  ▼
                     ┌─────────────────────────┐
                     │      Transcriber        │
                     │         core/           │
                     │                         │
                     │ English → Whisper       │
                     │ Hinglish → Sarvam AI    │
                     └────────────┬────────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │    Transcript   │
                         └────────┬────────┘
                                  │
                 ┌────────────────┼─────────────────┐
                 │                │                 │
                 ▼                ▼                 ▼
        ┌────────────────┐ ┌───────────────┐ ┌────────────────┐
        │ Summarizer     │ │ Extractor     │ │ Vector Store   │
        │                │ │               │ │                │
        │ Title          │ │ Actions       │ │ Embeddings     │
        │ Summary        │ │ Decisions     │ │ ChromaDB       │
        │                │ │ Questions     │ │                │
        └────────────────┘ └───────────────┘ └───────┬────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │    RAG Engine    │
                                           │                  │
                                           │ Retrieve relevant│
                                           │ transcript chunks│
                                           └─────────┬────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │   Mistral LLM    │
                                           └─────────┬────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │   User Answer    │
                                           └──────────────────┘
```

---

# 📂 Project Structure

```text
AI-Video-Assistant/
│
├── core/
│   ├── extractor.py
│   ├── rag_engine.py
│   ├── summarizer.py
│   ├── transcriber.py
│   └── vector_store.py
│
├── utils/
│   └── audio_processor.py
│
├── app.py
├── main.py
├── test.py
├── Requirements.txt
├── .gitignore
├── .env
└── README.md
```

---

# 📦 Module Responsibilities

### `main.py`

The main command-line entry point.

It orchestrates the complete processing pipeline:

```text
Input
 ↓
process_input()
 ↓
transcribe_all()
 ↓
generate_title()
 ↓
summarize()
 ↓
extract_action_items()
 ↓
extract_key_decisions()
 ↓
extract_questions()
 ↓
build_rag_chain()
 ↓
Interactive Q&A
```

---

### `utils/audio_processor.py`

Responsible for media ingestion.

It handles:

```text
YouTube URL
     ↓
yt-dlp
     ↓
Audio extraction
```

and for local files:

```text
Local media
     ↓
Pydub + FFmpeg
     ↓
WAV
```

The audio is standardized to:

```text
Mono
16 kHz
WAV
```

Long recordings are split into manageable chunks before transcription.

---

### `core/transcriber.py`

Responsible for speech recognition.

The transcription engine is selected based on the requested language:

```text
english
   ↓
Whisper
```

or:

```text
hinglish
   ↓
Sarvam AI
```

---

### `core/summarizer.py`

Responsible for:

* Generating a title
* Generating the final summary

It uses:

* LangChain
* Mistral
* Recursive text splitting

---

### `core/extractor.py`

Responsible for extracting structured information from the transcript.

It identifies:

```text
Action Items
Key Decisions
Open Questions
```

---

### `core/vector_store.py`

Responsible for converting transcript chunks into vector representations.

Technology:

```text
HuggingFace Embeddings
        ↓
all-MiniLM-L6-v2
        ↓
ChromaDB
```

The vector database is stored locally in:

```text
vector_db/
```

---

### `core/rag_engine.py`

Responsible for the conversational layer.

It combines:

```text
Retriever
   +
Transcript Context
   +
Mistral
```

to create a RAG pipeline that answers questions using the transcript as the knowledge source.

---

# 🔧 Technology Stack

| Layer                  | Technology                     |
| ---------------------- | ------------------------------ |
| Language               | Python                         |
| Media Download         | yt-dlp                         |
| Audio Processing       | Pydub + FFmpeg                 |
| English STT            | Whisper                        |
| Hinglish STT           | Sarvam AI                      |
| LLM                    | Mistral                        |
| Orchestration          | LangChain                      |
| Text Splitting         | RecursiveCharacterTextSplitter |
| Embeddings             | HuggingFace `all-MiniLM-L6-v2` |
| Vector Database        | ChromaDB                       |
| Environment Management | python-dotenv                  |

---

# 🚀 Setup

## 1. Clone the repository

```bash
git clone https://github.com/sonisiiuu7/AI-Video-Assistant.git
cd AI-Video-Assistant
```

---

## 2. Create a virtual environment

This project is currently set up using Python 3.11.

```bash
py -3.11 -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

---

## 3. Install dependencies

```bash
pip install -r Requirements.txt
```

If required by the installed dependency set:

```bash
pip install langchain-chroma
```

---

## 4. Install FFmpeg

FFmpeg is required for media conversion and audio processing.

Verify that it is available:

```bash
ffmpeg -version
```

---

## 5. Configure API keys

Create a `.env` file in the project root:

```env
MISTRAL_API_KEY=your_mistral_api_key
SARVAM_API_KEY=your_sarvam_api_key
```

Do **not** commit `.env` to GitHub.

The `.env` file is excluded through `.gitignore`.

---

# ▶️ Running the Application

Run:

```bash
python main.py
```

The application asks:

```text
Enter YouTube URL or local file path:
Language (english/hinglish):
```

### Example

```text
Enter YouTube URL or local file path:
https://www.youtube.com/watch?v=...

Language (english/hinglish):
english
```

The system then performs the complete processing pipeline.

---

# 🔄 Complete Execution Flow

```text
1. Receive YouTube URL / Local File
                ↓
2. Download / Read Media
                ↓
3. Convert to WAV
                ↓
4. Normalize Audio
                ↓
5. Split into Chunks
                ↓
6. Transcribe Audio
                ↓
7. Generate Meeting Title
                ↓
8. Generate Summary
                ↓
9. Extract Action Items
                ↓
10. Extract Key Decisions
                ↓
11. Extract Open Questions
                ↓
12. Create Transcript Embeddings
                ↓
13. Store Embeddings in ChromaDB
                ↓
14. Start RAG Chat
                ↓
15. Retrieve Relevant Transcript Context
                ↓
16. Generate Answer with Mistral
```

---

# 💡 Why This Architecture?

The project intentionally separates responsibilities instead of putting the entire pipeline into one Python file.

### Separation of Concerns

Each stage has a focused responsibility:

```text
Audio Processing
       ↓
Transcription
       ↓
LLM Processing
       ↓
Vector Storage
       ↓
Retrieval
       ↓
Question Answering
```

This makes the system easier to:

* Debug
* Test
* Extend
* Replace individual components
* Understand during development

For example, the transcription engine can be changed without redesigning the RAG layer.

---

# 🧩 Design Decisions

### Why chunk audio?

Long audio files can be expensive or impractical to process as a single unit.

Chunking allows the application to:

* Process long recordings
* Reduce memory pressure
* Handle model/API limits
* Recover from failures more easily

---

### Why normalize audio?

Different media files can have different:

* Sampling rates
* Channel counts
* Encodings
* Containers

Converting everything into a consistent WAV representation creates a predictable input for the transcription layer.

---

### Why use Whisper locally?

Local Whisper provides an offline transcription path for English audio and avoids requiring an external speech-to-text API for that branch.

---

### Why use Sarvam for Hinglish?

Hinglish can contain a mixture of Hindi and English.

The project therefore provides a separate transcription path through Sarvam AI that can translate the spoken content into English while transcribing it.

---

### Why use Mistral?

Mistral is used as the reasoning/generation layer for:

```text
Summarization
Title Generation
Action Item Extraction
Decision Extraction
Question Extraction
RAG Answers
```

This centralizes the language-generation tasks around one LLM interface.

---

### Why use embeddings?

Traditional keyword search can miss semantically related information.

For example:

```text
Question:
"What was decided about infrastructure?"
```

The transcript might never contain the exact word:

```text
"infrastructure"
```

but may contain:

```text
"We decided to move our backend to AWS."
```

Semantic embeddings allow the retrieval system to identify that these concepts are related.

---

### Why use RAG?

The LLM should answer questions based on the actual video rather than relying only on its general knowledge.

RAG provides the pattern:

```text
Question
   ↓
Retrieve relevant transcript
   ↓
Give context to LLM
   ↓
Generate grounded answer
```

This makes the assistant much more useful for document- and meeting-specific questions.

---

# 🛡️ Environment & Secrets

Sensitive credentials belong in:

```text
.env
```

and should never be hard-coded.

Example:

```env
MISTRAL_API_KEY=...
SARVAM_API_KEY=...
```

The `.env` file is excluded through:

```text
.gitignore
```

Never commit API keys to a public repository.

---

# ⚠️ Current Limitations

This project is a development-oriented implementation and has several areas that can be improved.

### YouTube extraction

`yt-dlp` may sometimes require an available JavaScript runtime for complete YouTube extraction support.

### CPU transcription

Whisper runs locally and can be computationally expensive on CPU, especially for long videos.

### LLM rate limits

The summarization and extraction pipeline makes multiple LLM requests, so API rate limits can become a bottleneck for longer transcripts.

### Persistent vector storage

The current implementation uses a local ChromaDB directory:

```text
vector_db/
```

This is appropriate for local development but would need a more robust storage strategy for multi-user or production deployments.

---

# 🚧 Future Improvements

Possible next steps include:

* Streaming transcription
* GPU-accelerated Whisper
* Better YouTube extraction/runtime support
* Speaker diarization
* Timestamp-aware transcripts
* Persistent conversation history
* Multi-video knowledge bases
* Authentication
* Web-based interface improvements
* Background processing for long videos
* Caching of transcripts and embeddings
* Production-grade vector database
* Async processing
* Automatic meeting minutes generation
* Export to PDF / Markdown
* Source citations with timestamps
* Multi-language support

---

# 🎯 Example Use Cases

This architecture can be adapted for:

### 📚 Education

Upload a lecture and ask:

> "What are the three major concepts explained in this lecture?"

### 🏢 Meetings

Process a meeting and ask:

> "What decisions were made?"

### 🎤 Interviews

Process an interview and ask:

> "What were the candidate's main technical projects?"

### ▶️ YouTube Learning

Give it a technical tutorial and ask:

> "Explain the database architecture discussed in the video."

### 🧑‍💻 Developer Research

Process a conference talk and ask:

> "What technologies did the speaker recommend?"

---

# 📌 Project Philosophy

The project follows a simple principle:

> **Don't just transcribe information — turn it into something you can query, summarize, and act upon.**

The combination of:

```text
Speech Recognition
        +
LLM Processing
        +
Embeddings
        +
Vector Search
        +
RAG
```

creates a complete pipeline for transforming unstructured audiovisual information into an interactive knowledge system.

---

# ⭐ Future Direction

The long-term goal is to evolve this from a command-line prototype into a full production-ready AI knowledge assistant:

```text
              ┌─────────────────────┐
              │       User          │
              └──────────┬──────────┘
                         ↓
              ┌─────────────────────┐
              │   Web Application   │
              └──────────┬──────────┘
                         ↓
              ┌─────────────────────┐
              │ Processing Pipeline │
              └──────────┬──────────┘
                         ↓
              ┌─────────────────────┐
              │ Transcript + RAG    │
              └──────────┬──────────┘
                         ↓
              ┌─────────────────────┐
              │ Conversational AI   │
              └─────────────────────┘
```

The current repository provides the foundation for that architecture.
