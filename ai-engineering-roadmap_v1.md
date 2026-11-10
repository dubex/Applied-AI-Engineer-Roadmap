# Applied AI / AI Engineering Roadmap for a Staff Software Engineer (with FDE track)

*Prepared 10 Nov 2026; **revised for a model-agnostic, open-weights-first approach.** About 37 weeks at 8–10 h/week (\~333 hours, plus optional electives), ending in a portfolio-grade capstone. A QA audit against the original brief and against real Staff/Principal and FDE job requirements is in Appendix B.*

## How to read this document

- **Verified links.** Every URL below was returned by a search or appeared in a page I retrieved during this session. Where I could not confirm a link, the row says **link not verified** and gives the exact title and platform to search for.
- **What I could not check.** I can't log in to your accounts. I can't confirm that a given O'Reilly title, Udemy course or Medium story is included in your plan, or that it isn't paywalled. Udemy retitles and revises courses often. Where the live title differs from the one I knew, I note both.
- **Platform labels.** O'Reilly, Udemy, Educative, Medium or Free.
- **⭐ = my single "start here" pick for that topic.**
- **Priority scale for a Staff Engineer.** **Must** (you'll be asked about it or you'll own it), **Should** (you need working fluency), **Nice** (go deep only if your work demands it).
- **2024+ preference.** Older items are flagged *(classic)*.

### Opinionated framing (how I'd steer you)

1. **Your edge is systems engineering, not model knowledge.** Reliability, evaluation, cost, security and team enablement are where Staff engineers differentiate. Learn the model only deeply enough to reason about failure modes.
2. **Evals come before cleverness.** Nearly every serious practitioner source below converges on this. Build the eval harness before you tune anything.
3. **Start with direct API calls and simple workflows.** Anthropic's guidance is that the most successful agent implementations use simple, composable patterns rather than heavy frameworks. Add frameworks only when you can name the problem they solve.
4. **"Harness engineering" is the 2026 framing for agent reliability.** The scaffolding, tools, context and feedback loops around the model decide whether it behaves. Treat it as a first-class discipline.
5. **Use a coding agent from day one (any one; the workflow concepts transfer).** It's both a topic and the fastest way to learn the rest. Phase 0 exists for this.
6. **Be model-agnostic by construction and open-weights-first by default.** Evals, not brand loyalty, decide which model runs where. See the policy section directly below.

---

## Revision: model-agnostic, open-weights-first (audit and policy)

**Verdict on v1.** The *concepts* in v1 (evals, RAG, context engineering, MCP, harnesses, LLMOps) were already model-agnostic. The *defaults and tooling* were not: many "start here" picks were Anthropic/OpenAI-authored, several tools were vendor-specific (Claude Code, Claude Agent SDK, vendor guides), and open-weights work (serving, fine-tuning) was an optional specialization rated "Should". For your stated preference that was backwards. What changed in this revision:

1. Open-weights literacy, local runtimes, hardware sizing, quantization, chat-template/tool-call differences, provider abstraction and cross-model portability testing are now explicit **Must** topics (new domain O in the landscape table).
2. Phase 1 starts on local open models; Phase 3 agents must run on at least three models; Phase 5 is now **core** open-weights engineering (fine-tune + quantize + serve); the capstone must run with an open-weights default and switch models by configuration only.
3. New neutral/open resources were added and vendor-tied rows are labeled.

### Vendor-tie audit of v1 resources

| v1 resource family | Tie | Verdict | Neutral / open path |
| --- | --- | --- | --- |
| Anthropic posts: context engineering, building effective agents, agent evals, harnesses | Vendor-authored; examples use Claude, ideas are general | **Keep as concepts** | Pair with Applied LLMs, Hamel Husain, Eugene Yan, and the Hugging Face Agents Course (added) |
| Claude Code best practices, Anthropic Academy skills/subagents/Claude Code courses | Tool-specific | **Optional**; learn the workflow ideas, not the product | Open-source coding agents that accept local models (Phase 0 row added) |
| MCP spec, MCP Academy course, A2A | MCP/A2A are open protocols; one course is vendor-hosted | **Keep** | The spec and changelog are the neutral source |
| Claude Agent SDK | Claude-oriented library | **Optional** | LangGraph, smolagents, LlamaIndex (Phase 3 row added) |
| OpenAI Agents SDK | OpenAI-originated; its README describes multi-provider support | **Keep with a portability test** | Run it against a local open model before trusting it |
| OpenAI/Anthropic prompt and function-calling guides | Vendor APIs | **Keep for concepts** | Local runtimes expose OpenAI-style tool calling, JSON mode and grammars (see portability rules) |
| Contextual Retrieval (Anthropic) | Post uses Claude to write chunk context | **Keep; technique is general** | Use a local LLM for the context-generation step and compare |
| Prompt caching examples | Vendor feature | **Keep; abstract it** | Self-hosted servers such as vLLM offer prefix caching |
| Phase 5 as optional | Process flaw | **Fixed** | Now core |

### Model tiers (use families as examples, not rankings)

Open-model leaderboards change monthly and the roundups I found (dated 30 Oct to Nov 2026) **disagree with each other**, so the roadmap teaches a *selection method* rather than a winner. Names below are examples drawn from those roundups; sizes are approximate quantized footprints they report. Confirm on each model card.

| Tier | Typical hardware | Example families named in recent roundups | Use it for |
| --- | --- | --- | --- |
| **L: laptop** | 16–24 GB RAM/VRAM | gpt-oss-20b (\~14 GB), Gemma 4 small/mid sizes, Qwen 3.5-class \~9B | Daily dev loops, unit evals, RAG, tool-calling basics |
| **S: single GPU/workstation** | 24–80 GB | Qwen3.x-27B (\~18 GB), Gemma 4 31B, gpt-oss-120b (\~65 GB), Mistral Small 4 | Agent projects, fine-tuning targets, serving benchmarks |
| **H: large open weights** | Multi-GPU cluster, or hosted via OpenRouter | DeepSeek V4/V4.1, Kimi K3, GLM-5.x, Qwen3.8 flagship, Mistral Large | Certifying agent behavior near frontier quality; usually reached through OpenRouter with a pinned provider |
| **F: closed frontier "ceiling"** | API (via OpenRouter or direct) | One or two only | Reference quality, judge cross-checks, one "ceiling run" per project to separate harness bugs from model limits |

