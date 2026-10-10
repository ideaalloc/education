# Business Model Landscape: Autonomous Socratic Tutoring

*Research report for the `education` idea repo (Socratic tutor + curriculum architect prose contracts in `socratic_tutoring/`). All web sources fetched live via curl on **2026-10-10** unless marked "could not verify live". Search engines (DuckDuckGo, Bing, Mojeek, Qwant, SearXNG instances) were bot-blocked from this environment, so discovery leaned on Wikipedia, official pricing pages, and scholarly APIs (arXiv, OpenAlex, Crossref). Where a claim could not be fetched from a primary or stable source, it is explicitly flagged.*

---

## Executive summary

1. **The market is real and price-anchored low.** Khanmigo sells a GPT-4-derived Socratic tutor at **$4/month ($44/year)** to consumers, with a separate custom-quoted district tier; Synthesis Tutor sells a premium guided-math tutor at **$29–45/month per child** ($300–999 annual/lifetime), proving a 7–10x higher price point is viable for a differentiated, engagement-driven product. Duolingo monetizes AI features inside a freemium funnel whose paid conversion was ~**6.8% of MAU** (2022) and whose revenue passed **$1B** (2025).
2. **Inference cost is no longer the constraint.** At current API prices (Claude Haiku 5.5 at $0.10/$0.50 per MTok, gpt-6-luna at $0.10/$0.50, Gemini 2.5 Flash at $0.30/$2.50), a 25-turn Socratic session costs roughly **half a cent to a few cents**, i.e. a heavy daily user costs **$0.15–$3/month** in COGS against $4–45 subscriptions. Gross margins of 80–95% are structurally available; the binding costs are distribution, curriculum content, and trust/compliance.
3. **Efficacy evidence now favors guardrails — exactly this repo's bet.** The Bastani et al. field experiment (~1,000 high-school math students, PNAS 2025) found raw GPT-4 access *harmed* subsequent exam performance (−17%) while a **prompt-guarded tutor variant mitigated the harm** and boosted practice performance (+127%). A 2026 adversarial-stress literature (Zhao/Knežević/Käser; Shah et al.) shows tutors leak answers under persistent student pressure — the "no-leak invariant" is a measurable, benchmarkable property, not a marketing adjective.
4. **Nobody sells determinism yet.** No incumbent advertises cross-session consistency or a verified leakage boundary. That is a wedge for B2B2G (districts, publishers) and assessment-adjacent products where auditability beats charm — but it is a feature, not a company. The moat candidates are (a) the curriculum-node corpus the architect generates, (b) leakage-robustness benchmarks as a trust artifact, and (c) compliance-ready determinism logs.
5. **Regulation raises the floor for serious players.** COPPA (US, under-13, verifiable parental consent), FERPA (school records), GDPR Art. 8 (EU, consent age 13–16), UNESCO 2024 GenAI-in-education guidance, and EU AI Act Art. 4 (AI literacy) all favor vendors that can *document* behavior — which a state-machine tutor with enumerated budgets and transitions does naturally and a freeform chatbot does not.

---

## 1. Competitive landscape

