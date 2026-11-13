# 03 · RAG与检索

> 检索质量决定 LLM 应用的上限，生成只是把检索结果说出来。

**收录 47 条**（Twitter 34 / YouTube 13）　|　采集时间 2026-11-13　|　本页展示精选 33 条，完整清单见 `数据/全量清单.md`

---

## 一、这类知识解决什么问题

RAG（检索增强生成）解决的是「模型不知道你的私有数据」这个问题。它的工程复杂度被普遍低估：多数团队花 80% 时间调提示词，但效果瓶颈通常在检索环节——检索没召回，再好的模型也答不出。

一条完整的 RAG 链路包含：解析（PDF/HTML/表格）→ 切分（chunking）→ 嵌入（embedding）→ 索引（向量库/关键词库）→ 检索（向量/关键词/混合）→ 重排（rerank）→ 组装上下文 → 生成 → 引用。

每一环都有独立的评测指标。把整条链路当一个黑盒调，是最常见的失败原因。

## 二、核心知识骨架

**切分策略**　固定长度切分简单但会切断语义；按结构切分（标题、段落、代码块）效果好但依赖解析质量。工程实践：保留重叠窗口（overlap），并在每个 chunk 头部附上来源元信息（文档名、章节），否则模型无法引用。

**混合检索**　纯向量检索擅长语义相似，弱于精确匹配（型号、编号、专有名词）；关键词检索（BM25）相反。生产系统普遍采用混合检索 + 重排。

**重排的价值**　向量检索返回 top-50，重排模型从中选 top-5。这一步通常带来最明显的准确率提升，成本却远低于换更大的生成模型。

**评测要分层**　检索层看 recall@k、MRR；生成层看忠实度（faithfulness，答案是否来自检索内容）、相关性、引用准确性。混在一起测，无法定位问题。

**反直觉发现**　有研究指出，给 Agent 硬加向量库有时会降低表现——因为检索引入的无关内容稀释了上下文。检索该不该加，要用数据判断。

## 三、常见误区

- **只调 top_k**。top_k 从 3 调到 10 可能召回更多，但也会引入噪音。要配合重排才有意义。
- **忽略解析质量**。PDF 里的表格、双栏排版、页眉页脚，解析错了后面全错。
- **不做引用**。没有出处的答案无法验证，用户不会信任。
- **用生成模型掩盖检索缺陷**。模型可以「编得像」，但那不是 RAG 成功。

## 四、精选内容

### Twitter（34 条，展示前 20）

