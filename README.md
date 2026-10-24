# rag-current-affairs-chatbot
Retrieval-Augmented Generation (RAG) chatbot that answers questions about 2026 current affairs in India using Wikipedia, ChromaDB, TinyLlama and a Gradio UI.
# Current Affairs RAG Chatbot

A Retrieval-Augmented Generation (RAG) chatbot that answers questions about 2026 events in India. It collects Wikipedia current-affairs pages, stores them in a ChromaDB vector database, retrieves the most relevant text for a question, and uses TinyLlama-1.1B to write a short answer based only on that text. A Gradio chat interface lets you try it from Google Colab.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<your-username>/<repo-name>/blob/main/rag_pipeline.ipynb)

## Demo

![Demo screenshot](screenshot.png)

Example questions:
- Tell me about IPL 2026
- What happened in India in January 2026?

## How It Works

```
Wikipedia pages -> cleaning -> chunking -> ChromaDB -> retrieval -> TinyLlama -> Gradio UI
```

1. **Data collection:** Downloads Wikipedia pages such as "2026 in India", monthly pages (January to June 2026), "2026 in science", "2026 in politics" and "2026 Indian Premier League" using `wikipedia-api`, and saves them as `.txt` files.
2. **Cleaning:** Removes short and empty lines and attaches the date to event lines that only appear under a date heading.
3. **Chunking:** Each cleaned line becomes one chunk, tagged with its source page title. Duplicate pages are detected with MD5 hashes and skipped.
4. **Vector store:** Chunks are added to a persistent ChromaDB collection using cosine distance and ChromaDB's default embedding model.
5. **Retrieval:** For each question, the top 4 chunks are fetched. If the best match is farther than the distance threshold (0.65), the bot answers "I don't know". Weakly related chunks are filtered out by comparing them with the best match.
6. **Generation:** The retrieved chunks are placed in a prompt for `TinyLlama/TinyLlama-1.1B-Chat-v1.0`, which is told to answer in 2-3 sentences using only the given context.
7. **Interface:** `gr.ChatInterface` provides the chat UI, launched with a temporary public link (`share=True`).

## Tech Stack

- Python, Google Colab
- `wikipedia-api` for data collection
- ChromaDB for vector storage and similarity search
- Hugging Face `transformers` and PyTorch for TinyLlama
- Gradio for the chat interface

## Getting Started

1. Click the **Open in Colab** badge above (use a GPU runtime for faster answers).
2. Run the cells in order. The first cells install the libraries, so you do not need to install anything separately.
3. The notebook mounts Google Drive and stores data at `/content/drive/MyDrive/RAG_Chatbot/`. If you use a different folder, change the paths in the notebook.
4. Run the last cell. Gradio prints a public link you can open to chat with the bot.

To run locally instead:

```bash
pip install -r requirements.txt
```

You will need to change the Google Drive paths to local folders and remove the `drive.mount` cell.

## Project Structure

```
.
├── rag_pipeline.ipynb   # Full pipeline: data, vector DB, model, Gradio app
├── requirements.txt
└── README.md
```

## Limitations

- TinyLlama is a very small model, so answers can be incomplete or occasionally wrong even with good context.
- The knowledge base only covers the Wikipedia pages listed above, so questions outside them return "I don't know".
- Chunks are single lines, so questions that need information spread across several lines may not be answered well.
- The Gradio link comes from Colab and expires when the session ends or after about 72 hours. This is a demo, not a permanent deployment.


## Author

**Anshika** - BTech AIML