| Product | Price (fetched 2026-10-10) | Segment | How genuinely Socratic? | Source |
|---|---|---|---|---|
| **Khanmigo** (Khan Academy) | **$4/mo or $44/yr** consumer; "Khanmigo for Districts" = custom quote (no public price; `/districts` page 404s) | B2C + B2B/B2G districts | Yes by design — launched Mar 2023 on GPT-4 with "never gives the answer" prompting; pilot reached ~65,000 students in 53 districts. Prompt-level guardrails only; no published state machine or leakage benchmark | khanmigo.ai/pricing; Wikipedia (Khanmigo) |
| **Synthesis Tutor** | Individual child: **$45/mo** monthly (sale $35), **$29/mo** billed annually ($348/yr; sale $25/mo = $300/yr), **$999 lifetime**; Family (multi-child): **$70/mo**, **$400/yr**, **$1,499 lifetime**; 7-day free trial; schools = "contact us" | B2C premium (ages 5–11 math); B2B nascent | Guided, discovery-based ("doesn't just teach — builds deep understanding"); notably **hybrid**: "leverages AI where appropriate while relying on expert educators and neuroscientists" — not pure LLM Socratic | synthesis.com/tutor |
| **Duolingo Max** | Tier above Super Duolingo; **exact current price not verifiable live** (duolingo.com/plus is JS-only; launch coverage $29.99/mo could not be re-fetched) | B2C freemium upsell | **No** — Roleplay/Video Call are conversation practice; "Explain My Answer" explains, it doesn't withhold. AI features are engagement, not Socratic discipline | blog.duolingo.com/duolingo-max/; Wikipedia (Duolingo) |
| **Squirrel AI** (Yixue, China) | No public per-seat price found; franchise learning-center model | B2C via 2,000+ learning centers in 200 cities (2019); 60,000 public schools served (2021) | **No** — adaptive knowledge-graph drilling (10,000+ "knowledge points" vs ALEKS's 1,000); system picks next item, does not question the student to insight. Raised $180M+, >$1B valuation (2018) | Wikipedia (Squirrel AI) |
| **Carnegie Learning MATHia** | **No public pricing** (quote-based B2B; product page carries no prices) | B2G/B2B districts, grades 6–12 | Partially — cognitive-tutor lineage (CMU); "contextual hints" and skill-by-skill adaptation, but step-based hinting within a fixed curriculum, not open-ended Socratic dialogue | carnegielearning.com/solutions/math/mathia/; Wikipedia (Carnegie Learning: sold to Apollo Group for $75M in 2011, PE-owned since 2018) |
| **Photomath** (Google) | Free + Photomath Plus subscription (price not on fetched pages) | B2C | **Anti-Socratic** — camera-scan gives step-by-step *solutions*. Google acquired it (announced 2022, closed June 2023) explicitly as a ChatGPT response. This is the "answer machine" the tutoring market must differentiate against | Wikipedia (Photomath) |
| **Quizlet (Q-Chat et al.)** | Freemium; Quizlet Plus subscription (price not fetched) | B2C, 60M+ MAU (2021) | Mostly not — Q-Chat (Mar 2023, OpenAI API) is a study-partner chatbot; Mar 2026 ChatGPT integration generates study materials from notes | Wikipedia (Quizlet) |
| **OpenAI ChatGPT** (Plus/Pro/Edu; study features) | Pro **$200/mo** verified; Plus **$20/mo widely reported but primary page 403s — could not verify live**; Edu = custom, unverified | B2C + B2B (Edu) | Default ChatGPT gives answers. 20M paid subscribers by Apr 2025; $12B annualized revenue Jul 2025 → $20B annualized Jan 2026. Any "study mode" is a thin guardrail over an answer machine | Wikipedia (OpenAI); platform.openai.com/docs/pricing |
| **Pearson / McGraw-Hill AI features** | **Not researched — no fetchable page found without search engines.** Marked as gap, not absence | B2G/B2B incumbents | Unknown | — |

**Landscape read:** three clusters — (1) *answer machines* (Photomath, default ChatGPT) that students love and learning-science evidence indicts; (2) *adaptive drill systems* (Squirrel AI, MATHia) that personalize sequencing but don't dialogue; (3) *genuine guided-questioning tutors* (Khanmigo, Synthesis) — the only cluster with a stated pedagogy of not giving answers, and the only cluster where this repo's determinism claim is even legible as a differentiator.

## 2. Business models observed

| Model | Example | Price points | Notes |
|---|---|---|---|
| Low-cost B2C subscription (nonprofit-subsidized) | Khanmigo | $4/mo, $44/yr | Priced at cost-recovery; funded partly by philanthropy/Microsoft partnership (free teacher tier, Aug 2024). Hard to compete on price; easy to compete on trust. |
| Premium B2C family subscription | Synthesis Tutor | $300–999/yr/child; family $400–1,499 | Sold on outcomes + child's love of math; strong neurodiverse-family positioning. Demonstrates real willingness to pay ~$25–45/mo when the product is emotionally compelling. |
| Freemium → AI-tier upsell | Duolingo (Max), Quizlet (Plus) | Max tier price unverifiable here | AI is a retention/conversion lever inside a huge funnel (Duolingo revenue $531M 2023 → $1.04B 2025; ~6.8% paid conversion 2022). |
| District per-seat licensing (B2G) | Khanmigo for Districts, MATHia | Custom/quote; **the oft-cited ~$35/student/yr Khanmigo district figure could not be verified live** | Procurement cycles are slow but contracts are large and sticky; pilot → district-wide is the observed path (Khanmigo pilot: 53 districts / 65k students). |
| Learning centers / franchise | Squirrel AI | No public price | Capital-heavy; China-specific; 1M+ registered students (2019). |
| Platform/API & answer-machines | Google (Photomath), OpenAI (ChatGPT Edu, API) | API: per-token (see §3) | The infrastructure layer everyone else rents. Also the competitive threat: ChatGPT study modes are "good enough" Socratic-adjacent for most consumers. |

**Willingness to pay / churn:** hard consumer-edtech churn benchmarks could not be fetched (search blocked). Verifiable anchors instead: Duolingo's 6.8% paid conversion and $1B revenue show freemium works at scale but converts single digits; Synthesis's $999 lifetime SKU shows a segment pays upfront to avoid churn; Khanmigo's $4 price suggests the nonprofit benchmark deliberately suppresses the consumer ceiling. Treat any churn number in pitch decks as unverified until sourced.

## 3. Unit economics (fetched API prices, 2026-10-10)

**Model prices per 1M tokens (input/output), fetched from official pages:**

| Model | Input | Output | Cached input | Source |
|---|---|---|---|---|
| Claude Haiku 5.5 | $0.10 (≤100k prompt) / $0.50 | $0.50 (≤100k) / $2.50 | $0.01 | docs.claude.com pricing |
| Claude Sonnet 5.5 | $2 | $10 | $0.10 | docs.claude.com |
| Claude Opus 5.5 | $4 | $20 | $0.20 | docs.claude.com |
| gpt-6-luna | $0.10 | $0.50 | $0.01 | platform.openai.com/docs/pricing |
| gpt-6.1-sol | $2.00 | $10.00 | $0.10 | platform.openai.com |
| gpt-6-astra | $10.00 | $50.00 | $1.00 | platform.openai.com |
| Gemini 2.5 Flash | $0.30 | $2.50 (incl. thinking) | $0.03 | ai.google.dev pricing |
| Gemini 3.1 Pro Preview | $2.00 (≤200k) | $12.00 | $0.20 | ai.google.dev |
| Gemini 3.8 Flash | $0.75 promo → $1.50 (2027) | $3.75 promo → $7.50 | $0.075 promo | ai.google.dev |

**Session cost model** (assumptions stated; these are estimates, not measured): a Socratic session per the repo's state machine ≈ 25 student/tutor turns, average ~3k input tokens/turn (system prompt + node JSON + transcript, mostly cacheable), ~180 output tokens/turn → ~75k input (70% cache-hit) + 4.5k output per session:

| Model tier | Cost/session | Heavy user (1 session/day, 30/mo) | vs. Khanmigo $4/mo | vs. Synthesis $29/mo |
|---|---|---|---|---|
| Budget (Haiku 5.5 / gpt-6-luna) | ~$0.005 | **~$0.15** | 96% gross margin | 99.5% |
| Mid (Sonnet 5.5 / Gemini 3.1 Pro) | ~$0.09–0.10 | **~$2.7–3.0** | ~30% margin — *tight* | ~90% |
| Frontier (Opus 5.5 / gpt-6-astra) | ~$0.18–0.21 | ~$5.5–6.5 | negative | ~80% |

**Implications:**
- A $4/mo Khanmigo-style price only works on budget-tier models or with philanthropic subsidy. A $29–45/mo Synthesis-style price funds frontier-model routing (strong model for misconception diagnosis, cheap model for routine turns).
- The repo's design is *cost-friendly*: one question per turn, short scannable outputs, enumerated budgets (2 hints, 2 counterfactuals, 1 breakdown, 3 off-path returns) bound worst-case tokens per problem. A determinism-first state machine also enables aggressive prompt caching (fixed system prompt + node JSON).
- **Human tutor comparison:** Preply's math marketplace lists lessons averaging **$17/hour** (from $3). An AI session at $0.005–0.10 is 100–3,000x cheaper per hour-equivalent; even at $3/mo COGS, an unlimited-use subscription undercuts one hour of human tutoring.

## 4. Efficacy evidence

| Evidence | Finding | Verified source (fetched 2026-10-10) |
|---|---|---|
| **Bloom's 2-sigma (1984)** | 1:1 mastery tutoring put the average tutored student above 98% of classroom controls; the open problem is matching it with scalable methods | Wikipedia (Bloom's 2 sigma problem); Bloom 1984, doi:10.3102/0013189x013006004 |
| **VanLehn 2011 meta-review** | Reality check: human tutoring effect is ~d=0.7, and step/substep-based ITS come close — *granularity of interaction*, not humanity, drives effectiveness | doi:10.1080/00461520.2011.611369 (2,260 citations, via OpenAlex) |
| **Bastani et al., PNAS 2025** ("Generative AI without guardrails can harm learning") | ~1,000 HS math students; GPT-4 access improved practice grades (+48% base, +127% guarded tutor) but **unguarded access cut subsequent exam grades −17%**; "the safeguards in GPT Tutor largely mitigate the negative learning effects" | doi:10.1073/pnas.2422633122; working paper SSRN 4895486 (2024) |
| **Abu-Nada et al. 2026** (foundation physics, GPT tutor vs textbook) | AI-tutor class scored higher on quizzes *and* showed better metacognitive calibration (confidence matched accuracy) | doi:10.33828/sei.v37.i2.6. *Note: the widely-cited Harvard/Klales arXiv preprint version (arXiv:2502.12150) resolves to a different paper in this environment — could not verify the preprint live; the published journal version was verified instead.* |
| **Answer leakage under attack** — Zhao, Knežević & Käser 2026 | Benchmarks LLM tutors (incl. pedagogically-aligned and multi-agent designs) against six groups of adversarial/persuasive student attacks; default "helpfulness" conflicts with pedagogy | arxiv.org/abs/2604.18660 |
| **Answer leakage benchmark** — Shah et al. 2026 (ACM) | AiTutor + Harness: dual-agent supervisor screens tutor drafts; scores leakage, Socratic compliance, hallucination under an escalating simulated attacker | doi:10.1145/3828792.3828794 |
| Socratic-tutor training line (found via OpenAlex; abstracts not fetched) | STAP (Socratic programming tutor, doi:10.1145/3775073.3775165); PEARL (RL for Socratic tutoring, arXiv:2605.29582); "Beyond Direct Answering" (heuristic RL, arXiv:2607.22996); ExplainRoute (pre-deployment audit of non-answer-giving tutors, arXiv:2609.03470) | OpenAlex search results, 2026-10-10 |
| **Klampfl & Neppert, "Guarding GPT"** | **Could not verify live.** Not found via arXiv API, OpenAlex, or Crossref in this environment; no ID or claim should be cited from it here. The leakage literature above covers the same ground with fetched sources. | — |

