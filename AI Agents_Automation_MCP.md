# AI Agents, Automation & MCP — Full Course Outline

**Language:** Python 3.12+
**Target:** Absolute beginner → able to design, build, secure, deploy and evaluate production agent systems
**Format:** Every module = concepts + working code you type and run + exercises + a checkpoint project
**Pace:** One module at a time. Tell me when you've finished one and we move to the next.

---

## How this course is structured

Seven parts, 35 modules. Each builds on the last. Nothing is skipped — including the boring, unglamorous parts (async Python, error handling, auth, evals) that are exactly what separates a demo from something that works.

| Part | Modules | What you'll be able to do at the end |
|---|---|---|
| 0. Foundations | 0–2 | Have a working dev environment and understand what an LLM actually is as a *component* |
| 1. Talking to models | 3–5 | Drive the Claude API confidently; get reliable structured data out of a model |
| 2. Tools & the agent loop | 6–9 | Build a real agent from scratch, with no framework, and understand every line |
| 3. MCP deep dive | 10–19 | Build, secure, deploy and publish MCP servers and clients — this is the biggest part |
| 4. Agent frameworks | 20–24 | Use PydanticAI, LangGraph and the Claude Agent SDK; know which to pick and why |
| 5. Knowledge & RAG | 25–27 | Give agents access to your own documents and data |
| 6. Automation & UI | 28–29 | Connect agents to real systems; build console and web interfaces |
| 7. Production | 30–34 | Evaluate, observe, secure, deploy, and ship a capstone |

---

## PART 0 — FOUNDATIONS

### Module 0 — Environment setup ← *you are here*
Python install and version check. `uv` as your package manager. Virtual environments (what they are and why every tutorial that skips them is setting you up for pain). Project structure. VS Code setup. Git basics. Getting an API key. `.env` files and why secrets never go in code. Your first Claude API call. Your first MCP smoke test. Troubleshooting table for the errors you *will* hit.
**Deliverable:** a working project folder that talks to Claude and runs an MCP server.

### Module 1 — Python you actually need for agents
Not a general Python course — the specific subset agent code depends on. Type hints and why they matter more here than in normal Python (the MCP SDK generates tool schemas from them). `async`/`await`, event loops, `asyncio.run`, `async with`, async iterators — the MCP SDK is async-first and you cannot avoid this. Decorators (how `@mcp.tool()` works under the hood). Pydantic v2: models, validation, `Field`, JSON schema generation. Context managers. Generators. Exceptions and custom exception classes. Environment variables. Logging (not `print`).
**Deliverable:** small async CLI tool that fetches and validates data concurrently.

### Module 2 — How LLMs work, for builders
Tokens and tokenization (and why your bill and your bugs both live here). Context windows and what "1M context" really means in practice. The completion loop. Temperature, top-p, and when to change them. System vs user vs assistant roles. Stop sequences. Streaming. Determinism and why you can't have it. Extended thinking / effort levels. Prompt caching. What a model fundamentally *cannot* do — and therefore what tools are for. Cost math: how to estimate what a feature costs before building it.
**Deliverable:** a token counter and cost estimator you'll reuse all course.

---

## PART 1 — TALKING TO MODELS

### Module 3 — The Claude API, properly
The `anthropic` Python SDK. `messages.create` in depth: every parameter. Multi-turn conversations and managing history yourself. Streaming responses. Handling `stop_reason`. System prompts. Images and PDFs as input. Error handling: rate limits, overloads, retries with exponential backoff. The Models API. Batch API for bulk jobs. Async client. Tracking token usage and cost per call.
**Also covered:** how to swap providers later (OpenAI, Gemini, local models via Ollama) so you're never locked in.
**Deliverable:** a robust, retrying, cost-tracked API wrapper module.

### Module 4 — Prompt engineering for agents
Different from chat prompting. System prompt architecture for agents. XML/structured delimiters. Few-shot examples. Chain-of-thought and when it's redundant. Role and persona setting. Handling ambiguity and refusals. Writing *tool descriptions* — the single most under-taught, highest-leverage skill in agent building. Prompt versioning and testing. Injection-resistant prompt design.
**Deliverable:** a prompt library with versioning.

