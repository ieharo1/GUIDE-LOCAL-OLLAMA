# Ollama Local Professional Guide
[![Docker Ready](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-black)](https://ollama.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Language: English](https://img.shields.io/badge/Language-English-blue)](#)
[![Status: Production Ready](https://img.shields.io/badge/Status-Production%20Ready-brightgreen)](#)

A practical guide to run local LLMs with Ollama using Docker, designed for portfolio-ready engineering and automation workflows.

## Goals

- Run open-source models without external APIs.
- Keep sensitive data inside local infrastructure.
- Standardize deployment with Docker Compose.
- Integrate cleanly with scripts, backends, and n8n.

## Use Cases

- Internal documentation assistant.
- Offline technical Q&A.
- Log analysis and ticket triage.
- Local RAG and agent prototypes.

## Stack

- Ollama
- Docker / Docker Compose
- Local REST API at `http://localhost:11434`

## Project Structure

```text
.
├── docker-compose.yml
├── .env.example
└── README.md
```

## Requirements

- Docker Desktop or Docker Engine + Compose
- Recommended: 16 GB RAM minimum
- Optional: NVIDIA GPU + NVIDIA Container Toolkit

## 1) Configure port

```bash
cp .env.example .env
```

Update if needed:

```env
OLLAMA_PORT=11434
```

## 2) Start Ollama (CPU)

```bash
docker compose up -d ollama
```

Check logs:

```bash
docker compose logs -f ollama
```

## 3) Start Ollama (optional GPU profile)

```bash
docker compose --profile gpu up -d ollama-gpu
```

Use this only when NVIDIA runtime is properly configured.

## 4) Pull models inside the container

```bash
docker compose exec ollama ollama pull llama3
```

If using GPU service:

```bash
docker compose exec ollama-gpu ollama pull llama3
```

## 5) Test generation via API

```bash
curl http://localhost:11434/api/generate \
  -d '{"model":"llama3","prompt":"Explain hexagonal architecture","stream":false}'
```

## 6) Python integration

```python
import requests

url = "http://localhost:11434/api/generate"
payload = {
    "model": "llama3",
    "prompt": "Summarize microservices advantages",
    "stream": False,
}

response = requests.post(url, json=payload, timeout=120)
response.raise_for_status()
print(response.json()["response"])
```

## Security

- Do not expose `11434` publicly without auth and reverse proxy.
- Isolate network in enterprise environments.
- Add rate limiting when serving multiple clients.

## Troubleshooting

- Container not responding: inspect `docker compose logs`.
- Model missing: run `ollama pull <model>`.
- Low performance: use quantized models (`q4`, `q8`) or enable GPU.

## License

MIT

