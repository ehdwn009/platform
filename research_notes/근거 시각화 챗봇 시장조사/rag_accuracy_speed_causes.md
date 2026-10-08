# Why GPTs-style personal document RAG chatbots feel slow and inaccurate: causes, remedies, measurement (as of 2026-10-08)

Scope note: This file covers root causes of inaccuracy and latency in "upload documents, chat over them" assistants, the engineering remedies with measured impact, latency budgets, evaluation methods, and HCI evidence on citations/highlighting. Korean-specific parsing issues are intentionally left to another researcher. Most quantified numbers come from arXiv papers (2023–2026) and vendor engineering blogs; practitioner forum evidence is labeled anecdotal.

---

## Key Question 1: Accuracy failure taxonomy — which failures are most common in practice?

### Takeaway
Published post-mortems and annotated error studies consistently put the majority of RAG errors *before* the LLM: chunking (~29%) and retrieval (~32%) together account for roughly 60% of errors in the only large annotated study found (EACL 2026), with "context mismatch" (chunk boundaries cutting off needed context) and "missed retrieval" (answer exists but isn't retrieved) as the two single largest buckets; the generation-side errors that remain are dominated by *misinterpretation of retrieved context*, not pure fabrication. Parsing of complex layouts (tables, multi-column) is a separate upstream failure that degrades every parser tested on OmniDocBench.

### Cited Findings

**Canonical taxonomies**
- "Seven Failure Points When Engineering a RAG System" (Barnett et al., arXiv 2401.05856, Jan 2024; IEEE/ACM CAIN 2024) is an experience report from three case studies (research, education, biomedical; one used BioASQ with 15,000 documents and 1,000 QA pairs). The seven failure points: FP1 Missing Content (question not answerable from corpus, system answers anyway), FP2 Missed Top Ranked Documents (answer in corpus but ranked below top-k), FP3 Not in Context (retrieved but dropped during consolidation), FP4 Not Extracted (answer in context but LLM fails to pull it out, often due to noise/contradiction), FP5 Wrong Format, FP6 Incorrect Specificity, FP7 Incomplete Answers. Key takeaways: "validation of a RAG system is only feasible during operation" and robustness "evolves rather than being designed in at the start." — [arXiv 2401.05856](https://arxiv.org/pdf/2401.05856); [HF paper page](https://huggingface.co/papers/2401.05856)
- "Classifying and Addressing the Diversity of Errors in RAG" (Layer6 AI, arXiv 2510.13975, submitted 15 Oct 2025, v2 15 Jan 2026, EACL 2026) proposes a 16-type taxonomy across four stages and annotated 406 erroneous responses (377 confirmed, 92.9% agreement). Pipeline: 128-token fixed chunks with 25-token overlap, gte-large-en-v1.5 embeddings, top-k=8, rank-zephyr-7b reranker to top-5, Llama-3-8B-Instruct generator, on DragonBall-EN (3,108 questions) / DragonBall-ZH (3,601) / CLAPnq (4,946). — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)
- Stage-level error shares on DragonBall-EN (n=832 errors): **Chunking 29.7%** (E3 Context Mismatch 207, E2 Underchunking 32, E1 Overchunking 0), **Retrieval 31.5%** (E4 Missed Retrieval 229, E5 Low Relevance 33), **Reranking 7.1%** (E7 Low Recall 53, E8 Low Precision 6), **Generation 32.7%** (E13 Misinterpretation 178, E10 Fabricated Content 34, E12 Incomplete Answer 17, E16 Numerical Error 16, E14 Contextual Misalignment 14, E9 Abstention Failure 6, E11 Parametric Overreliance 4, E15 Chronological Inconsistency 3). — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)
- The same paper's case study: after applying chunking/retrieval/reranking fixes only, total errors fell 832 → 534 (−36%); Context Mismatch 207 → 78, Missed Retrieval 229 → 122, reranker Low Recall 53 → 28; generation errors were unchanged because no generation fix was applied. — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)
- Their automatic error classifier (RAGEC) only reached 57.8% accuracy at stage level and 40.3% at error-type level, i.e., automated root-cause attribution is still unreliable. — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)