### Module 5 — Structured outputs
Why free text breaks pipelines. JSON mode vs structured outputs vs tool-use-as-extraction. Pydantic models → JSON Schema → validated Python objects, end to end. Nested and optional fields. Enums and constrained choices. Handling validation failures and repair loops. Streaming partial structured data.
**Deliverable:** a document → typed data extraction pipeline.

---

## PART 2 — TOOLS & THE AGENT LOOP

### Module 6 — Tool use from first principles
What "function calling" actually is (spoiler: the model never runs anything). Defining tools by hand. The `tool_use` / `tool_result` message cycle. Writing the loop yourself, step by step. Parallel tool calls. Tool errors and how to report them back to the model. Forcing/disabling tool choice. Anthropic's server-side tools: web search, code execution, computer use.
**Deliverable:** a calculator + weather agent, loop written by hand, no framework.

### Module 7 — Your first real agent
Agent vs workflow vs chain — the distinction that matters. The ReAct pattern. Planning and decomposition. Multi-step tool use. Loop termination and max-iteration guards. Handling model mistakes. Self-correction. Cost and latency control. When an agent is the wrong answer and a plain script is better.
**Deliverable:** a research agent that plans, searches, reads, and writes a report.

### Module 8 — Console interfaces
When console is the right choice (most of the time, early). `rich` for formatted output, tables, live displays, spinners. `typer` for CLI arguments. Streaming output to a terminal. Interactive REPL loops. Human-in-the-loop approval prompts. Slash commands. Config files.
**Deliverable:** a polished terminal chat client for your agent.

### Module 9 — Memory and state
Short-term: conversation history management. Context window overflow strategies — truncation, summarization, compaction. Long-term: persisting sessions to disk/SQLite. Semantic memory with embeddings. Scratchpads and working memory. Per-user state. What to remember and what to forget.
**Deliverable:** an agent that remembers you across restarts.

---

## PART 3 — MCP DEEP DIVE *(the core of this course)*

### Module 10 — MCP: what and why
The N×M integration problem MCP solves. Host / client / server architecture. JSON-RPC 2.0 fundamentals. The three primitives: tools, resources, prompts. Transports: stdio vs Streamable HTTP. **The 2026-07-28 stateless rewrite**: no handshake, no sessions, `server/discover`, header-based routing, cacheable lists. Protocol version history and how to read older tutorials without being misled. SDK v1 vs v2 differences table.

### Module 11 — Your first MCP server
`MCPServer` (the class formerly known as `FastMCP`). `@mcp.tool()` and type-hint-driven schemas. Docstrings as tool descriptions. Running over stdio. Testing in-memory with the new first-class `Client` — no subprocess, no transport, fast tests. The MCP Inspector. Structured tool output. Error handling: `ToolError` vs `MCPError` vs everything else, and which ones the model actually sees.
**Deliverable:** a working, tested MCP server.

### Module 12 — Resources, prompts and richer tools
Resources and resource templates. RFC 6570 URI templates (real ones now, with `{+path}` and `{?query}`). Prompts as reusable, parameterized templates. Completions/autocomplete. Returning images, audio and icons. Pagination. Annotations and tool hints (read-only, destructive, idempotent) and why hosts care.
**Deliverable:** a server exposing a real data source with all three primitives.

### Module 13 — Inside the handler
The `Context` object. Dependency injection. The new `Resolve(fn)` pattern — filling parameters from your own code instead of from the model. Elicitation: asking the user mid-call. **Multi-Round-Trip Requests (MRTR)** — the replacement for server-initiated requests, and the single most likely thing to break in ported code. Progress reporting. Lifespan (now runs once, not per session). Subscriptions via `subscriptions/listen`. Deprecated: roots, sampling, protocol logging.
**Deliverable:** a tool that asks the user for confirmation before doing something destructive, working on both protocol eras.

