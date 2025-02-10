# MultiModalRagForPapers

**MultiModalRagForPapers** is an advanced research paper processing and Retrieval-Augmented Generation (RAG) pipeline designed to extract, process, and analyze multimodal content—text, tables, and images—from research papers (PDFs). By leveraging latent summarization as a feature extraction mechanism, this system creates compact, semantically rich representations for downstream retrieval and question answering tasks.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Technical Details](#technical-details)
  - [PDF Partitioning and Element Extraction](#pdf-partitioning-and-element-extraction)
  - [Latent Summarization as Feature Extraction](#latent-summarization-as-feature-extraction)
  - [Multi-Modal Processing](#multi-modal-processing)
  - [Vector Store Integration and RAG Pipeline](#vector-store-integration-and-rag-pipeline)
  - [Evaluation Metrics and Methodology](#evaluation-metrics-and-methodology)
- [Installation](#installation)
- [Setup & Configuration](#setup--configuration)
- [Usage](#usage)
  - [Processing Research Papers](#processing-research-papers)
  - [Summarization and Latent Feature Extraction](#summarization-and-latent-feature-extraction)
  - [Retrieval and Question Answering](#retrieval-and-question-answering)
  - [Evaluation](#evaluation)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

MultiModalRagForPapers processes research paper PDFs—such as landmark works on Transformer architectures—by extracting their text, tables, and images, then transforming these elements into latent representations via summarization. These latent features are embedded into a vector store to support efficient retrieval-augmented generation, enabling advanced Q&A and content analysis over the multimodal data.

---

## Key Features

- **Multi-Modal Data Extraction:**  
  Extracts text, tables, and images from PDFs with fine-grained control over chunking and metadata preservation.
  
- **Advanced Element Processing:**  
  Utilizes the [unstructured](https://github.com/Unstructured-IO/unstructured) library for enhanced table handling, image extraction (with base64 encoding), and text partitioning based on titles and other heuristics.
  
- **Latent Summarization:**  
  Applies summarization chains that generate concise, latent feature representations for each extracted element (text, tables, images). These summaries capture key insights and serve as the semantic backbone for downstream retrieval.
  
- **Image Caption Analysis:**  
  Processes images along with their captions using generative models (e.g., Google Generative AI via Gemini) to extract detailed analyses of diagrams, graphs, and annotated visual content.
  
- **Vector Store Integration:**  
  Embeds latent summaries using `VoyageAIEmbeddings` (e.g., with the `voyage-3` model) into dense vector representations and indexes them in a Chroma vector store coupled with an in-memory document store for efficient multi-modal retrieval.
  
- **Retrieval-Augmented Generation:**  
  Integrates latent features from text, tables, and images to answer complex queries by combining information across modalities.
  
- **Comprehensive Evaluation:**  
  Evaluates the performance on texts, images, and tables both individually and in a multimodal setting using various metrics.

---

## Technical Details

### PDF Partitioning and Element Extraction

- **Advanced Chunking:**  
  The system leverages `unstructured.partition.pdf` with parameters tuned for high-resolution extraction. This includes:
  - **Granular Chunking:** Using strategies like `by_title` with custom character limits to maintain context coherence.
  - **Metadata Extraction:** Capturing page numbers, coordinates, and element types to aid in subsequent processing and retrieval.
  
- **Element Differentiation:**  
  Separates composite elements into:
  - **Text Blocks:** Narrative and structured text.
  - **Tables:** HTML representations enriched with adjacent captions.
  - **Images:** Base64-encoded images along with metadata such as captions and visual coordinates.

### Latent Summarization as Feature Extraction

- **Summarization Chains:**  
  Each extracted element (text, table, or image) is processed through an LLM-driven summarization chain (e.g., via ChatGroq or ChatGoogleGenerativeAI).  
- **Latent Features:**  
  The summaries serve as latent features that capture the essential semantics and numerical relationships, making them ideal for downstream retrieval tasks.

### Multi-Modal Processing

- **Text:**  
  Maintains essential numerical data and context, ensuring that summaries capture key findings and contributions.
  
- **Tables:**  
  Converts tables into HTML representations with associated captions. Summaries focus on relational data, key metrics, and numerical insights.
  
- **Images:**  
  Processes images by extracting their base64 strings and captions, then applies generative analysis to detail:
  - Figure numbers and labels.
  - Data flow, annotations, and color schemes.
  - Mathematical formulas and architectural details.

### Vector Store Integration and RAG Pipeline

- **Embedding:**  
  Latent summaries are embedded into dense vector representations using `VoyageAIEmbeddings`.
  
- **Vector Store:**  
  Documents are indexed in a Chroma vector store integrated with an in-memory document store for efficient multi-modal retrieval.
  
- **Multi-Modal Retrieval:**  
  A custom retriever combines latent features from all modalities, enabling complex queries to retrieve contextually relevant information from processed research papers.

### Evaluation Metrics and Methodology

- **Text Evaluation:**  
  - **Synthetic Data:** Uses synthetic text-based queries and corresponding reference answers to evaluate text summarization and retrieval performance.
  - **Metrics:** Precision, Recall, BLEU, ROUGE-L, and BERTScore are computed to assess semantic similarity and accuracy.
  
- **Images and Tables Evaluation:**  
  - **Targeted Q&A:** Evaluation on images and tables is performed through dedicated question-answering tasks where queries are designed to be answered solely based on the visual or tabular data.
  - **Metrics:** Similar metrics (BLEU, ROUGE-L, BERTScore) are used to evaluate the quality of the generated answers.
  
- **Multimodal Evaluation:**  
  - **Combined Context:** In addition to individual evaluations, the system is tested on multimodal queries where the context comprises both texts and visual elements (images/tables).
  - **Analysis:** This provides insights into how well the system integrates latent features from multiple modalities for coherent and accurate responses.

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
  Configure your environment (or create a `.env` file) with the following keys:

  ```bash
  export GOOGLE_API_KEY="your-google-api-key"
  export GROQ_API_KEY="your-groq-api-key"
  export LANGCHAIN_API_KEY="your-langchain-api-key"
  export LANGCHAIN_TRACING_V2="true"
  ```

- **API Keys:**  
  Replace the placeholder values with your actual API keys in the scripts as needed.

---

## Usage

### Processing Research Papers

- **PDF Extraction:**  
  Place your research paper PDFs (e.g., `attention_is_all_you_need.pdf`) in the designated directory.  
  Run the extraction script to partition the PDF into text, table, and image elements:

  ```bash
  python process_pdf.py
  ```

### Summarization and Latent Feature Extraction

- **Generate Latent Summaries:**  
  Each extracted element is summarized using LLM chains. The summaries are used as latent features that are embedded and indexed in the vector store.
  
- **Rate Limit Management:**  
  The code includes delays (using `time.sleep()`) to accommodate API rate limits. Adjust these delays as necessary.

### Retrieval and Question Answering

- **Query Pipeline:**  
  Use the provided query script to run multi-modal questions against the vector store:

  ```bash
  python query_pipeline.py
  ```

- **RAG Output:**  
  The system retrieves the most relevant latent features and composes responses that integrate both textual and visual contexts.

### Evaluation

- **Individual Evaluations:**
  - **Text:**  
    Evaluation on synthetic text-based queries, comparing generated answers to reference answers using precision, recall, BLEU, ROUGE-L, and BERTScore.
  - **Images and Tables:**  
    Evaluation through targeted question-answering tasks where queries are designed specifically for images or tables. The answers are assessed using the same evaluation metrics.
  
- **Multimodal Evaluation:**  
  For combined evaluation, queries that integrate both texts and visual elements are used to test the overall retrieval and generation performance.
  
- **Run Evaluation:**

  ```bash
  python evaluate.py
  ```

---

## Contributing

Contributions to improve processing algorithms, extend multimodal support, or enhance retrieval performance are welcome. Please open an issue or submit a pull request with your suggestions or fixes.

---

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for details.
```

