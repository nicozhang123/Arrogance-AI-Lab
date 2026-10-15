<a id="top"></a>

<div align="center">
  <img src="https://github.com/user-attachments/assets/58111e17-9f05-449e-baa0-503b136a3688" alt="arrogance.ai — AI Collaboration Workspace" width="100%" />

  <br /><br />

  <a href="./README.md"><img alt="Switch to Simplified Chinese" src="https://img.shields.io/badge/简体中文-切换语言-252D4A?style=for-the-badge" /></a>
  <a href="./README_EN.md"><img alt="English — current language" src="https://img.shields.io/badge/English-Current_Language-3279F6?style=for-the-badge" /></a>

  <br /><br />

  <img alt="Generation 1" src="https://img.shields.io/badge/Generation-01-6D61D8?style=flat-square" />
  <img alt="Development Preview" src="https://img.shields.io/badge/Status-Development_Preview-E5A345?style=flat-square" />
  <img alt="React 18" src="https://img.shields.io/badge/React-18-202A44?style=flat-square&amp;logo=react&amp;logoColor=61DAFB" />
  <img alt="FastAPI" src="https://img.shields.io/badge/FastAPI-Python-202A44?style=flat-square&amp;logo=fastapi&amp;logoColor=00D4AA" />
  <img alt="Node.js 20" src="https://img.shields.io/badge/Node.js-20-202A44?style=flat-square&amp;logo=nodedotjs&amp;logoColor=8CC84B" />
  <img alt="License not specified" src="https://img.shields.io/badge/License-Not_specified-39435F?style=flat-square" />

  <h1>One workspace. From ideas to deliverables.</h1>

  <p><strong>AI Chat · Knowledge Agent · Super Tasks · Creative Tools · Team Collaboration</strong></p>

  <p>Bring conversations, knowledge, creative work, and team decisions into one connected AI workspace.<br />Plan the work, review the steps, and keep results alongside the context that shaped them.</p>

  <br />

  <a href="#start"><img alt="Get Started" src="https://img.shields.io/badge/GET_STARTED-LOCAL_SETUP-4064D8?style=for-the-badge" /></a>
  <a href="#showcase"><img alt="Product Showcase" src="https://img.shields.io/badge/PRODUCT-SHOWCASE-1F8B9F?style=for-the-badge" /></a>
  <a href="#architecture"><img alt="System Architecture" src="https://img.shields.io/badge/SYSTEM-ARCHITECTURE-2B3556?style=for-the-badge" /></a>
</div>

<br />

> [!IMPORTANT]
> **Generation 1 · Development Preview** — This README documents the code snapshot dated **October 8, 2026**. Screenshots show locally validated interfaces using demonstration data. They do not establish production readiness, production certification, or end-to-end validation of every external AI provider.

## 📖 Explore

