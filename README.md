# Categorizing Trends in Science – Clustering arXiv Papers

Case study for **DLBDSMLUSL01 – Machine Learning: Unsupervised Learning and Feature Engineering** (IU International University of Applied Sciences), Task 3.

The project groups arXiv papers published between January 2020 and June 2026 into research topics using unsupervised learning, visualises them in low-dimensional views, and analyses how the topics develop over time.

## Repository structure

```
├── arxiv_topic_clustering.ipynb   # complete, documented pipeline (run top to bottom)
├── requirements.txt               # Python dependencies for local use
├── figures/                       # all figures produced by the notebook
└── README.md
```

The `data/` folder (intermediate results) and the raw dataset are not included because of their size; the notebook recreates them.

## Data

[arXiv Scientific Research Papers Dataset](https://www.kaggle.com/datasets/sumitm004/arxiv-scientific-research-papers-dataset) (Kaggle, 287,421 papers).
Download the CSV and place it in the same folder as the notebook. If the file name differs, adjust `DATA_PATH` in the settings cell.

## How to run

**Google Colab (recommended):** upload the notebook and the CSV, then choose *Runtime → Run all*. The first cell installs `umap-learn`.

**Locally:**
```bash
pip install -r requirements.txt
jupyter notebook arxiv_topic_clustering.ipynb
```

Full runtime is about 45–60 minutes on a standard CPU; the k-selection loop (Section 5.1) takes the longest. All random steps use `SEED = 42`.

## Method overview

| Step | Method | Key settings |
|---|---|---|
| Filtering | Year ≥ 2020, duplicates and abstracts < 20 words removed | 227,389 papers remain |
| Preprocessing | Title + abstract; removal of URLs, LaTeX, punctuation; stopwords incl. academic boilerplate; WordNet lemmatisation | |
| Features | TF-IDF, uni- and bigrams | `min_df=20`, `max_df=0.40`, 50,000 terms, sublinear tf |
| Dimensionality reduction | Truncated SVD (LSA), L2-normalised | 100 components |
| Visualisation | UMAP (cosine) on a 60,000-paper sample | |
| Clustering | K-Means | k chosen from 4 validity indices, inspection and stability check: **k = 25** |
| Trend analysis | Topic share per year; raw and post-stratified (bias-adjusted) | reference mix 2020–2023 |

## Main results

- 25 interpretable research topics, from reinforcement learning and graph neural networks to RAG, LLM agents and medical AI.
- Clustering is stable: Adjusted Rand Index of 0.656 between k = 20 and k = 25, with nine clusters keeping ≥ 88 % of their papers.
- The dataset contains strong sampling artefacts in the arXiv category mix (e.g. Computer Vision in 2024, NLP in 2025), corrected by reweighting.
- Bias-adjusted, the combined share of six LLM-related topics grew from **5.6 % (2020–22) to 32.3 % (2025–26)**, with the sharpest increase in 2023.
- Clusters agree only moderately with arXiv categories (NMI 0.24); 12 of 25 topics, including all LLM topics, have no majority category.

## Note on cluster numbering

Cluster names are assigned manually in Section 6. K-Means numbering depends on library versions and hardware; if the notebook is rerun in a different environment, compare the printed top terms with the names and adjust the `NAMES` dictionary if needed.
