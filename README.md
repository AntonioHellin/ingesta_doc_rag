# rag-document-assistant

An intelligent Retrieval-Augmented Generation (RAG) assistant designed for ingesting, indexing, and querying multi-format documentation. Powered by LangChain, OpenAI embeddings, and an interactive Gradio web interface.

---

## Project Overview

`rag-document-assistant` enables contextual conversational AI against custom knowledge bases. It ingests local documents (such as PDF and text files), chunks and embeds their contents into an in-memory vector store, and enriches LLM queries with semantically retrieved context.

The system includes both an interactive chat interface with source attribution and a dedicated document management portal for uploading documents, querying vector stores directly, and monitoring knowledge base status.

---

## Features

- **Document Ingestion Pipeline**: Ingest and process documents (PDF, TXT, DOCX) using `RecursiveCharacterTextSplitter`.
- **Vector Retrieval**: Embeddings generated using OpenAI (`text-embedding-3-small`) with cosine similarity filtering.
- **Context-Aware Conversational Agent**: Powered by OpenAI models with dynamic conversation history trimming and source citation.
- **Interactive Web Interface**: Built with Gradio Blocks featuring dual tabs:
  - **Chat**: Real-time conversational interface with memory and knowledge base diagnostics.
  - **Document Management**: File upload handler, manual raw text injection, semantic search testbed, and vector store reset.
- **Production Containerization**: Complete `Dockerfile` and automated Railway deployment script (`deploy_to_railway.sh`).

---

## Architecture Flow

```
User Document (PDF/Text) ──> PyPDFLoader / TextSplitter
                                    │
                                    ▼
                         OpenAI Embeddings (text-embedding-3-small)
                                    │
                                    ▼
                          InMemoryVectorStore
                                    │
User Query ───────> RAGRetriever ───┴───> Top-k Context ───> ChatOpenAI LLM ───> Response
```

---

## Prerequisites

- **Python**: 3.10 or higher
- **OpenAI API Key**: Access to OpenAI API (with permissions for embedding and completion models)
- **Git**

---

## Installation & Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/AntonioHellin/rag-document-assistant.git
   cd rag-document-assistant
   ```

2. **Create and activate a virtual environment**:
   ```bash
   # On macOS/Linux
   python3 -m venv .venv
   source .venv/bin/activate

   # On Windows (PowerShell)
   python -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

3. **Install dependencies**:
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

---

## Environment Configuration

Copy the example environment configuration and supply your OpenAI API key:

```bash
cp .env.example .env
```

Edit `.env`:

```ini
# Required: OpenAI API Key
OPENAI_API_KEY=your_actual_openai_api_key_here

# Optional: Default Server Port (defaults to 7860)
PORT=7860
```

> [!CAUTION]
> Never commit `.env` or sensitive API tokens to version control. The repository `.gitignore` is preconfigured to prevent tracking environment files.

---

## Usage

### Running Locally

Start the assistant with default options:

```bash
python app.py
```

### Command-Line Arguments

The application CLI supports customization for hosting, models, and network sharing:

```bash
python app.py --host 0.0.0.0 --port 7860 --model gpt-4o-mini --temperature 0.7
```

Available flags:
- `--host`: Network interface to bind (default: `0.0.0.0`)
- `--port`: Port number to listen on (default: `7860` or `$PORT`)
- `--model`: OpenAI model identifier (default: `gpt-5` / `gpt-4o-mini`)
- `--temperature`: Sampling temperature for responses (default: `0.7`)
- `--share`: Generates a public Gradio share link
- `--debug`: Launches Gradio in debug mode

Access the interface in your browser at `http://localhost:7860`.

---

## Deployment

### Docker

Build and run the containerized service:

```bash
docker build -t rag-document-assistant .
docker run -p 7860:7860 --env-file .env rag-document-assistant
```

### Railway

Ensure the [Railway CLI](https://docs.railway.app/guides/cli) is installed and authenticated, then run:

```bash
chmod +x deploy_to_railway.sh
./deploy_to_railway.sh
```

---
