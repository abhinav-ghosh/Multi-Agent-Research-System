# Multi-Agent Research System

A 4-agent AI pipeline that searches, scrapes, writes and critiques research reports on any topic — with a beautiful Streamlit web UI.

## Agents
- **Search Agent** — Finds recent web information using Tavily
- **Reader Agent** — Scrapes and extracts deep content from URLs
- **Writer Chain** — Drafts a full structured research report
- **Critic Chain** — Reviews and scores the report

## Tech Stack
- Python 3.11.7
- LangChain 1.3.9
- Groq (llama-3.3-70b-versatile)
- Tavily Search API
- Streamlit

## Setup

1. Clone the repo
git clone https://github.com/abhinav-ghosh/Multi-Agent-Research-System

2. Create virtual environment
uv venv .venv --python 3.11
.venv\Scripts\Activate.ps1

3. Install dependencies
uv pip install -r requirements.txt

4. Add your API keys in .env file
GROQ_API_KEY=your_groq_key_here
TAVILY_API_KEY=your_tavily_key_here

5. Run the app
streamlit run app.py
