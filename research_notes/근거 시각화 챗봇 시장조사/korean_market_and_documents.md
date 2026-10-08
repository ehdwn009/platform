# Korean RAG Chatbot Market & Korean Document Processing Landscape (as of 2026-10-08)

Scope: Korean vendors offering RAG/document chatbots classified by evidence UX level, Korean document parsing/OCR/HWP handling, Korean embedding/reranker quality, evidence of what Korean enterprise users complain about, and market/regulatory context. Research conducted 2026-10-08 via Korean- and English-language web search. Every date below is the source's own publication date unless noted; several Korean news sites display "발행일: 2026-10-08" in their header, which is the site's render date, not the article date, and was ignored.

Evidence-UX scale used throughout: L0 = no citation; L1 = link/file name only; L2 = text snippet quoted; L3 = jump to page in a viewer; L4 = original page rendered with the evidence passage highlighted.

---

## Key Question 1: Map of Korean vendors/products offering RAG or document chatbots, classified by evidence UX level

### Takeaway
Only one Korean vendor, Allganize (Alli), is documented at L4 (page preview with yellow highlight of the evidence passage, shipped July 2024, Korean highlight-model accuracy self-reported at 82%). LG CNS (DAP GenAI Knowledge Lake) and Hancom (한컴피디아) are documented at roughly L1–L2 ("출처 표시 + 원본 문서 확인"). For most large vendors (Samsung SDS FabriX, SKT 에이닷 비즈, Naver Works AI Studio, KT 믿:음, Kakao, Saltlux, 42Maru, Konan, Upstage) I could not find any public documentation of how evidence is shown, which itself is a signal that evidence UX is not a marketed differentiator in Korea.

### Cited Findings

