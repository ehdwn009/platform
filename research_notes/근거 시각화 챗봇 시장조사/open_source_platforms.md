# Open-source / self-hostable RAG & chatbot platforms: how they present evidence (state as of 2026-10-08)

Evidence-presentation scale used throughout:
- **L0** none · **L1** filename/link only · **L2** extracted text chunk shown · **L3** jumps to page inside an embedded viewer · **L4** renders the original page with highlighted bounding boxes / regions.
- Each highlighting claim is tagged **[confirmed via source/docs]** or **[inferred]**.

Research method note: ~35 web searches/fetches (GitHub READMEs, raw source files, issues/PRs, project docs). Star counts and release dates were only captured where a primary page was fetched; everything else is flagged as a gap rather than guessed.

---

## Key Question 1: Which projects implement L4 (page render + bbox highlight), and how does every candidate classify?

### Takeaway
Only two mature, general-purpose platforms render the original PDF page with a highlight overlay: **RAGFlow** (true bbox-based L4, PDF only, via `react-pdf-highlighter`) and **Kotaemon** (L4-by-text-search: PDF.js viewer opened at the page with a search phrase; no stored coordinates). Everything else in the requested list is L1–L2 (text chunk / link), with Open WebUI, Onyx and Morphik reaching at most a partial L3 (open the file / page image without highlight). A handful of small 2025–2026 hobby/demo repos (sift, DocAI, RagenAI, LlamaIndex sec-insights) implement L4-style highlighting with PyMuPDF/Docling coordinates or text-layer matching, and are worth studying but not reusing as platforms.

### Comparison table