**Read:** the 2024–2026 evidence arc moved from "AI tutors help" to "**unguarded AI tutors harm; guardrails restore the benefit**," and then to "guardrails leak under adversarial pressure." That is precisely the problem the repo's two-list leakage boundary, counted budgets, and signal-based classification address — and the emerging benchmark tooling (AiTutor Harness, ExplainRoute) means leakage-robustness is becoming an auditable, publishable property a product can be certified against.

## 5. Regulatory and market constraints

- **COPPA (US):** 15 U.S.C. ch. 91 — parental notice + **verifiable parental consent** for under-13 data collection; FTC enforcement. (Statute text fetched via law.cornell.edu; **ftc.gov itself blocked bots** from this environment.) Synthesis (ages 5–11) sits squarely inside COPPA; Khanmigo requires users to be 18+ for direct signup, pushing under-18 access through districts — a common compliance workaround.
- **FERPA (US):** 20 U.S.C. §1232g — school-record privacy; constrains what a district-deployed tutor may store (student work, mastery records — note the repo's open decision on **mastery persistence across sessions** is a FERPA-relevant design choice, not just a product one). ed.gov blocked bots; statute fetched via Cornell LII.
- **GDPR Art. 8 (EU):** information-society services offered directly to a child require parental consent below 16 (member states may lower to 13) + reasonable verification efforts (gdpr-info.eu/art-8-gdpr/, fetched).
- **AI-in-education policy:** UNESCO 2024 global guidance on GenAI in education (ethics, teacher training, data protection); **EU AI Act Art. 4** (AI-literacy obligations); US DoE reports on AIEd — all summarized in Wikipedia's "Artificial intelligence in education" (fetched). Primary pages (ed.gov, unesco.org) not fetched.
- **Procurement reality:** district sales run through pilots → cohort → district contracts (Khanmigo's 53-district pilot is the observable template); slow cycles, RFP requirements, and data-privacy agreements (Student Privacy Pledge-style) are the de facto barrier to entry — and to churn.

## 6. Strategic implications for this project

Grounded in the repo's actual design (state-machine.md, tutor.md, curriculum-architect.md):

1. **Sell the invariant, not the tutor.** The Bastani PNAS result is the pitch: unguarded AI harms learning, guarded AI doesn't. The repo is unusually *legible* as a guarded system — four observable classification signals, enumerated leakage two-lists, counted budgets with escalation. Most competitors (Khanmigo included, on public evidence) implement guardrails as prompt sentences; this design implements them as an executable contract. That difference is auditable, and auditability is what districts, regulators, and assessment publishers buy.
2. **Determinism is a B2B/B2G feature, not a B2C one.** Consumers buy outcomes and delight (Synthesis) or price (Khanmigo). Nobody at $4/mo cares that the same student turn classifies identically across runs. But a publisher embedding tutoring in an assessment product, a district accountable for equitable treatment, or a regulator-facing compliance story *does* care: cross-session consistency = reproducible transcripts = defensible behavior. The AGENTS.md framing ("that determinism, not answer quality, is the product") points at **white-label/API to publishers and edtech platforms**, and at **certification-style B2G sales**, rather than a consumer app war against a nonprofit ($4/mo) and a $1B-revenue freemium machine.
3. **The leakage benchmark is the moat candidate.** Zhao et al. and Shah et al. show the field converging on adversarial leakage testing. A vendor that *publishes* its leakage-robustness numbers (its own AiTutor-Harness-style evals against the tutor's boundary) converts the repo's invariant into marketing + compliance artifact that Khanmigo-scale incumbents — whose guardrails are prompt-level — cannot easily match without rebuilding on a state-machine contract. Escalate-with-record (`ESCALATE` naming the specific gap) is similarly audit-friendly.
4. **Pricing corridor.** COGS analysis (§3) says: budget-tier routing supports even a $4/mo consumer price with ~95% margin; a mid-tier model for `DEBUG_MISCONCEPTION`/classification and budget tier for routine turns lands a premium consumer or per-seat product at **$10–30/seat/mo (or ~$60–150/seat/yr)** with 85%+ margins — below Synthesis's premium but above Khanmigo's subsidized floor, justified by verifiable pedagogy. White-label per-API-call pricing (usage-based, e.g. $0.02–0.10/session cost-plus) is the natural publisher deal.
5. **Close the open decisions with business logic.** (a) *Escalation target*: human-reviewer handoff is not a gap — it's the premium B2G SKU ("AI tutor with certified human escalation"); (b) *mastery persistence*: cross-session student state is the FERPA-scoped data asset that makes multi-year district contracts sticky, but must be designed privacy-first; (c) *node schema enforcement* (`node.schema.json`): mechanically checkable nodes are the quality-control story content buyers will diligence.
6. **Risks.** ChatGPT study modes at "good enough" Socratic quality with 900M weekly users and distribution nobody can match; Khanmigo's price floor + nonprofit trust; determinism is only sellable if leakage-robustness actually survives adversarial students (the 2026 literature says naive guardrails don't) — the invariant must be *tested*, not just written.

## 7. Open questions / weak evidence

- **Duolingo Max current price** — could not verify (JS-only pages, no working search engine); launch-era $29.99/mo is from memory of 2023 coverage, unverified here.
- **Khanmigo district per-seat price** — the widely-cited ~$35/student/year figure could **not** be verified live; khanmigo.ai shows only "custom". Khan Academy blog slugs 404'd and Wayback was rate-limited (429).
- **ChatGPT Plus ($20/mo) and ChatGPT Edu terms** — openai.com consumer pages 403/JS-blocked; only Pro $200/mo (via Wikipedia/OpenAI) and API prices (platform.openai.com, fetched directly) are verified.
- **Klampfl & Neppert "Guarding GPT"** — not findable via arXiv API, OpenAlex, or Crossref from this environment; treated as unverifiable and replaced with fetched leakage literature (arXiv:2604.18660; doi:10.1145/3828792.3828794).
- **Harvard physics-tutor preprint (Klales et al.)** — arXiv:2502.12150 resolves to an unrelated paper here; verified the published journal version (Abu-Nada et al., doi:10.33828/sei.v37.i2.6) instead.
- **Pearson / McGraw-Hill AI tutoring features and pricing** — not researched; blocked by lack of working search.
- **Consumer edtech churn benchmarks, TAM/market-size numbers** — no fetchable primary source found; deliberately omitted rather than cited secondhand.
- **Session-cost model** — token assumptions (25 turns, 3k avg input, 180 output) are estimates; measure real transcripts before financial planning.
- **FTC/ed.gov/UNESCO primary pages** — blocked from this environment; relied on statute text (Cornell LII), gdpr-info.eu, and Wikipedia summaries.

## Appendix: verified source list (all fetched 2026-10-10)

- https://www.khanmigo.ai/pricing — $4/mo, $44/yr, Districts tier (custom)
- https://en.wikipedia.org/wiki/Khanmigo (→ Khan Academy) — launch, pilot scale, $4 access, teacher tier
- https://www.synthesis.com/tutor — full plan matrix, free trial, school contact, Ad Astra origin
- https://en.wikipedia.org/wiki/Squirrel_AI — centers, students, funding, knowledge-point granularity
- https://en.wikipedia.org/wiki/Duolingo — revenue, conversion, Max features, business model
- https://blog.duolingo.com/duolingo-max/ — Max launch features (OpenAI)
- https://en.wikipedia.org/wiki/Photomath — Google acquisition, Plus tier, solution-giving model
- https://en.wikipedia.org/wiki/Quizlet — MAU, freemium, Q-Chat, ChatGPT integration
- https://www.carnegielearning.com/solutions/math/mathia/ — product claims, no public price
- https://en.wikipedia.org/wiki/Carnegie_Learning — ownership history ($75M Apollo, 2011)
- https://platform.openai.com/docs/pricing — gpt-6-astra/6.1-sol/6-luna token prices
- https://docs.claude.com/en/docs/about-claude/pricing — Claude 5.5 family token prices
- https://ai.google.dev/gemini-api/docs/pricing — Gemini 2.5/3.x token prices
- https://en.wikipedia.org/wiki/OpenAI — Pro $200/mo, 20M paid subs, revenue trajectory
- https://preply.com/en/online/math-tutors — human tutoring avg $17/hr (from $3)
- https://en.wikipedia.org/wiki/Bloom%27s_2_sigma_problem — Bloom 1984 summary
- doi:10.1080/00461520.2011.611369 — VanLehn 2011 (OpenAlex metadata + abstract)
- doi:10.1073/pnas.2422633122 — Bastani et al., PNAS 2025 (OpenAlex abstract); SSRN 4895486 (2024 WP)
- doi:10.33828/sei.v37.i2.6 — Abu-Nada et al. 2026 physics AI-tutor study (OpenAlex abstract)
- https://arxiv.org/abs/2604.18660 — Zhao, Knežević, Käser, adversarial answer-leakage (arXiv API)
- doi:10.1145/3828792.3828794 — Shah et al., AiTutor leakage benchmark (OpenAlex abstract)
- https://www.law.cornell.edu/uscode/text/15/chapter-91 — COPPA statute
- https://www.law.cornell.edu/uscode/text/20/1232g — FERPA statute
- https://gdpr-info.eu/art-8-gdpr/ — GDPR Art. 8 (child consent, 16/13)
- https://en.wikipedia.org/wiki/Artificial_intelligence_in_education — UNESCO 2024 guidance, EU AI Act Art. 4, cheating/integrity survey data

*Blocked/failed fetches (documented, not invented): ftc.gov, ed.gov (bot-blocked), openai.com consumer pages (403/JS), duolingo.com/plus (JS shell), techcrunch.com article body (JS), theverge.com slug (404), khanmigo.ai/districts (404), web.archive.org (429 rate-limited), all general web search engines (bot-blocked).*
