# AWS 托管向量存储选型决策树 (Vector Store Advisor)

一个基于 Kiro Steering 的交互式向量存储选型工具。通过三阶段问答流程，引导 AWS SA/BD 根据客户负载特征，选出最合适的 AWS 托管向量存储服务。

## 覆盖的 AWS 服务

- Amazon ElastiCache (Valkey)
- Amazon MemoryDB (Valkey)
- Amazon Bedrock Agent Core Memory
- Amazon Aurora PostgreSQL (pgvector)
- Amazon DocumentDB
- Amazon Neptune
- Amazon OpenSearch Service
- Amazon S3 Vectors

## 决策流程

```
第一阶段：场景选型
  ├─ Agentic Memory（长期记忆）→ 按 Agent 框架筛选
  ├─ 知识库 → 文本检索(OpenSearch) / GraphRAG(Neptune)
  ├─ 多模态检索 → 多租户隔离(S3 Vectors) / 通用(OpenSearch)
  ├─ LLM 缓存 → ElastiCache
  └─ 其他 → 按现有数据位置和功能需求筛选

第二阶段：性能与数据特征（如果第一阶段未确定唯一推荐）
  └─ 向量维度、数量、QPS、延迟要求 → 对照性能参考数据筛选

第三阶段：成本选型（如果第二阶段仍有多个候选）
  └─ 波峰波谷、业务形态、预算 → 对照成本数据做最终推荐
```

## 使用方法

### 方式一：Kiro IDE

1. Clone 本仓库：
```bash
git clone https://github.com/fjjlb2088/vector-store-advisor.git
```

2. 用 Kiro 打开该目录

3. 在聊天框输入 `#` 选择 `vector-store-advisor`，然后说"帮我做向量存储选型"

4. 按提示回答问题，最终获得推荐报告

### 方式二：手动复制

将 `.kiro/steering/vector-store-advisor.md` 复制到你的 Kiro 工作区的 `.kiro/steering/` 目录下即可使用。

## 输出示例

```
🎯 AWS 向量存储选型推荐报告

客户场景摘要
- 业务场景：知识库 — 文本知识检索
- 数据规模：1024维 x 1000万
- 性能要求：QPS 5000, 延迟 <50ms

推荐方案
⭐ 首选：Amazon OpenSearch Service
- 推荐理由：支持关键字+向量双路召回，QPS 9000，延迟 10-60ms
- 预估月度成本：$762/月起

注意事项
- 建议使用 OpenSearch Serverless 应对波峰波谷
```

## 包含的决策数据

- 8 种 AWS 向量存储服务的性能参考数据（QPS、延迟、数据上限）
- 成本特征对比（Serverless 支持、计费模式、量化支持）
- 成本估算示例（768维 100万行 us-east-1 基准）
- 一票否决/一票决定规则
- Agent 框架兼容性矩阵（Mem0、LangGraph、Strands、LangChain）

## 文件结构

```
.kiro/
└── steering/
    └── vector-store-advisor.md   ← 决策规则 + 交互流程
```

## 适用人群

- AWS SA（Solutions Architect）
- AWS BD（Business Development）
- 需要为客户推荐向量存储方案的技术人员

## License

Internal use only.
