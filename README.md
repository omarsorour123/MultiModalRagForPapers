# MultiModalRagForPapers

**MultiModalRagForPapers** is an advanced research paper processing and Retrieval-Augmented Generation (RAG) pipeline designed to extract, process, and analyze multimodal content—text, tables, and images—from research papers (PDFs). Leveraging latent summarization as a feature extraction mechanism, the system creates compact, semantically rich representations that are then embedded into a vector store. These latent features are used to power efficient retrieval and question-answering over single modalities as well as combined (multi-modal) contexts.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Technical Details](#technical-details)
  - [PDF Partitioning and Advanced Element Extraction](#pdf-partitioning-and-advanced-element-extraction)
  - [Latent Summarization as Feature Extraction](#latent-summarization-as-feature-extraction)
  - [Multi-Modal Processing](#multi-modal-processing)
  - [Vector Store Integration and RAG Pipeline](#vector-store-integration-and-rag-pipeline)
  - [Evaluation Methodology](#evaluation-methodology)
- [Installation](#installation)
- [Setup & Configuration](#setup--configuration)
- [Usage](#usage)
  - [Processing and Extraction](#processing-and-extraction)
  - [Summarization and Latent Feature Extraction](#summarization-and-latent-feature-extraction)
  - [Retrieval and Question Answering](#retrieval-and-question-answering)
  - [Evaluation](#evaluation)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

MultiModalRagForPapers process research paper PDFs—such as the seminal "Attention Is All You Need" paper—by extracting text, tables, and images. Each modality is summarized via dedicated LLM chains that produce latent representations capturing key insights, numerical data, and relationships. These summaries are then embedded into a vector store, enabling efficient retrieval-augmented generation for complex, multimodal queries.

---

## Key Features

- **Advanced Multi-Modal Data Extraction:**  
  - **Text, Tables, and Images:** Uses the [unstructured](https://github.com/Unstructured-IO/unstructured) library to partition PDFs into coherent chunks while preserving metadata (e.g., page numbers, coordinates).
  - **Enhanced Figure/Table Name Extraction:** Implements heuristics to extract figure/table names from adjacent caption/text elements—even spanning across chunks.

- **Latent Summarization:**  
  - Uses summarization chains (with models like `llama-3.2-11b-vision-preview` and `gemini-2.0-flash-exp`) to produce concise, information-dense summaries.
  - These summaries act as latent features, capturing the semantic essence of each extracted element for downstream retrieval.

- **Multi-Modal Retrieval & RAG Pipeline:**  
  - Embeds latent summaries via `VoyageAIEmbeddings` and indexes them in a Chroma vector store (with an in-memory document store) for efficient multi-vector retrieval.
  - Supports both single-modal queries (text, table, or image only) and combined multimodal queries.

- **Comprehensive Evaluation:**  
  - Evaluates text summarization using synthetic queries and reference answers.
  - Separately assesses images and tables through targeted Q&A tasks based solely on visual or tabular data.
  - Includes a multi-modal evaluation where queries require integration of text, table, and image contexts.

---

## Technical Details

### PDF Partitioning and Advanced Element Extraction

- **Granular Chunking:**  
  The pipeline uses `unstructured.partition.pdf` with parameters tuned for improved extraction. The settings include:
  - **Chunking Strategy:** Using "by_title" with custom character limits for coherent context.
  - **Enhanced Table & Image Extraction:** Extracts tables (with HTML representations) and images (with base64 encoding) along with associated metadata.

- **Figure and Table Name Extraction:**  
  A dedicated module checks for caption elements (both above and below the target element, and even in neighboring chunks) to extract standardized names (e.g., "table 2" or "figure 1"). These names are stored as metadata and used during retrieval and evaluation.

### Latent Summarization as Feature Extraction

- **LLM Summarization Chains:**  
  Each extracted element—whether text, table (HTML), or image—is passed through a summarization chain. The output is a concise summary (typically 3–4 sentences for texts and tables, and 10–15 sentences for images) that captures key insights and quantitative details.

- **Semantic Latent Features:**  
  These summaries serve as latent feature vectors once embedded, preserving the essential semantic and contextual information required for effective retrieval.

### Multi-Modal Processing

- **Text:**  
  Processed to capture narrative content and numerical relationships.
  
- **Tables:**  
  Converted into HTML with caption extraction. Summaries focus on relational data and key metrics.

- **Images:**  
  Processed by embedding base64 image data and analyzing associated captions to extract visual and technical details (e.g., annotations, data flow, formulas).

### Vector Store Integration and RAG Pipeline

- **Embedding & Indexing:**  
  Latent summaries are embedded using `VoyageAIEmbeddings` (model "voyage-3") and stored in a Chroma vector store with a linked in-memory document store.
  
- **MultiVector Retriever:**  
  The custom retriever combines documents across modalities, allowing queries to be answered based on integrated context from texts, tables, and images.

### Evaluation Methodology

- **Single-Modality Evaluation:**  
  - **Text Evaluation:** Uses synthetic queries with corresponding reference answers; metrics such as BLEU, ROUGE-L, and BERTScore are computed.
  - **Images and Tables Evaluation:** Dedicated Q&A tasks target visual and tabular data (e.g., retrieving the correct figure/table name).

- **Multi-Modal Evaluation:**  
  For combined queries, the system retrieves and integrates latent features from texts, tables, and images. Evaluation metrics (precision, recall, BLEU, ROUGE-L, BERTScore) are computed to assess overall performance.

---

## Installation

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/omarsorour123/MultiModalRagForPapers.git
   cd MultiModalRagForPapers
   ```

2. **Create and Activate a Virtual Environment:**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

---

## Setup & Configuration

- **Environment Variables:**  
  Create a `.env` file or export the following keys in your shell:

  ```bash
  export GOOGLE_API_KEY="your-google-api-key"
  export GROQ_API_KEY="your-groq-api-key"
  export LANGCHAIN_API_KEY="your-langchain-api-key"
  export LANGCHAIN_TRACING_V2="true"
  ```

- **API Keys:**  
  Replace the placeholder values in the code with your actual API keys where indicated.

---

## Usage

### Processing and Extraction

- **PDF Extraction:**  
  Place your research paper PDFs (e.g., `attention_is_all_you_need.pdf`) in the designated directory. Run the extraction script to partition the PDF into texts, tables, and images:

  ```bash
  python process_pdf.py
  ```

### Summarization and Latent Feature Extraction

- **Summarization Chains:**  
  The code processes:
  - **Texts & Tables:** Using a summarization prompt with `llama-3.2-11b-vision-preview`.
  - **Images:** Using a specialized prompt with the Gemini 2.0 Flash model.
  
  Summaries are generated and used as latent representations for each modality.

### Retrieval and Question Answering

- **Query Pipeline:**  
  Use the provided query scripts to perform retrieval:
  - **Single-Modality Queries:** For texts, tables, or images individually.
  - **Multi-Modal Queries:** Combining context from all modalities.
  
  Example:

  ```bash
  python query_pipeline.py
  ```

### Evaluation

- **Single-Modality Evaluation:**  
  Runs synthetic text queries and targeted Q&A tasks for images and tables.  
- **Multi-Modal Evaluation:**  
  Evaluate retrieval accuracy using a dedicated multimodal dataset (e.g., queries expecting a specific table/figure name).

  Run the evaluation:

  ```bash
  python evaluate.py
  ```

  The evaluation script prints overall metrics (precision, recall, BLEU, ROUGE-L, BERTScore) along with detailed per-query results.

---

## Contributing

Contributions to improve extraction algorithms, extend multimodal support, or enhance evaluation methods are welcome. Please open an issue or submit a pull request with your suggestions or fixes.

---

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for details.

