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
3.  **Python Environment & Libraries:** Ensure you have Python installed, then install the necessary dependencies using pip:

   ```Bash
   pip install pandas matplotlib seaborn tqdm pydantic langchain-ollama langchain pypdf python-dotenv
   ```
4.  **Environment File (api_key.env):** Create a blank text file named api_key.env in the same directory as your project. Note: Since this pipeline runs entirely locally using Ollama and Mistral 7B, no API key is actually used. This file acts as a placeholder to prevent load_dotenv from throwing an error, but it is setup so users can easily outsource inference to external cloud APIs (like Gemini or OpenAI) in the future.

## 🚀 Execution Steps
1.  **Initialize:** Open your Jupyter Notebook and run Steps 1 through 3 to load the libraries, Pydantic schemas, and extraction classes.

2.  **Run Pipeline:** Execute Step 4 (Execution).

3.  **Input Path:** When the input box appears, type or paste the relative or local file path to your target ESG PDF (e.g., path/to/your/ESG_Report.pdf).

4.  **Review Findings:** Let the progress bar finish. The notebook will automatically print the aggregated KPI dataframes and generate the visual risk profile at the bottom of the page.

## 📊 Understanding the Visualizations (Auditor Guide)
The final reporting step generates two risk assessment graphs specifically designed to aid human auditors in their review:

1.  **Greenwashing Claim Frequency by Type (Bar Chart) 📉**
This chart categorizes the types of greenwashing the AI found. One critical category to watch for is the "Lesser of Two Evils". This occurs when a claim might be truthful within a specific product category, but it risks distracting the consumer from the much greater environmental impacts of the category as a whole. A classic example is a company boasting about "organic tobacco" or "fuel-efficient private jets."

2.  **Distribution of AI Confidence Scores (Histogram & Curve) 🔔**
A Confidence Score is a probability metric (ranging from 0.0 to 1.0) that represents how certain the AI is about its own greenwashing classification.

The tallest peak (the large purple block) shows where the model's certainty is most concentrated.

The Red Dashed Line acts as your Auditor Warning Threshold (set at 0.8). Anything at or to the right of this line means the model is highly confident the claim is greenwashing, signaling that an auditor needs to manually review that specific page immediately.
