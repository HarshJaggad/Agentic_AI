# Agentic_AI
# 📄 Financial & Document RAG Chatbot

An advanced **Retrieval-Augmented Generation (RAG)** chatbot built with **Streamlit** and **LangChain** that answers questions using **only** the content of your uploaded PDFs, such as financial filings, quarterly reports, contracts, and multi-page documents.

If the answer is not in the documents, the bot says: *"I don't know based on the uploaded documents."* This avoids hallucination.

---

## 🌟 Features

- **📊 Table-Aware Chunking**: Chunk size `1500` with `300` overlap keeps financial tables and metrics intact.
- **💾 Disk Persistence**: ChromaDB saves embeddings to `./chroma_db`, so you don't need to re-upload files after a restart.
- **🔍 Grounding Expander**: View the exact retrieved chunks behind every answer.
- **📍 Page-Level Citations**: Source badges show file name and page number, e.g. `Bajaj_Q1.pdf (Page 14)`.
- **🎛️ Adjustable Retrieval Depth (`top_k`)**: Sidebar slider (4–15) for multi-document comparisons such as QoQ or YoY analysis.
- **🤖 Selectable LLM**: Choose between `openai/gpt-oss-120b`, `openai/gpt-oss-20b`, and `qwen/qwen3.6-27b` via Groq.
- **📂 Multi-PDF Upload**: Upload several PDFs at once; duplicates are skipped.
- **🚫 Scanned PDF Detection**: Image-only PDFs with no extractable text are rejected with a clear message.
- **🗑️ One-Click Reset**: Delete the entire knowledge base from the sidebar.
- **🔒 Security**: `.gitignore` excludes `.env` and `chroma_db/`.

---

## 🏗️ Architecture

```
Upload PDFs → Text Validation (OCR check)
    → Table-Aware Split (1500 chars / 300 overlap)
    → Local Embeddings (HuggingFace all-MiniLM-L6-v2)
    → Persistent Vector Store (ChromaDB on disk)
    → Retrieve top_k chunks (configurable 4–15)
    → Groq LLM Generation (strict context-only)
    → Output: Answer + Context Expander + Page Citations
```

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python 3.10+** | Programming language |
| **Streamlit** | Web UI (chat, sidebar, uploader) |
| **LangChain** | Framework connecting all RAG steps |
| **PyPDF** | PDF text extraction |
| **HuggingFace (sentence-transformers)** | Local embeddings with `all-MiniLM-L6-v2` |
| **ChromaDB** | Persistent vector database |
| **Groq API** | Fast LLM inference |
| **python-dotenv** | Loads the API key from `.env` |

---

## 📁 Project Structure

```
Learning_Project/
├── app.py              # Streamlit UI and chat flow
├── rag_utils.py        # RAG pipeline (load, split, embed, store, retrieve, generate, cite)
├── requirements.txt    # Python dependencies
├── .env.example        # Template for API key
├── .gitignore          # Excludes .env and chroma_db/
└── README.md
```

---

## 🚀 Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/Learning_Project.git
cd Learning_Project
```

### 2. (Optional) Create a virtual environment

```bash
python -m venv venv
# Windows
venv\Scripts\activate
# Mac / Linux
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add your Groq API key

Get a free key from [console.groq.com](https://console.groq.com), then copy `.env.example` to `.env`:

```bash
# Windows
copy .env.example .env
# Mac / Linux
cp .env.example .env
```

Open `.env` and set:

```env
GROQ_API_KEY=your_groq_api_key_here
```

> ⚠️ Never commit your `.env` file or share your API key publicly.

### 5. Run the app

```bash
streamlit run app.py
```

The app opens at **http://localhost:8501**.

---

## 💡 How to Use

1. Upload one or more PDF files from the sidebar.
2. Click **⚙️ Process Uploaded PDFs** and wait for processing to finish.
3. Select the Groq model and adjust the `top_k` slider if needed.
4. Ask questions in the chat box, for example:
   - *"What is the net profit for Q1?"*
   - *"Compare NPA between Q1 and Q2."*
5. Expand **🔍 View Retrieved Context Chunks** to verify the answer, and check the source badges for page citations.
6. Use **🗑️ Reset & Delete Knowledge Base** to start fresh.

---

## 🔍 How It Works

1. **Load**: `PyPDFLoader` reads each PDF page and rejects empty or scanned files.
2. **Split**: `RecursiveCharacterTextSplitter` creates 1500-character chunks with 300 overlap.
3. **Tag**: Every chunk stores its source file name and page number.
4. **Embed**: `all-MiniLM-L6-v2` converts chunks into vectors locally on CPU.
5. **Store**: Vectors are saved in ChromaDB (`./chroma_db`).
6. **Retrieve**: The user's question is embedded and the top-k most similar chunks are fetched.
7. **Generate**: The Groq LLM answers using only the retrieved context (temperature `0.2`).
8. **Cite**: Unique file and page references are displayed with the answer.

---

## ⚠️ Limitations

- Scanned or image-only PDFs are not supported (OCR is not included).
- Answer quality depends on the retrieved chunks and the selected model.
- Complex tables may lose some formatting during text extraction.

---

## 🔮 Future Scope

- OCR support for scanned PDFs
- Hybrid search (BM25 + vector search) and re-ranking
- Support for Word, Excel, and CSV files
- User authentication and multi-user knowledge bases
- Cloud deployment
- Chat export (PDF / TXT)