#### 1. If I had to learn GenAI from scratch in 2026,  this is the resource st…
`@SCR01111` · ♥ 44 · 收藏 75 · 2026-08-08 · [原文](https://x.com/SCR01111/status/2086150096740389370)

> If I had to learn GenAI from scratch in 2026,  this is the resource stack I'd follow:  📌
>
> fundamentals → LLMs → RAG → agents → fine-tuning → evaluation → production 
>
> ━━━━━━━━━━━━━━━━━━
>
> 1. GENAI FUNDAMENTALS
>
> FREE
>
> • Generative AI for Everyone — https://t.co/CmLFBHy8lC
> https://t.co/hqkDZrOGCz
>
> • Google ML Crash Course
> https://t.co/kpQ3bVGiAb
>
> • Hugging Face Learn
> https://t.co/dKahBuS4If
>
> PAID
>
> • https://t.co/CmLFBHy8lC
> • Coursera
> • Udemy
>
> ━━━━━━━━━━━━━━━━━━
>
> 2. DEEP LEARNING + TRANSFORMERS
>
> FREE
>
> • https://t.co/pc6Ghubhim
> https://t.co/sVCmWuwR4U
>
> • Hugging Face LLM Course
> https://t.co/Xm4cM5VI0o
>
> • Andrej Karpathy — Zero to Hero
> https://t.co/IxD7Dl5ikc
>
> PAID
>
> • https://t.co/CmLFBHy8lC
> • Full Stack Deep Learning
>
> ━━━━━━━━━━━━━━━━━━
>
> 3. LLMs
>
> FREE
>
> • Stanford CS224N
> https://t.co/QuXDEgkNwx
>
> • OpenAI Developer Docs
> https://t.co/tXwhEBMzGB
>
> • Anthropic Docs
> https://t.co/4vPFAtaIHf
>
> PAID
>
> • …

#### 2. AI Architecture: 12 Layers Behind Modern AI Applications (2026)
`@GauravGoyalAI` · ♥ 19 · 收藏 15 · 2026-11-13 · [原文](https://x.com/GauravGoyalAI/status/2121071701480054866)

> AI Architecture: 12 Layers Behind Modern AI Applications (2026)
>
> Building an AI application is much more than connecting an LLM to a chatbot.
>
> A production-ready AI system brings together 12 essential layers:
>
> 1. Business Problem – Define what AI needs to solve.
> 2. Data Layer – Collect data from databases, APIs and documents.
> 3. Data Processing – Clean, parse, chunk and validate data.
> 4. Embedding Layer – Convert data into vector representations.
> 5. Knowledge Layer – Store embeddings in vector databases.
> 6. Retrieval Layer – Find relevant context using semantic, keyword and hybrid search.
> 7. Prompt Engineering – Guide model behaviour with prompts and guardrails.
> 8. AI Model Layer – Use LLMs like GPT, Claude, Gemini and Llama.
> 9. Agent Layer – Enable planning, reasoning and multi-step execution.
> 10. Tool Layer – Connect APIs, databases, browsers and external tools.
> 11. Output Layer – Gene…

#### 3. RAG vs. Graph RAG vs. Agentic RAG, clearly explained!
`@_avichawla` · ♥ 590 · 收藏 872 · 2026-11-10 · [原文](https://x.com/_avichawla/status/2120098143606100024)

> RAG vs. Graph RAG vs. Agentic RAG, clearly explained!
>
> Standard RAG embeds documents into vectors and retrieves the most similar chunks via similarity search. For direct factual lookups, this works well.
>
> But it breaks down when a query needs to connect facts spread across multiple documents. Similarity search retrieves individual chunks, not the relationships between them.
>
> Graph RAG adds a knowledge graph layer on top.
>
> → During indexing, an LLM extracts entities and relationships from the documents.
>
> → During retrieval, the system traverses these connections instead of relying on embedding similarity alone.
>
> This is what enables multi-hop queries.
>
> Say a vector DB stores three facts about internal services:
>
> ↳ "The checkout service uses payments API."
> ↳ "The payments API runs on cluster-3."
> ↳ "Cluster-3 is scheduled for maintenance on Friday."
>
> Someone asks: "Will the checkout service…

#### 4. VECTOR DATABASES FOR LLMs
`@e_opore` · ♥ 229 · 收藏 162 · 2026-02-26 · [原文](https://x.com/e_opore/status/2027093488873898260)

> VECTOR DATABASES FOR LLMs
> PINECONE, WEAVIATE, & PGVECTOR FOR SEMANTIC SEARCH
>
> Modern LLM applications rely heavily on vector databases to enable semantic search, retrieval, & Retrieval-Augmented Generation (RAG).
>
> Traditional databases search using exact keywords.
> Vector databases search using meaning.
>
> WHAT IS A VECTOR DATABASE?
>
> → A vector database stores embeddings instead of plain text
> → Embeddings are numerical representations of meaning
> → Similar meanings are placed close together in vector space
> → This enables semantic similarity search
>
> Instead of matching exact words, vector databases answer:
>
> → Which documents mean something similar to this query?
> → Which chunks are contextually related?
>
> WHY VECTOR DATABASES ARE ESSENTIAL FOR LLMs
>
> LLMs do not automatically remember your private data.
>
> Vector databases allow you to:
>
> → Store embeddings of documents
> → Perform fast similarity se…

#### 5. 𝗠𝗼𝘀𝘁 𝗽𝗲𝗼𝗽𝗹𝗲 𝘁𝗵𝗶𝗻𝗸 𝗔𝗜 = 𝗖𝗵𝗮𝘁𝗚𝗣𝗧.
`@triptitips` · ♥ 226 · 收藏 191 · 2026-11-12 · [原文](https://x.com/triptitips/status/2120855495603011589)

> 𝗠𝗼𝘀𝘁 𝗽𝗲𝗼𝗽𝗹𝗲 𝘁𝗵𝗶𝗻𝗸 𝗔𝗜 = 𝗖𝗵𝗮𝘁𝗚𝗣𝗧.
>
> Not even close.
>
> ChatGPT is what you see.
>
> The real AI revolution is the massive ecosystem being built underneath it.
>
> This AI Stack Map captures 100+ tools powering modern AI applications.
>
> 𝗧𝗵𝗲 𝗺𝗼𝗱𝗲𝗿𝗻 𝗔𝗜 𝘀𝘁𝗮𝗰𝗸 𝗹𝗼𝗼𝗸𝘀 𝘀𝗼𝗺𝗲𝘁𝗵𝗶𝗻𝗴 𝗹𝗶𝗸𝗲 𝘁𝗵𝗶𝘀:
>
> LLMs → OpenAI, Claude, Gemini, Llama, Mistral
> Agentic AI → LangGraph, CrewAI, AutoGen, CAMEL, Agno
> RAG → LangChain, LlamaIndex, Haystack, GraphRAG
> Embeddings → OpenAI, Voyage, Cohere, BGE
> MCP → Connecting models with tools, data and systems
> AI Security → Guardrails, Presidio, Lakera, Prompt Security
> Observability & Evals → LangSmith, Langfuse, Phoenix, Ragas
> Memory → Redis, Mem0, Zep, Neo4j, Chroma
> Agent Frameworks → OpenAI SDK, Semantic Kernel, Google ADK, Bedrock
> Automation → n8n, Zapier, Make, Airflow, Prefect
> Vector Databases → Pinecone, Weaviate, Qdrant, Milvus, pgvector
>
> But here’s the part that matters more than t…

#### 6. 8 AI model architectures, visually explained:
`@_avichawla` · ♥ 114 · 收藏 142 · 2026-11-07 · [原文](https://x.com/_avichawla/status/2119035997321314815)

> 8 AI model architectures, visually explained:
>
> LLMs get so much attention that it's easy to overlook the rest of the field. Different inputs, tasks, and deployment constraints have led to very different model designs.
>
> Here are eight specialized AI model architectures:
>
> 1. LLM (Large language models)
>
> Text is split into tokens, mapped to embeddings, and processed through transformer layers. The model generates its response by predicting successive tokens.
>
> ↳ GPT, Claude, Gemini, Llama.
>
> 2. LCM (Large concept models)
>
> Moves prediction to the sentence level. Sentences become SONAR embeddings, and the model predicts the next sentence representation. Meta explored both autoregressive and diffusion-based versions.
>
> ↳ Meta's LCM research introduced this approach.
>
> 3. LAM (Large action models)
>
> Connects a user's request to actions in an environment. These systems interpret intent, plan steps, a…

#### 7. 🤖 Day 10/30 - AI Agents Series
`@e_opore` · ♥ 57 · 收藏 34 · 2026-08-14 · [原文](https://x.com/e_opore/status/2088169986133012841)

> 🤖 Day 10/30 - AI Agents Series
>
> In the last post, we learned how Retrieval-Augmented Generation (RAG) allows AI agents to retrieve relevant information from external knowledge before generating responses. Today, we'll explore the technology that makes semantic search possible—embeddings and vector databases.
>
> Embeddings and Vector Databases – FAISS, ChromaDB, and Pinecone
>
> Traditional databases search using exact keywords. AI agents, however, need to understand the meaning behind text, even when different words are used. This is made possible through embeddings and vector databases.
>
> What Are Embeddings?
>
> Embeddings are numerical vector representations of text generated by AI models.
>
> Instead of storing words as plain text, embeddings capture their semantic meaning.
>
> For example, the sentences:
>
> → "How do I deploy a Docker container?"
>
> → "How can I run my application using Docker?"
>
> have…

#### 8. If I had to land a $200K AI engineer job in 90 days, I would not get a…
`@Divyyanshishrma` · ♥ 504 · 收藏 787 · 2026-11-10 · [原文](https://x.com/Divyyanshishrma/status/2120123647109325212)

> If I had to land a $200K AI engineer job in 90 days, I would not get a degree.
>
> I would master these 10 GitHub repos.
>
> 1. awesome-llm-apps
> The production AI playbook. RAG, agents, multimodal apps, all in working code. 106K+ stars.
> Repo → https://t.co/50OD473w70…
>
> 2. LangChain
> The foundational framework. Used in production by Klarna, Replit, Elastic, and most AI startups in 2026.
> Repo → https://t.co/e8juC9sbHg…
>
> 3. LangGraph
> The orchestration layer powering production agents. The skill on every senior AI engineer job description.
> Repo → https://t.co/e8juC9sbHg…
>
> 4. CrewAI
> Multi-agent coordination. The framework most Fortune 500 teams reach for first.
> Repo → https://t.co/Ixp7pBR3sD…
>
> 5. Ollama
> Run any open-source LLM on your own machine. The fastest way to learn how models actually work.
> Repo → https://t.co/x4D63YcRCg
>
> 6. awesome-mcp-servers
> MCP is the standard every major AI lab adopted i…

#### 9. Today, I'm launching https://t.co/5XKAsmIuam:
`@adxtyahq` · ♥ 1.9k · 收藏 2.2k · 2026-10-24 · [原文](https://x.com/adxtyahq/status/2114000416245703087)

> Today, I'm launching https://t.co/5XKAsmIuam:
>
> - The hands-on way to learn AI engineering: LLMs, RAG, agents, evals and inference.
> - 50+ interactive labs. Tweak tokenizers, attention, embeddings and KV caches live.
> - Train a neural network in your browser and watch it learn.
> - Build agentic workflows with tool calls, memory, guardrails and bounded autonomy.
> - Ship RAG that works: chunking, hybrid search, reranking, and LLM-as-a-judge evals.
> - Production AI: model routing, fallbacks, quantization, caching and cost per request.
> - 490+ interview questions, reported from OpenAI, Anthropic, Google, Meta, Amazon, Microsoft, Mistral and 50+ more.
> - Every answer is cited from papers and books, or measured on real models.

#### 10. An agent's memory can be a file you back up and move between machines,…
`@cutetoxicguy` · ♥ 0 · 收藏 0 · 2026-11-09 · [原文](https://x.com/cutetoxicguy/status/2119852529907544484)

> An agent's memory can be a file you back up and move between machines, rather than another database service you have to keep running. 💾
>
> Memvid packages content, metadata and search structures into a portable .mv2 file, with a Rust core and interfaces for other application stacks.
>
> The project describes its storage as an append-only sequence of immutable “Smart Frames,” each carrying content information, timestamps and checksums. The current design borrows an organizational idea from video encoding; it is not a requirement to turn your documents into a playable video.
>
> 🧩 The basic developer workflow is tangible:
>
> ▪️ Create a memory file.
> ▪️ Add documents with titles, source URIs and tags.
> ▪️ Commit the changes.
> ▪️ Search and retrieve matching content.
> ▪️ Inspect entries through timeline-oriented queries.
>
> The repository includes an example that performs those steps and reports whether th…

#### 11. RAG vs. Vectorless RAG, clearly explained!
`@_avichawla` · ♥ 650 · 收藏 750 · 2026-10-30 · [原文](https://x.com/_avichawla/status/2116069319205663159)

> RAG vs. Vectorless RAG, clearly explained!
>
> In a standard RAG setup, documents are split into chunks, embedded, and stored in a vector DB. The query gets embedded too, and the top-k closest chunks go to the LLM.
>
> The problem is that similarity and relevance answer different questions.
>
> For instance, five annual reports from one company can likely follow the same template every year, and only the numbers can change.
>
> So a FY2022 question can lie just as close to an FY2025 chunk, and the LLM might answer from the wrong year.
>
> Cross-references introduce another problem. If a chunk mentions "see Appendix G" but looks nothing like the table in Appendix G, that table will never make it to the top-k.
>
> Vectorless RAG drops the embedding model and the vector DB, and replaces similarity search with LLM reasoning instead.
>
> The preprocessing step parses each document's layout into a ToC tree index. …

#### 12. someone is paying $68,800 a month to an llm to find sentences that are…
`@leopardracer` · ♥ 40 · 收藏 23 · 2026-11-12 · [原文](https://x.com/leopardracer/status/2120840196883132884)

> someone is paying $68,800 a month to an llm to find sentences that are already sitting in their own documents. i realized it when a model with zero output tokens did the same job for $65
>
> i ran one file through three models at the same time to check: claude opus 5.5, claude haiku 5.5, and xerj reader, which can't generate text at all
>
> → file → offer_thread.eml, 412 lines, 14 emails
> → question → how much do i get if anthropic ipos
> → xerj reader → done in 80 ms
> → opus → still waiting for its first token
> → haiku → still waiting for its first token
>
> reader didn't write anything back. it highlighted line 287, characters 14 to 96, right in the email
>
> → output tokens → 0
> → cost → $0.0000065
> → made up values → none, it can only return text that's already in the file
>
> opus started typing 5 seconds later and haiku 7 seconds later. both gave the same answer, reworded and longer
>
> → opus → $0.00688
> →…

#### 13. New short course on sophisticated RAG (Retrieval Augmented Generation)…
`@AndrewYNg` · ♥ 2.5k · 收藏 1.7k · 2023-11-29 · [原文](https://x.com/AndrewYNg/status/1729924040230629485)

> New short course on sophisticated RAG (Retrieval Augmented Generation) techniques is out! Taught by @jerryjliu0 and @datta_cs of @llama_index and @truera_ai , this teaches advanced techniques that help your LLM generate good answers. 
>
> Topics include:
> - Sentence-window retrieval, which retrieves not just the most relevant sentence, but a window of sentences around it for higher quality context.
> - Auto-merging retrieval, which organizes your document into a hierarchical tree structure, where each parent node's text is split among its child nodes. Based on the relevance of the child nodes to a user query, this lets you better decide whether the entire parent node should be provided as context to the LLM.
> - Evaluation methodology for separately evaluating the quality of the key steps of RAG (context relevance, answer relevance, groundedness) so that you can perform error analysis, identify …

#### 14. 🚀 𝗠𝗮𝘀𝘁𝗲𝗿 𝗥𝗔𝗚 𝘄𝗶𝘁𝗵 𝗔𝘇𝘂𝗿𝗲 – 𝗳𝗿𝗼𝗺 𝗱𝗼𝗰𝘂𝗺𝗲𝗻𝘁𝘀 𝘁𝗼 𝗮𝗻𝘀𝘄𝗲𝗿𝘀
`@AiswaryaVenkit1` · ♥ 48 · 收藏 17 · 2026-11-07 · [原文](https://x.com/AiswaryaVenkit1/status/2118954099563700386)

> 🚀 𝗠𝗮𝘀𝘁𝗲𝗿 𝗥𝗔𝗚 𝘄𝗶𝘁𝗵 𝗔𝘇𝘂𝗿𝗲 – 𝗳𝗿𝗼𝗺 𝗱𝗼𝗰𝘂𝗺𝗲𝗻𝘁𝘀 𝘁𝗼 𝗮𝗻𝘀𝘄𝗲𝗿𝘀
>
> Retrieval-Augmented Generation (RAG) isn’t magic.
> It’s a well-designed pipeline — and Azure gives you all the building blocks.
>
> 𝗧𝗵𝗶𝘀 𝘃𝗶𝘀𝘂𝗮𝗹 𝗯𝗿𝗲𝗮𝗸𝘀 𝗥𝗔𝗚 𝗶𝗻𝘁𝗼 𝟰 𝗰𝗹𝗲𝗮𝗿 𝘀𝘁𝗮𝗴𝗲𝘀 👇
>
> 🔹 Indexing
> • Parse documents (PDFs, docs, etc.)
> • Chunk text for better recall
> • Generate embeddings using Azure embedding models
> • Store them in a vector database
>
> 🔹 Retrieval (R)
> • User query → encoded into embeddings
> • Semantic search over the vector store
> • Fetch the most relevant chunks
>
> 🔹 Augmentation (A)
> • Combine retrieved chunks
> • Build context-aware prompts
> • Ground the query with trusted data
>
> 🔹 Generation (G)
> • Send prompt + context to the LLM
> • Generate accurate, hallucination-reduced responses
>
> 💡 Key takeaway:
> RAG is not about “asking the LLM harder questions”
>
> it’s about feeding the LLM the right context at the right time.
>
> If you’re building enterpr…

#### 15. here are some points you should know about embeddings beyond just usin…
`@Prajhan_026` · ♥ 9 · 收藏 3 · 2026-11-11 · [原文](https://x.com/Prajhan_026/status/2120528391409483814)

> here are some points you should know about embeddings beyond just using them as vectors for search.
>
> mteb is not one test. it measures retrieval, classification, clustering, reranking, pair classification, semantic similarity, and summarization. a model can be strong overall while still being weak at search.
>
> embeddinggemma reaches 69.67 on MTEB english v2 at 768 dimensions, despite having only about 300M parameters and this shows smaller models can be surprisingly competitive.
>
> qwen3-embedding scales from 0.6B to 8B parameters, with published english v2 scores from 70.70 to 75.22. bigger models improve, but not always in proportion to their size. 
>
> vector size changes storage cost. embeddingGemma supports 768, 512, 256, and 128 dimensions. smaller vectors save storage, but quality gradually declines. 
>
> moe is entering embedding models. nomic embed v2 stores 475M parameters but activates…

#### 16. 🔎 Search text, images and video frames with one encoder, then use the …
`@cutetoxicguy` · ♥ 0 · 收藏 0 · 2026-11-13 · [原文](https://x.com/cutetoxicguy/status/2121045391638696446)

> 🔎 Search text, images and video frames with one encoder, then use the same model to choose between predefined answers.
>
> MetaEncoder v1 from Meta combines multimodal retrieval and closed-set decisions in a roughly 28.4B-parameter model using the Muse Glimmer architecture.
>
> Its interface has three main operations:
>
> ▪️ encode: turn content into an embedding for storage, clustering or comparison.
> ▪️ retrieve: rank a collection against a query.
> ▪️ decide: answer predefined questions about a state, with scores and probabilities.
>
> 🧩 Each embedding is a normalized 6,656-dimensional vector. An input can contain text, images, video frames or a mixture of them.
>
> Instructions tell the model what the representation is for. A document and a search query can use different instructions, and the same image can produce different embeddings depending on the task.
>
> For video, the wrapper accepts a list of f…

#### 17. This is one of the best videos I've seen outlining all the different c…
`@jerryjliu0` · ♥ 716 · 收藏 905 · 2024-07-27 · [原文](https://x.com/jerryjliu0/status/1816989760474481117)

> This is one of the best videos I've seen outlining all the different components of a multimodal RAG architecture 🧑‍🏫
>
> Multimodal data contains both images and text. Text is indexed via text embeddings and stored in a vector database. With images you have a lot more flexibility, and can choose to do text extraction for text embedding or use a CLIP-like model for image embeddings. During query-time you can retrieve from each collection and do a final reranking at the end! 
>
> Check out @engineerrprompt's full YouTube video here: https://t.co/vzP3VlWv8D
>
> Colab: https://t.co/GjV2XqB2SU

#### 18. The BEST one there for Agentic AI.
`@swapnakpanda` · ♥ 455 · 收藏 681 · 2026-09-30 · [原文](https://x.com/swapnakpanda/status/2105171880043053217)

> The BEST one there for Agentic AI.
>
> Absolutely beginner friendly. Covers every topic.
>
> https://t.co/03jDhYP07L
>
>   - Langchain
>   - Langgraph
>   - RAG
>   - Vectorless RAG
>   - Deep Agents
>   - Guardrails
>   - LLM Evals
>   - LLM Gateways
>
> Give 30 mins to 1 hour everyday. You won't regret at all.

#### 19. BYE-BYE TO TRADITIONAL WEB SCRAPING!!
`@dyfwsammy` · ♥ 324 · 收藏 316 · 2026-11-10 · [原文](https://x.com/dyfwsammy/status/2120128054915731961)

> BYE-BYE TO TRADITIONAL WEB SCRAPING!!
>
> Someone just built PixelRAG.
>
> Instead of turning websites into plain text, it takes screenshots and uses a vision-language model to read information directly from the pixels.
>
> That’s a big deal.
>
> Traditional scraping often loses important information like:
>
> → PDFs
> → Tables
> → Layouts
> → Buttons
> → Columns
>
> All of that can disappear when a webpage is converted into plain text.
>
> PixelRAG takes a different approach:
>
> It indexes pages exactly as they appear to the human eye.
>
> The team has already built a visual index of Wikipedia with over 30 million screenshots.
>
> And it reportedly beats the best text-based RAG by 18.1% on QA.
>
> It also comes with a plugin for Claude Code.
>
> Basically, it gives Claude eyes.
>
> Pass it a URL, an arXiv paper, or a local website, and Claude doesn’t need to scrape the DOM.
>
> It can actually look at the page.
>
> Here’s how it works:
>
> …

#### 20. We just gave Claude Code, Codex and Cursor one shared brain.
`@daleverett` · ♥ 184 · 收藏 177 · 2026-11-01 · [原文](https://x.com/daleverett/status/2117001857201754605)

> We just gave Claude Code, Codex and Cursor one shared brain.
>
> One learns something, they all know it. One deletes 48,120 files, you hit undo.
>
> No vector database. No embeddings. We matched OpenAI’s best embedding model without using a single one.
>
> Introducing WonderDrive: one drive for every agent you run. Coming soon. https://t.co/CNjUluRjNK
>
> @ycombinator @fdotinc @daltonmeon

### YouTube（13 条，展示前 13）

#### 1. Vector Databases Full Course for Beginners – Embeddings, Similarity Search & ChromaDB
`freeCodeCamp.org` · ▶ 13.0万 · 1:39:33 · 9 days ago · [观看](https://www.youtube.com/watch?v=lR931Iz8Z6U)

> You will also explore the internal architecture of a vector database, including storage, indexing, and the IVF and HNSW index ...

#### 2. How AI Agent Memory Works?
`Aishwarya Srinivasan` · ▶ 2.0万 · 18:44 · 2 days ago · [观看](https://www.youtube.com/watch?v=mNng-_1HDB4)

> RAG: How they work together in context engineering - Read and write paths: Triggering memory retrieval and managing updates ...

#### 3. RAG Crash Course: 5 Levels from Basic to Agentic RAG
`AI with Hassan` · ▶ 1.6万 · 47:55 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=hYYk9S_8UXg)

> RAG crash course for beginners 2027, this complete RAG crash course walks you from basic Retrieval Augmented Generation ...

#### 4. How to Build RAG From Scratch in 50 Lines of Python - Full Tutorial
`Tech With Tim` · ▶ 8.2万 · 26:34 · 13 days ago · [观看](https://www.youtube.com/watch?v=f9uAG3AQGWM)

> This video is sponsored by TripleTen, a professional education platform designed to help tech professionals and career changers ...

#### 5. RAG Explained in One Video | No Code, No Maths
`WsCube AI & Tech School` · ▶ 1.0k · 21:29 · 4 weeks ago · [观看](https://www.youtube.com/watch?v=Q-B1ykI4Ojw)

> 🤖 What is RAG (Retrieval-Augmented Generation)? Learn everything in one video — No Code, No Maths!
>
> Ever wondered how AI ...

#### 6. Build an End-to-End Production RAG System with Guardrails, Memory, HITL, Observability & AWS Bedrock
`DSwithBappy` · ▶ 7.5万 · 时长未知 · 10 days ago · [观看](https://www.youtube.com/watch?v=q08xK7Jd1nM)

> This isn't just another basic RAG tutorial! We'll explore how to build a production-oriented AI application with AI Guardrails, ...

#### 7. Build Production-Ready Advanced RAG From Scratch
`Balaji Chippada` · ▶ 1.0万 · 44:21 · 7 days ago · [观看](https://www.youtube.com/watch?v=Qi1FcoRWEiw)

> Build Production-Ready Advanced RAG From Scratch.
>
> code Link - https://drive.google.com/file/d ...

#### 8. Let's Learn RAGAS! 🤯 | RAG Evaluation Complete Tutorial
`AI with Thiru` · ▶ 8.3k · 46:03 · 3 weeks ago · [观看](https://www.youtube.com/watch?v=MXNJu0_PlJU)

> Building RAG applications is not enough — you need to evaluate how well they actually perform!
>
> In this video, learn about ...

#### 9. Google OKF WON’T Replace RAG: Here’s How to combine them
`Tech with Homayoun` · ▶ 878 · 7:02 · 2 weeks ago · [观看](https://www.youtube.com/watch?v=4NZAiIcXINo)

> Need help building AI agents or RAG systems for your business? Book ...

#### 10. What Is RAG? How AI Uses Your Data | Malayalam
`Brototype Malayalam` · ▶ 8.1k · 19:41 · 20 hours ago · [观看](https://www.youtube.com/watch?v=wjlPq0TubTA)

> How does AI answer questions using your own documents? Learn how RAG connects large language models with relevant ...

#### 11. RAG in Practice: Evaluation, Failure Points, & What Actually Works with Ben Auffarth
`Manning Publications` · ▶ 818 · 时长未知 · Streamed 6 days ago · [观看](https://www.youtube.com/watch?v=LGllWCy5M7E)

> Join Ben Auffarth for a practical discussion of the challenges that come up when building and running retrieval-augmented ...

#### 12. Japanese Sakiori and Rag Rug Weaving – What's the Difference?
`Sakiori - the other Boro` · ▶ 9.0k · 7:36 · 2 days ago · [观看](https://www.youtube.com/watch?v=hnnCjb6VdD4)

> Many countries share a tradition of weaving with rags to make sturdy floor rugs. But in Japan, rags were woven into much finer ...

#### 13. How Vector Databases Find Similar Things
`Monis Yousuf` · ▶ 5.1k · 12:42 · 12 days ago · [观看](https://www.youtube.com/watch?v=m6UbzpgRgtU)

> What is a vector database, and how do vector databases ACTUALLY work? In this video, we cover the fundamentals of vector ...

## 五、推荐学习顺序

1. 先做最小链路：固定切分 + 向量检索 + 直接拼接，跑通端到端
2. 建检索评测集（问题 + 应召回的文档），量化 recall@k
3. 换更好的解析与切分，观察 recall 变化
4. 加混合检索与重排，再测
5. 最后调生成提示与引用格式

## 六、收录说明

**纳入标准**：正文或标题含 ≥2 个技术概念；推文长度 ≥110 字符；YouTube 时长 ≥5 分钟且播放 ≥800；
具备可迁移的方法、原理或判断标准，而非单纯的产品发布或情绪表达。

**排除标准**：八卦/社会新闻/致富/招聘/娱乐/营销话术/标题党（含 `in just N minutes`、`without coding`、
`goldmine`、`🤯` 等煽动性标记）/非英文与中文语种。

**局限**：Twitter 的 Top 搜索偏向高互动内容，可能遗漏低传播但高质量的长文；YouTube 播放量门槛
会滤掉小众但专业的内容。本文件是**索引与提炼**，不是原文存档，引用请以原始链接为准。
