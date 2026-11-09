# imf-kyrgyz-llm-pipeline!
IMF Article IV Report → Insights Pipeline (Kyrgyz Republic, 2025)
An end-to-end Python pipeline that turns an unstructured IMF Article IV staff report into verified structured data and a four-panel macro-financial dashboard, using large language models (LLMs) under human oversight.
Report analysed: IMF Country Report No. 25/118, Kyrgyz Republic: 2025 Article IV Consultation – Press Release; and Staff Report (June 2025). https://doi.org/10.5089/9798229013031.002
What the notebook does
Part	Task
0	Setup: packages, settings, LLM provider and API key, the report PDF
1	Task 1 – PDF parsing and cleaning (`pypdf`), section detection, token-aware chunking (400 tokens, ~50 overlap), section summaries (baseline V1 vs optimised V2), executive summary, LLM-as-a-Judge
2	Retrieval index: local embeddings (`BAAI/bge-small-en-v1.5`) + FAISS `IndexFlatL2`
3	Task 2 – theme distribution (7 financial-stability themes), strengths and vulnerabilities with verified evidence quotes, core indicators as strict JSON with fallbacks, trends from the report tables, judge audits
4	Task 3 – RAG question answering with checked citations, plus a closed-book comparison
5	Extension – Multi-Query RAG (Python query variants + Reciprocal Rank Fusion) vs single-query RAG
6	Task 4 – tidy data with `pd.melt` and a 4-panel dashboard saved at 300 dpi
7	Run summary: API calls, cache use, latency
All outputs are saved as `.json` (and `.png` for figures) in `outputs/`.
How to run
Google Colab (recommended)
Open `IMF_Kyrgyzstan_LLM_Pipeline.ipynb` in Colab (File → Upload notebook, or open from GitHub).
Add your API key in Colab Secrets (key icon in the left sidebar) and switch on Notebook access:
`DEEPSEEK_API_KEY` for the default provider (`PROVIDER = "deepseek"`), or
`OPENROUTER_API_KEY` (`PROVIDER = "openrouter"`) or `GEMINI_API_KEY` (`PROVIDER = "gemini"`).
Runtime → Run all. If the IMF website blocks the automatic download, the notebook asks you to upload the report PDF (download it from the DOI link above).
Locally (VS Code / Jupyter, Python 3.10+)
Run the first cell to install the packages, restart the kernel once, then Run All. The API key is read from the environment variable named above, or asked for with a hidden prompt.
Design notes
Two different models: a fast model extracts and summarises (`deepseek-flash`), a stronger model judges (`deepseek-v4-pro`). The provider is one setting (`PROVIDER`) in cell 0.2.
Code verifies every LLM output: Pydantic JSON schemas, evidence quotes found verbatim in the report, numbers found in their quotes, numbers absent from the report treated as hallucinations (`null` + reason), RAG citations matched to the retrieved chunks.
Disk caching: every LLM answer is cached (key = hash of model, prompt, system instruction and schema), so re-runs reproduce the same results without new API calls. In Colab the cache is kept in Google Drive.
Temperature 0 and retries with back-off for repeatable, robust runs.
No secrets in the code: keys come only from Colab Secrets or environment variables.
Repository contents
`IMF_Kyrgyzstan_LLM_Pipeline.ipynb` – the full pipeline (code, prompts, figures and numbers used in the report)
`README.md` – this file
`LICENSE` – MIT License
The written report is submitted separately and is not part of this repository.
License
MIT – see `LICENSE`.
