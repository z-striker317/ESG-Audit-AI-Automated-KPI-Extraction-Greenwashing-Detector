# 📊 ESG-Audit AI: Automated KPI Extraction & Greenwashing Detector

An end-to-end Python pipeline built to automate the initial audit and risk assessment of corporate ESG/Sustainability Reports (PDFs) for Big Four accounting and consulting firms (Deloitte, PwC, etc.).

This project demonstrates proficiency in using modern **Local LLM Infrastructure** and **Structured Output** techniques to transform unstructured documents into actionable business intelligence.

---

## ✨ Project Highlights

* **Problem:** Manually auditing 100+ page ESG reports is slow, costly, and prone to human error.
* **Solution:** An AI agent that ingests a PDF, extracts Key Performance Indicators (KPIs), and flags vague or unsubstantiated claims ("Greenwashing") in a structured, verifiable format.
* **Key Achievement:** Successfully processed a large, multi-page corporate report using a **chunking strategy** to overcome the context limitations of local language models.
* **Result:** Generated over **300 KPIs** and flagged **100+ high-confidence risks** for auditor review.

## 🛠️ Tech Stack

| Category | Component | Rationale |
| :--- | :--- | :--- |
| **Local LLM** | **Ollama** | Provides a free, local server environment. |
| **Model** | **Mistral 7B Instruct** | Chosen for superior instruction-following, function calling, and reliable **JSON structured output** compared to base Llama models. |
| **Framework** | **LangChain** | Used for prompt templating and abstracting the model interaction via the `ChatOllama` wrapper. |
| **Data Structure** | **Pydantic** | Defines the strict schema for KPIs and Vague Claims, enforcing the data quality required for auditing. |
| **Document I/O** | **PyPDF** | Extracts raw text from the input PDF. |
| **Analysis/BI**| **Pandas, Matplotlib/Seaborn** | Aggregates and visualizes the audit findings directly in the Jupyter Notebook. |

---

## 🎯 Architecture and Workflow (The 6 Steps)

The pipeline is executed sequentially in a single Jupyter Notebook flow:

1.  **Schema Definition (Pydantic):** Defines the strict data contract for `ESG_KPI` (handling `Union[float, str]` for qualitative metrics) and `VagueClaim`.
2.  **Document Chunking:** The input PDF is broken down page-by-page.
3.  **LLM Extraction Loop:** The `ESGExtractor` sends **one page** to the **Mistral 7B** model. The model is forced, via the `method="json_schema"` constraint, to return a perfect JSON object.
4.  **Dynamic Metadata:** The LLM dynamically extracts the **Company Name** and **Report Year** from the first few successful pages.
5.  **Data Aggregation (Pandas):** Results from all chunks are collected into master Pandas DataFrames (`kpi_df` and `claims_df`).
6.  **Reporting:** The aggregated data is visualized using Matplotlib/Seaborn to present a final, actionable **Greenwashing Risk Profile** to the auditor.

## ⚙️ Setup and Installation

### Prerequisites

1.  **Ollama Server:** Must be installed and running on your local machine.
    * **Start Server:** (If not running) Open a terminal and run `ollama serve` (or ensure the desktop app is open).
2.  **Model Download:** Pull the specific model used in the project:
    ```CMD Terminal
    ollama pull mistral:7b-instruct
    ```
