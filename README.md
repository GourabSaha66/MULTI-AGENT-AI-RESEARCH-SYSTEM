# 🔬 ResearchMind — Multi-Agent AI Research System

A 4-agent AI pipeline that autonomously searches the web, scrapes content, writes a structured research report, and critiques it — all from a single topic input.

Built with **LangChain Agents**, **Mistral AI**, **Tavily Search API**, and **Streamlit**.

---

## 🧠 How It Works

The system runs four specialized AI components in sequence:

```
User Topic
    │
    ▼
┌─────────────────┐
│  Search Agent   │  ← Uses Tavily API to find top 5 relevant web results
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Reader Agent   │  ← Picks the best URL and scrapes full content via BeautifulSoup
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Writer Chain   │  ← Synthesizes all research into a structured report (Intro → Findings → Conclusion → Sources)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Critic Chain   │  ← Reviews and scores the report (X/10) with strengths and improvement areas
└─────────────────┘
```

Each agent is powered by **Mistral Small (mistral-small-2603)** via `langchain-mistralai`.

---

## ✨ Features

- **Tool-using agents** — Search Agent and Reader Agent use LangChain's `@tool` decorator with real-world tool calls (Tavily search + web scraping)
- **Structured prompt chains** — Writer and Critic use `ChatPromptTemplate | LLM | StrOutputParser` chains
- **Real-time UI** — Streamlit frontend with live pipeline step tracking, status indicators, and progress spinners
- **Downloadable report** — Final report exported as `.md` file
- **Clean web scraping** — BeautifulSoup strips scripts, styles, navbars and footers before returning readable content
- **Secure config** — API keys managed via `.env` and `python-dotenv`; never hardcoded

---

## 🗂️ Project Structure

```
├── app.py            # Streamlit UI — main entry point
├── agents.py         # Agent definitions (Search, Reader, Writer, Critic)
├── tools.py          # LangChain @tools (web_search via Tavily, scrape_url via BeautifulSoup)
├── pipeline.py       # CLI pipeline runner (run without Streamlit)
├── requirements.txt  # All dependencies
├── .env.example      # Template for environment variables
└── .gitignore        # Excludes .env, .venv, __pycache__, etc.
```

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| LLM | Mistral Small via `langchain-mistralai` |
| Agents & Chains | LangChain (`langchain`, `langchain-core`) |
| Web Search | Tavily API (`tavily-python`) |
| Web Scraping | `requests` + `BeautifulSoup4` |
| Frontend | Streamlit |
| Config | `python-dotenv` |
| Logging | `rich` |

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/GourabSaha66/multi-agent-research-system.git
cd multi-agent-research-system
```

### 2. Create a virtual environment

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Set up environment variables

```bash
cp .env.example .env
```

Open `.env` and fill in your API keys:

```env
MISTRAL_API_KEY=your_mistral_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
```

- Get your Mistral API key → [console.mistral.ai](https://console.mistral.ai)
- Get your Tavily API key → [app.tavily.com](https://app.tavily.com)

### 5. Run the app

```bash
streamlit run app.py
```

Or run the CLI pipeline directly:

```bash
python pipeline.py
```

---


---

## 🧩 Agent Design Details

### Search Agent
- Tool: `web_search(query: str)`
- Uses Tavily API to retrieve top 5 results with title, URL, and content snippet
- Returns structured text for the Reader Agent to consume

### Reader Agent
- Tool: `scrape_url(url: str)`
- Receives search results, selects the most relevant URL, and scrapes up to 3000 characters of clean text
- Strips `<script>`, `<style>`, `<nav>`, `<footer>` tags before extraction

### Writer Chain
- `ChatPromptTemplate | ChatMistralAI | StrOutputParser`
- Prompt enforces structure: Introduction → Key Findings (min 3) → Conclusion → Sources
- Combines both search snippets and scraped content as context

### Critic Chain
- `ChatPromptTemplate | ChatMistralAI | StrOutputParser`
- Returns a strict structured evaluation: Score (X/10), Strengths, Areas to Improve, One-line verdict

---

## 🔮 Potential Improvements

- [ ] Add a feedback loop: if Critic score < 7, route back to Writer with improvement notes
- [ ] Integrate LangGraph `StateGraph` for true conditional agent routing
- [ ] Add vector store memory (FAISS / Chroma) to avoid re-searching the same topics
- [ ] Stream LLM output token-by-token in the Streamlit UI
- [ ] Add support for multiple LLM providers (OpenAI, Gemini)

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 👤 Author

**Gourab Saha**  
[LinkedIn](https://linkedin.com/in/gourab-saha-677668211) · [GitHub](https://github.com/GourabSaha66) · sgourab66@gmail.com
