# Plan for Cloud-hosting Supabase and Ollama

This document outlines the necessary code modifications and architectural changes to transition Supabase and Ollama from self-hosted components to cloud-hosted or paid API solutions within the "Self-hosted AI Package" repository.

## Architectural Changes Overview

The core idea is to remove the Docker containers responsible for self-hosting Supabase and Ollama and instead configure the remaining services (n8n, Open WebUI, Langfuse) to connect to external, cloud-based instances of these services.

```mermaid
graph TD
    A[User Request] --> B{Cloud-hosted Supabase & Ollama};

    subgraph Current Architecture (Self-hosted)
        C[docker-compose.yml] --> D[n8n Service];
        C --> E[Ollama Service];
        C --> F[Supabase Services (Postgres, etc.)];
        D --> F;
        D --> E;
        G[Open WebUI] --> E;
        H[Langfuse] --> F;
    end

    subgraph Proposed Architecture (Hybrid)
        I[docker-compose.yml] --> J[n8n Service];
        I --> K[Other Self-hosted Services: Qdrant, Neo4j, Flowise, SearXNG, Caddy, Langfuse];
        J --> L[Cloud Supabase];
        J --> M[Cloud LLM API (e.g., OpenAI, Cloud Ollama)];
        N[Open WebUI] --> M;
        K --> L;
    end

    B --> Proposed Architecture (Hybrid);
```

## Detailed Plan for Code Modifications

### Goal 1: Remove Self-hosted Supabase Components

1.  **Modify [`docker-compose.yml`](docker-compose.yml)**:
    *   Remove the `include: ./supabase/docker/docker-compose.yml` line.
    *   Remove the `postgres` service definition.
    *   Remove `langfuse_postgres_data` from the `volumes` section.
    *   Update the `langfuse-worker` and `langfuse-web` services to remove `postgres` from their `depends_on` section.

2.  **Modify `.env.example` and `.env`**:
    *   Remove all Supabase-related environment variables: `POSTGRES_PASSWORD`, `JWT_SECRET`, `ANON_KEY`, `SERVICE_ROLE_KEY`, `DASHBOARD_USERNAME`, `DASHBOARD_PASSWORD`, `POOLER_TENANT_ID`.
    *   Add new environment variables for your cloud-hosted Supabase instance, such as:
        ```
        CLOUD_SUPABASE_URL=
        CLOUD_SUPABASE_ANON_KEY=
        CLOUD_SUPABASE_SERVICE_ROLE_KEY=
        ```

3.  **Modify `start_services.py`**:
    *   Review and remove any logic specifically starting or managing Supabase Docker containers.

4.  **Update `n8n` Configuration**:
    *   In `docker-compose.yml`, modify the `n8n` service's environment variables to point to your cloud-hosted Supabase instance:
        ```yaml
        environment:
          - DB_TYPE=postgresdb
          - DB_POSTGRESDB_HOST=${CLOUD_SUPABASE_URL}
          - DB_POSTGRESDB_USER=postgres
          - DB_POSTGRESDB_PASSWORD=${CLOUD_SUPABASE_SERVICE_ROLE_KEY}
          - DB_POSTGRESDB_DATABASE=postgres
        ```
    *   **Important**: The `DB_POSTGRESDB_HOST` might need to be just the hostname part of your Supabase URL, not the full URL. You'll need to consult Supabase's documentation for external connections.

5.  **Update `Langfuse` Configuration**:
    *   In `docker-compose.yml`, modify the `langfuse-worker` service's `DATABASE_URL` to point to your cloud-hosted Supabase Postgres instance:
        ```yaml
        environment:
          - DATABASE_URL=postgresql://postgres:${CLOUD_SUPABASE_SERVICE_ROLE_KEY}@${CLOUD_SUPABASE_URL}/postgres
        ```

### Goal 2: Remove Self-hosted Ollama Components and Integrate Cloud LLM API

1.  **Modify [`docker-compose.yml`](docker-compose.yml)**:
    *   Remove the `x-ollama` and `x-init-ollama` sections.
    *   Remove the `ollama_storage` volume.
    *   Remove the `ollama-cpu`, `ollama-gpu`, `ollama-gpu-amd` services.
    *   Remove the `ollama-pull-llama-cpu`, `ollama-pull-llama-gpu`, `ollama-pull-llama-gpu-amd` services.
    *   Remove `ollama` from the `caddy` service's environment variables.

2.  **Modify `.env.example` and `.env`**:
    *   Remove any Ollama-related environment variables.
    *   Add new environment variables for your chosen cloud LLM API (e.g., OpenAI, Anthropic, or a cloud-hosted Ollama instance). For example, if using OpenAI:
        ```
        OPENAI_API_KEY=
        OPENAI_BASE_URL=https://api.openai.com/v1
        ```

3.  **Modify `start_services.py`**:
    *   Remove any logic specifically starting or managing Ollama Docker containers or pulling Ollama models.

4.  **Update `n8n` Configuration**:
    *   If `n8n` has an `OLLAMA_HOST` environment variable, remove it.
    *   Instead, `n8n` will need to be configured to use the cloud LLM API directly. This typically involves setting up new credentials within n8n for the specific LLM provider.

5.  **Update `Open WebUI` Configuration**:
    *   Open WebUI needs to connect to the cloud LLM API. This usually involves configuring the Open WebUI instance (after it's running) to point to the external API endpoint and provide the necessary API keys.
    *   The `n8n_pipe.py` script might need to be reviewed if it's specifically designed to proxy requests to a local Ollama.

### Goal 3: Update Documentation and Instructions

1.  **Modify [`README.md`](README.md)**:
    *   Update the "What's included" section to reflect that Supabase and Ollama are no longer self-hosted.
    *   Adjust the "Prerequisites" and "Installation" sections to remove steps related to self-hosting Supabase and Ollama.
    *   Update the "Quick start and usage" section to reflect the new connection details for n8n and Open WebUI to cloud services.
    *   Remove or modify the "For Mac users running OLLAMA locally" section.
    *   Update the "Upgrading" and "Troubleshooting" sections to remove any references to self-hosted Supabase and Ollama issues.

2.  **Update `memory-bank/productContext.md`**:
    *   Reflect the change in architecture from self-hosted to hybrid for Supabase and Ollama.

3.  **Update `memory-bank/activeContext.md`**:
    *   Update the "Current Task" and "Current Focus" to reflect the completion of this planning phase.

4.  **Update `memory-bank/progress.md`**:
    *   Add an entry for this planning activity.

5.  **Update `memory-bank/decisionLog.md`**:
    *   Add a decision entry for moving Supabase and Ollama to cloud/paid API solutions.