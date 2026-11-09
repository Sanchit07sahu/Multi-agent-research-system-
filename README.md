# 🔬 ResearchMind – Multi-Agent AI Research Assistant

ResearchMind turns a single topic into a structured, source-cited research report. Four specialised stages work in sequence: a **Search Agent** finds sources, a **Reader Agent** scrapes the best one for depth, a **Writer Chain** drafts the report, and a **Critic Chain** scores and reviews it.

Built with **LangChain**, **OpenAI GPT-4o-mini**, **Tavily**, and **Streamlit**.

<!-- Add a screenshot or GIF of the app here -->
<!-- ![ResearchMind demo](assets/demo.png) -->

---

## Problem

Manual research means searching, opening many pages, reading, and summarising. This is slow and inconsistent. ResearchMind automates the workflow and adds a built-in quality check, so you get a first-draft report with sources in under a minute.

## How it works

```
Topic ──► Search Agent ──► Reader Agent ──► Writer Chain ──► Critic Chain
          (Tavily tool)    (scrape tool)    (report draft)   (score + feedback)
```

| Stage | Type | What it does |
|---|---|---|
| 1. Search Agent | LangChain agent + `web_search` tool | Queries Tavily and returns the top 5 results (title, URL, snippet) |
| 2. Reader Agent | LangChain agent + `scrape_url` tool | Picks the most relevant URL and scrapes up to 3,000 characters of clean text |
| 3. Writer Chain | Prompt → LLM → parser | Writes a report: Introduction, Key Findings (3+), Conclusion, Sources |
| 4. Critic Chain | Prompt → LLM → parser | Returns a score out of 10, strengths, areas to improve, and a one-line verdict |

All stages use `gpt-4o-mini` with `temperature=0` for consistent output.

## Tech stack

- **Language:** Python 3.12
- **LLM framework:** LangChain (`create_agent`, LCEL chains)
- **Model:** OpenAI GPT-4o-mini
- **Search:** Tavily Search API
- **Scraping:** Requests + BeautifulSoup
- **UI:** Streamlit
- **Config:** python-dotenv

## Project structure

```
.
├── app.py             # Streamlit web app
├── agents.py          # Agents, writer chain, critic chain
├── tools.py           # web_search and scrape_url tools
├── pipeline.py        # Command-line version of the pipeline
├── requirements.txt
├── .env.example       # Template for API keys
└── .gitignore
```

## Getting started

### 1. Clone and install

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Add API keys

Copy `.env.example` to `.env` and fill in your keys:

```
OPENAI_API_KEY=your_openai_key
TAVILY_API_KEY=your_tavily_key
```

Get keys from [OpenAI](https://platform.openai.com/) and [Tavily](https://tavily.com/). Never commit your `.env` file.

### 3. Run

**Web app:**

```bash
streamlit run app.py
```

**Command line:**

```bash
python pipeline.py
```

## Deployment

The simplest option is **Streamlit Community Cloud**:

1. Push the repo to GitHub.
2. Create a new app at [share.streamlit.io](https://share.streamlit.io) and point it to `app.py`.
3. Add `OPENAI_API_KEY` and `TAVILY_API_KEY` under **Settings → Secrets**.

It also runs on Hugging Face Spaces, Render, Railway, or in a Docker container on AWS, GCP, or Azure. If the app is public, set a spending limit on your OpenAI key.

## Evaluation

> Fill in this table with your own measurements. Do not publish numbers you have not measured.

I compared the full pipeline against a plain GPT-4o-mini prompt (no search, no agents) on **[N] topics**.

| Metric | Plain LLM | ResearchMind |
|---|---|---|
| Avg. critic score (/10) | [ ] | [ ] |
| Avg. real, working URLs cited | [ ] | [ ] |
| Avg. time per report (s) | [ ] | [ ] |
| Avg. cost per report ($) | [ ] | [ ] |

Note: the critic uses the same model as the writer, so its score is a rough signal and not ground truth. URLs were checked manually.

## Limitations

- The Reader Agent scrapes **one** URL, truncated to 3,000 characters.
- Only the first 800 characters of the search results are passed to the Reader Agent.
- Scraping fails on JavaScript-heavy or paywalled pages.
- The critic and writer share the same model, so the review can be biased.
- Reports can still contain errors; verify important claims against the cited sources.

## Roadmap

- [ ] Scrape the top 2–3 URLs instead of one
- [ ] Revision loop: writer rewrites using critic feedback until score ≥ 8
- [ ] Retries and fallbacks for failed scrapes
- [ ] Per-stage latency and token logging
- [ ] Export reports to PDF/DOCX

## License

MIT (add a `LICENSE` file if you want to publish under this license).
