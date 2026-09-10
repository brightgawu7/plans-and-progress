# RAG: Zero to Expert — Full Course Syllabus

**Language:** Python 3.12+
**Format:** One module per session. Every module = concepts → code you type and run → an exercise → a checkpoint.
**Rule:** You tell me when a module is done, and we move to the next one. If something breaks or doesn't click, we stop and fix it before moving on.

---

## How this course works

**Build-then-abstract.** For every major idea you first implement it yourself in plain Python (numpy, requests, sqlite) so you understand what's actually happening. *Then* we swap in the production library (LangChain / LlamaIndex / Qdrant) and you'll know exactly what it's doing under the hood. This is the difference between someone who "uses RAG" and someone who can debug it at 2am.

**Every module ships something runnable.** No slideware. By the end you'll have a repo with ~30 working projects.

**Where the UI shows up:**
- Modules 0–15: **console / scripts / Jupyter only.** No UI needed, and I'll say so explicitly.
- Module 16: **first real UI** — Streamlit chat app, then a FastAPI backend.
- Module 26 onward: production API, Docker, deployment.
I will always tell you at the start of a module whether you need a UI, a console script, or a notebook.

**What you need before Module 0:**
- A computer (Windows / Mac / Linux — all fine)
- Basic Python: variables, functions, loops, lists/dicts, importing a library. If you can write a for-loop, you're ready. If not, tell me and I'll insert a Module -1.
- **No** machine learning background required. No maths beyond "what is an average."
- Budget: roughly $10–30 of API credits across the whole course. There's a **fully free local path** (Ollama + open models) for every module — I'll give both.

**Estimated time:** 60–100 hours total, depending on how deep you go on exercises. Roughly 2–4 hours per module.

---

# PART 0 — FOUNDATIONS
*You cannot debug RAG if you don't understand what an LLM and an embedding actually are.*

### Module 0 — Environment setup
Python 3.12, virtual environments (`uv` / `venv`), project layout, `.env` and secret handling, Git basics, Jupyter, VS Code setup. Setting up an LLM provider (OpenAI / Anthropic / Google) **and** a free local option (Ollama). Your first API call, streaming, error handling, cost logging.
**Ship:** `hello_llm.py` that streams a response and prints token cost. **Console only.**

### Module 1 — LLM fundamentals for RAG
Tokens and tokenization (hands-on with `tiktoken`), context windows, temperature/top-p, system vs user vs assistant roles, structured output (JSON mode / Pydantic), tool calling, prompt caching. **Why hallucination happens** — mechanically, not hand-wavily. The knowledge-cutoff problem. Cost and latency maths.
**The big decision tree:** when to use RAG vs fine-tuning vs long-context vs tool-calling vs plain prompting — and when RAG is the *wrong* answer.
**Ship:** a script that proves the model hallucinates on private data, then proves it answers correctly when you paste the data in. That gap *is* RAG.

### Module 2 — Text representation and embeddings, from scratch
Bag-of-words → TF-IDF → word2vec → transformer embeddings. Vectors, dimensions, cosine similarity vs dot product vs Euclidean, normalization, mean pooling vs CLS pooling. Why "king − man + woman ≈ queen" and why that intuition partly misleads you. Matryoshka embeddings and truncatable dimensions.
**Ship:** semantic search over 200 sentences in pure numpy, no vector DB, ~60 lines. Then visualise the embedding space in 2D.

---

# PART 1 — CORE RAG
*A working pipeline, then every component broken open.*

### Module 3 — Your first complete RAG pipeline
The five stages: **load → chunk → embed → retrieve → generate.** Built end-to-end in one file with no frameworks. Then the same thing in LangChain and in LlamaIndex, side by side, so you can see what the framework hides. Citations, and refusing to answer when nothing relevant is retrieved.
**Ship:** ask questions about a PDF you supply, from the terminal, with sources.

### Module 4 — Document ingestion and parsing (the unglamorous 60% of the job)
PDFs (PyMuPDF, pdfplumber, Docling), DOCX, PPTX, HTML, Markdown, CSV/Excel, email, code. Scanned documents and OCR. Table extraction. Multi-column layouts. Headers/footers/boilerplate stripping. Encoding hell. Deduplication. Metadata extraction (title, section, page, author, date). Preserving document hierarchy. Web scraping and sitemap crawling.
**Ship:** an ingestion module that takes a messy folder of mixed formats and outputs clean, structured, metadata-rich documents.

### Module 5 — Chunking deep-dive
Fixed-size, token-based, recursive character, sentence-aware, structural/Markdown-aware, semantic chunking (embedding-similarity breakpoints), parent-child, sentence-window, late chunking, and **Contextual Retrieval** (prepending an LLM-written context blurb to each chunk — a technique that measurably cuts retrieval failures). Overlap strategy. Chunk size vs recall vs precision, measured empirically on your own data rather than guessed.
**Ship:** a chunking lab that runs 6 strategies over the same corpus and scores them against each other.

