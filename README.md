## Agent Equity Research Analyst

A lightweight multi-agent research assistant that scans the web for investable ideas and returns a single, concise JSON suggestion per agent (asset, decision, reason). The Gradio UI runs four independent researcher personas in parallel and displays their outputs side-by-side.

### What this project does
- **Core goal**: Rapidly generate investment suggestions grounded in fresh web research.
- **How**: Each agent builds a query, calls a search tool (via MCP/Tavily and optional local tools), and reasons with an LLM to output a structured JSON suggestion.

### Agent structure (high level)
- **UI** (`app.py`): Gradio app with a topic input and four output panes. It triggers all researchers concurrently.
- **Researchers** (`src/researchers.py`):
  - Builds a small LangGraph with nodes: prepare_query → search → reason.
  - Uses MCP servers for tools: Tavily remote search; optionally a local tools server.
  - LLM: `ChatGoogleGenerativeAI` for reasoning to a single JSON suggestion.
- **Personas** (`src/strategies.py`): Four distinct strategies: Warren, George, Ray, Cathie.
- **Prompt templates** (`src/templates.py`): Shared instruction requiring JSON with keys: asset, decision, reason.
- **MCP config** (`src/mcp_params.py`): Remote Tavily server wiring (via `mcp-remote`).
- **Local tools server (optional)** (`src/tools_server.py`): MCP tools for Tavily search and Polygon price lookup.

### Prerequisites
- **Python**: 3.12+
- **uv** (fast Python package manager). Install: 

### Environment variables
Create a `.env` file in the project root with any keys you plan to use:

```bash
# Required for LLM reasoning
GOOGLE_API_KEY=your_google_genai_api_key

# Enables remote search via Tavily MCP (recommended)
TAVILY_API_KEY=your_tavily_key

# Optional: enables polygon price tool in local tools server
POLYGON_API_KEY=your_polygon_key
```

### Install dependencies with uv
From the project root:

```bash
uv sync
```

This will create a virtual environment and install dependencies from `pyproject.toml` and `uv.lock`.

### Run the Gradio app
With the environment activated and `.env` configured:

```bash
uv run python app.py
```

The app will print a local URL. Open it in your browser. On load, the four researchers run once. Click "Analyze" to run again on a new topic.

### Optional: run the local MCP tools server
The researchers try to use a remote Tavily MCP server automatically. You can also run the local tools server providing Tavily and Polygon tools:

```bash
uv run python src/tools_server.py
```

Note: The app attempts to spawn a local tools server automatically; running it manually is optional but can help during development.

### Project layout
- `app.py`: Gradio UI and entrypoint.
- `src/researchers.py`: Agent orchestration with LangGraph and MCP tool calls.
- `src/strategies.py`: Four persona strategies.
- `src/templates.py`: Prompt template and output contract.
- `src/mcp_params.py`: Remote MCP server parameters (Tavily).
- `src/tools_server.py`: Optional MCP tools (Tavily search, Polygon price).
- `pyproject.toml`: Dependencies and project metadata.



