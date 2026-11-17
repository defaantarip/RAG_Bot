# RAG

A small retrieval-augmented generation script that answers questions from local Markdown files. It embeds the documents with Ollama, retrieves the closest passages for each question, and asks a local chat model to answer using only those passages.

The sample corpus is an employee handbook for Byte Bistro, a coffee shop in Austin, in `docs/handbook.md`.

## Requirements

- Python 3.13
- [uv](https://docs.astral.sh/uv/)
- [Ollama](https://ollama.com/) running at `http://localhost:11434`

Pull the two models the script calls:

```powershell
ollama pull nomic-embed-text
ollama pull qwen3:4b-instruct
```

## Setup

From the project directory:

```powershell
uv sync
```

## Run

```powershell
uv run rag.py
```

The script loads every `*.md` file in `docs/`, embeds the paragraphs, then waits for a question:

```text
Ask: What time does the cafe open on Saturday?
```

Type a question and press Enter. The answer is printed, then the prompt returns. Stop it with Ctrl+C.

To ask about your own material, put Markdown files in `docs/`. Paragraphs separated by a blank line become the chunks that are searched.

## How it works

1. Each Markdown file in `docs/` is split on blank lines into chunks.
2. `nomic-embed-text` turns every chunk into a vector through Ollama's `/api/embed`.
3. The same model embeds the question. Cosine similarity ranks the chunks, and the top 3 are kept.
4. Those passages are sent to `qwen3:4b-instruct` with instructions to answer only from that context. If the context does not contain the answer, the model is told to say "I don't know".

Model names and the Ollama URL are set at the top of `rag.py`.
