# SEFS — Semantic Entropy File System

> **Team:** LogicCrafters

A self-organising file manager that uses **semantic analysis** to automatically cluster and organise files into meaningful folders. Drop files into a monitored directory and watch SEFS analyse their content, compute semantic similarity, and reorganise them into intelligently named folders — all visualised in real time through an interactive 2D force-directed graph.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.1-green?logo=flask)
![D3.js](https://img.shields.io/badge/D3.js-v7-orange?logo=d3.js)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## Table of Contents

1. [Description](#description)
2. [Tech Stack](#tech-stack)
3. [Project Structure](#project-structure)
4. [How to Run the Project](#how-to-run-the-project)
5. [Dependencies](#dependencies)
6. [Important Instructions](#important-instructions)
7. [CLI Options](#cli-options)
8. [REST API](#rest-api)
9. [WebSocket Events](#websocket-events)
10. [How Clustering Works](#how-clustering-works)
11. [Configuration File](#configuration-file)
12. [License](#license)

---

## Description

SEFS (Semantic Entropy File System) is a Python-based tool that monitors a directory for file changes and **automatically organises files into semantically meaningful folders**. It works by:

1. Extracting text content from documents (PDFs, text files, markdown, etc.).
2. Converting each document into a numerical vector using TF-IDF or Sentence Transformer embeddings.
3. Clustering related documents together using K-Means with automatic cluster-count detection (silhouette scoring).
4. Physically moving files into intelligently named folders derived from the top keywords of each cluster.
5. Providing a **real-time web dashboard** with an interactive D3.js force-directed graph to visualise the file organisation.

### Key Features

- **Semantic Clustering** — TF-IDF or Sentence-Transformer embeddings + K-Means with automatic cluster-count selection via silhouette score.
- **OS-Level Folder Organisation** — Files are physically moved into semantically named directories derived from top keywords in each cluster.
- **Real-Time File Watching** — Watchdog-based monitor with debouncing; detects new, modified, moved, and deleted files instantly.
- **Live 2D Visualisation** — Interactive D3.js force-directed graph with cluster hulls, drag/zoom, tooltips, and colour-coded nodes.
- **WebSocket Updates** — Flask-SocketIO pushes changes to the browser the moment the file system is reorganised.
- **PDF & Text Extraction** — Reads `.pdf` (via PyPDF2), `.txt`, `.md`, `.rst`, `.csv`, `.json`, and `.log` files with multi-encoding fallback.
- **REST API** — Endpoints for status, graph data, folder structure, configuration, and manual actions (rescan, reset).
- **Configurable** — CLI flags, JSON config file, or in-code defaults for every tunable parameter.

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Language** | Python 3.10+ | Core application logic |
| **Web Framework** | Flask 3.1 | REST API & web server |
| **Real-Time Communication** | Flask-SocketIO + Eventlet | WebSocket-based live updates |
| **Frontend Visualisation** | D3.js v7 | Interactive force-directed graph |
| **Frontend Styling** | HTML5, CSS3, JavaScript | Dark-theme responsive UI |
| **NLP / Embeddings** | scikit-learn (TF-IDF), Sentence-Transformers | Document vectorisation |
| **Clustering** | scikit-learn (K-Means) | Unsupervised document grouping |
| **PDF Extraction** | PyPDF2 | Text extraction from PDF files |
| **File Monitoring** | Watchdog | Real-time file system event detection |
| **Numerical Computing** | NumPy | Vector operations |
| **Deep Learning Backend** | PyTorch (optional) | Backend for Sentence-Transformers |

---

## Project Structure

```
LogicCrafters_Vibe_Coding/
├── main.py                      # Entry point & CLI
├── requirements.txt             # Python dependencies
├── README.md                    # Project documentation (this file)
├── monitored_root/              # Default directory to monitor
│   ├── <semantic-folder-1>/     # Auto-created by SEFS
│   ├── <semantic-folder-2>/
│   └── ...
└── src/
    └── sefs/
        ├── __init__.py
        ├── config.py            # Centralised configuration
        ├── extractor.py         # PDF & text content extraction
        ├── semantic_engine.py   # TF-IDF / Transformer embeddings + K-Means
        ├── folder_manager.py    # OS-level folder creation & file moves
        ├── sync_manager.py      # Central pipeline coordinator
        ├── watcher.py           # File system watcher (watchdog)
        ├── web_server.py        # Flask + SocketIO server & REST API
        ├── static/
        │   ├── css/
        │   │   └── style.css    # Dark-theme UI styles
        │   └── js/
        │       └── app.js       # D3.js graph + Socket.IO client
        └── templates/
            └── index.html       # Main UI template
```

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        main.py                              │
│              CLI parsing · Component init · Server start     │
└────────────────────────┬────────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
   ┌───────────┐  ┌─────────────┐  ┌───────────┐
   │  Watcher  │  │ SyncManager │  │ WebServer  │
   │ (watchdog)│─▶│ (orchestra) │◀─│(Flask+SIO) │
   └───────────┘  └──────┬──────┘  └───────────┘
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
        ┌──────────┐ ┌────────┐ ┌──────────────┐
        │Extractor │ │Semantic│ │FolderManager │
        │(PDF/text)│ │ Engine │ │  (OS moves)  │
        └──────────┘ └────────┘ └──────────────┘
```

**Pipeline:** File change → Content extraction → Embedding + Clustering → Folder reorganisation → WebSocket notification → UI update.

---

## How to Run the Project

### Prerequisites

- **Python 3.10 or higher** installed on your system.
- **pip** (Python package manager).
- **Git** (to clone the repository).

### Step 1 — Clone the Repository

```bash
git clone https://github.com/LogicCrafters/LogicCrafters_Vibe_Coding.git
cd LogicCrafters_Vibe_Coding
```

### Step 2 — Create & Activate a Virtual Environment

**Windows:**
```bash
python -m venv .venv
.\.venv\Scripts\activate
```

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Step 3 — Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4 — Run the Application

```bash
python main.py
```

### Step 5 — Open the Web Dashboard

Open your browser and navigate to:

```
http://127.0.0.1:5000
```

Drop `.pdf`, `.txt`, `.md`, or other supported files into the `monitored_root/` folder and watch them get organised automatically.

---

## Dependencies

All dependencies are listed in `requirements.txt`. Install them with `pip install -r requirements.txt`.

| Package | Version | Purpose |
|---|---|---|
| `flask` | 3.1.0 | Web server & REST API |
| `flask-socketio` | 5.5.1 | Real-time WebSocket communication |
| `eventlet` | 0.37.0 | Async networking backend for SocketIO |
| `watchdog` | 6.0.0 | File system monitoring |
| `PyPDF2` | 3.0.1 | PDF text extraction |
| `scikit-learn` | 1.6.1 | TF-IDF vectorisation & K-Means clustering |
| `numpy` | 2.2.3 | Numerical / vector operations |
| `sentence-transformers` | 3.4.1 | *(Optional)* Transformer-based embeddings |
| `torch` | 2.6.0 | *(Optional)* PyTorch backend for transformers |

> **Note:** `sentence-transformers` and `torch` are only required if you use `--method transformer`. The default `tfidf` method works without them.

---

## Important Instructions

### Supported File Types

| Extension | Extraction Method |
|---|---|
| `.pdf` | PyPDF2 page-by-page text extraction |
| `.txt` `.md` `.rst` `.log` | Raw text with encoding fallback (UTF-8 → Latin-1 → CP1252 → ASCII) |
| `.csv` `.json` | Read as plain text |

### Embedding Methods

| Method | Flag | Description |
|---|---|---|
| **TF-IDF** *(default)* | `--method tfidf` | Fast, no GPU required. Best for documents with distinct keyword profiles. Vocabulary size up to 5,000 features with English stop-word removal. |
| **Sentence Transformers** | `--method transformer` | Uses `all-MiniLM-L6-v2` (384-dim). More accurate for semantically similar docs with different wording. Requires `sentence-transformers` and `torch`. |

### Things to Keep in Mind

- **Minimum files:** You need **at least 3 files** in the monitored folder for clustering to work (K-Means requires K ≥ 2 and each cluster needs members).
- **File placement:** Place files directly inside `monitored_root/` (or your custom `--root` path). SEFS will create sub-folders automatically.
- **Do not manually rename** the semantic folders (prefixed with the configured prefix) while SEFS is running — it will recreate them on the next scan.
- **Rescan & Reset:** Use the web UI buttons or the REST API (`POST /api/rescan`, `POST /api/reset`) to trigger a manual rescan or reset the folder structure.
- **Large PDFs:** Very large or scanned (image-only) PDFs may yield little or no text. SEFS works best with text-based documents.
- **Firewall:** The web server runs on `127.0.0.1:5000` by default (localhost only). To expose it on your network, use `--host 0.0.0.0`.

---

## CLI Options

| Flag | Default | Description |
|---|---|---|
| `--root PATH` | `./monitored_root` | Directory to monitor |
| `--method {tfidf,transformer}` | `tfidf` | Embedding method |
| `--clusters N` | `0` (auto) | Fixed cluster count (0 = auto-detect) |
| `--threshold F` | `0.15` | Similarity threshold (0.0–1.0) |
| `--port N` | `5000` | Web UI port |
| `--host ADDR` | `127.0.0.1` | Web UI host |
| `--debug` | off | Enable debug logging |

**Examples:**

```bash
# Monitor a custom folder with transformer embeddings
python main.py --root "C:\Users\Me\Documents" --method transformer

# Fixed 5 clusters on port 8080
python main.py --clusters 5 --port 8080

# Debug mode
python main.py --debug
```

---

## REST API

| Endpoint | Method | Description |
|---|---|---|
| `/` | GET | Web UI |
| `/api/status` | GET | System status (file count, cluster count, method, etc.) |
| `/api/graph` | GET | Graph data — nodes & edges for D3.js visualisation |
| `/api/folders` | GET | Managed folder structure with file lists |
| `/api/config` | GET | Current configuration |
| `/api/rescan` | POST | Trigger a full rescan & re-cluster |
| `/api/reset` | POST | Move all files back to root & remove semantic folders |
| `/api/open_folder` | POST | Open a folder in the OS file explorer |

---

## WebSocket Events

| Event | Direction | Payload |
|---|---|---|
| `connect` | Client → Server | Server responds with initial `graph_update` + `folder_update` |
| `graph_update` | Server → Client | `{ nodes: [...], edges: [...] }` |

| `folder_update` | Server → Client | `{ folders: [...] }` |
| `status_update` | Server → Client | `{ file_count, cluster_count, ... }` |
| `request_rescan` | Client → Server | Triggers full pipeline |
| `request_reset` | Client → Server | Resets folder structure |

---

## How Clustering Works

1. **Content Extraction** — Text is pulled from each file (PyPDF2 for PDFs, multi-encoding fallback for text files).
2. **Embedding** — Each document is converted to a vector via TF-IDF or Sentence Transformers.
3. **Optimal K Selection** — Silhouette scores are computed for K = 2…10; the K with the highest score is chosen.
4. **K-Means Clustering** — Files are assigned to clusters.
5. **Folder Naming** — The top-2 TF-IDF keywords from each cluster's combined text become the folder name (e.g., *"Coding & Vibe"*, *"Pollutants & Atmosphere"*).
6. **File Movement** — Files are physically moved into their cluster's folder; stale empty folders are cleaned up.

---

## Configuration File

Optionally, create `sefs_config.json` in the project root to persist settings:

```json
{
  "root_folder": "./monitored_root",
  "embedding_method": "tfidf",
  "num_clusters": 0,
  "similarity_threshold": 0.15,
  "watch_extensions": [".pdf", ".txt", ".md", ".rst", ".csv", ".json", ".log"],
  "host": "127.0.0.1",
  "port": 5000
}
```

---

## License

This project is licensed under the **MIT License**.

![0dcb0809-36b2-4882-acb7-70edfeadc17e](https://github.com/user-attachments/assets/97eff071-77a5-4b9a-b1bb-fb11baf8f001)
