# RAG 工程师 · 参考来源索引

> 本文件从 SKILL.md 内容中提取所有引用来源，建立可追溯的参考文献体系。
> 创建时间：2026-06-06

## 一、学术论文

| 论文 | 作者 | 发表 | 引用位置 | 核心贡献 |
|------|------|------|---------|---------|
| Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks | Patrick Lewis et al. (FAIR/UCL) | NeurIPS 2020, arXiv: 2005.11401 | 核心原理 | 定义 RAG 范式：参数化记忆 + 非参数化记忆 |
| A Survey on RAG Meets LLMs | Gao et al. | 2023, arXiv:2312.10997 | 检索策略 | RAG 技术全面综述 |
| From Local to Global: A Graph RAG Approach | Microsoft Research | 2024 | GraphRAG 章节 | 知识图谱 + RAG 的融合方案 |
| ColBERT: Efficient and Effective Passage Search | Omar Khattab, Matei Zaharia | SIGIR 2020 | 检索策略 | Late-interaction 检索范式 |
| ColPali: Efficient Document Retrieval with Vision Language Models | Faysse et al. | 2024 | 检索策略 | 多模态文档检索 |

## 二、行业报告与博客

| 来源 | 时间 | 引用位置 | 核心内容 |
|------|------|---------|---------|
| Anthropic Contextual Retrieval | 2024.09 | 检索策略 | BM25+向量混合，检索失败率降低 49% |
| OpenAI Embedding Guide | 2024 | 分块策略 | 分块最佳实践 |
| Douwe Kiela, AI Engineer Summit 演讲 | 2025 | 核心原理 | "系统 > 模型"，LLM 只占 RAG 系统 20% |
| Cohere Rerank v3.5 | 2025 | Reranking | Cross-Encoder 重排序最新模型 |
| Jina Reranker v2 | 2025 | Reranking | 多语言重排序模型 |
| Jina AI Late Chunking | 2024 | 分块策略 | 先 embedding 后切分，保留跨块上下文 |

## 三、框架与工具

| 工具 | 维护者 | 引用位置 | 核心能力 |
|------|--------|---------|---------|
| LlamaIndex | Jerry Liu | 分块/检索/索引 | 数据接入与索引优化 |
| LangChain | Harrison Chase | 分块/编排 | LLM 应用编排框架 |
| Microsoft GraphRAG | Microsoft Research | GraphRAG | 知识图谱构建与检索 |
| Neo4j | Neo4j Inc. | GraphRAG | 图数据库 |

## 四、Embedding 模型

| 模型 | 维度 | 来源 | 适用场景 |
|------|------|------|---------|
| text-embedding-3-large | 3072 | OpenAI | 通用英文，商用最强之一 |
| text-embedding-3-small | 1536 | OpenAI | 快速原型，性价比高 |
| bge-large-zh | 1024 | BAAI | 中文检索 |
| e5-large-v2 | 1024 | Microsoft | 通用多语言 |
| Cohere Embed v3 | 1024 | Cohere | 企业级，支持 search_document/search_query |

## 五、待补充（2025-2026 新进展）

- [ ] ColBERT v2 / ColPali 的 late-interaction 检索实际部署案例
- [ ] Agentic RAG 的生产环境最佳实践（当前仅有理论框架）
- [ ] Contextual Retrieval 的 BM25+向量混合的具体调参指南
- [ ] 多模态 RAG（图文混合检索）的端到端方案