### Module 14 — MCP clients in Python
The `Client` class. Four transport forms: in-memory, stdio subprocess, Streamable HTTP, legacy SSE. Discovering and calling tools programmatically. Client callbacks. Connecting to multiple servers at once (session groups). Response caching with `ttlMs` / `cacheScope`. Client-side validation and what happens when a server misbehaves. Protocol version negotiation and legacy fallback.
**Deliverable:** a client that aggregates tools from three servers.

### Module 15 — Wiring MCP into agents and hosts
Connecting your server to Claude Desktop, Claude Code, and other hosts. Config files and their gotchas. **Building your own host**: feeding MCP tools into the Claude API tool loop from Module 6 — this is where Parts 2 and 3 join up. Tool namespacing and collision handling. Filtering which tools an agent may see. Dynamic tool discovery.
**Deliverable:** your Module 7 agent, now powered entirely by MCP servers.

### Module 16 — Remote MCP servers
Streamable HTTP in depth. Running with `run(transport="streamable-http")`. Mounting into an existing ASGI/FastAPI app. Serving both protocol eras from one endpoint. Deploying behind a load balancer (now genuinely possible — this is what stateless bought us). Host allowlists. `RequestStateSecurity` keys for multi-replica MRTR. Notifications across replicas with a shared `SubscriptionBus`. Health checks, timeouts, graceful shutdown.
**Deliverable:** a publicly deployed MCP server.

### Module 17 — MCP security and authorization
Threat model: tool poisoning, prompt injection through tool results, confused-deputy, rug-pull servers, token theft. OAuth 2.1 for MCP. **CIMD (Client ID Metadata Documents)** — now the preferred path, replacing deprecated Dynamic Client Registration. RFC 9207 issuer validation. Issuer-bound credentials. Identity assertion (SEP-990). Enterprise-Managed Authorization. Scopes and least privilege. Input validation, sandboxing, allowlists, rate limiting. Auditing tool calls.
**Deliverable:** an OAuth-protected MCP server + a security review checklist.

### Module 18 — Advanced MCP
The low-level `Server` (fully rebuilt in v2: constructor handlers, typed params in / full results out, no ambient context var). Custom vendor-namespaced methods. Middleware. The extensions framework and reverse-DNS capability bundles. **MCP Apps** — servers shipping sandboxed interactive HTML UIs. The Tasks extension for long-running work. OpenTelemetry tracing (on by default in v2).
**Deliverable:** a server with middleware, a custom method, and traces.

### Module 19 — Shipping and sharing servers
Packaging for PyPI and `uvx`. The MCP Registry. Versioning and the 12-month deprecation policy. The conformance suite. Documentation that hosts and models can both read. Testing strategy for servers. Contributing back: SEPs and working groups.
**Deliverable:** a published, installable MCP server.

---

## PART 4 — AGENT FRAMEWORKS

### Module 20 — The landscape, and how to choose
Honest survey: LangGraph, PydanticAI, Claude Agent SDK, OpenAI Agents SDK, CrewAI, Google ADK, Microsoft Agent Framework, smolagents, Agno. What each optimizes for. The three axes that actually decide it: workflow complexity, vendor commitment, tolerance for abstraction. When to use no framework at all. Migration cost and lock-in.

### Module 21 — PydanticAI
Typed agents. Dependency injection. Structured results. `TestModel` and `FunctionModel` for testing without spending money. Streaming. MCP integration. Multi-provider support.
**Deliverable:** your agent rebuilt, fully typed and unit tested.

### Module 22 — LangGraph
State graphs and why "the workflow is the product". Nodes, edges, conditional routing. Checkpointing and durable execution. Human-in-the-loop interrupts. Time travel and replay. Subgraphs. Streaming. Persistence backends. LangSmith tracing.
**Deliverable:** a multi-step workflow that survives a process restart and pauses for human approval.

### Module 23 — Claude Agent SDK
The distinction from the `anthropic` package, made concrete. `query()` and `ClaudeAgentOptions`. `ClaudeSDKClient` for multi-turn sessions. Built-in tools (file ops, bash, web). Permissions and `allowed_tools`. Hooks for blocking dangerous actions. Subagents. In-process MCP servers via `create_sdk_mcp_server`. Sessions and resumption. Cost tracking.
**Deliverable:** an autonomous coding/ops agent with guardrails.

