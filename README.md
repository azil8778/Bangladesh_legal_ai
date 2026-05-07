# ⚖️ Bangladesh Legal AI

A Retrieval-Augmented Generation (RAG) system that answers questions about Bangladesh law — citing the exact Act, Section, and source. Built on **Saul-7B-Instruct-v1**, a legally fine-tuned LLM, grounded with **30,061 chunks** extracted from 1,064+ Bangladesh statutes, university law journals, and case studies.

> *"What is the punishment for theft under Bangladesh law?"*
> → The system retrieves the relevant section from the Penal Code and generates a cited answer.

---

## 🧠 Why Saul-7B?

Most legal AI projects use generic LLMs. This project deliberately uses **[Equall/Saul-7B-Instruct-v1](https://huggingface.co/Equall/Saul-7B-Instruct-v1)** — a 7B parameter model specifically fine-tuned on legal corpora. Combined with a FAISS-based RAG layer grounded in Bangladesh law, the system understands both legal language and Bangladesh-specific statutes.

---

## 📊 Dataset at a Glance

| Source | Count |
|---|---|
| Bangladesh government statutes (bdlaws) | 1,064 PDFs |
| Law chunks extracted | 28,602 |
| Academic journal & case study chunks | 1,459 |
| **Total chunks** | **30,061** |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     DATA PIPELINE                        │
│                                                         │
│  bdlaws PDFs  ──►  pdfplumber  ──►  Section Chunker    │
│  Law Journals ──►  Custom Chunker  ──►  JSONL files    │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    RAG PIPELINE                          │
│                                                         │
│  chunks.jsonl  ──►  all-MiniLM-L6-v2  ──►  FAISS      │
│                           │                             │
│   User Question ──► FAISS Search ──► Top-K Chunks      │
│                                           │             │
│                      Saul-7B-Instruct-v1 ◄─┘           │
│                           │                             │
│                    Cited Legal Answer                   │
└─────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| LLM | `Equall/Saul-7B-Instruct-v1` (7B, legal-domain) |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2` |
| Vector Search | FAISS |
| PDF Extraction | pdfplumber |
| Fine-tuning | QLoRA (PEFT + TRL) |
| Hardware | Google Colab T4 GPU |
| Language | Python 3.12 |

---

## 📁 Repository Structure

```
bangladesh-legal-ai/
├── README.md
├── requirements.txt
├── notebooks/
│   ├── 01_pdf_extraction.ipynb       # PDF → cleaned JSONL chunks
│   └── 02_bangladesh_legal_ai.ipynb  # Embeddings + FAISS + Saul-7B RAG
```

---

## 🚀 Reproducing This Project

### Step 1 — Collect Bangladesh Statutes

Download the law PDFs from the official Bangladesh government legal database at [bdlaws.minlaw.gov.bd](http://bdlaws.minlaw.gov.bd), or use the included scraper to collect them automatically.

Then run `notebooks/01_pdf_extraction.ipynb` to extract, clean, and chunk all statutes by section. This will produce a `chunks.jsonl` file (~28,000+ chunks).

### Step 2 — Collect Supplementary Legal Sources

Collect university law journals and case study PDFs from institutions such as:
- **Rajshahi University Law Journal**
- **Jagannath University Law Journal**
- Publicly available Bangladesh court case studies

These are not redistributed here — contact the respective institutions for access. Chunk them based on their formatting using the approach in `01_pdf_extraction.ipynb`, and save as a second JSONL file.

### Step 3 — Build the RAG Pipeline

Once both datasets are chunked and saved as JSONL files, follow `notebooks/02_bangladesh_legal_ai.ipynb` to:

1. Load and merge both JSONL files
2. Embed all 30,000+ chunks using `sentence-transformers/all-MiniLM-L6-v2`
3. Build the FAISS vector index
4. Load `Equall/Saul-7B-Instruct-v1` in 4-bit quantization (fits on T4 GPU)
5. Run the full RAG query pipeline with cited answers

---

## 📦 Installation

```bash
pip install transformers datasets peft trl accelerate bitsandbytes \
            sentence-transformers faiss-cpu pdfplumber tqdm
```

Or install from the requirements file:

```bash
pip install -r requirements.txt
```

> ⚠️ **GPU Required.** Set Google Colab runtime to **T4 GPU** before running the RAG notebook.

---

## 📚 Data Sources

| Source | Type | Access |
|---|---|---|
| [bdlaws.minlaw.gov.bd](http://bdlaws.minlaw.gov.bd) | Government statutes | Publicly available |
| Rajshahi University Law Journal | Academic journal | Via university library |
| Jagannath University Law Journal | Academic journal | Via university library |
| Bangladesh court case studies | Case law | Publicly available |

---

## 💡 Example Queries

```
Q: What is the punishment for theft under Bangladesh law?
Q: What are the conditions for bail under the Code of Criminal Procedure?
Q: What does the Contract Act say about void agreements?
Q: What are the rights of an arrested person in Bangladesh?
```

---

## 👤 Author

**Md. Arif Hossain**
BSc in Electrical & Electronic Engineering, University of Asia Pacific, Dhaka
GitHub: [azil8778](https://github.com/azil8778)

---

## ⚠️ Disclaimer

This system is for **informational and research purposes only**. It is not a substitute for professional legal advice. Always consult a qualified lawyer for legal matters.
