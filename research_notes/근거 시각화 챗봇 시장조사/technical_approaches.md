# Technical Approaches for Visual Grounding of LLM Answers (as of 2026-10-08)

Scope: how to map an LLM answer (per sentence) back to a precise location in the original source and render that location visually (highlight on the actual page), across PDF/DOCX/PPTX/XLSX/HWP/images/Markdown/web pages, plus web-search evidence and the "no evidence" case. Korean caveats are noted inline; Korean vendor market is covered by a separate researcher.

Reading guide for the report writer: each section follows Takeaway / Cited Findings / Inferences / Gaps. "Inferences" are my synthesis and are NOT sourced claims. Tool versions/dates are given where a source states them.

---

## KQ1. Universal rendering: "convert every format to PDF (or page images), then overlay bbox highlights" as a single pipeline

### Takeaway
Yes, this is the de-facto viable single pipeline: office formats go through LibreOffice (long-running worker: unoserver or Gotenberg with auto-start), HTML/Markdown go through Chromium print-to-PDF, HWP/HWPX go through LibreOffice + the H2Orestart extension with Korean fonts installed, and the resulting PDF is both (a) rasterized to page images (pypdfium2/PyMuPDF) and (b) parsed for bboxes. The main engineering cost is LibreOffice process management (startup overhead, one profile per worker, periodic restarts), and fidelity testing for HWP.

### Cited Findings

