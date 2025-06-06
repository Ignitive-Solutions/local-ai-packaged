# Self-hosted AI Package

**Self-hosted AI Package** is an open, docker compose template that
quickly bootstraps a fully featured AI and Low Code development
environment. It integrates with cloud-hosted Supabase for database, vector store, and authentication, and cloud LLM APIs (e.g., OpenAI, Anthropic, or a cloud-hosted Ollama instance) via Open WebUI and n8n.

This is Cole's version with a couple of improvements and the addition of Supabase, Open WebUI, Flowise, Neo4j, Langfuse, SearXNG, and Caddy!
Also, the local RAG AI Agent workflows from the video will be automatically in your 
n8n instance if you use this setup instead of the base one provided by n8n!

## Important Links

- [Local AI community](https://thinktank.ottomator.ai/c/local-ai/18) forum over in the oTTomator Think Tank

- [GitHub Kanban board](https://github.com/users/coleam00/projects/2/views/1) for feature implementation and bug squashing.

- [Original Local AI Starter Kit](https://github.com/n8n-io/self-hosted-ai-starter-kit) by the n8n team

- Download my N8N + OpenWebUI integration [directly on the Open WebUI site.](https://openwebui.com/f/coleam/n8n_pipe/) (more instructions below)

![n8n.io - Screenshot](https://raw.githubusercontent.com/n8n-io/self-hosted-ai-starter-kit/main/assets/n8n-demo.gif)

Curated by <https://github.com/n8n-io> and <https://github.com/coleam00>, it combines the self-hosted n8n
platform with a curated list of compatible AI products and components to
quickly get started with building self-hosted AI workflows.

### What’s included

✅ [**Self-hosted n8n**](https://n8n.io/) - Low-code platform with over 400
integrations and advanced AI components

✅ [**Supabase (Cloud)**](https://supabase.com/) - Leverages the cloud-hosted version of Supabase for its robust database, vector store, and authentication capabilities.

✅ [**Cloud LLM Integration (e.g., OpenAI, Anthropic, Cloud Ollama)**](https://ollama.com/) - Connects to your preferred cloud-based Large Language Model provider for powerful AI inference.

✅ [**Open WebUI**](https://openwebui.com/) - ChatGPT-like interface to
privately interact with your local models and N8N agents

✅ [**Flowise**](https://flowiseai.com/) - No/low code AI agent
builder that pairs very well with n8n

✅ [**Qdrant**](https://qdrant.tech/) - Open source, high performance vector
store with an comprehensive API. Even though you can use Supabase for RAG, this was
kept unlike Postgres since it's faster than Supabase so sometimes is the better option.

✅ [**Neo4j**](https://neo4j.com/) - Knowledge graph engine that powers tools like GraphRAG, LightRAG, and Graphiti 

✅ [**SearXNG**](https://searxng.org/) - Open source, free internet metasearch engine which aggregates 
results from up to 229 search services. Users are neither tracked nor profiled, hence the fit with the local AI package.

✅ [**Caddy**](https://caddyserver.com/) - Managed HTTPS/TLS for custom domains

✅ [**Langfuse**](https://langfuse.com/) - Open source LLM engineering platform for agent observability

## Prerequisites

Before you begin, make sure you have the following software installed:

- [Python](https://www.python.org/downloads/) - Required to run the setup script
- [Git/GitHub Desktop](https://desktop.github.com/) - For easy repository management
- [Docker/Docker Desktop](https://www.docker.com/products/docker-desktop/) - Required to run all services

## Installation

Clone the repository and navigate to the project directory:
```bash
git clone https://github.com/coleam00/local-ai-packaged.git
cd local-ai-packaged
```

Before running the services, you need to set up your environment variables.

1. Make a copy of `.env.example` and rename it to `.env` in the root directory of the project.
2. Set the following required environment variables:
   ```bash
   ############
   # N8N Configuration
   ############
   N8N_ENCRYPTION_KEY=
   N8N_USER_MANAGEMENT_JWT_SECRET=

   ############
   # Supabase Secrets (Cloud Hosted)
   ############
   CLOUD_SUPABASE_URL=your-supabase-project-url # e.g., xyz.supabase.co
   CLOUD_SUPABASE_ANON_KEY=your-supabase-anon-key
   CLOUD_SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-role-key

   ############
   # Neo4j Secrets
   ############   
   NEO4J_AUTH=

   ############
   # Langfuse credentials
   ############

   CLICKHOUSE_PASSWORD=
   MINIO_ROOT_PASSWORD=
   LANGFUSE_SALT=
   NEXTAUTH_SECRET=
   ENCRYPTION_KEY=  
   ```

> [!IMPORTANT]
> Make sure to generate secure random values for all secrets. Never use the example values in production.

3. Set the following environment variables if deploying to production, otherwise leave commented:
   ```bash
   ############
   # Caddy Config
   ############

   N8N_HOSTNAME=n8n.yourdomain.com
   WEBUI_HOSTNAME=:openwebui.yourdomain.com
   FLOWISE_HOSTNAME=:flowise.yourdomain.com
   SUPABASE_HOSTNAME=:supabase.yourdomain.com
   OLLAMA_HOSTNAME=:ollama.yourdomain.com
   SEARXNG_HOSTNAME=searxng.yourdomain.com
   NEO4J_HOSTNAME=neo4j.yourdomain.com
   LETSENCRYPT_EMAIL=your-email-address
   ```   

---

The project includes a `start_services.py` script that handles starting the local AI services. The script accepts a `--profile` flag, which is now primarily for Qdrant or other services that might have GPU-specific configurations if you add them. Since Ollama is cloud-hosted, the GPU profiles for Ollama are no longer directly applicable for local LLM execution.

```bash
python start_services.py # Default profile (e.g., cpu)
# or
python start_services.py --profile <your-chosen-profile-if-applicable-for-other-services>
```

> [!NOTE]
> Ensure your n8n and Open WebUI instances are configured to connect to your cloud LLM API endpoint and use the appropriate API keys. Refer to the `.env.example` for placeholder environment variables for your cloud LLM provider (e.g., `OPENAI_API_KEY`).

## Deploying to the Cloud

### Prerequisites for the below steps

- Linux machine (preferably Unbuntu) with Nano, Git, and Docker installed

### Extra steps

Before running the above commands to pull the repo and install everything:

1. Run the commands as root to open up the necessary ports:
   - ufw enable
   - ufw allow 8000 && ufw allow 3000 && ufw allow 5678 && ufw allow 3002 && ufw allow 80 && ufw allow 443
   - ufw allow 3001 (if you want to expose Flowise, you will have to set up the [environment variables](https://docs.flowiseai.com/configuration/environment-variables) to enable authentication)
   - ufw allow 8080 (if you want to expose SearXNG)
   # - ufw allow 11434 (Ollama is now cloud-hosted)
   - ufw reload

2. Set up A records for your DNS provider to point your subdomains you'll set up in the .env file for Caddy
to the IP address of your cloud instance.

   For example, A record to point n8n to [cloud instance IP] for n8n.yourdomain.com

## ⚡️ Quick start and usage

The main component of the self-hosted AI starter kit is a docker compose file
pre-configured with network and disk so there isn’t much else you need to
install. After completing the installation steps above, follow the steps below
to get started.

1. Open <http://localhost:5678/> in your browser to set up n8n. You’ll only
   have to do this once. You are NOT creating an account with n8n in the setup here,
   it is only a local account for your instance!
2. Open the included workflow:
   <http://localhost:5678/workflow/vTN9y2dLXqTiDfPT>
3. Create credentials for every service:
   
   Cloud LLM API: Configure credentials within n8n for your chosen provider (e.g., OpenAI, Anthropic). The `OLLAMA_HOST` environment variable is no longer used by n8n in this setup.

   Postgres (Cloud Supabase): Use your Cloud Supabase connection details.
   - Host: `<your-supabase-project-host>` (from your Supabase dashboard)
   - Database: `postgres` (or your specific database name)
   - User: `postgres` (or your Supabase database user)
   - Password: The password you set for your Supabase database.
   Ensure n8n's environment variables (`DB_POSTGRESDB_HOST`, `DB_POSTGRESDB_USER`, `DB_POSTGRESDB_PASSWORD`, `DB_POSTGRESDB_DATABASE`) in `docker-compose.yml` are correctly pointing to your `CLOUD_SUPABASE_URL` and related credentials from your `.env` file.

   Qdrant URL: http://qdrant:6333 (API key can be whatever since this is running locally)

   Google Drive: Follow [this guide from n8n](https://docs.n8n.io/integrations/builtin/credentials/google/).
   Don't use localhost for the redirect URI, just use another domain you have, it will still work!
   Alternatively, you can set up [local file triggers](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.localfiletrigger/).
4. Select **Test workflow** to start running the workflow.
5. Ensure your n8n workflows are configured to use the appropriate LLM nodes for your cloud provider (e.g., OpenAI node if using OpenAI).
6. Make sure to toggle the workflow as active and copy the "Production" webhook URL!
7. Open <http://localhost:3000/> in your browser to set up Open WebUI.
You’ll only have to do this once. You are NOT creating an account with Open WebUI in the 
setup here, it is only a local account for your instance!
8. Go to Workspace -> Functions -> Add Function -> Give name + description then paste in
the code from `n8n_pipe.py`

   The function is also [published here on Open WebUI's site](https://openwebui.com/f/coleam/n8n_pipe/).

9. Click on the gear icon and set the n8n_url to the production URL for the webhook
you copied in a previous step.
10. Toggle the function on and now it will be available in your model dropdown in the top left! 

To open n8n at any time, visit <http://localhost:5678/> in your browser.
To open Open WebUI at any time, visit <http://localhost:3000/>.

With your n8n instance, you’ll have access to over 400 integrations and a
suite of basic and advanced AI nodes such as
[AI Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/),
[Text classifier](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.text-classifier/),
and [Information Extractor](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.information-extractor/)
nodes. Configure the relevant LLM nodes (e.g., OpenAI, Anthropic) to use your cloud API credentials. Qdrant can still be used as a local vector store.

> [!NOTE]
> This starter kit is designed to help you get started with self-hosted AI
> workflows. While it’s not fully optimized for production environments, it
> combines robust components that work well together for proof-of-concept
> projects. You can customize it to meet your specific needs

## Upgrading

To update all containers to their latest versions (n8n, Open WebUI, etc.), run these commands:

```bash
# Stop all services
docker compose -p localai -f docker-compose.yml --profile <your-profile> down

# Pull latest versions of all containers
docker compose -p localai -f docker-compose.yml --profile <your-profile> pull

# Start services again with your desired profile
python start_services.py --profile <your-profile>
```

Replace `<your-profile>` with one of: `cpu`, `gpu-nvidia`, `gpu-amd`, or `none`.

Note: The `start_services.py` script itself does not update containers - it only restarts them or pulls them if you are downloading these containers for the first time. To get the latest versions of the remaining self-hosted services, you must explicitly run the commands above.

## Troubleshooting

Here are solutions to common issues you might encounter:

### Cloud Supabase Connection Issues

- Ensure your `CLOUD_SUPABASE_URL`, `CLOUD_SUPABASE_ANON_KEY`, and `CLOUD_SUPABASE_SERVICE_ROLE_KEY` in your `.env` file are correct.
- Verify that n8n and Langfuse services in `docker-compose.yml` are correctly configured to use these environment variables for their database connections.
- Check your Supabase project's network policies to ensure connections from your Docker environment are allowed.

### Cloud LLM API Issues

- Verify your API keys and base URLs for your chosen LLM provider are correctly set up in n8n credentials and/or Open WebUI settings.
- Ensure your n8n workflows are using the correct nodes for your cloud LLM provider.
- Check for any rate limits or usage restrictions on your LLM API plan.

### GPU Support Issues (for remaining local services like Qdrant if applicable)

- If you are using Qdrant or other local services that might benefit from GPU, ensure your Docker and system are configured correctly for GPU passthrough.

## 👓 Recommended reading

n8n is full of useful content for getting started quickly with its AI concepts
and nodes. If you run into an issue, go to [support](#support).

- [AI agents for developers: from theory to practice with n8n](https://blog.n8n.io/ai-agents/)
- [Tutorial: Build an AI workflow in n8n](https://docs.n8n.io/advanced-ai/intro-tutorial/)
- [Langchain Concepts in n8n](https://docs.n8n.io/advanced-ai/langchain/langchain-n8n/)
- [Demonstration of key differences between agents and chains](https://docs.n8n.io/advanced-ai/examples/agent-chain-comparison/)
- [What are vector databases?](https://docs.n8n.io/advanced-ai/examples/understand-vector-databases/)

## 🎥 Video walkthrough

- [Cole's Guide to the Local AI Starter Kit](https://youtu.be/pOsO40HSbOo)

## 🛍️ More AI templates

For more AI workflow ideas, visit the [**official n8n AI template
gallery**](https://n8n.io/workflows/?categories=AI). From each workflow,
select the **Use workflow** button to automatically import the workflow into
your local n8n instance.

### Learn AI key concepts

- [AI Agent Chat](https://n8n.io/workflows/1954-ai-agent-chat/)
- [AI chat with any data source (using the n8n workflow too)](https://n8n.io/workflows/2026-ai-chat-with-any-data-source-using-the-n8n-workflow-tool/)
- [Chat with OpenAI Assistant (by adding a memory)](https://n8n.io/workflows/2098-chat-with-openai-assistant-by-adding-a-memory/)
- [Use an open-source LLM (via HuggingFace)](https://n8n.io/workflows/1980-use-an-open-source-llm-via-huggingface/)
- [Chat with PDF docs using AI (quoting sources)](https://n8n.io/workflows/2165-chat-with-pdf-docs-using-ai-quoting-sources/)
- [AI agent that can scrape webpages](https://n8n.io/workflows/2006-ai-agent-that-can-scrape-webpages/)

### Local AI templates

- [Tax Code Assistant](https://n8n.io/workflows/2341-build-a-tax-code-assistant-with-qdrant-mistralai-and-openai/)
- [Breakdown Documents into Study Notes with MistralAI and Qdrant](https://n8n.io/workflows/2339-breakdown-documents-into-study-notes-using-templating-mistralai-and-qdrant/)
- [Financial Documents Assistant using Qdrant and](https://n8n.io/workflows/2335-build-a-financial-documents-assistant-using-qdrant-and-mistralai/) [ Mistral.ai](http://mistral.ai/)
- [Recipe Recommendations with Qdrant and Mistral](https://n8n.io/workflows/2333-recipe-recommendations-with-qdrant-and-mistral/)

## Tips & tricks

### Accessing local files

The self-hosted AI starter kit will create a shared folder (by default,
located in the same directory) which is mounted to the n8n container and
allows n8n to access files on disk. This folder within the n8n container is
located at `/data/shared` -- this is the path you’ll need to use in nodes that
interact with the local filesystem.

**Nodes that interact with the local filesystem**

- [Read/Write Files from Disk](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.filesreadwrite/)
- [Local File Trigger](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.localfiletrigger/)
- [Execute Command](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.executecommand/)

## 📜 License

This project (originally created by the n8n team, link at the top of the README) is licensed under the Apache License 2.0 - see the
[LICENSE](LICENSE) file for details.
