# RAG: Baseline Retrieval vs. Cross-Encoder Re-Ranking

## Overview

This project implements and compares two Retrieval-Augmented Generation (RAG) pipelines using the **EU Artificial Intelligence Act (EU AI Act)** as the source document.

The objective is to evaluate whether Cross-Encoder re-ranking improves the relevance of retrieved passages and the quality of generated answers compared with baseline retrieval.

## Methodology

The pipeline consists of the following steps:

1. **Document preprocessing:** Extract text from the EU AI Act PDF, clean it, and split it into overlapping chunks.
2. **Baseline retrieval:** Use a Bi-Encoder to generate embeddings and FAISS to retrieve the top 10 relevant chunks.
3. **Re-ranking:** Use a Cross-Encoder to re-score the top 10 candidates and select the top 3 chunks.
4. **Answer generation:** Provide the selected chunks and query to a local instruction-tuned language model.
5. **Evaluation:** Compare retrieved passages, generated answers, failure cases, and retrieval latency.

## Models and Tools

| Component | Model / Tool |
|---|---|
| Embeddings | `BAAI/bge-base-en-v1.5` |
| Vector search | FAISS (`IndexFlatIP`) |
| Re-ranking | `cross-encoder/ms-marco-MiniLM-L6-v2` |
| Answer generation | `Qwen/Qwen2.5-1.5B-Instruct` |
| Framework | Python, Hugging Face Transformers, Sentence Transformers |

## Dataset

The source document is the EU Artificial Intelligence Act, Regulation (EU) 2024/1689.

- Pages: 144
- Extracted text: approximately 599,631 characters
- Non-empty chunks: 300
- Chunk size: 450 words
- Chunk overlap: 80 words

## Retrieval Configuration

- Initial candidates retrieved: **Top 10**
- Final context passages: **Top 3**
- Embeddings: Normalized
- Similarity search: Inner product using FAISS

The Bi-Encoder efficiently retrieves candidate passages. The Cross-Encoder then evaluates query–passage relevance more directly to improve the ranking of the final context.

## Failure-Mode Analysis

The pipelines are compared using three queries designed to test different retrieval challenges:

1. **Prohibited practices and exceptions:** Tests retrieval of prohibited AI practices and the handling of legal exceptions.
2. **Narrow procedural or preparatory tasks:** Tests retrieval of conditions relevant to high-risk AI classification.
3. **Deployer versus provider obligations:** Tests whether the system distinguishes between the responsibilities of deployers and providers of high-risk AI systems.

The evaluation considers both retrieval relevance and whether the generated answers are supported by the retrieved context.

## Results

Cross-Encoder re-ranking improved the relevance of the retrieved context in some cases, with the clearest improvement observed for the query about deployer obligations.

However, re-ranking did not consistently eliminate unsupported claims or errors in interpreting legal exceptions.

### Average Retrieval Latency

| Measurement | Average time |
|---|---:|
| Baseline retrieval | 0.0317 s |
| Cross-Encoder re-ranking | 0.4305 s |
| Combined pipeline | 0.4623 s |

*Latency measurements exclude language-model answer generation.*

## Installation

Clone the repository:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_REPOSITORY_FOLDER>
```

Install the required libraries:

```bash
pip install torch transformers sentence-transformers faiss-cpu pymupdf
```

Depending on the notebook implementation, additional dependencies may be required.

## Running the Project

1. Open the Jupyter notebook included in the repository.
2. Ensure the EU AI Act PDF is available at the path configured in the notebook.
3. Run the notebook cells in order to preprocess the document, build the FAISS index, and initialize the models.
4. Execute the baseline and re-ranking pipelines.
5. Review the generated answers, failure-mode analysis, and latency measurements.

The first run may take longer because the required models need to be downloaded.

## Author

**Jyothika Goparaju**  
Roll No.: 2025701007
