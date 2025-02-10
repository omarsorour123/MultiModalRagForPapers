# Transformer Research Paper Processing & Retrieval-Augmented Generation (RAG) Pipeline

This project demonstrates an end-to-end pipeline for processing research papers (PDFs) on Transformer architectures, extracting textual content, tables, and images, and then using large language models (LLMs) for summarization, retrieval, and question answering. The pipeline leverages multiple libraries and APIs including LangChain, VoyageAI, unstructured, and Google’s Generative AI to build a robust retrieval-augmented generation system.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Requirements](#requirements)
- [Installation](#installation)
- [Setup & Configuration](#setup--configuration)
- [Usage](#usage)
  - [PDF Processing & Element Extraction](#pdf-processing--element-extraction)
  - [Summarization of Text and Tables](#summarization-of-text-and-tables)
  - [Image Analysis with Captions](#image-analysis-with-captions)
  - [Building the Vector Store & RAG Pipeline](#building-the-vector-store--rag-pipeline)
  - [Evaluation Metrics](#evaluation-metrics)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project processes a PDF (e.g., the "Attention Is All You Need" paper) by:
- Extracting text, tables, and images using the [unstructured](https://github.com/Unstructured-IO/unstructured) library.
- Summarizing extracted elements with LLMs (using models such as `voyage-3` via `langchain_voyageai` and `llama-3.2-90b-vision-preview`).
- Using image analysis to extract information from figures with Google Generative AI.
- Indexing summaries and raw elements in a vector store (Chroma) for retrieval-augmented question answering.
- Evaluating the system using metrics like BLEU, ROUGE, and BERTScore.

## Features

- **PDF Partitioning:** Breaks down research papers into coherent chunks based on titles and sections.
- **Element Extraction:** Distinguishes between text, tables, and images, and extracts metadata such as coordinates and captions.
- **Summarization:** Uses LLMs to generate concise summaries for each extracted element.
- **Image Analysis:** Analyzes images by supplying both image data (as base64) and captions to generate detailed descriptions.
- **Vector Store Integration:** Uses a multi-vector retrieval system (Chroma with an in-memory document store) to enable efficient retrieval-augmented generation.
- **Evaluation:** Computes retrieval and generation evaluation metrics (precision, recall, BLEU, ROUGE-L, and BERTScore) against a sample dataset.

## Architecture

1. **Input Processing:**  
   - A PDF is processed via `unstructured.partition.pdf` with fine-tuned parameters for better image/table handling.
   - The PDF is split into chunks that contain composite elements.

2. **Extraction & Summarization:**  
   - Tables and texts are extracted along with their metadata.
   - Summaries for text chunks and tables are generated using a prompt-driven LLM chain.
   - Images are analyzed using a two-step process: image extraction and caption-based analysis via Google Generative AI.

3. **Retrieval-Augmented Generation:**  
   - Summaries and raw content are indexed into a vector store.
   - A custom retrieval chain is built that combines both text and images.
   - The chain is then used to answer queries based on the provided context.

4. **Evaluation:**  
   - A set of queries are processed to compute retrieval quality (precision and recall) and generation quality (BLEU, ROUGE, BERTScore).

## Requirements

- Python 3.8+
- API Keys for:
  - **Google Generative AI:** (set as `GOOGLE_API_KEY`)
  - **VoyageAI, GROQ, LANGCHAIN:** (set corresponding environment variables)
- Dependencies (see [Installation](#installation) section)

## Installation

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/your-username/transformer-rag-pipeline.git
   cd transformer-rag-pipeline
   ```

2. **Create a Virtual Environment and Activate It:**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use: venv\Scripts\activate
   ```

3. **Install Dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

   > **Note:** Ensure that your `requirements.txt` includes libraries such as:
   > - `langchain`
   > - `unstructured`
   > - `langchain_voyageai`
   > - `langchain_google_genai`
   > - `langchain_groq`
   > - `chroma`
   > - `evaluate`
   > - `datasets`
   > - `tenacity`
   > - and any other dependency your code requires.

## Setup & Configuration

1. **Environment Variables:**  
   Set up the following environment variables (e.g., in your shell profile or a `.env` file):

   ```python
   os.environ["GOOGLE_API_KEY"] = "your-google-api-key"
   os.environ["GROQ_API_KEY"] = "your-groq-api-key"
   os.environ["LANGCHAIN_API_KEY"] = "your-langchain-api-key"
   os.environ["LANGCHAIN_TRACING_V2"] = "true"
   ```

2. **API Keys:**  
   Replace placeholder values with your actual API keys in the code as needed.

## Usage

### PDF Processing & Element Extraction

- The code partitions a PDF (e.g., `attention_is_all_you_need.pdf`) using the `partition_pdf` function with custom parameters for:
  - Improved table extraction.
  - Granular chunking by titles.
  - Metadata inclusion (e.g., page numbers, coordinates).

### Summarization of Text and Tables

- **Text Summarization:**  
  A prompt template is used with a LLM (via ChatGroq) to generate concise summaries of text chunks.
  
- **Table Summarization:**  
  Tables are processed similarly by extracting HTML representations and any associated captions before summarization.

### Image Analysis with Captions

- Images are processed by extracting the base64 string and caption.
- A dedicated function sends the image (with caption) to the Gemini model (via `ChatGoogleGenerativeAI`) for detailed analysis.

### Building the Vector Store & RAG Pipeline

- Summaries and original elements are stored in a Chroma vector store.
- A custom retrieval chain is built using a multi-vector retriever and an in-memory document store.
- Queries are answered based on both textual and visual context retrieved from the vector store.

### Evaluation Metrics

- The pipeline includes an evaluation script that:
  - Retrieves relevant context based on sample queries.
  - Generates answers and evaluates them using:
    - **Retrieval Metrics:** Precision and Recall (token-level matching).
    - **Generation Metrics:** BLEU, ROUGE-L, and BERTScore (using Hugging Face’s `evaluate` library).

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for improvements or bug fixes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

> **Disclaimer:** This README is provided as a starting point. Modify the instructions and sections as needed to better reflect your project’s structure, dependencies, and usage instructions.
```
