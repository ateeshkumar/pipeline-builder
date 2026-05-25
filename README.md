# VectorShift Pipeline Builder

A visual pipeline builder with a drag-and-drop canvas for composing node-based workflows, backed by a FastAPI service that validates the graph structure.

## Overview

This is a full-stack technical assessment submission for VectorShift. The app lets users visually construct data pipelines by wiring together typed nodes on a React Flow canvas. On submit, the frontend sends the graph to a Python backend that counts nodes/edges and checks whether the pipeline forms a valid Directed Acyclic Graph (DAG) using Kahn's topological sort algorithm.

## Tech Stack

| Layer    | Technology                          |
|----------|-------------------------------------|
| Frontend | React 18, React Flow 11, Zustand    |
| Backend  | Python, FastAPI, Pydantic           |
| Styling  | Plain CSS (dark theme)              |

## Project Structure

```
├── backend/
│   └── main.py          # FastAPI app — /pipelines/parse endpoint + DAG check
└── frontend/
    ├── public/
    └── src/
        ├── nodes/       # Individual node components (Input, LLM, Output, Text, …)
        │   ├── BaseNode.js
        │   ├── inputNode.js
        │   ├── llmNode.js
        │   ├── outputNode.js
        │   ├── textNode.js
        │   ├── filterNode.js
        │   ├── noteNode.js
        │   ├── mathNode.js
        │   ├── apiNode.js
        │   └── transformNode.js
        ├── App.js        # Root component
        ├── ui.js         # React Flow canvas with drag-and-drop
        ├── toolbar.js    # Draggable node palette
        ├── submit.js     # Submit button + fetch logic
        ├── store.js      # Zustand global state (nodes, edges, actions)
        └── index.css     # Dark-theme stylesheet
```

## Features

- **9 node types** — Input, Output, LLM, Text, Filter, Note, Math, API, Transform
- **Drag-and-drop canvas** — nodes are dragged from the toolbar onto the React Flow canvas
- **Dynamic Text node** — detects `{{variable}}` template syntax and auto-creates input handles for each variable
- **Shared Zustand store** — all components read from and write to a single reactive state tree
- **DAG validation** — the backend runs Kahn's topological sort on submit and reports whether the graph is cycle-free
- **Pipeline summary alert** — node count, edge count, and DAG status shown on submit

## Getting Started

### Prerequisites

- Node.js 16+
- Python 3.9+

### Backend

```bash
cd backend
pip install fastapi uvicorn pydantic
uvicorn main:app --reload
# Runs on http://localhost:8000
```

### Frontend

```bash
cd frontend
npm install
npm start
# Runs on http://localhost:3000
```

Open [http://localhost:3000](http://localhost:3000) to use the app. The frontend expects the backend at `http://localhost:8000`.

## API

### `GET /`
Health check — returns `{"Ping": "Pong"}`.

### `POST /pipelines/parse`

Accepts a pipeline payload and returns graph statistics.

**Request body**
```json
{
  "nodes": [{ "id": "input-1", ... }],
  "edges": [{ "source": "input-1", "target": "llm-1", ... }]
}
```

**Response**
```json
{
  "num_nodes": 3,
  "num_edges": 2,
  "is_dag": true
}
```

## Implementation Notes

- `BaseNode.js` is a shared wrapper that all node types extend — it renders the colored header strip and maps `handles` props to React Flow `<Handle>` elements, keeping individual node files thin.
- The DAG check in `main.py` uses Kahn's algorithm (BFS-based topological sort): build an in-degree map, start from zero-in-degree nodes, and confirm all nodes were visited. If `visited == len(nodes)`, the graph is acyclic.
- The Text node uses a regex (`/\{\{([a-zA-Z_][a-zA-Z0-9_]*)\}\}/g`) to extract variable names from its content and dynamically renders one `target` handle per unique variable.

## Author

Ateesh Kumar
