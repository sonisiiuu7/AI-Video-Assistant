# 🎥 AI Video Assistant

An end-to-end AI application that transforms **videos and audio into searchable, structured, and conversational knowledge**.

The system can ingest a YouTube video or local media file, convert it into standardized audio, transcribe the content, generate intelligent insights, and build a **RAG-powered assistant** that lets users ask questions about the original content.

The project provides both a **Streamlit Web UI** and a **Command-Line Interface (CLI)**.

---

## ✨ What This Project Does

The AI Video Assistant converts unstructured audiovisual content into structured information and an interactive knowledge source.

```text
                         VIDEO / AUDIO
                               │
                               ▼
                       Audio Processing
                               │
                               ▼
                         Transcription
                               │
                               ▼
                           Transcript
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼
         Title & Summary   Extraction      Vector Store
                              │                │
                              │                ▼
                              │             ChromaDB
                              │                │
                              └────────┬───────┘
                                       ▼
                                  RAG Engine
                                       │
                                       ▼
                                  Mistral LLM
                                       │
                                       ▼
                              Conversational Q&A
```

### Core capabilities

* 🎬 YouTube video processing
* 📁 Local audio/video processing
* 🎙️ English transcription with Whisper
* 🗣️ Hinglish transcription with Sarvam AI
* 📝 Automatic transcript generation
* 📋 AI-powered summarization
* 🏷️ Automatic title generation
* ✅ Action-item extraction
* 🔑 Key-decision extraction
* ❓ Open-question extraction
* 🔎 Semantic search using embeddings
* 🧠 Retrieval-Augmented Generation (RAG)
* 💬 Conversational question answering
* 🌐 Streamlit web interface
* 💻 Command-line interface

---

# 🖥️ User Interfaces

The project currently provides **two ways to interact with the system**.

## 🌐 Streamlit Web Application

The file:

```text
app.py
```

provides the web interface.

Launch it with:

```bash
streamlit run app.py
```

Streamlit will provide a local URL such as:

```text
http://localhost:8501
```

The web interface provides:

* YouTube URL / local file input
* Language selection
* Analyse button
* Pipeline status indicators
* Generated title
* Summary
* Action items
* Key decisions
* Open questions
* Transcript
* RAG-based chat

### Web Application Flow

```text
Browser
   │
   ▼
Streamlit UI
   │
   ▼
User provides video / audio
   │
   ▼
Processing Pipeline
   │
   ├── Audio Processing
   ├── Transcription
   ├── Summarization
   ├── Information Extraction
   └── RAG Construction
   │
   ▼
Results displayed in UI
   │
   ▼
Chat with the processed content
```

---

## 💻 Command-Line Interface

The file:

```text
main.py
```

acts as the CLI entry point.

Run:

```bash
python main.py
```

The application asks:

```text
Enter YouTube URL or local file path:
Language (english/hinglish):
```

After processing, the user can continue asking questions through the terminal.

### CLI Flow

```text
Terminal
   │
   ▼
YouTube URL / Local File
   │
   ▼
Processing Pipeline
   │
   ▼
Generated Insights
   │
   ▼
Interactive RAG Chat
```

---

# 🏗️ System Architecture

```text
                         ┌──────────────────────┐
                         │        USER          │
                         └──────────┬───────────┘
                                    │
                   ┌────────────────┴────────────────┐
                   │                                 │
                   ▼                                 ▼
          ┌─────────────────┐               ┌─────────────────┐
          │ Streamlit Web UI│               │      CLI        │
          │     app.py      │               │    main.py      │
          └────────┬────────┘               └────────┬────────┘
                   │                                 │
                   └────────────────┬────────────────┘
                                    ▼
                         ┌────────────────────────┐
                         │    Audio Processor     │
                         │ utils/audio_processor  │
                         └────────────┬───────────┘
                                      │
                                      ▼
                         ┌────────────────────────┐
                         │      Transcriber       │
                         │     core/transcriber   │
                         └────────────┬───────────┘
                                      │
                                      ▼
                              ┌──────────────┐
                              │  Transcript  │
                              └──────┬───────┘
                                     │
                   ┌─────────────────┼──────────────────┐
                   │                 │                  │
                   ▼                 ▼                  ▼
           ┌──────────────┐ ┌──────────────┐ ┌──────────────────┐
           │  Summarizer  │ │   Extractor  │ │   Vector Store   │
           │              │ │              │ │                  │
           │ • Title      │ │ • Actions    │ │ • Chunking       │
           │ • Summary    │ │ • Decisions  │ │ • Embeddings     │
           │              │ │ • Questions  │ │ • ChromaDB       │
           └──────────────┘ └──────────────┘ └────────┬─────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │    RAG Engine    │
                                           │ core/rag_engine  │
                                           └─────────┬────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │   Mistral LLM    │
                                           └─────────┬────────┘
                                                     │
                                                     ▼
                                           ┌──────────────────┐
                                           │ Conversational AI│
                                           └──────────────────┘
```

