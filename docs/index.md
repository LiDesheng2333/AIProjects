# Enterprise Knowledge Base RAG QA System

> 一个面向企业内部知识沉淀、制度查询、技术文档检索和员工自助问答场景的全栈 RAG 智能问答系统。项目基于 **Vue 3 + Flask + LangChain + Ollama + Qdrant + MySQL** 构建，支持多知识库管理、多格式文档向量化、飞书文档导入、带上下文记忆的智能问答、引用来源追溯、满意度反馈闭环和管理员数据看板。

![Vue](https://img.shields.io/badge/Vue-3.5-42b883)
![Vite](https://img.shields.io/badge/Vite-8.x-646cff)
![Flask](https://img.shields.io/badge/Flask-3.1-black)
![LangChain](https://img.shields.io/badge/LangChain-RAG-green)
![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-blue)
![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20DB-red)
![MySQL](https://img.shields.io/badge/MySQL-8.x-orange)

---

## 项目简介

传统企业知识通常分散在制度文档、技术规范、员工手册、飞书文档、PDF 和历史问答中，员工检索效率低，客服或行政/技术支持重复回答成本高。本项目通过 RAG（Retrieval-Augmented Generation，检索增强生成）方案，将企业文档解析、切分、向量化后存入 Qdrant 向量数据库，并结合本地 Ollama 大模型完成基于企业私有知识的智能问答。

系统不仅能回答“公司请假流程是什么？”、“API 认证方式怎么做？”这类知识库问题，还支持多轮上下文理解、长期记忆沉淀、答案引用来源展示和用户满意度反馈统计，形成从 **知识入库 → 语义检索 → 智能回答 → 反馈分析 → 知识优化** 的完整闭环。

---

## 核心功能

### 1. 企业知识库管理

- 支持创建、编辑、删除多个知识库
- 每个知识库拥有独立文档集合和独立向量 Collection
- 支持按知识库筛选文档和对话记录
- 适合按部门、业务线、产品线拆分企业知识，例如：规章制度库、技术文档库、产品帮助中心

### 2. 多格式文档上传与向量化

- 支持上传 `txt`、`md`、`pdf`、`docx` 文档
- 后端自动解析文档内容并进行文本切分
- 使用 Ollama Embedding 模型生成向量
- 使用 Qdrant 进行向量存储和相似度检索
- 支持上传状态管理：处理中、已就绪、失败
- 支持删除文档时同步清理 Qdrant 中的对应向量数据

### 3. 飞书文档导入

- 支持通过飞书文档链接或文档 Token 导入企业在线文档
- 支持飞书 `docx`、`doc`、`wiki` 常见链接解析
- 内置 tenant_access_token 缓存机制，减少重复鉴权请求
- 导入后自动转为 Markdown 文件并进入统一向量化流程

### 4. RAG 智能问答

- 用户可选择指定知识库进行提问
- 后端基于 LangChain 构建 RAG Chain
- 通过 Qdrant 检索 Top-K 相关知识片段
- 将检索内容、短期记忆、长期记忆共同注入 Prompt
- 使用本地 Ollama 大模型生成中文回答
- AI 回答会展示参考来源，提升可信度和可解释性

### 5. 多轮对话与长期记忆

- 支持会话级 `session_id`，可从历史记录继续对话
- 短期记忆：自动读取当前会话最近多轮问答，增强追问理解能力
- 长期记忆：从问答中提取有长期价值的用户偏好或上下文信息
- 长期记忆按用户和知识库维度保存，后续问答自动注入上下文

### 6. 答案满意度反馈闭环

- AI 回答支持“满意 / 不满意”反馈
- 不满意时可填写原因，便于定位知识库缺口或回答质量问题
- 管理员可查看各条问答反馈
- 后台统计整体满意度、满意/不满意数量、近 7 天满意度趋势、各知识库满意度

### 7. 管理员数据看板

- 用户总数、知识库数量、文档总数、今日提问数
- 近 7 天提问趋势折线图
- 知识库文档占比饼图
- 满意度趋势图
- 各知识库满意度柱状图
- 基于 ECharts 实现可视化展示，便于运营和优化知识库

### 8. 用户与权限体系

- JWT 登录认证
- 管理员 / 普通用户双角色设计
- 普通用户只能访问智能问答和自己的历史记录
- 管理员拥有知识库、文档、用户、统计看板等管理权限
- Axios 请求拦截器自动携带 Token，响应拦截器统一处理登录过期和错误提示

---

## 技术栈

### 前端

| 技术 | 说明 |
| --- | --- |
| Vue 3 | 前端核心框架，使用 Composition API |
| Vite | 前端工程化与开发服务器 |
| Vue Router | 页面路由与权限导航守卫 |
| Pinia | 用户登录状态和角色状态管理 |
| Element Plus | 后台管理 UI 组件库 |
| Axios | 前后端 HTTP 通信，统一拦截 Token 与错误处理 |
| ECharts | 管理员数据看板图表展示 |

### 后端

| 技术 | 说明 |
| --- | --- |
| Flask | Python Web 服务框架 |
| Flask-CORS | 跨域支持 |
| Flask-SQLAlchemy | ORM 数据模型管理 |
| PyMySQL | MySQL 数据库连接 |
| PyJWT | JWT 登录认证与权限校验 |
| LangChain | 构建 RAG 检索增强生成链路 |
| Ollama | 本地大语言模型与 Embedding 模型服务 |
| Qdrant | 高性能向量数据库，用于文档语义检索 |
| pypdf | PDF 文档解析 |
| python-docx | Word 文档解析 |
| Requests | 飞书开放平台接口调用 |

### AI / RAG 相关

| 模块 | 实现 |
| --- | --- |
| LLM | Ollama ChatOllama，本地 Qwen 系列模型 |
| Embedding | OllamaEmbeddings，本地向量模型 |
| 文档切分 | RecursiveCharacterTextSplitter |
| 向量存储 | QdrantVectorStore |
| 检索模式 | Dense Retrieval |
| 检索数量 | Top-K 相似片段召回 |
| Prompt | 参考资料 + 短期记忆 + 长期记忆 + 用户问题 |

---

## 系统架构

```text
┌──────────────────────────┐
│        Vue 3 前端         │
│  Element Plus / Pinia     │
│  Chat UI / 管理后台 / 看板 │
└─────────────┬────────────┘
              │ Axios + JWT
              ▼
┌──────────────────────────┐
│        Flask API          │
│ Auth / KB / Document      │
│ Chat / Feedback / Stats   │
└───────┬───────────┬──────┘
        │           │
        │           ▼
        │     ┌──────────────┐
        │     │    MySQL     │
        │     │ 用户/知识库/文档 │
        │     │ 对话/记忆/反馈   │
        │     └──────────────┘
        │
        ▼
┌──────────────────────────┐
│      LangChain RAG        │
│ 文档解析 / 文本切分 / 检索 │
│ Prompt 编排 / 答案生成     │
└───────┬───────────┬──────┘
        │           │
        ▼           ▼
┌──────────────┐   ┌──────────────┐
│   Qdrant      │   │    Ollama     │
│  向量数据库    │   │ LLM + Embedding│
└──────────────┘   └──────────────┘
```

---

## 项目亮点

### 多知识库向量隔离设计

系统不是将所有文档混在一个向量空间中，而是为每个知识库创建独立的 Qdrant Collection，例如 `kb_1`、`kb_2`。这种设计能减少跨业务知识干扰，提升召回准确性，也更符合企业中按部门、系统、业务线隔离知识资产的真实场景。

### 本地化大模型方案，适合企业私有知识场景

项目基于 Ollama 调用本地大语言模型和本地 Embedding 模型，企业文档无需发送到第三方云端 API。相比直接调用公网大模型 API，该方案更适合制度文档、内部流程、技术规范等敏感数据场景。

### 从“能问答”升级到“可运营”的 RAG 系统

项目不仅实现了基础 RAG 问答，还加入了满意度反馈、知识库满意度统计、近 7 天趋势、各知识库效果对比等运营指标。管理员可以根据低满意度回答定位知识缺口，持续优化知识库质量。

### 支持短期记忆 + 长期记忆

短期记忆用于理解当前会话中的追问和上下文，长期记忆用于保存用户长期偏好或业务背景。相比一次性问答系统，该项目具备更强的连续对话能力和个性化上下文理解能力。

### 文档接入能力完整

系统支持本地多格式文档上传，也支持飞书在线文档导入，并统一进入解析、切分、向量化流程。这种设计贴近企业实际知识分布，既能处理历史文件，也能接入在线协作文档。

### 前后端权限体系完整

系统实现了 JWT 登录认证、管理员/普通用户角色控制、前端路由守卫和后端接口装饰器校验。普通用户专注问答，管理员负责知识库、文档、用户和数据运营，符合真实企业后台系统的权限模型。

---

## 目录结构

```text
client/
├── src/
│   ├── api/                 # Axios 请求封装，按业务模块拆分 API
│   ├── components/          # 通用组件，如 ChatMessage
│   ├── router/              # Vue Router 路由和权限守卫
│   ├── stores/              # Pinia 用户状态管理
│   ├── views/               # 页面：登录、问答、知识库、文档、用户、统计看板
│   ├── App.vue
│   └── main.js
├── vite.config.js
└── package.json

server/
├── app.py                   # Flask 应用入口和蓝图注册
├── config.py                # 数据库、Ollama、Qdrant、上传、RAG 配置
├── models/                  # SQLAlchemy ORM 模型
├── routes/                  # API 路由：认证、知识库、文档、问答、用户、统计
├── services/                # RAG、向量化、飞书服务
├── utils/                   # JWT 权限、统一响应
├── sql/                     # 初始化和增量升级 SQL
├── uploads/                 # 上传/导入后的文档存储目录
└── requirements.txt
```

---

## 核心业务流程

### 文档入库流程

```text
管理员上传文档 / 导入飞书文档
        ↓
后端保存文档记录和原始文件
        ↓
解析 txt / md / pdf / docx 内容
        ↓
RecursiveCharacterTextSplitter 文本切分
        ↓
Ollama Embedding 生成向量
        ↓
写入对应知识库的 Qdrant Collection
        ↓
更新文档状态和知识库文档数量
```

### 智能问答流程

```text
用户选择知识库并提问
        ↓
读取当前 session 短期记忆
        ↓
读取用户在该知识库下的长期记忆
        ↓
Qdrant 检索相关文档片段
        ↓
拼接 Prompt：参考资料 + 短期记忆 + 长期记忆 + 问题
        ↓
Ollama LLM 生成回答
        ↓
保存问答历史、引用来源和可提取长期记忆
        ↓
前端展示回答、参考来源和满意度反馈入口
```

---

## 数据模型设计

| 表名 | 说明 |
| --- | --- |
| `t_user` | 用户表，包含账号、角色、状态等信息 |
| `t_knowledge_base` | 知识库表，维护知识库名称、描述、状态、文档数 |
| `t_document` | 文档表，记录文件名、路径、类型、大小、分块数、向量化状态 |
| `t_chat_history` | 对话历史表，保存问题、回答、引用来源、会话 ID |
| `t_chat_memory` | 长期记忆表，按用户和知识库保存可复用上下文 |
| `t_chat_feedback` | 问答反馈表，记录评分、满意度和反馈备注 |

---

## API 模块

| 模块 | 主要接口能力 |
| --- | --- |
| `/api/auth` | 登录、获取当前用户信息 |
| `/api/knowledge_base` | 知识库列表、新增、编辑、删除、获取全部知识库 |
| `/api/document` | 文档列表、上传文档、删除文档、导入飞书文档 |
| `/api/chat` | RAG 问答、历史记录、会话详情、长期记忆、满意度反馈 |
| `/api/user` | 用户列表、新增用户、编辑用户 |
| `/api/stats` | 管理员首页统计概览和图表数据 |

---

## 快速启动

### 1. 启动基础服务

需要先准备：

- MySQL
- Qdrant
- Ollama
- Ollama 中已安装 LLM 模型和 Embedding 模型

示例环境变量：

```bash
export MYSQL_HOST=127.0.0.1
export MYSQL_PORT=3306
export MYSQL_USER=root
export MYSQL_PASSWORD=123456
export MYSQL_DATABASE=db_enterprise_qa

export OLLAMA_BASE_URL=http://localhost:11434
export OLLAMA_LLM_MODEL=modelscope.cn/Qwen/Qwen3-8B-GGUF
export OLLAMA_EMBED_MODEL=modelscope.cn/Qwen/Qwen3-Embedding-8B-GGUF

export QDRANT_URL=http://localhost:6333
```

### 2. 初始化数据库

```bash
cd server
mysql -u root -p < sql/init.sql
```

如果是已有数据库升级，可执行：

```bash
mysql -u root -p < sql/add_chat_feedback.sql
mysql -u root -p < sql/add_chat_memory.sql
```

### 3. 启动后端

```bash
cd server
pip install -r requirements.txt
python app.py
```

后端默认运行在：

```text
http://127.0.0.1:5000
```

### 4. 启动前端

```bash
cd client
npm install
npm run dev
```

前端默认运行在：

```text
http://127.0.0.1:3000
```

Vite 已配置 `/api` 代理到 Flask 后端。

---

## 测试账号

```text
管理员：admin / 123456
普通用户：user1 / 123456
普通用户：user2 / 123456
```

---

## 适用场景

- 企业内部制度问答
- 员工手册 / 入职指南查询
- 技术文档智能检索
- API 文档问答助手
- 产品帮助中心智能客服
- 飞书知识库智能问答
- 内部 IT / HR / 行政自助问答平台

---

## 项目价值

这个项目完整覆盖了企业级 RAG 应用从数据接入、知识治理、向量检索、LLM 生成、权限管理到运营反馈的核心链路。相比只调用大模型接口的普通聊天 Demo，本项目更接近真实业务系统：有用户体系、有多知识库隔离、有文档生命周期管理、有私有化模型部署、有引用来源追溯、有满意度反馈和数据看板，能够体现对 RAG 工程落地、前后端协作和企业应用架构的完整理解。

---

## 可扩展方向

- 增加流式输出，提升问答交互体验
- 增加重排序模型，提高复杂问题召回精度
- 增加文档分块预览和命中片段高亮
- 增加知识库级权限控制，实现不同部门数据隔离
- 增加异步任务队列，提升大文件向量化体验
- 接入更多企业知识源，例如钉钉、Confluence、Notion、企业微信
- 引入评测集，自动评估不同模型和不同切分策略的问答效果

