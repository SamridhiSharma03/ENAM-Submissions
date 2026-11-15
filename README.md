[README.md](https://github.com/user-attachments/files/34880885/README.2.md)
# FINN: a briefing that leads into analysis

Mobile-first prototype for the ENAM AI Engineer build challenge.

**Live demo:** [Open FINN Demo](https://claude.ai/artifact/5QiDpn9E5J52QNWtqQhRs)
Open it on a phone. It is one static file (`index.html`), so there is nothing to install: double-click it to run locally.

## The idea

An investor does not need another feed plus a separate chatbot. FINN is a ranked briefing where every card has an **Interrogate** button, so push (what changed) flows straight into pull (why it matters, what management said before).

- **Briefing (push):** announcements ranked by priority, with routine filings collapsed. The window (24h, 3 days, 7 days, 30 days) is user-selectable. Results cards show Revenue, EBITDA, PBT and PAT, QoQ and YoY.
- **Quality of the beat:** the Titan card does not just say "PAT +114%". It flags that ₹671 cr of PBT is a non-cash Ind-AS credit, and shows PBT without it.
- **Trust (pull):** a management scorecard for Infosys comparing what was promised on earnings calls with what was delivered. Every row cites a document and PDF page.
- **Ask:** search by company or sector, plus a chat that answers only from the loaded documents. If the answer is not in them, it says so.
- **Alerts:** user-set category filters and volume / price thresholds.

## How the data was built

| Step | What | Tool |
|---|---|---|
| Sources | 5 Infosys earnings-call and press-conference transcripts, FY26 annual report, Screener quarterly export, Titan Q2 FY27 results filing and call transcript | Provided filings only (primary data) |
| Extraction | Guidance, outcomes, and results tables read from the documents | LLM-assisted, then checked by hand against the source text |
| Validation | Titan: revenue components sum to Total Income in all three quarterly columns. Infosys: margin recomputed as (operating profit − depreciation) / sales, which matches the reported ~21% | Plain arithmetic, no LLM |
| Growth maths | QoQ / YoY computed in code, not by a model | JavaScript |
| App | Single static HTML file, no build step, no network calls | Vanilla JS |

Cost to run the demo: zero. All model work was done ahead of time and stored as data, so the demo needs no API key.

## Decisions and trade-offs

- **Precompute, don't call a model live.** A demo that does not run is the worst outcome. Extraction happens once; the app only reads results.
- **Primary sources only on the pull side.** No news, no broker notes, no model memory. The chat refuses when a fact is not in the documents.
- **Currency caveat made explicit.** Infosys guides in constant currency; rupee revenue grew 10.7% YoY but constant-currency growth was 1.5%. The card says so instead of showing the flattering number.
- **Judgment over volume.** Six items on the briefing, ranked, instead of dozens of unranked ones.

## What is not built (honest list)

- **No live price / volume feed.** Thresholds are saved but do not fire.
- **No live exchange announcement feed.** The briefing holds six real items taken from the supplied filings, not a 15–20 stock portfolio.
- **"Any NSE ticker" is not wired.** Only Infosys (promised vs delivered) and Titan (results) are loaded.
- **Chat is retrieval over precomputed claims,** not a live LLM.
- No management-change bio enrichment.

## Next steps

1. Fetch pipeline: ticker → Screener page → annual reports, transcripts and results PDFs, cached by file hash.
2. Cheap model for bulk extraction (announcement classification, results tables) with arithmetic validation; stronger model only for promised-vs-delivered judgment.
3. End-of-day prices via yfinance, with the threshold alerts firing for real.
4. BSE announcements feed for the full portfolio, with web-push notifications.
5. Management-change cards with a short bio of the incoming person (web search, source labelled).
6. Live LLM chat restricted to retrieved passages, with citations.