### Module 6 — Embedding models in depth
Proprietary (OpenAI text-embedding-3, Cohere embed-v4, Voyage, Gemini) vs open weights (Qwen3-Embedding, BGE, E5, Nomic, KaLM, all-MiniLM). How to read MTEB/MMTEB — and why the leaderboard leader is usually *not* your best pick. Task instruction prefixes. Dimension vs quality vs storage cost. Symmetric vs asymmetric search. Running models locally with `sentence-transformers` and Text Embeddings Inference. Batching, rate limits, embedding cost maths. Embedding versioning (and why a model upgrade means a full re-index).
**Ship:** a bake-off harness that benchmarks 4 embedding models on *your* corpus and picks a winner on evidence.

### Module 7 — Vector databases and ANN search
**Theory:** exact kNN vs approximate; HNSW (graphs, `M`, `ef_construction`, `ef_search`); IVF; product/scalar/binary quantization; the recall-latency-memory triangle.
**Practice:** Chroma (prototyping) → Qdrant (production default) → pgvector (when you already run Postgres). Collections, upserts, payload/metadata filtering, snapshots, hybrid-ready schemas, index tuning. Choosing between them honestly, including "you don't need one yet."
**Ship:** the same corpus indexed in all three, benchmarked on recall and p95 latency.

### Module 8 — Retrieval and prompting basics
top-k selection, similarity thresholds, MMR for diversity, deduplicating near-identical chunks, context assembly and ordering, the "lost in the middle" effect, prompt templates for grounded answering, forcing citations, controlled refusal, streaming the answer.
**Ship:** a retriever+generator you can configure from a YAML file, so later modules can A/B test it.

---

# PART 2 — MAKING RETRIEVAL ACTUALLY GOOD
*Naive RAG fails ~half the time. This part is where the accuracy comes from.*

### Module 9 — Keyword and hybrid search
Why pure vector search misses exact terms, product codes, names and acronyms. BM25 implemented from scratch, then via `rank_bm25` / Elasticsearch / Qdrant sparse vectors. Learned sparse retrieval (SPLADE, miniCOIL). **Reciprocal Rank Fusion** and weighted score fusion, plus score normalization pitfalls.
**Ship:** hybrid retriever with tunable fusion, measured against vector-only and BM25-only.

### Module 10 — Reranking
Bi-encoders vs cross-encoders, and why a reranker is usually the single highest-ROI upgrade. Open rerankers (bge-reranker-v2, Jina, Qwen3-Reranker) vs API (Cohere Rerank). LLM-as-reranker. **Late interaction / ColBERT / MaxSim** and multi-vector storage, plus MUVERA-style compression. Retrieve-100-rerank-to-5 patterns. Latency and cost budgeting.
**Ship:** reranking layer with a measured before/after on NDCG@5.

### Module 11 — Query understanding and transformation
Query rewriting, **HyDE** (hypothetical document embeddings), multi-query expansion, **RAG-Fusion**, query decomposition for multi-hop questions, step-back prompting, self-query (LLM extracts metadata filters from natural language), acronym/synonym expansion, and **conversational query rewriting** — turning "what about the second one?" into a standalone query. Query routing and complexity classification (the basis of Adaptive RAG).
**Ship:** a query pipeline that classifies and routes each question to the right strategy.

### Module 12 — Advanced indexing architectures
Parent-document retrieval, auto-merging retrieval, **RAPTOR** (recursive clustering + summarization into a tree), summary indexes, multi-representation indexing, hypothetical-question indexing, and multi-index routing across separate corpora.
**Ship:** RAPTOR built from scratch over a long report, compared against flat chunking.

### Module 13 — Metadata, filtering, and access control
Metadata schema design, pre- vs post-filtering (and why post-filtering silently destroys recall), temporal filtering and freshness decay, per-user permission filtering, row-level security, multi-tenancy patterns (shared collection vs per-tenant collection vs per-tenant DB), and the leakage bugs that get companies sued.
**Ship:** a permission-aware retriever with tests proving user A can never see user B's documents.

### Module 14 — Context compression and selection
Contextual compression, extractive filtering, sentence-level relevance pruning, LLMLingua-style prompt compression, redundancy removal, reordering for position bias, and hard token-budget management with graceful degradation.
**Ship:** a compressor that cuts context tokens ~50% with no measurable answer-quality loss.

---

# PART 3 — GENERATION, INTERFACE, AND UX

### Module 15 — Grounded generation and conversation
Prompt architecture for faithful answers, inline citation with span-level attribution, structured/JSON answers, calibrated refusals ("I don't know" as a feature), self-verification passes, hallucination guardrails, answer formatting for different audiences, multi-turn memory, and conversational state without blowing the context window.
**Ship:** a conversational RAG engine with verified citations.

