# Who Wants to be a PoliMillionaire? — NLP Chatbot

An automated agent that plays the online quiz game *"Who Wants to be a PoliMillionaire?"*
using a locally-run, open-weights language model with retrieval augmentation (RAG),
a speech interface, and LoRA fine-tuning.

## Authorship

This project was carried out as a **group assignment** for the Natural Language Processing course.
The work in **this notebook is my own individual contribution** to that group effort.

## System overview

The agent is built around **Qwen2.5-14B-Instruct** (4-bit quantized, loaded via Unsloth),
wrapped in a layered decision pipeline. For every question it runs, cheapest-and-most-reliable first:

1. **Memory layer** — if the exact question was answered correctly before, reuse it; eliminate options known to be wrong (`game_log.json`).
2. **Pass 1** — a confidence-gated first answer from the model.
3. **RAG** — on low confidence, retrieve from Wikipedia / DuckDuckGo with a relevance gate (spaCy NER + TF-IDF) and answer again.
4. **Pass 2** — a two-vote override with context.
5. **Category learning** — few-shot prompting from logged answers and a LoRA adapter (used for the History category).

A **speech mode** uses a local Whisper model (`faster-whisper`) so the same pipeline can play the game's spoken interface.

## Contents

| File | Description |
|------|-------------|
| `PoliMillionaire.ipynb` | The notebook — full pipeline (model, RAG, speech, fine-tuning, evaluation) |
| `PoliMillionaire.html`  | Rendered HTML export of the notebook |
| `game_log.json`         | Persistent answer-memory log used by the memory / elimination layer |
| `Chatbot Performance.xlsx` | Measured per-category / per-strategy performance |
| `PoliMillionaire_Report.docx` | Technical report |

## How to run

1. Open `PoliMillionaire.ipynb` in Google Colab / Kaggle with a GPU runtime.
2. Run the setup cells (dependencies, game-server login, `millionaire_client`).
3. Load the model, then run the sections you want (gameplay, RAG, speech, fine-tuning, evaluation).
4. The `millionaire_client` package (provided with the course) must be on the Python path.