**Allganize (올거나이즈) — Alli / Alli Works — Evidence level: L4 (confirmed by vendor docs)**
- Alli answers carry numbered citations ([1], [2]); clicking opens a page preview of the source; the supporting passage is shown with a yellow background: "답변의 근거 부분을 노란색 배경색으로 프리뷰 화면에 표시합니다." Post dated 2024-10-17, by RAG team lead 이정훈. — [생성형 AI 환각 해결 전략 - 하이라이트 구현 (Generative AI hallucination strategy: implementing highlights)](https://www.allganize.ai/ko/blog-posts-ko/genai-hallucination-highlight)
- Same post: highlight model reached 82% accuracy on Korean test data; supports "한국어, 영어, 일본어 등"; argues citations alone are insufficient because "한 페이지에 텍스트가 많을 경우, 고객이 이를 읽고 이해하는 데 시간이 많이 소요됩니다" and that because RAG "100%일 수 없기 때문에" service-level design must compensate. — [same source](https://www.allganize.ai/ko/blog-posts-ko/genai-hallucination-highlight)
- Release notes (vendor): 2024-07-26 highlight of evidence passages in documents added; 2024-09-13 highlight performance improved; 2024-11-15 highlighting within answers in Alli Works; 2024-02-14 direct page-number entry in document preview; 2025-08-29 "document parsing time significantly reduced" (most recent entry). No release-note entry mentions HWP/HWPX. — [Alli What's new (업데이트)](https://docs.allganize.ai/allganize-alli-works/alli-dashboard/update)
- Alli Works product page states all features sit on the same Alli RAG engine, including on-premises deployment. — [알리 웍스 (Alli Works)](https://www.allganize.ai/ko/products/alli-works)
- Vendor marketing (unverified): "원본 문서의 레이아웃을 유지한 채 인용 위치를 직접 하이라이트" and page-level source verification. — [Allganize 홈 (엔터프라이즈 AI 솔루션, Alli)](https://www.allganize.ai/ko/home)
- Pricing: not published; enterprise sales. (No source found with list prices.)

**Upstage (업스테이지) — Solar / Document Parse / Timely / Loom — Evidence level: not documented (platform + document AI, not a packaged evidence-UX chatbot)**
- 2026-06-16: Upstage media day announced "Upstage Company" combining Solar models, the Daum portal (via acquired AXZ), and the Timely agent platform (70+ models from 12 companies); "Loom (룸)" desktop agent working on local PC files for document review/report generation, late-July launch, deployable on-prem/network-separated; Daum "AI Mode" chatbot over search results. Finance = 52.5% of 2026 revenue. Article does not mention citation/evidence display. — [업스테이지 "모델 넘어 서비스로"...'다음' 안고 에이전트 진화 (Upstage "beyond models to services")](https://www.aitimes.com/news/articleView.html?idxno=211795)
- 2025-04-15: Document Parse model document-parse-250404: "You can now upload .hwp and .hwpx files" (converted automatically for processing). — [Document Parse got stronger: Better at forms, rotation, and complex tables](https://www.upstage.ai/blog/en/upstage-document-parse-now-supports-rotated-docs-multi-page-tables-and-long-image-processing)
- Vendor claims (self-benchmark, not independently verified): DP-Bench TEDS 93.48 / TEDS-S 94.16, avg 0.6 s/page. — [Upstage Document Parse 제품 페이지](https://www.upstage.ai/ko/products/document-parse)
- Upstage acquired agent platform Timely (타임리) in June 2026 to expand no-code agents with Solar. — [업스테이지, AI 에이전트 플랫폼 '타임리' 인수](https://www.upstage.ai/ko/news/upstage-ai-agent-platform-acquisition)

**Naver Cloud (네이버클라우드) — HyperCLOVA X / CLOVA Studio RAG Reasoning / Naver Works AI Studio — Evidence level: API supports citations (L1–L2 capability); Works AI UX not documented**
- CLOVA Studio has a "RAG Reasoning" API described as generating grounded answers with answer types including "인용 출처" and "인용 출처 인덱싱 표기" (citation sources and indexed citation markers); separate Reranker API and 문단 나누기(segmentation)/임베딩 APIs. — [CLOVA Studio API 활용 - docs ncloud](https://guide.ncloud-docs.com/docs/clovastudio-explorer03); [CLOVA Studio 개요 - API 가이드](https://api.ncloud-docs.com/docs/ai-naver-clovastudio-summary)
- 2026-08-12: Naver Cloud announced three Naver Works features for government (행정망): Naver AI search via "AI탭" API, "Page" knowledge/document collaboration, and "EASY (Enterprise Agent Solution for You)" agent builder; "이용자는 행정망 안에서 외부 최신 정보와 부처 내부 문서를 함께 확인할 수 있다." Evidence display not described. — [네이버웍스, 문서 협업·AI 에이전트로 공공 업무 지원 (SEN TV)](https://www.sentv.co.kr/article/view/sentv202608120101); [네이버클라우드, 행정망에 AI 검색 심는다 (Nate)](https://m.news.nate.com/view/20260813n27369)
- 2026-02-27 Naver Works blog: AI Studio "회사 지침 및 규정 안내" assistant trained on an internal 업무 매뉴얼 board and PDFs; the headline "80% reduction" is from an accounting-expense assistant (weekly inquiries 5 → 1, no sample size), regulations bot "40–60%". The post does not describe source citations and does not mention HWP. — [사내 규정 문의 80% 줄인 비결 (How internal-rule inquiries fell 80%)](https://naver.worksmobile.com/blog/management-ai/)
- The ChatGPT-HWP article notes Naver's CLOVA X already supported HWP/HWPX upload. — [챗GPT, 한글 문서 품다…'AI 장벽' 오해 벗은 한컴 (ChatGPT embraces HWP) 2026-04-18](https://m.news.nate.com/view/20260418n02022)

**Samsung SDS (삼성SDS) — FabriX / Brity Copilot — Evidence level: not documented (L0–L1 unknown)**
- 2024-05-02 media day: FabriX connects company data/knowledge assets/business systems to generative AI; "정확도 높은 답변을 제공한다"; ~100,000 FabriX users; Brity Copilot piloted with ~10,000 employees; Korean speech recognition 94%; no statement on citations, file formats, or pricing. — [삼성SDS, 생성형 AI 서비스 FabriX와 Brity Copilot 출시 (Samsung SDS launches FabriX and Brity Copilot)](https://www.samsungsds.com/kr/news/sds-genai-mediaday.html)
- SDS insights page (2023-11-03): FabriX service module "사내 업무시스템 데이터와 연계할 수 있는 RAG 기능을 포함"; claims "환각 없는 정확한 응답 생성이 가능합니다"; RAG over PPT/PDF; no citation UX or HWP mention. — [삼성SDS 생성형 AI 서비스 - Simply Fit, Simply Chat](https://www.samsungsds.com/kr/insights/fabrix.html)
- SDS AI Journey page: employees register purchasing/sales/finance guide documents or personal documents as a "카탈로그" and use RAG Q&A; keyword filtering and permission controls. — [우리는 이미 AI와 일하고 있다. 삼성SDS AI Journey](https://www.samsungsds.com/kr/insights/samsung-sds-ai-journey.html)
- 2025-06-26 report claims FabriX wins in finance/public sector on language support and price vs Microsoft (vendor-friendly trade press, no numbers verified). — [삼성SDS, AI플랫폼 '패브릭스' 금융·공공에서 벌써 '합격점' (CEOSCOREDAILY)](https://www.ceoscoredaily.com/page/view/2025062616021226712)
- Samsung SDS Research published the SDS KoPub VDR benchmark (Nov 2025), the first large-scale public benchmark for visual document retrieval over Korean public documents (see KQ2). — [SDS KoPub VDR (arXiv 2511.04910)](https://arxiv.org/abs/2511.04910)

**LG CNS — DAP GenAI / Knowledge Lake — Evidence level: L1–L2 (source shown, opens original document)**
- 2024-04-16: Knowledge Lake processes Word, PowerPoint, Excel, 한글, PDF; recognizes tables and images; 5-step pipeline (적재 → 파싱·구조화 → 시맨틱 청킹 → 의미 강화 → 임베딩); "답변 관련 상세 내용이 필요한 경우, 별도 표기된 답변 출처를 통해 원본 문서를 확인할 수도 있다." No page-level or highlight claim. — [LG CNS, 기업용 생성 AI 플랫폼 고도화...이미지 생성·상담 챗봇·문서 분석까지 지원 (AI타임스)](https://www.aitimes.com/news/articleView.html?idxno=158817)
- Platform installable on customer's own servers/cloud. — [LG CNS, DAP GenAI 플랫폼으로 생성형 AI 해답 제시 (뉴스와이어)](https://www.newswire.co.kr/newsRead.php?no=987856)

**LG AI Research — EXAONE / ChatEXAONE — Evidence level: web-search sources shown; internal-document citation UX not documented**
- ChatEXAONE connects to internal document systems with per-user access control; "출처 선택" lets users choose web search scope (general, overseas, academic, YouTube); deep research "출처도 명확히 알려주는" — applies to web, not confirmed for internal documents. — [ChatEXAONE: Enterprise AI Agent 오픈 (LG AI Research blog)](https://www.lgresearch.ai/blog/view?seq=457); [[LG AI 토크콘서트 2025] '챗엑사원' 일반 공개 (THE AI)](https://www.newstheai.com/news/articleView.html?idxno=8412)

**SK Telecom — 에이닷 비즈 (A. Biz) / 에이닷 비즈 코워크 — Evidence level: not documented**
- 2026-04-29: update added "오토-RAG" ("사내 문서를 자동으로 연결·검색·활용해 답변 정확도를 높인다"), 에이전트 빌더 and 에이전트 스토어 for "1인 1에이전트"; co-developed with SK AX; used by 18 SK affiliates. No citation UX described. — [SK텔레콤 '에이닷 비즈' 나만의 에이전트 적용…한국형 클로드 노린다 (전자신문)](https://www.etnews.com/20260429000339)
- 2026-05-28: '에이닷 비즈 코워크' applied internally; legal contract-review example. — [SKT, 업무 AI 에이전트 '에이닷 비즈 코워크' 사내 적용 (바이라인네트워크)](https://byline.network/2026/05/28-613/)
- Note: SKT's A.X-Encoder-base is the base model for KURE-v2 (see KQ2). — [nlpai-lab/KURE](https://github.com/nlpai-lab/KURE)

**KT — 믿:음 (Mi:dm) K 2.5 Pro — Evidence level: not documented**
- 2026-02-26 (MWC26): 믿:음 K 2.5 Pro, 32B, 128K context "수백 페이지 분량의 문서 분석"; used in AICC, product/knowledge-search chatbots, document recognition, legal/finance. No RAG/citation UX details. — [KT, 자체 개발 AI 모델 '믿:음 K 2.5 프로' 공개 (Nate)](https://m.news.nate.com/view/20260226n35390); [KT, MWC26에서 믿:음 프로 선보인다 (뉴스핌)](https://www.newspim.com/news/view/20260226000349)
- KT claims "자체 개발한 벡터 모델 기반 한국적 검색증강생성(RAG) 기술" for enterprise knowledge bases (press wording, unverified). — [same 전자신문 search context](https://www.etnews.com/20260429000339)

**Kakao — 카나나 (Kanana) — Evidence level: N/A (consumer-focused)**
- 2026-04-22: Kakao started beta of AI search "카나나" inside KakaoTalk chat rooms (reservations, shopping, finance); consumer service. No enterprise document-chatbot product found. — [카카오, 카톡 내 AI 검색 도입…'카나나' 베타 시작 (Nate)](https://m.news.nate.com/view/20260422n26524)
- Kakao Enterprise's 2024 RAG talk emphasised chunking as core. — [[if카카오2024] 카카오엔터프라이즈의 RAG, '청크'가 핵심 (바이라인)](https://byline.network/2024/10/241022_kakaorag_3/)

**Wrtn (뤼튼) — 뤼튼 AX — Evidence level: not documented**
- Wrtn AX offers an enterprise version of Wrtn plus custom agents; RAG chatbot "사내·외의 정보와 맥락을 한 곳에서 물어보세요"; "PDF, HWP, 이미지 등 디지털 전환이 어려운 문서를 읽고 이해하는 한국어 특화 문서 분석 솔루션" (marketing claim). — [뤼튼 AX 공식 웹사이트](https://wrtn.ax/)

**Saltlux (솔트룩스) — LUXIA / LUXIA-ON — Evidence level: claims "근거 제시" (unspecified)**
- Vendor page: "분산된 고객 정형데이터를 지식그래프화 하여 추론을 통해 정확한 답변과 근거를 제시합니다"; LUXIA-ON appliance includes RAG over internal documents. No viewer/highlight documented. — [솔트룩스 LLM 및 RAG 운영 플랫폼](https://www.saltlux.com/kor/contents/view.do?mId=40); [LUXIA-ON](https://luxiaon.com/)
- 2025-02-13: LUXIA 2.5 announced as cheaper than DeepSeek (trade press). — [베일 벗은 솔트룩스 '루시아 2.5' (디지털데일리)](https://m.ddaily.co.kr/page/view/2025021318435920837)

**42Maru (포티투마루) — RAG42 / LLM42 — Evidence level: not documented**
- 2026-09-16: LLM42 v1.1 and RAG42 v1.3 received the government "인공지능 제품·서비스 확인제" certification (public procurement preference). RAG42 described as enterprise RAG over "PDF, 워드, 한글, 스캔 이미지 등". — [포티투마루, 정부 '인공지능 제품·서비스 확인제' 인증 획득 (Nate)](https://m.news.nate.com/view/20260916n33472); [포티투마루, 공공·방산 AI는 '내부망'에서 (벤처스퀘어)](https://www.venturesquare.net/1117996)

**Skelter Labs (스켈터랩스) — BELLA QNA / BELLA Agent — Evidence level: not documented; company acquired by Metanet**
- BELLA QNA: GPT-based Q&A over internal information; 2024-04 "all LLM" integration with RAG + hybrid search to prevent hallucination; Metanet acquired Skelter Labs. — [스켈터랩스, '벨라 큐나' 모든 LLM 연동 가능해진다 (아주경제)](https://www.ajunews.com/view/20240416150201316); [메타넷, '벨라 LLM' 스켈터랩스 인수로 무엇을 노리나 (블로터)](https://www.bloter.net/news/articleView.html?idxno=619793)

**Konan Technology (코난테크놀로지) — Konan RAG-X — Evidence level: not documented**
- 2025-05-13: Konan RAG-X is a multi-source on-premises B2B RAG (internal DB, legacy, public data, news); beta 2025-02-13; GS인증 1등급. — [코난테크놀로지, 올해 실적 반등 노린다 (ZDNet Korea)](https://zdnet.co.kr/view/?no=20250513140859); [코난테크놀로지 IR](https://m.irgo.co.kr/IR-COMP/402030/%EC%BD%94%EB%82%9C%ED%85%8C%ED%81%AC%EB%86%80%EB%A1%9C%EC%A7%80-IR-PAGE)

**MAUM AI (마음AI) — not a document-chatbot vendor in 2026**
- 2026-05: Positioned as Physical AI; presented edge-agent AI for maritime domain awareness. — [마음AI, 부산서 발표 (ZDNet Korea)](https://zdnet.co.kr/view/?no=20260518141445)

**Dalpha (달파) — document agent for manufacturing/distribution — Evidence level: not documented**
- Agent with three functions: 문서 데이터 매칭·자동화, 지류문서 DB화 (claims 99% OCR refinement accuracy), 사내 AI 챗봇; based on ~1,600 AI consulting engagements (vendor claims). — [달파, 제조·유통 특화 AI 에이전트 출시 (AI타임스)](https://www.aitimes.com/news/articleView.html?idxno=169632)

**Hancom (한글과컴퓨터) — 한컴피디아 (Hancompedia) / 한컴어시스턴트 / 한컴독스 AI — Evidence level: L1–L2 ("답변에 출처를 명시")**
- 2024-12-06 launch: 한컴피디아 is a document-based Q&A solution using RAG to reduce hallucination, "답변에 출처를 명시"; supports 한글 and PDF; web browser and on-premises; admin page with permission management; targets public sector and enterprise. 한컴어시스턴트: document-writing AI with cloud LLM or sLM for closed networks. — [한컴, AI 솔루션 한컴어시스턴트·한컴피디아 정식 출시 (AI타임스)](https://www.aitimes.com/news/articleView.html?idxno=165976)
- 한컴독스 AI: template/sentence generation/editing/image generation inside Web 한글; limitation noted that answers are based on the originally loaded document until re-saved. — [한컴독스 AI는 어떤 기능을 제공하나요?](https://support.hancom.com/42502a3d-c88c-4896-ad1b-faf369fd0036); [“이력서도 뚝딱”…한컴독스 AI 써보니 (바이라인)](https://byline.network/2024/10/241014_004/)
- 2026-04-18: OpenAI's ChatGPT began accepting HWP/HWPX uploads, reportedly without a partnership with Hancom; the article says HWP/HWPX dominates Korean (esp. public-sector) document use and that lack of support by global LLM services had limited practical use in Korea. — [챗GPT, 한글 문서 품다 (Nate/연합 계열)](https://m.news.nate.com/view/20260418n02022)

**Polaris Office (폴라리스오피스) — Polaris Office AI (NOVA) / Polaris AI DataInsight parser — Evidence level: not documented**
- 2025-12-29 blog: "Polaris AI DataInsight" parser reads document internal object structure for HWP, PDF, XLSX, DOC, DOCX; claims chart/graph value extraction, nested tables, multi-column, headers/footers, hidden layers, metadata; cites a survey that ~90% of public-administration workers write reports in HWP, image, or scanned PDF. Comparison table vs "other products/open-source parsers that don't support HWP/HWPX" is vendor marketing without benchmarks. — [공공 AI 전환 막는 HWP, 데이터는 있는데 AI가 못 읽는다? (Polaris Office business blog)](https://www.polarisoffice.com/business-blog/hwp-ai)
- Consumer product: HWP/PDF/MS Office in one place with summary, translation, PPT generation, AI chat. — [폴라리스 오피스](https://www.polarisoffice.com/ko)

**Smaller Korean doc-chat vendors found incidentally**
- AntDocs Chat (앤트피플): claims "모든 답변에 근거(출처 파일/페이지/문단)를 함께 제공" (L2–L3 claim, unverified). — [AntDocs Chat](https://www.antpeople.co.kr/sub/services/ai/antdocs-chat/)
- DFINITE "DARVIS" (사내용 AI 챗봇) blog recommends showing "규정집 페이지" as sources. — [사내 챗 GPT를 찾는다면? (DFINITE)](https://blog.dfinite.ai/company-chatgpt-guide)
- Makebot: "Hybrid RAG 기반 지능형 사내 검색 엔진" (marketing). — [Makebot.ai](https://www.makebot.ai/)

### Summary table (my classification; "n/d" = not documented publicly)

| Vendor / product | Evidence UX | Korean docs | HWP | Pricing / target |
|---|---|---|---|---|
| Allganize Alli | **L4** (page preview + yellow highlight, since 2024-07) | Yes (KR/EN/JP highlight model) | n/d in release notes | Enterprise, on-prem option |
| LG CNS DAP GenAI Knowledge Lake | L1–L2 (출처 표기 → 원본 문서 열기) | Yes | "한글 문서" listed | Enterprise, self-hosted |
| Hancom 한컴피디아 | L1–L2 ("답변에 출처 명시") | Yes | HWP + PDF | Public/enterprise, on-prem |
| Naver CLOVA Studio RAG Reasoning API | L2 capability (citation markers) | Yes | CLOVA X supports HWP upload | API usage pricing |
| Naver Works AI Studio | n/d | Yes | n/d | SMB/public; 30-day trial |
| Samsung SDS FabriX | n/d | Korean-optimised claims | n/d | Large enterprise; ~100k users (2024) |
| SKT 에이닷 비즈 (오토-RAG) | n/d | Yes | n/d | SK Group + enterprise |
| KT 믿:음 K 2.5 Pro | n/d | Yes | n/d | B2B |
| LG AI Research ChatEXAONE | Web sources shown; internal docs n/d | Yes | n/d | LG Group → public |
| Kakao Kanana | N/A | — | — | Consumer |
| Wrtn AX | n/d (claims HWP) | Yes | Claimed | Enterprise/edu/public |
| Saltlux LUXIA | "근거 제시" (unspecified) | Yes | n/d | Enterprise/public, appliance |
| 42Maru RAG42 | n/d | Yes | HWP listed | Public/defense, on-prem |
| Skelter Labs BELLA | n/d | Yes | n/d | Enterprise/edu |
| Konan RAG-X | n/d | Yes | n/d | On-prem B2B |
| Polaris Office AI / DataInsight | n/d | Yes | Parser claims HWP | Consumer + enterprise parser |
| Upstage (Solar/Doc Parse/Loom) | n/d | Yes | Document Parse accepts HWP/HWPX | API + on-prem; finance-heavy |
| Dalpha | n/d | Yes | n/d | Manufacturing/distribution |
| MAUM AI | N/A (physical AI pivot) | — | — | Defense/maritime |

### Inferences
- The Korean enterprise market is crowded on "RAG reduces hallucination" messaging, but almost nobody markets *how* the user verifies the answer. Allganize is the only vendor with a documented L4 experience, and its own 2024 blog argues the exact thesis of the user's product (citations are not enough on long pages). This leaves room for a personal/SMB L4 product, since Allganize is enterprise-sales only.
- Large SIs (Samsung SDS, LG CNS, SK AX) bundle RAG into group-wide platforms; evidence UX appears to be a secondary feature, consistent with the user's experience that their company's GPTs-like feature is slow/inaccurate and people re-open documents.
- HWP support is now table stakes for Korean document AI (ChatGPT, CLOVA X, Upstage, LG CNS, Hancom, 42Maru, Wrtn all claim it), but no vendor publishes HWP parsing accuracy numbers.

### Gaps
- No public documentation found of citation/evidence UX for Samsung SDS FabriX/Brity Copilot, SKT 에이닷 비즈, KT 믿:음, Naver Works AI Studio, Saltlux, 42Maru, Konan, Skelter Labs, Wrtn, Dalpha. Classification would require demos or sales decks.
- No list pricing found for any enterprise vendor; all are quote-based.
- Could not confirm whether Allganize preserves the original page layout (rendered image) vs re-flowed text in its preview; the 2024 post only confirms yellow highlighting in a page preview.
- Could not find a Korean vendor explicitly offering a *personal* (per-individual, self-serve) document chatbot with L3+ evidence; the closest are consumer office tools (Polaris, Hancom Docs AI) that do not expose evidence UX.

---

## Key Question 2: Korean document processing — parsing/OCR/layout tools, HWP/HWPX handling, embeddings/rerankers, Korean chunking issues

### Takeaway
There is no independent, published Korean-PDF benchmark comparing Upstage Document Parse, CLOVA OCR, Docling, MinerU, Marker and PaddleOCR; the available evidence is vendor self-benchmarks (Upstage DP-Bench), a 2023 developer comparison, and non-Korean studies. For HWP, the practical Linux-server options are: convert HWP/HWPX to PDF with LibreOffice + the H2Orestart extension (Java required, layout fidelity imperfect) or send to a commercial API (Upstage Document Parse accepts HWP/HWPX since April 2025); for text/table extraction use python-hwpx (HWPX, Apache-2.0) or hwp-hwpx-parser (HWP5+HWPX, pure Python), while pyhwp/hwp5txt is AGPL and loses table contents. For embeddings, Korean-tuned models (KURE-v2 nDCG@10 0.816, BGE-m3-ko) beat vanilla BGE-M3 (0.751) on MTEB-ko-retrieval; for rerankers, bge-reranker-v2-m3 (0.966 nDCG@10) and jina-reranker-v3.5 (0.984) lead on AutoRAGRetrieval. Korean BM25 needs morphological tokenization (Kiwi) to handle josa.

### Cited Findings

**Parsing / OCR / layout tools on Korean documents**
- Upstage Document Parse accepts .hwp/.hwpx since model document-parse-250404 (2025-04-15); vendor self-benchmark TEDS 93.48, TEDS-S 94.16 on DP-Bench; claims 0.6 s/page. No HWP-specific accuracy published. — [Document Parse got stronger (2025-04-15)](https://www.upstage.ai/blog/en/upstage-document-parse-now-supports-rotated-docs-multi-page-tables-and-long-image-processing); [Upstage Document Parse 제품 페이지](https://www.upstage.ai/ko/products/document-parse)
- 2023-11 developer comparison of 8 OCR APIs: Upstage rated 한글 인식 "상", 표/글자 "상", provides per-word confidence; Naver CLOVA OCR rated 한글 "상", 표/글자 "중상", ₩3/call with 300 free/month (2023 pricing); speed order Google > Azure > Upstage > CLOVA. — [[OCR/AI] OCR 성능 평가: 8가지 파이썬 API 비교평가 (2023)](https://sooeun67.github.io/data%20science/ocr-comparison/)
- 2026 Korean OCR roundups claim CLOVA accuracy 97–99% and note CLOVA supports common Hanja but struggles with classical/abbreviated Hanja (blog claims, unverified). — [한국어 OCR 솔루션 8가지 (Lido, 2026)](https://www.lido.app/kr/hangugeo-ocr)
- Docling vs MinerU (non-Korean, individual test): MinerU 97.5 mAP layout detection; 12-page doc Docling 8.2 s vs MinerU 14.7 s; MinerU "tends to do better on scanned research papers and CJK-language scans"; licensing differs (Docling MIT/Apache-2.0 reported; MinerU AGPL-3.0 per one source) — check LICENSE files. — [Docling vs MinerU: I Tested Both (2025)](https://www.codesota.com/ocr/docling-vs-mineru); [Docling vs MinerU for RAG](https://www.file2markdown.ai/blog/docling-vs-mineru)
- MinerU Korean-language support discussed on PyTorch KR forum. — [MinerU ... (feat. 한국어 지원) (PyTorchKR)](https://discuss.pytorch.kr/t/mineru-pdf-json-markdown-ocr-feat/5527)
- arXiv 2604.04948 (May 2026, Portuguese Army documents, NOT Korean): Docling + hierarchical splitting + VLM image descriptions 94.1 ± 1.6% vs manually curated Markdown 91.3%, LangChain PDFLoader 86.2%, DeepSeek OCR 79.1%; tables were the main failure point (naive loaders <70% on table questions vs >99% for structure-aware). Korean-language abstract on alphaxiv is a translation only. — [From PDF to RAG-Ready (alphaxiv KR page)](https://www.alphaxiv.org/ko/abs/2604.04948)
- SDS KoPub VDR (Samsung SDS Research, submitted 2025-11-07, v2 2025-11-10): "first large-scale, public benchmark for retrieving and understanding Korean public documents"; 361 real documents (256 KOGL Type-1, 105 from legal portals), 600 query–page–answer triples across 6 public domains, queries categorised text-based / visual-based / cross-modal; two tasks (text-only and multimodal retrieval); reports "substantial performance gaps, especially for cross-modal reasoning, even for state-of-the-art models." Dataset on Hugging Face `SamsungSDS-Research/SDS-KoPub-VDR-Benchmark`. — [SDS KoPub VDR (arXiv abs)](https://arxiv.org/abs/2511.04910)
- Korean public-document OCR study (2025): compared detectors (DBNet, PANet) and recognizers (CRNN, SAR) on a large public OCR dataset of local-government and diplomatic-archive documents of low quality. — [Comparative Analysis of OCR Architectures for Low-Quality Korean Public Documents (KoreaScience)](https://www.koreascience.kr/article/JAKO202509154004102.page)
- AI Hub Korean OCR datasets: "다양한 형태의 한글 문자 OCR" (printed + handwritten incl. table text, ~1.17M), "한국어 글자체 이미지" (11,172 syllables × 50 fonts); no Korean *layout/reading-order* dataset found. — [AI허브 다양한 형태의 한글 문자 OCR](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&dataSetSn=91); [한국어 글자체 이미지](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=81)

**HWP / HWPX handling**
- Hancom has published the HWP format spec since 2010; HWPX (XML, OWPML) became Hancom's default in 2021; HWP/HWPX reportedly >90% share in some official contexts. — [챗GPT, 한글 문서 품다 (2026-04-18)](https://m.news.nate.com/view/20260418n02022)
- Velog post "한국 공문서 RAG의 첫 번째 벽: HWP 파싱 해결법": HWP is a proprietary binary; on server/Mac/Linux "현실적으로 API를 써야 합니다"; shows LangChain loader for .hwp. — [한국 공문서 RAG의 첫 번째 벽 (Velog)](https://velog.io/@pjg9606/%ED%95%9C%EA%B5%AD-%EA%B3%B5%EB%AC%B8%EC%84%9C-RAG%EC%9D%98-%EC%B2%AB-%EB%B2%88%EC%A7%B8-%EB%B2%BD-HWP-%ED%8C%8C%EC%8B%B1-%ED%95%B4%EA%B2%B0%EB%B2%95)
- pyhwp (mete0r): hwp5txt drops table-cell contents (open GitHub issue), hwp5html breaks images, hwp5odt loses images; one project rejected pyhwp because it is AGPL-3.0; workaround: `hwp5html --html` then flatten tables; `hwp5proc cat file BinData/BIN0002.jpg` extracts embedded images. — [hwp5txt 표 텍스트 미추출 Issue #169](https://github.com/mete0r/pyhwp/issues/169); [hwp5txt/hwp5html/hwp5odt error Issue #165](https://github.com/mete0r/pyhwp/issues/165); [hwp5proc docs](https://pythonhosted.org/pyhwp/ko/hwp5proc.html); [OpenArchive Issue #133 (HWPX·HWP 5.0 파서)](https://github.com/jeongeundev/OpenArchive/issues/133)
- python-hwpx (airmang): pure-Python HWPX read/edit/generate/validate without Hancom Office, Apache-2.0; Markdown export preserving tables/footnotes/images with `image_dir`; releases continuing into 2026 (piwheels lists 2.11.1, 2026-06-12; Libraries.io shows 2.9.1 — verify on PyPI). — [GitHub airmang/python-hwpx](https://github.com/airmang/python-hwpx); [piwheels python-hwpx](https://www.piwheels.org/project/python-hwpx/)
- hwp-hwpx-parser (KimDaehyeon6873): pure Python, HWP5 + HWPX, only dependency olefile, methods get_images / get_tables_as_markdown / get_tables_as_csv; maturity unverified. — [GitHub hwp-hwpx-parser](https://github.com/KimDaehyeon6873/hwp-hwpx-parser)
- kordoc (chrisryugj): HWP 3.x/5.x, HWPX, PDF, Office → Markdown; CLI + MCP server. — [GitHub kordoc](https://github.com/chrisryugj/kordoc)
- hwp2md (roboco-io): Go-based HWP→Markdown v0.5.0. — [libraries.io hwp2md](https://libraries.io/go/github.com%2Froboco-io%2Fhwp2md)
- olefile approach: read PrvText stream (UTF-16 preview text) — not full body. — [파이썬에서 hwp 파일을 데이터프레임으로 (Medium)](https://thlee33.medium.com/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%97%90%EC%84%9C-hwp-%ED%8C%8C%EC%9D%BC%EC%9D%84-%EB%8D%B0%EC%9D%B4%ED%84%B0%ED%94%84%EB%A0%88%EC%9E%84%EC%9C%BC%EB%A1%9C-%EB%A7%8C%EB%93%A4%EA%B8%B0-a1af8b6bd235)
- pyhwpx (martiniifun): Hancom Office COM automation, Windows-only, requires 한/글 installed. — [pyhwpx PyPI](https://pypi.org/project/pyhwpx/)
- HWP → PDF on Linux:
  - LibreOffice extension H2Orestart (successor of hwp2odt) adds HWP/HWPX import; save as ODT only; requires JRE 8+; packaged in Ubuntu (0.7.13-1); complex-table rendering improved vs hwp2odt 0.4.6; one production log: "LibreOffice 25.2 + h2orestart로 사용자 실제 .hwpx(220KB) → PDF 430KB 변환 성공"; font substitution issues discussed in GitHub issues. — [H2Orestart LibreOffice Extensions](https://extensions.libreoffice.org/en/extensions/show/27504); [Launchpad h2orestart](https://launchpad.net/ubuntu/+source/h2orestart); [FileSharer PR #57 (.hwpx preview via H2Orestart)](https://github.com/jijungon/FileSharer/pull/57); [hwp2odt GitHub](https://github.com/k2webtech/hwp2odt); [hwp to pdf in LINUX (Velog)](https://velog.io/@shyim/hwp-to-pdf-in-LINUX)
  - simple-hwp2pdf (PyPI): "Standalone" engine needs no Hancom Office, any OS, ".hwpx만 완벽 지원, 레이아웃 깨질 수 있음"; "Office" engine requires Windows + Hancom. — [simple-hwp2pdf PyPI](https://pypi.org/project/simple-hwp2pdf/)
  - rhwp (Rust): PDF exporter for HWP/HWPX without Hancom tracked as an open issue (planning stage). — [rhwp Issue #21](https://github.com/edwardkim/rhwp/issues/21)
  - hwp2pdf-plus, hwpdf: Hancom automation, Windows-only. — [gomgom/hwp2pdf-plus](https://github.com/gomgom/hwp2pdf-plus); [gaon12/hwpdf](https://github.com/gaon12/hwpdf)
  - Adaptive parser "docparse" recommends converting legacy binary HWP to HWPX before ingestion. — [Engccer/docparse](https://github.com/Engccer/docparse)

**Korean embedding models and benchmarks**
- MTEB-ko-retrieval leaderboard (nlpai-lab, Korea University), avg over 9 Korean retrieval tasks, nDCG@10 / Recall@10: KURE-v2 (late-interaction, 154M, base skt/A.X-Encoder-base, released 2026-08-29) 0.8160 / 0.8921; yjoonjang/colbert-ko-en-v2 0.8063; KURE-v1 (dense, 568M, base BAAI/bge-m3, 2024-12-21) 0.7616 / 0.8629; dragonkue/BGE-m3-ko 0.7547; BAAI/bge-m3 0.7509 / 0.8588; nlpai-lab/KoE5 (base multilingual-e5-large, 2024-10-02) 0.7337; KURE-v2-unsupervised 0.7283. No Upstage/OpenAI rows; KURE-v2 was distilled from a reranker teacher. Caveat: KURE-v2 is late-interaction (ColBERT-style) so vector-DB integration differs. — [GitHub nlpai-lab/KURE](https://github.com/nlpai-lab/KURE); [임베딩 모델 선택 가이드 (data-dynamics)](https://www.data-dynamics.io/ko/blog/embedding-model-guide)
- Older KURE repo numbers (Top-k 5 avg NDCG): KURE-v1 0.67479, BGE-m3-ko 0.66692, bge-m3 0.66615 — different aggregation than the table above; sources disagree on exact figures. — [ko-embedding-leaderboard discussion (PyTorchKR)](https://discuss.pytorch.kr/t/ko-embedding-leaderboard/11416); [OnAnd0n/ko-embedding-leaderboard](https://github.com/OnAnd0n/ko-embedding-leaderboard); [su-park/mteb_ko_leaderboard](https://github.com/su-park/mteb_ko_leaderboard)
- Upstage embeddings: vendor claims superiority over OpenAI text-embedding-3-large in EN/KO/JA without a published table. Third-party (dragonkue model card, AutoRAG Korean retrieval): Upstage Embedding F1 0.4211 vs text-embedding-3-large 0.3947 at top-k 3; at top-k 10 mAP 0.1078 vs 0.1180 (OpenAI ahead), NDCG ~tie; BGE-m3-ko highest on most metrics. — [Upstage solar-embedding-1-large blog](https://www.upstage.ai/blog/en/solar-embedding-1-large); [dragonkue/BGE-m3-ko model card](https://huggingface.co/dragonkue/BGE-m3-ko)
- 2024-06 Steemit AutoRAG test: solar-embedding-1-large retrieval F1 0.31 / recall 0.62 vs text-embedding-3-small F1 0.22 / recall 0.44 (old versions). — [한국어 임베딩 모델 성능 비교 테스트 결과 (Steemit)](https://steemit.com/krdev/@anpigon/20240604t162445271z)
- Community thread on choosing Korean embeddings. — [한국어 임베딩에는 어떤 모델이 제일 좋을까요? (클리앙)](https://www.clien.net/service/board/cm_ai/18603203)

**Korean rerankers**
- Cheon Reranker cookbook, nDCG@10 on AutoRAGRetrieval (MTEB(kor), 114 questions over finance/public/healthcare/legal/commerce), same harness for all: jina-reranker-v3.5 (0.6B) 0.98381; jina-reranker-v3 0.97734; Qwen3-Reranker-4B 0.97065; Cheon Reranker (0.609B, Qwen3-0.6B base, 12,288-token context, fp32 ~2.4 GB) 0.96648; bge-reranker-v2-m3 (0.568B) 0.96627; Qwen3-Reranker-8B 0.95462; mxbai-rerank-large-v2 0.95311; zerank-2 0.94361. Example pipeline: BM25 over Kiwi morphemes on the 720-passage AutoRAG set, rerank top-10. No latency numbers. — [cheon-ai-official/cheon-reranker](https://github.com/cheon-ai-official/cheon-reranker)
- dragonkue/bge-reranker-v2-m3-ko: reported F1 0.9123 (Top-k 1) on Korean AutoRAG benchmark, cited as "1위" by a production Korean RAG project; metric differs from nDCG@10 above so not directly comparable. — [Dataloop model page](https://dataloop.ai/library/model/dragonkue_bge-reranker-v2-m3-ko/); [urstory/urstory-rag README](https://github.com/urstory/urstory-rag)
- Naver CLOVA Studio offers a hosted Reranker API. — [CLOVA Studio API 활용](https://guide.ncloud-docs.com/docs/clovastudio-explorer03)

**Korean chunking / tokenization issues**
- KCI paper (KorQuAD 1.0): 200-character fixed chunking gave the best answer accuracy (68.8%); similarity rose with chunk size but answer accuracy fell after 300 chars; sentence-level chunking performed worst. — [RAG 시스템에서 고정 크기 및 문장 단위 청킹 전략의 평가 (KCI)](https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART003311446)
- AI타임스: fixed-size chunking splits tables and separates image captions, ignoring visual structure of manuals. — [산업용 RAG가 실패하는 이유 중 하나는 데이터 '청킹' 방식 때문](https://www.aitimes.com/news/articleView.html?idxno=206222)
- kt cloud tech series: where you cut changes nDCG/Recall/Precision for the same document. — [kt cloud RAG #3 청킹 전략과 최적화 (2025-11)](https://tech.ktcloud.com/entry/2025-11-ktcloud-rag-ai-%EC%B2%AD%ED%82%B9%EC%A0%84%EB%9E%B5-%EC%B5%9C%EC%A0%81%ED%99%94)
- Josa problem in BM25: "연차는" ≠ "연차"; one project indexed Hangul bigrams alongside tokens; others tokenise with Kiwi keeping 체언·용언 어간·어근·외국어·한자·숫자 so josa drop out. — [mindexxo/rag-agent Issue #128](https://github.com/mindexxo/rag-agent/issues/128); [hyeonsikseo/finance-agentic-rag-s04](https://github.com/hyeonsikseo/finance-agentic-rag-s04)
- Small eval (16 synthetic docs, 20 queries): plain BM25 Top-3 hit 11/15 vs Kiwi-BM25 15/15; difference vanished when fused with dense retrieval (RRF k=60); reranker gain not confirmed at that scale. — [AH_05_06 PR #341](https://github.com/AI-HealthCare-05/AH_05_06/pull/341)
- LangChain KR tutorial: Kiwi/Kkma/Okt + BM25 + FAISS comparison (39 configurations); `KiwiBM25Retriever` in langchain_teddynote. — [한글 형태소 분석기(Kiwi, Kkma, Okt) + BM25 검색기 (WikiDocs)](https://wikidocs.net/251980); [velog 한글 검색 성능 테스트](https://velog.io/@sobit/%ED%95%9C%EA%B8%80-%EA%B2%80%EC%83%89-%EC%84%B1%EB%8A%A5-%ED%85%8C%EC%8A%A4%ED%8A%B8-l-%ED%95%9C%EA%B8%80-%ED%98%95%ED%83%9C%EC%86%8C-%EB%B6%84%EC%84%9D%EA%B8%B0Kiwi-Kkma-Okt-BM25-FAISS-%EB%A6%AC%ED%8A%B8%EB%A6%AC%EB%B2%84)
- AutoRAG blog on Korean BM25 tokenizer pitfalls; AutoRAG supports Kiwi tokenizer config for BM25. — [한국어 문서에서 BM25 사용 시 꼭 확인해야 할 것 (AutoRAG velog)](https://velog.io/@autorag/%ED%95%9C%EA%B5%AD%EC%96%B4-%EB%AC%B8%EC%84%9C%EC%97%90%EC%84%9C-BM25-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EA%BC%AD-%ED%99%95%EC%9D%B8%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B2%83)
- Real-world incident: Qwen2.5-7B answered Korean questions in Chinese in a Korean RAG project (model-choice risk for Korean). — [RAG_maker Issue #3](https://github.com/hjoong-tangunsoft/RAG_maker/issues/3)
- National Institute of Korean Language ran a 2025 task "한국어 어문 규범 기반 생성(RAG)" (Korean orthography-norm RAG). — [국립국어원 2025 과제](https://kli.korean.go.kr/taskOrdtm/taskList.do?taskOrdtmId=182&clCd=END_TASK&subMenuId=sub01)

### Inferences
- For a visual-grounding product, the rendering path matters more than text extraction: HWP/HWPX must become a page image. On Linux the only free path with reasonable fidelity is LibreOffice headless + H2Orestart (JRE), with known font-substitution/layout drift; a commercial fallback is Upstage Document Parse (accepts HWP/HWPX and returns layout elements with coordinates, which is what highlight overlays need). Keeping the original HWP bytes and showing a converted-PDF page is acceptable only if highlight coordinates are computed on the same converted PDF.
- Because there is no Korean layout benchmark, the team should build a small internal test set (e.g., 50–100 pages of their own HWP/PDF docs) and measure table-cell retention, reading order, and Hangul OCR error rate across Docling, MinerU, Upstage, CLOVA — every source I found recommends exactly this.
- Embedding choice: KURE-v1 or BGE-m3-ko (dense, drop-in for pgvector/Milvus) are safe; KURE-v2 is better but ColBERT-style. Hybrid retrieval should use Kiwi-tokenised BM25 to neutralise josa. For rerankers, bge-reranker-v2-m3 (or its -ko fine-tune) is the pragmatic default; jina-reranker-v3.5 scores higher but is commercial-licensed (check license).
- SDS KoPub VDR is the closest thing to a benchmark for "find the right page of a Korean public document," which is the core retrieval task of a visual-grounding product; its finding that cross-modal queries show "substantial gaps" suggests page-image retrievers (ColPali-style) plus text retrievers should both be evaluated.

### Gaps
- No independent head-to-head of Upstage Document Parse vs CLOVA OCR vs Docling/MinerU/Marker/PaddleOCR on Korean PDFs was found; the only cross-tool data is 2023 OCR-API testing and non-Korean 2025–2026 Docling/MinerU comparisons.
- Could not read the SDS KoPub VDR result tables (PDF not parseable via fetch); specific model scores remain unknown.
- Could not find a published fidelity evaluation of H2Orestart HWP→PDF conversion (only anecdotal successes and font issues).
- Current (2026) CLOVA OCR and Upstage pricing per page not verified.
- No published study on mixed Hanja/English chunking in Korean RAG; only josa/spacing issues were documented.

---

## Key Question 3: What Korean enterprise users complain about with internal AI chatbots (why they do / do not trust answers)

### Takeaway
Direct community posts (Blind/Clien/DC) could not be retrieved via search (Blind requires login and is not indexed), but survey and practitioner evidence consistently points to three complaints: irrelevant answers (동문서답), factually wrong answers, and missing sources/evidence — with "inaccurate information and low result reliability" the top enterprise concern at 61.3% in the 2025 Megazone/Foundry survey. Practitioner post-mortems show wrong-document retrieval (B-error fix presented as A-error fix), chunking that separates definitions from fixes, and citation logic that attaches sources even when no evidence exists, which is precisely why users re-open the original document.

### Cited Findings
- Megazone Cloud × Foundry survey of 749 Korean AI/IT managers (published 2025-08-26): 55.7% already use generative AI (22.4% company-wide, 33.2% some departments); top concerns: 잘못된 정보 생성 및 결과 신뢰도 부족 61.3%, 보안·개인정보 유출 53.3%, 저작권/법적 책임 21.0%, ROI 불확실 19.8%; top uses: 문서 요약·보고서 작성 43.1%, 데이터 분석 40.3%, 프로그래밍 보조 37.0%; 79.3% expect 2026 budgets to rise; "85% by 2026" is a forecast, not measured. — [“2026년 국내 기업 85%가 생성형 AI 도입…10곳 중 8곳 예산 확대” (CIO Korea)](https://www.cio.com/article/4045735/2026%EB%85%84-%EA%B5%AD%EB%82%B4-%EA%B8%B0%EC%97%85-85%EA%B0%80-%EC%83%9D%EC%84%B1%ED%98%95-ai-%EB%8F%84%EC%9E%8510%EA%B3%B3-%EC%A4%91-8%EA%B3%B3-%EC%98%88%EC%82%B0-%ED%99%95.html)
- 나우앤서베이 (NownSurvey), 800 Korean office workers, fielded 2023-06-02~07 (older but the only Korean survey found that isolates "no sources" as a complaint): top problems with generative AI: 동문서답 24.4%, 사실과 다른 답변 22.1%, 출처·근거를 제공하지 않음 16.4%; article concludes trust in responses is low and reliability is the key to satisfaction. — [대한민국 직장인 생성형 AI 활용 현황 (나우앤서베이)](https://www.nownsurvey.com/board/hotissue/view/wr_id/171/ptype/all/stx/)
- Practitioner post-mortem (노머스 backend dev 지승보, 2025-11-24, "사내 AI 챗봇. 쉽게 보고 도전했다가 박살났습니다"): generic query "A 에러 해결 방법" retrieved B-error docs and the LLM presented B's fix as A's; vector search over-weighted generic words like "에러"; paragraph chunking separated fixes from the error definitions; Gemini 2.5 Flash Lite ignored verbatim-quote instructions so they upgraded models; answers cite sources in Slack `<url|Title>` link format (L1); lesson: "문서는 작성도 중요하지만, 검색도 똑같이 중요하다는 사실을요." — [사내 AI 챗봇. 쉽게 보고 도전했다가 박살났습니다 (Wonderwall Tech)](https://tech.wonderwall.kr/articles/birthofknowbis/)
- Open-source Korean RAG chatbot issue (2026): sources were attached even when no evidence was retrieved, because "자료 없음" logic keyed on citation presence instead of actual grounding; expected behaviour: zero citations → noSource:true → show "자료 없음". — [자료를 못 찾은 답변에 출처가 붙어 「자료 없음」이 뜨지 않음 · Issue #181](https://github.com/changs0124/rag-chatbot/issues/181)
- Korean internal-regulation RAG projects converge on: show document name/article/annex per answer and refuse when no evidence; one opens the original PDF in the browser viewer at `#page=` (L3) so users can verify the cited chunk. — [beomjunlee0427/rag-assistant](https://github.com/beomjunlee0427/rag-assistant); [geumyeongchoi/docmind](https://github.com/geumyeongchoi/docmind)
- Textnet build log: answers that are not well formatted are perceived as an unfinished product; they added table-output validation. — [사내 지식관리도 AI로? 텍스트넷의 RAG 챗봇 구축기](https://article.textnet.kr/rag-chatbot-development-guide)
- Open-source experiment: TopK 3 missed relevant chunks, TopK 10 confused the LLM; TopK 5 chosen — accuracy/latency trade-off. — [leeheejae95/doc-chatbot](https://github.com/leeheejae95/doc-chatbot)
- 2025-11-18 전자신문 (reprinting a Politecnico di Torino study): document-QA models detect unanswerable questions at only 7%–60% document-level accuracy (best Qwen2.5-VL-72B 59.9%/58.1%); "이는 AI가 틀린 정보를 마치 맞는 것처럼 자신 있게 말할 위험이 크다는 뜻이다"; performance drops for documents over 8 pages; telling the model an answer may not exist and supplying document text helps. — ["챗GPT도 속았다"... AI에게 '답 없는 질문'하자 60% 이상 틀려 (전자신문)](https://www.etnews.com/20251118000248)
- ZDNet 2025-11-24: SNU researchers found ChatGPT tends to answer "아니요" when it does not know (bias in unknown cases). — [AI는 답 모르면 무조건 "아니요" (ZDNet Korea)](https://zdnet.co.kr/view/?no=20251124145624)
- 57% of Korean workers have hidden AI use or presented AI output as their own; only 40% say their company has an AI-use policy. — ["챗GPT 썼다고 말하면 오히려 손해" (아시아경제 via Daum)](https://v.daum.net/v/zyIB2pXSc1)
- LG U+ business insight: many employees use personally subscribed accounts, so companies cannot manage usage (motivation for official internal chatbots). — [ChatGPT 유료 구독, 우리 회사는 몇 명에게 먼저 도입해야 할까? (LG U+)](https://www.lguplus.com/biz/insight/story/903)
- Vendor blogs on failure causes (secondary, marketing-adjacent): "답변이 그럴듯하지만 근거(출처) 제시가 약함" as a practical barrier; chatbots with "실무에 도움이 되는 답변이 어설픔" get abandoned. — [AI 챗봇, 실제로 효과가 있나요? (리부트)](https://www.reboott.ai/blog/ai-chatbot-enterprise-use-cases); [우리 회사 AI 도입, 왜 실패했을까? (터치클래스)](https://blog.touchclass.com/archives/8684); [AI 도입, 왜 실패했을까? (달파)](https://dalpha.so/blog/ai-implementation-failure/)
- Allganize's own rationale (2024-10-17): citations alone are insufficient on long pages, especially finance/law. — [출처 하이라이트가 필요한 이유](https://www.allganize.ai/ko/blog-posts-ko/genai-hallucination-highlight)
- kt cloud cites a comparison (source unclear) of RAG factual accuracy 92%/hallucination 8% vs LLM-only 78%/22% — treat as unverified. — [kt cloud RAG #1 (2025-08)](https://tech.ktcloud.com/entry/2025-08-ktcloud-ai-rag-%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B5%AC%EC%A1%B0-%EC%9D%B4%ED%95%B4)

### Inferences
- The complaint pattern ("동문서답" + wrong facts + no evidence) maps directly onto the user's observation that employees re-open source documents; the two documented failure modes that highlight-level grounding would expose immediately are (a) the right document but wrong passage (B-error fix for A-error) and (b) citations attached with no real evidence. An L4 UI makes both visibly wrong in seconds instead of requiring a document re-read.
- Speed complaints are documented only generically (retrieval+generation latency, TopK trade-offs); no Korean source quantified acceptable latency. The user's internal-platform speed complaint is plausible but not externally corroborated with numbers.

### Gaps
- No Blind, Clien, or DC Inside posts about 사내 GPT/RAG were retrievable (Blind not indexed; search returned none). Direct employee quotes are missing.
- No Korean survey found that specifically measures internal (company-built) chatbot satisfaction or usage rates; all figures are about generative AI in general.
- The 나우앤서베이 "no sources" statistic is from 2023; no 2025–2026 Korean survey isolating the citation/evidence complaint was found.

---

## Key Question 4: Market context — Korean enterprise generative-AI adoption 2025–2026, AI 기본법 implementation, regulatory considerations for personal document chatbots

### Takeaway
Measured Korean enterprise generative-AI use was 55.7% in a 749-respondent 2025 survey (85% for 2026 is a projection); official 통계청-based AI adoption was only 6.4% of firms in 2023 (26% in ICT, 4% in manufacturing), so figures vary enormously by definition. The AI 기본법 (Framework Act on AI) and its enforcement decree took effect 2026-01-22 with a ≥1-year grace period on fines; obligations most relevant to a chatbot product are the Article 31 generative-AI notice/labeling duties (met by visible notice; invisible watermark allowed for non-deepfake outputs) and, for data handling, the PIPC's August 2025 generative-AI personal-data guidance (no reuse of user inputs beyond purpose, retention limits, PIA before launch).

### Cited Findings

**Adoption statistics**
- Megazone/Foundry 2025 survey (749 managers): 55.7% using generative AI; 74.0% increased investment YoY; 79.3% expect 2026 budget growth; >85% by 2026 is a projection based on current + implementing/planning firms. — [CIO Korea 2025-08-26](https://www.cio.com/article/4045735/2026%EB%85%84-%EA%B5%AD%EB%82%B4-%EA%B8%B0%EC%97%85-85%EA%B0%80-%EC%83%9D%EC%84%B1%ED%98%95-ai-%EB%8F%84%EC%9E%8510%EA%B3%B3-%EC%A4%91-8%EA%B3%B3-%EC%98%88%EC%82%B0-%ED%99%95.html)
- CIO Korea 2026 IT outlook survey: 60.3% rate generative AI "우선순위가 높다"; 74.0% increased related investment; firms using AI company-wide reach maturity stage 3+ at 56.4% vs 31.6% for department-only use; only 2.4% have no generative-AI plans. — [“생성형 AI가 IT 전략을 바꾼다” 2026 IT 전망 조사 결과 (CIO Korea)](https://www.cio.com/article/4111617/%EC%83%9D%EC%84%B1%ED%98%95-ai%EA%B0%80-it-%EC%A0%84%EB%9E%B5%EC%9D%84-%EB%B0%94%EA%BE%BC%EB%8B%A4-2026-it-%EC%A0%84%EB%A7%9D-%EC%A1%B0%EC%82%AC-%EA%B2%B0%EA%B3%BC.html)
- 대한상의 SGI analysis of 통계청 기업활동조사 panel 2017–2023 (reported 2025-06-08): AI adoption 2.8% (2018) → 6.4% (2023); ICT ~26%, education 15.7%, finance/insurance 15.5%, manufacturing ~4%; adopters show +4% sales, +7.6% value-added but no statistically significant productivity gain. — ["AI 도입한 기업, 매출 4%·부가가치 7.6% 높였다" (한국일보)](https://www.hankookilbo.com/News/Read/A2025060811190005720); [KDI 경제정보센터 요약](https://eiec.kdi.re.kr/policy/domesticView.do?ac=0000195535)
- KISDI Perspectives (Nov 2025, No.2) "국내 기업의 인공지능(AI) 도입 및 특징" exists but the PDF could not be parsed via fetch. — [KISDI report PDF](http://www.kisdi.re.kr/report/fileView.do?key=m2102058837181&arrMasterId=4334696&id=1880176)
- 과기정통부 2025 인터넷이용실태조사 (released 2026-03-31): 44.5% of Koreans have used generative AI (33.3% in 2024) — individuals, not firms. — [과기부, 2025 인터넷 이용 실태 조사 결과 발표 (뉴스핌)](https://www.newspim.com/news/view/20260331000211)
- 한국콘텐츠진흥원: content-industry generative AI utilisation 20.0% in H1 2025. — [2025년 콘텐츠산업 생성형 AI 활용률 20% 돌파 (플래텀)](https://platum.kr/archives/271989)
- AI Index 2026 (as cited by a Korean blog): Korean generative-AI usage rose from 25.9% (H1 2025) to 30.7% (H2 2025), the largest increase globally — population-level, secondary citation. — [2025년 기업 AI 활용 현황과 전망 (DFINITE blog)](https://blog.dfinite.ai/enterprise-ai-adoption-outlook)
- 2026-10-08 ZDNet: 하정우 (AI 수석/부위원장) said the three "독자 AI 파운데이션 모델" companies reached global level; audience interest centred on practical use. — [하정우 부위원장 "독파모 3사, 글로벌 수준 도달" (ZDNet Korea)](https://zdnet.co.kr/view/?no=20261008140113)

**AI 기본법 (인공지능 발전과 신뢰 기반 조성 등에 관한 기본법)**
- Act and enforcement decree in force since 2026-01-22; decree pre-announced 2025-11-12, approved by Cabinet 2026-01-20; MSIT runs a grace period of "최소 1년 이상" and will minimise on-site inspections except for serious harm cases; generative-AI outputs must be labelled (visible or invisible watermark allowed for non-deepfake outputs such as animation/webtoon; deepfake-type outputs need clearly perceivable notice). — ['인공지능기본법' 22일 시행…생성형 AI 결과물 '워터마크' 표시 의무 (정책브리핑, korea.kr)](https://www.korea.kr/news/policyNewsView.do?newsId=148958380); [“AI로 생성된 결과물” 고지해야…AI기본법 시행령 입법예고 (korea.kr)](https://www.korea.kr/news/policyNewsView.do?newsId=148954629)
- Article 31: providers of high-impact or generative-AI products/services must notify users in advance; decree allows diverse notification methods; MSIT published 「인공지능 투명성 확보 안내 지침」 on 2026-01-22; obligated party is the business that directly provides the AI service to users; obligations exist from 2026-01-22 even though fines are deferred. — [AI 기본법 시행과 그 시사점 (법률신문/세종)](https://www.lawtimes.co.kr/news/articleView.html?idxno=216500); [AI기본법 시행 6개월 — 계도기간이 끝나기 전에 (datalaw.kr)](https://datalaw.kr/posts/ai-basic-law-business-duties/)
- Provisions requiring sub-regulations (e.g., public-sector AI adoption promotion) were deferred to 2026-07-21; further decree amendments pre-announced. — [과기정통부, AI기본법 시행령 개정안 입법예고 (법률신문)](https://www.lawtimes.co.kr/news/articleView.html?idxno=221148); [ZDNet 2025-12-24 시행령 입법예고](https://zdnet.co.kr/view/?no=20251224132637)
- Foreign operators must designate a domestic representative; failure carries fines up to ₩30M (blog summary, verify in statute). — [AI 기본법 완전 정리 (피카부랩스)](https://peekaboolabs.ai/blog/ai-basic-law-guide)
- Government "인공지능 제품·서비스 확인제" certification gives public-procurement preference (42Maru obtained it 2026-09). — [포티투마루, AI 확인제 인증 획득…공공조달 우대 (Nate)](https://m.news.nate.com/view/20260916n20034)

**Personal-data / security guidance relevant to a document chatbot**
- 개인정보보호위원회 published 「생성형 인공지능(AI) 개발·활용을 위한 개인정보 처리 안내서」 on 2025-08-06: four lifecycle stages (목적 설정 → 전략 수립 → 학습·개발 → 시스템 적용·관리); operation-stage duties include no use of user inputs beyond stated purpose, retention-period compliance, safety measures; special attention to sensitive data in conversational Q&A logs; CPO-centred governance and PIA before launch; checks on data transfer when using external APIs/commercial models. — [PIPC 보도자료](https://www.pipc.go.kr/np/cop/bbs/selectBoardArticle.do?bbsId=BS074&mCode=C020010000&nttId=11410); [개인정보 포털 안내서](https://www.privacy.go.kr/front/bbs/bbsView.do?bbsNo=BBSMSTR_000000000049&bbscttNo=20836); [Kim & Chang 요약](https://www.kimchang.com/ko/insights/detail.kc?sch_section=4&idx=32696)
- KISA generative-AI security guide (2025 edition) summarised by a vendor blog (secondary). — [KISA 생성형 AI 보안 가이드 완벽 정리 (피카부랩스)](https://peekaboolabs.ai/blog/kisa-ai-security-guide)
- Public-sector demand signals: Naver Works features for 행정망 (Aug 2026); 42Maru positioning public/defense AI "내부망에서"; Polaris blog citing ~90% of public workers producing HWP/image/scanned PDF. — [SEN TV 2026-08-12](https://www.sentv.co.kr/article/view/sentv202608120101); [벤처스퀘어 포티투마루](https://www.venturesquare.net/1117996); [Polaris Office blog 2025-12-29](https://www.polarisoffice.com/business-blog/hwp-ai)

### Inferences
- A personal document chatbot is unlikely to be "고영향 AI" under the Act, so the practical compliance items are: a visible "AI 기반 서비스" notice at onboarding, labelling of generated text outputs, a privacy policy that forbids reuse of uploaded documents/queries for training, retention controls, and (if sold to companies) a PIA checklist. Showing the original page with highlight is also a transparency/explainability positive under the Act's spirit, though not a legal requirement.
- The large gap between "firms using generative AI" (55.7%) and "firms that formally adopted AI" (6.4%, 2023 official) indicates that most usage is individual/bottom-up — which is the user segment for a personal chatbot platform.

### Gaps
- Could not retrieve official 2024/2025 통계청 기업활동조사 AI-adoption figures or the KISDI Nov 2025 report body (PDF unreadable via fetch).
- No Korean source explicitly analyses whether a personal document-Q&A tool falls under any 고영향 AI category; the inference above is my reading of secondary summaries, not legal advice.
- Exact fine amounts and article numbers were taken from law-firm/blog summaries; verify against 국가법령정보센터.