### Module 16 — Building the interface ⭐ *first UI module*
**Streamlit** chat app: streaming responses, expandable source panels, thumbs up/down feedback capture, file upload, settings sidebar. Then the same engine behind a **FastAPI** backend with SSE streaming, plus a proper CLI with `typer`. Which one to use when. (Optional: a minimal React front-end if you want it.)
**Ship:** a shareable app someone non-technical can actually use.

---

# PART 4 — EVALUATION
*If you can't measure it, you're guessing. Systematic eval from day one is now standard practice, not a nice-to-have.*

### Module 17 — Retrieval evaluation
Building a golden dataset (manual + LLM-synthesized questions with human review), hit rate, recall@k, precision@k, MRR, **NDCG**, and how to interpret each. Hard-negative discovery. Error taxonomy: retrieval miss vs ranking failure vs chunking failure vs generation failure.
**Ship:** an eval harness + your first honest scorecard. Expect it to be worse than you hoped.

### Module 18 — End-to-end evaluation and regression testing
**RAGAS** (faithfulness, answer relevancy, context precision, context recall), **DeepEval** (pytest-style, CI-ready, custom G-Eval metrics), Phoenix/LangSmith/Langfuse. Designing trustworthy LLM-as-judge prompts, judge bias and how to calibrate against human labels, pairwise A/B evaluation, statistical significance on small eval sets, cost of evaluation, and **eval gates in CI** so a bad change can't ship.
**Ship:** GitHub Actions that fail the build when faithfulness drops.

---

# PART 5 — ADVANCED ARCHITECTURES

### Module 19 — Agentic RAG
Retrieval as a tool rather than a fixed step. ReAct loops, **LangGraph** state machines (nodes, edges, conditional routing, checkpointing), **Self-RAG** (reflection tokens: should I retrieve? is this relevant? is my answer supported?), **CRAG** (corrective retrieval with web-search fallback), iterative multi-hop retrieval, planner/executor patterns, sub-agents, and **Adaptive RAG** routing simple queries to the cheap path and hard ones to the expensive path. Loop limits, cost ceilings, and timeout safety.
**Ship:** an agentic RAG system that decides its own retrieval strategy per query.

### Module 20 — GraphRAG and knowledge graphs
When vector search structurally cannot answer a question ("how are X and Y connected across 400 documents?"). Entity and relation extraction, graph construction, Neo4j + Cypher basics, Microsoft GraphRAG's local vs global search, community detection and hierarchical summarization, hybrid graph+vector retrieval, and an honest cost/benefit analysis — GraphRAG is expensive and often unnecessary.
**Ship:** a knowledge graph over a corpus plus a hybrid graph/vector retriever.

### Module 21 — RAG over structured data
Text-to-SQL (schema linking, few-shot examples, semantic layers, validation and safe execution, self-correction on errors), RAG over dataframes and APIs, and hybrid systems that route between SQL, vector search, and graph depending on the question.
**Ship:** an assistant that answers both "what does the policy say about refunds" and "how many refunds did we issue in July."

### Module 22 — Multimodal RAG
Three architectures: **caption-and-index**, **unified multimodal embeddings** (Cohere Embed 4, voyage-multimodal), and **page-as-image late interaction** (ColPali / ColQwen), which skips OCR entirely and is often the answer for chart- and table-heavy PDFs. VLM-based answer generation over retrieved page images. Failure modes: caption drift, modality leakage, dominant-modality bias in fusion. Also: audio/video RAG via transcription + timestamped chunks.
**Ship:** a RAG system that correctly answers a question whose answer only exists inside a chart image.

### Module 23 — Long context, caching, and memory
Million-token context windows vs RAG: the real cost, latency, and recall trade-offs (long context degrades on retrieval-in-the-middle, and costs orders of magnitude more at scale). Hybrid "retrieve widely, reason over long context" designs. Prompt caching. Semantic caching of answers. Agent memory systems: episodic, semantic, working memory, and how memory differs from retrieval.
**Ship:** a semantic cache that cuts your per-query cost substantially.

### Module 24 — Fine-tuning for RAG
Fine-tuning embedding models on your domain with hard-negative mining and contrastive loss (Matryoshka + MultipleNegativesRanking), fine-tuning a reranker, **RAFT** (training the generator to ignore distractor chunks), and distilling a big judge into a small cheap one. Crucially: when *not* to do any of this.
**Ship:** a domain-tuned embedding model that beats the off-the-shelf model on your eval set.

---

# PART 6 — PRODUCTION
*Everything above is a prototype until this part.*

