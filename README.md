# Bangladesh_legal_ai
Ai chatbot for Bangladesh law law


## Reproducing This Project

### 1. Collect Bangladesh Statutes
Download the law PDFs from the official Bangladesh government 
legal database at bdlaws.minlaw.gov.bd, or use the included 
**Legal Scraper** notebook to collect them automatically.

Then run `notebooks/01_pdf_extraction.ipynb` to extract, 
clean, and chunk the statutes by section.

### 2. Collect Supplementary Legal Sources
Collect university law journals and case study PDFs from 
institutions such as Rajshahi University and Jagannath 
University (publicly available through their libraries).

Chunk these based on their formatting — the extraction 
notebook includes comments on adapting the chunker for 
different document structures.

### 3. Build the RAG Pipeline
Once both datasets are chunked and saved as JSONL, follow 
`notebooks/02_bangladesh_legal_ai.ipynb` to:
- Embed all chunks using sentence-transformers
- Build the FAISS vector index
- Fine-tune Phi-3 Mini 3.8B with QLoRA
- Run the full RAG query pipeline
