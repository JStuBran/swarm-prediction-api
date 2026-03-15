# 🐟 Swarm Prediction API

A monetized prediction API powered by the **MiroFish swarm intelligence engine**. Spawns multi-agent simulations where diverse AI agents debate, analyze, and collectively predict outcomes — then returns a structured prediction report.

Payments powered by **x402** on Base USDC ($0.50/call).

## Features

- 🤖 **Multi-agent swarm simulation** — 10-500 agents with diverse roles (Analyst, Skeptic, Expert, Journalist, Activist, etc.)
- 🔄 **Multi-round emergent behavior** — agents build on each other's outputs across rounds
- 📊 **Structured prediction reports** — narratives, trends, sentiment distribution, and sample agent actions
- 💳 **x402 payments** — $0.50/call on Base USDC, no API keys needed for callers
- 🔌 **MCP compatible** — discoverable via `/mcp` endpoint

## Quick Start

### Local Development

```bash
# Clone the repo
git clone https://github.com/JStuBran/swarm-prediction-api
cd swarm-prediction-api

# Set up environment
cp .env.example .env
# Edit .env with your LLM_API_KEY and WALLET_ADDRESS

# Install dependencies
pip install -r requirements.txt

# Run
uvicorn main:app --host 0.0.0.0 --port 8080 --reload
```

### Docker

```bash
docker build -t swarm-prediction-api .
docker run -p 8080:8080 --env-file .env swarm-prediction-api
```

### Deploy to Railway

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app)

1. Fork this repo
2. Connect to Railway
3. Set environment variables: `LLM_API_KEY`, `WALLET_ADDRESS`, `PUBLIC_URL`
4. Deploy

## API Reference

### `GET /health`

Health check.

```json
{
  "status": "ok",
  "service": "swarm-prediction-api",
  "version": "1.0.0"
}
```

### `GET /mcp`

MCP manifest for tool discovery.

### `POST /api/predict`

Run a swarm prediction simulation.

**Payment required:** Include `x-payment` header with valid x402 payment token.

**Request:**
```json
{
  "seed_text": "Will AI regulation pass in the EU in 2025?",
  "num_agents": 100,
  "rounds": 20
}
```

**Response:**
```json
{
  "prediction_id": "uuid",
  "seed_text": "...",
  "num_agents": 100,
  "rounds": 20,
  "started_at": "2025-01-01T00:00:00Z",
  "completed_at": "2025-01-01T00:00:30Z",
  "duration_seconds": 30.5,
  "total_actions": 180,
  "sentiment_distribution": {
    "positive": 60,
    "negative": 40,
    "neutral": 80
  },
  "top_narratives": [
    "Strong institutional pressure pushing regulation forward",
    "Industry lobbying creating significant delays",
    "..."
  ],
  "emerging_trends": [
    "Risk-based frameworks gaining consensus",
    "..."
  ],
  "report": "## Executive Summary\n\nThe swarm simulation reveals...",
  "sample_actions": [...]
}
```

## How It Works

The API mirrors the **MiroFish OASIS simulation engine**:

1. **Agent Spawning** — Creates N agents with diverse archetypes (based on `oasis_profile_generator.py` patterns)
2. **Multi-Round Simulation** — Agents react to the scenario across rounds, building emergent narratives (mirrors `simulation_runner.py`)
3. **Report Generation** — ReACT-style synthesis of agent outputs into a prediction report (mirrors `report_agent.py`)

### Agent Archetypes

| Role | Behavior |
|------|----------|
| Analyst | Objective, data-driven, pattern-seeking |
| Skeptic | Questions claims, seeks contrary evidence |
| Expert | Deep domain knowledge, technical precision |
| Journalist | Story-focused, impact-driven |
| Activist | Passionate, high emotional intensity |
| Policymaker | Regulatory lens, risk-averse |
| ... | 15 total archetypes |

## x402 Payment Integration

This API uses the [x402 payment protocol](https://github.com/coinbase/x402):

1. Call `POST /api/predict` without payment → receive `402 Payment Required` with payment details
2. Pay $0.50 USDC on Base to the wallet address
3. Include payment proof in `x-payment` header
4. Get your prediction

**Example with x402 client:**
```python
from x402.client import PaymentClient

client = PaymentClient(private_key="0x...")
response = client.post(
    "https://your-api.railway.app/api/predict",
    json={"seed_text": "Your prediction question"}
)
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `LLM_API_KEY` | ✅ | OpenAI-compatible API key |
| `WALLET_ADDRESS` | ✅ | Base wallet address for USDC payments |
| `LLM_BASE_URL` | ❌ | LLM API base URL (default: OpenAI) |
| `LLM_MODEL_NAME` | ❌ | Model name (default: gpt-4o-mini) |
| `PUBLIC_URL` | ❌ | Public deployment URL (for MCP manifest) |

## License

MIT