Licenses differ (roundups report Apache 2.0, MIT and custom terms such as the Kimi K3 license, the Llama community license and Mistral's license). Read the license before you build on a model.

### Where open weights can hurt learning, and the mitigation

1. **Agent loops on small local models fail more often** (practitioner guides say so). That makes it hard to tell a harness bug from a model limit. *Mitigation:* build agent logic on Tier S/H, use Tier L for unit tests and non-agentic phases, and do one Tier F "ceiling run".
2. **LLM-as-judge quality.** *Mitigation:* calibrate any judge against human labels, use the largest open model you can serve, cross-check with a closed judge sparingly, never let a model judge itself.
3. **Coding agents.** Practitioner guides report frontier closed models still lead on complex multi-file work. *Mitigation:* use whichever coding tool maximizes your productivity, but keep every *learning project* model-agnostic.
4. **No GPU.** Use rented GPUs or hosted open-weights endpoints; the code is identical behind an OpenAI-compatible API.
5. **Distilling from closed models.** Check that model's terms of use before using its outputs as training data.
6. **Exception log.** Any closed-model dependency is recorded with a reason and has a portability test.

### Portability rules (the engineering contract for every project)

1. **One `ModelClient` interface** speaking OpenAI-compatible HTTP (llama.cpp's `llama-server`, vLLM, Ollama and gateways such as LiteLLM all expose it), with an adapter layer for provider-specific features.
2. **No model names in code.** Model IDs, endpoints and sampling parameters are configuration.
3. **A `MODEL_CARD.md` per model you use:** license, context length, total vs active parameters, quantization, hardware, chat template, tool-call format, known quirks.
4. **Evals are the portability test.** Run the full suite on at least 3 models from 2 families at each release; track task success, tool-call validity rate, latency and cost.
5. **Prompts may vary per model family, but variants are versioned and measured.**
6. **Structured outputs:** use schema or grammar-constrained decoding where the runtime supports it (llama.cpp's server accepts JSON schema/grammar), otherwise validate and retry.
7. **Caching behind an abstraction** (vendor prompt caching vs self-hosted prefix caching).
8. **Audit trail** of which model *and which provider endpoint* saw which data.

### OpenRouter as the hosted leg (Tiers H and F)

Your plan is to reach both open-weights and proprietary models through OpenRouter. That works well as the *hosted* leg of this roadmap: one OpenAI-style chat-completions API, one key, and easy cross-model comparison, which is exactly what the Phase 1 bake-off and the Phase 3 and 7 portability matrices need. Keep local runtimes (llama.cpp, Ollama, vLLM) for Tiers L/S, because a hosted router can't teach you hardware sizing, quantization, serving or air-gapped deployment, and you'll need those for Phase 5 and the FDE track. I haven't verified whether OpenRouter can host your own fine-tuned weights; assume you'll serve those yourself.

What to know before you rely on it:

1. **A model slug is a pool of providers, not one deployment.** By default OpenRouter load-balances across providers to maximize uptime. Providers can differ in advertised quantization, context window and tool-calling support, so the same slug can behave differently from one request to the next. That is fine for production availability and bad for reproducible evals.
2. **Pin what you evaluate.** Use the `provider` object: `order`/`only`/`ignore` to choose endpoints, `quantizations` to filter precision, `require_parameters` so requests with tools or structured outputs only reach providers that support them (OpenRouter's docs recommend this for structured outputs), and `max_price` to cap spend. Disable fallbacks for eval runs. Docs: [provider routing](https://openrouter.ai/docs/guides/routing/provider-selection) (docs index: [openrouter.ai/docs/llms.txt](https://openrouter.ai/docs/llms.txt)).
3. **Record the endpoint in every eval result:** model slug, provider, reported quantization, context length, date. Treat a provider change as a possible regression source ("provider drift") alongside prompt and model changes. Provider-declared quantization and capability metadata are claims, not guarantees, so spot-check tool-call validity yourself.
4. **Privacy is a routing setting, not a default.** Use `zdr` (zero-data-retention endpoints only) and `data_collection: deny` for anything sensitive. The router is also an extra data processor, so read its privacy and provider-logging policy; OpenRouter's docs mention in-region (EU/US) routing for enterprise customers. Many FDE customers will forbid third-party gateways entirely, which is why the Phase 6 deployment pack needs a fully offline open-weights configuration.
5. **Don't make the router your only exit path.** Keep your `ModelClient` adapter and a second route (a local server or a LiteLLM-style gateway) so you can switch if pricing, availability or terms change.
6. **Cost tracking:** pass a stable user identifier where supported and log per-request cost yourself, so budgets and per-task cost don't depend on one dashboard.

---

# 1. Topic landscape

| Domain | Topic | What it is (and why it matters to you) | Priority |
| --- | --- | --- | --- |
| **A. LLM fundamentals** | Tokens & tokenization | Text is split into sub-word tokens; cost, latency and limits are all in tokens. Explains odd failures (counting, spelling, non-English cost). | Must |
|  | Embeddings | Vectors capturing meaning; the basis of semantic search, clustering and RAG. | Must |
|  | Transformers (conceptual) | Attention-based architecture; know it well enough to reason about context limits, KV cache and why long context degrades. No math derivations needed. | Should |
|  | Context windows & long-context behavior | Finite working memory with an "attention budget"; quality can degrade as context grows. Drives every design choice in agents. | Must |
|  | Sampling & determinism | Temperature, top-p, seeds; why "temperature 0" is still not fully deterministic. Matters for testing and evals. | Must |
|  | Model families & selection | Open-weight vs closed, reasoning vs non-reasoning, small vs large, multimodal; choosing on quality/latency/cost/privacy/licence with *your* evals, then routing between them. Open-weights-first by default. | Must |
|  | Reasoning models / test-time compute | Models that spend extra tokens thinking; changes latency, cost and prompting style. | Should |
|  | Training pipeline (pretrain → SFT → preference/RL) | Conceptual map of how assistants are made; tells you what fine-tuning can and cannot change. | Should |
|  | Multimodal (vision, documents, voice) | Image/PDF/audio inputs; critical for enterprise document workflows. | Should |
| **B. Prompt & context** | Prompt engineering | Writing clear instructions, examples and formats; still the cheapest lever. | Must |
|  | Context engineering | Curating *everything* that enters the window (instructions, tools, retrieved data, history, memory), including compaction and just-in-time retrieval. | Must |
|  | Prompt caching & prompt versioning | Cache stable prefixes to cut cost/latency; treat prompts as versioned, tested artifacts. | Must |
|  | Structured outputs | Schema-constrained JSON (vendor schema modes, or grammar-constrained decoding in local runtimes) so downstream code can trust output. | Must |
|  | Tool use / function calling | The model requests actions; your code executes them. The foundation of agents. | Must |
|  | Tool design (agent-computer interface) | Tool naming, schemas, error messages and response shape decide agent success more than prompts do. | Must |
| **C. Retrieval** | Embedding models & vector stores | ANN indexes, pgvector vs dedicated DBs, filtering, scale/cost trade-offs. | Must |
|  | RAG pipeline basics | Ingest → parse → chunk → embed → retrieve → generate → cite. | Must |
|  | Chunking, metadata, contextual retrieval | Chunk design and context-enriched chunks are the biggest quality levers. | Must |
|  | Hybrid search & reranking | BM25 + dense retrieval, rank fusion, cross-encoder rerankers. | Must |
|  | Query transformation | Rewriting, multi-query, HyDE to bridge query/document mismatch. | Should |
|  | Document parsing for messy data | PDFs, tables, scans/OCR, spreadsheets; the real bottleneck in enterprise deployments. | Must |
|  | Agentic RAG | Retrieval as a tool the agent calls iteratively, with self-checks. | Should |
|  | GraphRAG / knowledge graphs | Build entity graphs and community summaries for corpus-wide questions; expensive to index. | Nice |
|  | RAG evaluation | Separate retrieval quality from generation quality; build golden sets. | Must |
|  | Search vs RAG vs long-context | Deciding when each is the right tool. | Should |
| **D. Agents** | Workflows vs agents & core patterns | Prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer; when to prefer fixed code paths. | Must |
|  | Agent loop, planning, ReAct-style reasoning | The "LLM + tools in a loop" model, stopping conditions, budgets. | Must |
|  | When NOT to use agents | Deterministic problems, tight latency/cost, high-stakes actions without oversight. | Must |
|  | Memory | Short-term (context), long-term (files/DB), compaction, notes. | Should |
|  | Multi-agent & subagents | Delegation for context isolation and parallelism; costly and failure-prone if overused. | Should |
|  | Agent frameworks | LangGraph, OpenAI Agents SDK, Claude Agent SDK, CrewAI, hosted agent runtimes; know two well and the trade-offs of the rest. | Should |
|  | Durable execution & human-in-the-loop | Checkpointing, resumability, approval gates, idempotent side effects; where your backend skills transfer directly. | Must |
|  | Computer use, browser agents, sandboxes | Agents acting through UIs and shells; sandboxing and permissioning matter. | Should |
| **E. Interop** | Model Context Protocol (MCP) | Open standard for exposing tools/resources/prompts to AI apps. | Must |
|  | A2A & agent interoperability | Agent-to-agent protocol (complements MCP's agent-to-tool role). Skills/AGENTS.md-style conventions. | Should |
| **F. Harness engineering** | Harness design | The tools, context, constraints, verification and feedback loops that make a model reliable in an environment; includes progress files, checkpoints, lint/test gates and agent-legible repos. | Must |
| **G. Evaluation** | Eval fundamentals & error analysis | Look at real traces, categorize failures, then write targeted evals. | Must |
|  | Graders: code, LLM-as-judge, human | Choosing and calibrating graders; judge bias and validation. | Must |
|  | Agent evals (trajectory & outcome) | Tasks, trials, transcripts, outcomes, pass@k vs pass^k reliability. | Must |
|  | Benchmarks literacy | What public benchmarks do and don't tell you; contamination; why you need your own evals. | Should |
|  | Regression testing & CI gates | Run evals on every prompt/model/tool change; gate releases. | Must |
|  | Online evaluation & feedback flywheel | A/B tests, user feedback, trace mining to grow eval sets. | Should |
| **H. Fine-tuning** | Decision framework (prompt → RAG → fine-tune) | Fine-tune late, only when evidence says other levers are exhausted. | Must |
|  | SFT, LoRA/QLoRA | Parameter-efficient adaptation of open models; do it hands-on once (Phase 5). | Must |
|  | Preference tuning (DPO, RLHF-style) | Optimize on preferred vs rejected outputs. | Nice |
|  | Distillation & synthetic data | Use a strong model to train a cheaper narrow one. | Should |
|  | Open-weights vs vendor fine-tuning APIs | Owning the adapted weights (portable, deployable anywhere) vs renting a vendor's tuned model. | Should |
| **I. Inference & serving** | Hosted API operations | Rate limits, retries, streaming, batching, fallbacks, gateways. | Must |
|  | Caching (prompt, semantic, result) | Biggest cost/latency lever after model choice. | Must |
|  | Open-source serving (vLLM, llama.cpp, Ollama) | Self-hosting for privacy, cost, sovereignty or air-gapped customers. | Must |
|  | Quantization, KV cache, continuous batching | The mechanics behind throughput and memory; you need them to size hardware for open models. (Speculative decoding: Nice.) | Must |
|  | Cost & latency engineering | Model cascades/routing, TTFT vs total latency, token budgets, unit economics. | Must |
|  | Build-vs-self-host economics | GPU utilization, break-even analysis. | Should |
| **J. LLMOps** | Tracing & observability | Full traces of prompts, tool calls, tokens, cost; OpenTelemetry GenAI conventions. | Must |
|  | Prompt/model versioning & safe upgrades | Pinning, canaries, shadow traffic, eval-gated model swaps. | Must |
|  | CI/CD for AI | Eval suites in the pipeline, artifact versioning, rollback. | Must |
|  | FinOps for AI | Budgets, per-tenant cost attribution, spend caps. | Must |
|  | Incident response & drift | Detecting quality regressions from model or data changes. | Should |
| **K. Safety & security** | Prompt injection & the "lethal trifecta" | Private data + untrusted content + external communication in one agent = exfiltration risk. | Must |
|  | Data leakage, PII, tenant isolation | Retrieval permissions, logging hygiene, data residency. | Must |
|  | Agent authorization & sandboxing | Least privilege, scoped credentials, approval gates, isolated execution. | Must |
|  | Guardrails & red-teaming | Input/output checks, adversarial testing; know their limits. | Should |
|  | Governance & compliance | NIST AI RMF, EU AI Act, SOC 2/GDPR implications, audit trails. | Should |
| **L. AI-native engineering** | Coding agents & AI-native workflow | Claude Code/Codex/Cursor-style tools; plan → implement → verify loops, CLAUDE.md/AGENTS.md, subagents, skills, hooks. | Must |
|  | Reviewing & verifying AI-written code | Tests as feedback loops; keeping humans in the loop at the right altitude. | Must |
|  | Team enablement | Standards, guardrails, training, measuring productivity honestly. | Must |
| **M. Staff-level architecture** | AI system design patterns | Reference architectures, failure modes, degradation paths. | Must |
|  | Build vs buy vs partner | Vendors, open source, models, platforms; lock-in and exit costs. | Must |
|  | Use-case selection & ROI | Where AI pays off; TCO including evals, ops and human review. | Must |
|  | Reliability engineering for non-determinism | SLOs for probabilistic components, fallbacks, bounded autonomy. | Must |
|  | UX for AI | Streaming, uncertainty, approvals, trust. | Should |
|  | AI platform thinking | Gateways, shared eval/observability, golden paths for other teams. | Should |
| **N. FDE** | Customer discovery & problem scoping | Turning vague pain into a testable, measurable problem. | Must |
|  | Rapid prototyping with customers | Thin vertical slices on real data in days, not quarters. | Must |
|  | Integrating messy enterprise data/systems | Legacy APIs, SSO, data quality, access controls, ontologies. | Must |
|  | Deploying in customer environments | VPC/on-prem/air-gapped, security reviews, runbooks. | Must |
|  | Stakeholder communication | Exec readouts, expectation setting, honest uncertainty. | Must |
|  | Pilot → production | Success metrics, avoiding "pilot purgatory", change management. | Must |
|  | Field-to-product feedback loop | Turning one-off hacks into reusable product capabilities. | Must |
|  | Commercial awareness | Services vs product margin, scoping, when custom work becomes a moat or a trap. | Should |
| **O. Model portability & open-weights operations** *(new)* | Open-weights literacy | Model cards, base vs instruct, MoE total vs active parameters, licences (Apache 2.0, MIT, custom). | Must |
|  | Hardware & memory sizing | Weights + KV cache + quantization vs GPU/CPU/Apple-silicon budgets; why "fits in memory" is not "fast enough". | Must |
|  | Local runtimes vs servers | llama.cpp and Ollama for local/edge; vLLM for throughput; when to use each. | Must |
|  | Chat templates & tool-call formats | Different models serialize roles and tool calls differently; the usual reason an agent works on one model and breaks on another. | Must |
|  | Provider abstraction & gateways | OpenAI-compatible interface, routing, fallbacks, per-model config, cost tracking. | Must |
|  | Constrained / grammar-guided decoding | Force valid JSON or grammar at decode time; key for small models. | Should |
|  | Cross-model portability testing | Run the same evals across models/families/sizes; migrate on evidence. | Must |
|  | Judge-model selection & calibration | Choosing and validating an open (or closed) judge; avoiding self-preference and bias. | Must |
|  | Open evaluation tooling | Open frameworks that run on any model (e.g., Inspect AI). | Should |
|  | Data sovereignty & air-gapped deployment | Keeping prompts and data inside a boundary; offline customer installs. | Must |
|  | Licence & compliance for derived models | Terms for fine-tuned weights, distilled data and redistribution. | Should |
|  | Router/gateway operations (OpenRouter-style) | Provider pinning, quantization filters, ZDR/data-collection settings, price caps, fallbacks, `require_parameters` for tools and structured outputs. | Must |
|  | Provider drift & eval reproducibility | The same model slug can map to different providers/quantizations over time; log the endpoint with every eval. | Must |
| **P. Staff+ leadership & organizational impact** *(new, from QA)* | Technical strategy & vision docs | A 12-month plan that says what you will *not* build and why; ties architecture to business outcomes. | Must |
|  | RFC/ADR process & decision hygiene | Writing decisions down, inviting review, recording rejected options. | Must |
|  | Reference implementations & golden paths | Paved roads other teams can adopt instead of re-solving agents, evals and tracing. | Must |
|  | Mentoring & raising standards without authority | Teaching, code/design review, and setting norms across teams. | Must |
|  | Cross-functional partnership | Working with security, legal, data, procurement and product on AI controls and launches. | Must |
|  | Production/launch readiness reviews | A structured go/no-go for LLM systems (reliability, evals, security, cost, rollback). | Must |
|  | Incident management & blameless postmortems for AI | Triage, rollback, root-cause for non-deterministic failures; learning loops. | Must |
|  | Vendor & model lifecycle management | Contracts, SLAs, data terms, model deprecation windows, migration playbooks and exit plans. | Must |
|  | AI usage metering & cost allocation | Per-team/per-tenant attribution, showback/chargeback, spend caps. | Must |
|  | Measuring AI impact & adoption | Product KPIs and developer-productivity measures that don't rely on vanity metrics. | Should |
|  | Hiring, interviewing & enablement curriculum | Rubrics for AI engineers and training that scales your knowledge. | Should |
|  | Executive communication & decision memos | Translating trade-offs, risk and cost into plain language for leaders. | Must |
| **Q. Application domains (electives)** *(new, from QA)* | NL-to-SQL / analytics agents | Schema retrieval, semantic layer, SQL validation and self-correction. | Should |
|  | Voice & real-time agents | Speech pipelines, turn detection, latency budgets, cascaded vs speech-to-speech. | Should |
|  | Document AI deep-dive | Layout, tables, OCR/vision extraction, local/air-gapped parsing. | Should |
|  | Enterprise search & relevance engineering | Classic information-retrieval craft: relevance judgments, ranking metrics, query analysis. | Should |
|  | Ontology / semantic-layer modeling | Modeling customer entities and relationships so agents and users share vocabulary (Palantir-style). | Should |
|  | Multilingual & accessibility | Non-English quality, tokenization cost, assistive-technology compatibility. | Nice |
|  | On-device/edge, video/robotics/world models | Adjacent areas; know the landscape, don't go deep unless your work requires it. | Nice |
| **R. Reliability, security & compliance additions** *(new, from QA)* | Agent identity & enterprise auth | OAuth/OIDC, SSO/SAML, SCIM, scoped short-lived tokens, MCP authorization changes. | Must |
|  | Workflow engines, async & batch inference | Temporal-style durable workflows, queues, idempotency, backpressure, batch APIs. | Must |
|  | Resilience patterns | Circuit breakers, kill switches, feature flags, graceful degradation, safe rollouts. | Must |
|  | Load, soak & chaos testing for LLM systems | Rate-limit behavior, slow providers, partial outages, long-context stress. | Should |
|  | User simulation & synthetic test data | Simulated users/personas to test agents beyond cooperative cases. | Should |
|  | Human data & annotation operations | Labeling guidelines, inter-annotator agreement, review workflows, cost. | Should |
|  | Safety, bias & fairness evaluation | Measuring harmful or uneven behavior across groups and contexts. | Should |
|  | Privacy engineering | PII detection/redaction, retention, residency, privacy impact assessments. | Must |
|  | IP, copyright & data provenance | Rights in training and retrieval data, output ownership, licence compliance. | Should |
|  | Model registry, artifact & data lineage | Versioned prompts, adapters, datasets and eval results you can reproduce. | Should |
|  | Capacity planning, GPU procurement & IaC | Sizing, quotas, Terraform/Kubernetes for inference and tracing stacks. | Should |
|  | Enterprise integration for FDEs | SQL, data pipelines, API integration, VPC networking, SOC 2-style customer security reviews. | Must |
|  | Field operations for FDEs | Travel cadence, on-site conduct, conflict management, extracting repeatable patterns. | Should |

---

# 2. Phased roadmap

**Timeline overview (≈9 h/week):**

| Phase | Name | Weeks | Hours |
| --- | --- | --- | --- |
| 0 | AI-native workflow setup | 1 | 9 |
| 1 | Foundations | 3 | 27 |
| 2 | Building: prompts, tools, RAG, eval basics | 5 | 45 |
| 3 | Agents, MCP & harness engineering | 6 | 54 |
| 4 | Production: LLMOps, security, cost | 5 | 45 |
| 5 | Open-weights engineering: fine-tune, quantize, serve (core); advanced retrieval (optional) | 5 | 45 |
| 6 | Forward Deployed Engineer track | 4 | 36 |
| 7 | Capstone | 8 | 72 |
| 8 | Optional electives: NL-to-SQL, voice, document AI, enterprise search, compliance (pick by your job) | 2 each | \~18 each |
|  | **Total (excluding electives)** | **37** | **\~333** |

### Staff+ leadership thread (runs alongside every phase)

The QA audit found that v1 treated "Staff level" mostly as technical depth plus ADRs and talks. Staff/Principal AI postings also ask for technical strategy, build-vs-buy ownership, mentoring and raising standards without direct authority, cross-functional partnership (security, legal, data), launch-readiness reviews, cost allocation and credible executive communication. Each phase therefore ends with a leadership deliverable. Treat these as seriously as the code.

| After phase | Leadership deliverable | What it proves |
| --- | --- | --- |
| 0 | One-page team policy for AI coding tools plus an AGENTS.md-style standard | Enablement and guardrails |
| 1 | RFC: model selection and portability policy (tiers, exception log) | Technical strategy |
| 2 | ADR on retrieval design; 30-minute internal talk; shared eval-spec template | Decision hygiene, teaching |
| 3 | RFC: "workflow vs agent" decision guide plus a reference-implementation repo (golden path) | Reusable standards |
| 4 | Production readiness review (PRR) checklist for LLM systems, incident runbook and blameless postmortem template, threat-model review with security, vendor exit plan | Operational leadership |
| 5 | Capacity and cost model (GPU vs API), build-vs-buy memo, showback/chargeback design for AI spend | Business and platform judgment |
| 6 | Executive readout, stakeholder map with a conflict-resolution log, "repeatable patterns" extraction doc | Customer and stakeholder leadership |
| 7 | 12-month AI platform strategy doc, hiring rubric and interview loop for AI engineers, enablement curriculum, adoption and impact metrics, talk | Org-level impact |

Also mentor at least two engineers through a phase project each. Teaching exposes your own gaps fast.

## Phase 0: AI-native workflow setup (1 week)

- **Objectives:** Work daily with a coding agent (any one; an open-source agent pointed at a local model is a fine choice, but expect more agent-loop failures than with frontier models). Set up Python (uv), a local model runner (llama.cpp or Ollama) exposing an OpenAI-compatible endpoint, an OpenRouter account for hosted open-weights and proprietary models behind one API, and a notes repo. Learn plan-first workflows, project instruction files (AGENTS.md-style, vendor-neutral) and test-driven loops.
- **Prerequisites:** None.
- **Project: "Agent-built CLI".** Build a small tool (e.g., log summarizer or repo-stats CLI) almost entirely through a coding agent. Include a project instruction file, tests the agent must pass, and a one-page retrospective on where it went wrong and what constraints fixed it.
- **Portfolio output:** Repo + retrospective (this becomes the seed of your team-enablement material).

## Phase 1: Foundations (3 weeks)

- **Objectives:** Hold an accurate mental model of tokens, embeddings, attention/context, sampling and the training pipeline. Call APIs from raw HTTP and an SDK. Run open models locally through llama.cpp and Ollama and through an OpenAI-compatible endpoint. Do hardware/memory math, read model cards and licences, and compare model families on quality, latency and cost.
- **Prerequisites:** Phase 0; Python.
- **Project: "Model bake-off harness".** A CLI that runs the same 30 realistic tasks (extraction, classification, summarization, code, reasoning) across at least 5 models: three open-weights models of different sizes/families run locally (Tier L/S), one large open-weights model via OpenRouter (Tier H, pinned to a specific provider with the provider, quantization and context length logged), and optionally one closed "ceiling" model via OpenRouter. Record tokens, latency, cost (including your hardware estimate), licence, and a manual-rubric score. Add a `MODEL_CARD.md` for each model. Publish findings with a clear recommendation per task type.
- **Portfolio output:** Repo, results table, short write-up on model selection criteria.

## Phase 2: Building: prompts, context, tools, RAG, eval basics (5 weeks)

- **Objectives:** Write robust prompts and structured outputs. Implement tool calling. Build RAG from naive to hybrid + rerank + contextual chunking. Build your first retrieval and answer eval set (error analysis on real failures).
- **Prerequisites:** Phase 1.
- **Project: "Cited Q&A over a messy corpus".** Ingest 200+ real-world PDFs/HTML/spreadsheets (public filings, standards, manuals). Pipeline: parsing → chunking with metadata → hybrid search → reranker → answer with citations → streaming API. Create a 50-question golden set; report retrieval hit-rate and answer-quality before/after each improvement. Use an open-weights embedding model (kept swappable), and run generation with at least two models (one small open, one larger) to see how much each technique helps each.
- **Portfolio output:** Repo with eval report showing measured gains per technique; ADR on chunking/retrieval choices.

## Phase 3: Agents, MCP & harness engineering (6 weeks)

- **Objectives:** Distinguish workflows from agents and choose correctly. Design tools well. Build and consume MCP servers. Implement durable, resumable agent runs with human approval. Build a harness with progress artifacts, verification steps and budgets. Evaluate agents on trajectories and outcomes, including reliability across repeated trials.
- **Prerequisites:** Phase 2.
- **Project: "Ops copilot with an MCP tool layer".** Build an agent that triages incidents using an MCP server you write (tickets, logs, runbooks, deploy history; mock the backends). Include: a workflow-only baseline vs the agentic version, approval gate before any write action, checkpointed state (implemented once with LangGraph persistence and once on a workflow engine such as Temporal, with the trade-offs written up), a task suite with outcome graders, and pass@k/pass^k reporting. **Run the agent on at least three models (default: a Tier S open model) and report tool-call validity rate, success rate and cost per model (with the provider and quantization behind each OpenRouter run); diagnose every cross-model failure as harness, template or model limit.**
- **Portfolio output:** Repo, MCP server, eval results comparing workflow vs agent (cost, latency, success).

## Phase 4: Production: LLMOps, safety, cost (5 weeks)

- **Objectives:** Instrument everything (traces, tokens, cost). Gate releases with evals in CI. Run a prompt-injection threat model using the lethal-trifecta lens. Add caching, rate limiting, model fallback and per-tenant budgets. Practice a safe model upgrade.
- **Prerequisites:** Phase 3.
- **Project: "Harden the ops copilot".** Add OpenTelemetry tracing exported to a self-hostable LLM observability backend (traces stay inside your network), CI eval gates that run across more than one model, an injection test suite (poisoned tickets/logs), scoped credentials, caching, a gateway that routes between a local open-weights model and OpenRouter (explicit provider allow-lists, ZDR where required, max-price caps) with fallback, a cost dashboard, and a canary rollout for a model swap with a written post-mortem of what the evals caught.
- **Also deliver (added by QA):** resilience patterns (circuit breaker, kill switch, feature-flagged rollout, graceful degradation), an agent-identity design (scoped, short-lived credentials; SSO/OAuth integration for tools), a production readiness review, an incident runbook with a blameless postmortem of a staged failure, and a vendor/model deprecation and exit plan.
- **Portfolio output:** Threat model doc, CI config, dashboards (screenshots), upgrade runbook, PRR checklist, incident runbook, vendor exit plan.

## Phase 5: Open-weights engineering (5 weeks; Tracks A and B required, C optional)

- **Track A, Fine-tuning & distillation (2 weeks).** Fine-tune a small open model with LoRA/QLoRA on a narrow task using data labeled by a stronger model. Compare to the best prompted baseline on your eval set. Write an honest verdict. Check the teacher model's terms of use before training on its outputs.
- **Track B, Serving & inference economics (2 weeks).** Serve an open model with vLLM; benchmark throughput/latency/quality vs a hosted API; quantize (e.g., 4-bit vs 8-bit vs 16-bit) and measure the quality drop on *your* eval set; run the same model on llama.cpp for a CPU/edge comparison; compute break-even cost.
- **Track C, Advanced retrieval & multi-agent (optional, 1 week; otherwise use the week to harden Tracks A and B).** Try GraphRAG or agentic RAG on a question set where naive RAG fails; evaluate a subagent decomposition against a single agent.
- **Prerequisites:** Phases 2–4.
- **Project: "Distill, quantize & serve" (Tracks A+B).** Portfolio output: benchmark report with a clear build-vs-buy conclusion, the adapter weights, a serving config, and a model card. Optional add-on: "Retrieval stress test" (Track C).

## Phase 6: Forward Deployed Engineer track (4 weeks)

- **Objectives:** Run discovery, scope a problem, prototype against messy data, plan deployment into a restricted environment, communicate with executives, and convert field lessons into product requirements. Also (added by QA, from current FDE postings): integrate with enterprise identity (OAuth/OIDC, SSO/SAML, SCIM, IAM, VPC networking), write SQL and basic data pipelines against customer data, manage conflicting priorities across stakeholder groups, and extract repeatable patterns from one-off work.
- **Prerequisites:** Phases 2–5.
- **Project: "Mock engagement".** Invent or source a realistic customer scenario (e.g., invoices + email threads + ERP exports from a mid-size distributor). Deliver: (1) discovery notes and a problem statement with success metrics, (2) a two-week-style prototype on realistic messy data, (3) a deployment pack for a locked-down customer environment (container manifests, network/data-flow diagram, security questionnaire answers, and a fully offline configuration that runs an open-weights model on customer hardware), (4) a 10-minute exec readout, (5) a product-feedback memo describing what should become a platform feature. Role-play the discovery and readout with a colleague.
- **Portfolio output:** A redacted "case study" bundle.

## Phase 7: Capstone (8 weeks)

**"Enterprise knowledge-and-action agent"**: a production-grade, multi-tenant agentic system.

Required capabilities:

- RAG over heterogeneous documents with permission-aware retrieval, plus MCP tools to mock ticketing/CRM systems.
- **Open-weights default, model-agnostic by configuration:** the whole system runs on a self-hosted open model, switches to another model (open or hosted) by changing configuration only, and ships a **portability matrix** (success rate, tool-call validity, latency, cost per successful task) for at least four models across two families, plus a model-swap CI job.
- Human approval gates for risky actions; durable, resumable runs.
- Three eval layers (retrieval, trajectory, outcome) with CI gating and an LLM-judge calibrated against human labels.
- Full tracing, per-tenant cost attribution, prompt caching, model fallback and spend caps.
- A written threat model addressing the lethal trifecta, with an automated injection test suite.
- A customer-deployment pack (containerized, documented network and data flows) and an operations runbook.
- Deliverables: architecture doc with ADRs, a 10-minute demo video, a public write-up of lessons (what failed, what the evals caught), and a talk you give internally.

Success criteria: a new engineer can deploy it from the README; a model upgrade is fully eval-gated; you can state cost per successful task and your SLOs; the eval suite meets your bar on at least two open-weights models, and you can explain the gap (if any) to a closed "ceiling" model.

---

## Phase 8: Optional electives (2 weeks each; pick the ones your job needs)

These are adjacent application domains that Staff/Principal and FDE postings mention but a general roadmap can't cover deeply. Each elective follows the same rules as everything else: open-weights default, a golden eval set, portability across at least two models, and a one-page ADR.

| Elective | Project | Eval you must build |
| --- | --- | --- |
| E1. NL-to-SQL / analytics agents | Natural-language analytics over a real schema: schema/table retrieval, a semantic layer, SQL validation and self-correction, read-only execution | Gold-query set with execution-match accuracy; failure taxonomy |
| E2. Voice and real-time agents | A cascaded speech-to-text → LLM → text-to-speech agent with turn detection and interruption handling; try a speech-to-speech variant | Per-stage and end-to-end latency budget; task-success rate on scripted calls |
| E3. Document AI pipelines | Layout-aware parsing, table extraction and OCR/vision-model fallback on messy PDFs, running fully local | Field-level extraction accuracy; table-structure fidelity; cost per page |
| E4. Enterprise search & relevance | Hybrid search with relevance judgments, query-log analysis and ranking evaluation | Ranking metrics (e.g., NDCG) on labeled queries; before/after of each change |
| E5. Compliance sprint | Map your capstone to NIST AI RMF functions; produce a system/model card, a lightweight privacy impact assessment and a gap list against ISO/IEC 42001 and the EU AI Act | A control-to-evidence matrix reviewed by someone from security or legal |

---

# 3. Resources by phase

*Legend:* ⭐ = start here for that topic. "Free" items are first-party docs, engineering blogs, papers or repos.

## Phase 0: AI-native workflow

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Coding-agent workflow *(Claude-specific; concepts transfer)* | Claude Code: Best practices | Free | [code.claude.com/docs/en/best-practices](https://code.claude.com/docs/en/best-practices) | Official patterns: explore/plan/implement phases, instruction files, permissions. Core idea: context fills fast, so manage it. | ⭐ |
| Coding-agent workflow | Claude Code in Action (Anthropic Academy) | Free | [anthropic.skilljar.com/claude-code-in-action](https://anthropic.skilljar.com/claude-code-in-action) | Short official course on integrating the tool into real development. |  |
| Skills & subagents | Introduction to agent skills (Anthropic Academy) | Free | [anthropic.skilljar.com/introduction-to-agent-skills](https://anthropic.skilljar.com/introduction-to-agent-skills) | Reusable instruction bundles; useful for team enablement. The Academy also lists "Introduction to subagents" (browse from [anthropic.skilljar.com](https://anthropic.skilljar.com/); direct link not verified). |  |
| Mindset | Harness engineering: leveraging Codex in an agent-first world (OpenAI) | Free | [openai.com/index/harness-engineering](https://openai.com/index/harness-engineering/) | A team shipped a large codebase with agent-written code; the lesson is that engineers design environments, intent and feedback loops. Re-read in Phase 3. |  |
| Practitioner tips | Claude Code Best Practices: 12 Patterns Agentic Engineers Use | Medium (Level Up Coding) | [levelup.gitconnected.com/…-65264e3eb919](https://levelup.gitconnected.com/claude-code-best-practices-12-patterns-agentic-engineers-use-65264e3eb919) | Skimmable community distillation. It summarizes a third-party repo, so treat as secondary. |  |
| Ongoing | Simon Willison's Weblog | Free | [simonwillison.net](https://simonwillison.net/) | Daily-use notes on LLM tooling from a careful practitioner. |  |
| Open-source coding agents | Open Source AI Coding Agents in 2026: The 12 Worth Using (OpenAlternative) | Free | [openalternative.co/blog/best-open-source-ai-coding-agents](https://openalternative.co/blog/best-open-source-ai-coding-agents) | Survey of open agents (OpenCode, Cline, Aider, Goose and others) with licences and local-model support. Secondary source; verify each project's repo and licence. Note one tool listed is tuned for its vendor's own models. |  |
| Local runtime for the agent | llama.cpp (`llama-server` gives an OpenAI-compatible endpoint) | Free (GitHub) | [github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) | MIT-licensed C/C++ inference; runs GGUF models and can pull them from Hugging Face. |  |

## Phase 1: Foundations

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| LLM mental model | Deep Dive into LLMs like ChatGPT (Andrej Karpathy, Feb 2025, \~3.5 h) | Free (YouTube) | [youtube.com/watch?v=7xTGNNLPyMI](https://www.youtube.com/watch?v=7xTGNNLPyMI) | Best single walkthrough of the full training stack and model "psychology" without needing math. | ⭐ |
| LLM mental model | Hands-On Large Language Models (Jay Alammar, Maarten Grootendorst, 2024) | O'Reilly | [oreilly.com/library/view/hands-on-large-language/9781098150952](https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/) | Heavily illustrated; covers tokens, embeddings, prompting, semantic search/RAG. Code: [GitHub](https://github.com/HandsOnLLM/Hands-On-Large-Language-Models). |  |
| Spine for the whole roadmap | AI Engineering: Building Applications with Foundation Models (Chip Huyen, Dec 2024) | O'Reilly | [oreilly.com/library/view/-/9781098166298](https://www.oreilly.com/library/view/-/9781098166298/) | The most complete applied treatment: model understanding, evaluation, prompting, RAG/agents, fine-tuning, inference, architecture. Companion repo: [chiphuyen/aie-book](https://github.com/chiphuyen/aie-book). Read Part 1 now, the rest later. | ⭐ (as book) |
| Hands-on course | Hugging Face LLM Course | Free | [huggingface.co/learn/llm-course](https://huggingface.co/learn/llm-course) | Free, broad, built by the Transformers team; skim early chapters, use later chapters in Phase 5. |  |
| Course (video) | LLM Engineering: Master AI, LLMs & Agents (Ed Donner & Ligency) | Udemy | [udemy.com/course/llm-engineering-master-ai-and-large-language-models](https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models/) | 8-week project-based path starting from transformers and local models; its listing describes working with 20+ models, both frontier and open-source, which suits a model-agnostic approach. *Live title now shown as "AI Engineer Core Track: LLM Engineering, RAG, QLoRA, Agents."* Course slug verified; use search if it redirects. |  |
| Course (interactive) | LLM Bootcamp | Educative | [educative.io/courses/llm-bootcamp](https://www.educative.io/courses/llm-bootcamp) | Starts with transformers, tokenization and embeddings and builds to prompting, security and token optimization. |  |
| Transformer paper *(classic, 2017)* | Attention Is All You Need | Free | [arxiv.org/abs/1706.03762](https://arxiv.org/abs/1706.03762) | Read the abstract and diagrams only; you don't need the math. |  |
| Open-weights model selection | Open-weight roundups (secondary; they disagree, so use them to build a shortlist, then test) | Free | [sysart.consulting/insights/best-open-weight-llms-2026](https://sysart.consulting/insights/best-open-weight-llms-2026/) · [geotoolbox.ai/blog/best-open-source-llms](https://geotoolbox.ai/blog/best-open-source-llms) | Licence, size and hardware comparisons as of Oct–Nov 2026. Always confirm on the model card (search the model on Hugging Face; direct links not verified). | ⭐ (for shortlisting) |
| Run models locally | llama.cpp | Free (GitHub) | [github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) | The reference local runtime; OpenAI-compatible server, GGUF, multimodal support. | ⭐ |
| Run models locally | Ollama | Free (GitHub) | [github.com/ollama/ollama](https://github.com/ollama/ollama) | Easiest local runner (MIT-licensed; listed in llama.cpp's README). |  |
| Hosted open + proprietary models | OpenRouter provider routing docs | Free | [openrouter.ai/docs/guides/routing/provider-selection](https://openrouter.ai/docs/guides/routing/provider-selection) | Official reference for the `provider` object (order, only, ignore, quantizations, zdr, max_price, require_parameters). Read before your first bake-off. Doc index: [openrouter.ai/docs/llms.txt](https://openrouter.ai/docs/llms.txt). | ⭐ (for the hosted leg) |
| Reproducibility pitfalls | Your OpenRouter Model Slug Is a Pool, Not a Deployment | Free (DEV Community) | [dev.to/moksh/your-openrouter-model-slug-is-a-pool-not-a-deployment-24d3](https://dev.to/moksh/your-openrouter-model-slug-is-a-pool-not-a-deployment-24d3) | Third-party measurement of how providers for one slug differ in context window, quantization and tool support. Secondary source; verify against current provider listings. |  |
| Provider abstraction | LiteLLM | Free (GitHub) | [github.com/BerriAI/litellm](https://github.com/BerriAI/litellm) | Gateway that unifies hosted and local engines (Ollama, vLLM) behind one OpenAI-style interface. |  |
| Closed-model reference *(ceiling only)* | OpenAI models and guides index | Free | [developers.openai.com/resources/guides](https://developers.openai.com/resources/guides) | One vendor catalog for comparison; use alongside open model cards. Anthropic docs root: [docs.claude.com](https://docs.claude.com). |  |
| Staff framing | What We've Learned From A Year of Building with LLMs (Yan, Bischof, Frye, Husain, Liu, Shankar, 2024) | Free / O'Reilly | [applied-llms.org](https://applied-llms.org/); [O'Reilly report](https://oreilly.com/library/view/what-we-learned/9781098176716) | Tactical, operational and strategic lessons; excellent early calibration. | ⭐ (for judgment) |

## Phase 2: Building (prompts, tools, RAG, eval basics)

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Prompt engineering | Anthropic prompt engineering overview | Free | [docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview) | Official techniques with examples. | ⭐ |
| Prompt engineering | OpenAI Prompt engineering guide | Free | [developers.openai.com/docs/guides/prompt-engineering](https://developers.openai.com/docs/guides/prompt-engineering) | Notes that reasoning and GPT-style models need different prompting. |  |
| Prompt engineering | Prompt Engineering for LLMs (John Berryman, Albert Ziegler, 2024) | O'Reilly | [oreilly.com/library/view/prompt-engineering-for/9781098156145](https://www.oreilly.com/library/view/prompt-engineering-for/9781098156145/) | Written by early GitHub Copilot engineers; strongest on assembling prompts as application design. |  |
| Prompt engineering | Prompt Engineering for Generative AI (Phoenix, Taylor, 2024) | O'Reilly | [oreilly.com/library/view/-/9781098153427](https://www.oreilly.com/library/view/-/9781098153427/) | Broader, beginner-to-intermediate; useful if the Berryman book feels dense. |  |
| Context engineering | Effective context engineering for AI agents (Anthropic) | Free | [anthropic.com/engineering/effective-context-engineering-for-ai-agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) | Defines context engineering as the progression from prompt engineering; just-in-time retrieval, compaction, subagent isolation. | ⭐ |
| Tool use / structured outputs | Function calling help article (OpenAI) | Free | [help.openai.com/en/articles/8555517](https://help.openai.com/en/articles/8555517) | Clear on how function calling and Structured Outputs relate; the full guides are listed on the [OpenAI guides index](https://developers.openai.com/resources/guides). |  |
| Tool design | Writing effective tools for agents (Anthropic) | Free | [anthropic.com/engineering/writing-tools-for-agents](https://www.anthropic.com/engineering/writing-tools-for-agents) | How to design tools LLMs use well; tool quality often matters more than prompt tweaks. | ⭐ |
| RAG basics | Hands-On LLMs, Ch. 8 "Semantic Search and RAG" | O'Reilly | […/ch08.html](https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/ch08.html) | Clear first implementation with embeddings. | ⭐ |
| RAG basics | Fundamentals of Retrieval-Augmented Generation with LangChain | Educative | [educative.io/courses/rag-llm](https://www.educative.io/courses/rag-llm) | Interactive hands-on RAG in the browser. |  |
| RAG end-to-end | LLM Zoomcamp (DataTalksClub) | Free (GitHub) | [github.com/DataTalksClub/llm-zoomcamp](https://github.com/DataTalksClub/llm-zoomcamp) | Free course building a RAG app with evaluation and monitoring, plus a capstone. |  |
| Better retrieval | Introducing Contextual Retrieval (Anthropic, Sep 2024) | Free | [anthropic.com/news/contextual-retrieval](https://www.anthropic.com/news/contextual-retrieval) | Context-enriched chunks + BM25 + reranking, with a published cookbook; reports large retrieval-failure reductions on their benchmarks (treat as indicative). | ⭐ |
| Patterns *(classic, 2023)* | Patterns for Building LLM-based Systems & Products (Eugene Yan) | Free | [eugeneyan.com/writing/llm-patterns](https://eugeneyan.com/writing/llm-patterns) | Evals, RAG, fine-tuning, caching, guardrails, defensive UX, feedback; still the best map. Predates agents. |  |
| Advanced RAG | Agentic RAG | Educative | [educative.io/courses/agentic-rag](https://www.educative.io/courses/agentic-rag) | Reasoning-driven retrieval with LlamaIndex, evaluation and deployment. |  |
| Advanced RAG | GraphRAG documentation (Microsoft Research) | Free | [microsoft.github.io/graphrag](https://microsoft.github.io/graphrag/) | The reference implementation; warns indexing can be expensive, so start small. Paper: [arXiv 2404.16130](https://arxiv.org/abs/2404.16130). |  |
| RAG in practice | I Built a RAG Pipeline That Kept Lying to Users. Here's What Fixed It. | Medium (Level Up Coding) | [levelup.gitconnected.com/…-486abe39e662](https://levelup.gitconnected.com/i-built-a-rag-pipeline-that-kept-lying-to-users-heres-what-fixed-it-486abe39e662) | Hybrid search, chunking and fusion explained in a practitioner voice. Verify its statistics independently. |  |
| RAG & agents (book) | AI Engineering, Ch. 6 (RAG and Agents), Ch. 5 (Prompt Engineering) | O'Reilly | [Chip Huyen book](https://www.oreilly.com/library/view/-/9781098166298/) | Systematic treatment of the same material (chapter numbering per first edition). |  |
| Eval basics | Your AI Product Needs Evals (Hamel Husain, Mar 2024) | Free | [hamel.dev/blog/posts/evals/](https://hamel.dev/blog/posts/evals/) | The canonical case for domain-specific evals. | ⭐ |
| Eval basics | Building eval systems that improve your AI product (Husain & Shankar) | Free (Lenny's Newsletter) | [lennysnewsletter.com/p/building-eval-systems-that-improve](https://www.lennysnewsletter.com/p/building-eval-systems-that-improve) | Error-analysis-first workflow. |  |

## Phase 3: Agents, MCP & harness engineering

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Agent fundamentals / when not to | Building effective agents (Anthropic, Dec 2024) | Free | [anthropic.com/engineering/building-effective-agents](https://www.anthropic.com/engineering/building-effective-agents) | Workflows vs agents, composable patterns, advice to start with direct API use. Required reading. | ⭐ |
| Agent fundamentals | Building Applications with AI Agents (Michael Albada, Sep 2025) | O'Reilly | [oreilly.com/library/view/building-applications-with/9781098176495](https://www.oreilly.com/library/view/building-applications-with/9781098176495/) | Book-length treatment: orchestration, memory, multiagent, monitoring, security. Key chapters: [Ch. 8 single vs multi-agent](https://www.oreilly.com/library/view/building-applications-with/9781098176495/ch08.html), [Ch. 9 validation](https://www.oreilly.com/library/view/building-applications-with/9781098176495/ch09.html). |  |
| Agent fundamentals | AI Agents: The Definitive Guide (Nicole Koenigstein; early release) | O'Reilly | [oreilly.com/library/view/-/0642572247775](https://www.oreilly.com/library/view/-/0642572247775) | Production focus (evaluation, sandboxing, fault tolerance). Early-release: check completeness. |  |
| Enterprise framing | Building Effective AI Agents: Architecture Patterns and Implementation Frameworks (Anthropic guide) | Free | [claude.com/resources/guides/building-effective-ai-agents](https://claude.com/resources/guides/building-effective-ai-agents) | Useful for explaining patterns to non-engineers and customers. |  |
| Course (video) | Agentic AI Engineering: "The Complete Agentic AI Engineering Course" (Ed Donner) | Udemy | [udemy.com/course/the-complete-agentic-ai-engineering-course](https://www.udemy.com/course/the-complete-agentic-ai-engineering-course/) | Project-heavy; course descriptions cite OpenAI Agents SDK, CrewAI, LangGraph, AutoGen and MCP. *Live title now "AI Engineer Agentic Track: The Complete Agent & MCP Course."* |  |
| Course (interactive) | AI Skills Learning Path for Software Engineers | Educative | [educative.io/path/learning-ai-skills-for-software-engineers](https://www.educative.io/path/learning-ai-skills-for-software-engineers) | Path covers prompting, MCP-integrated agentic workflows, RAG and multi-agent orchestration. |  |
| Harness engineering | Effective harnesses for long-running agents (Anthropic, Nov 2025) | Free | [anthropic.com/engineering/effective-harnesses-for-long-running-agents](https://anthropic.com/engineering/effective-harnesses-for-long-running-agents) | Initializer + incremental coding agent, progress files, git checkpoints. Directly reusable. | ⭐ |
| Harness engineering | Harness engineering (OpenAI, Feb 2026) | Free | [openai.com/index/harness-engineering](https://openai.com/index/harness-engineering/) | Agent-legible repos, executable constraints, agent-to-agent review loops. |  |
| Frameworks *(Claude-oriented; optional)* | Claude Agent SDK overview | Free | [code.claude.com/docs/en/agent-sdk/overview](https://code.claude.com/docs/en/agent-sdk/overview) | Claude Code's agent loop, tools and context management as a library; docs also compare it with the client SDK and a hosted option. |  |
| Frameworks *(README describes multi-provider support; test it on a local model)* | OpenAI Agents SDK (Python) | Free (GitHub) | [github.com/openai/openai-agents-python](https://github.com/openai/openai-agents-python) | Small primitives: agents, handoffs, guardrails, sessions, tracing. *Repo path confirmed via secondary descriptions and forks.* |  |
| Frameworks | LangGraph: durable execution | Free | [docs.langchain.com/oss/python/langgraph/durable-execution](https://docs.langchain.com/oss/python/langgraph/durable-execution) | Checkpointing and resumability; maps onto the workflow-engine concepts you know. Repo: [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph). | ⭐ (for durability) |
| Open-source agents (model-agnostic) | Hugging Face Agents Course | Free | [huggingface.co/learn/agents-course](https://huggingface.co/learn/agents-course) | Covers smolagents, LlamaIndex and LangGraph, plus bonus units on observability/evaluation and on fine-tuning a model for function calling. Best neutral on-ramp. | ⭐ (for open-weights agents) |
| Tool-calling reliability | Chat templates & tool-call formats | Free | [HF LLM Course, Ch. 11 (chat templates)](https://huggingface.co/learn/llm-course/chapter11/1) | Why tool calls differ across models; read before debugging "the agent works on model A but not B". |  |
| Frameworks | CrewAI course | Educative | **link not verified**: search "Build AI Agents and Multi-Agent Systems with CrewAI" on Educative | One of several multi-agent options; I'd learn it after a single-agent framework. |  |
| MCP | Introduction to Model Context Protocol (Anthropic Academy) | Free | [anthropic.skilljar.com/introduction-to-model-context-protocol](https://anthropic.skilljar.com/introduction-to-model-context-protocol) | Build servers and clients in Python; tools, resources and prompts. | ⭐ |
| MCP | MCP: Advanced Topics (Anthropic Academy) | Free | [anthropic.skilljar.com/model-context-protocol-advanced-topics](https://anthropic.skilljar.com/model-context-protocol-advanced-topics) | Production-oriented follow-on. |  |
| MCP | AI Agents with MCP (Kyle Stratis) | O'Reilly | [oreilly.com/library/view/-/9798341639546](https://www.oreilly.com/library/view/-/9798341639546/) | Deep dive on clients, servers, transports, testing and securing. Check its coverage against the newest spec. |  |
| MCP | Mastering MCP: Building Advanced Agentic Applications | Educative | [educative.io/collection/10370001/6502147955752960](https://www.educative.io/collection/10370001/6502147955752960) | Multi-server coordination and RAG integration. |  |
| MCP (current spec) | MCP 2026-07-28 changelog | Free | [modelcontextprotocol.io/specification/2026-07-28/changelog](https://modelcontextprotocol.io/specification/2026-07-28/changelog) | **Important:** this revision makes the core stateless (no initialize handshake or protocol sessions), deprecates HTTP+SSE, and also deprecates Roots, Sampling and Logging. Older tutorials may predate this. Release notes: [MCP blog](https://blog.modelcontextprotocol.io/tags/mcp/). | ⭐ |
| Interoperability | A2A Protocol documentation | Free | [a2a-protocol.org/latest](https://a2a-protocol.org/latest/) | Agent-to-agent standard under the Linux Foundation; v1.0 reached in 2026. Complements MCP. Context: [Linux Foundation release](https://www.linuxfoundation.org/press/a2a-protocol-surpasses-150-organizations-lands-in-major-cloud-platforms-and-sees-enterprise-production-use-in-first-year). |  |
| Agent evals | Demystifying evals for AI agents (Anthropic, Jan 2026) | Free | [anthropic.com/engineering/demystifying-evals-for-ai-agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) | Tasks, trials, graders, transcripts vs outcomes; capability vs regression evals. | ⭐ |
| Agent course (free) | LangChain Academy: Introduction to LangGraph | Free | **link not verified** (referenced in the LangGraph README as a free course) | Good structured walkthrough of state and persistence. |  |

## Phase 4: Production (LLMOps, safety, cost)

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Observability | Langfuse: LLM observability via OpenTelemetry | Free | [langfuse.com/docs/opentelemetry/get-started](https://langfuse.com/docs/opentelemetry/get-started) | Vendor-neutral tracing via OTel, with maps from GenAI semantic conventions. Pair with the spec: [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/). Confirm its self-hosting options in its docs (not verified here). | ⭐ |
| Open evaluation tooling | Inspect AI (UK AI Security Institute) | Free | [inspect.aisi.org.uk](https://inspect.aisi.org.uk/) | MIT-licensed eval framework with 200+ pre-built evals that run on any model; good for your cross-model portability suite. | ⭐ (for portability evals) |
| Gateway & routing | LiteLLM | Free (GitHub) | [github.com/BerriAI/litellm](https://github.com/BerriAI/litellm) | Route between local and hosted models, with fallbacks and cost tracking. |  |
| Observability | Building Applications with AI Agents, Ch. 10 "Monitoring in Production" | O'Reilly | […/ch10.html](https://www.oreilly.com/library/view/building-applications-with/9781098176495/ch10.html) | Agent-specific monitoring concerns. |  |
| LLMOps architecture | AI Engineering, Ch. 10 "AI Engineering Architecture and User Feedback" | O'Reilly | [oreilly.com/…/ch10.html](https://www.oreilly.com/library/view/ai-engineering/9781098166298/ch10.html) | Staged architecture: context enhancement, guardrails, routing, caching, orchestration, feedback. | ⭐ |
| LLMOps course | Applied AI Engineer: Build, Evaluate, and Operate Production LLM | Educative | [educative.io/courses/applied-ai-engineer](https://www.educative.io/courses/applied-ai-engineer) | Short (≈5 h) course with modules on deployment and responsible AI. |  |
| Security | OWASP Top 10 for LLM Applications (2025) | Free | [genai.owasp.org](https://genai.owasp.org) *(project site; per OWASP materials)* | Shared vocabulary: prompt injection, excessive agency, system prompt leakage, vector/embedding weaknesses, unbounded consumption. | ⭐ |
| Security | The lethal trifecta for AI agents (Simon Willison, Jun 2025) | Free | [simonwillison.net/2025/jun/16/the-lethal-trifecta](https://simonwillison.net/2025/jun/16/the-lethal-trifecta/) | The single most useful threat-modeling heuristic for agents. | ⭐ |
| Security | Building Applications with AI Agents, Ch. 12 "Protecting Agentic Systems" | O'Reilly | […/ch12.html](https://www.oreilly.com/library/view/building-applications-with/9781098176495/ch12.html) | Agent-specific vulnerabilities and defenses. |  |
| Guardrails | OpenAI "Building guardrails for agents" guide | Free | Listed on [developers.openai.com/resources/guides](https://developers.openai.com/resources/guides) | Vendor perspective on input/output safeguards; direct link not verified. |  |
| Cost / latency | OpenAI "Latency optimization" & "Production best practices" guides | Free | Listed on [developers.openai.com/resources/guides](https://developers.openai.com/resources/guides) | Practical latency and ops checklists; direct links not verified. |  |
| Cost / caching | Contextual Retrieval post, section on prompt caching | Free | [anthropic.com/news/contextual-retrieval](https://www.anthropic.com/news/contextual-retrieval) | Concrete example of using prompt caching to reduce preprocessing cost. |  |
| Eval gates | Demystifying evals (regression evals section) | Free | [anthropic.com/engineering/demystifying-evals-for-ai-agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) | Capability evals become regression suites. |  |
| Governance | NIST AI Risk Management Framework 1.0 (NIST AI 100-1) | Free | [nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-1.pdf](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-1.pdf) | Govern/Map/Measure/Manage vocabulary for customer security reviews. A GenAI profile (NIST AI 600-1, 2024) extends it; link not verified. | ⭐ |
| Governance | ISO/IEC 42001; EU AI Act | Free / paid | **link not verified**: search the official standard and EU texts | ISO/IEC 42001 is described as a certifiable AI management-system standard; the EU AI Act is binding regulation. Get legal review. |  |
| Observability *(book)* | Observability Engineering (Majors, Fong-Jones, Miranda) | O'Reilly | **link not verified**: search the title on O'Reilly | Classic grounding for tracing and high-cardinality debugging; your SRE instincts transfer. |  |

## Phase 5: Open-weights engineering

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Fine-tuning decision | What We've Learned…, Part III: Strategy | Free (O'Reilly Radar) | [oreilly.com/radar/…-part-iii](https://www.oreilly.com/radar/what-we-learned-from-a-year-of-building-with-llms-part-iii/) | Argues fine-tuning is heavy machinery to deploy only after other approaches fail. | ⭐ |
| SFT & LoRA | Hugging Face LLM Course, Chapter 11: Supervised Fine-Tuning | Free | [huggingface.co/learn/llm-course/chapter11/1](https://huggingface.co/learn/llm-course/chapter11/1) | Chat templates, SFT with TRL, LoRA, evaluation. | ⭐ |
| SFT & LoRA | Fine-tuning weeks of Ed Donner's course (QLoRA) | Udemy | [udemy.com/course/llm-engineering-master-ai-and-large-language-models](https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models/) | Hands-on QLoRA project (course listing shows a dedicated fine-tuning week). |  |
| Papers *(classics)* | LoRA ([2106.09685](https://arxiv.org/abs/2106.09685)); QLoRA ([2305.14314](https://arxiv.org/abs/2305.14314)); DPO ([2305.18290](https://arxiv.org/abs/2305.18290)) | Free | linked at left | Short, readable primary sources. |  |
| Fine-tuning (book) | AI Engineering, Ch. 7–8 (Finetuning; Dataset Engineering) | O'Reilly | [Chip Huyen book](https://www.oreilly.com/library/view/-/9781098166298/) | Best decision-oriented coverage, including data quality and synthetic data. |  |
| Fine-tuning (book) | LLM Engineer's Handbook (Iusztin, Labonne) | O'Reilly | **link not verified**: search title on O'Reilly | End-to-end LLM project with training and deployment. |  |
| Serving | vLLM documentation | Free | [docs.vllm.ai](https://docs.vllm.ai) · [GitHub](https://github.com/vllm-project/vllm) | Open-source serving with continuous batching, PagedAttention, quantization options (GPTQ/AWQ/FP8). | ⭐ |
| Serving theory | Efficient Memory Management for LLM Serving with PagedAttention (SOSP 2023) | Free | [arxiv.org/abs/2309.06180](https://arxiv.org/abs/2309.06180) | The key idea behind modern serving throughput. |  |
| Serving theory | How continuous batching enables 23x throughput (Anyscale) | Free | [anyscale.com/blog/continuous-batching-llm-inference](https://www.anyscale.com/blog/continuous-batching-llm-inference) | Intuitive explanation of batching. |  |
| Serving ops | vLLM on Kubernetes (AWS sample) | Free | [aws-samples.github.io/…/vllm](https://aws-samples.github.io/sample-genai-on-eks-starter-kit/components/llm-model/vllm/) | Concrete flags for GPU memory, tensor parallelism and quantization. Reference only. |  |
| Inference (book) | AI Engineering, Ch. 9 "Inference Optimization" | O'Reilly | [Chip Huyen book](https://www.oreilly.com/library/view/-/9781098166298/) | Model-level and service-level optimization overview. |  |
| Local models | Ollama and llama.cpp | Free (GitHub) | [ollama/ollama](https://github.com/ollama/ollama) · [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) | Local runners; llama.cpp covers GGUF and quantized models, Ollama is the easiest on-ramp. |  |
| Practical fine-tuning | Unsloth fine-tuning guide | Free | [docs.unsloth.ai/get-started/fine-tuning-guide](https://docs.unsloth.ai/get-started/fine-tuning-guide) · [GitHub](https://github.com/unslothai/unsloth) | Open-source LoRA/QLoRA/DPO/GRPO workflow for modest GPUs; the guide recommends starting with QLoRA on a small instruct model (vendor's own claim). Use alongside HF's course for neutral grounding. | ⭐ (for hands-on) |
| Fine-tune for tool calls | HF Agents Course, bonus unit: fine-tuning an LLM for function calling | Free | [huggingface.co/learn/agents-course](https://huggingface.co/learn/agents-course) | A concrete open-weights fine-tuning exercise tied to agents. |  |
| Prefix caching & serving options | vLLM on Kubernetes (AWS sample) | Free | [aws-samples.github.io/…/vllm](https://aws-samples.github.io/sample-genai-on-eks-starter-kit/components/llm-model/vllm/) | Lists prefix caching, multi-LoRA and quantization flags. |  |
| From-scratch *(optional)* | Build a Large Language Model (From Scratch) (Sebastian Raschka) | O'Reilly | [oreilly.com/library/view/-/9781633437166](https://www.oreilly.com/library/view/-/9781633437166) | Only if you want deeper intuition; not required. |  |
| Multi-agent | Building Applications with AI Agents, Ch. 8 | O'Reilly | […/ch08.html](https://www.oreilly.com/library/view/building-applications-with/9781098176495/ch08.html) | Honest trade-offs: single agents are often better when latency matters. | ⭐ |
| Advanced RAG | GraphRAG docs; Agentic RAG course | Free / Educative | see Phase 2 | Try only where naive hybrid RAG demonstrably fails. |  |

## Phase 6: Forward Deployed Engineer

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| What the job is | A Day in the Life of a Palantir Forward Deployed Software Engineer (2020) *(classic)* | Free | [blog.palantir.com/a-day-in-the-life-of-a-palantir-forward-deployed-software-engineer-45ef2de257b1](https://blog.palantir.com/a-day-in-the-life-of-a-palantir-forward-deployed-software-engineer-45ef2de257b1) | The origin story and rhythm of embedding with customers. Dated, but still the primary source. | ⭐ |
| What the job is today | What are Forward Deployed Engineers, and why are they so in demand? (Pragmatic Engineer) | Free (partly paywalled) | [newsletter.pragmaticengineer.com/p/forward-deployed-engineers](https://newsletter.pragmaticengineer.com/p/forward-deployed-engineers) | Covers Palantir's "Delta" origins and how AI labs and startups use the role. |  |
| Business model | Trading Margin for Moat: Why the FDE Is the Hottest Job in Startups (a16z, Jun 2025) | Free | [a16z.com/services-led-growth](https://a16z.com/services-led-growth) | Why services-heavy delivery can build defensibility in AI. |  |
| Counterpoint | Decagon Hit $100 Million Betting Against Forward Deployed Engineers (Newcomer) | Free | [newcomer.co/p/decagon-hit-100-million-betting-against](https://www.newcomer.co/p/decagon-hit-100-million-betting-against) | A founder's argument that long-term embedding signals product gaps. Read both sides. |  |
| Altitude of engagement | Forward Deployed Engineering needs a Forward Deployed Executive (Provectus, May 2026) | Free | [provectus.com/blog/forward-deployed-engineering-needs-a-forward-deployed-executive](https://www.provectus.com/blog/forward-deployed-engineering-needs-a-forward-deployed-executive) | Vendor perspective (note the bias) on engaging at business-unit KPI level. |  |
| Explaining AI to stakeholders | Building Effective AI Agents (Anthropic enterprise guide) | Free | [claude.com/resources/guides/building-effective-ai-agents](https://claude.com/resources/guides/building-effective-ai-agents) | Language and diagrams you can reuse in customer workshops. |  |
| Messy data | Hands-On LLMs, Ch. 8; AI Engineering Ch. 8 (dataset engineering) | O'Reilly | see Phases 1–2 | Retrieval and data-quality fundamentals for unstructured sources. |  |
| Messy data / systems | Designing Data-Intensive Applications (Martin Kleppmann) *(classic)* | O'Reilly | **link not verified**: search title on O'Reilly | Integration, consistency and data-flow thinking. |  |
| Deploying in restricted environments | vLLM on Kubernetes sample; Applied AI Engineer (Educative) | Free / Educative | [AWS sample](https://aws-samples.github.io/sample-genai-on-eks-starter-kit/components/llm-model/vllm/) · [Educative](https://www.educative.io/courses/applied-ai-engineer) | Self-hosting patterns for customers who can't use public APIs. |  |
| Enterprise platform thinking | Building AI Agent Platforms (O'Mahony, Nonnenmacher; early release) | O'Reilly | [preface (TOC not final)](https://www.oreilly.com/library/view/building-ai-agent/0642572243906/preface01.html) | Platform-level concerns: runtimes, lifecycle, evolution. Early release, so incomplete. |  |
| Customer discovery *(classic)* | The Mom Test (Rob Fitzpatrick) | O'Reilly / retail | **link not verified**: search the title | Interviewing technique that avoids polite false positives. |  |
| Staff-level influence *(classic)* | The Staff Engineer's Path (Tanya Reilly) | O'Reilly | **link not verified**: search the title | Cross-team influence and technical strategy. |  |
| Job intelligence | Current FDE postings at Palantir, OpenAI and Anthropic | Free | **link not verified**: use each company's careers page | Read 3–4 real postings and map each requirement to a gap in your portfolio. |  |

## Phase 7: Capstone (reuse + deepen)

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Architecture | AI Engineering, Ch. 10; Patterns for Building LLM-based Systems | O'Reilly / Free | [Ch. 10](https://www.oreilly.com/library/view/ai-engineering/9781098166298/ch10.html) · [Yan](https://eugeneyan.com/writing/llm-patterns) | Reference architecture and pattern checklist for your ADRs. | ⭐ |
| Durable execution | LangGraph durable execution docs | Free | [docs.langchain.com/…/durable-execution](https://docs.langchain.com/oss/python/langgraph/durable-execution) | Persistence, resume and idempotency design guidance. |  |
| Threat model | Lethal trifecta + OWASP Top 10 for LLM Apps | Free | see Phase 4 | Basis for the written threat model. |  |
| Evals | Anthropic agent evals + Hamel/Shreya workflow | Free | see Phases 2–3 | Eval design for all three layers. |  |
| Tracing | Langfuse OTel + OpenTelemetry GenAI conventions | Free | see Phase 4 | Instrumentation. Note the MCP 2026-07-28 spec also documents OpenTelemetry trace-context propagation in request metadata. |  |
| Practical build course | Applied AI Engineer (Educative); LLM Zoomcamp | Educative / Free | see earlier phases | Checklists to compare your finished system against. |  |

---

## Staff+ leadership, reliability add-ons and electives (added by QA)

| Topic | Resource | Platform | Link | Why | Start |
| --- | --- | --- | --- | --- | --- |
| Staff+ role & influence | StaffEng guides (Will Larson): staff-plus interviews, career ladders, where staff-plus fit in an org | Free | [staffeng.com/guides](https://staffeng.com/guides/) | The best free map of Staff+ expectations; use it to design your leadership deliverables. | ⭐ |
| Staff+ role & influence | Staff Engineer: Leadership beyond the management track (Larson) | Free site / book | [staffeng.com](https://staffeng.com/book) *(path cited by secondary sources)* | Book-length treatment. Pair with *The Staff Engineer's Path* (Reilly; link not verified). |  |
| Production readiness | Google SRE Book, Ch. 32: production readiness reviews | Free | [sre.google/sre-book/evolving-sre-engagement-model](https://sre.google/sre-book/evolving-sre-engagement-model) | Template for a PRR you can adapt to LLM systems. The book also has a postmortem-culture chapter ([table of contents](https://sre.google/sre-book/table-of-contents)). | ⭐ |
| Decision records *(classic, 2011)* | Documenting Architecture Decisions (Michael Nygard) | Free | [cognitect.com/blog/2011/11/15/documenting-architecture-decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) | The original ADR format. |  |
| Durable workflows | Temporal: OpenAI Agents SDK integration (Python) | Free | [docs.temporal.io/develop/python/integrations/openai-agents-sdk](https://docs.temporal.io/develop/python/integrations/openai-agents-sdk) | Agent loop runs in a workflow; model calls run as retryable activities. Docs note durability does not extend to MCP servers, which run independently. | ⭐ |
| Durable workflows | Temporal: AI framework integrations (Oct 2026) | Free | [temporal.io/blog/ai-framework-integrations](https://temporal.io/blog/ai-framework-integrations) | Lists integrations for several agent frameworks (including LangGraph); shows the pattern is framework-agnostic. |  |
| Agent identity & auth | MCP 2026-07-28 changelog (authorization changes) | Free | [modelcontextprotocol.io/specification/2026-07-28/changelog](https://modelcontextprotocol.io/specification/2026-07-28/changelog) | Notes OAuth client-registration changes. A third-party checklist recommends OAuth 2.1 with PKCE and short-lived scoped tokens for remote MCP: [MCP server security checklist](https://predictionguard.com/blog/mcp-server-security-checklist) *(vendor blog; verify)*. |  |
| Enterprise integration (FDE) | What FDE postings ask for (identity, cloud, data, evals) | Free | [tryexponent.com/blog/forward-deployed-engineer-resume](https://www.tryexponent.com/blog/forward-deployed-engineer-resume) | Skill list includes OAuth, SAML, SCIM, SSO, IAM, VPC, SOC 2, plus RAG, evals and orchestration. Use it as a gap checklist. |  |
| FDE market context | The rise of the AI forward-deployed engineer (TechTarget) | Free | [techtarget.com/searchenterpriseai/feature/The-rise-of-the-AI-forward-deployed-engineer](https://www.techtarget.com/searchenterpriseai/feature/The-rise-of-the-AI-forward-deployed-engineer) | Why labs and clouds embed engineers: the bottleneck moved from model access to deployment. |  |
| E1 NL-to-SQL | How we built Text-to-SQL at Pinterest (2024) | Medium | [medium.com/pinterest-engineering/how-we-built-text-to-sql-at-pinterest-30bad30dabff](https://medium.com/pinterest-engineering/how-we-built-text-to-sql-at-pinterest-30bad30dabff) | Real production lessons, including RAG-based table selection. A good use of your Medium subscription. | ⭐ |
| E2 Voice agents | LiveKit Agents documentation and repo | Free | [docs.livekit.io/agents](https://docs.livekit.io/agents/) · [github.com/livekit/agents](https://github.com/livekit/agents) | Open-source framework for realtime voice agents with pluggable speech and LLM components and turn detection. | ⭐ |
| E2 Voice agents | The real-time voice AI stack for agents (Deepgram, Aug 2026) | Free | [deepgram.com/blog/real-time-voice-ai-stack-agents-architecture-guide](https://deepgram.com/blog/real-time-voice-ai-stack-agents-architecture-guide) | Latency-budget thinking and cascaded vs speech-to-speech trade-offs. Vendor blog; verify claims. |  |
| E3 Document AI | Docling (IBM Research, LF AI & Data) | Free | [github.com/docling-project/docling](https://github.com/docling-project/docling) · [docs](https://docling-project.github.io/docling) | MIT-licensed document parsing that can run locally, which suits air-gapped customers. Check each bundled model's licence. | ⭐ |
| E4 Enterprise search | Relevant Search (Doug Turnbull, John Berryman) *(classic)* | O'Reilly / Manning | **link not verified**: search the title | Classic on search relevance, co-authored by the author of *Prompt Engineering for LLMs*. |  |
| E4 Enterprise search | Hands-On LLMs Ch. 8 and Eugene Yan's patterns (hybrid retrieval) | O'Reilly / Free | see Phase 2 | Modern retrieval on top of the classic craft. |  |
| E5 Compliance | NIST AI RMF 1.0 | Free | [nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-1.pdf](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-1.pdf) | Backbone for the control-to-evidence matrix. | ⭐ |
| AI cost allocation | FinOps for AI / usage metering and chargeback | Free | **link not verified**: search "FinOps Foundation AI" | Staff JDs ask for metering, showback/chargeback and spend optimization. Pair with your gateway's cost logs. |  |

---

# 4. Staying current

**Cadence that works:** \~2 hours a week. One newsletter skim, one primary-source read (an engineering post or spec change), one hands-on experiment (re-run a past eval on a new model). Re-run your Phase 1 bake-off whenever a major model ships.

**Primary sources (highest signal)**

- Anthropic Engineering blog (posts above live under `anthropic.com/engineering`); OpenAI's engineering posts (e.g., the harness-engineering article above).
- Model Context Protocol blog and changelog: [blog.modelcontextprotocol.io](https://blog.modelcontextprotocol.io/tags/mcp/); spec revisions are dated (latest I verified: 2026-07-28).
- A2A project docs: [a2a-protocol.org](https://a2a-protocol.org/latest/).
- Claude Code docs and Agent SDK docs: [code.claude.com/docs](https://code.claude.com/docs/en/agent-sdk/overview).

**Newsletters and blogs**

- [Latent Space](https://latent.space/): swyx and Alessio Fanelli's publication and podcast for AI engineers; also associated with the AI Engineer conference series.
- [Simon Willison's Weblog](https://simonwillison.net/): fast, careful notes; essential on prompt injection.
- [Hamel Husain's blog](https://hamel.dev/) and [Eugene Yan's writing](https://eugeneyan.com/start-here): evals, applied patterns, ML systems.
- [Import AI](https://importai.substack.com) (Jack Clark): research and policy; [Interconnects](https://interconnects.ai) (Nathan Lambert): open models and post-training.
- [Pragmatic Engineer](https://newsletter.pragmaticengineer.com/): how AI changes engineering orgs and roles (including FDEs).
- [AI Engineer talks](https://ai.engineer/talks/what-we-learned-from-a-year-of-building-with-llms): conference talks from the AI Engineer series.
- **Open-weights tracking:** watch release notes for [llama.cpp](https://github.com/ggml-org/llama.cpp) and [vLLM](https://github.com/vllm-project/vllm), and the model cards on Hugging Face. Treat "best open model" lists as hypotheses: the Oct–Nov 2026 roundups I read rank models differently, so re-run your own bake-off (Phase 1) when a promising release lands.

**People worth following**

Simon Willison (security/tooling), Hamel Husain and Shreya Shankar (evals), Eugene Yan (applied patterns), Chip Huyen (AI engineering book), Andrej Karpathy (fundamentals), Jay Alammar (visual explanations), Jack Clark (research/policy), Nathan Lambert (open models), Gergely Orosz (engineering orgs), the MCP lead maintainers (David Soria Parra, Den Delimarsky) for protocol changes, and the authors of the harness posts (Justin Young at Anthropic; Ryan Lopopolo at OpenAI).

**Communities**

LangChain Forum, vLLM community discussions, and the DataTalksClub LLM Zoomcamp community are active and beginner-friendly.

**Habits that keep you honest**

1. Keep a private "model change log" tied to your eval suite so you know when a new model improves or regresses your tasks.
2. Read changelogs, not just announcements (the MCP 2026-07-28 revision changed core assumptions many tutorials still teach).
3. Distrust single-number claims in marketing posts and secondary blogs; check the primary source and its methodology.
4. Treat Medium as the lowest-signal channel for this field. Use it for practitioner stories (see the two flagged above), not for facts about protocols or APIs.

---

# 5. Watchlist: what to keep watching, listening to and checking

Section 4 lists *what to read*. This section is the *watching layer*: video, podcasts, live trackers and release feeds, plus a routine that turns watching into decisions. Budget **about 2 hours a week** and **about 3 hours once a month**.

## 5.1 Live trackers (check monthly, and whenever a new model or provider change lands)

These disagree with each other. Use them to build a shortlist, never to make the final choice; your own evals decide.

| Tracker | What it tells you | Watch out for | Link |
| --- | --- | --- | --- |
| OpenRouter Rankings | Which models developers actually *use* (tokens processed through OpenRouter) | Measures popularity and usage, not quality | [openrouter.ai/rankings](https://openrouter.ai/rankings) |
| OpenRouter Models and Benchmarks | Catalog, prices, providers per model, and benchmark views | Provider-declared capabilities are claims; spot-check tool calling | [openrouter.ai/models](https://openrouter.ai/models) · [openrouter.ai/benchmarks](https://openrouter.ai/benchmarks) |
| Artificial Analysis | Independent intelligence, price and speed comparisons; good for cost/quality trade-offs | Index definitions change between versions (its founders discussed an upcoming index revision on the Latent Space podcast) | [artificialanalysis.ai/leaderboards/models](https://artificialanalysis.ai/leaderboards/models) |
| LMArena | Blind human-preference votes | Preference can reward style; check vote counts and uncertainty before trusting small rank gaps | [lmarena.ai/leaderboard](https://lmarena.ai/leaderboard) |
| SWE-bench | Agentic software-engineering task results | One domain only; scaffolding affects scores | [swebench.com](https://www.swebench.com) |
| Hugging Face Open LLM Leaderboard | *Do not use as current.* A secondary report says it was archived in June 2024; old bookmarks are stale | Stale rankings | n/a |

**Rule:** when a new open-weights model tops a list, add it to your Phase 1 bake-off harness and run *your* tasks (pinned provider, logged quantization) before changing anything.

## 5.2 Video queue (YouTube)

| Channel | Why watch | Link |
| --- | --- | --- |
| AI Engineer (conference talks) | The best single stream of practitioner talks on agents, evals, infra and FDE-style deployment | [youtube.com/@aidotengineer](https://www.youtube.com/@aidotengineer) |
| Andrej Karpathy | Few videos, but durable first-principles explanations | [youtube.com/@AndrejKarpathy](https://www.youtube.com/@AndrejKarpathy) |
| Hugging Face | Open-source model and tooling workshops; fits your open-weights focus | [youtube.com/@HuggingFace](https://www.youtube.com/@HuggingFace) |
| OpenRouter | Product and model-landscape updates for your hosted leg | [youtube.com/@OpenRouterAI](https://www.youtube.com/@OpenRouterAI) |
| DeepLearning.AI | Short courses and talks on new techniques and protocols | [youtube.com/@Deeplearningai](https://www.youtube.com/@Deeplearningai) |
| Lenny's Podcast | How AI products and evals work in real companies (the Hamel Husain / Shreya Shankar evals conversation is a good start) | [youtube.com/@LennysPodcast](https://www.youtube.com/@LennysPodcast) |
| Dwarkesh Patel | Long, rigorous interviews with frontier researchers and lab leaders | [youtube.com/@DwarkeshPatel](https://www.youtube.com/@DwarkeshPatel) |

**Starter queue (watch in this order):**

1. Karpathy, *Deep Dive into LLMs like ChatGPT*: [youtube.com/watch?v=7xTGNNLPyMI](https://www.youtube.com/watch?v=7xTGNNLPyMI)
2. AI Engineer talk, *What We Learned From a Year of Building With LLMs*: [ai.engineer/talks/what-we-learned-from-a-year-of-building-with-llms](https://ai.engineer/talks/what-we-learned-from-a-year-of-building-with-llms)
3. AI Engineer talk, *Building Blocks for LLM Systems & Products* (Eugene Yan): [ai.engineer/talks/building-blocks-for-llm-systems-products](https://ai.engineer/talks/building-blocks-for-llm-systems-products)
4. Latent Space, *Artificial Analysis: independent LLM evals as a service* (how benchmarks are built, and why you still need your own): [latent.space/p/artificialanalysis](https://latent.space/p/artificialanalysis)
5. Latent Space, *OpenRouter: from Seed to Stripe* (Sep 2026; how the router you'll use thinks about multi-model routing). Find it via [latent.space/podcast](https://www.latent.space/podcast); direct episode link not verified.
6. Lenny's Podcast, *Why AI evals are the hottest new skill for product builders* (Husain and Shankar). Search the title on the channel above.

## 5.3 Podcasts (commute and gym)

| Podcast | Best for | Link |
| --- | --- | --- |
| Latent Space | AI engineering, including a stream covering Forward Deployed Engineering. Separate feeds exist for engineering/FDE, science and "lightning" episodes: [feed directory](https://latent.space/pods) | [latent.space/podcast](https://www.latent.space/podcast) |
| TWIML AI Podcast | Long-running technical interviews with ML practitioners | [twimlai.com/podcast/twimlai](https://twimlai.com/podcast/twimlai/) |
| Dwarkesh Podcast | Frontier-lab perspective and long-horizon trends | [dwarkesh.com](https://www.dwarkesh.com/) |

## 5.4 Release and spec feeds (these change what you build)

| Feed | Why it matters | Link |
| --- | --- | --- |
| llama.cpp releases | New model-architecture support, quantization formats and server features arrive here first | [github.com/ggml-org/llama.cpp/releases](https://github.com/ggml-org/llama.cpp/releases) |
| vLLM repo (use the Releases tab) | Serving features, quantization and caching changes | [github.com/vllm-project/vllm](https://github.com/vllm-project/vllm) |
| Ollama repo (use the Releases tab) | Local model support and API changes | [github.com/ollama/ollama](https://github.com/ollama/ollama) |
| MCP blog and spec changelog | Spec revisions are dated; the 2026-07-28 revision removed sessions and deprecated several features | [blog.modelcontextprotocol.io](https://blog.modelcontextprotocol.io/tags/mcp/) · [changelog](https://modelcontextprotocol.io/specification/2026-07-28/changelog) |
| A2A project | Agent-to-agent protocol, now at v1.0 | [a2a-protocol.org/latest](https://a2a-protocol.org/latest/) |
| OpenTelemetry GenAI conventions | Tracing attribute names are still evolving, which affects your observability code | [opentelemetry.io/docs/specs/semconv/gen-ai](https://opentelemetry.io/docs/specs/semconv/gen-ai/) |
| OpenRouter docs index | Routing, privacy and provider-setting changes | [openrouter.ai/docs/llms.txt](https://openrouter.ai/docs/llms.txt) |

## 5.5 Trends I'd actively watch through 2027 (grounded in this research)

1. **Protocol churn:** MCP's move to a stateless core and its deprecations, and whether A2A and MCP converge in practice. Re-check any MCP tutorial's date.
2. **Harness engineering becoming standard practice:** watch for shared patterns (progress artifacts, agent-legible repos, executable constraints) and tooling that productizes them.
3. **The open-weights gap on agentic tasks:** roundups report frontier-scale open models close to closed ones on coding and agent benchmarks, while small local models still fail more often in agent loops. Re-measure on your own tasks each quarter.
4. **Provider and gateway behavior:** quantization, context-length and tool-support differences behind one model name, and how routers expose or control them.
5. **Evals tooling and practice:** open frameworks (e.g., Inspect AI) and how teams run regression suites across models.
6. **The FDE debate:** services-heavy deployment as a moat versus a sign of product gaps. Keep reading both sides.

## 5.6 A routine that turns watching into decisions

| When | Time | Do |
| --- | --- | --- |
| Daily | 10 min | One feed only (Latent Space or your newsletter). No new tools adopted on the day you read about them |
| Weekly (Friday) | 2 h | One talk from the starter queue or AI Engineer channel; skim release feeds; add anything interesting to your radar list |
| Monthly | 3 h | Check the trackers in 5.1; re-run your bake-off on any new shortlist model; update your model change log |
| Quarterly | Half day | Re-run the full portability matrix (capstone), review provider drift, retire one tool and trial one new one |

**Radar list template:** keep a single file with four columns: *Adopt / Trial / Assess / Hold*. A tool moves from Assess to Trial only if you can name the problem it solves and the eval that would prove it.

**Triggers that force a re-test:** a new model in your shortlist; a provider, price or quantization change behind your OpenRouter route; a spec revision (MCP, A2A, tracing conventions); a major runtime release (llama.cpp, vLLM, Ollama).

**Signal filters:** prefer primary sources (specs, engineering posts, model cards); check methodology behind any headline number; treat leaderboards as hypotheses; be wary of "X killed Y" content.

---

## Appendix: What I'd deliberately skip or defer

- Training transformers from scratch (unless you're curious), custom CUDA kernels, pretraining.
- Framework hopping. Pick two agent frameworks and the raw SDK; ignore the rest until a customer requires them.
- Multi-agent architectures before you have a single-agent baseline and an eval suite.
- Fine-tuning before you can show that prompting, retrieval and better tools have plateaued on your evals.
- Vector-database shopping. Start with Postgres + pgvector (or what you already run) and switch only on measured need.
- Vendor-specific lock-in features (proprietary tool formats, hosted memory, one-vendor eval dashboards) unless wrapped behind your adapter layer.
- Chasing the top open-model leaderboard slot. Pick two or three open families, learn their quirks deeply, and swap only when your evals say so.

---

## Appendix B: QA audit report (10 Nov 2026)

**What this is.** I can't spawn a separate independent agent, so this is a structured audit I ran as a distinct pass: (1) a checklist check against your original brief, (2) a mechanical scan of the document, (3) a benchmark against what real Staff/Principal AI-engineer and Forward Deployed Engineer postings ask for in 2026, and (4) a link audit.

**Honest conclusion.** No list can be proven exhaustive in a field that changes monthly. This one is **complete against your brief** and was **materially expanded after the audit** to cover Staff+ leadership, enterprise integration and several adjacent domains. Remaining gaps are listed below.

### 1. Brief conformance (your original 15 topic areas)

| Your requested area | Where it is covered | Verdict |
| --- | --- | --- |
| LLM fundamentals (tokens, embeddings, transformers, context, sampling, model choice) | Landscape A, O; Phase 1 | Covered |
| Prompt and context engineering | Landscape B; Phase 2 | Covered |
| Embeddings, vector DBs, RAG basic to advanced (chunking, hybrid, rerank, GraphRAG, agentic RAG) | Landscape C; Phases 2 and 5 (Track C); electives E3, E4 | Covered |
| Tool use / function calling / structured outputs | Landscape B, O; Phases 2 and 3 | Covered |
| Agents, memory, multi-agent, frameworks, when not to use agents | Landscape D; Phase 3 | Covered |
| MCP and interoperability (A2A) | Landscape E; Phase 3; Section 5.4 | Covered |
| Harness engineering | Landscape F; Phase 3 | Covered |
| Evaluation (evals, LLM-as-judge, benchmarks, regression) | Landscape G, O; Phases 2 to 4 | Covered |
| Fine-tuning (SFT, LoRA/QLoRA, DPO, distillation, hosted/open) | Landscape H; Phase 5 | Covered |
| Inference and serving (open models, quantization, vLLM/Ollama, latency/cost, caching) | Landscape I, O; Phase 5 | Covered |
| LLMOps (observability, versioning, deployment, monitoring, CI/CD) | Landscape J, R; Phase 4 | Covered |
| Safety, security, guardrails, governance | Landscape K, R; Phase 4; elective E5 | Covered, strengthened |
| AI-assisted development | Landscape L; Phase 0 | Covered |
| Staff-level architecture, build-vs-buy, ROI | Landscape M, P; leadership thread | Covered, strengthened |
| FDE curriculum | Landscape N, R; Phase 6; leadership thread | Covered, strengthened |

A mechanical keyword scan confirmed every named item in your brief (including LangGraph, CrewAI, OpenAI Agents SDK, Claude Agent SDK, DPO, QLoRA, vLLM, Ollama) appears in the document.

### 2. Gaps the audit found, and what was done

| # | Gap | Severity | Fix |
| --- | --- | --- | --- |
| G1 | Staff+ leadership was thin (ADRs and talks only): no technical strategy, RFC culture, mentoring, cross-functional work, readiness reviews, hiring, enablement | High | New landscape domain P; leadership deliverable after every phase; StaffEng, SRE-book PRR and ADR resources |
| G2 | Enterprise identity and integration (OAuth/OIDC, SSO/SAML, SCIM, IAM, VPC, SOC 2) missing for agents and FDE | High | Domain R; Phase 4 agent-identity deliverable; Phase 6 objectives; resources |
| G3 | Workflow engines and async/batch work beyond LangGraph (e.g., Temporal) | High | Domain R; Phase 3 project builds durability twice; verified Temporal docs |
| G4 | Vendor and model lifecycle (contracts, SLAs, deprecation, exit plans) and cost allocation (metering, showback/chargeback) | High | Domain P; Phase 4 vendor exit plan; Phase 5 cost model |
| G5 | Legal, IP and privacy engineering | Medium-high | Domain R rows; elective E5 |
| G6 | Adjacent application domains: NL-to-SQL, voice, document AI, enterprise search, multilingual, accessibility | Medium | Domain Q; new Phase 8 electives with projects and required evals; verified resources |
| G7 | Testing methodology: user simulation, annotation ops, safety/bias evals, load/chaos testing, resilience patterns | Medium | Domain R; Phase 4 deliverables |
| G8 | Data governance and lineage, model registry, capacity planning, IaC | Medium | Domain R |
| G9 | FDE field skills: stakeholder conflict, pattern extraction, data pipelines | Medium | Phase 6 objectives; domain R |
| G10 | Edge/on-device, video, robotics, world models | Low | Listed as Nice-to-know, deliberately not taught |

### 3. Consistency and link audit

- Fixed: hour totals disagreed (header said \~335, table said \~333); a resource heading still said "Advanced / specialization" after Phase 5 became core.
- The document has about 140 distinct URLs (142 at the time of this audit). **19 items are marked "link not verified"** by design, each with the exact title to search.
- Several "why" notes rely on secondary sources (roundups, vendor blogs); those are labeled.

### 4. Residual limitations (be aware of these)

1. **Currency:** model names, leaderboards and tool popularity change monthly; Section 5 is the mitigation, not a cure.
2. **Depth vs breadth:** the new domains P, Q and R are covered by the plan and projects, but resource depth for some rows is thinner than for core topics (several rows are marked link not verified).
3. **Out of scope on purpose:** pretraining/foundation-model research, RL research, robotics and world models, and deep domain specialties (healthcare, finance regulation). Add a domain-specific module if your job needs one.
4. **Not testable by me:** whether each paid-platform item is included in your subscriptions, and whether you will have GPU access. The plan degrades gracefully to hosted open-weights endpoints.
5. **Time realism:** with the electives and leadership deliverables the real calendar is longer than 37 weeks; the leadership deliverables are designed to be done *on top of* project work, not instead of it.