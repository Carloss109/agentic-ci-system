# Agentic CI System — Enterprise AI Vendor Strategy
### UFV Corporate Intelligence 2025–2026 | Team 7

---

## Live URL

**🌐 https://[carloss109].github.io/agentic-ci-system/**

---

## Team

| Name | Role |
|------|------|
| Carlos Martín Oliveros | Lead Analyst & Developer (BA1) |

**Course:** Corporate Intelligence 2025–2026  
**Professor:** César Moreno Pascual Ph.D.  
**Universidad Francisco de Vitoria**  
**Date:** 25 April 2026

---

## Strategic Question

> Should a European Fortune 500 company commit to a single enterprise AI provider (OpenAI, Anthropic, or Google) or adopt a multi-vendor strategy to avoid lock-in and maximise strategic flexibility?

**Recommendation:** CONDITIONAL GO — Structured Multi-Vendor Strategy  
**Expected Value:** €8.41M (3-year NPV, 8% discount rate) vs. €5.99M for single-vendor  
**Confidence:** Medium-High (70%)

---

## AI Tools Used

| Tool | Used For |
|------|----------|
| Claude Sonnet 3.7 (Anthropic API) | Agent backbone: AMC scoring, scenario generation, ECOMO calculations, report drafting |
| Claude claude.ai | Code generation for Python agent, JavaScript workbench, document generation |
| GitHub Copilot | Boilerplate Python autocomplete, API wrappers, error handling |

All AI-generated code was manually reviewed and tested. All AI-generated analysis was validated against source documents.

---

## System Architecture

```
User Query (Web Interface)
        │
        ▼
┌─────────────────────┐
│   ReAct Loop Agent  │  ← Reason → Tool Call → Observe → Re-reason
└─────────┬───────────┘
          │ Tool calls
    ┌─────▼──────┐    ┌──────────────┐    ┌───────────────┐
    │ OSINT Tools│    │  RAG Engine  │    │  CI Frameworks│
    │ SEC EDGAR  │    │  ChromaDB    │    │  AMC Analysis │
    │ Crunchbase │    │  Vector Store│    │  Scenario Plng│
    │ Web Scraper│    │  (10-K, MCs) │    │  ECOMO Calc   │
    └─────┬──────┘    └──────┬───────┘    └───────┬───────┘
          └──────────────────┴──────────────────────┘
                             │
                    ┌────────▼────────┐
                    │ Output Layer    │
                    │ Confidence Score│  HIGH / MEDIUM / LOW
                    │ Source Citation │  per finding
                    │ Human-in-Loop  │  LOW → flagged for review
                    └─────────────────┘
                             │
                    ┌────────▼────────┐
                    │ GitHub Pages    │
                    │ Interactive     │
                    │ Workbench       │
                    └─────────────────┘
```

**Components:**
- **Python Agent** — runs locally, produces analysis output (JSON)
- **Web Front-end** — React/HTML interactive workbench hosted on GitHub Pages
- **Data Store** — ChromaDB vector database (indexed from 10-K filings, model cards, regulatory docs)

---

## Installation (Python Agent)

### Requirements

- Python 3.10+
- Node.js 18+ (for web front-end)

### Setup

```bash
# Clone the repository
git clone https://github.com/[team7]/agentic-ci-system.git
cd agentic-ci-system

# Install Python dependencies
pip install -r requirements.txt

# Install Node dependencies (web front-end)
cd frontend
npm install
```

### requirements.txt

```
langchain>=0.2.0
langchain-anthropic>=0.1.0
chromadb>=0.5.0
playwright>=1.40.0
requests>=2.31.0
pandas>=2.0.0
python-dotenv>=1.0.0
```

---

## API Keys Required

Create a `.env` file in the project root. **Do NOT commit this file to the repository.**

```env
ANTHROPIC_API_KEY=your_key_here
CRUNCHBASE_API_KEY=your_key_here   # free tier sufficient
```

SEC EDGAR API and Google Patents are free and require no key.

