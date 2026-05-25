# VectorShift Pipeline Builder

A full-stack visual pipeline builder where users drag and drop nodes onto a canvas to compose data workflows. The backend validates whether the resulting graph is a valid DAG (no cycles).

---

## Tech Stack

| Layer    | Technology                       |
|----------|----------------------------------|
| Frontend | React 18, React Flow 11, Zustand |
| Backend  | Python, FastAPI, Pydantic        |

---

## Project Structure

```
├── backend/
│   └── main.py          # FastAPI server — DAG validation endpoint
└── frontend/
    └── src/
        ├── nodes/       # 9 node types (Input, LLM, Output, Text, Filter, Note, Math, API, Transform)
        ├── App.js        # Root layout
        ├── ui.js         # React Flow canvas + drag-and-drop logic
        ├── toolbar.js    # Node palette (draggable chips)
        ├── submit.js     # Submit button — POSTs pipeline to backend
        └── store.js      # Zustand state (nodes, edges, actions)
```

---

## Features

- **9 node types** — drag any node from the toolbar onto the canvas
- **Dynamic Text node** — type `{{variableName}}` and a live input handle appears for that variable automatically
- **Connect nodes** — draw edges between handles to wire the pipeline
- **DAG validation** — click Submit to send the graph to the backend; it returns node count, edge count, and whether the pipeline is cycle-free
- **Dark theme canvas** with minimap and snap-to-grid

---

## Running the Project

> Run the backend first, then the frontend. Both must be running for Submit to work.

### 1. Backend

```bash
cd backend

# Install dependencies
pip install fastapi uvicorn pydantic

# Start the server
uvicorn main:app --reload
```

Backend runs at **http://localhost:8000**

---

### 2. Frontend

```bash
cd frontend

# Install dependencies
npm install

# Start the app
npm start
```

Frontend runs at **http://localhost:3000** — open this in your browser.

---

## How It Works

1. Drag nodes from the top toolbar onto the canvas.
2. Connect nodes by dragging from one handle to another.
3. Click **Submit Pipeline** — the frontend sends the full graph (nodes + edges) to `POST /pipelines/parse`.
4. An alert shows the result: number of nodes, number of edges, and whether it's a valid DAG.

---

## API Reference

### `GET /`
Health check.
```json
{ "Ping": "Pong" }
```

### `POST /pipelines/parse`
Validates the pipeline graph.

**Request**
```json
{
  "nodes": [{ "id": "input-1" }, { "id": "llm-1" }],
  "edges": [{ "source": "input-1", "target": "llm-1" }]
}
```

**Response**
```json
{
  "num_nodes": 2,
  "num_edges": 1,
  "is_dag": true
}
```

The DAG check uses **Kahn's topological sort** — if all nodes are reachable via BFS from zero-in-degree nodes, the graph has no cycles.

---

## Author

**Ateesh Kumar**