[Quick Start](#start) · [Highlights](#highlights) · [Core Modules](#features) · [Six Tools](#tools) · [Showcase](#showcase) · [Architecture](#architecture)

[Configuration](#configuration) · [Usage](#usage) · [Testing & Limitations](#limitations) · [Developer Docs](#documentation) · [Roadmap](#roadmap) · [License & Security](#security)

---

<a id="start"></a>
## 🚀 Quick Start

Run the commands below in **Windows PowerShell**, starting from the project root. **Docker, Neo4j, and Redis are not required** for everyday local development.

### 1. Install dependencies

| Requirement | Details |
| :-- | :-- |
| **Python 3.11 or 3.12** | The current containers use Python 3.11. Python 3.12 skips the conditional DeepFilterNet / torchaudio dependencies. |
| **Node.js 20 + npm** | Matches the frontend build image. |
| **AI provider access** | Required for cloud-powered features. Local spreadsheet statistics and image cropping do not require a model API key. |
| **Local model cache** | Pre-cache the models needed for local knowledge retrieval or speech transcription. |

```powershell
# Create a Python environment and install backend dependencies
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r backend/requirements.txt

# Create .env only if it does not exist; preserve existing credentials
if (-not (Test-Path -LiteralPath .env)) {
    Copy-Item -LiteralPath .env.example -Destination .env
}

# Install frontend dependencies
Set-Location frontend
npm.cmd ci
Set-Location ..
```

Edit the root `.env` file and replace the placeholders for the services you intend to use. Leave unused integrations unconfigured. **Copying the template does not establish provider connectivity or model authorization.**

### 2. Start the workspace

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1 -NoBrowser
```

| Service | Local URL |
| :-- | :-- |
| React development UI | http://127.0.0.1:5173 |
| FastAPI backend | http://127.0.0.1:8001 |
| Interactive API documentation | http://127.0.0.1:8001/docs |
| Health check | http://127.0.0.1:8001/api/health |

The launcher starts services in the background and writes logs to `.run-logs/`.

> [!TIP]
> The launcher skips ports **8001** and **5173** when they are already occupied. After changing backend code or `.env`, restart the existing processes so you are not testing an older instance.

<details>
<summary><strong>Alternative: run each service in a separate terminal</strong></summary>

Use `Ctrl+C` in each terminal to stop its service.

```powershell
# Terminal A: project root
.\.venv\Scripts\python.exe -m uvicorn backend.app:app --reload --port 8001
```

```powershell
# Terminal B: frontend directory
npm.cmd run dev
```

</details>

### 3. Prepare knowledge models, when needed

Embedding and reranking models load **from the local cache only**. The startup script enables Hugging Face offline mode. Missing models may cause retrieval over existing content to fall back to keyword search; that fallback does **not** mean vector retrieval is working.

With network access to Hugging Face, cache the default models before starting the app:

```powershell
$env:HF_HUB_OFFLINE = "0"
$env:TRANSFORMERS_OFFLINE = "0"
.\.venv\Scripts\python.exe -c "from sentence_transformers import SentenceTransformer, CrossEncoder; SentenceTransformer('BAAI/bge-small-zh-v1.5'); CrossEncoder('BAAI/bge-reranker-base')"
```

Use the same account and `HF_HOME` for model preparation and backend startup. Substitute your configured model names where appropriate; pre-downloaded local model directories also work. Models should be prepared once, not downloaded for every question.

### 4. Build the frontend for deployment

```powershell
Set-Location frontend
npm.cmd run build
Set-Location ..
```

The build is written to `frontend/dist/`. Restart FastAPI to serve it on **port 8001**. Frontend routes use the backend's routing fallback; unknown `/api/*` endpoints still return HTTP 404.

A frontend build is not a complete production deployment. Configure HTTPS, secret management, persistent storage, access control, and backups as described in [Deployment](DEPLOYMENT.md). Docker was not exercised during the original README verification.

---

<a id="highlights"></a>
## ✨ One workspace. Connected ways to work.

<p align="center">
  <a href="https://github.com/user-attachments/assets/3a1f2727-56b2-4162-bd49-11fc549cfb73"><img src="https://github.com/user-attachments/assets/3a1f2727-56b2-4162-bd49-11fc549cfb73" alt="arrogance.ai workspace overview" width="100%" /></a>
</p>

<table>
  <tr>
    <td width="50%" valign="top">
      <h3>🧠 Ask your knowledge</h3>
      <p>Turn PDF, DOCX, TXT, and Markdown files into a searchable knowledge base. Combine vector and keyword retrieval, semantic reranking, evidence citations, and optional knowledge graph enrichment.</p>
      <a href="https://github.com/user-attachments/assets/66cf943a-fd3e-4fe5-8fe3-cc86a08e0618"><img src="https://github.com/user-attachments/assets/66cf943a-fd3e-4fe5-8fe3-cc86a08e0618" alt="Knowledge Agent interface" width="100%" /></a>
    </td>
    <td width="50%" valign="top">
      <h3>⚡ Turn goals into deliverables</h3>
      <p>Break work into steps, set budgets, request optional team approval, and pause or retry execution. Keep saved results and previewable or downloadable HTML / JSON artifacts together.</p>
      <a href="https://github.com/user-attachments/assets/e146bbdf-ba67-4da7-9e06-a9b52b7d5ab8"><img src="https://github.com/user-attachments/assets/e146bbdf-ba67-4da7-9e06-a9b52b7d5ab8" alt="Super Tasks planning and execution interface" width="100%" /></a>
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <h3>🎨 Create, analyze, and automate</h3>
      <p>Explore six dedicated tools for video, images, data analysis, annotation, voice, and workflows. Use local processing where available and configure external providers for model-powered features.</p>
    </td>
    <td width="50%" valign="top">
      <h3>🤝 Collaborate with traceable memory</h3>
      <p>Connect group chat, channels, project boards, Gantt charts, and agent handoffs. Preserve team decisions through explicitly approved, versioned memory with membership-based access.</p>
    </td>
  </tr>
</table>

### From conversation to execution

<p align="center">
  <a href="https://github.com/user-attachments/assets/72cc5420-e269-4158-9828-1a19f3b76f3c"><img src="https://github.com/user-attachments/assets/72cc5420-e269-4158-9828-1a19f3b76f3c" alt="Team task workflow overview" width="100%" /></a>
</p>

> A typical team-task flow, not an automatic pipeline for every message. **Approvals are optional**, and ordinary chat messages do not automatically become persistent project memory.

<a id="features"></a>
## 🧩 Core Modules

| Module | What you can do |
| :-- | :-- |
| **⚡ Super Tasks** | Decompose goals, execute steps, request optional team approval, manage API-call and token budgets, pause and resume work, retry failures, and preview or download saved HTML / JSON artifacts. |
| **💬 General Chat** | Switch models, stream responses, manage sessions, attach files, apply Prompt Skills, and connect local models. |
| **📚 Knowledge Agent** | Import and chunk documents, combine vector and keyword retrieval, rerank results, cite evidence, and optionally enrich answers with a knowledge graph. |
| **🌐 Communication & Collaboration** | Work in group chats and channels with members, roles, AI participation, a voice lobby, project workspaces, task boards, Gantt charts, and agent handoffs. |
| **👤 Friends & Direct Messaging** | Find users, send and manage friend requests, maintain a friend list, exchange private messages, use emoji and stickers, track unread messages, and block users. |
| **🗂️ Team Project Memory** | Propose and approve decisions or rules, correct or revoke entries, inspect revision history and source citations, and isolate access by group membership. |

<a id="tools"></a>
## 🧰 Six Tools, One Workspace

| Tool | Capabilities | Requirements & current scope |
| :-- | :-- | :-- |
| **🎬 AI Video** | Seedance video generation from text or reference images, task status tracking, and a gallery of generated work. | Requires a Volcano Ark account with access to the selected model and a backend API key. |
| **🖼️ AI Image Studio** | Qwen image generation, local editing, precise cropping, color adjustments, design presets, and multi-size ZIP exports. | Local edits do not call a model. Generation requires Qwen configuration. |
| **📊 AI Data Analysis** | Linked full-dataset filters, BI charts, field profiling, quality rules, cleaned data versions, joins, follow-up questions, and CSV / PDF exports. | Statistics and processing are deterministic and local by default. AI interpretation is optional. |
| **🏷️ AI Data Annotation** | Single-image, batch, and ZIP imports; object-detection pre-annotation; confidence filtering; class correction; false-label removal; JSON / YOLO exports. | Uses `hustvl/yolos-tiny` by default. This is not an integrated YOLO model-training framework. |
| **🎙️ AI Voice** | Open conversation, interview practice, emotional companionship, Aria / Omni experiences, voice and volume options, call content, and digital avatars. | Depends on Agora / Qwen Omni and browser microphone permission. |
| **🕸️ AI Workflow** | A drag-and-drop canvas, connected nodes, configuration, saved workflows, a dashboard, execution history, and Super Task event triggers. | Reuses configured models, knowledge retrieval, and built-in text tools. It is not a complete Dify replacement. |

### Inside the toolkit

<table>
  <tr>
    <td width="50%" valign="top">
      <h3>🎬 AI Video</h3>
      <a href="https://github.com/user-attachments/assets/23f5e9c0-5252-42eb-b41d-604ee2a43349"><img src="https://github.com/user-attachments/assets/23f5e9c0-5252-42eb-b41d-604ee2a43349" alt="AI Video workspace" width="100%" /></a>
    </td>
    <td width="50%" valign="top">
      <h3>🖼️ AI Image Studio</h3>
      <a href="https://github.com/user-attachments/assets/405642e1-280c-4a4f-b9ee-d2cd9d2c8c98"><img src="https://github.com/user-attachments/assets/405642e1-280c-4a4f-b9ee-d2cd9d2c8c98" alt="AI Image Studio workspace" width="100%" /></a>
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <h3>📊 AI Data Analysis</h3>
      <a href="https://github.com/user-attachments/assets/d8895b50-f0a9-4b39-8ba4-e2d90933d54b"><img src="https://github.com/user-attachments/assets/d8895b50-f0a9-4b39-8ba4-e2d90933d54b" alt="AI Data Analysis workspace" width="100%" /></a>
    </td>
    <td width="50%" valign="top">
      <h3>🏷️ AI Data Annotation</h3>
      <a href="https://github.com/user-attachments/assets/9480a85c-bc46-4981-b8ba-a0c11f7ae65f"><img src="https://github.com/user-attachments/assets/9480a85c-bc46-4981-b8ba-a0c11f7ae65f" alt="AI Data Annotation workspace" width="100%" /></a>
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <h3>🎙️ AI Voice</h3>
      <a href="https://github.com/user-attachments/assets/5927ccb3-2598-4040-882e-d5f417d3505e"><img src="https://github.com/user-attachments/assets/5927ccb3-2598-4040-882e-d5f417d3505e" alt="AI Voice workspace" width="100%" /></a>
    </td>
    <td width="50%" valign="top">
      <h3>🕸️ AI Workflow</h3>
      <a href="https://github.com/user-attachments/assets/4b3f10ae-6131-40a7-b5d3-b17e8dcfef60"><img src="https://github.com/user-attachments/assets/4b3f10ae-6131-40a7-b5d3-b17e8dcfef60" alt="AI Workflow workspace" width="100%" /></a>
    </td>
  </tr>
</table>

<details>
<summary><strong>Interface & internationalization</strong></summary>

The grouped sidebar supports collapsible sections, icon-label tooltips, independently scrolling recent conversations, and account access. Core pages offer **Chinese / English interfaces**, **blue-and-white / black-and-white themes**, and responsive layouts.

User input, model-generated content, and raw third-party errors are not automatically translated.

</details>

---

<a id="showcase"></a>
## 🖥️ Product Showcase

> These are screenshots from local UI verification with demonstration data, not design mockups. The corresponding repository image directory is `docs/images/gen1/`.

### 01 / Super Tasks — More than a chat response

Keep execution progress, saved HTML reports, JSON data, and downloads in one place. Team deliverables can return to the project workspace for review and follow-up.

<p align="center">
  <a href="https://github.com/user-attachments/assets/96c8e36b-77d6-4b29-ba9c-c85da43416f3"><img src="https://github.com/user-attachments/assets/96c8e36b-77d6-4b29-ba9c-c85da43416f3" alt="Super Tasks saved results and report preview" width="100%" /></a>
</p>

<p align="center"><sub>HTML report generated from demonstration data. Technical validation does not establish that every business conclusion has been manually reviewed.</sub></p>

<table>
  <tr>
    <td width="50%" valign="top">
      <h3>02 / Data Workspace</h3>
      <a href="https://github.com/user-attachments/assets/03b70333-ab12-4e5d-982f-46727cb8ad4d"><img src="https://github.com/user-attachments/assets/03b70333-ab12-4e5d-982f-46727cb8ad4d" alt="BI data workspace with synchronized filters" width="100%" /></a>
      <p><strong>One filter. A consistent data view.</strong> Metrics, charts, field profiles, paginated tables, and CSV exports update together. Quality checks, cleaning, joins, and follow-up questions use consistent data versions. Demo example: <strong>120 → 21 rows</strong>.</p>
    </td>
    <td width="50%" valign="top">
      <h3>03 / Project Memory</h3>
      <a href="https://github.com/user-attachments/assets/6cff241f-3295-44a6-b3a0-b02ec8aa8eb7"><img src="https://github.com/user-attachments/assets/6cff241f-3295-44a6-b3a0-b02ec8aa8eb7" alt="Project memory review and approval" width="100%" /></a>
      <p><strong>Confirm first. Reference later.</strong> AI can use team decisions and rules only after a project manager or administrator approves them. Corrections, revocations, citations, and revision history remain traceable.</p>
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <h3>04 / Workflow Automation</h3>
      <a href="https://github.com/user-attachments/assets/7bfd7078-7349-45ff-952d-4648cb1490dd"><img src="https://github.com/user-attachments/assets/7bfd7078-7349-45ff-952d-4648cb1490dd" alt="Workflow canvas and execution history" width="100%" /></a>
      <p><strong>Let task events start the next workflow.</strong> Trigger saved workflows when a Super Task is approved, delivered, or fails. Inspect node progress, inputs, outputs, elapsed time, and errors, with controls to stop or retry.</p>
    </td>
    <td width="50%" valign="top">
      <h3>05 / AI Voice</h3>
      <a href="https://github.com/user-attachments/assets/3318f7dc-d55e-4bc7-a0a2-c57cdc2797ee"><img src="https://github.com/user-attachments/assets/3318f7dc-d55e-4bc7-a0a2-c57cdc2797ee" alt="AI Voice scenario and mode selection" width="100%" /></a>
      <p><strong>Choose a scenario. Start a conversation.</strong> Explore open chat, interview practice, or emotional companionship, then configure Aria / Omni and voice options.</p>
    </td>
  </tr>
</table>

<details>
<summary><strong>More real-time voice screens</strong></summary>

<p align="center">
  <a href="https://github.com/user-attachments/assets/4b0857d2-98c4-4c7c-85a3-56b15b586f68"><img src="https://github.com/user-attachments/assets/4b0857d2-98c4-4c7c-85a3-56b15b586f68" alt="AI Voice call interface" width="48%" /></a>
  <a href="https://github.com/user-attachments/assets/f0dd407a-3f8a-4b3c-bea7-0634b975aeaa"><img src="https://github.com/user-attachments/assets/f0dd407a-3f8a-4b3c-bea7-0634b975aeaa" alt="AI Voice digital avatar interface" width="48%" /></a>
</p>

Local UI call states are not provider performance guarantees. Avatar mouth movement follows audio intensity; it is not accurate phoneme-level lip synchronization.

</details>

---

<a id="architecture"></a>
## 🏗️ System Architecture

<p align="center">
  <a href="https://github.com/user-attachments/assets/870ace0d-95ba-481a-a298-f82136b96a83"><img src="https://github.com/user-attachments/assets/870ace0d-95ba-481a-a298-f82136b96a83" alt="arrogance.ai system architecture diagram" width="100%" /></a>
</p>

> The diagram describes module responsibilities, not a default configuration with every external service enabled. Optional integrations require separate setup. The original illustration contains Chinese labels; the table below provides an English overview.

| Layer | Technologies & responsibilities |
| :-- | :-- |
| **User interface** | React 18, Vite 6, Tailwind CSS, HeroUI, Framer Motion, and Lucide; ECharts, PixiJS / Live2D avatars, and Agora RTC. |
| **API & real-time communication** | FastAPI, Uvicorn, and HTTPX; streaming HTTP, group-chat WebSockets, and provider real-time voice connections. |
| **Models & orchestration** | LangChain Core / OpenAI adapters, LangGraph, and task / workflow runtimes; Qwen, DeepSeek / OpenAI-compatible endpoints, and Ollama. |
| **Knowledge retrieval** | Custom chunking, Sentence Transformers, ChromaDB, BM25 / RRF, reranking, and optional Neo4j enrichment. |
| **Local computation** | Deterministic data loading, statistics, filtering, quality checks, cleaning, and joins. Metrics are computed from data, not invented by a model. |
| **Persistence** | SQLite for users, conversations, projects, tasks, artifacts, analysis versions, and memory; Chroma for vectors; optional Redis. |

**Request flow**

```text
Browser → FastAPI → Business Module → Local Computation / External Model
       → Saved State & Artifacts → UI / Team Workspace
```

### Repository structure

```text
Langchain_chatbot/
├── backend/
│   ├── app.py                   # ASGI application entry point
│   ├── api/                     # Application assembly and business routes
│   ├── agents/                  # Knowledge, finance, voice, and vision capabilities
│   ├── core/                    # Authentication and shared runtime
│   ├── infrastructure/          # Databases, repositories, caches, persistence
│   ├── schemas/                 # API schemas
│   └── requirements.txt
├── frontend/
│   ├── src/components/          # Pages and components
│   ├── src/features/            # Feature modules
│   ├── src/hooks/               # State and interaction hooks
│   ├── src/services/            # API and streaming protocols
│   └── src/styles/              # Themes and responsive styles
├── tests/                       # Unit, evaluation, and browser tests
├── docs/                        # Design documents, development notes, images
├── scripts/                     # Operations and helper scripts
├── k8s/                         # Optional deployment resources
├── .env.example                 # Configuration template
├── run_app.ps1                  # Windows startup script
└── DEPLOYMENT.md                # Deployment instructions
```

Default data locations include `chat_history.db`, `chroma_data/`, and `uploads/`. Back them up and check module-specific paths before changing `APP_DATA_DIR`. **An ordinary restart should not delete application data.**

<a id="configuration"></a>
## ⚙️ Configuration

Start with the root [`.env.example`](.env.example). Configure only the integrations you need; the table below lists variable names, not credentials.

| Feature | Main environment variables | Notes |
| :-- | :-- | :-- |
| **OpenAI-compatible chat** | `OPENAI_API_KEY`, `OPENAI_BASE_URL`, `OPENAI_MODEL` | Supports compatible services such as DeepSeek. The endpoint, key, and model must match. |
| **Qwen / multimodal** | `QWEN_API_KEY`, `QWEN_BASE_URL`, `QWEN_MODEL`, `QWEN3_7_MODEL` | Availability depends on the models authorized for your account. |
| **Local models** | `OLLAMA_BASE_URL`, `OLLAMA_MODEL` | Run Ollama separately and provision the selected model. |
| **Knowledge retrieval** | `EMBEDDING_MODEL`, `RERANKER_MODEL`, `RAG_*` | Configure local models, retrieval strategies, and generation limits. |
| **Optional knowledge graph** | `NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD`, `NEO4J_DATABASE` | Uses timeout and fallback policies; not required by every tool. |
| **Optional cache** | `REDIS_URL` | Real-time state and caching, with local fallback where implemented. |
| **Video generation** | `SEEDANCE_API_KEY`, `SEEDANCE_BASE_URL`, `SEEDANCE_MODEL` | Keep the key on the backend. The endpoint and model must be authorized. |
| **Image generation** | `QWEN_API_KEY`; optional `QWEN_IMAGE_MODEL`, `QWEN_IMAGE_API_URL` | Uses a separate image endpoint, not the chat model. |
| **Pre-annotation** | `HF_TOKEN`, `HF_OBJECT_DETECTION_MODEL`, `HF_INFERENCE_MODE` | `auto` prioritizes the remote API; without a token, local loading is attempted. |
| **Omni real-time voice** | `QWEN_REALTIME_API_KEY`, `QWEN_REALTIME_URL`, `QWEN_REALTIME_MODEL`, `QWEN_REALTIME_VOICE` | Configures the native real-time voice connection. |
| **Aria / Agora** | `AGORA_APP_ID`, `AGORA_CUSTOMER_ID`, `AGORA_CUSTOMER_SECRET`, RTC token / UID, `AGORA_LLM_*`, `AGORA_TTS_*` | Cloud callbacks require HTTPS. The channel, UID, and token must match. |
| **Local transcription** | `WHISPER_MODEL`, `WHISPER_MODEL_DIR`, `WHISPER_DEVICE` | Audio-to-text transcription, separate from native real-time voice. |
| **Data storage** | `APP_DATA_DIR` | Defaults to the project root. Back up SQLite, Chroma, and uploads. |
| **Performance** | `MODEL_FAST_MODE`, `MODEL_*TIMEOUT*`, `CHAT_RESPONSE_DEADLINE_SECONDS`, `GROUP_AI_DEADLINE_SECONDS` | Connection reuse, fast mode, timeouts, and context-size strategies. |

Some optional settings use code-defined defaults and may not appear in the template.

> [!WARNING]
> Never commit `.env`, API keys, RTC tokens, or private data. Replace example development database passwords before any public deployment.

<a id="usage"></a>
## 🔄 Usage Workflows

### Personal workspace

1. **Open your workspace.** Register or sign in, then choose a feature from the sidebar.
2. **Start with a conversation.** Chat directly or import documents into the Knowledge Agent for evidence-grounded answers. Use Prompt Skills for reusable response styles.
3. **Work with data.** Upload CSV / XLSX files to filter, check quality, clean, and join datasets. Model calls are not required by default.
4. **Explore specialized tools.** Use creation, annotation, and voice features after configuring their required providers.
5. **Move to a Super Task.** For multi-step work, review the plan, follow execution, and inspect saved results and artifacts.

### Team delivery loop

| Stage | Action |
| :-- | :-- |
| **1 · Define** | Discuss the objective, constraints, and delivery format in group chat, then associate a Super Task. |
| **2 · Plan** | Review the steps, required inputs, ownership, and budgets. |
| **3 · Approve, when needed** | Select the team group and request project-manager confirmation when authorization is required. Personal tasks do not require group approval. |
| **4 · Execute & review** | Track step status and use the available controls to address missing inputs, exhausted budgets, or failures. |
| **5 · Deliver** | Publish a delivery summary, review the agent's deliverable in the project workspace, and continue tracking work on the task board. |
| **6 · Confirm memory** | Manually propose decisions or rules for project-manager or administrator approval. Ordinary messages do not automatically become persistent policies. |

Execution step counts follow the task's plan, and progress reflects recorded execution. The six-stage login animation is a product demonstration, not a fixed six-step backend sequence.

### Team memory permissions

| Principle | Behavior |
| :-- | :-- |
| **Membership-scoped access** | Only current group members may read group memory. Personal tasks do not automatically receive it. |
| **Explicit approval** | Ordinary members cannot confirm rules or modify another member's active memory. |
| **Versioned references** | AI cites specific memory versions and sources. Revocation prevents new references. |
| **Reviewable corrections** | Corrections require fresh confirmation, and earlier revisions remain available. |
| **Preserved history** | Earlier answers and completed deliverables are not silently rewritten. |
| **Transparent model usage** | Memory retrieval does not itself make an additional model call. Referenced content still counts toward normal input tokens. |

See [Team Project Memory](docs/team-project-memory.md) for details.

<a id="limitations"></a>
## 📋 Testing & Limitations

### Local regression tests

With development dependencies installed, run these deterministic tests for project memory, group chat, task execution, full-data analytics, and response performance. Docker is not required.

```powershell
.\.venv\Scripts\python.exe -m unittest tests.unit.test_project_memory tests.unit.test_group_chat tests.unit.test_super_task_runtime tests.unit.test_analysis_workspace tests.unit.test_response_speed
```

Check frontend protocols, artifact previews, and the production build:

```powershell
Set-Location frontend
node --test src/services/chatStream.test.mjs src/features/super-task/artifactPreview.test.mjs
npm.cmd run build
Set-Location ..
```

**Verification record:** Earlier milestone checks used temporary SQLite databases, mock model providers, real spreadsheet operations, and browser tests at **1440 / 768 / 390 px** widths, as documented in the relevant modules. The original README review covered code, screenshots, and links; it did not rerun every application test or invoke paid models.

### Generation 1 boundaries

- **Super Task execution.** Spreadsheet analysis uses real local tools and generates reports. Video and workflow steps within Super Tasks currently produce briefs or design text—not generated videos or executable workflows. Standalone video and workflow tools have separate entry points.
- **Analytics limits.** BI accepts up to **10 MB per file, 50,000 non-empty rows, 100 columns, and 1,000,000 cells**. Files exceeding these limits are rejected. Descriptive Super Task reports currently read up to **5,000 rows and 100 columns from the first worksheet**, with sampling notices.
- **BI scope.** The data workspace is not unrestricted natural-language SQL, prediction, causal analysis, or a complete Power BI replacement. DuckDB, Great Expectations, and Metabase are design references, not installed components.
- **Batch annotation.** ZIP imports allow up to **100 MB per archive, 100 images, 50 MB of extracted image data, and 10 MB per image**. Pre-annotations require human review; accuracy is not guaranteed.
- **Costs and cancellation.** Estimated or reserved tokens may differ from actual provider billing. Stopping a task does not guarantee cancellation of requests already sent or reimbursement of their cost.
- **Workflow recovery.** Workflows retain versions and execution history and use a recoverable queue. A crash may cause an interrupted node to repeat a model call. Manual retries restart the original workflow version from the beginning; there is no universal checkpoint-resume or exactly-once guarantee.
- **Voice and avatars.** Microphone access generally requires HTTPS or localhost. External providers need separate integration testing, and avatar assets require authorization. Arbitrary real-time browser or desktop control is not a supported claim.
- **External services.** Configuration does not establish reachability, authorization, or integration readiness. Local checks are not a substitute for provider testing, and no unified production response-time SLA is provided.
- **Privacy.** Analytics, task artifacts, and project memory have service-side permission checks. Ordinary media also uses static-resource paths and may not have equivalent privacy guarantees. Audit these paths before public deployment.
- **Localization.** Core interfaces support Chinese and English. User content is not automatically translated; new components and third-party error messages require continued verification.

### Troubleshooting

| Symptom | What to check first |
| :-- | :-- |
| **`Not Found` after a page refresh** | Use the correct frontend port, or build `frontend/dist/` and restart FastAPI for single-service mode on port 8001. |
| **Persistent API-configuration warning** | Check the root `.env`, placeholder keys, variable names, and any old backend processes. |
| **Connection failures or long waits** | Check the provider endpoint, network / proxy, quota, model access, and `.run-logs/backend.err.log`. |
| **Hugging Face errors** | Check the local cache and `HF_HOME`; verify whether retrieval has fallen back to keyword search. |
| **Voice lobby or call failures** | Check microphone permission, HTTPS, RTC token expiry, channel / UID, Agora callbacks, and Qwen Realtime settings. |
| **Older BI reports do not update** | Older reports may contain only summaries or previews. Re-upload the original dataset. |

When reporting an issue, include reproduction steps, the affected module, UI language, browser, timestamp, and sanitized logs. **Do not include credentials, tokens, or private data.**

<a id="documentation"></a>
## 📚 Developer Documentation

| Document | What it covers |
| :-- | :-- |
| [Deployment](DEPLOYMENT.md) | Environment setup, builds, and deployment. |
| [High-level Design](docs/design/概要设计说明书.md) / [Detailed Design PDF](docs/design/arrogance.ai-详细设计说明书.pdf) | Product and system design. Read historical claims in the context of the current code. |
| [Code Structure](docs/architecture/CODE_STRUCTURE.md) / [Task Lifecycle](docs/architecture/TASK_LIFECYCLE_DESIGN.md) | Module organization and task state transitions. |
| [Development Stages](docs/development-stages.md) | Project evolution and implementation milestones. |
| [Agent Tool Delivery](docs/agent-real-delivery.md) | Inputs, budgets, approvals, reporting, and acceptance boundaries. |
| [Super Task Artifacts](docs/super-task-results.md) | HTML / JSON previews and downloads. |
| [Team Project Memory](docs/team-project-memory.md) | Confirmation, versioning, sources, and permissions. |
| [BI Data Workspace](docs/data-analysis-workspace.md) | Linked filtering, data quality, cleaning, joins, and follow-up questions. |
| [Workflow Automation](docs/workflow-automation.md) | Events, queues, execution, and recovery. |
| [Response Performance](docs/response-speed.md) | Streaming, connection pooling, fallbacks, and timeouts. |
| [Voice Lobby Reliability](docs/voice-lobby-reliability.md) | Group voice failure handling. |
| [Real-time Voice Handbook](docs/interview/声网实时语音Agent-详细设计与面试手册.md) / [Live2D](docs/LIVE2D_AVATAR.md) | Voice architecture and digital avatars. |
| [Ollama Local Models](docs/OLLAMA_LOCAL_MODEL.md) | Local model runtime setup. |
| [RAG Evaluation PDF](docs/interview/arrogance.ai-RAG质检评测面试手册.pdf) | Retrieval evaluation and quality checks. |
| [Kubernetes](k8s/README.md) / [K8s Command Reference](docs/kubernetes/K8s常用命令速查.md) | Optional deployment resources. |
| [Image Notes](docs/images/gen1/README.md) | Screenshot sources and demonstration data. |
| [Legacy README](docs/archive/README-knowledge-base.md) | Early documentation. Use this README for current features and startup instructions. |

<a id="roadmap"></a>
## 🧭 Roadmap

The next priorities are completing real tool-based deliverables, validating business use cases, and improving observability before expanding cross-tool orchestration.

Larger-scale data processing, enterprise permissions, and finer-grained voice interaction remain **future directions, not shipped features**.

<a id="security"></a>
## 🔐 License & Security

The documented snapshot does not include a standalone `LICENSE` file in the project root. Do not assume an MIT license or unrestricted open-source usage rights. Obtain explicit permission from the owner before redistribution or commercial use.

Review the separate licenses and permissions for third-party SDKs, avatars, fonts, videos, and other assets. Before a public deployment, also review credential handling, data access, static media paths, and backups.

---

<div align="center">
  <h3>Think together. Build with context.</h3>
  <p><strong>arrogance.ai</strong> · Generation 01</p>
  <p><sub>React · FastAPI · AI Agents · Team Collaboration</sub></p>
  <a href="./README.md"><img alt="Switch to Simplified Chinese" src="https://img.shields.io/badge/简体中文-Switch-252D4A?style=flat-square" /></a>
  <a href="./README_EN.md"><img alt="English — current language" src="https://img.shields.io/badge/English-Current-3279F6?style=flat-square" /></a>
  <p><a href="#top">↑ Back to top</a></p>
</div>