**How bad is baseline RAG?**
- CRAG benchmark (Meta, NeurIPS 2024 Datasets & Benchmarks): most advanced LLMs reach ≤34% accuracy without retrieval; "adding RAG in a straightforward manner improves the accuracy only to 44%"; state-of-the-art industry RAG solutions "only answer 63% questions without any hallucination." Scoring labels answers perfect/acceptable/missing/incorrect and penalizes hallucination more than "I don't know." — [arXiv 2406.04744](https://arxiv.org/abs/2406.04744)
- A secondary summary claims straightforward RAG lowered CRAG truthfulness from ~20% to 13.4% because noisy retrieval adds errors; this number could not be verified in the primary text. — [alphaxiv summary](https://www.alphaxiv.org/abs/2406.04744) (unverified)

**Parsing / layout failures (upstream of chunking)**
- OmniDocBench (CVPR 2025): "All models showed clear performance degradation on multi-column and complex layouts compared to single-column documents"; reading-order errors are the main risk on multi-column pages; tables scored with TEDS include nested structures, multilevel headers and spanning cells. Benchmark covers Chinese and English pages only. — [OmniDocBench CVPR 2025](https://openaccess.thecvf.com/content/CVPR2025/papers/Ouyang_OmniDocBench_Benchmarking_Diverse_PDF_Document_Parsing_with_Comprehensive_Annotations_CVPR_2025_paper.pdf); [GitHub](https://github.com/opendatalab/OmniDocBench)
- Applied Sciences (MDPI, 2026) RAG study on administrative documents: Docling + hierarchical splitting + VLM image descriptions reached 94.1 ± 1.6% accuracy; **table-dependent questions showed a 33-percentage-point gap** between basic and hierarchy-aware splitting; metadata enrichment and hierarchy-aware chunking "contributed more to accuracy than the conversion framework alone." — [doi 10.3390/app16105069](https://doi.org/10.3390/app16105069)
- A QCon London 2026 talk by a Rabobank AI tech lead: "most failures in RAG systems stem from indexing and retrieval, rather than the language model itself"; flattening complex layouts to plain text strips structure and leads to misread numbers and tables; their fix combined text extraction with vision-language models. — [InfoQ, Mar 2026](https://infoq.com/news/2026/03/retrieval-production-ai/) (talk report)
- Practitioner retrospective across five projects: hidden PDF metadata broke chunking logic; Outlook attachments and wikis each had their own quirks. — [dev.to / Dextra Labs](https://dev.to/dextralabs/implementing-retrieval-augmented-generation-rag-with-real-world-constraints-3ajm) (anecdotal)
- A 2026 production data-layer paper reports access-control bugs in application-layer filter logic are "among the most severe and hardest-to-detect failures" and freshness requirements "are usually left unspecified until a user gets a stale answer." — [arXiv 2605.03275](https://arxiv.org/pdf/2605.03275)

**Generation-side: fluent-but-wrong, and the "cannot verify" problem**
- Observability writers note incorrect answers "often appear fluent and well-formatted while citing wrong sources." — [dev.to](https://dev.to/dowhatmatters/why-a-workflow-pack-for-rag-eval-4hd) (anecdotal)
- The GPTs-style product itself: OpenAI community thread (opened 28 Mar 2025, closed by OpenAI_Support 19 Aug 2026 for inactivity) reports that since a March 2025 update custom GPTs "no longer seem to use uploaded knowledge files," give "made-up or inaccurate responses" when asked to quote a section, and intermittently claim no file access; no explanation or fix from OpenAI in-thread. — [OpenAI community](https://community.openai.com/t/are-your-custom-gpts-ignoring-their-uploaded-knowledge-files-too/1154282) (anecdotal, 3 posts)
- Other GPT-builder threads: wrong torque values from a single car-care PDF ("don't rely on retrieval for anything serious"); recommendation that Markdown beats PDF because "PDFs can distort formatting and images are ignored." — [OpenAI community: knowledge base best practices](https://community.openai.com/t/my-gpt-knowledge-base-best-practices/589487); [GPT editor / knowledge base problems](https://community.openai.com/t/gpt-editor-knowledge-base-problems/624794) (anecdotal, 2023–2024)

### Inferences
- For a personal "upload my docs" assistant, the dominant *fixable* losses are (a) layout/table parsing, (b) chunk boundaries losing context, and (c) retrieval misses from vocabulary mismatch — together they explain more than half of errors before any LLM prompt tuning. Generation-stage "misinterpretation" (the largest generation bucket) is also partly a retrieval/context quality problem (noisy, truncated chunks).
- "Answer is right but user can't verify it" is not in any formal taxonomy, but it is the practical consequence of FP4/E13 fluency without grounding; it is addressed by the evidence/citation layer rather than by retrieval tuning (see Q5).
- The EACL 2026 counts come from a small 8B model with 128-token chunks; frontier models would likely shift the share away from generation errors, making chunking/retrieval an even larger fraction.

### Gaps
- No published study specifically quantifies failure shares for *enterprise personal-assistant* workloads (heterogeneous user uploads, PPT/XLSX, scanned PDFs); the DragonBall/CLAPnq numbers are academic proxies.
- No reliable data on how often stale/duplicate document versions cause wrong answers; only qualitative mentions.
- Reddit r/RAG / HN post-mortem threads were not surfaced by search (results returned papers and blogs instead).

---

## Key Question 2: Remedies and their measured impact

### Takeaway
The best-documented wins, in rough order of effect size per unit of effort: (1) a reranker on top of hybrid BM25+dense retrieval (Recall@5 +17% relative over hybrid alone, +27–39% over single retrievers on T2-RAGBench; Anthropic: 67% fewer retrieval failures combined with contextual chunks), (2) structure-aware/hierarchical chunking for table-heavy docs (+33 pp on table questions), (3) contextual chunk enrichment (−35% to −49% retrieval failures), (4) forced quoting with mechanical string verification for hallucination control; late chunking, GraphRAG and semantic chunking show modest or unproven gains. Long context beats RAG on accuracy for small corpora (<~200k tokens) but costs ~26× more per query in one 2026 study.

### Cited Findings

**Hybrid search + reranking**
- T2-RAGBench (23,088 queries over 7,318 financial text+table documents; arXiv 2604.01733, 2 Apr 2026): Recall@5 — BM25 0.644, dense (text-embedding-3-large) 0.587, Hybrid RRF 0.695, **Hybrid + Cohere Rerank 0.816**, Corrective RAG (GPT-4.1-mini) 0.658. nDCG@10 — BM25 0.515, dense 0.466, hybrid 0.551, hybrid+rerank 0.683. MRR@3 0.433 → 0.605 with reranking (+39.7% relative). End-to-end Number-Match answer accuracy with GPT-4.1-mini: BM25 0.251, dense 0.257, hybrid 0.282, oracle retrieval 0.350; with GPT-5.4: hybrid 0.346, oracle 0.403. Cohere endpoint processed the 23K-query benchmark in ~1 hour at 300K tokens/min. — [arXiv 2604.01733](https://arxiv.org/html/2604.01733v1)
- Note the oracle gap: even perfect retrieval yields only 0.35–0.40 on these numeric table questions — generation over tables remains a bottleneck. — [arXiv 2604.01733](https://arxiv.org/html/2604.01733v1)
- Anthropic Contextual Retrieval (19 Sep 2024): top-20 retrieval failure rate (1−recall@20, Gemini Text 004 embeddings, averaged over codebases/fiction/arXiv/science): baseline 5.7% → contextual embeddings 3.7% (−35%) → + contextual BM25 2.9% (−49%) → + reranking (top-150 → top-20) 1.9% (−67%). Passing 20 chunks beat 5 or 10. One-time contextualization cost ≈ $1.02 per million document tokens with prompt caching (800-token chunks, 8k-token docs, 50–100-token generated context). — [Anthropic engineering](https://www.anthropic.com/engineering/contextual-retrieval)
- WANDS e-commerce benchmark (secondary blog): tuned hybrid nDCG 0.7497 vs BM25 0.6983 vs vector 0.6953 (+7.4%); basic RRF only 0.7068, so most gain came from field boosting, not fusion alone. — [denser.ai 2026 guide](https://denser.ai/blog/hybrid-search-for-rag/) (vendor blog)
- CeQe paper: fusing BM25 with semantic search via RRF improves Recall@100 on every dataset tested, largest gains where the two retrievers disagree most. — [arXiv 2608.00452](https://arxiv.org/pdf/2608.00452)
- Reranker vendor comparison (Cohere/BGE/Jina/Voyage, June 2026): BGE and Cohere "deliver similar results on domain-specific non-English content"; recommends testing on your own corpus. — [ianas.fr](https://ianas.fr/en/blog/2026/06/07/reranker-comparatif-cohere-bge-jina-voyage/) (blog)
- Federated-search paper: reranking accounted for ~63% of total latency on one dataset and ~84% of retrieval-side time on another; cutting candidate count reduced it substantially. — [arXiv 2502.19280](https://arxiv.org/pdf/2502.19280)

**Chunking**
- Late chunking (Jina, arXiv 2409.04701 v3, Jul 2025): averaged over 3 embedding models × 4 datasets, late chunking with sentence boundaries gave +3.63% relative (+1.9 absolute) over naive chunking; requires a long-context embedder (Jina v3 = 8K tokens; longer docs need windowing). — [arXiv 2409.04701](https://arxiv.org/pdf/2409.04701)
- "Reconstructing Context" (arXiv 2504.19754, 2025) compared late chunking vs ContextualRankFusion on NFCorpus with Jina-v3 and found ContextualRankFusion better overall. — [arXiv 2504.19754](https://arxiv.org/pdf/2504.19754)
- EACL 2026 taxonomy fixes: Context Mismatch → small overlaps + adaptive (structure-aware/semantic/hybrid) chunking; Missed Retrieval → query rewriting, hybrid sparse/dense, adaptive thresholds, retrieve more chunks; Reranker low recall → pass more chunks / fine-tune in-domain. — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)
- "Engineering RAG Systems for Real-World Applications" (arXiv 2506.20869): chunk sizes of 200–500 tokens "struck a practical balance between retrieval relevance and query latency," while smaller chunks bloated the index. — [arXiv 2506.20869](https://arxiv.org/pdf/2506.20869)
- Parent-child vs GraphRAG: only anecdotal — one team reportedly switched from GraphRAG to parent-child because GraphRAG was too slow for production; a guide calls parent-child "the de facto production pattern." — [futureagi blog](https://futureagi.com/blog/advanced-chunking-techniques-for-rag/) (vendor blog, anecdotal)
- "RAG vs. GraphRAG: A Systematic Evaluation" (arXiv 2502.11371, v3 Mar 2026): reports "distinct strengths across different tasks" (QA and query-focused summarization) and that hybrid selection/integration yields consistent gains; per-task numbers not retrievable from the abstract. — [arXiv 2502.11371](https://arxiv.org/abs/2502.11371)

**Parsing remedies**
- Docling 2.110.0 on opendataloader-bench: overall 0.89, TEDS table 0.93, 0.549 s/page (competitor-published comparison; lowest-scoring parser 0.57). — [nutrient.io](https://www.nutrient.io/blog/best-pdf-parsers-for-rag/) (competitor blog, indicative only)
- OmniDocBench v1.5 leaderboard led by newer OCR/VLM models (e.g., GLM-OCR 94.62 composite). — [codesota leaderboard](https://www.codesota.com/ocr/benchmark/omnidocbench) (aggregator)

**Vision-language retrieval (ColPali family) — relevant to page-image evidence display**
- ColPali (ICLR 2025): embeds page images directly; original model 81.3 avg nDCG@5 on ViDoRe v1; indexes much faster than OCR+layout+captioning pipelines (Unstructured) because it skips those steps. — [arXiv 2407.01449](https://arxiv.org/html/2407.01449v6); [ICLR 2025 PDF](https://proceedings.iclr.cc/paper_files/paper/2025/file/99e9e141aafc314f76b0ca3dd66898b3-Paper-Conference.pdf)
- ViDoRe v2 (arXiv 2505.17166) was created because SOTA models passed nDCG@5 of 90 on v1; it notes strong retrievers "incur significant computational cost and inference latency." — [arXiv 2505.17166](https://arxiv.org/pdf/2505.17166)
- ColModernVBERT (250M params) is within 0.6 nDCG@5 points of ColPali with 10× fewer parameters; a 2026 paper reports CPU query-encoding 0.032 s vs ColPali 0.222 s (ViDoRe avg 68.6 vs 69.2). — [arXiv 2608.10636 (DistilVDR)](https://arxiv.org/pdf/2608.10636); [arXiv 2609.07262](https://arxiv.org/pdf/2609.07262) (exact attribution of the latency figure between these two not confirmed)
- "Lost in OCR Translation?" (DocEng '25, arXiv 2505.05666): compared ColPali vs OCR (Nougat) + Llama 3.2 90B across document quality levels; vision RAG "performs well on documents it was fine-tuned on" but "OCR-based RAG is better able to generalize to unseen documents of varying quality." No quantitative figures in abstract. — [arXiv 2505.05666](https://arxiv.org/abs/2505.05666)
- Spatially grounded retrieval (arXiv 2512.02660) propagates patch-level relevance to page regions — i.e., late-interaction retrievers can localize *where* on a page the match is. — [arXiv 2512.02660](https://arxiv.org/pdf/2512.02660)

**Long context vs RAG**
- Anthropic: if the knowledge base is under ~200,000 tokens (~500 pages), include it entirely in the prompt with prompt caching instead of RAG; RAG for larger/growing bases. — [Anthropic engineering](https://www.anthropic.com/engineering/contextual-retrieval)
- "Token Tax of Epistemic Accuracy" (arXiv 2606.20898, 2026; manufacturing safety training; 324 matched question pairs): long-context ≈73% correct vs semantic RAG ≈65%, at ~26× per-query token cost ($0.118 vs ~$0.0045 per query). Single domain, modest sample. — [arXiv 2606.20898](https://arxiv.org/html/2606.20898v1)
- "Long Context vs. RAG for LLMs" (arXiv 2501.01880, 2025): long context better on Wikipedia QA, RAG better on dialogue queries; prior work split (one found LC better on multi-hop, one found RAG ahead up to 32K, Self-ROUTE kept LC accuracy at lower cost). — [arXiv 2501.01880](https://arxiv.org/pdf/2501.01880)
- "When Retrieval Succeeds and Fails" (arXiv 2510.09106): long-context models still face attention-scaling costs and pick up more irrelevant text as windows grow. — [arXiv 2510.09106](https://arxiv.org/pdf/2510.09106)

**Citation forcing, abstention, hallucination control**
- "Abstention vs. Hallucination" (arXiv 2405.02228): RAG variants reduce hallucination rate vs zero-context baselines but "often show near-zero abstention rate even when hallucination remains high" — retrieved text looks like evidence and makes the model answer. — [arXiv 2405.02228](https://arxiv.org/html/2405.02228)
- SQuAD 2.0 evidence-calibrated RAG study: calibrated evidence sufficiency substantially reduced unsupported answers vs a forced-answer reranker (hallucination-proxy rate from 77.80%), but calibration itself was weak (AUROC 0.5475, ECE 0.1144); "retrieval coverage alone is insufficient." — [JTIE](https://jtie.stekom.ac.id/index.php/jtie/article/view/536)
- Hobbyist experiment (not peer-reviewed): forcing the answer to quote its source and string-checking the quote was the only defence that worked — 5.3% hallucination on SQuAD 2.0, refused 50/50 unanswerable textbook questions; an LLM "is the answer here?" gate had no effect; the quote-forced baseline was 87.3% correct with 7.3% wrongly refused (over-refusal cost). — [GitHub no-hallucination](https://github.com/aryanchauhanoffical/no-hallucination); [GitHub super-hallucinations](https://github.com/za1d3v/super-hallucinations) (anecdotal, small sample)
- Citation-enforced RAG for tax documents (arXiv 2603.14170): citation enforcement grounds claims; abstention prevents generation when evidence is insufficient; abstention triggered most often on low-retrieval-similarity queries. — [arXiv 2603.14170](https://arxiv.org/pdf/2603.14170)
- LLM-driven logical retrieval (arXiv 2605.27123, 2026): refusal rate on answer-unavailable questions 0.767 → 0.828 while hallucination fell 0.128 → 0.083. — [arXiv 2605.27123](https://arxiv.org/pdf/2605.27123)
- AbstentionBench (arXiv 2506.09038): reasoning LLMs fail on unanswerable questions. — [arXiv 2506.09038](https://arxiv.org/html/2506.09038v1)
- "Prompt-Based Abstention Fails Under Misleading Context" (arXiv 2608.22228, 2026) — title indicates prompt-only "say I don't know" is fragile when retrieved context is misleading; body not read. — [arXiv 2608.22228](https://arxiv.org/pdf/2608.22228)
- Highlighted Chain-of-Thought (HoT, arXiv 2503.02003, v6 Jun 2026): prompting the model to tag key facts in the question and highlight the facts it uses in the answer reduces hallucination and improves accuracy vs vanilla CoT across 22+ tasks (no single aggregate figure on abstract page). — [arXiv 2503.02003](https://arxiv.org/abs/2503.02003)

### Inferences
- A minimal high-leverage stack for a personal assistant: layout-aware parser (tables as structured HTML/markdown, page/bbox retained) → structure-aware chunks of ~200–500 tokens with parent context and LLM-generated chunk context → hybrid BM25+dense → cross-encoder rerank of ~50–150 candidates to ~10–20 → generator forced to quote verbatim spans that are string-verified against the chunk → abstain when the top rerank score is below a tuned threshold. Every link in that chain has at least one quantified study behind it.
- For small personal corpora (tens of documents, <200k tokens), "stuff everything + prompt cache" is a legitimate accuracy-maximizing alternative; the user should route by corpus size.
- ColPali-style page-image retrieval is attractive for *evidence display* (retrieval unit = page, with patch-level relevance maps) but generalization to unseen, low-quality scans is contested (DocEng '25), so a text+vision hybrid is safer than vision-only.

### Gaps
- No same-setup head-to-head of Cohere Rerank 3.5 vs bge-reranker-v2-m3 vs Jina vs ColBERT on hybrid pipelines was found; vendor numbers are not comparable.
- One search summary cited an arXiv study where off-the-shelf cross-encoders (ms-marco-MiniLM, bge-reranker-base) *degraded* nDCG by 0.3–3.1% on technical/scientific corpora; the primary paper could not be identified, so treat as an unconfirmed caution that rerankers can hurt out-of-domain.
- Measured impact of HyDE, multi-query, and agentic RAG on document assistants was not found in quantified form in this pass.
- GraphRAG per-task win/loss numbers were not retrieved (abstract only).

---

## Key Question 3: Latency — breakdown, what users accept, techniques, why GPTs feel slow

### Takeaway
Retrieval stages are typically well under half a second (embed 20–80 ms, ANN 10–150 ms, rerank 50–400 ms), while LLM prefill/time-to-first-token dominates (500 ms–2 s+ for short contexts; 5.5 s measured at 16K tokens on a 14B model), so GPTs-style products feel slow mostly because they inject thousands of retrieved tokens into a frontier model without streaming-first UX, caching, or per-stage budgets. The only controlled HCI study found (2026, n=240) shows users notice a 2 s TTFT only 27% of the time but 66% at 9 s and 82% at 20 s, with 9 s rated "more useful/thoughtful" than 2 s — i.e., the problem is unexplained waiting, not seconds per se.

### Cited Findings

**Per-stage breakdowns**
- Practitioner budget (Go/RAG engineering post): query embedding 20–80 ms; ANN vector search 10–150 ms; cross-encoder rerank 50–400 ms (often over network); context assembly sub-ms; LLM first token 500–2000 ms+. Example SLO: 2 s user-facing budget, 700 ms retrieval budget (100 ms embed / 200 ms search / 300 ms rerank timeouts) leaving ~1.3 s for LLM; set timeouts at p95 + 20% headroom, not at the mean; "timeout cascade" occurs when one outer timeout is shared by sequential stages so a slow index query eats the LLM's deadline. — [dev.to (Sep 21, year not shown)](https://dev.to/neeraj_singhi_golang/retrieval-latency-budgets-in-rag-pipelines-vector-search-reranking-and-the-timeout-cascade-55jk)
- "Why RAG Latency Is a Prefill Problem, Not a Retrieval Problem" (Daily Dose of DS, 2 Aug 2026): embedding "a few ms", ANN over a few million vectors "tens of ms", cross-encoder rerank of 8 candidates "perhaps 100 ms"; production RAG queries typically send 4,000–16,000 tokens to the model; at 16K tokens a 14B model on an NVIDIA L20 took >5.5 s before the first output token (citing the CacheClip paper); prefill attention scales quadratically; standard prefix caching helps little for RAG because retrieved chunks change per request and in order. — [dailydoseofds](https://www.dailydoseofds.com/building-rag-systems-course-part-12-with-implementation/)
- A 2026 arXiv study on retrieved-content representations: original-document latency ≈9 s, almost entirely TTFT; reducing input size lowers it proportionally. — [arXiv 2605.30790](https://arxiv.org/pdf/2605.30790)
- Reranker cost estimates (order of magnitude): cross-encoder ≈8 ms per candidate (≈800 ms for 100), ColBERT-style late interaction ≈0.3 ms per candidate. — [echelonedge blog](https://www.echelonedge.com/blogs/breaking-down-a-rag-pipeline-where-latency-really-comes-from/) (estimates, not benchmarks)
- Reranking took ~63% of total latency on one dataset in a federated-search setting. — [arXiv 2502.19280](https://arxiv.org/pdf/2502.19280)
- Large indexes that don't fit memory cause thrashing that worsens both retrieval and TTFT. — [arXiv 2504.08930 (VectorLiteRAG)](https://arxiv.org/pdf/2504.08930)

**What users accept**
- Controlled study (arXiv 2604.06183, 2026; 240 Prolific participants; TTFT fixed at 2 / 9 / 20 s, then 25 tok/s streaming): delay noticed by 26.6% at 2 s, 66.3% at 9 s, 81.5% at 20 s. Trust showed no significant effect (means 5.80–6.19 / 7). The 2 s condition was rated *less* thoughtful than 9 s (5.76 vs 6.09, p=.008) and less useful (6.19 vs 6.44, p=.021). Prompt volume did not differ by latency; only 12 participants (mostly in the 20 s cell) described cutting back. Authors give no numeric threshold; they note ~1 s and ~10 s HCI heuristics as approximate reference points and warn 20 s risks reading as inefficiency. — [arXiv 2604.06183](https://arxiv.org/html/2604.06183v1)
- Measurement of six mainstream AI video-chat apps: every chatbot averaged >2.8 s response delay vs the sub-second ITU-T threshold for natural conversation; long initial waits can cause session abandonment (no cutoff given). — [arXiv 2510.00481](https://arxiv.org/html/2510.00481v2)
- Practitioner guidance: TTFT under ~1 s for interactive chat with short prompts; multi-second waits normal for very long contexts; report p50/p95/p99. — [ClickHouse](https://clickhouse.com/resources/engineering/llm-inference-latency)
- Vendor rules of thumb (uncited): <1 s feels instant, >3 s feels slow; stream TTFT <500 ms, p99 <5 s; "satisfaction drops 15%/s beyond 1 s" is unsourced. — [conferbot](https://www.conferbot.com/blog/chatbot-analytics-metrics-guide) (vendor, unverified)

**Techniques**
- Prompt caching: Anthropic states caching can cut latency "by more than 2x and costs by up to 90%" (in the contextual-retrieval context) — [Anthropic](https://www.anthropic.com/engineering/contextual-retrieval); secondary sources cite Anthropic's "up to 85% lower latency for long prompts" (confirmed in one blogger's own tests), cache reads ≈10% of base input price, shaving 1–2 s off TTFT — [ngrok blog](https://ngrok.com/blog/prompt-caching); OpenAI automatic caching "up to 80% latency reduction" on cached prefix (≥1,024 tokens, 50% input discount; ~50% hit rate observed, best-effort) — [PromptHub](https://www.prompthub.us/blog/prompt-caching-with-openai-anthropic-and-google-models) (secondary); Gemini implicit caching 75% discount on 2.5 models, explicit up to 90%; one paper observed Gemini implicit caching does not engage below ≈70,000 tokens — [arXiv 2609.19113](https://arxiv.org/pdf/2609.19113)
- Semantic caching: Portkey early test ~20% hit rate at 99% accuracy for Q&A/RAG — [Portkey](https://portkey.ai/blog/reducing-llm-costs-and-latency-semantic-cache/); VoiceAgentRAG (arXiv 2603.02206): 75% hit rate (150/200 queries, conversational follow-ups), retrieval 110.4 ms → 0.35 ms on hits — [arXiv 2603.02206](https://arxiv.org/html/2603.02206v1); PerCache (mobile): up to 34.4% end-to-end latency reduction — [arXiv 2601.11553](https://arxiv.org/html/2601.11553v1); personalized, time-sensitive and context-dependent answers should not be cached; monitor false-positive rate and freshness — [dev.to semantic caching](https://dev.to/nikhil_ramank_152ca48266/-semantic-caching-in-enterprise-rag-production-architectures-for-faster-lower-cost-llm-systems-h3)
- Streaming "fixes perceived latency, not real latency." — [boundev latency budget](https://www.boundev.ai/blog/latency-budget-rag-pipeline)
- Binary quantization made retrieval ~4× faster in the author's demo. — [dailydoseofds](https://www.dailydoseofds.com/building-rag-systems-course-part-12-with-implementation/)
- Smaller visual retrievers: ColModernVBERT 0.032 s vs ColPali 0.222 s CPU query encoding (see Q2). — [arXiv 2608.10636](https://arxiv.org/pdf/2608.10636)
- Limiting file_search result count "can help reduce both token usage and latency, but may come at the cost of reduced answer quality" (OpenAI Responses API file search docs, mirrored). — [OpenAI file search docs mirror](https://glama.ai/mcp/servers/@quintonwall/fast-pyairbyte/blob/7dfc9ce46d714368554f5420788e47f466f620dd/pyairbyte-guide/mcp-server-docs/openai-filesearch.txt)

**Why GPTs-style products feel slow (anecdotal)**
- GPT builder with 20 PDFs of 300+ pages reported slow knowledge retrieval and errors; 50 MB JSONL files make previews "take ages" and often error. — [OpenAI community](https://community.openai.com/t/my-gpt-knowledge-base-best-practices/589487)
- Blog test: when a GPT loads from its knowledge base, "response times were significantly slower"; one large file vs many small files made no significant difference. — [seanmcp.com](https://www.seanmcp.com/articles/is-it-better-to-have-one-large-file-or-multiple-small-files-with-gpts/)

### Inferences
- The company platform's "slow" perception most likely comes from: (1) no streaming or a long silent wait before the first token while a frontier model prefills 5–15K tokens of retrieved text, (2) serial stages (embed → search → rerank → LLM) with no parallelism or caching, (3) top-k set high to compensate for weak retrieval, which inflates prefill, and (4) a second full LLM pass to produce citations. Each is addressable without changing the model.
- Evidence highlighting can be made nearly free at query time if page renders and chunk→bbox maps are precomputed at ingest; the highlight step then costs only a string match, not an LLM call.
- A practical budget for a personal assistant on API models: first visible token <1 s (stream a "searching your documents…" status immediately), retrieval+rerank ≤500 ms p95, full answer ≤5 s p95, with prompt caching of the system prompt + per-document context.

### Gaps
- No vendor-published latency breakdown for OpenAI file_search / Custom GPT knowledge retrieval or Gemini File Search was found; only user anecdotes.
- No study of speculative retrieval (pre-fetching while the user types) with numbers.
- The 2604.06183 study fixed streaming at 25 tok/s and did not test "no streaming"; no controlled study found comparing streaming vs non-streaming satisfaction.

---

## Key Question 4: Evaluation — frameworks, metrics, building a small personal eval set

### Takeaway
RAGAS/DeepEval provide faithfulness (claim-level support by retrieved context), answer relevancy, and context precision/recall, but *citation* precision/recall must usually be custom-built at claim level; recent results show answer-relevancy and citation faithfulness can diverge sharply (GPT-5.5: 0.756 vs 0.417 on LongEval), so a personal eval set should separately track retrieval hit rate, faithfulness, citation precision, and abstention/over-refusal.

### Cited Findings
- RAGAS faithfulness decomposes the answer into statements and checks each against the retrieved context; context precision evaluates whether ground-truth relevant chunks are ranked at the top (a retrieval-ranking metric, not citation precision). — [Langfuse RAGAS guide](https://langfuse.com/resources/engineering/evaluation-of-rag-with-ragas); [atlan RAG evaluation 2026](https://atlan.com/know/how-to-evaluate-rag-systems-explained/)
- DeepEval FaithfulnessMetric checks that the generator "does not hallucinate AND contradict any factual information presented in the retrieval_context"; it ships RAGAS-style wrappers (RAGASFaithfulnessMetric, RAGASContextualPrecisionMetric) and a GEval metric for custom criteria; the RAGAS composite score averages answer relevancy, faithfulness, contextual precision, contextual recall; designed for CI/CD unit tests. — [DeepEval RAG guide](https://deepeval.com/guides/guides-rag-evaluation); [Confident AI CI/CD guide](https://www.confident-ai.com/blog/how-to-evaluate-rag-applications-in-ci-cd-pipelines-with-deepeval); [GitHub deepeval](https://github.com/confident-ai/deepeval)
- Survey taxonomy lists Answer Attribution/Faithfulness Score and Citation Precision/Recall as generation metrics with RAGAS, ARES, FActScore, TRUVA as representative tools. — [arXiv 2407.13193](https://arxiv.org/pdf/2407.13193)
- MiRAGE (arXiv 2510.24870, 2025) defines CiteF1 via claim decomposition: precision = support between a subclaim and its citations; recall = max support between subclaims citing the same source. — [arXiv 2510.24870](https://arxiv.org/pdf/2510.24870)
- LongEval 2026 scientific-QA entry: GPT-5.5 led answer relevancy (0.756) but had "extremely low Citation Faithfulness at 0.417" — answer relevance hides weak grounding. — [arXiv 2607.14400](https://arxiv.org/pdf/2607.14400)
- 30-query controlled study comparing RAGAS, DeepEval and LLM-as-judge under an abstention policy: evaluators "were not uniformly designed for abstention-heavy workloads," so groundedness scores disagree on refusals; measure abstention and hallucination separately. — [ResearchGate](https://www.researchgate.net/publication/399331938_Benchmarking_Hallucination_Evaluation_for_RAG_Under_an_Abstention_Policy_A_Controlled_30-Query_Study_with_RAGAS_DeepEval_and_LLM-as-Judge)
- Automated error-stage classification (RAGEC) reaches only 57.8% stage / 40.3% type accuracy, so human annotation of a few hundred failures remains the reliable root-cause method; the paper's own set was 406 annotated errors with 92.9% inter-annotator agreement. — [arXiv 2510.13975](https://arxiv.org/html/2510.13975v2)
- EncouRAGe (arXiv 2511.04696): local, fast RAG evaluation framework. — [arXiv 2511.04696](https://arxiv.org/pdf/2511.04696)
- Benchmarks usable as reference points: CRAG (4 labels perfect/acceptable/missing/incorrect; truthfulness penalizes hallucination more than "I don't know") — [arXiv 2406.04744](https://arxiv.org/abs/2406.04744); T2-RAGBench for text+table financial docs (Recall@5, MRR@3, nDCG@10, Number Match) — [arXiv 2604.01733](https://arxiv.org/html/2604.01733v1); ViDoRe v1/v2 for visual page retrieval (nDCG@5) — [arXiv 2505.17166](https://arxiv.org/pdf/2505.17166); OmniDocBench for parsing (TEDS tables, reading order) — [GitHub](https://github.com/opendatalab/OmniDocBench)
- Liu et al.'s human-audit protocol (citation recall = share of sentences fully supported by their citations; citation precision = share of citations that support their sentence) is directly reusable for a personal golden set. — [arXiv 2304.09848](https://arxiv.org/html/2304.09848v2)
- Anthropic's retrieval metric for its own eval was 1−recall@20 averaged across domains. — [Anthropic](https://www.anthropic.com/engineering/contextual-retrieval)

### Inferences
- A cheap personal eval set: (1) pick 30–100 questions you actually asked, (2) record for each the gold page/span (page number + quoted text) — this doubles as the ground truth for the highlight UI, (3) include ~20% unanswerable questions to measure abstention and over-refusal separately (CRAG-style scoring), (4) include table-dependent and multi-column questions explicitly since those are the largest known parser/chunker failure classes, (5) compute retrieval hit@k on gold page, faithfulness (RAGAS/DeepEval), and citation precision via verbatim-quote string matching (no LLM judge needed when the system is forced to quote).
- Because the proposed product's trust mechanism is verbatim span highlighting, "citation precision" can be measured mechanically (does the highlighted span exist and does it contain the answer?), which is cheaper and more reliable than LLM-judged faithfulness.

### Gaps
- No source quantifies how large a golden set must be for stable metrics on a personal corpus; the 30-query study is the smallest found and reports evaluator disagreement.
- ARES and TruLens specifics were not retrieved in this pass.
- RAGAS synthetic test-set generation was not verified from a primary page.

---

## Key Question 5: Trust research — do citations/highlights change verification behavior, and does passage highlighting reduce verification time?

### Takeaway
Citations raise self-reported trust even when they are random (AAAI 2025), and only ~52% of generative-search sentences were fully supported by their citations in the 2023 Stanford audit — so citations alone are a weak trust mechanism. The best controlled evidence on highlighting (HoT, n=63) shows highlighted supporting facts cut verification time ~25% (62.4 s → 47.3 s) and raise accuracy on correct answers (78.8% → 84.5%), but *lower* users' ability to catch wrong answers (72.2% → 54.8%); phrase-level highlighting is the preferred granularity (IBM, n=208), and verification aids fail when the interface is slow or cluttered (PaperTrail, n=26).

### Cited Findings

**Citation accuracy audits**
- Liu, Zhang & Liang, "Evaluating Verifiability in Generative Search Engines" (EMNLP Findings 2023; Bing Chat, NeevaAI, Perplexity, YouChat): on average only 51.5% of generated sentences fully supported by citations and 74.5% of citations support their sentence. Recall: Perplexity 68.7, NeevaAI 67.6, Bing Chat 58.7, YouChat 11.1. Precision: Bing Chat 89.5, Perplexity 72.7, NeevaAI 72.0, YouChat 63.6. Citation precision was strongly negatively correlated with perceived utility (r = −0.96): Bing paraphrased sources closely but answered less usefully. — [arXiv 2304.09848](https://arxiv.org/html/2304.09848v2); [ACL Anthology](https://aclanthology.org/2023.findings-emnlp.467/)
- A 2025 CJR Tow Center audit (secondary summary) found 37% of responses incorrect across eight AI search engines on news-citation tasks. — [futureagi review](https://futureagi.com/blog/perplexity-ai-review-citations/) (secondary)
- "Answer Bubbles" (arXiv 2603.16138) studies information exposure in AI-mediated search. — [arXiv 2603.16138](https://arxiv.org/pdf/2603.16138)

**Citations and trust behavior**
- "Citations and Trust in LLM Generated Responses" (AAAI 2025, arXiv 2501.01303): live QA experiment with a commercial chatbot; responses shown with zero, one, or five citations that were relevant or random. Significant increase in trust when citations were present — *even when random* — and a significant *decrease* in trust when participants actually checked the citations. — [arXiv 2501.01303](https://arxiv.org/pdf/2501.01303)
- PaperTrail claim-evidence interface (arXiv 2602.21045, 2026; 26 researchers): claim-level provenance significantly *reduced* trust in LLM outputs vs standard citation provenance; "time pressure, system latency, and interface complexity prevented meaningful engagement with provenance features." — [arXiv 2602.21045](https://arxiv.org/pdf/2602.21045)
- "From Verification Burden to Trusted Collaboration" (arXiv 2512.11661): design goals for LLM-assisted literature review framed around the cost of verification. — [arXiv 2512.11661](https://arxiv.org/html/2512.11661v1)

**Highlighting the exact passage**
- HoT user study (arXiv 2503.02003 §5.6, Table 12; v6 27 Jun 2026): 63 students each verified 10 responses (GSM-Symbolic and DROP; 120 HoT vs 120 CoT, half correct/half incorrect; 2-minute limit). Mean verification time **47.26 s with highlights vs 62.38 s without (−15.1 s, ≈25% faster)**. Accuracy judging *correct* LLM answers: 84.48% vs 78.82%. Accuracy judging *incorrect* answers: **54.83% vs 72.21%** — highlights made users more likely to accept wrong answers. — [arXiv 2503.02003](https://arxiv.org/html/2503.02003)
- HoT cites earlier work that high-quality text highlights reduce decision time and effort in classification tasks while low-quality highlights harm user accuracy. — [arXiv 2503.02003](https://arxiv.org/pdf/2503.02003)
- "Highlight All the Phrases" (IBM, arXiv 2508.06846, 2025): two experiments × 104 IBM employees (208 total), 7-point scales. Trust: baseline 2.63 → phrase-level highlight-all 3.31, term-level 3.62 (Exp 1); baseline 2.75 → phrase 3.67, term 3.34 (Exp 2). Ease of validation: baseline 4.29 → phrase 4.74 (p=.03), term 4.72 (p=.04) in Exp 1; in Exp 2 term-level *dropped* to 4.23 (p=.01) because dense term markup made text "difficult to read." Phrase-level highlight-all was the most preferred design in both experiments (52.9% vs 26.9% term-level in Exp 1). Participants who initially judged the response inaccurate raised trust after markup; those who judged it accurate lowered trust — read as calibration. Numeric factuality scores performed worst; red–green scale flagged as inaccessible. — [arXiv 2508.06846](https://arxiv.org/html/2508.06846)
- Medium/SO Development posts claim "up to 3× increase in perceived reliability" from PDF source highlighting with no underlying study. — [SO Development](https://so-development.org/building-trust-in-llm-answers-highlighting-source-texts-in-pdfs/) (unsubstantiated marketing claim)

### Inferences
- The evidence supports the user's thesis that *verbatim span highlighting* reduces verification cost (≈25% faster in the one timed study) and is preferred over term-level or numeric-score displays. It also supports a design warning: highlighting increases acceptance of wrong answers, so the system must pair highlights with (a) mechanical verification that the highlighted span actually exists in the source (string match), (b) explicit abstention/"weak evidence" states, and (c) low latency and low clutter, or users disengage (PaperTrail).
- Because random citations raise trust as much as real ones, the product's trust claim should rest on *verifiability* (one click to the exact page region) rather than on citation presence; measuring citation precision (Liu et al. protocol) is the honest KPI.
- The Liu et al. precision–utility trade-off (r = −0.96) implies a design tension: tightly quoted answers verify easily but may feel less helpful; a two-layer answer (synthesized summary + quoted evidence) is a reasonable compromise, with the quoted layer being what is highlighted.

### Gaps
- No study directly measures verification *time* for highlighting inside a rendered PDF page (HoT highlights were inline in the answer text, on math/reading tasks, not enterprise documents).
- No longitudinal evidence on whether highlighting reduces the "re-open the source document" behavior the user observed at their company.
- No HCI study found on page-image evidence (ColPali-style) vs text-snippet evidence for user trust.
- Participant counts and effect sizes of the AAAI 2025 citation study were not on the abstract page.