---

## How to Run Locally

### Run the Python Agent

```bash
# Step 1: Index documents (run once, or when you add new documents)
python agent/index_documents.py

# Step 2: Run full CI analysis
python agent/run_analysis.py --question "Should our company use OpenAI, Anthropic, or Google for enterprise AI?"

# Step 3: Output will be saved to output/analysis.json
```

### Start the Web Interface

```bash
cd frontend
npm start
# Opens at http://localhost:3000
```

### Push Analysis Output to GitHub Pages

```bash
# After running the agent, copy output to frontend public folder
cp output/analysis.json frontend/public/data/analysis.json
cd frontend
npm run build
# Then push to gh-pages branch (see GitHub Pages setup guide)
```

---

## Expected Output

The agent produces a structured JSON output (`output/analysis.json`) containing:

```json
{
  "query": "...",
  "timestamp": "2026-04-25T...",
  "amc_analysis": {
    "openai": { "awareness": 5, "motivation": 5, "capability": 4, "confidence": "HIGH", "sources": [...] },
    "anthropic": { ... },
    "google": { ... }
  },
  "scenarios": [ { "name": "Compliance Maze", "probability": 0.40, "implication": "..." }, ... ],
  "ecomo": {
    "option_a": { "npv": 5990000, "ev": 5990000 },
    "option_b": { "npv": 9390000, "ev": 8410000 }
  },
  "recommendation": { "verdict": "CONDITIONAL GO", "confidence": "MEDIUM-HIGH", "option": "B" },
  "low_confidence_flags": [ { "claim": "...", "reason": "Source not retrieved" } ]
}
```

The web interface reads this JSON and renders an interactive CI workbench with AMC tables, scenario matrix, ECOMO comparison, and the final recommendation.

---

## TN10–TN12 Capabilities Demonstrated

| Capability | Reference | Implementation |
|-----------|-----------|----------------|
| ReAct Loop | TN10 | `agent/react_loop.py` — Reason → tool call → observe → re-reason |
| Tool Use / API Integration | TN10 | SEC EDGAR API, Crunchbase free tier, web scraping via Playwright |
| RAG | TN10 | ChromaDB vector store indexed from 10-K filings, model cards, EU AI Act text |
| Confidence Scoring | TN12 | Every finding tagged HIGH/MEDIUM/LOW based on source count and quality |
| Anti-Hallucination | TN12 | Mandatory source attribution; unattributed claims auto-downgraded to LOW |
| Human-in-the-Loop | TN12 | LOW-confidence findings quarantined and flagged for human review |

---

## Known Limitations and Failure Modes

1. **Stale data:** The ChromaDB index must be manually refreshed when new documents are available. Old documents produce outdated analysis without warning.
2. **Hallucinated citations:** Despite the anti-hallucination layer, the agent can occasionally fabricate source names that sound plausible (e.g., non-existent Gartner reports). The confidence scoring catches most cases but not all.
3. **Benchmark sensitivity:** AMC capability scores are sensitive to which benchmarks are indexed. If a new benchmark emerges that favours a different vendor, scores will be incorrect until the index is updated.
4. **Single-language limitation:** OSINT collection is limited to English-language sources. Non-English EU regulatory documents (e.g., German Bundestag publications) are not currently indexed.
5. **No real-time data:** The agent does not have live access to financial markets or breaking news. Time-sensitive intelligence requires manual supplementation.

---

## Submission Checklist

- [x] CI Analysis Report: `Group_7_CI_Report.docx`
- [x] Individual Reflection: `Carlos_Martin_Reflection.pdf`
- [x] Presentation Slides: `Group_7_Presentation.pptx`
- [x] GitHub Pages URL: `https://[team7].github.io/agentic-ci-system/`
- [x] README with architecture diagram and installation instructions
- [x] API keys NOT included in repository
- [x] AI tools disclosed

---

*Team 7 | Carlos Martín Oliveros | UFV Corporate Intelligence 2025–2026*
