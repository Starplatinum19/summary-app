# Automatic Research‑Paper Summarizer

## How it works (via Sumy)
We run one of Sumy’s extractive algorithms:

- TextRank
- LexRank
- LSA

### Purpose
- It reads the article, gives every sentence a score, and keeps the top‑ranked ones—no rewriting involved.

### Why
- Runs almost instantly (under a tenth of a second for a 1k‑word paper) because there are no big neural weights to load.

### When we also use it
Even when you ask for an abstractive summary, we still run extractive first; that output can be fed to the neural model if you want a quicker “hybrid” pass later.

---

## How abstractive summarization works
Large transformer models read the whole text and write brand‑new sentences that capture the same ideas.

### Model sizes
- BART‑large ≈ 406M parameters  
- DistilBART ≈ 306M (smaller, faster)  
- PEGASUS ≈ 568M (best quality)  
- T5‑base ≈ 220M (easiest to fine‑tune)

### Speed trade‑off
- CPU: 20–30 seconds for a 1k‑word paper  
- GPU: ~10× faster

### Job in our app
Optional **quality mode**.  
Tick the checkbox and you get a smoother, more coherent summary with paraphrasing and fewer repeats, at the cost of extra wait time and RAM.

---

## ToolChain and rationale
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/484d6f65-d3ed-49e6-a0ee-aa13c717fec7" />

---

## Models and Algorithms
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/a1d2b31f-5fb1-4f28-9aa3-bcc73dd5a41f" />

---

# ROUGE‑L

### What is ROUGE‑L
An automatic metric based on the LCS algorithm.

### How it works
Finds the longest word subsequence in both system and reference summaries.

### Where it’s used
- Benchmarking extractive vs abstractive methods in our evaluation  
- Logging summary quality during model comparison experiments

### Why we rely on it
Fast, language‑agnostic, industry‑standard for comparing summarizers.

---

## Example

**Reference sentence (6 words):**  
“cats sit on the mat”  
→ tokens: `[cats, sit, on, the, mat]`

**System summary (5 words):**  
“cats are on the mat”  
→ tokens: `[cats, are, on, the, mat]`

### Longest Common Subsequence (LCS)
LCS = **cats, on, the, mat**

- LCS length = 4  
- Recall = 4 / 6 ≈ 0.67  
- Precision = 4 / 5 = 0.80  
- F₁ ≈ 0.73  

ROUGE‑L F₁ ≈ **0.73** tells us that the machine summary retains about **73%** of the reference’s wording and order.

---

# Abstractive Quality
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/c6be7c9c-21e3-4d80-9a7c-f3cfc609976f" />

# Abstractive Speed
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/b00d47df-66a6-4b72-8620-74b2e14c53bb" />

# Extractive Quality
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/2bd298fc-0d11-473c-898e-9f20dc7ebdd3" />

# Extractive Speed
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/b7497c6d-3686-4966-9790-5ad9fdfa7557" />

# Quality vs Speed — Scatter Plot
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/404e60cc-ab5e-4ed6-acee-e43a5c110500" />

---

# Overall Structure

- Imports & Dependencies  
- Helper functions (cleaning & postprocessing)  
- Model loading and caching  
- Streamlit UI (sidebar & main)  
- Summarization logic (extractive & abstractive)  
- Output & download (TXT/DOCX)

---

# Libraries

- streamlit: UI framework  
- pdfplumber: PDF text extraction  
- langdetect & MarianMT: language detection & translation  
- sumy: extractive summarization algorithms  
- transformers: abstractive summarization models  
- python-docx: DOCX export  

---

# Helper Functions

### clean_text()
- Remove citations  
- Remove special chars  
- Lowercase  

### postprocess()
- Fix hyphens  
- Normalize spacing  
- Capitalize  

Handles both extractive & abstractive inputs uniformly.

---

# Models

`@st.cache_resource load_pipe`  
- Pipelines for summarization  
- Auto GPU if `torch.cuda.is_available()`  
- Supports BART, DistilBART, PEGASUS, T5 checkpoints  

---

# Streamlit UI

### Sidebar controls
- File uploader  
- Language detect  
- Translate  
- Extractive algorithm dropdown  
- Optional abstractive model dropdown  

### Main area
- Displays summaries  
- Download buttons  

---

# Summarization Logic

### Extractive
- Sumy TextRank / LexRank / LSA  
- Top‑7 sentences  

### Abstractive
- Transformer pipelines on `cleaned[:1024]` text  

### Hybrid flow
- Use extractive output if desired  
- Normalized and displayed side‑by‑side  

---

# Output and Download

- Display summary with `st.write`  
- Download as TXT via `st.download_button`  
- Generate DOCX with python-docx, download via button  
