# Repository Overview: Self-hosted AI Package

This repository, "Self-hosted AI Package," is an open-source Docker Compose template designed to quickly set up a comprehensive local AI and low-code development environment. It integrates various tools to enable users to build and run self-hosted AI workflows, focusing on privacy and local execution.

## Key Technologies Included

The package bundles the following core components:

*   **n8n:** A low-code automation platform with extensive integrations and AI components.
*   **Supabase:** An open-source database-as-a-service, serving as a database, vector store, and authentication provider.
*   **Ollama:** A cross-platform tool for installing and running local Large Language Models (LLMs).
*   **Open WebUI:** A ChatGPT-like interface for interacting with local models and n8n agents.
*   **Flowise:** A no/low-code AI agent builder that complements n8n.
*   **Qdrant:** A high-performance vector store, offering an alternative to Supabase for faster RAG operations.
*   **Neo4j:** A knowledge graph engine for advanced AI applications like GraphRAG.
*   **SearXNG:** A privacy-focused, open-source internet metasearch engine.
*   **Caddy:** A server for managed HTTPS/TLS, useful for custom domains in production deployments.
*   **Langfuse:** An open-source platform for LLM engineering observability.

## Interconnections and Workflow

The components are orchestrated via `docker-compose.yml` and managed by a `start_services.py` script. The primary workflow involves:

1.  **Setup:** Users configure environment variables (especially for Supabase, n8n, Neo4j, and Langfuse) and select a GPU profile (Nvidia, AMD, CPU, or none for local Ollama).
2.  **Service Launch:** The `start_services.py` script brings up all the Docker containers.
3.  **n8n Integration:** n8n acts as the central automation hub, connecting to Ollama for LLM inference, Supabase for data and vector storage, and Qdrant for high-performance vector search. Pre-configured local RAG AI Agent workflows are included.
4.  **Open WebUI Interaction:** Open WebUI provides a chat interface to interact with local LLMs and n8n agents, utilizing a Python pipe (`n8n_pipe.py`) to connect to n8n webhooks.
5.  **Local File Access:** A shared folder allows n8n to interact with local files on disk.

## Installation and Usage Overview

The installation involves cloning the repository, configuring environment variables, and running a Python script to start the Docker services. Usage typically begins by accessing n8n and Open WebUI via `localhost` ports, setting up credentials, and activating pre-included workflows. The project also provides instructions for cloud deployment with Caddy for domain management.