### Module 25 — Production system design
Offline (ingestion) vs online (query) paths. Ingestion pipelines with queues and workers. Incremental sync and change-data-capture — detecting new/updated/deleted source documents. Idempotent upserts and document versioning. Index versioning and zero-downtime re-indexing (blue/green). Backfills. Schema migration. Handling a 10-million-document corpus. Full reference architecture diagram you'll implement.

### Module 26 — The production service
FastAPI done properly: async everything, dependency injection, connection pooling, request batching, SSE streaming, timeouts, retries with backoff, circuit breakers, graceful degradation when the vector DB is down, rate limiting, config management, structured logging, health checks. Dockerfile + docker-compose with Qdrant/Postgres/Redis.
**Ship:** a containerised API you could actually put in front of users.

### Module 27 — Latency, throughput, and cost engineering
Profiling the full latency budget (embed → search → rerank → generate) and attacking each. Caching layers. Quantization and dimension reduction. Async parallel retrieval. Model routing (cheap model for easy queries). Batch vs realtime embedding. GPU vs CPU inference, vLLM and TEI for self-hosting. Building a cost-per-query model and a monthly forecast.
**Ship:** measured p50/p95/p99 before and after optimisation.

### Module 28 — Observability
OpenTelemetry tracing across the whole pipeline. Langfuse / Phoenix / LangSmith. Logging every retrieved chunk and score so failures are reproducible. Capturing user feedback. Online (production) eval sampling. Dashboards for retrieval health, hallucination rate, latency, cost. Drift detection: when your corpus or your users' questions change under you. Alerting.
**Ship:** a dashboard where you can click any bad answer and see exactly why it happened.

### Module 29 — Security, privacy, and governance
**Indirect prompt injection through retrieved documents** — the #1 RAG-specific vulnerability — plus data poisoning of the index. Sanitization and content isolation. PII detection and redaction at ingestion. Tenant isolation testing. Secrets management. Output guardrails. Audit trails and data lineage. Right-to-be-forgotten / deletion propagation. Compliance overview (GDPR, EU AI Act, HIPAA-shaped constraints) and self-hosting for sensitive data. Red-teaming your own system.
**Ship:** a red-team suite that attacks your RAG app, and the fixes.

### Module 30 — MLOps, CI/CD, and operating the system
Repo structure and testing strategy for LLM apps (unit, integration, eval, smoke). CI with eval gates. Canary and shadow deployments. A/B testing retrieval configurations against real users. Feature flags for retrieval strategies. Runbooks and incident response for "the bot is confidently wrong." Data freshness SLOs. On-call reality.

### Module 31 — Deployment
Deploying to a VPS, to AWS/GCP/Azure, serverless vs always-on, Kubernetes essentials, managed vector DB vs self-hosted, self-hosting open models with vLLM/Ollama, air-gapped/on-prem deployments, scaling and autoscaling, backup and disaster recovery, and total cost of ownership at 10 / 1k / 100k queries per day.
**Ship:** your app live on the internet with a URL.

---

# PART 7 — MASTERY

### Module 32 — Capstone I: Enterprise knowledge assistant
Multi-format ingestion, hybrid retrieval + reranking, permission-aware multi-tenancy, conversational memory, citations, full eval suite, observability, Docker, deployed, with a written architecture doc and cost model. This is the portfolio piece.

### Module 33 — Capstone II: your choice
Pick one: (a) multimodal financial-report analyst using ColPali, (b) GraphRAG research assistant over a paper corpus, (c) agentic customer-support bot with tool use and escalation, (d) a domain system for your own job/business.

### Module 34 — Staying expert
How to read a RAG paper in 15 minutes. Key benchmarks (BEIR, MTEB, ViDoRe, CRAG, RAGBench) and how to not be fooled by them. Evaluating a new technique against your own baseline before adopting it. Where the field is heading: agentic retrieval, memory-centric architectures, retrieval as reasoning, context governance. Reading list, communities, and RAG system-design interview prep.

---

## Appendix A — Glossary
Maintained across the course: every term (ANN, HNSW, MaxSim, NDCG, RRF, MMR, cross-encoder, late interaction, hard negative, etc.) defined the first time it appears, collected here.

## Appendix B — The repo
By the end you'll have:
```
rag-course/
├── m00_setup/ ... m34_mastery/
├── shared/            # reusable library you build across the course
├── data/              # corpora used throughout
├── evals/             # golden datasets and eval harness
├── docker/
└── docs/              # your own notes + architecture decisions
```

## Appendix C — Things most tutorials skip, that this course covers
Ingestion of genuinely messy documents · incremental re-indexing · embedding model migration · permission filtering · prompt injection via documents · eval before optimisation · cost modelling · when RAG is the wrong tool · graceful degradation · the fact that most "advanced RAG" techniques don't help your specific corpus and you need evidence to know which do.
