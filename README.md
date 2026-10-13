# PDF Question Answering with RAG (LangChain + Chroma + Gradio)

A Colab notebook that builds a retrieval-augmented question-answering bot over a PDF, step by step: load,
chunk, embed, store, retrieve, generate, and serve it in a Gradio UI. The example document is an 11-page
paper on LoRA (low-rank adaptation for LLMs).

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-0.3-1C3C3C)
![Chroma](https://img.shields.io/badge/Chroma-vector%20store-FF6B6B)
![Gradio](https://img.shields.io/badge/Gradio-UI-F97316?logo=gradio&logoColor=white)
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Francis-Gallo/GenerativeAI-RAG-QA-Bot/blob/main/AI%20RAG%20chat%20assistant.ipynb)

## Pipeline

| Step | What the notebook uses | Result on the example PDF |
|---|---|---|
| 1. Load | `PyPDFLoader` | 11 pages |
| 2. Split | `RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)` | 38 chunks |
| 3. Embed | `sentence-transformers/all-MiniLM-L6-v2` | 384-d vectors |
| 4. Store | in-memory `Chroma` collection `research_paper` | 38 documents |
| 5. Retrieve | similarity search, `k=3` | top-3 chunks per question |
| 6. Generate | `google/flan-t5-large` through a Hugging Face `text2text-generation` pipeline (CPU) | answer grounded in the retrieved context (truncated to 3,000 chars) |
| 7. Serve | `gradio.Interface` | text box → answer |

## Run it

1. Open the notebook in Colab with the badge above.
2. Upload the PDF to `/content/` (or change `pdf_path` to your own document).
3. Run all cells. Gradio prints a temporary public `*.gradio.live` link.

## What I learned

- Chunk size and overlap matter as much as the model for small documents.
- A small seq2seq model (FLAN-T5) with a short, explicit context prompt is enough for factual questions about one paper.
- Keeping retrieval and generation separate makes it easy to swap the embedding model or the LLM.

## Possible improvements

- Show the source chunks and page numbers with each answer
- Persist the Chroma store and support uploading files from the UI
- Add a small evaluation set (question → expected passage) to measure retrieval hit rate
- Move from the deprecated `langchain.*` imports to `langchain_community` / `langchain_huggingface`

## Credits

The example PDF is a third-party paper on LoRA. Credit its original authors, or replace the bundled copy with a link
to the source (for example the original LoRA paper, [arXiv:2106.09685](https://arxiv.org/abs/2106.09685)).
