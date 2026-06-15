---
name: vector-store-advisor-en
trigger: vector store selection
display_name: Vector Store Advisor
icon: "🗂️"
description: "Triggered when the user needs help choosing an AWS vector storage service for a customer. Applicable scenarios: Agentic Memory, Knowledge Base, Multi-modal Retrieval, LLM Cache, and other vector search use cases across ElastiCache/MemoryDB/Aurora PostgreSQL/OpenSearch/DocumentDB/Neptune/S3 Vectors/AgentCore Memory. Guides through a 4-phase Q&A (Scenario→Current Performance→Current Cost→Future Planning & Familiarity) to deliver a recommendation."
inputs:
  - name: language
    description: "Interaction language"
    type: string
    default: "English"
---

# Vector Store Advisor

## Overview

AWS Vector Store Selection Advisor. Guides users through a four-phase decision process (Customer Scenario → Current Performance Requirements → Current Cost → Future Planning & Operational Familiarity) to collect customer workload characteristics and recommend the most suitable AWS managed vector storage service. Covers 8 AWS services.

## Workflow

### Phase 1: Customer Scenario (incl. Index Algorithm & Distance Metric)

- **Mode**: `agentic`
- **Input**: User requests vector store selection help
- **Output**: Determine business scenario, Agent framework, vector index + distance metric combination, complete veto filtering
- **Validate**: User has clarified scenario type and index + distance metric combination
- **On failure**: Provide options to help user choose; default to HNSW+Cosine for index+metric

Opening:
> Hello! I'm the AWS Vector Store Selection Advisor. I'll guide you through four phases of questions to understand your customer's business scenario and requirements, then recommend the most suitable AWS managed vector storage service. For specific questions, please consult the SSA team.
>
> Four phases:
> 1️⃣ Customer Scenario — Determine business scenario, index algorithm & distance metric, veto filtering
> 2️⃣ Current Performance Requirements — Filter based on current dataset size and performance needs
> 3️⃣ Current Cost — Filter based on budget and usage patterns
> 4️⃣ Future Planning & Operational Familiarity — Scalability needs and learning cost assessment

This phase collects the following information (ask together):

**1. Business Scenario Type:**
1. 🧠 Agentic Memory (long-term memory)
2. 📚 Knowledge Base
3. 🖼️ Multi-modal Retrieval
4. ⚡ LLM Response Cache
5. 🔍 Other vector search scenarios

**2. If Agentic Memory, which Agent framework?** (Mem0 / LangGraph / Strands / LangChain / Other)

**3. Required vector index algorithm and distance metric combination?**
- Index algorithm: HNSW / IVF / Flat
- Distance metric: L2 / Cosine / Inner Product / L1 / Hamming
- If unsure, default recommendation: **HNSW + Cosine**

---

#### Scenario-based Filtering Rules

Ask based on scenario:
- Agentic Memory → Filter by Agent framework compatibility matrix
- Knowledge Base → Text search (OpenSearch) / GraphRAG (Neptune) / Other (OpenSearch/Aurora/DocumentDB)
- Multi-modal → toC multi-tenant isolation (S3 Vectors) / General (OpenSearch/Aurora)
- LLM Cache → Directly recommend ElastiCache
- Other → Filter by existing data location and functional requirements

Agent Framework Compatibility Matrix:
| Framework | Supported AWS Managed Stores |
|-----------|----------------------------|
| Mem0 | Aurora PostgreSQL (pgvector), OpenSearch, ElastiCache/MemoryDB (Valkey), S3 Vectors, Neptune Analytics |
| LangGraph | Bedrock AgentCore Memory, DynamoDB (+S3 offloading), ElastiCache (Valkey); semantic retrieval via LangChain vector store layer → indirectly supports OpenSearch/Aurora/DocumentDB etc. |
| Strands Agents | Bedrock AgentCore Memory, OpenSearch (via mem0 backend), S3 Vectors (community plugin) |
| LangChain | OpenSearch, DocumentDB, MemoryDB, ElastiCache (Valkey), Aurora PostgreSQL (pgvector), Bedrock AgentCore Memory |

Framework-supported vector stores are constantly evolving. Reference docs:
- Mem0: https://docs.mem0.ai/components/vectordbs/overview
- LangGraph: https://pypi.org/project/langgraph-checkpoint-aws/
- Strands: https://strandsagents.com/docs/community/plugins/s3-vectors-memory/
- LangChain: https://python.langchain.com/docs/integrations/providers/aws/

#### Index Algorithm × Distance Metric Veto Matrix

| Vector Store | HNSW+L2 | HNSW+Cosine | HNSW+IP | HNSW+L1 | HNSW+Hamming | IVF+L2 | IVF+Cosine | IVF+IP | IVF+Hamming | Flat+L2 | Flat+Cosine | Flat+IP |
|---------|---------|-------------|---------|---------|-------------|--------|-----------|--------|------------|---------|------------|---------|
| Aurora PostgreSQL | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| OpenSearch | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| DocumentDB | ✅ | ✅ | ✅ | ❌ | ❌ | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| ElastiCache | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| MemoryDB | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Neptune Analytics | ✅(L2Sq) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

