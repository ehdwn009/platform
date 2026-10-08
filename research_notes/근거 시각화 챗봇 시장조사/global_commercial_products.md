# Global commercial AI chat/RAG products: how they visually present evidence (state as of October 2026)

Scope note: Global (non-Korean) products only. Evidence levels used throughout:
- **L0** no citation
- **L1** link / footnote / file-name chip only
- **L2** extracted text snippet or quote shown (hover popover, side panel, "sources" list with excerpt)
- **L3** jumps to the location inside a rendered original document (page/slide/section), with or without a text highlight in the viewer
- **L4** renders the original page image/layout with a highlighted region or bounding box for the exact passage

Method caveat: several primary help pages (Adobe helpx, OpenAI Help Center, Perplexity Help Center) returned HTTP 403 to automated fetch; for those I rely on search-engine excerpts of the official page plus third-party hands-on descriptions, and I flag each case. Where I could not confirm whether the *original page* (vs. extracted text) is rendered, I say so explicitly.

---

## Key Question 1: Which products render the original document with highlighting (L3/L4), and what level is each product at?

### Takeaway
True L4 (original page rendered + passage highlighted) is confirmed only for Adobe Acrobat AI Assistant (PDF-native) and strongly claimed for Hebbia Matrix (finance/legal, enterprise-only); ChatDOC and Humata are L3–L4 for PDFs but with reliability questions. Most general-purpose assistants (ChatGPT, Claude app, Gemini app, Copilot, Perplexity, Notion, Dash, Guru, Dust, Box AI) sit at L1–L2, and even the strongest document-first tools (Gemini Notebook/NotebookLM, Glean) jump to/highlight *extracted text*, not a rendered page image. Microsoft is only now shipping "deep citations" (Word/PowerPoint first) with GA not before 2027.

### Cited Findings (product by product)