| Project | Level | License | Stack (FE / BE) | Parser pipeline | Highlight coordinates | Web-search citations | Maturity notes |
|---|---|---|---|---|---|---|---|
| RAGFlow | **L4** (PDF only) [confirmed] | Apache-2.0 | React / Go (1.0) + Python (legacy) | DeepDoc (layout+OCR+table, ONNX); MinerU & Docling optional | `position_int` = [page,x0,x1,y0,y1] stored per chunk in doc engine; rendered by `react-pdf-highlighter` | Yes (agent/search features; not verified in depth) | 91.8k stars; 1.0.0-rc1 2026-09-29 |
| Kotaemon | **L4 by text search** (PDF only) [confirmed] | Apache-2.0 | Gradio (Python) / Python | Default PDF/HTML/XLSX loaders; Unstructured, Docling, PaddleOCR, Azure DI, Adobe PDF Extract optional | No coordinates: `data-page` + `data-search` phrase → PDF.js find | Yes (web search pipeline exists; not verified here) | 25.8k stars; last release v0.12.0 May 2025 (third-party claim) |
| Open WebUI | L2 (CitationModal chunk text); L3 only via model-invoked `display_file` tool [confirmed] | "Open WebUI License" (BSD-3 + branding clause ≥50 users) | Svelte / Python | Built-in readers (own code since 0.11.4), Azure DI, Docling/Tika etc. | None; PR #25076 (page navigation) closed unmerged 2026-08-28 | Yes | 0.11.x line, autumn 2026 |
| Dify | L2 [confirmed] | Apache-2.0 + extra conditions (logo, multi-tenant) | Next.js / Python | Own ETL / Unstructured | None (segment text + score) | Yes | widely used; version not captured |
| AnythingLLM | L2 [confirmed via docs/3rd-party] | MIT (prior knowledge, unverified) | React / Node | Own collector | None; page identity not guaranteed after internal splitting | Yes (agents) | — |
| Onyx (ex-Danswer) | L1/L2; "Uploaded Files citations open preview" in v3.3.0 (page jump unconfirmed) | MIT (prior knowledge, unverified) | Next.js / Python | Own PDF extraction (pypdf-class; marker/surya proposed & declined) | None | Yes | v3.3.0 (2026) |
| Khoj | L2 (reference notes) [confirmed via docs] | AGPL-3.0 (prior knowledge, unverified) | Web/Python | Own | None | Yes | — |
| LibreChat | L2 (inline markers → chunk; filename) [confirmed via docs] | MIT (prior knowledge, unverified) | React / Node + Python RAG API | RAG API (pgvector) | None | Yes (via tools) | citations config v1.2.9 |
| Verba (Weaviate) | L2 (chunk view, text highlight inside document text view) [confirmed via blog] | BSD-3 (prior knowledge, unverified) | Next.js / Python | Own readers/chunkers | None (text-level) | No (local only) | — |
| Quivr | L1/L2 [thin evidence] | Apache-2.0 (prior knowledge) | — / Python | LangChain loaders | None | — | — |
| PrivateGPT | L2 (collapsible source panel: filename, chunk, page for PDFs) [3rd-party] | Apache-2.0 (prior knowledge) | Gradio / Python | LlamaIndex loaders | None | No | — |
| Morphik | L3-ish: returns whole **page images** (base64/URL) as chunks; no bbox [confirmed via docs] | BSL 1.1 → Apache-2.0 after 4 yrs | Next.js / Python | ColPali-style visual embeddings (no OCR) | None (page-level only) | — | — |
| PaperQA2 | L2 (in-text citations + per-chunk summaries) [confirmed] | Apache-2.0 (prior knowledge) | CLI/lib Python | Grobid | None | Yes (search agent) | — |
| GPT-Researcher | L1 (inline URL citations in report) [3rd-party] | Apache-2.0 vs MIT (sources conflict) | Next.js / Python | LangChain loaders for local docs | None | Yes | — |
| Perplexica (renamed Vane) | L1 (document-level source cards) [3rd-party] | MIT | Next.js / SearXNG | n/a (web) | None | Yes (core) | — |
| Morphic | L1 (inline numbered + source cards) [3rd-party] | Apache-2.0 | Next.js | n/a (web) | None | Yes (core) | — |
| SurfSense | L1/L2 (citation-backed answers; viewer unconfirmed) | — | Next.js / Python | Own | None | Yes (Reddit/YT/etc.) | — |
| LightRAG | L2 (citation support added Mar 2025; WebUI) [confirmed via README] | MIT (prior knowledge) | React / Python | Own chunking | None | No | EMNLP 2025 |
| Cognita | L0/L1 (no citation UI found) | Apache-2.0 (prior knowledge) | React / Python | Own | None | No | low activity |
| LlamaIndex sec-insights / `@llamaindex/pdf-viewer` | **L4-lite** (react-pdf viewer with citation highlighting) [confirmed via README] | MIT (prior knowledge) | Next.js / FastAPI | LlamaIndex | Text-layer based (inferred) | No | demo, 2023–24 |
| Haystack demos / LangChain examples | not located in this research | — | — | — | — | — | gap |
| Small 2026 repos: sift, DocAI, RagenAI (PR #1085), ThinkIT-V2, KURIPP | L4 prototypes (PyMuPDF word bboxes / react-pdf text-layer / Docling provenance) | varies | React / Python | PyMuPDF, Docling | word-level bbox or text match | No | hobby scale |

### Cited Findings

**RAGFlow**
- README: license Apache-2.0, 91.8k stars; "Latest Updates" lists 1.0.0-rc1 dated 2026-09-29; supported sources "Word, slides, Excel, TXT, images, scanned copies, structured data, web pages"; DeepDoc = "Deep document understanding-based knowledge extraction… layout analysis, OCR, table recognition", CPU inference in 1.0 OSS; core services (API, Admin, Ingestor, Syncer) are "a unified Go service" using CGO + ONNX Runtime, Python only in tooling/SDK; frontend React in `web/`; storage/search options Elasticsearch, Infinity, MySQL, MinIO, Kvrocks, NATS JetStream, ClickHouse; features "Grounded citations with reduced hallucinations… visualization of text chunking… traceable citations" — [RAGFlow README](https://github.com/infiniflow/ragflow)
- Korean README (2025-10-23 update) says MinerU and Docling are supported as document parsing methods — [README_ko](https://github.com/infiniflow/ragflow/blob/main/README_ko.md)
- Frontend `web/src/components/document-preview/pdf-preview.tsx` imports `{ AreaHighlight, Highlight, IHighlight, PdfHighlighter, PdfLoader, Popup } from 'react-pdf-highlighter'`, uses PDF.js worker `/pdfjs-dist/pdf.worker.min.js`, passes `highlight.position` straight to `Highlight`, scrolls to first highlight via `scrollRef` after a 100 ms timeout — [pdf-preview.tsx](https://raw.githubusercontent.com/infiniflow/ragflow/main/web/src/components/document-preview/pdf-preview.tsx) [confirmed]
- Router `document-preview/index.tsx`: `highlights` prop is passed only to `PdfPreviewer` (`isPdf = fileType === 'pdf'`); `positions: number[][]` goes to `ExcelCsvPreviewer` for xlsx/xls; Doc/Ppt/Epub/Txt/Md previewers receive no highlight props — [index.tsx](https://raw.githubusercontent.com/infiniflow/ragflow/main/web/src/components/document-preview/index.tsx) [confirmed]
- Retrieval API returns per chunk a `positions` field, each entry a 5-value array `[page, x0, x1, y0, y1]` (example `[1, 120, 40, 200, 60]`) — [Dosu-generated RAGFlow API summary](https://app.dosu.dev/49f9a533-7254-4314-b3e9-a7313bc58cc8/documents/b6b6bd7c-0dcf-4c53-a1fa-cfc0f30f1e97) (third-party, verify against official API); the chunk API returns HTTP 500 if any `positions` entry is not length 5 (`api/apps/restful_apis/chunk_api.py` lines 113-118) — [Issue #18148](https://github.com/infiniflow/ragflow/issues/18148)
- Issue #18148 states "Highlighting is PDF-only by design, handled in `web/src/components/document-preview/index.tsx:26-39`" — [Issue #18148](https://github.com/infiniflow/ragflow/issues/18148)

**Kotaemon**
- README: Apache-2.0; 25.8k stars; "Advanced citations with document preview… view citations (incl. relevant score) directly in the in-browser PDF viewer with highlights"; built on Gradio; default supported types `.pdf, .html, .mhtml, .xlsx`, other types (`.doc, .docx, …`) require Unstructured (full Docker image); optional PDF.js: download PDF_JS_DIST and extract to `libs/ktem/ktem/assets/prebuilt`; multimodal parsing options Azure Document Intelligence (API), Adobe PDF Extract (API), Docling (local), PaddleOCR (local) — [Kotaemon README](https://github.com/Cinnamon/kotaemon)
- `libs/ktem/ktem/utils/render.py` builds `<a href="#" class="pdf-link" data-src="{BASE_PATH}/file={pdf_path}" data-page="{page_idx}" data-search="{highlight_text}" data-phrase="{phrase}">`; `page_idx = int(doc.metadata.get("page_label", 1))`; function returns early unless `file_type == application/pdf`; no coordinate/bbox fields generated; `phrase` set false for ja/cn (via `fast_langdetect`), true otherwise — [render.py](https://raw.githubusercontent.com/Cinnamon/kotaemon/main/libs/ktem/ktem/utils/render.py) [confirmed: text-search based, not bbox]
- `libs/ktem/ktem/assets/js/main.js`: `scrollToCitation` clicks the `.pdf-link` inside the enclosing `<details>` when the PDF modal is open; `fullTextSearch` uses MiniSearch to find the best-matching sentence and wraps it in `<mark>` in the info panel — [main.js](https://raw.githubusercontent.com/Cinnamon/kotaemon/main/libs/ktem/ktem/assets/js/main.js)
- Third-party evaluation: last release v0.12.0 dates from May 2025; "not recommended for new adoption" (as of July 2026) — [ai-radar.aoe.com](https://ai-radar.aoe.com/tools/kotaemon/) (opinion, secondary)
- Docling is available in Kotaemon as the `DoclingReader` loader — [Docling integrations](https://docling-project.github.io/docling/integrations/kotaemon/)

**Open WebUI**
- Discussion #20829 (Jan 2026): clicking a source doesn't go to the passage; proposal = open PDF at page + highlight via stored bboxes (preferred) or text-search fallback, PDF.js — [Discussion #20829](https://github.com/open-webui/open-webui/discussions/20829)
- PR #25076 "open exact PDF citations in source panel": adds right-side SourcePanel reusing existing PDFViewer, navigates to `metadata.page` when present; explicit non-goals "no highlighting, no bbox support and no non-PDF viewers"; closed **unmerged** by maintainer on 2026-08-28 (merge conflicts, inactivity), pointing to `display_file` in 0.11.1; author noted default DOCX loader extracts text without page numbers — [PR #25076](https://github.com/open-webui/open-webui/pull/25076) [confirmed]
- v0.11.1: `display_file` tool lets a model open a PDF/Word/slide deck at a page or slide number ("a reply that cites something on page 76 can put that page in front of you") — requires Open Terminal connection, tool-capable model, and a model tool call (criticised in the PR thread as a separate workflow just to inspect evidence) — [v0.11.1 release](https://github.com/open-webui/open-webui/releases/tag/v0.11.1); [v0.11.1 blog](https://openwebui.com/blog/v0-11-1-the-model-learns-to-stop-and-ask)
- v0.11.4: readers for text/HTML/Word/CSV/PDF/Azure DI are now in-house rather than `langchain-community`; citation-link fix — [v0.11.4 release](https://github.com/open-webui/open-webui/releases/tag/v0.11.4)
- Peer-reviewed description of Open WebUI (arXiv 2510.02546) — [arXiv](https://arxiv.org/abs/2510.02546)

**Dify**
- "Citation and Attribution" toggle shows below the response: segment text, segment number, match score; "Link to Knowledge" goes to the segment list; chatflow only (workflow apps must format `Array[object]` themselves); no page field documented — [Dify legacy docs](https://legacy-docs.dify.ai/guides/knowledge-base/retrieval-test-and-citation); [Discussion #7635](https://github.com/langgenius/dify/discussions/7635); [Discussion #21599](https://github.com/langgenius/dify/discussions/21599)
- Complaint: too many citations displayed below the reply when only 3 used — [Discussion #10897](https://github.com/langgenius/dify/discussions/10897)

**AnythingLLM**
- Shows retrieved chunks with score per answer; each vector stores original text; "no guarantee that relevant text stays together"; page-level identity not promised, workaround = upload per-page records — [AnythingLLM docs](https://docs.useanything.com/chatting-with-documents/rag-in-anythingllm); [OPProductivity preprocessing tool](https://github.com/OPProductivity/anythingllm-pdf-parser-embedder-assistant); [InsiderLLM guide](https://insiderllm.com/guides/anythingllm-setup-guide/)

**Onyx**
- Inline citations with links to source documents; 51 connectors — [teamazing review](https://www.teamazing.com/blog/onyx-ai-enterprise-review-2026/); [Onyx repo](https://github.com/onyx-dot-app/onyx)
- v3.3.0: "Uploaded Files citations open preview"; PDF image extraction fix — [v3.3.0 notes](https://newreleases.io/project/github/onyx-dot-app/onyx/release/v3.3.0)
- Issue #1543 "Citations are sometimes wrong" (irrelevant/made-up citations) closed not planned; Issue #1938 "Improve PDF text extraction" (marker/surya) closed not planned — [#1543](https://github.com/onyx-dot-app/onyx/issues/1543); [#1938](https://github.com/onyx-dot-app/onyx/issues/1938)

**Khoj** — chat "shows reference notes used to generate a response" — [Khoj chat docs](https://docs.khoj.dev/features/chat/)

**LibreChat** — agents File Search with citations; `maxCitations` (default 30), `maxCitationsPerFile` (7), `minRelevanceScore` (0.45); maintainer: "citations are not guaranteed to work every time since it's up to the LLM"; Sources UI was on 2025 roadmap, shipping unconfirmed — [config v1.2.9](https://www.librechat.ai/changelog/config_v1.2.9); [Discussion #9889](https://github.com/danny-avila/LibreChat/discussions/9889); [2025 roadmap](https://www.librechat.ai/blog/2025-02-20_2025_roadmap)

**Verba** — UI shows chunks behind an answer and highlights the matching paragraph; Document Explorer has Content/Chunk/Vector views — [Weaviate blog](https://weaviate.io/blog/verba-open-source-rag-app); [Verba releases](https://github.com/weaviate/Verba/releases)

**PrivateGPT / Quivr** — citations as JSON (filename + chunk id + score), Gradio collapsible source panel; page numbers depend on loader — [PromptQuorum comparison](https://www.promptquorum.com/power-local-llm/anythingllm-vs-privategpt-vs-openwebui-rag) (secondary); Quivr "source references in a web app" only — [PromptQuorum RAGFlow review](https://www.promptquorum.com/power-local-llm/ragflow-document-understanding-rag-review)

**Morphik** — ColPali retrieval returns whole page images as chunks; `output_format` base64 (default) / URL (temporary HTTPS links) / text (OCR→markdown); no bbox/citation feature documented — [Morphik ColPali docs](https://dev.morphik.ai/docs/concepts/colpali) [confirmed]; license BSL 1.1 with automatic Apache-2.0 relicensing 4 years after each release — [morphik-core fork README](https://github.com/tZimmermann98/morphik-core); HN launch — [Show HN](https://news.ycombinator.com/item?id=43601495)

**PaperQA2** — "high accuracy RAG… PDFs, text files, Microsoft Office documents, source code"; in-text citations; per-chunk summaries re-scored; Grobid parser; Citation Traversal tool — [paper-qa repo](https://github.com/future-house/paper-qa); [medevel](https://medevel.com/paperqa2/)

**LightRAG** — "LightRAG now supports citation functionality" (Mar 2025); web UI + REST API — [LightRAG repo](https://github.com/hkuds/lightrag)

**Cognita** — UI for collections/data sources/indexing; no citation UI found in any source — [Cognita repo](https://github.com/truefoundry/cognita)

**GPT-Researcher / Perplexica (Vane) / Morphic** — Perplexica MIT; Morphic Apache-2.0; GPT-Researcher listed MIT by RepoCloud; "Perplexica and Morphic… cite at the document level. They don't do passage-level attribution" — [Perplexica guide](https://joshuaopolko.com/perplexica-self-hosted-guide/); [Morphic review](https://sites.google.com/view/aitoolfree/morphic-ai-review); [RepoCloud](https://repocloud.io/details/Vane/); [localaimaster](https://localaimaster.com/blog/local-answer-engine-citations)

**SurfSense** — "answers that cite their sources"; MakeUseOf review: citation-backed PDF chat but no viewer-highlight confirmed — [SurfSense repo](https://github.com/MODSetter/SurfSense); [WindowsForum summary](https://windowsforum.com/news/surfsense-on-windows-local-pdf-chat-model-choice-and-privacy-limits-vs-notebooklm.446459/)

**LlamaIndex sec-insights** — features list "PDF viewer with citation highlighting"; extracted as `@llamaindex/pdf-viewer` ("PDF viewer component as used by secinsights. Using react-pdf"), `PDFViewer` wrapped in `PdfFocusProvider`; react-pdf v9 / pdfjs v4 Next.js caveats — [sec-insights](https://github.com/run-llama/sec-insights); [run-llama/pdf-viewer](https://github.com/run-llama/pdf-viewer); [npm](https://www.npmjs.com/package/@llamaindex/pdf-viewer)

**Small 2026 L4 prototypes**
- sift: PyMuPDF per-page word bboxes, longest contiguous matching run against the quote, colored highlights in side-by-side viewer — [sift](https://github.com/Hamzamazhar1999/sift)
- DocAI (Sep 2026): react-pdf, "source highlight when text layer matches"; page-aware chunking with PyMuPDF — [doc-ai PR #1](https://github.com/itsnghianvn/doc-ai/pull/1)
- RagenAI PR #1085: element-level provenance (Docling), citation highlights the paragraph, tested in real pdf.js — [RagenAI PR](https://github.com/webamigos/RagenAI/pull/1085)
- Feature requests in other repos asking for exactly this (ThinkIT-V2 #31, KURIPP #19, RAGbot #67, pdf-chat-langchain-rag #2) — [ThinkIT](https://github.com/yan-ulc/ThinkIT-V2/issues/31); [KURIPP](https://github.com/Jay-Raam/KURIPP/issues/19); [RAGbot](https://github.com/avikumart/RAGbot/issues/67)
- 21st.dev "bounding-box citations" UI component (hover extracted field → highlight source region) — [21st.dev](https://21st.dev/@extend/components/bounding-box-citations)
- Towards AI "Visual Grounding for Advanced RAG" (Docling + LangChain + Qdrant; highlights passages on the document) — [Towards AI](https://pub.towardsai.net/visual-grounding-for-advanced-rag-frameworks-3034161ab7d8)

### Inferences
- The L4 "market" among self-hostable platforms is effectively RAGFlow alone for coordinate-accurate highlighting; Kotaemon achieves a visually similar result with a much cheaper mechanism (page + PDF.js find) that is fragile for OCR'd/CJK text and depends on the LLM quoting verbatim.
- Web-search citation UX (Perplexica/Morphic/Onyx/Open WebUI) and document-grounding UX are built as separate systems in every project; none unify a web source card and a page-highlight card in one citation model — an opening for the user's product.
- Licenses stated as "prior knowledge, unverified" (AnythingLLM MIT, Onyx MIT, Khoj AGPL-3.0, Quivr/PrivateGPT/PaperQA2/Cognita Apache-2.0, Verba BSD-3, LightRAG MIT) were not re-confirmed in this research; treat as likely but check LICENSE files before relying on them.
- RAGFlow's 1.0 rewrite to Go (Sep 2026) means forking the highlight pipeline now means forking Go code (`Go TokenChunker`) as well as the legacy Python path; both exist in the repo per issues #18148/#18149.

### Gaps
- Star counts / release cadence for Open WebUI, Dify, AnythingLLM, Onyx, Khoj, LibreChat, Verba, Quivr, PrivateGPT, PaperQA2, LightRAG, Cognita, SurfSense, Perplexica, Morphic, GPT-Researcher were not captured from primary pages (GitHub rate-limited; budget).
- Haystack demos and LangChain citation examples: no sources located; cannot classify.
- Whether Onyx v3.3.0's "open preview" jumps to the cited page is unconfirmed.
- GPT-Researcher license conflict (MIT per RepoCloud vs Apache-2.0 in prior knowledge) unresolved.

---

## Key Question 2: How exactly do RAGFlow and Kotaemon implement PDF highlight citations?

### Takeaway
RAGFlow: DeepDoc layout parser emits per-text-segment PDF coordinates (`_pdf_positions`), chunking merges them into a per-chunk `position_int` list of `[page, x0, x1, y0, y1]` stored alongside the chunk in the doc engine (ES/Infinity), returned by the retrieval/chunk API as `positions`, and drawn by `react-pdf-highlighter` (PDF.js) in `web/src/components/document-preview/pdf-preview.tsx`. Kotaemon: no coordinates at all — the citation pipeline asks the LLM for verbatim evidence strings, the info panel marks them with `<mark>`, and the PDF.js viewer is opened at `page_label` with the evidence as a search phrase (`data-search` / `data-phrase`).

### Cited Findings
**RAGFlow pipeline (from Issue #18148, confirmed via source paths)**
- Parser stage: `normalize_pdf_items_metadata` then `finalize_pdf_chunk` in `rag/flow/parser/pdf_chunk_metadata.py:178` builds `position_int` from the per-segment `_pdf_positions` list — [Issue #18148](https://github.com/infiniflow/ragflow/issues/18148)
- Chunker: `TokenChunker._merge_text_chunks_by_token_size` in `rag/flow/chunker/token_chunker.py` (Python) and an equivalent Go `TokenChunker` (#18149) merge segment coordinates into chunks — [#18148](https://github.com/infiniflow/ragflow/issues/18148); [#18149](https://github.com/infiniflow/ragflow/issues/18149)
- Storage/retrieval: `position_int` is included in the retriever's `src` fields at `rag/nlp/search.py:155`; `list_chunks` in `api/apps/restful_apis/chunk_api.py` returns them; legacy path used `pdf_parser.crop(..., need_position=True)` in `rag/nlp/__init__.py:tokenize_chunks` — [Issue #18148](https://github.com/infiniflow/ragflow/issues/18148)
- Frontend: `react-pdf-highlighter` components (`PdfLoader`, `PdfHighlighter`, `Highlight`, `AreaHighlight`, `Popup`); `PdfLoader` fetches with auth headers; `highlightTransform` chooses `Highlight` for text vs `AreaHighlight` for image; scroll to first highlight after 100 ms — [pdf-preview.tsx](https://raw.githubusercontent.com/infiniflow/ragflow/main/web/src/components/document-preview/pdf-preview.tsx)
- Chunk highlight is "PDF-only by design"; Excel gets cell `positions`, other types nothing — [index.tsx](https://raw.githubusercontent.com/infiniflow/ragflow/main/web/src/components/document-preview/index.tsx)
- DeepDoc described as vision-model based layout analysis of document screenshots (third-party Korean tutorial) — [wikidocs](https://wikidocs.net/369113)

**Kotaemon pipeline**
- `render.py` emits `pdf-link` anchors with `data-src`, `data-page` (from `metadata["page_label"]`), `data-search` (full evidence text stripped of newlines/quotes), `data-phrase` (true except for ja/cn); only for `application/pdf` — [render.py](https://raw.githubusercontent.com/Cinnamon/kotaemon/main/libs/ktem/ktem/utils/render.py)
- `main.js`: info-panel highlighting via MiniSearch + `<mark>`; clicking a citation while the PDF modal is open triggers the `.pdf-link` click — [main.js](https://raw.githubusercontent.com/Cinnamon/kotaemon/main/libs/ktem/ktem/assets/js/main.js)
- The citation pipeline (`CitationPipeline` / `CiteEvidence`) expects the LLM to return an `evidences` list; when the model returns a string, pydantic validation fails and no highlight is shown (Issue #469); with Llama3.1-8B highlights appear "less than once in ten times", log shows "Got 0 cited docs" (Issue #541) — [#469](https://github.com/Cinnamon/kotaemon/issues/469); [#541](https://github.com/Cinnamon/kotaemon/issues/541)
- HN demo post: users can choose between "highlights" and "inline citations"; clicking a sentence shows its supporting source — [Show HN kotaemon](https://news.ycombinator.com/item?id=42608494)
- In-browser PDF.js viewer requires manually extracting PDF_JS_DIST into `libs/ktem/ktem/assets/prebuilt` — [Kotaemon README](https://github.com/Cinnamon/kotaemon)

### Inferences
- [inferred] RAGFlow must convert `[page, x0, x1, y0, y1]` into react-pdf-highlighter's `position` object (`{pageNumber, boundingRect{x1,y1,x2,y2,width,height}, rects[]}`) in a hook not fetched here; the coordinate order in the API (x0, x1, y0, y1 rather than x0, y0, x1, y1) is a classic source of off-by-axis bugs for integrators.
- [inferred] Kotaemon's JS that actually consumes `data-search`/`data-phrase` (opening PDF.js with a find query) was not located; the attribute names strongly imply PDF.js' built-in find controller (`#search=` / `phrase` match), i.e. highlight accuracy = quality of PDF text layer + exact-match of LLM quote.
- RAGFlow's design decouples highlight from the LLM (coordinates come from parsing, not from the answer), so highlights are stable regardless of model size — the opposite trade-off from Kotaemon.

### Gaps
- RAGFlow's exact hook converting `positions` → `IHighlight` (likely under `web/src/hooks/` or `pages/document-viewer`) not fetched.
- Kotaemon's PDF viewer JS consuming the `pdf-link` attributes not fetched (raw path unknown).
- DeepDoc internals (model names, coordinate units — PDF points vs rendered px) not confirmed.

---

## Key Question 3: Which frontend components exist for rendering documents with highlight overlays?

### Takeaway
For PDFs the practical open-source choices are PDF.js-based React wrappers (`react-pdf-highlighter` MIT but stale, its `-extended` fork, `react-pdf` + custom overlay, `@llamaindex/pdf-viewer`) or the newer PDFium-WASM **EmbedPDF** (MIT core + annotation plugin). Commercial SDKs (Nutrient/PSPDFKit, Apryse) are quote-priced ($1.5k entry to $15–45k+/yr). Office formats have no comparable "highlight overlay" viewer; OnlyOffice (AGPLv3) and Collabora (MPL-2.0) are full editor servers, not annotation canvases.

### Cited Findings
- `react-pdf-highlighter` (agentcooper): "Set of React components for PDF annotation. Built on top of PDF.js"; text and image highlights, popover, scroll-to-highlight; highlight data format independent of zoom/viewport ("easy to save on a server"); base `PdfHighlighter` renders nothing itself, expects a user-defined highlight container child; MIT; latest npm 8.0.0-rc.0 published ~2 years ago — [GitHub](https://github.com/agentcooper/react-pdf-highlighter); [npm](https://www.npmjs.com/package/react-pdf-highlighter)
- Fork `react-pdf-highlighter-extended` (DanielArnould): adds HighlightContext/PdfHighlighterContext/MonitoredHighlightContainer, zoom support, exposed styling, bugfixes — [fork](https://github.com/DanielArnould/react-pdf-highlighter)
- React PDF Viewer highlight plugin: renders a target element after text selection (note-taking style) — [react-pdf-viewer.dev](https://react-pdf-viewer.dev/plugins/highlight/)
- `@llamaindex/pdf-viewer`: react-pdf based, `PDFViewer` + `PdfFocusProvider`; needs dynamic import on Next.js (`Promise.withResolvers` error with react-pdf v9 / pdfjs v4) — [run-llama/pdf-viewer](https://github.com/run-llama/pdf-viewer)
- EmbedPDF: MIT core "and all standard plugins"; annotation plugin (highlight, sticky notes, free text, ink); built on PDFium compiled to WebAssembly (PDFium is Apache-2.0, separate notice obligation); React viewer page lists plugins as separate packages — [embedpdf.com](https://www.embedpdf.com/); [React viewer](https://www.embedpdf.com/react-pdf-viewer); [GitHub v2](https://github.com/embedpdf/embed-pdf-viewer/tree/v2)
- Nutrient (PSPDFKit): commercial, free trial license; third-party estimates $15,000–45,000/yr mid-market, self-hosted $25k–200k+ — [npm pspdfkit](https://www.npmjs.com/package/pspdfkit); [Capterra](https://www.capterra.com/p/171174/PSPDFKit-SDK/); [Vendr](https://www.vendr.com/marketplace/pspdfkit)
- Apryse WebViewer: "Entry-level packages from $1,500", consumption-based; Vendr median $24,351/yr; described as source-available (not open source) — [Apryse pricing](https://apryse.com/pricing); [Vendr Apryse](https://www.vendr.com/marketplace/apryse)
- OnlyOffice Docs Community Edition AGPL v3; Enterprise/Developer editions commercial; sources conflict on a 20-connection cap — [OnlyOffice compare editions](https://www.onlyoffice.com/compare-editions); [elest.io](https://blog.elest.io/collabora-online-vs-onlyoffice-which-self-hosted-office-suite-after-the-euro-office-fork/)
- Collabora Online: MPL-2.0, free CODE edition — [Wikipedia Collabora Online](https://en.wikipedia.org/wiki/Collabora_Online)
- DEV article walkthrough: "React PDF bbox Highlighting: Show RAG Citations on the Page" — [dev.to](https://dev.to/fangtanbamrung/react-pdf-bbox-highlighting-show-rag-citations-on-the-page-il9)
- OpenDataLoader blog (2026-06-04) on citing PDF sources with bounding boxes — [bswen](https://docs.bswen.com/blog/2026-06-04-pdf-source-citations-rag/)

### Inferences
- RAGFlow is production evidence that `react-pdf-highlighter` works at scale despite stale releases; the `-extended` fork or EmbedPDF are the lower-risk choices for a new build in 2026.
- EmbedPDF's PDFium engine renders page bitmaps itself, which also makes it a candidate for server-side page rasterisation parity (same engine client/server) — [inferred].
- Highlight data should be stored page-relative and normalised (0–1 fractions, top-left origin) so any viewer (PDF.js, PDFium, `<img>` page raster) can draw it — practice seen in Docling-based projects below.

### Gaps
- PDF.js (Apache-2.0) and react-pdf (MIT) licenses are prior knowledge, not re-fetched.
- `@react-pdf-viewer` license terms not checked.
- No open-source viewer with highlight overlays for PPTX/DOCX/XLSX was found; this likely does not exist outside page-rasterisation approaches.

---

## Key Question 4: How do these projects deal with non-PDF formats (PPTX, DOCX, XLSX, HWP, images)? Does anyone convert everything to PDF/page images first?

### Takeaway
No surveyed platform highlights inside non-PDF originals. RAGFlow and Kotaemon gate highlighting on `file_type == pdf` (RAGFlow gives Excel cell positions only); Open WebUI's DOCX loader has no page numbers; Open WebUI 0.11.1 `display_file` can open Word/slides at a page/slide but only through a model tool call. None of the platforms documents a "convert everything to PDF first" ingestion step; the standard DIY route is LibreOffice headless (`soffice --headless --convert-to pdf`, ~3.3 s/file when respawning per file). HWP is unsupported by RAGFlow and Kotaemon; options are LlamaIndex `HWPReader` (text only, no tables/HWPX), commercial Hancom/Polaris loaders, or community HWP5/HWPX parsers.

### Cited Findings
- RAGFlow: highlight props only reach `PdfPreviewer`; `positions` (cell coords) reach `ExcelCsvPreviewer`; Doc/Ppt/Epub/Txt/Md previewers get none — [index.tsx](https://raw.githubusercontent.com/infiniflow/ragflow/main/web/src/components/document-preview/index.tsx); Issue #9957 (v0.20.4): chunked DOC files cannot be previewed in KB view — [#9957](https://github.com/infiniflow/ragflow/issues/9957); DOCX text-box and content-control recovery PRs #19735, #20221 concern extraction, not positions — [#19735](https://github.com/infiniflow/ragflow/pull/19735); [#20221](https://github.com/infiniflow/ragflow/pull/20221)
- Kotaemon: `render.py` returns early unless `application/pdf`; `.doc/.docx` need Unstructured — [render.py](https://raw.githubusercontent.com/Cinnamon/kotaemon/main/libs/ktem/ktem/utils/render.py); [README](https://github.com/Cinnamon/kotaemon)
- Open WebUI: "OWUI's default DOCX loader extracts text without page numbers, so Word files lack a location to open" (PR author) — [PR #25076](https://github.com/open-webui/open-webui/pull/25076); 0.11.1 `display_file` takes a page or slide number for PDFs, Word documents, slide decks; 0.11.2 Word/slides previewed "as the finished document" — [0.11.1 blog](https://openwebui.com/blog/v0-11-1-the-model-learns-to-stop-and-ask); [0.11.2 notes](https://traceary.com/open-webui/v0-11-2)
- Morphik treats documents as lists of page images (PDF examples only) — [Morphik docs](https://dev.morphik.ai/docs/concepts/colpali)
- PaperQA2 accepts Microsoft Office documents (text extraction, no visual) — [paper-qa](https://github.com/future-house/paper-qa)
- LibreOffice headless: `soffice --headless --convert-to pdf --outdir`; JSON filter options incl. `PageRange`; forum report ~3.3 s per DOCX over 1,000+ files due to per-file start-up; keep one long-running instance — [converterer cheatsheet](https://www.converterer.com/blog/libreoffice-headless/); [Ask LibreOffice](https://ask.libreoffice.org/t/headless-libreofficewriter-slow-to-export-docx-to-pdf/51995); [Go + LibreOffice server example](https://github.com/wteja/pdf-converter)
- HWP: RAGFlow README_ko lists Word/slides/Excel/text/images/scans/structured/web, no HWP — [README_ko](https://github.com/infiniflow/ragflow/blob/main/README_ko.md); LlamaIndex `HWPReader` extracts text but not table structure or HWPX (Hancom blog) — [Hancom blog](https://blog.hancom.com/han-addons-six-services-guide/); Hancom Data Loader (commercial) preprocesses HWP/HWPX/PDF/OOXML for RAG — same source; Polaris Office blog on HWP parsing variance — [Polaris](https://www.polarisoffice.com/business-blog/rag-document-parsing-faq/); community repo with own HWP 5.0 + HWPX + PDF(pdfplumber+PDFium) parsers — [AMIGO-KFTC/RAG PR](https://github.com/AMIGO-KFTC/RAG/pull/1); HWP→PDF automation write-up — [mirso](https://blog.mir.so/13)
- Docling: provenance (page_no + bbox + char span) exists per doc item but docling-serve chunk endpoints return item refs without resolved coordinates (Issue #613, workaround = second conversion pass); `docling-langchain` issue asks for bbox in `dl_meta` — [docling-serve #613](https://github.com/docling-project/docling-serve/issues/613); [docling-langchain #29](https://github.com/docling-project/docling-langchain/issues/29)
- Docling bboxes may use `BOTTOMLEFT` origin; projects normalise to top-left fractions before storing — [docling-serve #613](https://github.com/docling-project/docling-serve/issues/613); Medium guide stores boxes in PDF points, multiple bboxes per chunk for tables — [Layout-aware RAG with evidence pins](https://vipulmshah.medium.com/layout-aware-rag-with-evidence-pins-building-clickable-citations-for-pdfs-using-docling-neo4j-5305769759f0)
- langboard PR #560 "preserve Docling structure and page provenance in RAG chunks" — [langboard](https://github.com/yamonco/langboard/pull/560)

### Inferences
- A "normalise everything to PDF (LibreOffice) → parse with layout-aware parser (Docling/DeepDoc/MinerU) → store page+bbox per chunk → render page raster or PDF.js with overlay" pipeline is the only route to L4 for Office/HWP files; no surveyed platform ships it end-to-end, so it is a genuine differentiator.
- HWP→PDF via LibreOffice is unreliable (LibreOffice's HWP import filter is limited; prior knowledge) — Hancom's own converters or HWPX (XML) parsing plus custom layout render are the realistic paths; [inferred, not sourced].
- Kotaemon's PDF-only gating and Open WebUI's missing DOCX page numbers show the common failure: text loaders discard layout, so page-level grounding is lost before chunking.

### Gaps
- No source found confirming any platform runs LibreOffice conversion at ingest for citation purposes.
- HWP support in Docling/MinerU not verified.

---

## Key Question 5: Recurring complaints about citation accuracy, highlight misalignment, speed

### Takeaway
Complaints cluster into (a) coordinate bookkeeping bugs when chunks are merged/overlapped (RAGFlow), (b) LLM-dependent citation extraction that silently fails with small models or malformed JSON (Kotaemon, LibreChat), (c) citations pointing at wrong/irrelevant documents (Onyx, Dify over-citing), and (d) missing deep-linking so users cannot verify claims (Open WebUI). Speed complaints surface around parsing (LibreOffice per-file start-up, Docling second pass) rather than rendering.

### Cited Findings
- RAGFlow #18148 (opened 2026-08-12, closed 2026-09-29): Python `TokenChunker` prepends overlap text without carrying its coordinates → `position_int` covers only non-overlap text → highlight shifted/truncated; fixed by "carry overlap-head PDF positions into new chunk" and a follow-up "prevent chained over-carry" (2026-09-01) — [#18148](https://github.com/infiniflow/ragflow/issues/18148)
- RAGFlow #18149: same bug in Go `TokenChunker`, plus possible page off-by-one when parser outputs skip page normalisation — [#18149](https://github.com/infiniflow/ragflow/issues/18149)
- RAGFlow #4040 (v0.14.1): chunk highlights disappeared in document preview and search results (only chunk images shown) — regression — [#4040](https://github.com/infiniflow/ragflow/issues/4040)
- Kotaemon #469: evidence returned as string not list → `CiteEvidence` validation error → no highlight; #541: with Llama3.1-8B highlights "less than once in ten times", "Got 0 cited docs", suspected over-strict text matching — [#469](https://github.com/Cinnamon/kotaemon/issues/469); [#541](https://github.com/Cinnamon/kotaemon/issues/541)
- Open WebUI #20829: "clicking a source doesn't take you to the exact passage… nothing highlights the text"; PR #25076 closed unmerged; commenter: using `display_file` for evidence requires terminal connection + tool-capable model + model tool call — [#20829](https://github.com/open-webui/open-webui/discussions/20829); [PR #25076](https://github.com/open-webui/open-webui/pull/25076)
- Onyx #1543: citations reference irrelevant files or are made up; citation points to one doc while sources show another — closed not planned — [#1543](https://github.com/onyx-dot-app/onyx/issues/1543)
- LibreChat: citations "not guaranteed… up to the LLM"; `minRelevanceScore` filter can hide the citation UI entirely — [Discussion #9889](https://github.com/danny-avila/LibreChat/discussions/9889)
- Dify: too many citations shown vs actually used — [Discussion #10897](https://github.com/langgenius/dify/discussions/10897)
- AnythingLLM: chunking may break page identity; advice "if citations look wrong, the answer is probably wrong too" — [InsiderLLM](https://insiderllm.com/guides/anythingllm-setup-guide/); [OPProductivity](https://github.com/OPProductivity/anythingllm-pdf-parser-embedder-assistant)
- Docling-serve #613: second conversion pass needed to get bbox for chunks ("costly") — [#613](https://github.com/docling-project/docling-serve/issues/613)
- LibreOffice conversion ~3.3 s/file with per-file process spawn — [Ask LibreOffice](https://ask.libreoffice.org/t/headless-libreofficewriter-slow-to-export-docx-to-pdf/51995)

### Inferences
- Parser-derived coordinates (RAGFlow) fail at chunk-merge boundaries; LLM-derived quotes (Kotaemon/LibreChat) fail at generation. A robust design stores coordinates at the smallest parsed unit (line/word/element) and resolves highlights by aligning the answer's cited span to those units post-hoc, so neither chunk overlap nor model size breaks it — this is what sift (word-level bbox alignment) and RagenAI (element-level provenance) do.
- Page off-by-one and coordinate-origin (bottom-left vs top-left) mismatches are the two most common integration bugs across RAGFlow and Docling-based projects.

### Gaps
- No quantitative benchmarks of highlight accuracy or latency found for any platform.
- Reddit/HN threads specifically complaining about highlight quality were not found beyond the Kotaemon Show HN.

---

## Key Question 6: License implications for a personal project that may become a product

### Takeaway
Permissive and safe to fork/embed: RAGFlow and Kotaemon (Apache-2.0), react-pdf-highlighter and EmbedPDF (MIT; PDFium Apache-2.0 notice), Perplexica (MIT), Morphic (Apache-2.0). Conditional: Open WebUI (branding must stay above 50 users/30 days unless enterprise licence; not OSI), Dify (logo retention; written permission for multi-tenant SaaS). Restrictive for products: Morphik (BSL 1.1 — production use limits until 4-year Apache conversion), Khoj (AGPL-3.0, prior knowledge), OnlyOffice Community (AGPLv3). Commercial viewers (Nutrient, Apryse) are quote-based and expensive for a personal project.

### Cited Findings
- RAGFlow Apache-2.0 — [README](https://github.com/infiniflow/ragflow); Kotaemon Apache-2.0 — [README](https://github.com/Cinnamon/kotaemon)
- Open WebUI License: BSD-3 basis, branding clause added v0.6.6 (date reported as 19 Apr or 5 May 2025 — sources conflict); exceptions ≤50 users in rolling 30 days, written permission, or enterprise licence; not OSI compliant — [wz-it](https://wz-it.com/en/blog/open-webui-license-branding-business/); [areebi](https://www.areebi.com/resources/blog/open-webui-enterprise-license-explained)
- Dify: Apache-2.0 plus conditions — written authorisation for multi-tenant SaaS, Dify logo/copyright must remain in console/apps — [aitoolradar](https://aitoolradar.io/guides/dify); [enterpriseai.tools](https://www.enterpriseai.tools/tools/dify/)
- Morphik Core BSL 1.1, auto-relicense to Apache-2.0 four years after each version's release; HN post called it "fully open-source" (conflict) — [morphik-core README (fork)](https://github.com/tZimmermann98/morphik-core); [Show HN](https://news.ycombinator.com/item?id=43601495)
- EmbedPDF MIT core + plugins; includes PDFium under Apache-2.0 — [embedpdf.com](https://www.embedpdf.com/); react-pdf-highlighter MIT — [GitHub](https://github.com/agentcooper/react-pdf-highlighter)
- Perplexica MIT, Morphic Apache-2.0 — [Perplexica guide](https://joshuaopolko.com/perplexica-self-hosted-guide/); [Morphic review](https://sites.google.com/view/aitoolfree/morphic-ai-review)
- OnlyOffice Community AGPLv3; Collabora MPL-2.0 — [OnlyOffice](https://www.onlyoffice.com/compare-editions); [Wikipedia](https://en.wikipedia.org/wiki/Collabora_Online)
- Nutrient/Apryse commercial pricing — see Key Question 3 citations.

### Inferences
- Forking RAGFlow's `document-preview` + `react-pdf-highlighter` wiring and its `position_int` schema is licence-clean (Apache-2.0 → attribution + NOTICE), and is the fastest way to study a working L4 implementation.
- Studying (not copying) Kotaemon's text-search fallback is useful as the degraded path for scanned/OCR'd or Office-converted files where bboxes are missing.
- Avoid depending on Morphik Core code for a product until the relevant version's BSL change date passes; using it as a reference design is fine.

### Gaps
- Licence texts for AnythingLLM, Onyx, Khoj, Quivr, PrivateGPT, Verba, PaperQA2, LightRAG, Cognita, GPT-Researcher, SurfSense were not fetched in this research.
- Open WebUI exact clause wording and the 50-user definition should be read from the repo LICENSE before product decisions.
