# AWS 托管向量存储选型决策树 (Vector Store Advisor)

一个基于 Kiro Steering 的交互式向量存储选型工具。通过三阶段问答流程，引导 AWS SA/BD 根据客户负载特征，选出最合适的 AWS 托管向量存储服务。

## 覆盖的 AWS 服务

- Amazon ElastiCache (Valkey)
- Amazon MemoryDB (Valkey)
- Amazon Bedrock AgentCore Memory
- Amazon Aurora PostgreSQL (pgvector)
- Amazon DocumentDB
- Amazon Neptune Analytics
- Amazon OpenSearch Service
- Amazon S3 Vectors

## 决策流程

```
第一阶段：场景选型
  ├─ Agentic Memory（长期记忆）→ 按 Agent 框架筛选
  ├─ 知识库 → 文本检索(OpenSearch) / GraphRAG(Neptune) / 其他
  ├─ 多模态检索 → 多租户隔离(S3 Vectors) / 通用(OpenSearch)
  ├─ LLM 缓存 → ElastiCache
  └─ 其他 → 按现有数据位置和功能需求筛选

第二阶段：性能与数据特征
  └─ 向量维度、数量、QPS、延迟要求、扩展性需求 → 对照性能参考数据筛选

第三阶段：成本选型
  └─ 波峰波谷、业务形态、预算 → 对照成本数据做最终推荐
```

## 语言支持

本工具提供中英双语版本：

| 语言 | 文件 |
|------|------|
| 🇨🇳 中文 | `.kiro/steering/vector-store-advisor-zh.md` |
| 🇺🇸 English | `.kiro/steering/vector-store-advisor-en.md` |

## 使用方法

### 方式一：Kiro IDE

1. Clone 本仓库：
```bash
git clone https://github.com/fjjlb2088/vector-store-advisor.git
```
2. 用 Kiro 打开该目录
3. 在聊天框输入 `#` 选择对应语言版本的 steering 文件
4. 说"帮我做向量存储选型" 或 "Help me choose a vector store"
5. 按提示回答问题，最终获得推荐报告

### 方式二：手动复制

将 `.kiro/steering/vector-store-advisor-zh.md`（或 `-en.md`）复制到你的 Kiro 工作区的 `.kiro/steering/` 目录下即可使用。

## 包含的决策数据

- 8 种 AWS 向量存储服务的性能参考数据（Benchmark: Cohere-10M, 768维, HNSW）
  - 读性能（QPS、P99延迟、高并发表现）
  - Recall（ef_search=40 ~ 100 范围）
  - 写入性能（vectors/s、存储大小）
- 扩展性对比（横向扩展 vs 纵向扩展）
- 成本特征对比（Serverless 支持、计费模式、量化支持）
- 成本估算示例（768维 100万行 us-east-1 基准）
- 存储空间计算公式
- 一票否决/一票决定规则
- Agent 框架兼容性矩阵（Mem0、LangGraph、Strands、LangChain）

## 输出示例

```
🎯 AWS 向量存储选型推荐报告

客户场景摘要
- 业务场景：知识库 — 文本知识检索
- 数据规模：1024维 x 1000万
- 性能要求：QPS 5000, 延迟 <50ms

推荐方案
⭐ 首选：Amazon OpenSearch Service
- 推荐理由：支持关键字+向量双路召回，QPS 3400+（单节点），可横向分片扩展
- 预估月度成本：$762/月起

注意事项
- 建议使用 OpenSearch Serverless 应对波峰波谷
- 可通过 PQ/SQ 量化降低存储成本
```

## 文件结构

```
.kiro/
└── steering/
    ├── vector-store-advisor-zh.md   ← 中文版决策规则 + 交互流程
    └── vector-store-advisor-en.md   ← 英文版决策规则 + 交互流程
```

## 适用人群

- AWS SA（Solutions Architect）
- AWS BD（Business Development）
- 需要为客户推荐向量存储方案的技术人员

---

# AWS Managed Vector Storage Selection Decision Tree (Vector Store Advisor)

An interactive vector store selection tool powered by Kiro Steering. Guides AWS SA/BD through a three-phase Q&A flow to recommend the best AWS managed vector storage service based on customer workload characteristics.

## Covered AWS Services

- Amazon ElastiCache (Valkey)
- Amazon MemoryDB (Valkey)
- Amazon Bedrock AgentCore Memory
- Amazon Aurora PostgreSQL (pgvector)
- Amazon DocumentDB
- Amazon Neptune Analytics
- Amazon OpenSearch Service
- Amazon S3 Vectors

## Decision Flow

```
Phase 1: Scenario Selection
  ├─ Agentic Memory → Filter by Agent framework
  ├─ Knowledge Base → Text retrieval(OpenSearch) / GraphRAG(Neptune) / Other
  ├─ Multi-modal Retrieval → Multi-tenant isolation(S3 Vectors) / General(OpenSearch)
  ├─ LLM Cache → ElastiCache
  └─ Other → Filter by existing data location and functional requirements

Phase 2: Performance & Data Characteristics
  └─ Vector dimensions, count, QPS, latency, scalability → Match against benchmark data

Phase 3: Cost Selection
  └─ Peak/valley patterns, business model, budget → Final recommendation based on cost data
```

## Language Support

This tool provides bilingual versions:

| Language | File |
|----------|------|
| 🇨🇳 Chinese | `.kiro/steering/vector-store-advisor-zh.md` |
| 🇺🇸 English | `.kiro/steering/vector-store-advisor-en.md` |

## Usage

### Option 1: Kiro IDE

1. Clone this repository:
```bash
git clone https://github.com/fjjlb2088/vector-store-advisor.git
```
2. Open the directory with Kiro
3. Type `#` in the chat box and select the steering file for your preferred language
4. Say "Help me choose a vector store"
5. Answer the guided questions to receive a recommendation report

### Option 2: Manual Copy

Copy `.kiro/steering/vector-store-advisor-en.md` (or `-zh.md`) to your Kiro workspace's `.kiro/steering/` directory.

## Included Decision Data

- Performance benchmarks for 8 AWS vector storage services (Benchmark: Cohere-10M, 768d, HNSW)
  - Read performance (QPS, P99 latency, high-concurrency behavior)
  - Recall (ef_search=40 ~ 100 range)
  - Write performance (vectors/s, storage size)
- Scalability comparison (horizontal vs vertical scaling)
- Cost feature comparison (Serverless support, billing model, quantization)
- Cost estimation examples (768d, 1M rows, us-east-1 baseline)
- Storage space calculation formulas
- Veto/Override rules
- Agent framework compatibility matrix (Mem0, LangGraph, Strands, LangChain)

## Output Example

```
🎯 AWS Vector Storage Selection Report

Customer Scenario Summary
- Business scenario: Knowledge Base — Text knowledge retrieval
- Data scale: 1024d x 10M vectors
- Performance requirements: QPS 5000, Latency <50ms

Recommendation
⭐ Primary: Amazon OpenSearch Service
- Rationale: Supports keyword+vector hybrid retrieval, QPS 3400+ (single node), horizontally scalable via sharding
- Estimated monthly cost: from $762/month

Notes
- Consider OpenSearch Serverless for peak/valley workloads
- Use PQ/SQ quantization to reduce storage costs
```

## File Structure

```
.kiro/
└── steering/
    ├── vector-store-advisor-zh.md   ← Chinese decision rules + interaction flow
    └── vector-store-advisor-en.md   ← English decision rules + interaction flow
```

## Target Audience

- AWS SA (Solutions Architect)
- AWS BD (Business Development)
- Technical staff recommending vector storage solutions for customers

## License

Internal use only.
