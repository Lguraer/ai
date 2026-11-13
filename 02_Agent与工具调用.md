# 02 · Agent与工具调用

> Agent 的本质是把「模型输出」变成「系统动作」，难点全在边界控制。

**收录 94 条**（Twitter 61 / YouTube 33）　|　采集时间 2026-11-13　|　本页展示精选 40 条，完整清单见 `数据/全量清单.md`

---

## 一、这类知识解决什么问题

Agent 不是一个新产品品类，而是一种架构模式：模型在循环中决定调用哪个工具、拿到结果、再决定下一步，直到任务完成。它的能力上限由工具设计决定，可靠性上限由错误处理决定。

这个领域当前最热的三个话题是：**工具协议**（MCP 是否成为标准）、**上下文管理**（长任务中如何不爆上下文）、**编排方式**（单 Agent 循环 vs 多 Agent 分工）。

一个被反复验证的结论：**多 Agent 不是默认更优**。多个 Agent 之间传递信息会损失上下文，协调开销随数量上升。多数场景下，一个设计良好的单 Agent 加清晰的工具边界，优于三个互相调用的 Agent。

## 二、核心知识骨架

**工具调用三要素**　工具名（语义清晰、不易混淆）、参数 schema（严格类型、必填标注）、返回值（结构化、含错误码）。工具描述写得好不好，直接决定模型选得对不对。

**MCP 的定位**　Model Context Protocol 解决的是「工具接入的标准化」：把 N 个应用 × M 个工具的适配矩阵，降为 N + M。它不提升模型能力，只降低集成成本。2026 年的讨论已经从「要不要用」转向「怎么治理」——工具权限、调用审计、协议一致性测试。

**记忆的两层**　短期记忆 = 上下文窗口内的对话与工具结果；长期记忆 = 外部存储（向量库、结构化数据库、文件）。两者的失效模式不同：前者是溢出，后者是检索不准与写入污染。

**harness（运行框架）**　指包裹模型的那层工程代码：提示组装、工具分发、错误重试、循环终止条件、成本控制。业界讨论的「Agent 效果不好」，多数时候问题在 harness 而非模型。

**终止条件**　Agent 最常见的故障是无限循环与提前放弃。必须有明确的终止判据：最大轮次、预算上限、成功条件、失败升级路径。

## 三、常见误区

- **工具给太多**。工具数量超过十几个后，模型选择准确率明显下降。正确做法是分组、按场景动态暴露。
- **没有幂等设计**。Agent 重试时可能重复下单、重复发邮件。写操作必须带幂等键。
- **把权限交给 Agent**。Agent 拿着用户的完整权限执行，一旦被提示注入劫持，损失是真实的。最小权限原则在这里同样适用。
- **迷信多 Agent**。先证明单 Agent 做不到，再考虑拆分。

## 四、精选内容

### Twitter（61 条，展示前 20）