---

# 🔄 End-to-End Project Flow

The complete pipeline works as follows:

```text
1. User provides a YouTube URL or local media file
                         ↓
2. Media is downloaded / read
                         ↓
3. Audio is converted to WAV
                         ↓
4. Audio is normalized
   • Mono
   • 16 kHz
                         ↓
5. Audio is split into manageable chunks
                         ↓
6. Speech is transcribed
   • English  → Whisper
   • Hinglish → Sarvam AI
                         ↓
7. Complete transcript is generated
                         ↓
8. Transcript is used for title generation
                         ↓
9. Transcript is summarized
                         ↓
10. Action items are extracted
                         ↓
11. Key decisions are extracted
                         ↓
12. Open questions are extracted
                         ↓
13. Transcript is split into smaller semantic chunks
                         ↓
14. Chunks are converted into vector embeddings
                         ↓
15. Embeddings are stored in ChromaDB
                         ↓
16. Retriever searches for relevant transcript chunks
                         ↓
17. Retrieved context is provided to Mistral
                         ↓
18. Mistral generates a grounded answer
```

---

# 🎙️ 1. Audio & Video Processing

The first stage is responsible for converting different media sources into a standard format.

### YouTube input

```text
YouTube URL
     ↓
    yt-dlp
     ↓
Downloaded audio
```

### Local file input

```text
Local Audio / Video
        ↓
   Pydub + FFmpeg
        ↓
      WAV Audio
```

The project then standardizes the audio to:

```text
WAV
Mono channel
16 kHz sample rate
```

Finally, long audio is split into smaller chunks.

### Why normalize audio?

Different media files can have different formats, sample rates, channels, and encodings.

Standardization gives the transcription layer a predictable input format.

### Why chunk the audio?

Processing very long recordings as one huge input can increase memory usage and make speech recognition harder to manage.

Chunking allows the system to process long recordings incrementally.

---

# 📝 2. Speech Transcription

The transcription layer chooses an engine based on the selected language.

## English

```text
Audio Chunk
    ↓
Whisper
    ↓
English Transcript
```

Whisper runs locally, which avoids requiring an external speech-recognition API for the English path.

## Hinglish

```text
Audio Chunk
    ↓
Sarvam AI
    ↓
English Transcript
```

The Sarvam path is used to handle Hinglish speech and translate it into English while transcribing.

---

# 🧾 3. Transcript Processing

Once the complete transcript is available, the system sends it through several downstream tasks.

```text
                    Transcript
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
      Summarizer      Extractor     Vector Store
          │              │              │
          ▼              ▼              ▼
       Summary      Structured      Embeddings
        Title        Insights          │
                                        ▼
                                      ChromaDB
```

This design keeps each responsibility separate.

---

# 🧠 4. AI Summarization

The summarization system uses a **map → combine** approach.

```text
                 Full Transcript
                        ↓
                Text Splitting
                        ↓
            ┌───────────┼───────────┐
            ▼           ▼           ▼
          Chunk 1     Chunk 2     Chunk N
            │           │           │
            ▼           ▼           ▼
         Summary 1   Summary 2   Summary N
            └──────────┼──────────┘
                       ▼
                Combined Summary
```

### Why use this architecture?

A large transcript may be too big to efficiently process in one LLM request.

Breaking the transcript into pieces allows the system to process larger recordings and then combine the partial results.

---

# 🏷️ 5. Title Generation

The application also generates a short professional title based on the transcript.

Example:

```text
Input:
Long technical discussion about deploying an AI application

Output:
"AI Application Deployment Strategy"
```

This makes generated results easier to organize and identify.

---

# ✅ 6. Action Item Extraction

The extractor identifies tasks discussed during the conversation.

For each action item, the system attempts to identify:

* Task
* Owner
* Deadline

Example:

```text
1. Prepare the project proposal
   Owner: Rahul
   Deadline: Friday
```

This transforms a transcript into an actionable work summary.

---

# 🔑 7. Key Decision Extraction

The system identifies decisions made during a meeting or discussion.

Example:

```text
1. The team decided to use PostgreSQL.
2. Deployment will happen next Monday.
```

