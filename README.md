# Multi-Agent Research System

A 4-agent AI pipeline that searches, scrapes, writes and critiques research reports on any topic — with a beautiful Streamlit web UI.

---

## How It Works

The system runs a **shared state dictionary** that gets populated at each step of the pipeline. Every agent reads from and writes to this state, passing information forward to the next agent.

```
User enters a topic
        │
        ▼
┌─────────────────────────────────────┐
│         01 · Search Agent           │  ← Tavily API
│  Searches the web for 5 results     │
│  Returns: title, URL, snippet       │
└─────────────────────────────────────┘
        │
        │  state["search_results"] ✓
        ▼
┌─────────────────────────────────────┐
│         02 · Reader Agent           │  ← BeautifulSoup
│  Picks the most relevant URL        │
│  Scrapes up to 3000 chars of text   │
└─────────────────────────────────────┘
        │
        │  state["scraped_content"] ✓
        ▼
   [ search_results + scraped_content combined ]
        │
        ▼
┌─────────────────────────────────────┐
│         03 · Writer Chain           │  ← Groq LLM
│  Drafts a full research report:     │
│  Intro · Key Findings · Conclusion  │
│  Sources                            │
└─────────────────────────────────────┘
        │
        │  state["report"] ✓
        ▼
┌─────────────────────────────────────┐
│         04 · Critic Chain           │  ← Groq LLM
│  Reviews the report strictly        │
│  Gives score, strengths, weaknesses │
└─────────────────────────────────────┘
        │
        │  state["feedback"] ✓
        ▼
┌─────────────────────────────────────┐
│         Streamlit UI                │
│  Displays report + critic feedback  │
│  Download report as .md             │
└─────────────────────────────────────┘
```

---

## State Space

The pipeline uses a shared `state` dictionary that grows at each step:

| After Step | State Contains |
|------------|---------------|
| Start | `state = {}` |
| After Search Agent | `state = { search_results }` |
| After Reader Agent | `state = { search_results, scraped_content }` |
| After Writer Chain | `state = { search_results, scraped_content, report }` |
| After Critic Chain | `state = { search_results, scraped_content, report, feedback }` |

---

## What Each Agent Does

### 01 · Search Agent
- Receives the user's topic as input
- Calls the **Tavily Search API** to fetch 5 recent, reliable web results
- Each result contains a title, URL, and a short snippet (300 chars)
- Stores the combined results in `state["search_results"]`

### 02 · Reader Agent
- Receives `state["search_results"]` (first 800 chars) as input
- Picks the most relevant URL from the search results
- Uses **BeautifulSoup** to scrape and clean the page content
- Returns up to 3000 characters of clean text
- Stores it in `state["scraped_content"]`

### 03 · Writer Chain
- Receives both `search_results` and `scraped_content` combined
- Sends them to **Groq LLM (llama-3.3-70b-versatile)** with a structured prompt
- LLM writes a full research report with:
  - Introduction
  - Key Findings (minimum 3 points)
  - Conclusion
  - Sources (all URLs found)
- Stores the report in `state["report"]`

### 04 · Critic Chain
- Receives `state["report"]` as input
- Sends it to **Groq LLM** with a strict critic prompt
- LLM evaluates the report and returns:
  - Score out of 10
  - Strengths
  - Areas to improve
  - One line verdict
- Stores feedback in `state["feedback"]`

---

## Agents

- **Search Agent** — Finds recent web information using Tavily
- **Reader Agent** — Scrapes and extracts deep content from URLs
- **Writer Chain** — Drafts a full structured research report
- **Critic Chain** — Reviews and scores the report

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3.11.7 | Core language |
| LangChain 1.3.9 | Agent framework |
| Groq (llama-3.3-70b-versatile) | LLM for writing and critiquing |
| Tavily Search API | Web search for agents |
| BeautifulSoup | Web scraping |
| Streamlit | Web UI |
| python-dotenv | API key management |

---

## Project Structure

```
multi-agent-research-system/
├── app.py           # Streamlit web UI
├── agents.py        # Agent and chain definitions
├── tools.py         # web_search and scrape_url tools
├── pipeline.py      # CLI version of the pipeline
├── requirements.txt # Python dependencies
├── .env             # API keys (not committed)
└── .gitignore       # Ignores .env and .venv
```

---

## Setup

**1. Clone the repo**
```bash
git clone https://github.com/abhinav-ghosh/Multi-Agent-Research-System
cd Multi-Agent-Research-System
```

**2. Create virtual environment**
```bash
uv venv .venv --python 3.11
.venv\Scripts\Activate.ps1
```

**3. Install dependencies**
```bash
uv pip install -r requirements.txt
```

**4. Add your API keys in `.env`**
```
GROQ_API_KEY=your_groq_key_here
TAVILY_API_KEY=your_tavily_key_here
```

**5. Run the app**
```bash
streamlit run app.py
```

---

## API Keys (both free)

| Key | Where to Get |
|-----|-------------|
| Groq API Key | [console.groq.com](https://console.groq.com) |
| Tavily API Key | [app.tavily.com](https://app.tavily.com) |
