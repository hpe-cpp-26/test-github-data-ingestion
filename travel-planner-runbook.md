# Travel Planner AI Agent: Local Development Runbook

This guide outlines the steps to initialize and operate the Travel Planner AI agent locally, utilizing your `uv` workspace and `FastMCP` server configuration.

---

## Setup & Initialization

### 1. Project Sync

Navigate to the root directory and synchronize the workspace environment:

```bash
uv sync

```

### 2. Configure Environment

Initialize your local environment variables from the template:

```bash
cp .env.example .env

```

*Ensure you have populated the `.env` file with your `OPENAI_API_KEY`, `TAVILY_API_KEY` (or relevant search provider), and any required database connection strings.*

---

## Running the Services

### 1. Start the FastMCP Server

The agent relies on the FastMCP server for tool registration and inter-agent communication. Launch the server:

```bash
uv run python -m app.server.mcp_host

```

### 2. Initiate the Agent Controller

Once the server is active, start the agent interface or controller script to begin processing travel requests:

```bash
uv run python -m app.agents.travel_controller

```

---

## Development Workflow

### Tool Discovery & Testing

To verify that all registered tools (e.g., flight search, hotel booking, itinerary generation) are correctly exposed via the MCP protocol:

```bash
uv run python -m app.scripts.inspect_tools

```

### Running Agent Simulations

Test the agent's logic flow with a predefined travel scenario:

```bash
uv run python -m app.tests.run_scenario --file examples/trip_query_tokyo.json

```



## Maintenance & Debugging

### Clean Workspace

If you encounter dependency conflicts or need to perform a fresh build:

```bash
# Remove virtual environment and reinstall
rm -rf .venv
uv sync

```

### Monitoring Agent State

Use the following command to log the state transitions of the LangGraph agent:

```bash
export LOG_LEVEL=DEBUG
uv run python -m app.agents.travel_controller --verbose

```



## Troubleshooting

* **Server Timeout:** If the MCP server fails to respond, ensure the port defined in your configuration (default: 8000) is not being held by a zombie process.
* **Missing Tool Calls:** If the agent fails to invoke a tool, verify the `tool_registry` in your `server.py` and ensure the function signatures are decorated correctly with `@mcp.tool()`.
* **Database State:** If the agent is returning stale travel data, clear the local SQLite cache:
```bash
rm data/agent_state.db

```

