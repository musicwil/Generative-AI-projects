# Amazon Video Game Reviews — LLM Sentiment Analysis (1999–2014)

> **TL;DR**: We used a Large Language Model (LLM) to classify Amazon video game reviews as **positive** or **negative**. A few-shot prompt outperformed a zero-shot baseline on our filtered subset of the dataset. The project is fully reproducible and includes optional enrichment to map ASINs to product titles.

---

## 🎯 Project Goals

* Build a transparent, reproducible **sentiment analysis** pipeline for Amazon video game reviews.
* Compare **zero-shot** vs **few-shot** LLM prompting.
* Produce a concise, hiring-manager-friendly narrative while keeping technical rigor for evaluators.
* Optionally enrich records by resolving **ASIN → product title** via search.

---

## 📦 Dataset

* **Source**: Hugging Face — `LoganKells/amazon_product_reviews_video_games`
* **Coverage**: October 14, 1999 → July 22, 2014 (per dataset timestamps)
* **Subset used**: `data_100min` (filtered), **5,674 reviews** after preprocessing
* **Notable fields** (original dataset has 10 columns):

  * `asin` – Amazon Standard Identification Number
  * `overall` – Star rating (1–5)
  * `reviewText` – Free-text review
  * `summary` – Short review headline
  * `reviewerID`, `reviewerName` – Reviewer metadata
  * `unixReviewTime` – Review time (Unix epoch seconds)
  * `vote`, `style` – Optional metadata fields
  * `reviewTime` – Human-readable time string

> See the notebook for exact schemas and any casting/cleaning steps.

---

## 🧰 Tech Stack

* **Language**: Python 3.11
* **Notebook**: Jupyter / Colab compatible
* **LLM**: OpenAI GPT family (API)
* **Search (optional)**: Tavily API for ASIN→title lookup
* **Data**: Hugging Face Datasets or local parquet/csv

---

## 🔄 Workflow Overview

1. **Load & filter** dataset (drop empties/dupes, basic normalization).
2. **Label definition**: binary sentiment (`positive`/`negative`).
3. **Zero-shot baseline**: direct instruction prompt.
4. **Few-shot prompt**: inject selected examples from a manually curated set of 19 “gold” reviews to anchor model behavior.
5. **Evaluation**: manual qualitative review; identify and log confusion points for analysis.
6. **ASIN enrichment (optional)**: attempt product title retrieval via search.
7. **Artifacts**: optional — export predictions and illustrative tables (top/bottom reviews).

---

## 🧪 Prompting Strategy (High Level)

* **System message**: task framing, concise label set, output schema.
* **Zero-shot**: clear definitions + constraints + 1-line output contract.
* **Few-shot**: representative positive/negative examples (short, non-leaky), plus a reiteration of the output schema to reduce drift.

> Few-shot generally reduced edge-case flips and stabilized outputs on mixed-polarity texts (e.g., positive gameplay with negative shipping).

---

## 📈 Results (Summary)

* **Qualitative**: Few-shot yielded crisper separations on borderline reviews and fewer inconsistencies across reruns.
* **Evaluation method**: No numeric metrics were computed because the dataset lacked gold sentiment labels; performance was judged via manual spot checks and qualitative comparison.
* **Illustratives**: Top-10 most positive and bottom-10 most negative examples are displayed for auditability.

---

## ⚙️ Reproducibility

### 1) Environment

```bash
# create & activate environment
conda create -n vg-llm python=3.11 -y
conda activate vg-llm

# install deps
pip install -r requirements.txt
```

**requirements.txt (excerpt)**

```
huggingface_hub
datasets
pandas
numpy
matplotlib
openai
python-dotenv
tqdm
```

### 2) Secrets

Create a `.env` file (do **not** commit it):

```
OPENAI_API_KEY=sk-...
TAVILY_API_KEY=tvly-...  # optional, only if doing ASIN→title enrichment
```

### 3) Run notebook

Open `notebooks/amazon_video_games_sentiment.ipynb` and run all cells.

### 4) Outputs (optional)

* `processed_predictions.parquet` — predictions and key fields (if exported)
* `figures/*.png` — charts for README/blog (if generated)

---

## 🔎 ASIN → Title Enrichment (Optional)

We attempt to map `asin` values to human-readable **product titles** using a web search API. Because some ASINs refer to peripherals (e.g., memory cards) or bundles rather than games, the enrichment includes simple heuristics and a manual review checklist.

**Known limitations**

* Some ASINs resolve to accessories, not games.
* Search snippets may contain near-matches; minimal post-processing is applied.
* We log ambiguous cases for manual verification.

---

## 🧹 Data Cleaning Notes

* Removed rows with empty `reviewText` or non-informative content.
* Normalized whitespace and basic punctuation quirks.
* For the `data_100min` subset, retained longer reviews to reduce label ambiguity.

---

## 📝 How to Cite

> Logan Kells. *Amazon Product Reviews — Video Games*. Hugging Face: `LoganKells/amazon_product_reviews_video_games`.

---

## 🔐 Security & Privacy

* No raw credentials committed. Use `.env` and `.gitignore`.
* API usage is limited to text classification and optional search queries.

---

## 📄 License

This project is licensed under the **MIT License**. See `LICENSE` for details.

---

## 🙌 Acknowledgments

* Hugging Face community for dataset hosting
* OpenAI models for LLM classification
* Tavily for optional search enrichment

---

## 📮 Contact

Questions or feedback? Open an issue or reach out via the website.