**Google NotebookLM → rebranded "Gemini Notebook" (2026)**
- Official help (now hosted at support.google.com/gemininotebook): hovering over an inline citation shows the full quoted text; selecting it takes you to that spot in the source so you can read it in context; saved notes keep clickable citations; answers are meant to draw only on uploaded sources, and out-of-scope requests get a "can't answer" reply — [Google Help: Use chat in Gemini Notebook](https://support.google.com/gemininotebook/answer/16179559?hl=en)
- Third-party guides state NotebookLM was rebranded to Gemini Notebook in July 2026; Google's help center index now lists "Use chat in Gemini Notebook" — [Felloai guide](https://felloai.com/how-to-use-notebooklm/); [Kortex comparison](https://www.kortex-notebooklm.com/blog/notebooklm-vs-chatgpt); [Google Help index](https://support.google.com/notebooklm/answer/14278184)
- Clicking a numbered marker makes the source panel scroll to and highlight the exact passage; XDA's hands-on shows a citation preview with highlighted text from the cited passage — [Tech Insider 2026 guide](https://tech-insider.org/au/how-to-use-notebooklm-2026/); [XDA Developers](https://www.xda-developers.com/notebooklm-scattered-pdf-research-usable/)
- Supported sources: PDFs, Google Docs/Slides, web links, Word, CSV, images, YouTube (transcript only, cites timestamped transcript passages) — [Sourclip guide](https://www.sourclip.com/guides/notebooklm-complete-guide); [Kangaroos guide](https://www.kangaroos.ai/guides/ai-note-taking/notebooklm)
- Critic: citations give only a general pointer; "there's no page number or paragraph reference you can cite in a formal paper"; for very short sources it may cite the whole document — [XDA limitations](https://www.xda-developers.com/notebooklm-limitations/); [AI Agents Library](https://www.aiagentslibrary.com/setup-guides/notebooklm/)
- **Level: L3 (text-viewer highlight).** I could not confirm from any source that the source viewer renders the original PDF page image/layout; all descriptions refer to the quoted text in a source panel. Treat "original page rendering" as unconfirmed.

**Adobe Acrobat AI Assistant (desktop, web, mobile, Knowledge Base, PDF Spaces, Student Spaces, Acrobat Studio)**
- Official help (via search excerpt; page 403'd on direct fetch): "AI Assistant highlights where it found the answer in the document and suggests follow-up questions… Select a source number to jump to the relevant section of the document" — [Adobe helpx: Use AI Assistant to get answers](https://helpx.adobe.com/acrobat/desktop/use-acrobat-ai/generative-ai-features/ai-get-answers.html)
- Knowledge Base (multi-file collections): citations pointing to a file open that file in the same browser tab with the relevant passage highlighted; web-source citations open the webpage — [Adobe helpx: chat citations](https://helpx.adobe.com/knowledge-base/web/user-guide-getting-started/ai-assistant-and-insights/chat-citations.html)
- Mobile/PDF Spaces: each citation shows document title, section, page number; multi-citation links open a list of references and go to the cited section with file name and page highlighted — [Adobe helpx: mobile citations](https://helpx.adobe.com/acrobat/using/view-citations-on-mobile.html)
- Student Spaces: selecting a citation marker takes you to the exact place in the original document; citations appear only when the answer is based on an uploaded document — [Adobe helpx: learn with cited AI](https://helpx.adobe.com/acrobat/web/student-spaces/learn-with-cited-ai.html)
- Adobe's disclaimer: citations show which content supported a response but do not guarantee it is complete or correct — [Adobe helpx: view citations](https://helpx.adobe.com/acrobat/using/view-citations.html)
- Shape of AI pattern library names Adobe as the example of "inline highlights": passages in attached PDFs are highlighted, cited in a summary panel so users can jump to source text; Adobe also cites individual paragraphs within a PDF — [Shape of AI: Citations](https://www.shapeof.ai/patterns/citations)
- **Level: L4 for PDFs** (Acrobat is a PDF renderer; highlight is drawn on the actual page). Non-PDF files are handled by Acrobat's PDF conversion; I found no source describing a distinct evidence view for PPT/XLSX/DOCX.

**Hebbia (Matrix)**
- Third-party descriptions: users can hover over any AI-generated answer "to see the exact highlight in the original document"; a "Verifiable Fact Layer" creates an auditable trail per data point; each output sentence is linked to its origin and clicking a citation highlights the corresponding section in the original document — [Dynamic Business](https://dynamicbusiness.com/ai-tools/hebbia-revolutionizes-ai-interface-meet-the-matrix.html); [note.com case study](https://note.com/gen_ai/n/n558985ff153e?hl=en)
- Matrix UI = spreadsheet grid: rows are documents, columns are questions, every cell a cited answer; each result links back to its source passage — [DataStudios](https://www.datastudios.org/post/hebbia-ai-document-research-for-finance-matrix-workflows-and-pricing); [Hebbia blog](https://www.hebbia.com/blog/5-ways-equity-research-teams-use-hebbia-to-drive-speed-and-insight)
- Pricing: not published; enterprise sales only; third-party estimates ~US$10k/user/yr (Professional) and ~US$3–3.5k (Lite); scanned/poorly formatted files reduce accuracy; connects to SharePoint, Box, Dropbox, Egnyte, Intralinks — [DataStudios](https://www.datastudios.org/post/hebbia-ai-document-research-for-finance-matrix-workflows-and-pricing)
- **Level: L4 claimed** (hover-to-highlight in original doc) but only from vendor/marketing-adjacent sources; no independent hands-on confirming whether the highlight is a rendered-page bounding box vs. text-layer highlight.

**Humata**
- Vendor: answers include citations so users can trace where an insight came from; reviewers describe citations "presented as highlights within the document" and cited links that take you into the source document — [Humata](https://www.humata.ai/); [ToolJunction review](https://www.tooljunction.io/ai-tools/humata-ai); [Seaflux review](https://www.seaflux.tech/blogs/humata-ai-document-analysis-summarizer-pdf-processing/)
- Independent evaluation (arXiv, 2026): Humata's highlighted passages "were not always the information actually used to generate the answer"; rated below ScienceOS and ChatPDF; failed to process one test paper — [arXiv 2605.10125 "Useful for Exploration, Risky for Precision"](https://arxiv.org/pdf/2605.10125)
- **Level: L3–L4 for PDFs** (in-document highlight), with documented highlight/answer mismatch.

**ChatPDF**
- Reviews: "clickable citations that link to specific pages in the document"; "side-by-side chat and document view for easy verification" — [Paperguide](https://paperguide.ai/blog/ai-tools-to-chat-with-pdf/); [Atlas Workspace](https://www.atlasworkspace.ai/blog/pdf-chatbot)
- Competitor claim (Logically): ChatPDF "doesn't highlight the exact passage in the original PDF" — [Logically comparison](https://logically.app/comparisons/chatpdf-alternative/) (vendor-biased)
- Scored highest alongside ScienceOS in the arXiv 2026 academic-tool evaluation — [arXiv 2605.10125](https://arxiv.org/pdf/2605.10125)
- **Level: L3** (page jump in side-by-side viewer); exact-passage highlight disputed/unconfirmed.

**ChatDOC**
- Vendor: click the cited page number to jump to the section; numbered reference markers show the content snippet; highlighting any part of the answer auto-jumps to the corresponding section; three tracing levels — page-level, citation/paragraph-level, and sentence-level; a "Detailed citation" preference toggle; keyboard back/forward for citation jumps (changelog 2023–2024, old) — [ChatDOC blog](https://docs.chatdoc.com/blog/4-advanced-ways-of-using-chatdoc-to-chat-pdf-files/); [ChatDOC Medium](https://medium.com/@chatdocai/top-5-knacks-to-chat-with-pdf-more-effectively-on-chatdoc-7f251cd938a3); [ChatDOC changelog](https://chatdoc.com/log/)
- **Level: L3–L4 for PDF** (sentence-level jump in the PDF viewer); whether a visual highlight box is drawn on the rendered page is not explicitly documented in sources found.

**Glean (Assistant / Chat)**
- Citations appear inline immediately after the statements they support, plus a "View sources" section; hover previews context — [Glean docs: Citations](https://docs.glean.com/user-guide/assistant/glean-chat/glean-chat-citations/glean-citations)
- Deep-linked citations: hover popover shows source details and **page number**, cited text highlighted in blue with the rest of the paragraph for context, an Expand button for a fuller preview; for some file types the link jumps to a specific section (e.g., a slide); Confluence attachments can be highlighted; prerequisites: agentic engine enabled; **limitations: "Navigating directly to the cited page from a citation isn't supported in this release"; tables/code blocks render as plaintext** — [Glean docs: Deep-linked citations](https://docs.glean.com/user-guide/assistant/glean-chat/glean-chat-citations/deep-linked-citations)
- Chat API: optional `snippets` / `referenceRanges` arrays with exact supporting text per inline citation fragment — [Glean developers: Deep Linked Citations](https://developers.glean.com/guides/chat/deep-linked-citations)
- **Level: L2+ (snippet popover with page number); not L3** because click-to-page navigation is explicitly not yet supported.

**Microsoft 365 Copilot**
- Current state: responses show citations to web and work content (OneDrive/SharePoint files, emails, meeting notes) inline; drafted content includes references to the source content; plugins/connectors return a citation with title + URL that renders as a clickable reference — [Office Watch 2026](https://office-watch.com/2026/copilot-word-citations-image-editing/); [M365 Admin: referenced sources cited](https://m365admin.handsontek.net/microsoft-copilot-microsoft-365-referenced-sources-are-cited-in-drafted-content-2/); [Microsoft Learn: custom engine agent UX](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/ux-custom-engine-agent)
- Roadmap ID 523223 "Deep citations": lets users verify by linking directly to the relevant part of the reference; scope starts with Word and PowerPoint; Meetings, Web and PDF later; Preview Jan CY2026, GA **Feb CY2027**; Desktop/Mac/Web; status "In development"; entry last updated 2026-09-30 — [mc.merill.net RM523223](https://mc.merill.net/message/RM523223)
- Conflicting dates elsewhere: Preview Oct CY2026 / GA Nov CY2026, or Preview Aug 2026 — [M365 Admin](https://m365admin.handsontek.net/microsoft-copilot-microsoft-365-deep-citations-copilot/); [WindowsForum](https://windowsforum.com/news/microsoft-365-copilot-deep-citations-preview-in-august-2026.436081/). Dates have slipped repeatedly; treat as unshipped at GA level.
- **Level: L1 today (file/link reference chips); L3 planned via Deep citations.**

**ChatGPT (file uploads, Projects, Deep Research)**
- OpenAI Help on file uploads (search excerpt; page 403'd): 512 MB/file, 2M tokens/file; "ChatGPT Enterprise supports Visual Retrieval for PDF files. All other plans and document files only support text-based retrieval"; images in documents discarded on non-Enterprise plans — [OpenAI Help: File Uploads FAQ](https://help.openai.com/en/articles/8555545-file-uploads-faq)
- Mid-2025 third-party report: "ChatGPT doesn't generate a clickable citation to the PDF, but it can quote the text" — [DataStudios (mid-2025, older)](https://www.datastudios.org/post/chatgpt-pdf-upload-and-analysis-full-report-on-usage-and-capabilities-mid-2025-update)
- Web citations: desktop hover shows a preview of the source; "Sources" button at the bottom of the response; mobile requires click-through — [Typescape](https://typescape.ai/blog/chatgpt-citations-explained/)
- **Level: L1–L2 for uploaded files (unconfirmed for 2026 Projects UI); L1–L2 for web.** I found no OpenAI documentation describing an in-document jump or highlight for uploaded files. Gap flagged below.

**Claude (Anthropic) — app Projects and Citations API**
- App: a 2026 comparison says "for Claude Projects, citation badges link back to source passages where available" and that Projects need prompting for citation discipline; retrieval "can miss relevant passages, and project files are not a full reference system" — [Atlas Workspace: Claude vs NotebookLM](https://www.atlasworkspace.ai/blog/notebooklm-vs-claude-projects)
- API (official, GA on Claude API, Bedrock, Vertex, Foundry): `char_location` (plain text: start/end char index), `page_location` (PDF: `start_page_number` 1-indexed, `end_page_number` exclusive), `content_block_location` (custom content); `cited_text` returned and not billed as output tokens; "Only text citations are currently supported. Image citations are not yet possible"; PDF text is extracted and chunked into sentences; "PDFs that are scans of documents and do not contain extractable text are not citable"; .docx/.xlsx must be converted to plain text — [Claude Platform Docs: Citations](https://platform.claude.com/docs/en/build-with-claude/citations); [Claude cookbook](https://platform.claude.com/cookbook/misc-using-citations)
- **Level: app L1–L2 (unconfirmed in-document highlight); API returns page-range granularity, no bounding boxes.**

**Gemini (app, Gems, Workspace/Drive) and Google APIs**
- Gems: Drive files added as knowledge are kept current; a "Disable knowledge citations" option exists, which also disables citations for files uploaded in Gem chats; Gems on personal accounts transition to "skills" from Nov 2026 — [Google Help: Gems](https://support.google.com/gemini/answer/15146780?hl=en&co=GENIE.Platform%3DDesktop)
- Drive: natural-language search shows an "AI Overview" at the top "complete with citations"; "Ask Gemini in Drive" answers over Drive, Gmail, Calendar, Chat — [Google Workspace blog, March 2026](https://blog.google/products-and-platforms/products/workspace/gemini-workspace-updates-march-2026/)
- Gemini API File Search: `file_citation` annotations include `file_name`, `source`, `page_number` for paged files, `custom_metadata`, and `media_id` for image chunks (downloadable); supports PDF, Word, Excel, PowerPoint, JSON, XML, zip, text/markdown/HTML/CSV/code, PNG/JPEG up to 4K (with `gemini-embedding-2`); no audio/video; 100 MB/file; storage and query embeddings free, indexing billed — [Gemini API docs: File search](https://ai.google.dev/gemini-api/docs/file-search); [Google blog: multimodal File Search](https://blog.google/innovation-and-ai/technology/developers-tools/expanded-gemini-api-file-search-multimodal-rag/)
- Forum report: page-number citation "doesn't work with Gemini 3.5 Flash" (24 replies, unresolved as found) — [Google AI Developers Forum](https://discuss.ai.google.dev/t/file-search-tool-page-number-citation-doesnt-work-with-gemini-3-5-flash/146786)
- Vertex AI Search (being renamed Agent Search): chunk-mode results carry `pageSpan {pageStart, pageEnd}` (1-based) and `documentMetadata {uri, title, mimeType, structData}` — [Google Cloud REST: SearchResult](https://docs.cloud.google.com/generative-ai-app-builder/docs/reference/rest/v1/SearchResult); [Parse and chunk documents](https://cloud.google.com/generative-ai-app-builder/docs/parse-chunk-documents)
- Gemini `GroundingMetadata`: `groundingSupports` have segment start/end indices into the *response text* plus chunk indices; confidence scores not available for Gemini 2.5+ — [Google Cloud: GroundingMetadata](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1/GroundingMetadata); [Grounding with Agent Search](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/grounding/grounding-with-vertex-ai-search)
- **Level: Gemini app L1–L2 (unconfirmed highlight for direct uploads); APIs return page numbers / page spans, no bounding boxes.**

**Perplexity (incl. file upload, Spaces)**
- Official help page 403'd. Third-party: answers about uploads indicate "page-level references"; when mixing file + web, Perplexity merges file passages with linked online citations and "sometimes lists external links even when the core information came from the file"; leaving Focus on Auto/Web can cause web sources to replace PDF content; scanned PDFs are not read (OCR first); plan limits reported inconsistently (free: 3 PDFs/day vs. 5 files/Space, 25 MB/file) — [DataStudios](https://www.datastudios.org/post/perplexity-ai-pdf-reading-file-ingestion-long-document-processing-and-structured-extraction-capab); [Perplexity AI Magazine troubleshooting](https://perplexityaimagazine.com/perplexity-hub/perplexity-pdf-upload-not-working-fixes/); [Markdownconverters](https://markdownconverters.com/learn/ai-file-uploads/perplexity)
- Shape of AI credits Perplexity with popularizing inline multi-source references with title + favicon metadata — [Shape of AI](https://www.shapeof.ai/patterns/citations)
- **Level: L1–L2** (numbered inline chips + page-level reference text); no evidence of in-document jump/highlight.

**Box AI**
- Web app: hover the number(s) in the bottom-left of the response to see which parts of the document were used; mobile: tap to open a small window with the source text; API `include_citations` (default true) returns the source file and direct content citations — [Box docs: Accessing Box AI](https://docs.box.com/en/box-ai/using-box-ai); [Box AI for Mobile](https://support.box.com/hc/en-us/articles/40781628625299-Box-AI-for-Mobile); [Box Dev: Ask questions](https://developer.box.com/guides/box-ai/ai-tutorials/ask-questions)
- Out-of-scope queries fall back to general knowledge; Box warns responses may be inaccurate — [Box docs](https://docs.box.com/en/box-ai/using-box-ai)
- **Level: L2.**

**Dropbox Dash**
- "Provides cited sources so you can verify answers"; March 2026 release notes mention "improved citations" and a chat-first experience over company content and the web — [Dash chat feature page](https://dash.dropbox.com/features/chat); [Dash release notes](https://dash.dropbox.com/release-notes)
- Dropbox's own eval write-up: in production they saw "high ROUGE with missing citations, strong BERTScore with made-up file names" — [Data Tinkerer on Dropbox eval](https://www.datatinkerer.io/p/how-dropbox-made-ai-evaluation-work-at-scale)
- **Level: L1–L2** (no evidence of in-document location).

**Notion AI**
- AI Connectors: answers cite the sources used, including specific Slack messages; owner/admin required to set up; up to 36 h to index large content — [Notion Help: AI Connectors](https://www.notion.com/help/notion-ai-connectors); [Notion guide](https://www.notion.com/help/guides/use-ai-connectors-to-access-more-of-your-teams-knowledge)
- **Level: L1** (source links).

**Guru**
- Positions itself as "cited, permission-aware answers everywhere you work"; "every answer shows where it came from" — [SourceForge listing (promotional)](https://sourceforge.net/software/ai-tools/integrates-with-dropbox-dash/)
- **Level: L1** (card/source link); no independent UX description found.

**Coveo Relevance Generative Answering (RGA)**
- Citations point to the items holding the chunks used; hover shows the chunk; click opens the item; for HTML items the page opens at the chunk's location with the chunk highlighted (`disableCitationAnchoring` turns this off); the same item can be cited multiple times for different chunks — [Coveo docs: configure search interface for RGA](https://docs.coveo.com/en/nb6a0037/)
- Model card: citation precision/recall/F1 measured against the chunks actually used — [Coveo RGA model card](https://docs.coveo.com/en/o5g90043/)
- Coveo "strongly recommends" a disclaimer prompting users to use citation links — [Coveo content requirements](https://docs.coveo.com/en/nb6a0008/)
- **Level: L3 for HTML (anchored highlight); L2 for other item types.** Docs pages are ~1000 days old per search metadata.

**Sana (Sana AI knowledge assistant)**
- Vendor: "accurate citations"; users can narrow knowledge sources per query — [Sana](https://sanalabs.com/meet-sana-ai)
- Shape of AI: Sana's citation popover "highlights the relevant parts of the source" — [Shape of AI](https://www.shapeof.ai/patterns/citations)
- **Level: L2.**

**Dust**
- Review: RAG agents with semantic search, "uses citations to increase accuracy"; outdated data, duplicates, vague titles, non-textual data and deep content weaken retrieval — [Cybernews Dust review](https://cybernews.com/ai-tools/dust-ai-review/)
- **Level: L1–L2** (no documentation found of in-document location).

**Writer**
- Only a corporate description surfaced (enterprise generative AI platform); no documentation of citation UX found — [Wikipedia: Writer Inc.](https://en.wikipedia.org/wiki/Writer_Inc.) **Level: unknown (gap).**

**Elicit / Consensus / SciSpace (academic)**
- Consensus "Citation Grounding": hover a citation to see the supporting passage; click opens the paper at that passage; an "Evidence" tab lists every quote pulled from a paper; web-app only (Pro messages, Deep searches); the API/MCP return metadata and takeaways, not per-claim quotes — [Consensus docs: Citation Grounding](https://docs.consensus.app/core-features/citation-grounding) → **L3**
- Elicit: supporting quote (text snippet) shown per extracted table cell, with explanation; no upload-and-chat equivalent to SciSpace — [IntuitionLabs guide](https://intuitionlabs.ai/articles/elicit-data-extraction-clinical-papers); [Paperguide](https://paperguide.ai/blog/elicit-vs-scispace/) → **L2**
- SciSpace Chat with PDF: single-paper reading; highlight a paragraph to get a plain-language explanation — [Atlas comparison](https://www.atlasworkspace.ai/blog/consensus-vs-scispace) → **L2 (unconfirmed highlight-back of citations)**
- HKUST library: all four tools (Scite, Elicit, Consensus, Scopus AI) produce real citations, "but this does not ensure the citations will match with the arguments generated"; Consensus can extract original text, easing verification — [HKUST Library](https://library.hkust.edu.hk/sc/trust-ai-lit-rev/)

**Others found (document-AI infrastructure with spatial grounding — relevant as enablers, not chat products)**
- Reducto: Extract API returns per-field citations with page reference and bounding box `{left, top, width, height}` normalized to [0,1]; Parse output attaches page + bbox per block; citations unsupported when agent-in-the-loop is enabled; Studio citation viewer for bbox-level inspection — [Reducto docs](https://llms.reducto.ai/json-schema-extraction-with-citations); [Reducto agent workflows](https://llms.reducto.ai/agent-workflows)
- Azure AI Content Understanding: grounding ties each extracted field to page number and bounding box so reviewers can jump to the source — [Microsoft Learn](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/document/enrichments)
- Tensorlake: argues common VLMs "don't return the grounding information needed for citations: bounding boxes, element coordinates" — [Tensorlake blog](https://www.tensorlake.ai/blog/rag-citations)
- Ironclad (legal): extracted values matched to tokens with bounding-box coordinates so a highlight can be drawn on the page — [Ironclad](https://ironcladapp.com/resources/articles/grounding-systems)
- Pinecone Assistant: each citation has a position marker and references with `file` + `pages: [n]`; `include_highlights=true` returns the supporting excerpt (API ≥ 2025-04); no char offsets or in-page coordinates documented — [Pinecone docs: chat](https://docs.pinecone.io/guides/assistant/chat-with-assistant); [Pinecone blog](https://www.pinecone.io/learn/pinecone-assistant-citation-highlights/)
- Amazon Bedrock Knowledge Bases: PDFs on OpenSearch Serverless/Aurora get `x-amz-bedrock-kb-document-page-number` metadata automatically (not with "no chunking"); surfaced in `retrievedReferences[].metadata` — [AWS docs: configure queries](https://docs.aws.amazon.com/bedrock/latest/userguide/kb-test-config.html); [RetrievedReference](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_agent-runtime_RetrievedReference.html); older re:Post threads say no built-in page numbers (superseded) — [AWS re:Post](https://repost.aws/questions/QUt5kzltFER7KYlBNbbD76cQ/bedrock-knowledge-base-pdf-page-numbers)

### Summary table (my classification)

| Product | Level | Original page rendered? | Highlight granularity | Notes |
|---|---|---|---|---|
| Adobe Acrobat AI Assistant | L4 (PDF) | Yes (Acrobat viewer) | passage highlight on page; page no. + section on mobile | Only confirmed L4 among mainstream tools |
| Hebbia Matrix | L4 (claimed) | Claimed | sentence→source highlight on hover | Enterprise-only, ~$10k/seat est. |
| ChatDOC | L3–L4 (PDF) | Yes (PDF viewer) | page / paragraph / sentence tracing | Highlight box not explicitly documented |
| Humata | L3–L4 (PDF) | Yes | in-document highlight | Highlight ≠ actual evidence (arXiv 2026) |
| ChatPDF | L3 | Side-by-side viewer | page-level link | Passage highlight disputed |
| Consensus | L3 | Opens paper at passage | quote-level | Academic papers only |
| Gemini Notebook (NotebookLM) | L3 (text) | Unconfirmed; source panel text | quoted passage highlight | No page numbers |
| Coveo RGA | L3 (HTML) / L2 | HTML anchored | chunk highlight | Other types: open item |
| Glean | L2+ | No (popover) | sentence highlight in blue + page no. | Click-to-page explicitly unsupported |
| Box AI, Sana, Elicit, SciSpace | L2 | No | snippet | |
| Perplexity, ChatGPT, Claude app, Gemini app, Dash, Dust | L1–L2 | No (unconfirmed) | file chip / page-level text | |
| M365 Copilot | L1 (→L3 planned) | No | reference chip | Deep citations GA Feb 2027 (roadmap) |
| Notion AI, Guru | L1 | No | source link | |
| Writer | ? | — | — | No documentation found |

### Inferences
- The L4 pattern exists only where the product *owns the renderer* (Acrobat) or is a vertical, high-price enterprise tool (Hebbia). General assistants have standardised on L1–L2 popovers; this is the gap a "visual grounding"-first product would occupy.
- Even best-in-class horizontal tools (Glean, Gemini Notebook) stop at highlighted *extracted text*; none documented a rendered-page bounding box for PPT/XLSX/images.
- Infrastructure to do L4 (page-level bbox from Reducto, Azure Content Understanding; page numbers from Gemini File Search, Vertex pageSpan, Bedrock metadata, Claude page_location) is now commodity, which lowers the barrier for a new entrant.

### Gaps
- Could not fetch Adobe helpx, OpenAI Help, or Perplexity Help directly (HTTP 403); Adobe statements come from search-engine excerpts of the official pages.
- No source confirmed whether Gemini Notebook's source viewer renders original PDF page images vs. extracted text.
- No official OpenAI documentation found describing the 2026 ChatGPT Projects file-citation UI (click behavior, highlight).
- No official Anthropic documentation found describing in-app Projects citation UI.
- Writer: no citation UX documentation found at all.
- Hebbia: no independent hands-on confirming the rendered-page highlight mechanic.

---

## Key Question 2: How does each product handle PDF vs PPT vs Excel vs Word vs images — does the evidence view differ by file type?

### Takeaway
Almost every product treats PDF as the primary (and often only) target for location-aware evidence; Office formats are typically converted to text, and image content inside documents is either dropped (ChatGPT non-Enterprise, Claude API) or only recently indexed (Gemini File Search with `media_id`). Only Glean ("jump to a slide") and Microsoft's roadmap (Word/PowerPoint first) describe file-type-specific location behavior.

### Cited Findings
- OpenAI: non-Enterprise plans do text-only retrieval and "discard any images"; Enterprise has Visual Retrieval for PDFs — [OpenAI Help: File Uploads FAQ](https://help.openai.com/en/articles/8555545-file-uploads-faq)
- Claude API: PDFs text-extracted and sentence-chunked; scanned PDFs not citable; image citations unsupported; .docx/.xlsx must be converted to plain text before citing — [Claude Platform Docs: Citations](https://platform.claude.com/docs/en/build-with-claude/citations)
- Gemini File Search: indexes PDF, Word, Excel, PowerPoint, JSON, XML, CSV, HTML, code; PNG/JPEG images get `media_id` citations; `page_number` only for paged files — [Gemini API docs](https://ai.google.dev/gemini-api/docs/file-search)
- Glean: deep-linked citation can jump "to a specific section, such as a slide in a presentation"; tables/code blocks shown as plaintext — [Glean docs](https://docs.glean.com/user-guide/assistant/glean-chat/glean-chat-citations/deep-linked-citations)
- Microsoft Deep citations scope order: Word and PowerPoint → Meetings, Web, PDF — [mc.merill.net](https://mc.merill.net/message/RM523223)
- Gemini Notebook: YouTube sources cite timestamped transcript passages, i.e., evidence is always text — [Sourclip guide](https://www.sourclip.com/guides/notebooklm-complete-guide)
- Perplexity: scanned PDFs cannot be read; OCR required — [Perplexity AI Magazine](https://perplexityaimagazine.com/perplexity-hub/perplexity-pdf-upload-not-working-fixes/)
- Reducto bboxes are normalized to [0,1] "for PDFs/images" specifically — [Reducto docs](https://llms.reducto.ai/json-schema-extraction-with-citations)
- Vertex AI Search `pageSpan` is defined for chunks from paged documents (1-based) — [Google Cloud: Parse and chunk](https://cloud.google.com/generative-ai-app-builder/docs/parse-chunk-documents)

### Inferences
- A product that renders *slides as slides* and *sheets as grids* with region highlights would be differentiated; no competitor documents this today.
- Scanned/image-only PDFs are a universal weak spot at the API layer (Claude, Perplexity); OCR + bbox from a document-AI layer (Reducto/Azure) is required for L4 on scans.

### Gaps
- No source describes how Adobe Acrobat AI Assistant presents evidence for converted PPT/XLSX (beyond "it's a PDF now").
- No product documentation found for cell-level (spreadsheet) evidence highlighting in any chat product.

---

## Key Question 3: How do products handle answers with multiple sources (inline numbered, side panel, stacked cards, grid)?

### Takeaway
The dominant pattern is sentence-level inline numbered markers plus a consolidated "sources" list; document-centric tools add a side-by-side viewer, Hebbia uses a document×question grid, and Adobe mobile uses a multi-citation list that opens the chosen file at its page.

### Cited Findings
- Glean: inline citations after each statement + "View sources" section; same source can appear multiple times — [Glean docs](https://docs.glean.com/user-guide/assistant/glean-chat/glean-chat-citations/glean-citations)
- Coveo: the same item can be cited more than once when different chunks were used — [Coveo docs](https://docs.coveo.com/en/nb6a0037/)
- Adobe mobile/PDF Spaces: "Multi-citation links open a list of references and go to the cited section, with the file name and page highlighted" — [Adobe helpx mobile](https://helpx.adobe.com/acrobat/using/view-citations-on-mobile.html)
- Hebbia: grid where rows = documents, columns = questions, each cell cited — [DataStudios](https://www.datastudios.org/post/hebbia-ai-document-research-for-finance-matrix-workflows-and-pricing)
- Gemini Notebook: numbered grey markers per sentence; hover = quote, click = jump — [Google Help](https://support.google.com/gemininotebook/answer/16179559?hl=en)
- Perplexity: inline numbered chips with title/favicon; when file + web mixed, web links may appear even if the file was the real source — [DataStudios](https://www.datastudios.org/post/perplexity-ai-pdf-reading-file-ingestion-long-document-processing-and-structured-extraction-capab)
- Box AI: a single cluster of numbers in the bottom-left of the answer (not per-sentence) — [Box docs](https://docs.box.com/en/box-ai/using-box-ai)
- ChatGPT web: inline citations with hover preview + "Sources" button — [Typescape](https://typescape.ai/blog/chatgpt-citations-explained/)
- Pinecone Assistant API: citation `position` marker in the answer + list of references each with file and pages — [Pinecone docs](https://docs.pinecone.io/guides/assistant/chat-with-assistant)
- Claude API: each text block can carry a `citations[]` array, each with its own location object — [Claude Platform Docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Pattern taxonomy (inline highlights / direct quotations / multi-source references / lightweight links; hover preview, side panel, deep link, inline cues) — [Shape of AI](https://www.shapeof.ai/patterns/citations)

### Inferences
- Per-sentence markers with hover quotes are table stakes; what no horizontal product does is show *multiple rendered page thumbnails* stacked per answer. That is an open UX slot.

### Gaps
- No source details how Adobe desktop Knowledge Base orders or groups citations across multiple files beyond "opens that file".

---

## Key Question 4: Do citations actually increase trust? What complaints exist (hallucinated citations, wrong page, etc.)?

### Takeaway
Experimental evidence shows citations raise self-reported trust *even when the citations are random*, and trust drops once users actually check them — so accurate, verifiable evidence is a differentiator, but the mere presence of citations is not proof of quality. Complaints cluster around: real source/wrong claim (~25% unsupported in one benchmark), highlights that aren't the real evidence (Humata), made-up file names (Dropbox's own eval), missing page references (NotebookLM), and citations pointing to web instead of the uploaded file (Perplexity).

### Cited Findings
- Live QA experiment with zero/one/five relevant-or-random citations: trust rose significantly when citations were present, "a result that held true even when the citations were random"; trust fell significantly when participants checked the citations — [arXiv 2501.01303 "Citations and Trust in LLM Generated Responses"](https://arxiv.org/abs/2501.01303)
- Agent benchmark: every cited link existed, yet 22 of 88 claims (25%) were not supported by the cited source — [DEV Community](https://dev.to/fjfdepedro/my-ai-agents-didnt-fake-citations-one-in-four-still-didnt-hold-391j)
- "A valid link… does not prove the sentence next to it came from there"; resolving links is "necessary and nowhere near sufficient" — [DEV Community (Enhanciar)](https://dev.to/enhanciar_ai_96a4ba4877e3/how-we-make-an-llm-cite-every-answer-down-to-fileline-and-check-the-citation-is-telling-the-truth-hmj)
- Generative search engines audit: nearly half of generated sentences lacked citations and only ~three-quarters of cited ones were supported — [UMD Libraries guide citing the study](https://lib.guides.umd.edu/c.php?g=1340355&p=9880574)
- Library guide: "ghost citations" (fake URLs or real URLs to unrelated content); RAG links "may not be where the AI is actually getting this information" — [UMD Libraries](https://lib.guides.umd.edu/c.php?g=1340355&p=9880574)
- Humata's highlighted passages "not always the information actually used to generate the answer" — [arXiv 2605.10125](https://arxiv.org/pdf/2605.10125)
- Dropbox Dash eval: "high ROUGE with missing citations, strong BERTScore with made-up file names" — [Data Tinkerer](https://www.datatinkerer.io/p/how-dropbox-made-ai-evaluation-work-at-scale)
- NotebookLM: "occasionally the AI will interpret a passage differently than you would, so… click through to check"; no page/paragraph reference; limited-context complaints (book not read past a certain page) — [Tech Insider](https://tech-insider.org/au/how-to-use-notebooklm-2026/); [XDA limitations](https://www.xda-developers.com/notebooklm-limitations/); [Substack comments](https://soloailab.substack.com/p/stop-drowning-in-browser-tabs-learn-any-skill-fast-with-this-ai-tool/comments)
- Perplexity: external links listed even when the file was the real source; Focus mode confusion — [DataStudios](https://www.datastudios.org/post/perplexity-ai-pdf-reading-file-ingestion-long-document-processing-and-structured-extraction-capab)
- Gemini File Search: page numbers reported broken on Gemini 3.5 Flash (forum thread, 24 replies) — [Google AI Dev Forum](https://discuss.ai.google.dev/t/file-search-tool-page-number-citation-doesnt-work-with-gemini-3-5-flash/146786)
- OpenAI file_search: developers report `start_index`/`end_index` not lining up with source quotes and file references "seem to be wrong" — [OpenAI community](https://community.openai.com/t/how-do-you-find-the-used-bit-of-text-with-file-search/1151276); [OpenAI community](https://community.openai.com/t/when-searching-in-the-assistant-the-file-references-seems-to-be-wrong/515157)
- Academic: fabricated references rose from 1 in 2,828 papers (2023) to 1 in 458 (2025) — [STAT News, May 2026](https://www.statnews.com/2026/05/07/lancet-study-finds-steep-rise-fraudulent-citations-academic-papers/)
- HKUST: real citations "do not ensure the citations will match with the arguments" — [HKUST Library](https://library.hkust.edu.hk/sc/trust-ai-lit-rev/)
- Design guidance: show the retrieved chunk text next to each citation so users can see what the model saw; refusing to cite beats a wrong answer with a fake source — [llmbestpractices](https://llmbestpractices.com/ai-agents/rag-citations)
- Vendor disclaimers: Adobe (citations don't guarantee correctness), Box (verify responses), Coveo (recommends on-screen disclaimer prompting users to open citations) — [Adobe helpx](https://helpx.adobe.com/acrobat/using/view-citations.html); [Box docs](https://docs.box.com/en/box-ai/using-box-ai); [Coveo docs](https://docs.coveo.com/en/nb6a0008/)
- "No evidence found" handling: Gemini Notebook returns a "can't answer" style reply for out-of-source requests — [Google Help](https://support.google.com/gemininotebook/answer/16179559?hl=en); Adobe Student Spaces shows citations only when the answer is based on an uploaded document — [Adobe helpx](https://helpx.adobe.com/acrobat/web/student-spaces/learn-with-cited-ai.html); Box AI falls back to general knowledge for out-of-scope queries — [Box docs](https://docs.box.com/en/box-ai/using-box-ai)

### Inferences
- Because random citations still raise trust, an L4 product carries ethical/UX responsibility: the rendered highlight must be the *actual* evidence span (verify-the-citation step, like Enhanciar's approach), or it will amplify misplaced trust.
- Explicit "no supporting passage found" states are rare and under-documented; this is a differentiator opportunity.

### Gaps
- No Reddit/HN threads surfaced in search results specifically on "citation points to wrong page" for NotebookLM/ChatGPT; evidence here is from guides, academic papers and vendor evals.
- arXiv 2501.01303 abstract gives no participant count or effect sizes.
- No G2/Capterra review excerpts on citation UX were retrievable within budget (gap for enterprise tools like Glean, Guru, Coveo, Writer).

---

## Key Question 5: Which products expose evidence via API/SDK and at what granularity?

### Takeaway
Text-offset and page-number granularity is widely available (Anthropic, Gemini, Vertex, Bedrock, Pinecone, Glean, Box, Coveo); bounding-box granularity is available only from document-parsing/extraction APIs (Reducto, Azure AI Content Understanding), not from any chat/RAG answer API. A builder must therefore compose a page-number/char-offset citation with a separate layout layer to achieve L4.

### Cited Findings
| API | Granularity returned | Source |
|---|---|---|
| Anthropic Citations (Claude API, Bedrock, Vertex, Foundry) | `char_location` (start/end char index), `page_location` (`start_page_number` 1-indexed, `end_page_number` exclusive), `content_block_location`; `cited_text` free of output-token cost; no bbox; no image citations; scanned PDFs not citable | [Claude Platform Docs](https://platform.claude.com/docs/en/build-with-claude/citations) |
| OpenAI file_search (Responses/Assistants) | `file_citation` with `file_id`, `index` (position in results), `start_index`/`end_index` in response text; no page number documented; developers report misalignment | [OpenAI docs: File search](https://developers.openai.com/api/docs/guides/tools-file-search); [OpenAI community](https://community.openai.com/t/assistant-api-annotations/730817) |
| Gemini API File Search | `file_citation` annotations: `file_name`, `source`, `page_number` (paged files), `custom_metadata`, `media_id` for image chunks | [Gemini API docs](https://ai.google.dev/gemini-api/docs/file-search) |
| Gemini GroundingMetadata (Vertex) | `groundingSupports` with response-text segment start/end + chunk indices; `groundingChunks` with title/uri; confidence not available Gemini 2.5+ | [GroundingMetadata ref](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1/GroundingMetadata) |
| Vertex AI Search / Agent Search | chunk `pageSpan {pageStart, pageEnd}` (1-based), `documentMetadata {uri,title,mimeType,structData}` | [SearchResult ref](https://docs.cloud.google.com/generative-ai-app-builder/docs/reference/rest/v1/SearchResult) |
| Amazon Bedrock KB | `retrievedReferences[].metadata["x-amz-bedrock-kb-document-page-number"]` (OpenSearch Serverless/Aurora, chunking on) | [AWS docs](https://docs.aws.amazon.com/bedrock/latest/userguide/kb-test-config.html) |
| Pinecone Assistant | citation `position`, `references[].pages[]`, optional `highlight` excerpt (`include_highlights`, API ≥2025-04) | [Pinecone docs](https://docs.pinecone.io/guides/assistant/chat-with-assistant) |
| Glean Chat API | per-fragment citation `referenceRanges` with `snippets` of exact text | [Glean developers](https://developers.glean.com/guides/chat/deep-linked-citations) |
| Box AI API | `include_citations` (default true) → source file + content citations | [Box Dev docs](https://developer.box.com/guides/box-ai/ai-tutorials/ask-questions) |
| Coveo RGA (Atomic/Quantic) | citations with title/uri/clickableuri + chunk; `disableCitationAnchoring` | [Coveo docs](https://docs.coveo.com/en/nb6a0037/) |
| Consensus API/MCP | paper metadata + key takeaways only (no per-claim quotes) | [Consensus docs](https://docs.consensus.app/core-features/citation-grounding) |
| Reducto Extract/Parse | per-field/block citation: page + bbox `{left, top, width, height}` normalized [0,1] | [Reducto docs](https://llms.reducto.ai/json-schema-extraction-with-citations) |
| Azure AI Content Understanding | per-field grounding: page number + bounding box | [Microsoft Learn](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/document/enrichments) |

- Anthropic cost notes: enabling citations slightly increases input tokens (system prompt + chunking); `cited_text` not counted toward output or subsequent input tokens; works with prompt caching, batch — [Claude Platform Docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Gemini File Search pricing: indexing embeddings billed, storage and query embeddings free, retrieved tokens billed as context; limits 100 MB/file, 1 GB–1 TB per project by tier; cannot combine with Google Search grounding or URL context in the same request — [Gemini API docs](https://ai.google.dev/gemini-api/docs/file-search)

### Inferences
- A practical L4 stack as of Oct 2026: (a) parse with a bbox-emitting layer (Reducto / Azure Content Understanding / own OCR+layout), (b) answer with Claude Citations (`cited_text` + page) or Gemini File Search (`page_number`), (c) map `cited_text` back to the parser's word/line boxes on that page to draw the highlight. No vendor offers (c) end-to-end in a chat API.

### Gaps
- Could not verify whether OpenAI's 2026 Responses API has added page numbers to `file_citation` (search returned no evidence of it).
- Microsoft 365 Copilot / Graph: no public API granularity for Deep citations found (feature still in preview/roadmap).
