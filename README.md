
# 🌐 Web Brochure Builder – Multilingual Web Brochure Generator

## Table of Contents
1. [Project Overview](#project-overview)  
2. [Why This Repo Exists](#why-this-repo-exists)  
3. [Key Features](#key-features)  
4. [Architecture Diagram](#architecture-diagram)  
5. [Installation & Setup](#installation--setup)  
6. [How to Run the Notebooks](#how-to-run-the-notebooks)  
   - [Project Brochure Studio (single‑model UI)](#project-brochure-studio)  
   - [Project Multilingual Web Brochure Generator (full pipeline)](#project-multilingual-web-brochure-generator)  
7. [Core Modules & Functions Explained](#core-modules--functions-explained)  
8. [Extending & Customising the Project](#extending--customising)  
9. [Testing & Development Workflow](#testing--development-workflow)  
10. [Troubleshooting & FAQ](#troubleshooting--faq)  
11. [License](#license)  
12. [Acknowledgements & Further Reading](#acknowledgements--further-reading)  

---

## Project Overview
**Web Brochure Builder** is a Python‑based Jupyter notebook suite that turns any public company website into a concise, markdown‑formatted brochure.  
It does this by:

1. **Scraping** the landing page (and a curated set of secondary pages).  
2. **Feeding** the raw text to a Large Language Model (LLM) (OpenAI GPT‑4o‑mini by default).  
3. **Prompting** the LLM to generate a short, engaging marketing brochure in English.  
4. **Translating** that brochure into any supported language, injecting humor and cultural nuance while preserving markdown structure.

All of this runs locally (or in a hosted notebook) with a single API key and a few Python dependencies.

---

## Why This Repo Exists
Creating a polished marketing brochure normally involves:

* Manual reading of dozens of web pages.  
* Copy‑pasting, editing, and re‑formatting content.  
* Hiring a copywriter or relying on generic translation tools.

**Web Brochure Builder** automates the *research* and *drafting* steps, allowing founders, marketers, investors, or developers to get a ready‑to‑publish brochure in seconds.  
The multilingual extension adds a witty, culturally‑aware translation layer, making it useful for global outreach.

---

## Key Features
| Feature | Description | Benefits |
|---------|-------------|----------|
| **URL Validation** | Uses `validators.url()` to ensure the input is a well‑formed URL before any network request. | Prevents wasted API calls and cryptic errors. |
| **Dynamic Link Selection** | Sends the full list of discovered links to GPT with a system prompt that returns the most brochure‑relevant links in JSON. | Guarantees the final narrative focuses on About, Careers, Docs, etc., not on irrelevant pages. |
| **Recursive Scraping** | For each selected link, extracts clean title & body text (removes scripts, styles, images). | Supplies rich context for a richer brochure. |
| **LLM‑Powered Brochure Generation** | A system prompt guides GPT‑4o‑mini to produce a short, markdown‑styled brochure. | Leverages state‑of‑the‑art language generation without hand‑crafting the copy. |
| **Streaming Output** | Optional streaming function (`stream_gpt`, `stream_claude`, `stream_gemini`) updates the UI in real‑time. | Improves UX: you can watch the story unfold. |
| **Multilingual Humor Translation** | Uses `pycountry` for language validation and a second system prompt to translate with wit. | Turns a plain translation into a memorable, culturally‑aware piece. |
| **Model Flexibility** | Supports OpenAI, Anthropic Claude, and Google Gemini (via dedicated streaming wrappers). | Future‑proofs the repo for any LLM you prefer. |
| **Gradio UI** | A lightweight web UI (`Project Brochure Studio.ipynb`) lets non‑technical users pick a model and generate a brochure instantly. | No notebook knowledge required. |
| **MIT License** | Free for commercial and private use. | Encourages community contributions. |

---

## Architecture Diagram
```
┌─────────────────────┐
│   User Input (URL)   │
└───────┬─────────────┘
        │
        ▼
┌─────────────────────┐   ┌─────────────────────┐
│ validators.url()    │   │ pycountry (lang)    │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│ requests.get()      │   │ OpenAI API (gpt)    │
│ (HTML → soup)       │   │ (system/user)      │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│ extract links       │──▶│ link‑selection LLM │
│ (BeautifulSoup)    │   │ (JSON output)      │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│ scrape each link   │──▶│ brochure LLM       │
│ (title, text)      │   │ (markdown output) │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│ combine content    │──▶│ translation LLM    │
│ (master string)    │   │ (humorous, lang)  │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│ display markdown   │   │ optional streaming │
│ (IPython.display)  │   │ (real‑time)        │
└─────────────────────┘   └─────────────────────┘
```

---

## Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/AsutoshaNanda/Web-Brochure-Builder.git
cd Web-Brochure-Builder
```

### 2. (Optional) Create a Virtual Environment
```bash
python -m venv .venv
# Linux / macOS
source .venv/bin/activate
# Windows
.venv\Scripts\activate
```

### 3. Install Dependencies
The repo ships a `requirements.txt` (if missing, the command below works):
```bash
pip install -r requirements.txt
# or manually:
pip install openai anthropic google-generativeai python-dotenv validators beautifulsoup4 pycountry gradio
```

### 4. Add Your OpenAI API Key
Create a `.env` file in the repo root:
```
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxx
GOOGLE_API_KEY=AIzaSyxxxxxxxxxxxxxxx
```
> **Tip:** Only the OpenAI key is required for the default pipeline. Claude and Gemini keys are optional.

### 5. Launch Jupyter
```bash
jupyter notebook
```
Open either notebook (see next section).

---

## How to Run the Notebooks

### Project Brochure Studio (`Project Brochure Studio.ipynb`)
A **Gradio‑powered UI** that lets you:

* Input a company name, URL, and pick a model (GPT, Claude, Gemini).  
* Press **Generate**, and watch a streaming markdown brochure appear.  
* Share the live UI via Gradio’s `share=True` link (useful for quick demos).

**Typical workflow**  
1. Fill the three fields.  
2. Click **Generate**.  
3. Copy the markdown output or export it to a file.

### Project Multilingual Web Brochure Generator (`Project Multilingual Web Brochure Generator.ipynb`)
A **full‑pipeline notebook** that:

1. **Validates** the URL and language code.  
2. **Scrapes** the landing page, extracts title & body.  
3. **Finds** additional relevant pages via an LLM‑driven link‑selection step.  
4. **Aggregates** content from all pages into a single master string.  
5. **Generates** an English brochure with GPT‑4o‑mini.  
6. **Translates** the brochure into a target language, injecting humor while preserving markdown structure.  

**Running the pipeline**

```python
# Example (run each cell sequentially)
myurl = "https://huggingface.co/"
company_name = "Hugging Face"
target_language = "JPN"  # any ISO‑639‑2/3 code accepted by pycountry

# 1️⃣ Scrape & select links → 2️⃣ Build master content → 3️⃣ Generate brochure → 4️⃣ Translate
display(Markdown(get_brochure(company_name, myurl)))          # English version
display(Markdown(get_brochure_target_language(myurl, company_name, target_language)))  # Japanese version
```

**Result**: Two beautifully formatted markdown sections, ready for copy‑paste into a website, PDF, or slide deck.

---

## Core Modules & Functions Explained

| Module / Function | Purpose | Important Notes |
|-------------------|---------|-----------------|
| `scape_webpage(myurl)` | Basic scraper for a single page (title + body). | Strips `<script>`, `<style>`, `<img>`, `<input>` tags. |
| `stream_gpt(prompt)` / `stream_claude(prompt)` / `stream_gemini(prompt)` | Generator that yields incremental LLM responses for streaming UI. | Removes stray backticks to keep markdown clean. |
| `stream_brochure(company_name, myurl, model)` | Wrapper that selects the appropriate streaming function based on the chosen model. | `model` argument: `"GPT"`, `"Claude"` or `"Gemini"`. |
| `get_links_user_prompt(myurl)` | Constructs a user‑prompt containing all discovered links for the link‑selection LLM. | Encourages the model to output JSON. |
| `get_links(myurl)` | Calls OpenAI with the link‑selection system prompt and returns parsed JSON. | Uses `response_format={'type':'json_object'}` for reliable parsing. |
| `scrape_web(myurl)` | Full‑page scraper used for secondary links (about, careers, etc.). | Returns a tuple `(title, text)`. |
| `get_all_details(myurl)` | Orchestrates scraping of landing page **and** all selected secondary pages, concatenating their contents. | Produces the master string fed to the brochure LLM. |
| `get_brochure(company_name, myurl)` | Calls OpenAI with the brochure system prompt and the master string. | Returns markdown brochure. |
| `system_lang_prompt` | System prompt for the translation stage, explicitly asking for humor, cultural relevance, and markdown preservation. | Template placeholders (`{target_language}`) are filled at runtime. |
| `get_brochure_in_target_language(...)` | Builds the final user prompt for translation, including the English brochure as context. | Ensures the LLM sees the full source text. |
| `get_brochure_target_language(...)` | Calls OpenAI for the translation step and returns the humorous multilingual brochure. | Uses the same `gpt-4o-mini` model by default. |

---

## Extending & Customising the Project

1. **Add New LLMs**  
   *Create a wrapper similar to `stream_gpt` that respects the model’s streaming API, then update the dropdown in the Gradio UI.*

2. **Cache Scraped Pages**  
   *Implement a simple SQLite or JSON cache keyed by URL to avoid re‑scraping during iterative development.*

3. **CLI Wrapper**  
   *Wrap the core functions in a `click`‑based command line interface for non‑notebook usage.*

4. **Custom Prompts**  
   *Replace `system_prompt` or `system_lang_prompt` with your own tone (e.g., formal, technical, or brand‑specific).*

5. **Support More Output Formats**  
   *Add PDF export via `markdown2` → `weasyprint` or HTML conversion for web embedding.*

6. **Unit Tests**  
   *The repository includes a `tests/` folder skeleton. Add tests for `scape_webpage`, `get_links`, and language validation using `pytest`. Mock network calls with `responses`.*

---

## Testing & Development Workflow

```bash
# Install testing extras
pip install pytest responses

# Run the test suite
pytest -v
```

Typical development steps:

1. **Create a branch**  
   `git checkout -b feature/your‑idea`  
2. **Implement** & **run notebooks** to verify interactive behavior.  
3. **Add/Update tests** for any new function.  
4. **Run linting** (`flake8` or `black`) to keep code style consistent.  
5. **Submit a PR** with a concise description and screenshots/GIFs of the UI.

---

## Troubleshooting & FAQ

| Problem | Likely Cause | Fix |
|---------|--------------|-----|
| `Invalid Website` message | URL missing scheme (`https://`) or fails `validators.url()`. | Ensure you include `https://` and that the domain resolves. |
| LLM returns *empty* brochure | Prompt length exceeded model token limit or API quota exhausted. | Reduce the size of the master content (`user_prompt = user_prompt[:20000]` already caps it) or check your API usage. |
| Translation contains raw code fences (```) | Some models keep original backticks. | The streaming functions already `replace('```','')`; for static calls, add a post‑process step. |
| API key not recognized | `.env` not loaded or key has whitespace. | Run `load_dotenv()` at the top of the notebook, and double‑check the key format. |
| `pycountry` fails to recognise language code | Using a non‑ISO code (e.g., `ENGLISH` instead of `ENG`). | Provide a 2‑letter (`en`) or 3‑letter (`eng`) ISO code. |
| Gradio UI not reachable after `share=True` | Network restrictions or firewall. | Run locally without `share=True`, or open the provided local URL (`http://127.0.0.1:7860`). |

---

## License
This project is released under the **MIT License**. See the `LICENSE` file for full terms.

---

## Acknowledgements & Further Reading
- **OpenAI GPT‑4o‑mini** – the primary language model for generation and translation.  
- **Anthropic Claude‑3‑Sonnet** & **Google Gemini‑2.5‑Flash** – alternative back‑ends supported in the UI.  
- **BeautifulSoup** – HTML parsing and clean‑text extraction.  
- **Gradio** – quick web UI scaffolding.  
- **pycountry** – ISO language validation.  
- **GitHub Discussions & Issues** – for community support, feature requests, and bug reports.

> *From a single URL to a multilingual, witty brochure, Web Brochure Builder is your silent partner in turning web noise into a concise, compelling story. Clone it, feed it a site, and let the AI spin the yarn.* 🚀

---  

*Happy building!* 🎉