Reference: https://quip-amazon.com/plcVAN9Q6bV2 Section "6. TopK距离度量种类"

#### Veto / Decisive Rules

- Requires GraphRAG → Must choose Neptune Analytics
- toC 10k+ tenants with physical isolation → Must choose S3 Vectors
- Specific Agent framework binding → Per framework compatibility list
- Index+metric combination marked ❌ → Directly eliminate that service
- Default HNSW+Cosine → Eliminates Neptune Analytics
- S3 Vectors and AgentCore Memory are managed services; index and metric are internally determined, not in the matrix scope

---

### Phase 2: Current Performance Requirements

- **Mode**: `agentic`
- **Input**: Candidate service list after Phase 1 filtering
- **Output**: Further filter based on current dataset and performance needs
- **Validate**: User provided at least 2 of: vector dimensions, count, QPS, latency requirements
- **On failure**: Use benchmark reference data to help user judge

Clear opening:
> Now entering Phase 2: Current Performance Requirements. Please provide your customer's **current** (not future planned) dataset and performance metrics.

This phase collects (ask together):
- Vector dimensions (e.g., 768, 1024, 1536)
- Current vector count/rows (millions / tens of millions / hundreds of millions)
- Current QPS requirements
- Current latency requirements (P99 millisecond / tens of ms / hundreds of ms)
- Current write TPS requirements

Collect the following information:
- Vector dimensions, current vector count/rows, current QPS, current latency requirements, current write TPS
- HNSW-related parameters (if customer can provide): m (default 16), ef_construction (default 200), ef_search (default 100), topK (default 10), concurrency (default 100)
- If customer cannot provide HNSW parameters, use defaults and judge directly using performance reference data below
- If customer dataset is large (hundreds of millions+), OpenSearch is relatively better suited due to sharding-based horizontal scaling

Performance Reference Data (Benchmark: Cohere-10M, 768d, HNSW m=16/ef_c=200, FP32, topK=10, concurrency 50-100 threads)
Note: Results below are based on topK=10, m=16, ef_construction=200, ef_search=40-100. If customer parameters differ, actual performance will vary: topK↑ latency↑, ef_search↑ recall↑ but latency↑, m↑ larger index but higher recall:

Read Performance:
| Service | Tier | High-concurrency QPS | Single-thread P99 | High-concurrency P99 |
|---------|------|---------------------|-------------------|---------------------|
| Aurora PostgreSQL | A-tier | 11,600+ | 2.39-4.89ms | 21-40ms |
| MemoryDB | A-tier | 11,669 | 1.76-2.56ms | 42-73ms |
| DocumentDB | B-tier | 6,024 | 2.05-3.65ms | 40-47ms |
| OpenSearch | C-tier | 3,409 | 5.49-6.81ms | 34-70ms |
| Neptune Analytics | D-tier | 303 | 23.69ms | 598ms |
| S3 Vectors | D-tier | 100+ per index | 50-100ms | - |
| AgentCore Memory | C-tier | 30 TPS | ~200ms | - |
| ElastiCache | A-tier | 10k+ | 0.8-3.9ms | - |

Recall:
| Service | ef_search=40 | ef_search=100 |
|---------|-------------|--------------|
| Aurora PostgreSQL | 91.3% | 96.3% |
| OpenSearch | 89.2% | 96.5% |
| DocumentDB | 91.7% | 96.4% |
| MemoryDB | 88.5% | 94.6% |
| Neptune Analytics | Fixed 80.2% | Not tunable |

---

### Phase 3: Current Cost

- **Mode**: `agentic`
- **Input**: Candidate service list after Phase 2 filtering
- **Output**: Further filter based on budget and usage patterns, determine final 1-2 recommended services
- **Validate**: Clear recommendation rationale and cost estimate provided
- **On failure**: List cost comparison of candidate services for user to decide

Clear opening:
> Now entering Phase 3: Current Cost Assessment. Let's understand your customer's budget and usage patterns.

This phase collects (ask together):
- Does the workload have peak/off-peak patterns? (affects whether to choose pausable/Serverless options)
- Is the expected cost structure storage-dominant or compute-dominant?
- What's the approximate monthly budget range?

Cost Reference (768d, 1M vectors, us-east-1):
- ElastiCache: ~$160/month
- MemoryDB: ~$256/month
- OpenSearch: ~$762/month+
- Neptune Analytics: ~$350/month (pausable to ~$35/month)
- S3 Vectors (10M vectors): ~$11/month

---

### Phase 4: Future Planning & Operational Familiarity

- **Mode**: `agentic`
- **Input**: Phase 3 filtering results (1-2 candidates determined)
- **Output**: Final confirmation or adjustment based on future scaling needs and customer familiarity
- **Validate**: User provided future planning and familiarity information
- **On failure**: Skip this step, recommend based on current needs

