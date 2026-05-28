# AWS 托管向量存储选型决策树 (Vector Store Advisor)

一个基于 Kiro Steering / Amazon Quick Skill 的交互式向量存储选型工具。通过三阶段问答流程，引导 AWS SA/BD 根据客户负载特征，选出最合适的 AWS 托管向量存储服务。

## 覆盖的 AWS 服务

- Amazon ElastiCache (Valkey)
- Amazon MemoryDB (Valkey)
- Amazon Bedrock AgentCore Memory
- Amazon Aurora PostgreSQL (pgvector)
- Amazon DocumentDB
- Amazon Neptune Analytics
- Amazon OpenSearch Service
- Amazon S3 Vectors

## 使用方法

### 方式一：Kiro IDE

1. Clone 本仓库
2. 用 Kiro 打开该目录
3. 在聊天框输入 `#` 选择 `.kiro/steering/` 下对应语言版本的文件
4. 说"帮我做向量存储选型"

### 方式二：Amazon Quick Desktop

1. 将 `.quickwork/skills/vector-store-advisor/` 目录复制到你本地的 `~/.quickwork/skills/` 下
2. 重启 Amazon Quick 或开始新对话
3. 说"向量存储选型"（中文）或 "vector store selection"（English）即可触发

### 方式三：手动复制

将对应文件复制到你的工具目录：
- Kiro: 复制到 `.kiro/steering/`
- Quick: 复制到 `~/.quickwork/skills/vector-store-advisor/`

## 语言支持

| 语言 | Kiro Steering | Amazon Quick Skill |
|------|--------------|-------------------|
| 🇨🇳 中文 | `.kiro/steering/vector-store-advisor-zh.md` | `.quickwork/skills/vector-store-advisor/SKILL-zh.md` |
| 🇺🇸 English | `.kiro/steering/vector-store-advisor-en.md` | `.quickwork/skills/vector-store-advisor/SKILL-en.md` |

## 文件结构

```
vector-store-advisor/
├── .kiro/steering/                              ← Kiro IDE 用
│   ├── vector-store-advisor-zh.md
│   └── vector-store-advisor-en.md
├── .quickwork/skills/vector-store-advisor/      ← Amazon Quick Desktop 用
│   ├── SKILL-zh.md
│   └── SKILL-en.md
├── README.md
└── .gitignore
```

## 包含的决策数据

- 8 种 AWS 向量存储服务的性能参考数据（Benchmark: Cohere-10M, 768维, HNSW）
- 扩展性对比（横向扩展 vs 纵向扩展）
- 成本特征对比（Serverless 支持、计费模式、量化支持）
- 成本估算示例（768维 100万行 us-east-1 基准）
- 存储空间计算公式
- 一票否决/一票决定规则
- Agent 框架兼容性矩阵（Mem0、LangGraph、Strands、LangChain）

## 适用人群

- AWS SA（Solutions Architect）
- AWS BD（Business Development）
- 需要为客户推荐向量存储方案的技术人员

---

# AWS Managed Vector Storage Selection Decision Tree (Vector Store Advisor)

An interactive vector store selection tool powered by Kiro Steering / Amazon Quick Skill. Guides AWS SA/BD through a three-phase Q&A flow to recommend the best AWS managed vector storage service based on customer workload characteristics.

## Covered AWS Services

- Amazon ElastiCache (Valkey)
- Amazon MemoryDB (Valkey)
- Amazon Bedrock AgentCore Memory
- Amazon Aurora PostgreSQL (pgvector)
- Amazon DocumentDB
- Amazon Neptune Analytics
- Amazon OpenSearch Service
- Amazon S3 Vectors

## Usage

### Option 1: Kiro IDE

1. Clone this repository
2. Open the directory with Kiro
3. Type `#` in the chat box and select the steering file from `.kiro/steering/`
4. Say "Help me choose a vector store"

### Option 2: Amazon Quick Desktop

1. Copy `.quickwork/skills/vector-store-advisor/` to your local `~/.quickwork/skills/`
2. Restart Amazon Quick or start a new conversation
3. Say "vector store selection" (English) or "向量存储选型" (Chinese) to trigger

### Option 3: Manual Copy

Copy the relevant file to your tool directory:
- Kiro: copy to `.kiro/steering/`
- Quick: copy to `~/.quickwork/skills/vector-store-advisor/`

## Language Support

| Language | Kiro Steering | Amazon Quick Skill |
|----------|--------------|-------------------|
| 🇨🇳 Chinese | `.kiro/steering/vector-store-advisor-zh.md` | `.quickwork/skills/vector-store-advisor/SKILL-zh.md` |
| 🇺🇸 English | `.kiro/steering/vector-store-advisor-en.md` | `.quickwork/skills/vector-store-advisor/SKILL-en.md` |

## File Structure

```
vector-store-advisor/
├── .kiro/steering/                              ← For Kiro IDE
│   ├── vector-store-advisor-zh.md
│   └── vector-store-advisor-en.md
├── .quickwork/skills/vector-store-advisor/      ← For Amazon Quick Desktop
│   ├── SKILL-zh.md
│   └── SKILL-en.md
├── README.md
└── .gitignore
```

## Included Decision Data

- Performance benchmarks for 8 AWS vector storage services (Cohere-10M, 768d, HNSW)
- Scalability comparison (horizontal vs vertical scaling)
- Cost feature comparison (Serverless support, billing model, quantization)
- Cost estimation examples (768d, 1M rows, us-east-1 baseline)
- Storage space calculation formulas
- Veto/Override rules
- Agent framework compatibility matrix (Mem0, LangGraph, Strands, LangChain)

## Target Audience

- AWS SA (Solutions Architect)
- AWS BD (Business Development)
- Technical staff recommending vector storage solutions for customers

## License

Internal use only.