#### 1. If you learn AI Security Engineering, you will never be unemployed aga…
`@suraj_sharma14` · ♥ 2.9k · 收藏 5.7k · 2026-11-02 · [原文](https://x.com/suraj_sharma14/status/2117225221971415403)

> If you learn AI Security Engineering, you will never be unemployed again.
>
> I’ll do this.
>
> Stage 1: AI Attack Surfaces & Threat Modeling
> - Learn:  OWASP Top 10 for LLMs, STRIDE for AI, the "Confused Deputy" problem in agentic workflows & data flow mapping.
>
> - Practice:  Draw a complete threat model for an AI agent that reads emails and writes to a CRM, identifying every indirect injection and data exfiltration vector.
>
> - Why:  You cannot secure an agentic workflow if you don't understand how an attacker can hijack the agent's legitimate privileges.
>
> Stage 2: Advanced Prompt Injection & Jailbreaking
> - Learn:  Direct vs. Indirect injection, Crescendo attacks (multi-turn grooming), payload splitting and encoding evasion (Base64, ROT13).
>
> - Practice:  Build an automated harness that successfully bypasses a commercial LLM's system prompt using a multi-turn Crescendo strategy.
>
> - Why:  Basic "i…

#### 2. 90% of AI Engineering interviews in 2026 come down to these 7 points.
`@ConsciousRide` · ♥ 773 · 收藏 1.4k · 2026-06-17 · [原文](https://x.com/ConsciousRide/status/2067239474527166605)

> 90% of AI Engineering interviews in 2026 come down to these 7 points.
>
> 1.  LLM Fundamentals: tokenization, transformers & attention, fine-tuning (LoRA/QLoRA), context management, model selection
>
> 2.  RAG Systems: chunking strategies, embeddings, vector databases, retrieval & reranking, hallucination mitigation
>
> 3.  Agentic Workflows: tool calling & function calling, ReAct/Plan-Execute patterns, memory & state, multi-agent orchestration
>
> 4.  Inference Optimization: quantization (AWQ/GGUF), serving engines (vLLM/TGI), batching & KV cache, latency vs cost tradeoffs
>
> 5.  Evaluation & Observability: LLM-as-judge evals, custom metrics, A/B testing, drift detection, prompt/response logging
>
> 6.  MLOps Pipelines: experiment tracking, model versioning & registries, CI/CD for AI, data pipelines, deployment automation
>
> 7.  Production Realities: safety guardrails & prompt injection, scaling inference…

#### 3. Here’s my 2026 roadmap to becoming a production-ready AI/ML & Agentic …
`@Wasif14652883` · ♥ 1 · 收藏 0 · 2026-11-12 · [原文](https://x.com/Wasif14652883/status/2120970619496153297)

> Here’s my 2026 roadmap to becoming a production-ready AI/ML & Agentic AI Engineer, from system design and RAG to agents, serving, evals, and security.
>
> AI/ML & AGENTIC AI ENGINEER ROADMAP 2026
> │
> ├── 1. AI System Design Fundamentals
> │   ├── ML System Lifecycle (Data → Train → Deploy → Monitor)
> │   ├── HLD & LLD for AI Systems
> │   ├── Functional vs Non-Functional Requirements
> │   ├── Latency, Throughput & Cost Trade-offs
> │   ├── Build vs Buy (APIs vs Self-Hosted Models)
> │   └── Scalability & Reliability for AI Workloads
> │
> ├── 2. LLM & Foundation Model Engineering
> │   ├── Transformer & Attention Basics
> │   ├── Prompt Engineering & Structured Outputs
> │   ├── Fine-Tuning (LoRA, QLoRA, PEFT)
> │   ├── Model Selection & Routing
> │   ├── Context Windows & Token Economics
> │   └── Multimodal Models (Vision, Audio, Text)
> │
> ├── 3. RAG & Knowledge Systems
> │   ├── Chunking & Embedding Strategies
> │   ├── …

#### 4. Agentic AI Engineer Roadmap
`@neerajjj6785` · ♥ 72 · 收藏 26 · 2026-11-11 · [原文](https://x.com/neerajjj6785/status/2120425753380831714)

> Agentic AI Engineer Roadmap
>
> ├── 01 Python & Software Engineering
> │   ├── Advanced Python, Asyncio & Concurrency
> │   ├── Git, Linux, APIs & Backend Development
> │   └── OOP, Design Patterns & System Design
> │
> ├── 02 LLM Fundamentals
> │   ├── Transformer Architecture & Attention
> │   ├── Tokens, Embeddings & Context Windows
> │   └── LLM APIs, Inference & Model Selection
> │
> ├── 03 Prompt & Context Engineering
> │   ├── Prompt Design & Structured Outputs
> │   ├── Context Windows & Context Management
> │   └── Dynamic Context & Prompt Optimization
> │
> ├── 04 Tool Calling & Function Execution
> │   ├── Function Calling & Schema Design
> │   ├── External APIs & Tool Integration
> │   └── Parallel Tool Execution & Error Handling
> │
> ├── 05 RAG & Knowledge Systems
> │   ├── Embeddings & Vector Databases
> │   ├── Hybrid Search & Reranking
> │   └── Graph RAG & Retrieval Evaluation
> │
> ├── 06 Agent Architecture & Design Patt…

#### 5. 𝗜𝗙 𝗬𝗢𝗨 𝗪𝗔𝗡𝗧 𝗧𝗢 𝗨𝗡𝗗𝗘𝗥𝗦𝗧𝗔𝗡𝗗 𝗔𝗜 𝗔𝗚𝗘𝗡𝗧𝗦, 𝗟𝗘𝗔𝗥𝗡 𝗧𝗛𝗘𝗦𝗘 𝟵 𝗧𝗘𝗥𝗠𝗦
`@mayaislam_ai` · ♥ 178 · 收藏 123 · 2026-11-08 · [原文](https://x.com/mayaislam_ai/status/2119315305235370221)

> 𝗜𝗙 𝗬𝗢𝗨 𝗪𝗔𝗡𝗧 𝗧𝗢 𝗨𝗡𝗗𝗘𝗥𝗦𝗧𝗔𝗡𝗗 𝗔𝗜 𝗔𝗚𝗘𝗡𝗧𝗦, 𝗟𝗘𝗔𝗥𝗡 𝗧𝗛𝗘𝗦𝗘 𝟵 𝗧𝗘𝗥𝗠𝗦
>
> AI agents are becoming more than just chatbots.
>
> They can use tools, remember context, retrieve information, work with other agents, and execute multi-step tasks.
>
> But first, you need to understand the language behind them.
>
> 𝗛𝗘𝗥𝗘 𝗔𝗥𝗘 𝟵 𝗜𝗠𝗣𝗢𝗥𝗧𝗔𝗡𝗧 𝗧𝗘𝗥𝗠𝗦:
>
> 01 — 𝗛𝗔𝗥𝗡𝗘𝗦𝗦
> The loop that turns an LLM into an agent.
>
> Context → LLM → Tool Call → Result → Context
>
> It connects the model with skills, memory, prompts, APIs, browsers and other tools.
>
> 02 — 𝗠𝗘𝗠𝗢𝗥𝗬 & 𝗦𝗧𝗔𝗧𝗘
> Memory helps an agent remember information across tasks.
>
> → Short-term memory
> → Long-term memory
> → Current state
> → Open tasks
>
> 03 — 𝗥𝗔𝗚
> Retrieval-Augmented Generation helps an agent find relevant information before generating an answer.
>
> Question → Search → Relevant Data → LLM → Answer
>
> This helps ground responses in external knowledge.
>
> 04 — 𝗠𝗖𝗣
> Model Context Protocol provides …

#### 6. AI is not one technology.
`@Viveksahu1215` · ♥ 367 · 收藏 249 · 2026-11-09 · [原文](https://x.com/Viveksahu1215/status/2119626597494129054)

> AI is not one technology.
>
> It’s an entire ecosystem.
>
> And the deeper you go, the more you realize that ChatGPT or Claude is only one layer of a much bigger stack.
>
> At the foundation, you have:
>
> → Machine learning
> → Neural networks
> → Transformers
> → Computer vision
> → Large language models
>
> Then comes the infrastructure that makes these models useful:
>
> → Data & knowledge systems
> → Fine-tuning
> → Prompt engineering
> → RAG
> → Knowledge graphs
> → Model serving & inference
>
> But that’s still not the end.
>
> The next layer is where AI starts doing things:
>
> → AI agents
> → Tool orchestration
> → Multi-agent systems
> → Autonomous execution
> → Memory systems
> → Self-reflection & error recovery
>
> And then AI moves into real-world applications:
>
> → AI copilots
> → Personalization
> → Content generation
> → Code generation
> → Voice & video
> → Business automation
>
> But there’s one part that becomes more important as AI gets mo…

#### 7. 𝗜𝗙 𝗬𝗢𝗨 𝗪𝗔𝗡𝗧 𝗧𝗢 𝗨𝗡𝗗𝗘𝗥𝗦𝗧𝗔𝗡𝗗 𝗔𝗜 𝗔𝗚𝗘𝗡𝗧𝗦, 𝗟𝗘𝗔𝗥𝗡 𝗧𝗛𝗘𝗦𝗘 𝟵 𝗧𝗘𝗥𝗠𝗦
`@mayaislam_ai` · ♥ 84 · 收藏 36 · 2026-11-11 · [原文](https://x.com/mayaislam_ai/status/2120355046122824152)

> 𝗜𝗙 𝗬𝗢𝗨 𝗪𝗔𝗡𝗧 𝗧𝗢 𝗨𝗡𝗗𝗘𝗥𝗦𝗧𝗔𝗡𝗗 𝗔𝗜 𝗔𝗚𝗘𝗡𝗧𝗦, 𝗟𝗘𝗔𝗥𝗡 𝗧𝗛𝗘𝗦𝗘 𝟵 𝗧𝗘𝗥𝗠𝗦
>
> AI agents are becoming more than just chatbots.
>
> They can use tools, remember context, retrieve information, work with other agents, and execute multi-step tasks.
>
> But first, you need to understand the language behind them.
>
> 𝗛𝗘𝗥𝗘 𝗔𝗥𝗘 𝟵 𝗜𝗠𝗣𝗢𝗥𝗧𝗔𝗡𝗧 𝗧𝗘𝗥𝗠𝗦:
>
> 01 — 𝗛𝗔𝗥𝗡𝗘𝗦𝗦
> The loop that turns an LLM into an agent.
>
> Context → LLM → Tool Call → Result → Context
>
> It connects the model with skills, memory, prompts, APIs, browsers and other tools.
>
> 02 — 𝗠𝗘𝗠𝗢𝗥𝗬 & 𝗦𝗧𝗔𝗧𝗘
> Memory helps an agent remember information across tasks.
>
> → Short-term memory
> → Long-term memory
> → Current state
> → Open tasks
>
> 03 — 𝗥𝗔𝗚
> Retrieval-Augmented Generation helps an agent find relevant information before generating an answer.
>
> Question → Search → Relevant Data → LLM → Answer
>
> This helps ground responses in external knowledge.
>
> 04 — 𝗠𝗖𝗣
> Model Context Protocol provides …

#### 8. 𝗔𝗡𝗧𝗛𝗥𝗢𝗣𝗜𝗖 𝗖𝗟𝗔𝗨𝗗𝗘 𝗔𝗚𝗘𝗡𝗧𝗜𝗖 𝗔𝗜 𝗘𝗖𝗢𝗦𝗬𝗦𝗧𝗘𝗠 𝗘𝗫𝗣𝗟𝗔𝗜𝗡𝗘𝗗 🤖
`@AamirAnsar94694` · ♥ 51 · 收藏 20 · 2026-11-13 · [原文](https://x.com/AamirAnsar94694/status/2121101551074742645)

> 𝗔𝗡𝗧𝗛𝗥𝗢𝗣𝗜𝗖 𝗖𝗟𝗔𝗨𝗗𝗘 𝗔𝗚𝗘𝗡𝗧𝗜𝗖 𝗔𝗜 𝗘𝗖𝗢𝗦𝗬𝗦𝗧𝗘𝗠 𝗘𝗫𝗣𝗟𝗔𝗜𝗡𝗘𝗗 🤖
>
> Claude is more than an AI chatbot.
>
> Anthropic's ecosystem brings together models, agent frameworks, tools, memory, security, and infrastructure to build and deploy intelligent AI agents.
>
> Here are 7 important layers of the Claude ecosystem.
>
> 1. Core Models — The Intelligence Layer
>
> Claude models power reasoning, coding, and complex task execution.
>
> - Opus: Advanced reasoning and complex tasks.
> - Sonnet: Balanced speed and intelligence.
> - Haiku: Fast, lightweight responses.
>
> 2. Frameworks & Tools — Build AI Agents
>
> These tools help developers create and manage agentic workflows.
>
> - Claude Agent SDK
> - Claude API
> - MCP
> - Claude Managed Agents
>
> 3. Production & Workspace — Turn AI Into Action
>
> Claude connects AI capabilities with real-world applications.
>
> Examples:
>
> - Claude Code
> - Claude Cowork
> - Claude Design
> - Claude Security
>
> 4. Memory & …

#### 9. 🤖 Your AI Agent is MORE than just an LLM!
`@Officially_Dev_` · ♥ 31 · 收藏 9 · 2026-11-08 · [原文](https://x.com/Officially_Dev_/status/2119272109222580272)

> 🤖 Your AI Agent is MORE than just an LLM!
>
> 5 building blocks explain how modern AI agents actually work. 👇
>
> Most people talk about AI agents, but understanding what's happening behind the scenes is what separates AI users from people who can actually build AI systems.
>
> Here's the breakdown 🧵
>
> 🧠 1. LLM = THE BRAIN
>
> A Large Language Model understands instructions, reasons through problems, and generates responses.
>
> But an LLM alone doesn't automatically know your private business data or have access to every tool.
>
> 📚 2. RAG = THE KNOWLEDGE LIBRARY
>
> Retrieval-Augmented Generation helps an LLM retrieve relevant information from external knowledge sources.
>
> Instead of relying only on its existing knowledge, it can use your documents and data to generate more grounded answers.
>
> 🔌 3. MCP = THE CONNECTION LAYER
>
> Model Context Protocol provides a standardized way for AI applications to connect wi…

#### 10. wtf is an agent harness.
`@Saboo_Shubham_` · ♥ 602 · 收藏 867 · 2026-10-30 · [原文](https://x.com/Saboo_Shubham_/status/2116013734359703554)

> wtf is an agent harness.
>
> An agent is a model plus a harness.
>
> Model reads the context and picks the next action. The harness is all the code that turns that choice into work.
>
> Claude Code, Codex, Antigravity, OpenClaw and Hermes Agent are all harnesses.
>
> 8 common parts every agent harness shares:
>
> 1. The loop
> • Sends the context to the model and runs whatever tools it asks for
> • Feeds the results back in for the next step
> • Decides when the task is finished
>
> 2. Tools and sandbox
> • Give the model a terminal, files, a browser and your connected apps
> • Run every task inside its own isolated computer
> • Keep the damage from a bad command inside the sandbox
>
> 3. Subagents
> • Take side tasks off the main agent and run them in parallel
> • Start each one with a fresh context window
> • Send back only a short summary, so the main context stays clean
>
> 4. Context management
> • Decides what the model sees…

#### 11. TITLE
`@Romka2x` · ♥ 4 · 收藏 0 · 2026-11-12 · [原文](https://x.com/Romka2x/status/2120920332655554791)

> TITLE
> AI News: DNA Predictions by Cell, University AI Rules, and Always-On Agents
> https://t.co/4oEHZdlocz
>
> DESCRIPTION
> AI research, assessment guidance, education and veteran use, regional investment, and new workplace and developer models.
>
> AI-generation disclosure: This video uses AI-generated narration and visuals.
>
> Chapters
> 00:00 AI predicts DNA effects by cell type
> 00:30 OfS calls for AI-free assessments
> 01:05 AI homework use outpaces guidance
> 01:40 20,000+ claim free ChatGPT Plus
> 02:14 Databricks plans $3B AI expansion
> 02:54 Genie AI delegates ongoing work
> 03:25 Gemini AI scores bounded choices
> 03:55 ERNIE AI preview API is available
> 04:26 BotPsy targets on-device AI tools
>
> SOURCES
> https://t.co/LMX2yNdtBz — DeepMind
> https://t.co/O0uOL1hNRi — OfS
> https://t.co/zHTbd5Vub2 — OpenAI
> https://t.co/GzOFFVQkk2 — OpenAI
> https://t.co/4f63cDeDXz — Databricks
> https://t.co/M0rc5QqNOZ — Databrick…

#### 12. Andrej Karpathy: "90% of what AI twitter tells you to learn will be de…
`@0xMortyx` · ♥ 2.0k · 收藏 4.6k · 2026-06-01 · [原文](https://x.com/0xMortyx/status/2061491256107159736)

> Andrej Karpathy: "90% of what AI twitter tells you to learn will be dead in 6 months"
>
> 90% of what ai twitter tells you to learn dies in 6 months
>
> senior engineers already stopped chasing it
>
> the dead list: autogen, crewai, autonomous agent pitches, agent marketplaces, benchmark leaderboards, semantic kernel, dspy as a general framework, horizontal "build any agent" platforms, per-seat pricing for agents
>
> the pattern is obvious. demos that break in production. hype that never ships. frameworks that go viral on monday and vanish by spring
>
> what actually compounds:
> context engineering
> tool design
> orchestrator-subagent pattern
> eval discipline
> the harness mindset. harness > model, always
> mcp as the protocol layer
>
> the edge isn't the newest framework. it's staying a few steps ahead until your signal becomes everyone's mass-opinion
>
> book and study this

#### 13. RAG might already be becoming obsolete.
`@Suryanshti777` · ♥ 622 · 收藏 1.1k · 2026-05-15 · [原文](https://x.com/Suryanshti777/status/2055326692051390540)

> RAG might already be becoming obsolete.
>
> A month ago, Andrej Karpathy dropped a simple GitHub gist called “LLM Wiki.”
>
> Now the comments section looks like the birth of an entirely new AI category.
>
> 5000+ stars later, developers are rapidly building:
> • persistent AI memory systems
> • self-maintaining knowledge bases
> • multi-agent research environments
> • contradiction detection engines
> • AI-native company operating systems
> • local-first memory architectures
> • graph-based reasoning layers
> • evolving second brains
>
> And the craziest part?
>
> Most of them were built in DAYS.
>
> Because the core idea is insanely powerful:
>
> Instead of AI repeatedly retrieving raw chunks like traditional RAG…
>
> …the model continuously maintains a living knowledge system.
>
> Not temporary context.
>
> Persistent synthesis.
>
> The shift sounds subtle until you realize what it changes:
>
> RAG:
> retrieve → answer → forget
>
> LLM Wiki:…

#### 14. Stateful vs. Stateless MCP core.
`@DailyDoseOfDS_` · ♥ 96 · 收藏 102 · 2026-11-08 · [原文](https://x.com/DailyDoseOfDS_/status/2119459958198808965)

> Stateful vs. Stateless MCP core.
>
> (Anthropic's biggest MCP update)
>
> Let us explain what that means:
>
> Until this release, talking to an MCP server worked like a phone call. Both sides ran an initialize handshake, and the server returned a session id carried on every later request.
>
> That session was a live object inside one specific server process, holding the negotiated state.
>
> Picture a restaurant where the waiter who took the order is the only one who knows what was ordered. Everything works until that waiter goes home.
>
> Because that state lives in one process, a balancer could not spread requests across three MCP server instances. Teams pinned clients with sticky sessions or pushed session state into shared storage, which blocked autoscaling and made a single restart drop every open session.
>
> The latest update deletes all of it. The handshake and the Mcp-Session-Id header are gone, and…

#### 15. 𝐖𝐡𝐚𝐭 𝐢𝐬 𝐌𝐂𝐏 (𝐌𝐨𝐝𝐞𝐥 𝐂𝐨𝐧𝐭𝐞𝐱𝐭 𝐏𝐫𝐨𝐭𝐨𝐜𝐨𝐥)?
`@Ai_Vaidehi` · ♥ 1.1k · 收藏 1.1k · 2026-04-18 · [原文](https://x.com/Ai_Vaidehi/status/2045356965161836916)

> 𝐖𝐡𝐚𝐭 𝐢𝐬 𝐌𝐂𝐏 (𝐌𝐨𝐝𝐞𝐥 𝐂𝐨𝐧𝐭𝐞𝐱𝐭 𝐏𝐫𝐨𝐭𝐨𝐜𝐨𝐥)?
>
> Most AI agents are trapped inside their own walls.
> MCP is the protocol that connects them to the outside world data sources, tools, and workflows.
>
> 𝐖𝐡𝐚𝐭 𝐢𝐬 𝐌𝐂𝐏?
>
> • MCP is an open-source standard that connects AI applications to external systems like data sources, tools, and workflows.
> • It enables seamless integrations, allowing AI models like ChatGPT to access data, use tools, and perform tasks like web app creation or database queries.
> • MCP simplifies development, reducing complexity and time by providing a standardized way to connect AI systems to various resources.
> • It enhances AI capabilities, making models more powerful and personalized by allowing them to interact with external systems and data on behalf of users.
>
> 𝐁𝐞𝐟𝐨𝐫𝐞 𝐌𝐂𝐏
>
> LLM → Slack, Google Drive, GitHub (separate connections for each).
>
> Every integration is custom. Every tool require…

#### 16. HOLY F*CK, can't believe my eyes, someone leaked an entire reverse eng…
`@HoodieFade_` · ♥ 916 · 收藏 1.3k · 2026-11-02 · [原文](https://x.com/HoodieFade_/status/2117258560270655816)

> HOLY F*CK, can't believe my eyes, someone leaked an entire reverse engineering degree on GitHub.
>
> This free stack completely automates dissecting closed binaries, applications, and firmware using AI agents:
>
> 1. radare2 → Full binary analysis straight in the terminal.
> Replaces heavy GUI tools and lets you tear through code via scripts and CLI.
> Instant execution file analysis on remote headless servers.
>
> ↳ https://t.co/HLFQkpSs8X
>
> 2. Ghidra → Free decompiler matching high-end enterprise suites.
> NSA's open-source tool that completely covers commercial decompiler needs.
> Visual call graphs, structured control flow, and automated logic recovery.
>
> ↳ https://t.co/LCrxavTuqy
>
> 3. LLM4Decompile → Neural machine code translation into clean C.
> Specialized model that reconstructs variables and function structures after compiler passes.
> Turns stripped assembly into readable source code in seconds.
>
> ↳ h…

#### 17. holy f*ck... the best AI agents don't have a better model. they have b…
`@mika_systems` · ♥ 465 · 收藏 851 · 2026-11-07 · [原文](https://x.com/mika_systems/status/2119080680021692927)

> holy f*ck... the best AI agents don't have a better model. they have better notes
>
> Stanford University just published the whole method for free in a paper called Agentic Context Engineering. your agent reviews every run, writes down each lesson and adds it to its notes. it never rewrites them, so nothing it learns is ever lost
>
> same weights, zero fine-tuning. 38.6% → 64.2%, level with IBM's top production agent on Opus 5.5. fine-tuning for that jump costs $5,000-20,000. here it cost $0
>
> it comes down to 4 agents:
>
> → worker: does the task and saves the full log
> → critic: reads the log and marks every mistake and every win
> → teacher: turns each one into a one-line lesson
> → librarian: files the lesson into CLAUDE.md. merges duplicates, never deletes a line
>
> that's why asking your agent to "clean up" your CLAUDE.md is a mistake. every rewrite throws away lessons it already paid for
>
> paste th…

#### 18. What happens when an AI agent calls a tool? 🔧
`@rajmahato111` · ♥ 3 · 收藏 0 · 2026-11-07 · [原文](https://x.com/rajmahato111/status/2119131249013137839)

> What happens when an AI agent calls a tool? 🔧
>
> Watch the 11-step loop:
> → the model only REQUESTS a tool call
> → your app validates, authorizes, and asks a human for risky writes
> → only then does code hit the real API
> → the result goes back with the same call ID
>
> Rule of thumb: the model proposes, your code disposes.
>
> Based on the OpenAI function calling guide, Anthropic tool-use docs, and the MCP spec. Original diagrams, simplified teaching example.
>
> #AIEngineering #AIAgents #LLM #SoftwareEngineering #SystemDesign #FunctionCalling #MachineLearning #DevTips

#### 19. CLAUDE IS SO MUCH BETTER WHEN YOU USE MCP
`@rileybrown` · ♥ 4.5k · 收藏 1.1万 · 2025-06-10 · [原文](https://x.com/rileybrown/status/1932278373511753742)

> CLAUDE IS SO MUCH BETTER WHEN YOU USE MCP
>
> Both Claude Sonnet and Opus are Insanely smart Agents.
>
> But they can't read your mind.
>
> You need to Give it Context...
> (MCP = model CONTEXT protocol)
>
> Here is an example...
>
> Then a tutorial on how to give Claude access to MCP tools without writing a single line of code or copying any "JSON".
>
> TIME STAMPS: 
> 00:00 - Introduction
> 01:00 - First Example with @notionhq 
>              - Searching, Research, Scripting
> 02:53 - Generating 5 YouTube Thumbnails @heyglif 
>              - Adding Auto Comments to Notion
> 04:52 - Claude Project System Prompt
>              - (Will make follow up video)
> 05:13 - How to set up these MCP's

#### 20. holy f*ck, 3D DESIGNERS ARE NOT READY FOR WHAT’S COMING IN 2027.
`@thegreatest_sv` · ♥ 1.5k · 收藏 2.7k · 2026-11-07 · [原文](https://x.com/thegreatest_sv/status/2119129564047982889)

> holy f*ck, 3D DESIGNERS ARE NOT READY FOR WHAT’S COMING IN 2027.
>
> your next competitor might not even know how to use Blender.
>
> just ONE person with an AI agent could be building 3D models, materials and entire scenes while you’re still working on the first render.
>
> and the scary part? these tools are already on GitHub.
>
> 7 repos every 3D designer NEEDS to know before it’s too late:
>
> 1. MCP for Blender - GIVE CLAUDE CONTROL OF BLENDER
>
> let AI create 3D objects, edit materials, build scenes and render.
>
> https://t.co/nG4caKbVJg
>
> 2. BlendRelay MCP - YOUR AI 3D MODELING ASSISTANT
>
> create and edit Blender scenes using prompts, reference images and AI agents.
>
> https://t.co/SbUkcHDvGa
>
> 3. TRELLIS.2 - TURN IMAGES INTO 3D
>
> generate detailed 3D assets with complex shapes and realistic PBR materials.
>
> https://t.co/PK32fkHov9
>
> 4. Hunyuan3D - GENERATE 3D MODELS WITH AI
>
> turn reference images into text…

### YouTube（33 条，展示前 20）

#### 1. Build an AI Agent Evaluation System with DeepEval & LangChain (Full Project, step-by-step)
`AI Career Lab` · ▶ 12.1万 · 时长未知 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=eL9BRo-eFYY)

> If you're learning AI agents, LLM evaluation, tool calling, customer support automation, or backend AI engineering, this tutorial ...

#### 2. MCP Security Fundamentals Course
`freeCodeCamp.org` · ▶ 11.3万 · 时长未知 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=gYXLRMycVMk)

> Learn how to secure the Model Context Protocol (MCP), the standard that connects AI agents to tools and data. This course walks ...

#### 3. Build Your First AI Agent in 20 Minutes (No Coding)
`Metics Media` · ▶ 74.8万 · 21:14 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=rCkYVxOX-dI)

> A chatbot waits for your next message. An AI agent keeps working while you sleep. In this beginner tutorial, you'll build your first AI ...

#### 4. What is MCP ? | Completely Explained in 20 Minutes
`Apna College` · ▶ 53.3万 · 21:57 · 4 weeks ago · [观看](https://www.youtube.com/watch?v=GeVCpohRU8Y)

> Notes: https://dsa.apnacollege.in/studymaterial/mcp Get Placement Ready & learn from the best No.1 Online Tech ...

#### 5. What Is Loop Engineering? Building Better AI Agents
`IBM Technology` · ▶ 14.2万 · 10:48 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=DQBqh6gcx7U)

> 00:00 – Understand Loop Engineering vs Prompt Engineering 01:18 – Build an AI Agent Loop Around a Goal 02:30 – Define ...

#### 6. Complete Agentic AI Course From Scratch 🔥 | JavaScript, LLMs, LangChain, LangGraph, RAG & MCP
`VIRTUAL CODE` · ▶ 9.8万 · 7:59:01 · 4 weeks ago · [观看](https://www.youtube.com/watch?v=eynUbHweaQM)

> Complete Agentic AI Course From Scratch to Advanced | Build AI Agents with JavaScript Pay as you want ...

#### 7. Build Your FIRST AI Agent in Claude Code. (11 Minutes).
`Tristen O'Brien` · ▶ 34.6万 · 10:55 · 7 days ago · [观看](https://www.youtube.com/watch?v=ge2FmPeVsT8)

> Get started on the Max plan: https://clau.de/TristenObrien This video is sponsored by Anthropic. AI agents sound complicated, but ...

#### 8. AI Agent Memory Architecture: Structured SQL, Semantic Vector Search & Memory Bank
`Google Cloud Tech` · ▶ 8.6万 · 12:27 · 2 weeks ago · [观看](https://www.youtube.com/watch?v=8C4bh59FtsI)

> Code for this episode → https://g.dev/ai/fashion-mind In this episode, you'll learn the critical difference between Structured Facts ...

#### 9. RAG Explained for Beginners | Complete Flow from Documents to AI Answers | LangChain Tutorial
`Durga Software Solutions` · ▶ 6.2万 · 2:20:38 · 4 weeks ago · [观看](https://www.youtube.com/watch?v=OiY8M_sVszY)

> LangChain & Agentic AI - From Basics to Advanced tutorials || Day - 45 || by Mr. DURGA Sir On 13-10-2026 @7PM (IST) ...

#### 10. Are Vector Databases Still Needed? RAG, AI Agents & Search
`IBM Technology` · ▶ 52.8万 · 10:01 · 7 days ago · [观看](https://www.youtube.com/watch?v=rXFV1ivDe6c)

> Learn more about Vector Databases here → https://ibm.biz/~aHY62ka9p Do AI applications still need vector databases?

#### 11. Master Claude 2.0 & AI Agents (HyperFrames, Cowork & MCP Explained)
`The Cutting Edge School` · ▶ 7.5万 · 时长未知 · 2 weeks ago · [观看](https://www.youtube.com/watch?v=ZCPq7e8TklY)

> Welcome to our Chief AI Officer playlist, where we will teach you how to use the best AI tools to build AI literacy in your ...

#### 12. Grok Bot Tutorial for Beginners: How to Set Up Your First AI Agent
`Parker Prompts` · ▶ 4.8万 · 12:07 · 4 days ago · [观看](https://www.youtube.com/watch?v=xHYp3LvPCTA)

> Go from ABSOLUTE ZERO to using AI like the people who do this for a living   https://www.skool.com/ai-fluency In this video, ...

#### 13. I Got ChatGPT to Use KIttl & Design For Me! Full Tutorial!!
`Detour Shirts` · ▶ 5.8k · 15:40 · 2 days ago · [观看](https://www.youtube.com/watch?v=zkUlYmJ1AOI)

> Join The Shirtheads For More Design Tips https://www.skool.com/theshirtheads/about Try Kittl MCP: https://go.kit.tl/mbxb Use code ...

#### 14. This ONE AI Agent Will Make You So Productive It's Almost Unfair
`Sandeep Swadia` · ▶ 46.6万 · 16:25 · 8 days ago · [观看](https://www.youtube.com/watch?v=x9hrptrGGY0)

> Join my free newsletter → https://sandeepswadia.beehiiv.com/ Take us on your morning run or commute, follow us on Spotify: ...

#### 15. Is the Agent Harness Dead? — Jediah Katz, SpaceXAI
`AI Engineer` · ▶ 40.5万 · 18:15 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=7Y4sbZgBdnc)

> Is the agent harness dead? Jediah Katz (SpaceXAI, formerly Cursor's longest-tenured agent-harness engineer) explains what ...

#### 16. Agentic Engineering: 5 Terms Every AI Developer Should Know
`IBM Technology and IBM Developer` · ▶ 11.1万 · 11:41 · 2 weeks ago · [观看](https://www.youtube.com/watch?v=SIZsFGvtClM)

> ... breaks down five essential concepts behind agentic engineering, including prompt engineering, context engineering, AI agents, ...

#### 17. Agentic RAG Masterclass: This Changes the Game
`Aishwarya Srinivasan` · ▶ 2.5万 · 时长未知 · 4 weeks ago · [观看](https://www.youtube.com/watch?v=a8ngaK6pN_o)

> Learn how agentic RAG works, how agents retrieve information, evaluate what they find, and decide what to look for next.

#### 18. 7 AI Agent Architecture Design Laws of Senior AI Engineers
`Cloud X Berry` · ▶ 2.4万 · 13:59 · 8 days ago · [观看](https://www.youtube.com/watch?v=GVq_MQH2CNA)

> In this video, Cloud X Berry explains AI agent architectures in a simple and beginner-friendly way. A chatbot can tell you how to fix ...

#### 19. Fish Audio MCP Tutorial: Create AI Product Ads with Claude & AI Voice
`Digital Product Wizard` · ▶ 1.2万 · 5:20 · 3 days ago · [观看](https://www.youtube.com/watch?v=uvR4AgZ2S5o)

> Explore Fish Creative MCP: https://fish.audio/?fpr=3p5lil Fish Audio MCP Tutorial: AI Product Ads & Voice Generation Want to ...

#### 20. Multimodal AI agent architecture: Voice, vision, tools & memory
`Google Cloud Tech` · ▶ 1.1万 · 6:10 · 1 day ago · [观看](https://www.youtube.com/watch?v=gg-gXO0tIUY)

> Full Season 1 playlist → https://www.youtube.com/playlist?list=PLc2r9FdYHRlg What actually separates a real-time multimodal AI ...

## 五、推荐学习顺序

1. 先做一个「单轮工具调用」：模型选工具 → 执行 → 返回结果
2. 加循环与终止条件，把最大轮次、预算、超时写死
3. 补错误处理：工具失败、参数非法、结果为空，各走什么分支
4. 接入 MCP 统一工具层，做权限与审计
5. 最后才考虑多 Agent 编排与长期记忆

## 六、收录说明

**纳入标准**：正文或标题含 ≥2 个技术概念；推文长度 ≥110 字符；YouTube 时长 ≥5 分钟且播放 ≥800；
具备可迁移的方法、原理或判断标准，而非单纯的产品发布或情绪表达。

**排除标准**：八卦/社会新闻/致富/招聘/娱乐/营销话术/标题党（含 `in just N minutes`、`without coding`、
`goldmine`、`🤯` 等煽动性标记）/非英文与中文语种。

**局限**：Twitter 的 Top 搜索偏向高互动内容，可能遗漏低传播但高质量的长文；YouTube 播放量门槛
会滤掉小众但专业的内容。本文件是**索引与提炼**，不是原文存档，引用请以原始链接为准。
