# 🛡️ Insurance Reviews NLP Insights - Final Project

An end-to-end NLP pipeline and interactive dashboard for analyzing ~35,000 French insurance reviews. This project covers the entire machine learning lifecycle, from advanced cleaning and unsupervised discovery to high-performance supervised classification and RAG-based search.

## 📊 Overview
This project was developed to provide deep insights into customer sentiment across 40+ French insurers. It addresses the common "Neutral class challenge" in sentiment analysis and features a side-by-side comparison of classical ML, deep learning, and transformer architectures.

### Key Features
- **Data Cleansing**: Spacy-led lemmatization and LanguageTool spelling correction.
- **Unsupervised Insights**: Topic Modeling (LDA), Anomaly Detection (Isolation Forest), and custom Word2Vec/GloVe embeddings.
- **Supervised Intelligence**: Real-time sentiment predictor comparing:
  - **Ridge Regression** (Star rating prediction as a continuous value)
  - **Bi-LSTM** (Keras-based neural architecture with GloVe)
  - **CamemBERT** (State-of-the-Art French Transformer)
- **Explainability**: SHAP and LIME integration to interpret model predictions.
- **Hybrid Search & RAG**: Combined Keyword (BM25) and Semantic (FAISS/Sentence-BERT) search with LLM-powered (Qwen 2.5) response synthesis.

---

## 🛠️ Tech Stack
- **Dashboard**: [Streamlit](https://streamlit.io/)
- **NLP Libraries**: `Spacy`, `LanguageTool`, `NLTK`, `TextBlob`
- **Machine Learning**: `Scikit-Learn`, `TensorFlow/Keras`
- **Transformers**: `CamemBERT`, `Sentence-Transformers`
- **Large Language Model**: `Qwen 2.5 (1.5B)` for RAG and Summarization
- **Vector Search**: `FAISS`, `Rank-BM25`

---

## 🚀 Quick Start

### Installation
1. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   python3 -m spacy download fr_core_news_sm
   ```

2. Generate the processed data and visuals from the notebook:
   - Run `NLP2026_Project2.ipynb` to populate the `outputs/` directory.

### Running the App
Launch the Streamlit dashboard:
```bash
streamlit run app.py
```

---

## 📁 Project Structure
- `app.py`: Main Streamlit application.
- `code/`: Modular Python scripts for:
  - `preprocessing.py`: Cleaning & Lemmatization.
  - `unsupervised.py`: LDA & Anomaly Detection.
  - `supervised.py`: Model training & Inference.
  - `analysis.py`: SHAP explanations & RAG logic.
- `data/`: Raw Excel files (individual insurer reviews).
- `outputs/`: Pre-processed datasets (`reviews_clean.csv`), trained models, and visualizations.

---

## 📉 Core Findings
- **The "Neutral" Problem**: 3-star reviews are often linguistically polarized (extreme praise or complaints). Treating star-rating as a regression problem was the most effective workaround.
- **Language Matters**: Using domain-specific French models (CamemBERT) yielded a massive performance jump compared to generic English-based models used on translated text.
- **Customer Trends**: "Rapport Qualité Prix" is the dominant predictor of positive sentiment, while processing delays for "Non-responsible accidents" drive the majority of negative sentiment.
