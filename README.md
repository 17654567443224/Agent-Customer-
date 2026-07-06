# Agent-Customer

> 基于 **PyQt6** 开发的桌面端 AI 电商客服系统，集成 **LLM、Agent、RAG** 等能力，实现多轮对话、工具调用、知识库检索及商品推荐，支持智能客服自动回复与业务处理。

项目采用模块化架构设计，将 **渠道接入、消息处理、Agent 调度、工具调用、知识检索** 等模块解耦，便于后续扩展新的平台和业务能力。

---

## ✨ 项目亮点

### 🤖 自研 Agent 框架

项目未依赖 LangChain、Agno 等 Agent 框架，而是根据业务需求实现了一套轻量级 Agent。

支持：

- 多轮对话管理
- 会话上下文维护
- Tool Calling
- 工具执行结果回传
- Agent 状态管理

Agent 能根据用户问题自主决定是否调用工具，例如查询商品、检索知识库、发送商品卡片或转接人工客服，而非依赖固定流程。

---

### 📚 双知识库 RAG

系统采用双知识库设计，将商品知识与客服知识分离，提高检索准确率。

**商品知识库**

主要存储：

- 商品介绍
- 商品规格
- 商品价格
- 使用方法
- 商品成分

**客服知识库**

主要存储：

- 售后政策
- 退款规则
- 发货说明
- 物流问题
- 常见客服问答

不同类型的问题分别检索对应知识库，减少无关知识干扰，提高回答质量。

---

### 🛍 商品知识自动同步

系统支持自动同步店铺商品信息。

整体流程：

```text
拼多多商品 API
        │
        ▼
获取商品列表
        │
        ▼
调用多模态 LLM 提取商品知识
        │
        ▼
写入商品知识库
```

无需人工维护商品知识，可保持知识库与店铺商品同步更新。

---

### ⚡ 异步消息处理架构

消息处理采用 **消息队列 + Handler Pipeline** 架构。

```text
WebSocket
      │
      ▼
Message Queue
      │
      ▼
Pre Handler
      │
      ▼
Keyword Handler
      │
      ▼
AI Handler
      │
      ▼
Reply
```

优势：

- 消息处理解耦
- 支持异步消费
- 易于扩展新的 Handler
- 提高高并发场景下的处理能力

---

### 🔌 多渠道架构

目前支持：

- 拼多多 WebSocket

渠道层进行了统一抽象，新增平台时仅需实现对应 Adapter，即可复用整个 AI Agent 及消息处理流程。

---

### 💬 智能转人工

支持关键词触发人工客服，例如：

- 转人工
- 投诉
- 人工客服

满足条件后 Agent 自动调用工具完成会话转接。

---

## 🏗 系统架构

```text
                   User
                     │
                     ▼
            Channel(WebSocket)
                     │
                     ▼
              Message Queue
                     │
        ┌────────────┴────────────┐
        │                         │
   Pre Handler           Keyword Handler
        │                         │
        └────────────┬────────────┘
                     │
                     ▼
              Customer Agent
                     │
        ┌────────────┼────────────┐
        │            │            │
   商品工具      商品知识库     客服知识库
        │            │            │
        └────────────┼────────────┘
                     │
                     ▼
             Tool Execution
                     │
                     ▼
                  Response
```

---

## 🛠 Agent 可调用工具

| 工具 | 功能 |
|------|------|
| `get_shop_products` | 获取店铺商品列表 |
| `get_product_knowledge` | 查询商品详细知识 |
| `search_customer_service_knowledge` | 检索客服知识库 |
| `send_goods_link` | 发送商品卡片 |
| `transfer_conversation` | 转接人工客服 |

---

## 📂 项目结构

```text
Agent-Customer/
│
├── Agent/                         # 自研 Agent 框架
│   └── CustomerAgent/
│       ├── custom/                # LLM、Session、Tool Executor
│       └── tools/                 # Agent 工具实现
│
├── Channel/                       # 渠道接入
│   └── pinduoduo/
│       ├── core/
│       └── utils/API/
│
├── Message/                       # 消息处理
│   ├── core/                      # Queue、Consumer
│   └── handlers/                  # Handler Pipeline
│
├── bridge/                        # Context、Reply
│
├── core/                          # DI、缓存、状态管理
│
├── database/                      # 数据库、知识库、商品同步
│
├── ui/                            # PyQt6 UI
│
├── utils/                         # 工具模块
│
├── scripts/                       # 构建脚本
│
└── app.py                         # 程序入口
```

---

## 🚀 快速开始

### 环境要求

- Python >= 3.11
- Windows

### 安装依赖

```bash
uv sync
```

### 启动项目

```bash
python app.py
```

首次启动将在项目根目录自动生成：

```text
config.json
```

主要配置：

| 配置项 | 说明 |
|--------|------|
| llm | 大模型配置 |
| embedder | 向量模型配置 |
| knowledge_base | 知识库存储路径 |
| business_hours | 人工客服工作时间 |
| prompt | AI 提示词配置 |

---

## ⚙️ 技术栈

| 模块 | 技术 |
|------|------|
| 编程语言 | Python 3.11 |
| UI | PyQt6、pyqt6-fluent-widgets |
| Agent | 自研 Agent Framework |
| LLM | OpenAI Compatible API |
| 数据库 | SQLAlchemy、SQLite |
| 文档解析 | pypdf、python-docx、openpyxl、xlrd |
| 中文分词 | jieba |
| Token 统计 | tiktoken |
| 网络通信 | asyncio、websockets |
| 配置管理 | Pydantic |
| 日志 | Loguru |

---

## 📌 项目特点

- 自研 Agent 框架，实现多轮对话、上下文管理与 Tool Calling。
- 双知识库（商品知识 + 客服知识）设计，提高知识检索准确率。
- 商品知识自动同步，通过商品 API 与多模态 LLM 自动构建知识库。
- 消息处理采用异步队列与 Handler Pipeline，提升系统可扩展性。
- 渠道层统一抽象，方便扩展新的电商平台。
- WebSocket 支持自动重连及心跳检测，提高连接稳定性。

---

## 📄 License

MIT