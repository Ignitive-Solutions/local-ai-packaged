# Product Context

## Project Overview
This document outlines the core purpose and high-level architecture of the project.

## Goals
- To provide a centralized knowledge base for project context.
- To facilitate seamless transitions between different modes and tasks.
- To ensure consistency and maintainability across the project.

## Architecture
This project is a Docker Compose template that orchestrates several open-source AI and low-code tools. It's designed to provide a hybrid environment for building and running AI workflows, utilizing cloud-hosted Supabase and cloud LLM APIs.

**Key Components:**
- **n8n:** Low-code automation platform.
- **Supabase (Cloud):** Cloud-hosted database, vector store, and authentication.
- **Cloud LLM API (e.g., OpenAI, Anthropic, Cloud Ollama):** External LLM provider.
- **Open WebUI:** Interface for cloud LLMs and n8n agents.
- **Flowise:** No/low-code AI agent builder.
- **Qdrant:** High-performance vector store.
- **Neo4j:** Knowledge graph engine.
- **SearXNG:** Privacy-focused internet metasearch engine.
- **Caddy:** Managed HTTPS/TLS server.
- **Langfuse:** LLM engineering platform for observability.

## Constraints
- Requires Python, Git, and Docker for remaining self-hosted components.
- Environment variables must be configured for various services, including cloud Supabase and cloud LLM API credentials.
- GPU support for local components (e.g., Qdrant) varies by OS/hardware.
- Primarily designed for proof-of-concept projects, not fully optimized for production.

## Key Stakeholders
- Users looking for a self-hosted AI and low-code development environment.
- Developers building AI workflows and agents.
- Individuals interested in privacy-focused AI solutions.

## Initial Timestamp
2025-05-29T08:08:16Z