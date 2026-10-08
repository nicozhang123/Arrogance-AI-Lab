# Arrogance-AI-Lab
# LangChain Graph RAG Chatbot

[部署指南 / Deployment Guide](DEPLOYMENT.md)

[概要设计说明书](docs/design/概要设计说明书.md)

[详细设计说明书（PDF）](docs/design/arrogance.ai-详细设计说明书.pdf)

[RAG 质检评测面试手册（PDF）](docs/interview/arrogance.ai-RAG质检评测面试手册.pdf)

[Kubernetes 本地部署](k8s/README.md)

[Kubernetes 常用命令速查](docs/kubernetes/K8s常用命令速查.md)

[中文](#中文说明) · [English](#english)

---

## 中文说明

一个前后端分离的智能知识库问答系统，结合本地 BGE 向量检索、ChromaDB、Reranker、Neo4j 知识图谱和 OpenAI 兼容大模型。系统支持文档上传、自动图谱生成、多轮问答、来源追踪、用户认证及 Selenium 可视化测试。

### 项目目录

```text
Langchain_chatbot/
├─ backend/            FastAPI、RAG、Neo4j、认证、数据库与语音服务
├─ frontend/           React + Vite 前端
├─ tests/              后端单元测试与 Selenium 端到端测试
├─ scripts/            知识库、图谱和性能测试脚本
├─ knowledge_docs/     演示知识库文档
├─ k8s/                Kubernetes 部署资源
└─ docker-compose.yml  本地容器编排
```

### 核心功能

- React + FastAPI 前后端分离架构
- PDF、DOCX、TXT、Markdown 文档上传与解析
- `BAAI/bge-small-zh-v1.5` 本地文本向量模型
- ChromaDB Top-K 语义召回
- `BAAI/bge-reranker-base` 候选片段重排
- ChromaDB + Neo4j 混合 Graph RAG 检索
- 知识库问答与通用 AI 对话双模式
- DeepSeek 与 Qwen-VL 模型切换
- Qwen-VL 图片上传、预览和图文问答
- 浏览器麦克风录音与本地 Whisper 语音转文字，确认后发送
- Qwen3.5-Omni 原生实时语音：服务端 VAD、流式文字/语音、语音打断，并可自动调用 ChromaDB 与 Neo4j 知识库工具
- 文档上传后自动生成 Neo4j 文档、章节和事实图谱
- DeepSeek、OpenAI 及其他 OpenAI 兼容模型接入
- 大模型不可用时的本地检索模式
- SQLite 用户、认证会话和历史对话持久化
- 图片验证码及六项严格密码强度校验
- 根据最新知识库动态生成推荐问题
- “换一批”推荐问题轮换
- 回答来源横向滚动、展开、评分及图谱来源高亮
- 极光紫、深空黑、纸张白、日落橙四套界面风格
- 单浏览器连续 Selenium 登录注册与聊天测试

### RAG 工作流程

```text
上传文档
   ├── 文本解析与切片
   ├── BGE Embedding → ChromaDB
   └── 文档/章节/事实抽取 → Neo4j

用户问题
   ├── ChromaDB Top-K 语义召回
   ├── BGE Reranker 精排
   ├── Neo4j 关系检索
   └── 检索上下文 → 大模型生成回答

通用聊天
   └── 对话历史 → DeepSeek/OpenAI 兼容模型
```

Neo4j 不可用时，向量知识库仍可正常入库和检索；大模型接口不可用时，可以启用本地检索模式直接查看知识片段。

页面顶部可以切换两种模式：

- **知识库问答**：使用 ChromaDB、Reranker 和 Neo4j，并展示引用来源。
- **通用 AI 对话**：跳过知识库检索，直接调用大模型，不展示知识库来源。

通用聊天模式可以选择 DeepSeek 或 Qwen-VL。选择 Qwen-VL 后支持上传
PNG、JPG 和 WEBP 图片（最大 5 MB），并将图片和问题一起发送给视觉模型。

切换模式时会创建新会话，防止知识库上下文与通用聊天上下文互相污染。

### 快速启动

要求：

- Python 3.10 或更高版本
- Node.js 18 或更高版本
- Chrome 与 ChromeDriver（仅 Selenium 测试需要）
- Neo4j Aura 或本地 Neo4j（图谱功能需要）

首次安装：

```powershell
cd F:\Langchain_chatbot

python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r backend/requirements.txt

cd frontend
npm.cmd install
npm.cmd run build
cd ..

Copy-Item .env.example .env
```

编辑 `.env`，配置大模型和 Neo4j。不要把真实密钥提交到 Git：

```env
OPENAI_API_KEY=your-api-key
OPENAI_BASE_URL=https://api.deepseek.com
OPENAI_MODEL=deepseek-chat

EMBEDDING_MODEL=BAAI/bge-small-zh-v1.5
RERANKER_MODEL=BAAI/bge-reranker-base

QWEN_API_KEY=your-qwen-api-key
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
QWEN_MODEL=qwen3-vl-plus
QWEN3_7_MODEL=qwen3.7-plus
QWEN_TEMPERATURE=0.4

# 火山方舟 Seedance 视频生成
SEEDANCE_API_KEY=your-volcengine-ark-api-key
SEEDANCE_BASE_URL=https://ark.cn-beijing.volces.com/api/v3
SEEDANCE_MODEL=doubao-seedance-1-5-pro-251215

NEO4J_URI=bolt+s://your-instance.databases.neo4j.io
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=your-password
NEO4J_DATABASE=neo4j
```

一条命令启动 FastAPI、React 并打开浏览器：

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1
```

只启动服务，不打开浏览器：

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1 -NoBrowser
```

默认开发地址：

- React：<http://127.0.0.1:5173>
- FastAPI：<http://127.0.0.1:8001>
- OpenAPI：<http://127.0.0.1:8001/docs>

### 分别启动

后端：

```powershell
cd F:\Langchain_chatbot
$env:HF_HUB_OFFLINE="1"
$env:TRANSFORMERS_OFFLINE="1"
.\.venv\Scripts\python.exe -m uvicorn backend.app:app --reload --port 8001
```

前端：

```powershell
cd F:\Langchain_chatbot\frontend
npm.cmd run dev
```

### 自动知识图谱

上传文档后，系统会同时写入两个知识存储：

1. 文本片段经过 BGE 向量化后写入 ChromaDB。
2. 文档标题、章节及事实句自动写入 Neo4j。

自动图谱使用以下结构：

```text
(Document)-[:HAS_SECTION]->(Section)-[:HAS_FACT]->(Fact)
```

这一过程不依赖在线大模型，因此 DeepSeek 余额不足时仍然可以生成图谱。

在 Neo4j Query 中查看所有自动生成的图谱：

```cypher
MATCH path =
  (source:Entity)
  -[relation:KNOWLEDGE_RELATION]->
  (target:Entity)
WHERE source.dataset STARTS WITH "upload-"
RETURN path
```

查看星港智慧园区人工整理图谱：

```cypher
MATCH path =
  (source:Entity {dataset: "starport-smart-park"})
  -[relation:KNOWLEDGE_RELATION]->
  (target:Entity)
RETURN path
```

导入或更新园区图谱：

```powershell
.\.venv\Scripts\python.exe -m scripts.seed_starport_graph
```

### Selenium 可视化测试

统一启动服务，并在一个 Chrome 会话中连续测试日落橙主题、密码强度、图片验证码、注册、退出、重新登录和 8 个知识库问题：

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1 -Test
```

也可以直接运行测试：

```powershell
$env:E2E_STEP_DELAY="0.15"
$env:E2E_KEEP_OPEN="5"
.\.venv\Scripts\python.exe -m tests.e2e.full_journey_visible
```

当前连续测试包含 13 个步骤，并校验每个回答的目标关键词及引用来源。

### 主要接口

| 方法 | 路径 | 说明 |
|---|---|---|
| `GET` | `/api/health` | 服务和模型配置状态 |
| `GET` | `/api/auth/captcha` | 获取图片验证码 |
| `POST` | `/api/auth/register` | 注册 |
| `POST` | `/api/auth/login` | 登录 |
| `POST` | `/api/auth/logout` | 退出 |
| `GET` | `/api/sessions` | 历史对话列表 |
| `GET` | `/api/sessions/{id}/messages` | 对话消息 |
| `POST` | `/api/chat` | Graph RAG 问答 |
| `POST` | `/api/knowledge/documents` | 上传、向量化并自动生成图谱 |
| `POST` | `/api/knowledge/search` | Top-K 检索与重排 |
| `GET` | `/api/knowledge/stats` | 知识库状态 |
| `GET` | `/api/knowledge/recommendations` | 动态推荐问题 |
| `GET` | `/api/video-generation/config` | Seedance 配置与能力 |
| `POST` | `/api/video-generation/tasks` | 创建文生视频或图生视频任务 |
| `GET` | `/api/video-generation/tasks/{id}` | 查询视频生成进度和结果 |
| `DELETE` | `/api/video-generation/tasks/{id}` | 取消或删除视频任务 |
| `GET` | `/api/graph/status` | Neo4j 状态 |
| `POST` | `/api/graph/search` | 图谱关系搜索 |

### 数据持久化

- `chat_history.db`：账号、登录会话和历史对话
- `chroma_data/`：文本片段及向量
- Neo4j：实体、章节、事实和关系
- 浏览器 `localStorage`：登录令牌和界面风格

生产部署时需要为 SQLite、ChromaDB 和模型缓存配置持久化数据卷，并通过 Nginx 或 Caddy 提供 HTTPS。

---

## English

An end-to-end knowledge-base chatbot built with React and FastAPI. It combines local BGE embeddings, ChromaDB retrieval, reranking, Neo4j graph retrieval, and OpenAI-compatible language models.

### Features

- React frontend and FastAPI backend
- PDF, DOCX, TXT, and Markdown ingestion
- Local `BAAI/bge-small-zh-v1.5` embeddings
- ChromaDB Top-K semantic retrieval
- `BAAI/bge-reranker-base` cross-encoder reranking
- Hybrid ChromaDB and Neo4j Graph RAG
- Dual knowledge-base and general AI chat modes
- DeepSeek and Qwen-VL model switching
- Qwen-VL image upload, preview, and visual question answering
- Automatic Neo4j graph generation after every document upload
- DeepSeek, OpenAI, and other OpenAI-compatible model support
- Local retrieval mode when the generation model is unavailable
- Persistent users, authentication sessions, and chat history in SQLite
- SVG image CAPTCHA and six-rule password-strength validation
- Dynamic questions generated from the latest knowledge base
- Refreshable question recommendations
- Horizontally scrollable source cards with graph-source highlighting
- Four UI styles: Aurora, Midnight, Paper, and Sunset
- Continuous visible Selenium tests in a single browser session

### Architecture

```text
Document upload
   ├── Parse and split text
   ├── BGE Embedding → ChromaDB
   └── Document/Section/Fact extraction → Neo4j

User question
   ├── ChromaDB Top-K retrieval
   ├── BGE Reranker
   ├── Neo4j relationship retrieval
   └── Retrieved context → LLM answer

General chat
   └── Conversation history → DeepSeek/OpenAI-compatible model
```

If Neo4j is unavailable, vector ingestion and retrieval continue to work. If the LLM API is unavailable, local retrieval mode can still return the relevant knowledge passages.

The header provides two isolated modes:

- **Knowledge Base** uses ChromaDB, reranking, and Neo4j and displays sources.
- **General Chat** bypasses retrieval and talks directly to the configured LLM without knowledge-base sources.

General Chat can use either DeepSeek or Qwen-VL. When Qwen-VL is selected, users
can upload PNG, JPG, or WEBP images up to 5 MB and send the image together with
their question.

Switching modes starts a new conversation to prevent context leakage between the two workflows.

### Quick start

Requirements:

- Python 3.10+
- Node.js 18+
- Chrome and ChromeDriver for Selenium tests
- Neo4j Aura or a local Neo4j instance for graph features

Install:

```powershell
cd F:\Langchain_chatbot

python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r backend/requirements.txt

cd frontend
npm.cmd install
npm.cmd run build
cd ..

Copy-Item .env.example .env
```

Configure `.env` with your model provider and Neo4j credentials. Never commit real secrets.

Start the backend and frontend and open the application:

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1
```

Start without opening a browser:

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1 -NoBrowser
```

Development URLs:

- React: <http://127.0.0.1:5173>
- FastAPI: <http://127.0.0.1:8001>
- OpenAPI: <http://127.0.0.1:8001/docs>

### Automatic graph generation

Every uploaded document is written to both ChromaDB and Neo4j. The graph generator creates `Document`, `Section`, and `Fact` nodes connected by `HAS_SECTION` and `HAS_FACT` relationships. This deterministic extraction works without an online LLM.

View all automatically generated graphs:

```cypher
MATCH path =
  (source:Entity)
  -[relation:KNOWLEDGE_RELATION]->
  (target:Entity)
WHERE source.dataset STARTS WITH "upload-"
RETURN path
```

### Visible end-to-end test

Start the services and run the continuous Sunset-theme test:

```powershell
powershell -ExecutionPolicy Bypass -File .\run_app.ps1 -Test
```

The test uses one Chrome session to verify the theme, password strength, CAPTCHA, registration, logout, login, eight knowledge-base questions, answer keywords, and source cards.

### Persistence

- `chat_history.db`: users, authentication sessions, and chat history
- `chroma_data/`: chunks and vectors
- Neo4j: entities, sections, facts, and relationships
- Browser `localStorage`: authentication token and UI style

For production, mount persistent volumes for SQLite, ChromaDB, and the model cache, and serve the application behind HTTPS with Nginx or Caddy.

### Docker deployment

Install and start Docker Desktop, then run from the project directory:

```powershell
docker compose up --build -d
docker compose ps
```

Open:

- Application: <http://127.0.0.1:8001>
- API documentation: <http://127.0.0.1:8001/docs>
- Neo4j Browser: <http://127.0.0.1:7474>

The first knowledge-base request downloads the local BGE embedding and reranker
models. The Compose model cache prevents downloading them again after a restart.
Existing `chat_history.db`, `chroma_data/`, and `uploads/` are mounted into the
container, so local accounts, conversations, knowledge chunks, and avatars remain
available.

Useful commands:

```powershell
docker compose logs -f app
docker compose restart app
docker compose down
```

`docker compose down` stops the services without deleting the model and Neo4j
volumes. Do not add `-v` unless you intentionally want to remove those volumes.
