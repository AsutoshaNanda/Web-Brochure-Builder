
# 📚 Web Brochure Builder – A Journey From URL to Storytelling Masterpiece

## 🌟 Introduction: The Tale Begins

Imagine you’ve just discovered a promising startup, an inspiring nonprofit, or a vibrant community platform. You want to share its essence with investors, potential partners, or curious customers—**but you don’t have the time to sift through endless pages, extract key messages, and craft a compelling narrative**.  

Enter **Web Brochure Builder**, a Python notebook that transforms a website’s raw content into a polished, multilingual brochure with a dash of humor and personality. It’s more than a script; it’s a digital storyteller that **listens, curates, and translates**—all powered by OpenAI’s GPT‑4o‑mini.

## 📖 The Story So Far

The repository started as a personal experiment: *“Can I automate the creation of a marketing brochure straight from a website?”* The answer was a resounding **YES**, but only after a series of adventures:

1. **Scouting the Web** – A simple URL input, validated with `validators`, opens the gate to the site’s HTML.
2. **Harvesting the Treasure** – BeautifulSoup parses titles, body text, and discovers hidden gems (links to About, Careers, Docs, etc.).
3. **Choosing the Worthy Links** – A clever system‑prompt asks GPT to pick the most relevant pages for a brochure, returning JSON that maps each link to its purpose.
4. **Deep‑Diving Into Each Page** – Each chosen link is scraped, its title and content collected, and stitched together into a master document.
5. **The Grand Narrative** – A second system‑prompt guides GPT to weave all this information into a **short, engaging markdown brochure**.  
6. **Going Global** – The final touch: a language‑specific prompt that translates the brochure, adds wit, and preserves structure—turning the English draft into a humorous, culturally‑aware version in any supported language.

All of this lives inside a single, interactive Jupyter notebook: `Project Multilingual Web Brochure Generator.ipynb`.

## 🚀 Core Features (The Hero’s Toolbox)

| Feature | What It Does | Why It Matters |
|---------|--------------|----------------|
| **URL Validation** | Ensures the entered website is well‑formed before any request. | Prevents wasted API calls and confusing errors. |
| **Dynamic Link Selection** | Sends the full list of discovered links to GPT with a system‑prompt to select the most brochure‑relevant ones. | Guarantees the final brochure focuses on the right pages (About, Careers, Docs, etc.). |
| **Recursive Scraping** | Visits each selected link, extracts clean text (stripping scripts, images, styles). | Provides rich, context‑aware content for the story. |
| **Customizable System Prompts** | Two main prompts: one for brochure creation, one for translation with humor. | Gives you full creative control over tone, length, and style. |
| **Streaming Output** | Optional streaming function renders the brochure in real‑time as GPT streams its answer. | Enhances UX in notebooks—watch the story unfold! |
| **Multilingual Humor** | Uses `pycountry` to validate target language codes, then translates with witty flair. | Turns a plain translation into a memorable, culturally‑aware piece. |
| **OpenAI‑First Design** | All heavy‑lifting done by GPT‑4o‑mini, with optional response format enforcement (JSON, markdown). | Leverages state‑of‑the‑art LLM capabilities without reinventing the wheel. |

## 🛠️ Technical Architecture (A Map of the Realm)

```
┌─────────────────────┐
│   User Input (URL)   │
└───────┬─────────────┘
        │
        ▼
┌─────────────────────┐   ┌─────────────────────┐
│  validators.url()   │   │  pycountry (lang)   │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│  requests.get()      │   │   OpenAI API (gpt) │
│  (HTML → soup)       │   │   (system/user)   │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│  extract links      │──▶│  link‑selection LLM │
│  (BeautifulSoup)   │   │  (JSON output)     │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│  scrape each link   │──▶│  brochure LLM       │
│  (title, text)      │   │  (markdown output) │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│  combine content    │──▶│  translation LLM    │
│  (master string)    │   │  (humorous, lang)  │
└───────┬─────────────┘   └───────┬─────────────┘
        │                     │
        ▼                     ▼
┌─────────────────────┐   ┌─────────────────────┐
│  display markdown   │   │  optional streaming│
│  (IPython.display)  │   │  (real‑time)       │
└─────────────────────┘   └─────────────────────┘
```

## 📦 Installation & Quick Start (Your First Quest)

1. **Clone the Repo**  
   ```bash
   git clone https://github.com/AsutoshaNanda/Web-Brochure-Builder.git
   cd Web-Brochure-Builder
   ```

2. **Create a Virtual Environment (optional but heroic)**  
   ```bash
   python -m venv .venv
   source .venv/bin/activate   # on Windows: .venv\Scripts\activate
   ```

3. **Install Dependencies**  
   ```bash
   pip install -r requirements.txt
   # If a requirements file is missing, install manually:
   pip install openai python-dotenv validators beautifulsoup4 pycountry
   ```

4. **Add Your OpenAI API Key**  
   - Create a `.env` file in the repo root.  
   - Add: `OPENAI_API_KEY=sk-proj-...` (your secret key).  

5. **Run the Notebook**  
   ```bash
   jupyter notebook "Project Multilingual Web Brochure Generator.ipynb"
   ```
   - Follow the prompts:  
     1. **Enter the website URL** (e.g., `https://huggingface.co/`).  
     2. **Provide the company name** (for a personal touch).  
     3. **Pick a target language code** (e.g., `JPN` for Japanese).  

6. **Watch the Magic**  
   - The notebook will fetch links, decide which are relevant, scrape them, and finally generate a markdown brochure.  
   - You’ll see a nicely formatted output, plus a **streaming version** if you run the streaming cell.

## 🧩 Example Run (A Mini‑Story)

```
Enter the Website: https://huggingface.co/
Valid Website
Enter the name of the Company :  Hugging Face
Enter the language:  JPN
Valid Language
```

Result: A sleek English brochure followed by a **Japanese version full of playful idioms**—ready to copy into a PDF or web page.

## 🤝 Contributing (Join the Narrative)

We welcome fellow storytellers, data wranglers, and AI enthusiasts! Here’s how you can add a chapter:

1. **Fork the repo** and clone your fork.
2. Create a feature branch: `git checkout -b feature/your‑awesome‑idea`.
3. Make improvements—maybe:
   - Support for additional LLMs (Claude, Gemini).  
   - A CLI wrapper for non‑notebook use.  
   - Caching of scraped pages to avoid redundant requests.  
4. Add or update tests (we use `pytest` for core functions).  
5. Submit a Pull Request with a clear description of the narrative change.

## 📜 License

This project is licensed under the MIT License – see the `LICENSE` file for details.

## 🌐 Where to Find More

- **Original Notebook** – `Project Multilingual Web Brochure Generator.ipynb`  
- **Issue Tracker** – Report bugs or suggest features in the GitHub Issues tab.  
- **Community** – Join the discussion on the repository’s Discussions page or ping the author on Twitter/LinkedIn.

---

*From a single URL to a multilingual brochure, Web Brochure Builder is your silent partner in turning web noise into a concise, compelling story. Grab the code, feed it a site, and let the AI spin the yarn.*  

Happy building! 🚀

