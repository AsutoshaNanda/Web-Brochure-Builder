# Web-Brochure-Builder

A Jupyter Notebook project that scrapes a company website, extracts relevant links/pages, and uses a chat model to generate a short, humorous marketing brochure based on the site's contents.

This repo was built around the OpenAI API (via the OpenAI Python client). If you want to run the notebook in an open-source / local-only environment, you can replace the OpenAI calls with a local LLM provider such as Ollama. See the "Alternative: Ollama" section below for guidance.

---

## Features (high-level)
- Fetches and scrapes a user-provided company landing page.
- Identifies and filters relevant links (About, Careers, Blog, Enterprise, etc.) using a chat model.
- Scrapes those relevant pages for content and combines them.
- Sends aggregated content to a chat model to generate a short, entertaining brochure in markdown.
- Optional streaming mode to render the brochure chunk-by-chunk in the notebook.

---

## Repo contents
- LICENSE — MIT License.
- Project Web-Brochure-Builder.ipynb — the main Jupyter notebook that contains scraping, link-selection, and brochure-generation logic.

---

## Requirements
- Python 3.8+
- Jupyter Notebook / JupyterLab
- Python packages: requests, beautifulsoup4, validators, openai (or alternative client), python-dotenv, IPython

Example install:
pip install -r requirements.txt
(If a requirements.txt isn't included, install packages manually):
pip install requests beautifulsoup4 validators openai python-dotenv ipython notebook

---

## Setup — environment & API keys (full details)

1. Clone the repo:
   git clone <repo-url>
   cd Web-Brochure-Builder

2. Create a virtual environment (recommended):
   python -m venv .venv
   source .venv/bin/activate   # macOS / Linux
   .venv\Scripts\activate      # Windows

3. Create a `.env` file in the repo root (this notebook expects an env var). Example `.env`:
   OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

   Important:
   - Do NOT commit `.env` to source control.
   - Add `.env` to `.gitignore` if you use git:
     echo ".env" >> .gitignore

4. Verify the API key format:
   - The included notebook checks that the OPENAI_API_KEY exists and (in that notebook) expects keys that start with `sk-proj-`. If your key differs, adjust the notebook check or use your standard OpenAI key.
   - If you want to use a local provider (Ollama) instead of OpenAI, see "Alternative: Ollama" below.

---

## How to use — step-by-step (notebook-first flow)

1. Open the notebook:
   jupyter notebook "Project Web-Brochure-Builder.ipynb"
   or
   jupyter lab

2. In the notebook, run the initial cells in order. Key cells and interactions:
   - Cell that loads environment variables via python-dotenv: ensures OPENAI_API_KEY is available.
   - There is a small sanity check that prints whether the API key looks present/valid.
   - The notebook sets a headers dict with a `User-Agent` string to avoid obvious blocking from some websites.
   - Input prompt: enter the URL of the target website when prompted (e.g., https://huggingface.co/). The code validates the URL.
   - The notebook performs a GET request and builds a BeautifulSoup object, removes script/style/img/input tags, extracts visible text.
   - It collects all anchor links, and then uses the chat model to select which links are relevant to a brochure (About, Careers, Blog, Company pages, etc.). The chat model returns a JSON object listing relevant links.
   - The notebook scrapes those relevant pages for page titles and body text, aggregates them, and builds a prompt for the brochure-generation step.
   - You are asked for the company name (used as context for the brochure).
   - Finally, the notebook sends the aggregated content to a chat model (the notebook uses `gpt-4o-mini`) to produce a short markdown brochure. The result is displayed in the notebook as Markdown output.
   - Optional: the notebook includes a streaming mode function that streams chunks of output (looks like ChatGPT streaming) so you can display the brochure as it is generated.

3. Example interactive flow:
   - Enter URL: https://huggingface.co/
   - Enter company name: Hugging Face
   - Wait for link-selection and scraping
   - View generated brochure in the output cell.

---

## Code notes & configuration
- The notebook uses `openai = OpenAI()` from the OpenAI Python client, and calls `openai.chat.completions.create(...)`. If you use a different client library or provider, modify those call sites.
- Model choices: the notebook references `gpt-4o-mini`. Replace with any model you have access to. Be mindful of usage and cost.
- The notebook strips out scripts, images, styles, and inputs before extracting text — this helps keep the prompt succinct.
- Filtering links: the model-based filter may return relative URLs. The notebook assumes the returned JSON contains full https URLs; if relative URLs are returned, you may need to join them against the base URL before scraping.

---

## Alternative: Using Ollama (local LLM) instead of OpenAI
If you want to use a local provider and keep the project open-source without depending on an OpenAI API key, Ollama is a common choice. High-level steps:

1. Install Ollama and your desired model:
   - Follow Ollama installation instructions: https://ollama.com/docs
   - Pull/run a model locally, e.g. `ollama pull llama2` (or any model you prefer) and run `ollama serve` (if required by your Ollama version).

2. Adjust the notebook to use Ollama:
   - Option A — Use the Ollama HTTP API:
     - Send POST requests to the local Ollama endpoint (often `http://127.0.0.1:11434/api/generate?model=<model-name>` or similar). Check your Ollama version docs for exact endpoints.
     - Build a compatible chat request payload (system + user messages) and parse streaming/batched responses accordingly.
   - Option B — Use an Ollama Python client (if available) or a small wrapper that posts to the local HTTP API.

3. Example (conceptual) request pattern using requests (adjust per Ollama docs):
   ```python
   # Pseudocode / example — confirm exact Ollama API shape from docs
   import requests, json
   url = "http://127.0.0.1:11434/api/generate?model=your-model-name"
   payload = {
       "prompt": "Your prompt here",
       "temperature": 0.2,
       "max_length": 512
   }
   r = requests.post(url, json=payload)
   result = r.json()
   # Parse result and extract generated text
   ```
   - Replace the notebook's `openai.chat.completions.create(...)` calls with the equivalent request+parse logic.

4. Environment variable & configuration:
   - Set `MODEL_PROVIDER=ollama` (optional) and adjust conditional code in the notebook to branch between OpenAI and Ollama.
   - Local Ollama usually does not require an API key by default; check your deployment/configuration.

Note: APIs and endpoints vary across Ollama versions — consult Ollama docs for exact usage.

---

## Security & privacy
- Never commit API keys or secrets into source control.
- Use `.env` or a secrets manager to store API keys.
- When scraping websites, respect robots.txt and the website's terms of service. Some websites disallow scraping or may block requests.
- This project includes a User-Agent header but does not handle advanced anti-bot measures or rate limiting.

---

## Troubleshooting & tips
- If HTTP requests return non-200: the site may block scraping, require JavaScript, or need different headers. Consider using a headless browser (Selenium, Playwright) for JS-heavy sites.
- If the model returns invalid JSON for link-selection: increase system prompt clarity, or add sanitization/parsing steps.
- If OpenAI calls fail: check your OPENAI_API_KEY in `.env`, ensure network access, and confirm the correct client installation.
- If you need more concise prompts or want to avoid sending entire long pages to the model, pre-summarize page text before sending.

---

## Licensing
MIT License. See LICENSE file for full text.

---

## Final notes
- The notebook is designed as an experiment and demo; treat it as a starting point. You may want to:
  - Add URL normalization / relative->absolute link resolution,
  - Add caching for scraped pages,
  - Add throttling / politeness delays during scraping,
  - Improve prompt design to better control brochure tone and length,
  - Add error handling for network and parsing failures.

If you want, I can:
- Provide a ready-to-run `requirements.txt`.
- Add a small script version (python file) that runs end-to-end outside Jupyter.
- Generate a branch with example Ollama integration code adapted to the current notebook.

Enjoy building brochures!