This prevents important decisions from being buried inside a long transcript.

---

# ❓ 8. Open Question Extraction

The system identifies unresolved questions and topics requiring follow-up.

Example:

```text
1. Who will handle deployment?
2. What is the final API cost?
3. When will testing begin?
```

This provides a concise list of remaining discussion points.

---

# 🔎 9. Vector Embeddings

To make the transcript searchable, it is divided into smaller text chunks.

```text
Transcript
     ↓
Recursive Text Splitting
     ↓
Text Chunks
     ↓
HuggingFace Embeddings
     ↓
Vector Representations
```

The project uses:

```text
all-MiniLM-L6-v2
```

for generating embeddings.

---

# 🗄️ 10. ChromaDB Vector Store

The generated embeddings are stored in ChromaDB.

```text
Transcript Chunks
       ↓
Embeddings
       ↓
ChromaDB
```

The local persistent storage directory is:

```text
vector_db/
```

### Why use a vector database?

Normal keyword search may fail when two pieces of text express the same idea using different words.

For example:

```text
Question:
"What did they decide about infrastructure?"
```

The transcript might say:

```text
"We decided to migrate the backend to AWS."
```

Semantic embeddings allow the system to recognize the relationship between the query and the relevant transcript content.

---

# 🧠 11. Retrieval-Augmented Generation

The RAG layer connects semantic retrieval with the language model.

```text
                    User Question
                         │
                         ▼
                  Semantic Retriever
                         │
                         ▼
              Relevant Transcript Chunks
                         │
                         ▼
                  Context + Question
                         │
                         ▼
                    Mistral LLM
                         │
                         ▼
                    Final Answer
```

### Why RAG?

The language model should answer questions using the contents of the processed video rather than relying solely on general knowledge.

The retriever supplies the relevant transcript context before the answer is generated.

---

# 💬 12. Conversational Video Assistant

After processing a video, the user can ask questions such as:

```text
"What were the main decisions?"
```

```text
"Who was responsible for the deployment?"
```

```text
"What technologies were discussed?"
```

```text
"What problems did the team identify?"
```

The RAG pipeline retrieves the relevant transcript context and uses Mistral to generate the answer.

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

## `app.py`

The Streamlit web application.

Responsible for:

* User interface
* Input collection
* Language selection
* Running the pipeline
* Displaying processing status
* Displaying generated insights
* Providing conversational interaction

---

## `main.py`

The CLI entry point.

Responsible for orchestrating the processing pipeline from the terminal.

```text
Input
 ↓
Audio Processing
 ↓
Transcription
 ↓
Title
 ↓
Summary
 ↓
Extraction
 ↓
RAG
 ↓
Interactive Q&A
```

---

## `utils/audio_processor.py`

Responsible for:

* YouTube audio downloading
* Local media ingestion
* Audio conversion
* Audio normalization
* Audio chunking

Technologies:

```text
yt-dlp
Pydub
FFmpeg
```

---

## `core/transcriber.py`

Responsible for:

* Loading Whisper
* Transcribing English audio
* Sending Hinglish audio to Sarvam AI
* Combining chunk-level transcripts

---

## `core/summarizer.py`

Responsible for:

* Title generation
* Transcript summarization
* Recursive text splitting
* Combining partial summaries

---

## `core/extractor.py`

Responsible for:

* Action item extraction
* Key decision extraction
* Open question extraction

---

## `core/vector_store.py`

Responsible for:

* Transcript chunking
* Embedding generation
* ChromaDB creation
* Retriever configuration

---

## `core/rag_engine.py`

Responsible for:

* Building the RAG chain
* Retrieving relevant transcript chunks
* Supplying context to the LLM
* Generating answers

---

# 🔧 Technology Stack

| Layer                   | Technology                     |
| ----------------------- | ------------------------------ |
| Programming Language    | Python                         |
| Web UI                  | Streamlit                      |
| Media Download          | yt-dlp                         |
| Audio Processing        | Pydub                          |
| Media Processing        | FFmpeg                         |
| English Speech-to-Text  | Whisper                        |
| Hinglish Speech-to-Text | Sarvam AI                      |
| LLM                     | Mistral                        |
| LLM Framework           | LangChain                      |
| Text Splitting          | RecursiveCharacterTextSplitter |
| Embeddings              | HuggingFace `all-MiniLM-L6-v2` |
| Vector Database         | ChromaDB                       |
| Environment Variables   | python-dotenv                  |

---

# 🚀 Setup

## 1. Clone the repository