### Module 24 — Multi-agent systems
When multiple agents genuinely help (and the much more common case where they don't). Orchestrator–worker. Handoffs. Parallel fan-out/fan-in. Debate and critic patterns. Shared state and message passing. The A2A protocol and how it relates to MCP. Failure modes: infinite loops, cost explosions, diffuse responsibility.
**Deliverable:** a multi-agent pipeline with a supervisor.

---

## PART 5 — KNOWLEDGE & RAG

### Module 25 — Embeddings and vector search
What embeddings are, geometrically. Choosing a model. Chunking strategies and why naive chunking ruins retrieval. Vector databases: Chroma, Qdrant, pgvector — and when a plain file is enough. Similarity metrics. Indexing and metadata filtering.

### Module 26 — RAG that works
The naive RAG pipeline and its failure modes. Hybrid search (BM25 + vector). Reranking. Query rewriting and expansion. Contextual retrieval. Citations and grounding. Evaluating retrieval separately from generation. **Agentic RAG** — letting the agent decide what and when to retrieve.

### Module 27 — Knowledge as an MCP server
Wrapping your RAG pipeline as MCP tools and resources. Designing retrieval tools a model can use well. Caching. Access control per user.
**Deliverable:** a document-Q&A MCP server any host can plug into.

---

## PART 6 — AUTOMATION & INTERFACES

### Module 28 — Automating real systems
Integrating third-party APIs (auth patterns, pagination, rate limits). Webhooks in and out. Scheduling: cron, APScheduler, Temporal. Event-driven agents and queues. File and email automation. Browser automation (Playwright) and when it beats an API. Idempotency, retries, dead-letter queues. Where n8n/Zapier-style tools fit and where code wins.
**Deliverable:** an unattended agent triggered by a real-world event.

### Module 29 — Building user interfaces
Choosing: console vs web vs API vs chat-app-embedded. Streamlit for internal tools. Chainlit for chat UIs. FastAPI + server-sent events for custom frontends. Streaming to a browser. Rendering tool calls so users can see what the agent did. Approval UIs. Auth and multi-user.
**Deliverable:** a web UI over your agent, with streaming and approvals.

---

## PART 7 — PRODUCTION

### Module 30 — Evaluating agents
Why "it looked good in testing" fails. Building eval datasets. Deterministic assertions vs LLM-as-judge. Trajectory evaluation vs final-answer evaluation. Regression suites. CI integration. Measuring tool-selection accuracy. Red-teaming your own agent.

### Module 31 — Observability and cost
Structured logging for agents. OpenTelemetry and distributed traces across agent → MCP server → API. LangFuse / LangSmith / Phoenix. Tracking cost per request, per user, per feature. Latency budgets. Debugging non-deterministic failures. Alerting.

### Module 32 — Safety and guardrails
Prompt injection — direct and indirect — with working attacks and defenses. Untrusted tool output. The lethal trifecta (private data + untrusted content + external communication). Permission systems and human approval gates. Output filtering. PII handling. Sandboxing execution. Rate and spend limits. Failing safe.

### Module 33 — Deployment
Packaging with Docker. Environment and secret management. Deploying agents: containers, serverless, long-running workers. Scaling stateless MCP servers. Health checks and graceful degradation. CI/CD. Versioning agents and prompts. Rollback strategy.

### Module 34 — Capstone
You design and build a complete system: custom MCP servers, an agent that orchestrates them, a UI, auth, evals, observability, and a deployment. I review it in detail and we iterate until it's genuinely production-grade.

---

## Ground rules

- **Type the code.** Don't copy-paste. You learn the API surface through your fingers.
- **Break things deliberately.** Each module has "now break it" exercises. Debugging is the skill.
- **Tell me when you're stuck** — with the actual error text. Don't skip ahead.
- **Tell me when a module is done** and I'll deliver the next one.
- If something in a module is unclear, ask before moving on. Later modules assume earlier ones.

Total realistic time: 3–6 months at a few hours a week. Faster if you're full-time.