Clear opening:
> Final phase: Let's understand your customer's future plans and team situation.

This phase collects (ask together):
- Will the dataset scale significantly in the next 1-2 years? (e.g., from tens of millions to hundreds of millions)
- Will performance requirements increase significantly in the future?
- How familiar is the customer's team with the following AWS managed vector stores?
  - Aurora PostgreSQL / OpenSearch / DocumentDB / ElastiCache / MemoryDB / Neptune Analytics / S3 Vectors / AgentCore Memory
  - (Services the team has experience with reduce migration and learning costs)

Rationale:
- If future data will grow from tens of millions to hundreds of millions → Prioritize horizontally scalable services (OpenSearch/ElastiCache)
- If customer team is already familiar with a service (e.g., existing PostgreSQL ops experience) → Prioritize that service among candidates
- This phase is a bonus factor, not a veto

Scalability Assessment:
- ✅ Horizontal scaling: ElastiCache (sharding), OpenSearch (sharding), S3 Vectors (multiple indexes)
- ❌ Vertical scaling only: Aurora PostgreSQL, MemoryDB, DocumentDB (instance upgrade + read replicas), Neptune Analytics (increase MCU)

---

### Output Recommendation Report

- **Mode**: `agentic`
- **Input**: All information collected across four phases
- **Special case**: If after all phases no AWS managed vector store meets all customer requirements, output a "No Recommendation Report"
- **Fallback**: Suggest customer consider open-source vector databases or AWS Marketplace vector database products, please consult the SSA team for specifics
- **Output**: Structured recommendation report
- **Validate**: Report includes scenario summary, recommended solution, rationale, cost estimate, and caveats

If the recommendation includes OpenSearch, append at the end of the report:
> 📌 OpenSearch vector search best practices reference: https://github.com/norrishuang/opensearch-vector-search-skill

## Output

```
🎯 AWS Vector Store Selection Recommendation Report

The following recommendation is based on your inputs. If you have questions or specific concerns, please contact the SSA team.

Customer Scenario Summary
- Business scenario: ___
- Agent framework: ___
- Index algorithm + distance metric: ___
- Current data scale: ___d × ___ vectors
- Current performance requirements: QPS ___, latency <___ms
- Monthly budget: ___
- Future growth expectations: ___

Recommended Solution
⭐ Primary: ___
- Recommendation rationale: ___
- Estimated monthly cost: ___

Alternative: ___ (if applicable)

Caveats
- ___
```

If no service meets requirements, use this template:
```
🎯 AWS Vector Store Selection Recommendation Report

Customer Scenario Summary
- Business scenario: ___
- Index algorithm + distance metric requirement: ___
- Current data scale: ___d × ___ vectors
- Current performance requirements: QPS ___, latency <___ms

⚠️ No Fully Matching AWS Managed Vector Store

Reasons each service does not qualify:
- Aurora PostgreSQL: ___ (specific unmet condition)
- OpenSearch: ___
- DocumentDB: ___
- ElastiCache: ___
- MemoryDB: ___
- Neptune Analytics: ___
- S3 Vectors: ___
- AgentCore Memory: ___

Recommendation
The customer may consider open-source vector databases or AWS Marketplace vector database products (e.g., Pinecone, Milvus, Weaviate, Neo4j, etc.).
For example: If the customer needs GraphRAG + Cosine distance metric, but Neptune Analytics only supports L2Squared, then Neptune Analytics cannot be selected — consider Neo4j or other open-source graph databases with vector search capabilities.
Please consult the SSA team for specifics.
```

## Lessons Learned

### Do

- Ask all questions within a phase together, wait for user response before moving to the next phase
- Clearly state at the beginning of each phase "Now entering Phase X"
- Give brief feedback on each user response
- If a recommendation is determined at any phase, skip remaining phases
- Cite specific performance data and cost data when recommending
- If all AWS managed vector stores are eliminated, must explain why each service doesn't meet requirements, and suggest open-source or Marketplace alternatives, consult SSA team for specifics
- In the no-recommendation report, provide specific examples of the conflict scenario (e.g., "needs GraphRAG + Cosine, but Neptune only supports L2Squared") and recommend corresponding open-source alternatives
- Before the final conclusion, add: "The following recommendation is based on your inputs. If you have questions or specific concerns, please contact the SSA team"

### Don't

- Don't ask questions from multiple phases at once
- Don't guess data scale when user hasn't provided information
- Don't recommend service features the user hasn't mentioned needing
- Don't naturally transition from performance phase to cost phase — must explicitly open a new phase

### Common Failures

- User description is vague → Provide options to help choose
- User unsure about data scale → Use order-of-magnitude estimates (millions/tens of millions/hundreds of millions)
- User doesn't know index algorithm and distance metric → Default to HNSW+Cosine

### When to Ask the User

- Whether data needs persistence (eliminates ElastiCache)
- Whether there's a specific Agent framework binding
- Vector index algorithm and distance metric requirements (default HNSW+Cosine)
