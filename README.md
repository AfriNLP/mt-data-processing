# Multilingual Parallel Corpus Pipeline

A unified pipeline to download, preprocess, assess the quality, merge, and push multilingual parallel corpora to the Hugging Face Hub. Supports sources like Hugging Face Datasets, GitHub, and OPUS.

---

## ✨ Features

- **Download datasets from**:
  - Hugging Face Hub: Supports downloading datasets in various formats from the Hugging Face Hub. Specify the dataset format and provide the required details, such as column names.
  - GitHub : Supports downloading datasets hosted on GitHub or any publicly accessible URL. Provide the source URL and the target destination.
  - OPUS : Supports downloading OPUS datasets by specifying the dataset name and URL. You can also retrieve information about all available OPUS datasets using the `opus_info` function (available in `utils.py`).
- **Preprocessing**:
  - Rule-based filtering: involves deduplication, dropping empty segments, and removing HTML tags.
  - Semantic filtering: evaluates the translation pairs with cosine similarity scores derived from sentence embedding models, using the SentenceTransformers library 
  - Language detect filtering: discards segments that are unlikely to be in the expected language. Two language models are available; AfroLid and FastText. 
  - Quality estimation filtering:  apply reference-free evaluation of the translation using Comet models and exclude segments that are lower than the threshold. 
- **Quality Assessment**:
  - Estimates the translation quality of the preprocessed dataset using Comet model. 
- **Merge, Deduplicate and Push**:
  - Dedulicate agains test dataset to avoid overlaps
  - Combine all processed datasets into one
  - Deduplicate globally to avoid same segments from datasets
  - Push to Hugging Face Hub

---

## 🚀 Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/amaneth/mt-data-processing.git
   cd mt-data-processing
2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
3. **Configure settings**
    Modify the `config.yaml` file to define your language pair, data sources, and pipeline settings.
4. **Preprocess the dataset**
    ```bash
    python process.py --config config.yaml

5. **Push to Hugging Face Hub**
    ```bash
    python push_to_hub.py --dataset data


## 🛠️ `config.yaml` Overview

The `config.yaml` file controls the entire pipeline. Here’s an overview of its sections:

### `dataset`
- `lang_pair`: Source and target languages (e.g., `en-am`)
- `sources`: List of dataset types to include (`hf`, `github`, `opus`)

#### Hugging Face datasets (`hf`)
- `name`: Identifier for the dataset
- `path`: HF dataset ID
- `split`: Train/test/dev
- `config_name`: Config name if needed
- `src_col` / `tgt_col`: Source and target language fields

#### GitHub datasets (`github`)
- `name`: Identifier for the dataset
- `src_url`: URL to the source file
- `tgt_url`: URL to the target file

#### OPUS datasets (`opus`)
- `name`: Identifier for the dataset
- `url`: Download URL

---

### `preprocessing`
- `pipelines`: List of preprocessing steps, e.g., `rule_filter`, `semantic_filter`
- `from_cache`: If true, it checks if the dataset is already preporcessed in the `save_dir` and skips preprocessing"

### `filters`
- **Rule filter**:
  - `min_length`, `max_length`
  - `max_length_ratio`
- **Semantic filter**:
  - `threshold`: Similarity threshold
  - `chunk_size`: Batch size for filtering
- **Language detect filter**:
  - `batch_size`: Batch size for fasttext processing
  - `min_score`: threshold value for filtering

---

### `output`
- `prefix`: Prefix for filtered dataset files
- `format`: Final dataset format (e.g., `json`, `csv`, `parquet`)
- `save_dir`: Output directory for saving results

---

### `logging`
- `log_file`: Log filename
- `log_dir`: Directory for storing logs
- `level`: Log level (`INFO`, `DEBUG`, etc.)