**LibreOffice headless / unoserver / Gotenberg**
- Gotenberg is a Docker API that converts `.docx, .xlsx, .pptx, .odt, .ods, .odp` and "100+ other formats" via LibreOffice, and also converts HTML/Markdown via Chromium — [Gotenberg](https://gotenberg.dev/)
- Gotenberg startup overhead was measured at about 2–3 s added per conversion vs a constantly idling LibreOffice listener (issue is ~5 years old; current behavior may differ); multi-file requests still ran linearly in doc count — [gotenberg/gotenberg #373](https://github.com/gotenberg/gotenberg/issues/373)
- Gotenberg tuning flags used in practice: `--libreoffice-auto-start=true` and `--libreoffice-restart-after=10`; sizing guidance 2 vCPU, 2–4 GB RAM for concurrent workloads; image ~1 GB — [portalzine Day 48: Gotenberg](https://portalzine.de/day-48-gotenberg-docker-powered-pdf-generation-from-html-office-docs-and-more-7-days-of-docker/)
- unoconv is deprecated/archived; unoserver is the successor that keeps a long-running LibreOffice worker with a separate client; its Python interpreter must be able to `import uno` — [Transloadit devtip](https://transloadit.com/devtips/unoconv-for-document-conversion-ease/)
- Raw `soffice --headless` startup dominates batch cost: 4 DOCX files took 5.96 s in one invocation vs 13.52 s as four separate invocations — [anarcat batch PDF conversion](https://anarc.at/blog/2019-10-08-libreoffice-pdf-batch/)
- Concurrent LibreOffice instances require a separate user profile per worker (`-env:UserInstallation=...`) — [converterer LibreOffice headless cheatsheet](https://www.converterer.com/blog/libreoffice-headless/)
- LibreOffice CLI syntax: `--convert-to OutputFileExtension[:OutputFilterName[:OutputFilterParams]]`; without `--outdir` the cwd is used — [LibreOffice filter names help](https://help.libreoffice.org/latest/en-US/text/shared/guide/convertfilters.html)
- A competitor's marketing claims a single LibreOffice instance cannot run parallel operations due to its lock mechanism and recommends scaling with multiple Gotenberg instances behind a load balancer (unverified, vendor source) — [pdfbolt Gotenberg comparison](https://pdfbolt.com/compare/gotenberg)

**HWP / HWPX (Hancom) conversion**
- Community recipe (2025–2026): install LibreOffice + Korean fonts (Nanum, Noto CJK) via apt, install the H2Orestart `.oxt` extension system-wide with `sudo unopkg add --shared ./H2Orestart-0.7.7.oxt`, then run `libreoffice --headless --infilter=Hwp2002_File --convert-to pdf:writer_pdf_Export --outdir ./output yourinput.hwp`; extension source is GitHub `ebandal/H2Orestart` — [velog: hwp to pdf in LINUX](https://velog.io/@shyim/hwp-to-pdf-in-LINUX)
- Stock LibreOffice HWP support is described as "basic"; HWP is "not supported by Word, LibreOffice, macOS, or Linux without extra work" — [Science Times HWP guide (2025-10-13)](https://www.sciencetimes.com/articles/60641/20251013/complete-guide-converting-hwp-files-pdf-format.htm)
- A hosted Apify actor converts HWP/HWPX to PDF/Word using LibreOffice + unoserver + H2Orestart (third-party; sends documents off-site) — [Apify HWP converter](https://apify.com/minji_k/hwp-converter/api/cli)
- Korean fonts must be installed on the conversion host or glyphs render incorrectly — [Science Times HWP guide](https://www.sciencetimes.com/articles/60641/20251013/complete-guide-converting-hwp-files-pdf-format.htm)

**HTML / Markdown → PDF via Chromium**
- Chromium headless print-to-PDF slowness is attributed mainly to the page-loading phase rather than printing itself — [chromium headless-dev thread](https://groups.google.com/a/chromium.org/g/headless-dev/c/te0ZcvKx4cc)
- Chromium's virtual time budget can truncate PDFs on fast hardware regardless of budget setting — [Martin Paul Eve, headless PDF printing](https://eve.gd/2021/10/18/headless-pdf-printing-in-chrome-when-the-standard-timeout-isnt-enough/)

**PDF → page images**
- pypdfium2 rasterization: a Docling PR cut raster-only scanned page rendering from ~435 ms/page to ~161 ms/page at scale 3.0 (another tester: 92 → 20 ms/page); gains 2.7×–4.7× depending on hardware — [docling PR #3803](https://github.com/docling-project/docling/pull/3803)
- pypdfium2 describes itself as fast rendering "probably outperforming poppler and ghostscript" and "can almost reach pymupdf" in speed — [pypdfium2 GitHub](https://github.com/pypdfium2-team/pypdfium2)

### Inferences
- Single canonical "PDF + page PNGs + normalized bboxes" representation works because every downstream parser (KQ2) and viewer (pdf.js or an `<img>` + absolutely-positioned `<div>` overlay) only needs page size and a rectangle. Store page images at 2 DPI tiers (thumbnail ~100 DPI, viewer ~150–200 DPI) at ingest time.
- For HWP, the practical quality risk is layout fidelity (tables, equation objects, vertical text, 글맵시). Because the highlight is drawn on the *converted* PDF, fidelity errors are visible but not catastrophic — the highlight still lands on the right text in the rendered page. Run a fidelity test set (government-style HWP forms are the typical hard case).
- XLSX is the odd one out: "page" is not natural. Two options: (a) LibreOffice print-to-PDF with "fit to width" and cite page+bbox (loses cell identity), or (b) treat the sheet as a grid, cite `sheet!A1:C3` and render your own HTML grid with cell highlight (Chunkr exposes `ss_range` / `ss_header_bbox` for spreadsheets, see KQ2). Recommend (b) for XLSX and (a) as fallback.
- Markdown → HTML → Chromium PDF produces deterministic pagination you control; alternatively render Markdown to HTML and use the web-page highlight path (KQ6) with no PDF at all.

### Gaps
- No benchmark comparing Chromium print-to-PDF vs pypdfium2 vs PyMuPDF in ms/page on a controlled corpus was found.
- No authoritative statement of H2Orestart's HWP feature coverage (equations, OLE objects, HWPX) was found; Hancom's own official converters/SDK are left to the Korean-market researcher.
- No current (2026) measured Gotenberg/unoserver throughput numbers found; the 2–3 s overhead figure is old.

---

## KQ2. Layout-aware parsers that return text WITH bounding boxes and page numbers

### Takeaway
Nearly every modern parser returns block-level bboxes; word/line-level boxes come from Azure Document Intelligence, AWS Textract, Google Document AI, PyMuPDF, pdfplumber, Surya, PP-StructureV3's OCR layer, Chunkr (nested OCR boxes), LlamaParse (words/lines/cells), and Upstage (`words` option). Pure VLM-to-Markdown parsers (Nougat, olmOCR text mode, VLM Markdown modes) lose coordinates. Coordinate conventions differ widely (origin, units, normalization) so normalize at ingest to one schema.

### Cited Findings (per tool)

**Docling** (open source, IBM)
- `BoundingBox` fields: `l, t, r, b: float`, `coord_origin: CoordOrigin = TOPLEFT`; `CoordOrigin` enum = `TOPLEFT | BOTTOMLEFT`; methods `to_top_left_origin(page_height)`, `to_bottom_left_origin(page_height)`, `normalized(page_size)` (scales to 1.0×1.0), `as_tuple()`; `Size(width, height)` — [docling-core base.py](https://raw.githubusercontent.com/docling-project/docling-core/main/docling_core/types/doc/base.py)
- DoclingDocument stores `texts`, `tables`, `pictures`, `key_value_items` (all `DocItem`), `body`/`furniture`/`groups` trees; items carry bounding boxes and provenance — [Docling concepts: DoclingDocument](https://docling-project.github.io/docling/concepts/docling_document/)
- Each element keeps page number and bbox via a `prov` array with `page_no`, `bbox`, `charspan` — [docling.ai](https://docling.ai/)
- Tables: prov item with bbox + page_no; for a full-extent table box, compute the enclosing box from cell boxes — [docling discussion #2368](https://github.com/docling-project/docling/discussions/2368)
- Known bugs: text spanning columns/pages may get the page/bbox of the first element only (ReadingOrderModel) — [docling #1699](https://github.com/docling-project/docling/issues/1699); captions/checkbox items can have empty `prov` in docling.rs — [docling.rs #609](https://github.com/docling-project/docling.rs/issues/609)
- docling-serve `/v1/chunk/hybrid` returns `doc_items` as JSON-pointer references without resolved `prov`, so bbox/page are missing from the chunk response (workaround: second conversion call) — [docling-serve #613](https://github.com/docling-project/docling-serve/issues/613)
- Docling scored 50.3% overall / 64.0% on digital PDFs on olmOCR-Bench in Datalab's run (vendor-run) — [marker-pdf PyPI](https://pypi.org/project/marker-pdf/)

**Marker** (Datalab)
- JSON output is a tree: pages → blocks; each block has a `polygon` field "4-corner polygon, in (x1,y1), (x2,y2), (x3,y3), (x4,y4) format, starting at the top left and running clockwise"; page example 612×792 points — [datalab-to/marker README](https://github.com/datalab-to/marker)
- Marker 2.0.0 released 2026-07-20; Datalab's olmOCR-Bench run (single B200): Marker balanced 76.0% overall / 83.5% born-digital; MinerU pipeline backend 72.7%; Docling 50.3%; Marker no-OCR CPU mode 23.7 pages/s vs MinerU pipeline 0.54 pages/s on GPU (vendor-run; MinerU VLM/hybrid modes not in run) — [marker-pdf PyPI](https://pypi.org/project/marker-pdf/)

**MinerU** (OpenDataLab)
- `content_list.json`: each block has `bbox` = `[x0, y0, x1, y1]` mapped to 0–1000, plus `page_idx` and `type`; added in v2.2.0 (2025-09-05); v3.0 adds `content_list_v2.json`; VLM backend normalizes to [0,1] instead; `model.json` holds raw pixel boxes — [MinerU output files doc](https://opendatalab.github.io/MinerU/reference/output_files/), [MinerU changelog](https://opendatalab.github.io/MinerU/reference/changelog/)
- Dr. DocBench (2026) found MinerU achieves best table scores (TEDS, TEDS-S) over general VLMs but specialized parsers do worse on reading order / instruction-following — [Dr. DocBench arXiv 2606.01393](https://arxiv.org/pdf/2606.01393)
- MinerU2.5-Pro paper (2026-04) — [arXiv 2604.04771](https://arxiv.org/pdf/2604.04771)
- A community viewer renders `layout.json` side by side with Markdown with bidirectional click-to-navigate — [mineru-layout-viewer](https://github.com/perfectddt/mineru-layout-viewer)

**Unstructured**
- `element.metadata.coordinates` holds `points`, `system` (e.g. `PixelSpace`), `layout_width`, `layout_height`; points start at top-left — [Unstructured docs: document elements](https://docs.unstructured.io/open-source/concepts/document-elements); example `layout_width: 612, layout_height: 792` — [LangChain Unstructured loader](https://docs.langchain.com/oss/python/integrations/document_loaders/unstructured_file)
- API param `coordinates` defaults to false; `strategy` = fast | hi_res | auto — [Unstructured API params](https://unstructured.readthedocs.io/en/main/apis/api_parameters.html)
- Bug: bboxes differ between default and `hi_res` strategy — [unstructured #3100](https://github.com/Unstructured-IO/unstructured/issues/3100); with chunking overlap, overlapped text lacks coordinates — [unstructured #3810](https://github.com/Unstructured-IO/unstructured/issues/3810)

**PyMuPDF (fitz)**
- `Page.get_text("words")` returns words with position; first four values are the bbox; `search_for()` returns `Rect` list per match, `quads=True` for non-horizontal text; output restricted to the clip rect — [PyMuPDF text recipes](https://pymupdf.readthedocs.io/en/latest/recipes-text.html), [PyMuPDF TextPage](https://pymupdf.readthedocs.io/en/latest/textpage.html)
- Highlight boxes may be slightly wider than glyphs and include neighboring spaces — [Artifex: Highlighting text with PyMuPDF](https://artifex.com/blog/highlighting-text-with-pymupdf)
- PyMuPDF uses top-left y-down page space; pdf.js `convertToViewportPoint` expects bottom-left PDF user space, so y must be flipped (`y_pdf = page_height - y_mu`) — [technetexperts PyMuPDF→PDF.js](https://www.technetexperts.com/fix-pymupdf-pdfjs-coordinates/), [Nutrient: PDF.js coordinate systems](https://www.nutrient.io/blog/pdfjs-coordinate-systems-pdf-to-screen/)
- A baseline enterprise RAG walkthrough uses pymupdf for text+positions and merges cited lines per page into one highlight box — [Towards Data Science](https://towardsdatascience.com/baseline-enterprise-rag-from-pdf-to-highlighted-answer-enterprise-document-intelligence-vol-1-1/)

**PaddleOCR 3.x / PP-StructureV3**
- `parsing_res_list`: list of dicts in reading order with `block_bbox`, `block_label`, `block_content`; saved as `{basename}_res.json` — [PP-StructureV3 tutorial](https://www.paddleocr.ai/latest/en/version3.x/pipeline_usage/PP-StructureV3.html)
- 3.x results must be read from `parsing_res_list` (not the 2.x `{type,bbox,res}` shape); table HTML in `table_res_list[].pred_html`; in-memory objects differ from saved JSON — [writeragent PR #1059](https://github.com/KeithCu/writeragent/pull/1059)

**Surya** (Datalab)
- Surya 2 is a single VLM for layout, OCR, tables; layout output: `bboxes` in reading order with `polygon` (4-corner), `bbox` `[x0,y0,x1,y1]`, `label`; OCR blocks: `polygon` in `[[x0,y0],[x1,y0],[x1,y1],[x0,y1]]` order, `bbox` axis-aligned; "90+ languages"; Korean support stated only generically ("any language") — [datalab-to/surya](https://github.com/datalab-to/surya)

**Upstage Document Parse** (Korean vendor; API facts only)
- `coordinates` boolean returns bbox for each layout element (docs say default true; Dify plugin says false — set explicitly); `words` option includes word-level OCR results; latest model `document-parse-260930`; `words` option first added in `document-parse-260630` (now deprecated) — [Upstage console docs](https://console.upstage.ai/api/document-parse), [Upstage model history](https://console.upstage.ai/docs/models/history)
- Sample response: `elements[]` with `category`, `page`, `coordinates: [{"x":0.06,"y":0.05}, ...]` (normalized points; unconfirmed against official schema) — [OpenClaw skill page](https://skills.lc/openclaw/skills/openclaw-skills-skills-upstage-deployment-upstage-document-parse-skill-md)
- LangChain `UpstageDocumentParseLoader` exposes `coordinates` (default True) — [LangChain API ref](https://python.langchain.com/api_reference/upstage/document_parse/langchain_upstage.document_parse.UpstageDocumentParseLoader.html)

**Azure Document Intelligence (Layout / Read)**
- Spans = character `offset` + `length` into top-level `content`; default unit is grapheme clusters; `stringIndexType` can switch to Unicode code points (Python 3) or UTF-16 code units (Java/JS/.NET) — [Azure analyze response](https://docs.azure.cn/en-us/ai-services/document-intelligence/concept/analyze-document-response?view=doc-intel-4.0.0)
- Polygons attached to pages/words/lines/paragraphs via bounding regions; units: "pixel" for images, "inch" for PDF; paragraphs built from contiguous lines — [Azure layout model](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/prebuilt/layout?view=doc-intel-4.0.0)
- Korean caveat: REST reference says "for non-space delimited languages, such as Chinese, Japanese, and Korean, each character is represented as its own word" — [Azure REST Get Analyze Result](https://learn.microsoft.com/en-us/rest/api/aiservices/document-models/get-analyze-result?view=rest-aiservices-v4.0+%282024-11-30%29)
- Official sample maps words to paragraphs by span containment: `word.span.offset >= span.offset and (word.span.offset + word.span.length) <= (span.offset + span.length)` — [azure-sdk sample_analyze_layout.py](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/documentintelligence/azure-ai-documentintelligence/samples/sample_analyze_layout.py)

**AWS Textract**
- Output is a list of `Block` objects (`BlockType`: PAGE, LINE, WORD, tables/cells, key-value, selection, queries, LAYOUT); each has `Geometry.BoundingBox {Width, Height, Left, Top}` as ratios (page = 1.0) and `Geometry.Polygon [{X,Y}...]`; `Relationships` of type CHILD link LINE→WORD; child blocks don't know their parent; `Confidence` field — [Textract response objects](https://docs.aws.amazon.com/textract/latest/dg/how-it-works-document-layout.html)

**Google Document AI**
- Fetch of the official response-handling doc failed (redirect); field names below are from my training knowledge and must be verified: `document.text` with `layout.textAnchor.textSegments[{startIndex,endIndex}]` (byte offsets into UTF-8 text) and `layout.boundingPoly.normalizedVertices`. (See Gaps.)
- Tensorlake's blog describes Document AI-style output as pages with fragments (type `section_header|table|text`, content, bbox, reading order) — [Tensorlake: Citation-Aware RAG](https://www.tensorlake.ai/blog/rag-citations)

**LlamaParse / Reducto / Chunkr / Mistral OCR**
- LlamaParse: word, line, and table-cell boxes; `layout` option adds bboxes for tables, figures, titles, text, lists; JSON alongside Markdown/text/XLSX — [Reducto comparison (vendor)](https://llms.reducto.ai/document-parser-comparison)
- Reducto: Parse output has per-block and per-chunk boxes with normalized coordinates and page refs; Extract gives per-field citations (page + bbox); citations and chunking reportedly cannot be enabled together in the documented flow — [Reducto vs LlamaParse (vendor)](https://llms.reducto.ai/reducto-vs-llamaparse), [apiscout comparison](https://apiscout.dev/guides/llamaparse-vs-reducto-best-document-ai-api-2026)
- Chunkr: each segment has pixel bbox (`left, top, width, height`), page number, page dimensions; nested `ocr[]` items each with own bbox relative to the segment (add segment left/top to convert); legacy schema used `x1/y1/x2/y2`; spreadsheet inputs add `ss_range`, `ss_header_bbox` — [Chunkr parse outputs](https://docs.chunkr.ai/pages/features/parse/outputs), [Chunkr legacy bbox](https://legacy-docs.chunkr.ai/bounding-box), [Chunkr Excel output](https://docs.chunkr.ai/pages/v1/excel-parser/understanding-the-output)
- Mistral OCR: per-page Markdown + `images[]` each with base64 and `bbox {x,y,width,height}` plus page dimensions/DPI; "bbox annotation" labels each extracted figure; "document annotation" sends OCR Markdown + first eight image bboxes to a vision model, 8-page limit — [Mistral annotations docs](https://docs.mistral.ai/studio/document-processing/annotations), [Mistral OCR cookbook](https://docs.mistral.ai/resources/cookbooks/mistral-ocr-data_extraction). Third-party guides describe OCR 4/4.1 with `include_bounding_boxes=True`, a `blocks` list (title/paragraph/table/signature) normalized to page size, and confidence per block — conflicting on model names and pricing; unverified — [aimadetools OCR 4 tutorial](https://www.aimadetools.com/blog/how-to-use-mistral-ocr-4-api-python/), [explainx OCR 4.1](https://explainx.ai/blog/mistral-ocr-4-bounding-boxes-document-ai-api-2026)

**olmOCR / Nougat / other VLM OCR**
- Hugging Face's OCR guide lists OlmOCR-2 with "Grounding" among features but does not state whether the model emits coordinates; some models (DeepSeek-OCR style) interleave detection boxes in a grounding prompt mode; LightOnOCR-2-1B-bbox is a separate bbox variant — [HF blog: OCR open models](https://huggingface.co/blog/ocr-open-models)
- Nougat paper describes Markdown/LaTeX output; the search found no statement of coordinate output (my knowledge: Nougat is text-only) — [Nougat arXiv 2308.13418](https://arxiv.org/pdf/2308.13418)
- A 2026 paper proposes an LMM for "precisely grounding elements in documents" — [arXiv 2606.24118](https://arxiv.org/pdf/2606.24118)

**Benchmarks**
- No found benchmark measures bbox accuracy directly; a 2026 comparison warns flattening to Markdown "throws away page numbers, bounding boxes, block information" — [builderai MinerU vs Docling vs Marker](https://builderai.tools/blog/pdf-parsing-for-rag-mineru-docling-marker-compared)
- 2026 benchmarks that exist: PureDocBench (source-traceable) — [arXiv 2605.07492](https://arxiv.org/pdf/2605.07492); ParseBench — [arXiv 2604.08538](https://arxiv.org/pdf/2604.08538); Dr. DocBench — [arXiv 2606.01393](https://arxiv.org/pdf/2606.01393)
- EdgeParse (deterministic, no GPU) claims bbox for every element and reports Docling at 0.758 overall / 2.332 s/doc on its 200-doc suite (self-run) — [xz-tak/edgeparse](https://github.com/xz-tak/edgeparse)

### Inferences
- Granularity map (for the design doc): word/line-level = Azure DI, Textract, Document AI, PyMuPDF/pdfplumber (born-digital), Surya, PP-OCR layer, Chunkr `ocr[]`, LlamaParse, Upstage `words`; block/element-level = Docling, Marker, MinerU, Unstructured, Reducto blocks, Mistral blocks; none = Nougat, VLM-Markdown modes.
- Recommended default for a personal platform: Docling (or Marker) for structure + PyMuPDF `get_text("words")`/`search_for` for word-level refinement on born-digital PDFs; a cloud OCR (Azure DI or Upstage) for scans and Korean-heavy scans. Always keep the parser's own coordinate convention in metadata and convert to one canonical form (top-left origin, normalized 0–1, page 1-indexed).
- Korean: word segmentation differs (Azure treats each CJK char as a word), so "word bbox" for Korean often means per-syllable boxes; merge adjacent boxes on the same line when drawing highlights.

### Gaps
- Google Document AI official doc fetch failed; confirm `textAnchor` byte-vs-char semantics in official docs.
- No independent Korean-language accuracy comparison of these parsers found (deferred to Korean-market researcher).
- Upstage response schema not confirmed from an official schema page.
- Mistral OCR 4.x block-bbox fields unverified against official API reference.

---

## KQ3. Chunk → (file, page, bbox list) traceability through chunking, embedding, reranking; chunks spanning pages; tables and figures as evidence

### Takeaway
Store provenance as a list of per-page regions on every chunk (never one page + one box), carry it as opaque metadata through embedding/reranking, and give the LLM short stable IDs (Tensorlake's `<c>page.order</c>` pattern) that resolve server-side to regions. Tables carry an atomic table box plus optional cell boxes; figures carry the picture region.

### Cited Findings
- Tensorlake pattern: chunk text contains inline anchors `<c>[page_num].[reading_order]</c>` (e.g. `<c>2.1</c>`); chunk metadata has a `citations` object mapping each ID → `{page, bbox:{x1,y1,x2,y2}}`; section metadata holds `title, start_page, end_page, header_bbox`; the prompt asks the model to ignore anchors in prose and return `{answer, citations:[ids]}`; retrieval/hybrid/reranking work unchanged because provenance is metadata; bboxes add ~10–15% storage; structured extraction with `provide_citations=True` returns `<field>_citation` lists with `page_number, x1, y1, x2, y2` — [Tensorlake: Citation-Aware RAG](https://www.tensorlake.ai/blog/rag-citations)
- Legal-document implementation rule set: long items split into pieces inherit the whole item bbox on every piece; short merged items get the min-x/min-y/max-x/max-y union of contributors on the same page; tables carry their atomic bbox; all bboxes converted to TOPLEFT origin (BOTTOMLEFT flipped against page height) — [legalvault-ai PR #31](https://github.com/BhaveshKhaple/legalvault-ai/pull/31)
- RagenAI: Docling per-element boxes carried through ingest; boxes normalized at the server boundary (not in the viewer) because Docling reports bottom-left points and pdf.js renders top-left at user zoom — [RagenAI PR #1085](https://github.com/webamigos/RagenAI/pull/1085)
- Docling+Neo4j "evidence pins" design: allow multiple bboxes per chunk for tables, or child Cell nodes when per-cell highlights are needed — [Vipul Shah, Layout-Aware RAG with Evidence Pins](https://vipulmshah.medium.com/layout-aware-rag-with-evidence-pins-building-clickable-citations-for-pdfs-using-docling-neo4j-5305769759f0)
- Docling table extent: compute enclosing box from cell boxes; table prov has bbox+page — [docling discussion #2368](https://github.com/docling-project/docling/discussions/2368)
- Docling reading-order bug for text spanning columns/pages (prov takes first element's page/box) — [docling #1699](https://github.com/docling-project/docling/issues/1699)
- Open WebUI stores only the page integer for citations; a feature request asks for stored bboxes (preferred) with text-search+highlight fallback — [open-webui discussion #20829](https://github.com/open-webui/open-webui/discussions/20829)
- Page-indexing mismatch (0-indexed loops vs 1-indexed PDFs) is a common bug; bboxes must come from the same extraction pass as the text — [theneuralbase citation extraction](https://theneuralbase.com/document-ai/learn/advanced/citation-extraction-for-rag/)
- OpenDataLoader tutorial: each chunk stores bbox `[left, bottom, right, top]` in PDF points and 1-indexed page — [bswen OpenDataLoader citations](https://docs.bswen.com/blog/2026-06-04-pdf-source-citations-rag/)
- React viewer pattern: group citations by page, one overlay layer per page, percentage positioning on a wrapper sized to the rendered page — [DEV: React PDF bbox highlighting](https://dev.to/fangtanbamrung/react-pdf-bbox-highlighting-show-rag-citations-on-the-page-il9)
- Unstructured chunk overlap text lacks coordinates — [unstructured #3810](https://github.com/Unstructured-IO/unstructured/issues/3810)
- "Evidence Units" paper proposes parser-independent, ontology-grounded evidence units for retrieval (2026) — [arXiv 2604.00500](https://arxiv.org/pdf/2604.00500)

### Inferences
- Proposed canonical schema (my synthesis):
  ```
  Chunk {
    chunk_id, doc_id, text, headings[], element_type (text|table|figure|list|cell),
    regions: [ { page: int(1-idx), bbox: {x0,y0,x1,y1} normalized 0..1 top-left, kind: "block"|"line"|"cell" } ],
    char_map: [ {chunk_start, chunk_end, region_idx} ]   // sub-chunk char ranges → region, enables sentence-level highlight
    parent_item_id, split_index, source_parser, parser_coord_meta
  }
  ```
  The `char_map` is what lets a *sentence-level* citation (KQ4: `cited_text` or char offsets) be narrowed to the right line/paragraph boxes inside a multi-paragraph chunk.
- Embedding and reranking only see `text` (and `headings`), so provenance survives trivially; the risk is in chunkers that re-split text (e.g. LangChain splitters) — re-derive `char_map` after any re-split by fuzzy aligning (KQ4) back to the parser items.
- Tables: embed row/cell-serialized text with `element_type=cell` and `regions=[cell bbox]` for cell-level evidence; keep a parent table chunk with the atomic box for whole-table evidence. Figures: embed caption + VLM description, region = picture bbox.

### Gaps
- No published study quantifying how often chunks span pages in typical corpora or the UX impact of multi-page highlights.
- No source on provenance-preserving chunking for XLSX beyond Chunkr's `ss_*` fields.

---

## KQ4. Sentence-level attribution: getting the LLM to cite at sentence granularity, API support, post-hoc attribution, and recovering exact spans

### Takeaway
Three API-native options exist with different granularities: Anthropic Citations (sentence-level `cited_text` + char/page/block indices; exact-span guaranteed), Gemini `groundingSupports` (response-side `segment.startIndex/endIndex` → chunk indices; no source-side offsets), OpenAI Responses `file_citation` (file-level + a single `index` into the answer; no span). For all three, map the cited passage back to parser coordinates via fuzzy alignment (rapidfuzz `partial_ratio_alignment`), then verify with NLI. Research shows post-hoc citation gives better coverage; generation-time citation gives better precision.

### Cited Findings

**Anthropic Citations API**
- Location types and fields: `char_location` {`cited_text`, `document_index`, `document_title`, `start_char_index` (0-indexed), `end_char_index` (exclusive)}; `page_location` {`start_page_number` (1-indexed), `end_page_number` (exclusive)}; `content_block_location` {`start_block_index` (0-indexed), `end_block_index` (exclusive)} — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Chunking: plain text and PDF are chunked into sentences; custom content documents (list of `{"type":"text","text":...}` blocks) are used as-is with no further chunking — "if you want Claude to be able to cite specific sentences from your RAG chunks, put each RAG chunk into a plain text document; if you do not want further chunking, put chunks into custom content documents" — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- PDF: text extracted and chunked into sentences; image citations not supported, so scanned PDFs without extractable text are not citable — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- `cited_text` does not count toward output tokens, and not toward input tokens when passed back; citations guaranteed to be valid pointers to provided documents; enabling citations slightly increases input tokens; citations cannot be combined with structured outputs (`output_config.format` → 400) — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Streaming: citations arrive as `citations_delta` inside `content_block_delta` events, one citation per delta appended to the current text block's `citations` list — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- All active models support citations; GA on Claude API, AWS, Bedrock, Google Cloud, Microsoft Foundry; example model `claude-opus-5-5`; `search_result` blocks can also be cited — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Anthropic's internal evals reported Citations outperform custom implementations on recall accuracy (launch claim, Jan 2025) — [Rohan Paul on X summarizing launch](https://x.com/rohanpaul_ai/status/1882527222994444630)
- Community: match `cited_text` to parser bboxes and open PDF.js with the span highlighted — [raac-rag #19](https://github.com/Guernik/raac-rag/issues/19)

**Gemini / Vertex AI grounding**
- `groundingMetadata`: `webSearchQueries[]`, `searchEntryPoint.renderedContent` (required Search Suggestions widget), `groundingChunks[].web.{uri,title}`, `groundingSupports[].segment.{startIndex,endIndex,text}` + `groundingChunkIndices[]`; sample `add_citations` sorts supports by `end_index` descending and inserts `[i+1](uri)` at `end_index`; unit of indexes not stated; Gemini 3 bills per executed search query; page updated 2026-09-02; example model `gemini-3.8-flash` — [Gemini API Google Search grounding](https://ai.google.dev/gemini-api/docs/generate-content/google-search)
- Vertex Google Search grounding example includes `confidenceScores` per chunk (e.g. 0.866; 0.628/0.649) alongside `segment` offsets (85–214) — [Vertex grounding with Google Search](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/grounding/grounding-with-google-search)
- Parallel Web Search grounding example shows a `segment` with only `endIndex: 77` (no `startIndex`) — [Vertex grounding with Parallel](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/grounding/grounding-with-parallel)
- Vertex AI Search datastore grounding follows the same `groundingSupports` pattern; ADK describes it as connecting sentences in the answer to `groundingChunks` — [ADK Vertex AI Search grounding](https://raw.githubusercontent.com/google/adk-docs/main/docs/grounding/vertex_ai_search_grounding.md)
- Gemini 3 `google_search` tool: a GitHub issue reports a case where `groundingMetadata` was missing for a search model — [opencode-antigravity-auth #384](https://github.com/NoeFabris/opencode-antigravity-auth/issues/384)

**OpenAI Responses file_search**
- `file_citation` annotation fields: `type, file_id, filename, index`; no start/end offsets and no quoted text; include retrieved chunks with `include: ["file_search_call.results"]` (results have `filename`, `text`); `max_num_results`, `filters`; supported types include .pdf .docx .pptx .md .txt .html .json (no .xlsx listed); rate limits 500/1000 RPM by tier — [OpenAI file search guide](https://developers.openai.com/api/docs/guides/tools-file-search)
- Developers report Responses citations map only to the file, not the retrieved chunk; no apparent mapping between `file_search_call.results` and annotations; Assistants API (legacy) had `start_index/end_index` + `quote` — [OpenAI community: annotations reference a file](https://community.openai.com/t/file-search-annotations-reference-a-file-rather-than-a-chunk/1367438/3), [OpenAI community: correlate citations](https://community.openai.com/t/how-can-i-correlate-inline-citations-with-file-search-results/1335592); Vercel AI SDK issue on missing annotations — [vercel/ai #7749](https://github.com/vercel/ai/issues/7749)

**Cohere**
- Two citation modes for streamed RAG: "fast" (citations inline as generated) vs "accurate" (full answer first, then citations mapped; adds latency, more precise indices) — [Cohere RAG citations](https://docs.cohere.com/docs/rag-citations)

**Research: generation-time vs post-hoc; benchmarks**
- Saxena et al. (NeurIPS 2025 LLM Eval workshop; arXiv 2025-09-25, v2 2025-12-18): consistent coverage-vs-correctness trade-off; retrieval is the main driver of attribution quality; P-Cite (post-hoc) gives high coverage with competitive correctness and moderate latency; G-Cite (generation-time) prioritizes precision but sacrifices coverage and speed; recommend P-Cite-first for high-stakes, G-Cite for strict claim verification — [arXiv 2509.21557](https://arxiv.org/abs/2509.21557)
- ALCE (Gao et al., EMNLP 2023): datasets ASQA, QAMPARI, ELI5; corpora segmented into 100-word passages; evaluates fluency (MAUVE), correctness, citation quality; "even top models achieve around 50% citation support" — [arXiv 2305.14627](https://arxiv.org/abs/2305.14627), [princeton-nlp/ALCE](https://github.com/princeton-nlp/ALCE)
- LongCite (ACL Findings 2025): fine-grained sentence-level citations; describes a post-RAG setup where the model answers first, then retrieves chunks/sentences using the answer and adds citations — [LongCite ACL 2025](https://aclanthology.org/2025.findings-acl.264.pdf)
- Post-hoc NLI checks measure correctness not faithfulness (no access to the generation process) — [On Synthesizing Data for Context Attribution, arXiv 2504.05317](https://arxiv.org/pdf/2504.05317)
- "Why your RAG citations are lying: post-hoc rationalization" — [TianPan.co 2026-04-23](https://tianpan.co/blog/2026/04/23/rag-citations-post-hoc-rationalization)
- CiteGuard (arXiv 2510.17853) and CiteGuard-RAG (arXiv 2609.15830): validation-centered citation attribution — [CiteGuard](https://arxiv.org/pdf/2510.17853), [CiteGuard-RAG](https://arxiv.org/html/2609.15830)
- Mechanistic interpretation of how LLMs cite in RAG (2026) — [arXiv 2606.28358](https://arxiv.org/pdf/2606.28358)
- "Cited but Not Verified": parsing/evaluating source attribution in deep research agents (2026) — [arXiv 2605.06635](https://arxiv.org/html/2605.06635v1)
- Sentence-level verification during generation with DeBERTa NLI, KV-cache rollback, measured on SGLang (legal RAG) — [azterizm/legal-rag-verifier](https://github.com/azterizm/legal-rag-verifier)
- HALLMARK: agentic lookups inflate false positives in citation verifiers — [arXiv 2607.18360](https://arxiv.org/pdf/2607.18360)

**Fuzzy span recovery**
- rapidfuzz `partial_ratio` finds the best matching substring; `partial_ratio_alignment` additionally returns start/end indices of the match in both strings (gives character offsets per quote); rapidfuzz fixed multiple partial_ratio bugs vs fuzzywuzzy so thresholds may shift — [rapidfuzz/RapidFuzz](https://github.com/rapidfuzz/RapidFuzz), [rapidfuzz 3.14.0 docs mirror](https://tessl.io/registry/tessl/pypi-rapidfuzz/3.14.0/files/docs/fuzzy-matching.md)

### Inferences
- Recommended pipeline for sentence-level attribution (my synthesis):
  1. Retrieve top-k chunks with provenance (KQ3). 2. Generate with Anthropic Citations using *custom content documents* where each content block = one parser item (paragraph/line/cell), so `start_block_index` resolves directly to a region with no text matching; or plain-text documents for sentence granularity inside a chunk. 3. For other models, prompt with numbered IDs (Tensorlake `<c>p.o</c>` pattern) and request `{answer_sentences:[{text, ids[]}]}`. 4. Post-hoc: split answer into sentences; for every sentence lacking a citation, run embedding retrieval over the already-retrieved item texts + NLI (DeBERTa-class) to attach or mark "unsupported" (gives the P-Cite coverage benefit). 5. Resolve `cited_text` (or the quote) to exact character offsets within the item via `partial_ratio_alignment` (threshold ~85–90, min length ~20 chars), then to word/line boxes via the item's `char_map`.
- Anthropic's `page_location` alone (page numbers) is too coarse for highlighting; always use `cited_text` + fuzzy alignment or custom content blocks.
- Gemini offsets are on the *response*, so the source-side span must be recovered by matching `segment.text` against the chunk text (`groundingChunks[i]` retrievedContext text) — same fuzzy step.
- OpenAI Responses gives neither; treat it as file-level and do fully post-hoc attribution against `file_search_call.results` texts.
- Korean caveat: NLI models for Korean are weaker and sentence splitting must handle Korean punctuation/연결어미; use a multilingual NLI (e.g. mDeBERTa-class) and a Korean-aware sentence splitter (kss) — these specific tools are my recommendation, not sourced.

### Gaps
- Exact ALCE citation recall/precision computation (TRUE NLI model) not confirmed from the search; see paper Section 3.
- Vertex AI Search grounded-generation details (datastore `retrievedContext` fields, page numbers, limits) could not be extracted (page truncated).
- No quantitative numbers from Saxena et al. abstract page; full paper needed for precision/recall tables.
- No source found evaluating Anthropic Citations accuracy on Korean documents.

---

## KQ5. Multi-source evidence UX patterns and HCI evidence on citation UX, verification cost, and trust

### Takeaway
Production systems converge on inline numbered markers + hover preview + a side source panel (Perplexity added a "Source Context Panel" in July 2026 showing the supporting passage beside the answer). HCI evidence (AAAI 2025, CHI 2026 poster) says citations raise trust even when random, trust drops when users actually check, and users rarely interact with references — so the UX must make verification cheap (highlighted passage on the real page) rather than merely present citations.

### Cited Findings
- Ding et al., "Citations and Trust in LLM Generated Responses" (AAAI 2025): live QA experiment with 0/1/5 citations, relevant or random; trust increased significantly when citations were present, even when random; trust decreased significantly when participants checked the citations — [arXiv 2501.01303](https://arxiv.org/abs/2501.01303), [AAAI proceedings](https://ojs.aaai.org/index.php/AAAI/article/view/34550)
- Ouyang & Narechania (CHI 2026 EA/poster; arXiv 2026-03-06): 1,517 references from 30 QA pairs across 9 LLM conversational systems; ChatGPT most references (avg 9.5/response, quality 15.48/20 CRAAP); Hunyuan-TurboS fewest (4.0, 11.65/20); preliminary user study found people rarely interact with references; calls for better interface designs — [arXiv 2604.15326](https://arxiv.org/abs/2604.15326)
- U-Lens (2026): supporting user uncertainty management in long-form LLM responses — [arXiv 2607.10604](https://arxiv.org/pdf/2607.10604)
- Do & Geyer "Hide or Highlight: factuality expression and user trust" and Do et al. "Highlight All the Phrases: visual factuality indicators" (both AIES 2025) — [KAIST HCAI workshop paper listing](https://xai.kaist.ac.kr/static/files/2025_hcai_workshop/paper_29.pdf) (workshop pilot, n=6: verbalized uncertainty increased citation checking)
- Perplexity: every answer links to numbered citations; hover to preview, click to read full source; users can highlight answer text to check referenced sources — [Perplexity getting started](https://www.perplexity.ai/hub/getting-started)
- Perplexity July 2026 release: "Source Context Panel" lets users select a citation and inspect its supporting passage while keeping the surrounding answer visible — [Releasebot Perplexity notes](https://releasebot.io/updates/perplexity-ai)
- Perplexity averages ~21.9 citations/response vs ChatGPT ~10.4 (another study 7.92; another 6.88) — vendor SEO studies — [quickseo](https://quickseo.ai/blog/chatgpt-vs-perplexity-for-ai-visibility-in-2026-citations-traffic-and-conversion-compared), [authoritytech](https://authoritytech.io/curated/ai-citation-11-percent-platform-overlap-per-engine-audit-2026), [frase](https://www.frase.io/blog/which-ai-engines-cite-which-sources)
- Perplexity: "inline numbered, always visible; source inspection primary interaction"; Google AI Mode: "linked cards, collapsed by default" — [usegrowthos comparison](https://www.usegrowthos.com/blog/google-ai-overviews-vs-chatgpt-vs-perplexity)
- ChatGPT attribution is inconsistent (inline links, end list, or none); searched answers show inline citation chips and a Sources panel — [Typescape ChatGPT citations](https://typescape.ai/blog/chatgpt-citations-explained/), [qwairy](https://www.qwairy.co/blog/how-to-know-chatgpt-sources)
- Gemini "Double-check response" (G icon under answer): green highlight = a web source corroborates; orange = not found online or conflicts; unhighlighted = opinion/reasoning; it verifies an already-generated answer post hoc, unlike grounding at generation time — [st-hakky Gemini double-check](https://book.st-hakky.com/en/data-science/gemini-pro-double-check-feature), [apprendre-ia double-check](https://apprendre-ia.qdna.fr/en/gemini/verify-with-google-search)
- Medium walkthrough of highlighting source texts in PDFs to build trust — [sodevelopment Medium](https://sodevelopment.medium.com/building-trust-in-llm-answers-highlighting-source-texts-in-pdfs-0a9d26417353)
- Chunkr blog on building PDF experiences with bbox-level citations — [Chunkr blog](https://chunkr.ai/blog/building-better-pdf-experiences-with-chunkr)

### Inferences
- Design pattern set for 3+ sources (my synthesis grounded in the above): (1) per-sentence inline markers `[1][2]` that on hover show a *cropped page-image snippet* with the highlight (cheap verification without navigation); (2) click opens a split view: answer left, source viewer right, scrolled to the page with all regions for that sentence highlighted; (3) an evidence rail/carousel listing each distinct (doc, page) with thumbnail; (4) a per-sentence support badge: supported / partially / unsupported (from NLI), mirroring Gemini's green/orange semantic; (5) "show all evidence for this answer" toggles overlays on every cited page.
- Given Ding et al., don't pad with weak citations; fewer, verifiable, visually-confirmable citations are the trust-correct design. Given Ouyang & Narechania, default to showing the evidence preview inline (hover/expand) rather than requiring click-through.

### Gaps
- No CHI/UIST/CSCW full paper specifically measuring verification time with highlighted-page evidence vs text snippets was found.
- No official Google documentation on AI Mode's citation UI was found; third-party only.
- No screenshots/teardowns of Perplexity "Source Context Panel" beyond the release note.

---

## KQ6. Web-search evidence: rendering a fetched page snapshot with the cited passage highlighted

### Takeaway
Two complementary mechanisms: (a) link-out with a Text Fragment URL (`#:~:text=prefix-,start,end,-suffix`), supported in Chrome 80+, Edge, Safari 16.1+, Firefox 131+; (b) own snapshot: fetch/render with Playwright, locate the passage via DOM text-node walk → `Range`, highlight via the CSS Custom Highlight API (Baseline 2026, no DOM mutation), scroll into view, screenshot or persist the DOM (SingleFile-style). Content APIs (Firecrawl, Jina, Exa) return Markdown/highlights but no offsets into the live DOM, so span recovery is again fuzzy matching.

### Cited Findings
- Text Fragment syntax `#:~:text=[prefix-,]textStart[,textEnd][,-suffix]`; the `:~:` delimiter; percent-encode; Chrome 80+ on all platforms; also Chrome, Edge, Safari, Firefox (Firefox 131+, Safari 16.1+); browser strips the directive from `document.URL` so the target page cannot read it — [testmuai Scroll-to-Text support](https://www.testmuai.com/learning-hub/scroll-to-text-fragment-browser-support/), [Caleb Hearth text fragments](https://calebhearth.com/text-fragments), [GoogleChromeLabs link-to-text-fragment](https://github.com/GoogleChromeLabs/link-to-text-fragment)
- CSS Custom Highlight API: style arbitrary `Range`s via `Highlight` + `CSS.highlights.set()` + `::highlight()` without modifying DOM; ranges can cross element boundaries (unlike `surroundContents`); MDN lists it as Baseline 2026 (newly available since March 2026); caniuse: Chrome/Edge 105+, Safari 17.2+, Firefox 140+ (another source says Firefox 149); feature-detect with `CSS.highlights` — [MDN Custom Highlight API](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Custom_highlight_API), [caniuse Highlight API](https://caniuse.com/mdn-api_highlight), [ICS Media](https://ics.media/en/entry/260427/), [GoogleChrome modern-web-guidance: highlight text ranges](https://github.com/GoogleChrome/modern-web-guidance/blob/main/skills/modern-web-guidance/guides/ui-behaviors/highlight-text-ranges.md)
- Playwright: `locator.highlight()` is a debug aid only; element screenshot `page.locator("main").screenshot()`; clipped screenshot via `clip: {x,y,width,height}` — [Playwright Locator API](https://playwright.dev/docs/api/class-locator), [scrapeops Playwright screenshots](https://scrapeops.io/playwright-web-scraping-playbook/nodejs-playwright-take-screenshots/); style-based highlighting pattern — [Gleb Bahmutov highlight element](https://glebbahmutov.com/blog/highlight-element/)
- Exa Contents returns "condensed highlights" by default with full text optional; pricing from $1/1,000 pages — [Firecrawl vs Exa (vendor)](https://www.firecrawl.dev/alternatives/firecrawl-vs-exa); Firecrawl returns clean Markdown/JSON, search can attach full page; Jina Reader `r.jina.ai/` prefix returns Markdown/JSON, `s.jina.ai/?q=` searches and fetches results; rate limits 20 rpm anonymous / 500 rpm with key — [apiscout Firecrawl vs Jina vs Apify](https://apiscout.dev/guides/firecrawl-vs-jina-vs-apify-scraping-api-2026), [Apify Jina vs Firecrawl](https://blog.apify.com/jina-ai-vs-firecrawl/)
- Anthropic Citations supports `search_result` content blocks and `web_search_result_location` citations (per the doc's document-type list) — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- Gemini grounding provides `groundingChunks[].web.uri/title` and response-side segment offsets, and *requires* rendering `searchEntryPoint.renderedContent` — [Gemini Google Search grounding](https://ai.google.dev/gemini-api/docs/generate-content/google-search)

### Inferences
- Web evidence pipeline (my synthesis): at search time, fetch the page in Playwright → save (1) a full-page PNG, (2) the extracted text with a map from text offsets to DOM `Range`s (walk `TreeWalker(SHOW_TEXT)` and record cumulative offsets), (3) page HTML snapshot. After the LLM cites a passage, fuzzy-align the quote to the extracted text, build the `Range`, apply `CSS.highlights`, `scrollIntoView`, and (a) screenshot a clipped region around `range.getBoundingClientRect()` for the hover card, (b) store the snapshot HTML for a full "view source page" mode, and (c) emit a Text Fragment URL for "open original". Text fragments break if the page text changes; the snapshot is the durable record.
- Google AI Overviews/AI Mode and Perplexity do not show highlighted page snapshots; they show cards/snippets and link out — so a highlighted-snapshot UX is a differentiator rather than table stakes.
- Korean caveat: text-fragment matching is exact-string; Korean pages with NBSP/zero-width characters or spacing normalization can defeat it — normalize whitespace before building fragments and prefer prefix/suffix context.

### Gaps
- No source found describing how Perplexity/Google render passage highlights on source pages (they appear not to).
- No API among Firecrawl/Jina/Exa confirmed to return character offsets into the page; unverified.
- SingleFile/Readability snapshot tooling was not researched in this pass.

---

## KQ7. "No evidence" answers: labeling model-knowledge answers vs grounded, double-check features, selective abstention

### Takeaway
Products converge on: show a Sources panel only when retrieval/search ran (ChatGPT), offer post-hoc verification that colors each claim (Gemini Double-check: green corroborated / orange unsupported), and tell users to verify. Research shows models abstain poorly when context lacks the answer; practical designs use context-only abstention prompting, counterfactual/LLM-judge checks, or prompt-only confidence-aware abstention (I-CALM), and must separately handle "answer absent from context but known parametrically."

### Cited Findings
- ChatGPT: doesn't search unless a tool is enabled (enabled by default); without search, answers come from training data; users can check the search icon state or whether web sources are cited — [OpenAI Help: Does ChatGPT tell the truth?](https://help.openai.com/en/articles/8313428-does-chatgpt-tell-the-truth); non-search "citations" may be pattern-matched and nonexistent — [Typescape](https://typescape.ai/blog/chatgpt-citations-explained/)
- Gemini Double-check: post-hoc cross-reference against Google Search; green = corroborated (not a guarantee the source is accurate), orange = not found/conflicting, unhighlighted = opinion/reasoning — [st-hakky](https://book.st-hakky.com/en/data-science/gemini-pro-double-check-feature), [apprendre-ia](https://apprendre-ia.qdna.fr/en/gemini/verify-with-google-search)
- Know-Or-Not library: evaluates out-of-knowledge-base robustness; argues contradictory perturbations are unrealistic and that deployed contexts are "informationally adjacent, though still insufficient" — [arXiv 2505.13545](https://arxiv.org/pdf/2505.13545)
- Models should "refrain from answering when, based on the context alone, the answer is simply absent"; answering from parametric knowledge risks wrong verification when parametric knowledge is incorrect — [arXiv 2404.06283](https://arxiv.org/pdf/2404.06283)
- Chen et al. 2024a used counterfactual prompting to help RAG models decide to abstain, relying on LLM-as-judge — cited in [Know-Or-Not](https://arxiv.org/pdf/2505.13545)
- I-CALM (2026): prompt-only, single-forward-pass confidence-aware abstention for black-box models; explicit IDK permission leaves the threshold unspecified — [arXiv 2604.03904](https://arxiv.org/html/2604.03904)
- AbstentionBench: reasoning LLMs fail on unanswerable questions; "knowing when not to answer is equally critical" — [HF paper 2506.09038](https://huggingface.co/papers/2506.09038)
- Selective abstention learning (ACL 2025): probe which questions the model gets right, train to abstain on wrong ones — [papernotes ACL 2025](https://en.papernotes.org/ACL2025/hallucination/alleviating_hallucinations_from_knowledge_misalignment_in_large_language_models_/)
- Gemini API grounding returns `groundingSupports` only for supported segments; a response with no supports has no citations to show — [Gemini Google Search grounding](https://ai.google.dev/gemini-api/docs/generate-content/google-search)
- Ding et al.: trust rises with any citations, so unsourced answers must not be dressed with decorative citations — [arXiv 2501.01303](https://arxiv.org/abs/2501.01303)

### Inferences
- Labeling scheme (my synthesis): three answer states per sentence, derived from the attribution step — `grounded` (document/web evidence, highlightable), `verified-by-search` (post-hoc search found corroboration; Gemini-green analogue), `model-knowledge` (no evidence; render with a distinct neutral style and a one-line banner "이 답변은 문서/검색 근거 없이 모델 지식으로 생성되었습니다", plus a "검색으로 확인" button that runs the KQ6 pipeline on that sentence). Never show a source panel or citation markers for model-knowledge sentences.
- Retrieval gating: if top reranked score is below a calibrated threshold AND the NLI step attaches no support to any sentence, route to the "model-knowledge" state instead of showing weakly-related documents.
- For personal knowledge bases, an explicit "not in your documents" state ("업로드한 문서에서 근거를 찾지 못했습니다") is more useful than a blanket IDK; then optionally continue with web search or model knowledge, clearly labeled.

### Gaps
- No standard label/benchmark convention for "no evidence found" in RAG was found.
- No official Google help page for Double-check was retrieved; details are from third-party guides.
- No user study comparing label designs ("from model knowledge" banners) on trust/behavior was found.

---

## KQ8. Speed: typical latencies and making evidence rendering feel instant

### Takeaway
Generation dominates end-to-end (≈10 s of a measured 10 s pipeline vs ~120 ms retrieval, ~31 ms rerank); evidence rendering should therefore be pre-computed at ingest (page images + bboxes) and delivered as a `sources` event before tokens stream, with citations streamed (Anthropic `citations_delta`) or sent in a final event; lazy-load the full viewer only on click.

### Cited Findings
- 480 timed queries across four LLM families: LLM generation ~9,800 ms; vector+BM25 retrieval ~120 ms; reranking ~31 ms (mean 31.3 ms); embedding+RRF ~49 ms — [clouatre-labs rag-reranking-benchmarks](https://github.com/clouatre-labs/rag-reranking-benchmarks)
- RAGMark: reranking grows ~linearly with top-k (7 ms at k=1 → 24 ms at k=10); trimming context cut TTFT for an 8B model from 322 ms to 165 ms at k=10 — [RAGMark arXiv 2609.05760](https://arxiv.org/pdf/2609.05760)
- Federated search study: reranking took 6.27 s on MMLU (~63% of latency) with a heavy reranker — [arXiv 2502.19280](https://arxiv.org/pdf/2502.19280)
- Practitioner budgets: query embedding 20–80 ms; cross-encoder rerank 50–400 ms; first token 500–2000+ ms — [DEV: retrieval latency budgets](https://dev.to/neeraj_singhi_golang/retrieval-latency-budgets-in-rag-pipelines-vector-search-reranking-and-the-timeout-cascade-55jk)
- Local CPU query encoders 17–31 ms vs hosted embedding API median ~232 ms (network/cold start) — [arXiv 2605.23618](https://arxiv.org/pdf/2605.23618)
- Cohere "fast" vs "accurate" citation modes (accurate = answer first then map citations; adds latency) — [Cohere RAG citations](https://docs.cohere.com/docs/rag-citations)
- Production stack pattern: typed SSE events `sources` (before generation) → `token` deltas → `done` with final latency — [DEV: production RAG streaming and citations](https://dev.to/madhudadi/understanding-the-production-rag-stack-streaming-and-citations-in-building-oc6); proposal to stream text deltas then send citations in a final event, logging TTFT — [production-rag-service #5](https://github.com/tsriharsha402/production-rag-service/issues/5)
- If citations come from a second pass after the full answer, streaming of the answer is still "the only way to get acceptable perceived latency" — [cianfrani.dev Citations in the Key of RAG](https://cianfrani.dev/posts/citations-in-the-key-of-rag/)
- Anthropic streams citations incrementally as `citations_delta` — [Claude Citations docs](https://platform.claude.com/docs/en/build-with-claude/citations)
- pypdfium2 raster ~161 ms/page (after optimization) at scale 3.0 for scanned pages; 20–92 ms/page on other hardware — [docling PR #3803](https://github.com/docling-project/docling/pull/3803)
- Parser throughput: Marker no-OCR CPU 23.7 pages/s; MinerU pipeline 0.54 pages/s on GPU (vendor-run) — [marker-pdf PyPI](https://pypi.org/project/marker-pdf/); Docling 2.332 s/doc on EdgeParse's 200-doc suite (self-run) — [edgeparse](https://github.com/xz-tak/edgeparse)

### Inferences
- Ingest-time precompute: PDF conversion + page PNGs (2 sizes) + parser JSON + normalized regions; this makes evidence rendering a static-asset fetch (CDN/object storage), independent of query latency.
- Query-time sequence: emit `sources` (doc, page, thumbnail URL, regions of retrieved chunks) immediately after rerank (~200 ms), stream tokens, attach per-sentence citations as they arrive (`citations_delta`) or in a final `citations` event after post-hoc NLI (~100–300 ms extra for a small cross-encoder/NLI on ≤20 pairs — my estimate, unsourced). Hover cards use pre-cropped region PNGs; the full pdf.js/page viewer is lazy-loaded on click.
- Post-hoc attribution's latency cost is bounded by the number of (sentence × candidate item) NLI pairs; restrict candidates to the retrieved chunks' items.

### Gaps
- No source found on pre-rendering page images at ingest as a latency technique (it is standard practice but unsourced here).
- No measured latency for NLI-based post-hoc attribution at sentence level in production found.

---

## Appendix A. Coordinate conventions cheat-sheet (sourced in KQ2)

| Tool | Unit / origin | Granularity | Field |
|---|---|---|---|
| Docling | points; `coord_origin` TOPLEFT or BOTTOMLEFT; `normalized()` available | block, table cells | `prov[].bbox{l,t,r,b}`, `page_no` |
| Marker | page points, top-left clockwise polygon | block | `polygon[4]` |
| MinerU | 0–1000 normalized (pipeline) or 0–1 (VLM); pixels in model.json | block | `bbox[x0,y0,x1,y1]`, `page_idx` |
| Unstructured | `PixelSpace` with `layout_width/height` | element | `metadata.coordinates.points` |
| PyMuPDF | points, top-left y-down | word/line/match | `get_text("words")`, `search_for()` |
| PP-StructureV3 | pixels | block (+ OCR lines) | `parsing_res_list[].block_bbox` |
| Surya | pixels | layout block, OCR line | `polygon`, `bbox` |
| Upstage | normalized points (unconfirmed) | element (+ `words`) | `elements[].coordinates[{x,y}]`, `page` |
| Azure DI | inch (PDF) / pixel (image); spans in grapheme clusters by default | word/line/paragraph | `polygon`, `span{offset,length}` |
| Textract | ratio 0–1 of page | WORD/LINE/LAYOUT/CELL | `Geometry.BoundingBox{Left,Top,Width,Height}`, `Polygon` |
| Chunkr | pixels, `left,top,width,height` (legacy x1y1x2y2) | segment + nested OCR | `segments[].bbox`, `ocr[].bbox` |
| Mistral OCR | page pixels (images); blocks normalized (OCR 4, unverified) | image / block | `images[].bbox{x,y,width,height}` |
| Anthropic Citations | n/a (text) | sentence / block / page | `start_char_index`, `start_block_index`, `start_page_number` |
| Gemini grounding | response text offsets | answer segment → chunk | `groundingSupports[].segment{startIndex,endIndex}` |
| OpenAI Responses | answer text position | file | `file_citation.index` |

Canonical target (recommended, my synthesis): `{page: 1-indexed int, bbox: [x0,y0,x1,y1] normalized 0..1, origin: top-left}` + page `{width_pt, height_pt, image_url_by_dpi}`.
