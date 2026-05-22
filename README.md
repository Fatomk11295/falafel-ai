# Falafel AI 🧆 — Personal Chef Agent

A multimodal AI chef assistant built with LangChain, LangGraph, and Google Gemini. The agent suggests recipes based on the ingredients you have, respects your dietary restrictions, and remembers your preferences across sessions.

---

## What It Does

- **Ingredient-based recipe suggestions** — tell the agent what's in your fridge and it searches the web for matching recipes
- **Allergy awareness** — automatically avoids ingredients you're allergic to
- **Cuisine preferences** — prioritizes recipes from your favourite cuisine styles (e.g. Italian, Asian)
- **Persistent user profile** — your allergies and preferences are saved to a local file and remembered across sessions
- **Image input** — upload a photo of your ingredients and the agent identifies them and suggests recipes

---

## Architecture

```
User Input (text or image)
        │
        ▼
  Falafel AI Agent  ◄──── User Profile (user_profile.json)
  (Gemini 2.5 Flash Lite)
        │
        ├──► web_search          → Tavily Web Search API
        ├──► save_allergies      → user_profile.json
        └──► save_cuisine_prefs  → user_profile.json
        │
        ▼
  Recipe Suggestion
```

**Key components:**

| Component | Role |
|---|---|
| `ChatGoogleGenerativeAI` | The LLM powering the agent (Gemini 2.5 Flash Lite) |
| `create_agent` | LangChain function that wires the model + tools + memory |
| `InMemorySaver` | Keeps conversation history within a session |
| `TavilyClient` | Web search tool for finding real recipes |
| `user_profile.json` | Persistent storage for allergies and cuisine preferences |
| `FileUpload` (ipywidgets) | Widget for uploading ingredient images in the notebook |

---

## Tools

| Tool | Description |
|---|---|
| `web_search` | Searches the web for recipes matching the user's ingredients |
| `save_allergies` | Saves the user's food allergies to the profile file |
| `save_cuisine_preferences` | Saves the user's preferred cuisine styles to the profile file |

---

## Setup

### 1. Clone and install dependencies

```bash
git clone <your-repo-url>
cd langchain-tutorial

# Using uv (recommended)
uv sync

# Or using pip
pip install -r requirements.txt
```

### 2. Set up API keys

Copy the example env file and fill in your keys:

```bash
cp example.env .env
```

Edit `.env` and add:

```
GOOGLE_API_KEY=your_google_api_key_here   # required — get it at aistudio.google.com
TAVILY_API_KEY=your_tavily_api_key_here   # required — get it at tavily.com
```

- **Google API Key**: [aistudio.google.com](https://aistudio.google.com) → Get API key → Create API key (free)
- **Tavily API Key**: [tavily.com](https://tavily.com) → Sign up → free tier available

### 3. Run the notebook

```bash
# Using uv
uv run jupyter lab

# Or with pip (after activating venv)
jupyter lab
```

Open `Falafel_AI.ipynb` and run cells in order from top to bottom.

---

## Usage

**Run cells 1–6 first** to set up the agent, then:

```python
# Tell the agent your profile (first time only — saved for future sessions)
chat("I'm allergic to dairy and nuts, and I love Italian and Asian food")

# Ask for recipe suggestions with text
chat("I have tomato, lettuce, corn, sweet potato. What can I make for dinner?")

# Or upload an image of your ingredients using the file upload widget (Cell 7)
# then run:
chat_with_image("What ingredients do you see? What can I make for dinner?")
```

---

## Project Structure

```
langchain-tutorial/
├── Falafel_AI.ipynb       # Main notebook
├── user_profile.json      # Auto-created — stores your allergies and preferences
├── .env                   # Your API keys (not committed to git)
├── example.env            # Template for .env
├── requirements.txt       # Python dependencies
└── FALAFEL_AI_README.md   # This file
```

---

## Requirements

See `requirements.txt` for the full list. Key dependencies:

```
langchain>=1.1.3
langchain-google-genai>=3.1.0
langgraph>=1.0.3
tavily-python>=0.7.13
python-dotenv>=1.2.0
ipywidgets>=8.1.8
jupyterlab>=4.5.7
```

---

## Notes

- `user_profile.json` is created automatically on first run — do not delete it between sessions or your profile will reset
- Add `user_profile.json` to `.gitignore` before pushing to GitHub if you don't want to share your personal profile
- The agent uses `thinking_budget: 0` for faster responses — remove this line in Cell 5 if you want more careful reasoning at the cost of speed
