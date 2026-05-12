# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Mem0** ("mem-zero") is an intelligent memory layer for AI agents and assistants — provides persistent, personalized memory via both a hosted platform API and self-hosted open-source SDKs. This is a **polyglot monorepo** (Python + TypeScript). License: Apache-2.0.

## Essential Commands

### Python SDK (`mem0/`)
```bash
hatch shell dev_py_3_11       # dev environment (Python 3.11)
make lint                      # ruff check (line-length 120)
make format                    # ruff format
make test                      # pytest tests/
```

### TypeScript SDK (`mem0-ts/`)
```bash
cd mem0-ts && pnpm install
pnpm run build                 # tsup
pnpm run test                  # jest
pnpm run typecheck             # tsc --noEmit
```

### Python CLI (`cli/python/`)
```bash
cd cli/python && pip install -e ".[dev]"
ruff check . && ruff format .  # line-length 100
pytest
```

### Node CLI (`cli/node/`)
```bash
cd cli/node && pnpm install
pnpm run lint                  # biome
pnpm run test                  # vitest
```

### Server (`server/`)
```bash
cd server
docker compose up              # FastAPI + PostgreSQL/pgvector + Neo4j + Dashboard
# API: localhost:8888, Dashboard: localhost:3000
```

### OpenMemory (`openmemory/`)
```bash
cd openmemory && docker compose up   # Qdrant + API + UI
```

## Architecture

- **`mem0/`** — Core Python SDK: `Memory` (self-hosted), `MemoryClient` (hosted platform). Provider pattern across LLMs (24), vector stores (30), embeddings (15), graphs (4), rerankers (5). Each category has `base.py` + concrete implementations.
- **`mem0-ts/`** — TypeScript SDK: `MemoryClient` from `mem0ai`, `Memory` from `mem0ai/oss`.
- **`server/`** — FastAPI REST server, Docker Compose with PostgreSQL/pgvector + Neo4j 5.x.
- **`openmemory/`** — Self-hosted platform: `api/` (FastAPI + Alembic + MCP), `ui/` (Next.js 15).
- **`cli/`** — Python (Typer) and Node (Commander) CLIs.
- **`skills/`** — Claude Code skill definitions (reference + pipeline).

## Key Conventions

- **Python:** Ruff (line-length 120 for SDK, 100 for CLI) + isort (profile=black). Pydantic v2. `snake_case.py` files. Provider pattern: inherit from `base.py`, config in `configs.py`, register in `__init__.py`.
- **TypeScript:** pnpm only. tsup builds. `snake_case.ts` files. Linter/formatter varies by package (Prettier/Biome/ESLint).
- **Dependencies:** New Python deps go in optional groups in `pyproject.toml`, never in core `dependencies`.
- **Do NOT** commit `.env` files, use npm/yarn, modify `embedchain/` casually, or skip pre-commit hooks.

## CI/CD

Python SDK: `ci.yml` (ruff + pytest on 3.10–3.12). TypeScript SDK: `ts-sdk-ci.yml` (prettier + build + jest on Node 20/22). Publishing via OIDC trusted publishing (no tokens). Full details: see `AGENTS.md`.

---

See `AGENTS.md` for comprehensive details: full repo structure, all test commands, provider authoring guide, PR template, evaluation framework, CLI workflows, and contributing guidelines.
