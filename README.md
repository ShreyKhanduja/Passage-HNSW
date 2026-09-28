# Passage

Passage is a lightweight document search app. Upload files, index their contents with Jina embeddings in Qdrant Cloud, and retrieve relevant passages with semantic search.

## Features

- Upload PDF, DOCX, and TXT documents.
- Search indexed documents and see matching passages with relevance scores and source filenames.
- Use a single-file HTML, CSS, and JavaScript frontend with a FastAPI backend.
- Store vectors in Qdrant Cloud and retrieve nearest neighbors with its HNSW index.

## HNSW Indexing

Passage uses Qdrant's HNSW (Hierarchical Navigable Small World) vector index for approximate nearest-neighbor search. During ingestion, each document is split into overlapping chunks of 300 words with a 50-word overlap. Jina's `jina-embeddings-v3` converts each chunk into a 1024-dimensional vector, which is stored in the `rag_collection` collection using cosine distance.

When you search, Passage embeds the query with the same model and asks Qdrant for the closest vectors. Qdrant's HNSW graph helps find relevant chunks efficiently without comparing the query against every stored vector. Results include each chunk's source filename and relevance score. HNSW index tuning is managed by Qdrant; this project does not set custom graph parameters.

## Requirements

- Python 3.10 or newer
- A Qdrant Cloud cluster URL and API key
- A Jina API key

## Setup

From the repository root, create and activate a virtual environment, then install the backend dependencies:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r backend\requirements.txt
```

On macOS or Linux, activate it with `source .venv/bin/activate` and use `backend/requirements.txt` as the dependency file.

Create `backend/.env` from the example:

```powershell
Copy-Item backend\.env.example backend\.env
```

Set the following values in `backend/.env`:

```dotenv
JINA_API_KEY=your_jina_api_key
QDRANT_URL=https://your-cluster-url:6333
QDRANT_API_KEY=your_qdrant_cloud_api_key
```

Keep this file private; it contains credentials.

## Run

Start the API in a terminal:

```powershell
cd backend
uvicorn main:app --reload
```

Start the frontend in a second terminal from the repository root:

```powershell
cd frontend
python -m http.server 5173
```

Open [http://localhost:5173](http://localhost:5173). The frontend expects the API at `http://localhost:8000` by default; you can change the API address in the sidebar.

## How To Use

1. Open **Add documents** and select one or more PDF, DOCX, or TXT files.
2. Wait for indexing to finish; the confirmation reports how many passages were added.
3. Open **Search**, enter a question or phrase, and review the matching passages and source files.

## Project Structure

```text
backend/       FastAPI API and document retrieval service
frontend/      Single-file web interface (index.html)
```

## License

MIT