```bash
git clone https://github.com/sonisiiuu7/AI-Video-Assistant.git
cd AI-Video-Assistant
```

---

## 2. Create a Python 3.11 virtual environment

```bash
py -3.11 -m venv venv
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

Verify:

```bash
python --version
```

---

## 3. Install dependencies

```bash
pip install -r Requirements.txt
```

If required:

```bash
pip install langchain-chroma
```

---

## 4. Install FFmpeg

Verify that FFmpeg is available:

```bash
ffmpeg -version
```

---

## 5. Configure environment variables

Create a `.env` file in the project root:

```env
MISTRAL_API_KEY=your_mistral_api_key
SARVAM_API_KEY=your_sarvam_api_key
```

Keep your API keys private.

The `.env` file is excluded from Git through `.gitignore`.

---

# ▶️ Run the Web Application

Start Streamlit with:

```bash
streamlit run app.py
```

Open the local URL provided by Streamlit, typically:

```text
http://localhost:8501
```

Then provide:

```text
YouTube URL or local file
```

Select:

```text
english
```

or:

```text
hinglish
```

and click:

```text
⚡ Analyse
```

---

# ▶️ Run the CLI

Alternatively:

```bash
python main.py
```

Then enter:

```text
Enter YouTube URL or local file path:
```

followed by:

```text
Language (english/hinglish):
```

---

# 🔐 Environment & Secrets

API keys should always be stored in `.env`:

```env
MISTRAL_API_KEY=...
SARVAM_API_KEY=...
```

Never hard-code API keys directly inside Python files.

Never commit `.env` to GitHub.

Generated/local files such as the virtual environment, Python cache, downloaded media, and local vector database should also remain outside version control.

---

# ⚠️ Current Limitations

### YouTube extraction

YouTube extraction can depend on `yt-dlp` compatibility with YouTube's current delivery mechanisms and supported JavaScript runtimes.

### CPU transcription

Whisper can be computationally expensive when running locally on a CPU, particularly for long recordings.

### API rate limits

Several stages use the Mistral API, including title generation, summarization, extraction, and RAG responses. Longer transcripts can therefore require multiple LLM requests.

### Local vector database

The current implementation stores ChromaDB locally in:

```text
vector_db/
```

This is suitable for local development but would need a more robust storage architecture for a production multi-user application.

---

# 🚧 Future Improvements

Possible improvements include:

* GPU-accelerated transcription
* Streaming transcription
* Speaker diarization
* Timestamp-aware transcripts
* Better YouTube extraction/runtime support
* Conversation history
* Multi-video knowledge bases
* Improved RAG evaluation
* Retrieval citations with timestamps
* Persistent user sessions
* Authentication
* Background processing for long videos
* Async pipeline execution
* Caching transcripts and embeddings
* Production-grade vector database
* PDF/Markdown export
* Additional language support
* Cloud deployment
* Improved UI/UX

---

# 🎯 Example Use Cases

## 📚 Education

Process a lecture and ask:

> "What are the three main concepts explained in this lecture?"

## 🏢 Meetings

Process a meeting and ask:

> "What decisions were made?"

## 🎤 Interviews

Process an interview and ask:

> "What projects did the candidate discuss?"

## ▶️ YouTube Learning

Process a technical tutorial and ask:

> "Explain the architecture discussed in this video."

## 🧑‍💻 Developer Research

Process a conference talk and ask:

> "Which technologies were recommended?"

---

# 📌 Project Philosophy

The core idea behind the project is:

> **Don't just transcribe information — turn it into something you can search, summarize, understand, and interact with.**

The system combines:

```text
Speech Recognition
       +
LLM Processing
       +
Information Extraction
       +
Embeddings
       +
Vector Search
       +
RAG
       =
Conversational Video Intelligence
```

Instead of treating a video as something that must simply be watched, the project turns its content into a **queryable knowledge source**.

---

# 🔮 Future Architecture

The current application already provides a Streamlit interface. A future production architecture could extend the existing system with scalable backend processing, authentication, persistent storage, and deployment infrastructure.

```text
                         USER
                           │
                           ▼
                  ┌─────────────────┐
                  │  Streamlit UI   │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │  Backend/API    │
                  └────────┬────────┘
                           │
               ┌───────────┼───────────┐
               ▼           ▼           ▼
          Processing    LLM Layer   Vector DB
             │              │           │
             └──────────────┼───────────┘
                            ▼
                       RAG Engine
                            │
                            ▼
                     Conversational AI
```

The existing project provides the foundation for this evolution.
