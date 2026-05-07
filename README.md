# UJ Advisor

UJ Advisor is an Arabic academic advising assistant for University of Jeddah-style advising scenarios. The project combines Retrieval-Augmented Generation (RAG), SQL-based student data analysis, decision routing, and predictive models to answer academic questions about regulations, study plans, courses, students, training, clubs, faculty offices, and student risk/support needs.

The main implementation is in `UJADV_final.ipynb`.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Main Features](#main-features)
- [Project Structure](#project-structure)
- [System Architecture](#system-architecture)
- [Data and Artifacts](#data-and-artifacts)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [How to Run](#how-to-run)
- [Example Questions](#example-questions)
- [Evaluation Files](#evaluation-files)
- [Important GitHub Notes](#important-github-notes)
- [Limitations](#limitations)
- [Future Improvements](#future-improvements)

---

## Project Overview

The system is designed to support academic advising in Arabic. It can answer questions from multiple sources:

1. **Knowledge base retrieval** using ChromaDB and multilingual embeddings.
2. **Student database queries** using SQLite.
3. **Predictive model analysis** for academic risk and student support needs.
4. **Web fallback search** using Tavily when the local knowledge base is not enough.
5. **Conversation memory** for follow-up questions.

The assistant can handle questions such as:

- Academic regulations and definitions.
- Degree plan requirements.
- Course prerequisites.
- Student completed and remaining hours.
- Students who failed or did not complete a course.
- Training eligibility and training status.
- Student risk analysis and support recommendations.
- Faculty office/contact information.
- Clubs and extracurricular activities.

---

## Main Features

## Dataset / Large Files

The dataset files are not included in this repository because of GitHub size limits.

Download the dataset from Google Drive:

[Download SP2.zip](https://drive.google.com/file/d/1LUOF_aUNrbkjSSVS7oyO2wg8-bxpUhek/view?usp=sharing)

After downloading, unzip the file and place the folder in the project root:

```text
UJ_ADVISOR/
├── UJADV_final.ipynb
├── SP2/
├── README.md

### 1. Arabic RAG Question Answering

The project uses a vector database to retrieve relevant context from academic documents and then generates Arabic answers using an LLM.

Covered knowledge collections include:

- `transfer_rules`
- `degree_plans`
- `electives`
- `coop_rules`
- `regulations`
- `academic_calendar`
- `coop_replies`
- `clubs`
- `student_helpers`
- `faculty_offices`
- `specialization`


### 2. Decision Router

The system decides whether a question should be answered using:

- RAG retrieval.
- SQL functions.
- Predictive models.
- Hybrid logic combining more than one source.
- Web search fallback.

The router detects whether the user is asking about a policy, a student record, a course, a risk/support analysis, or a mixed case.

### 3. SQL Student Database Layer

The SQLite database stores student and course information. The notebook includes helper functions to answer structured advising questions directly from the database.

Database tables include:

| Table | Purpose |
|---|---|
| `students` | Student profiles, advisors, hours, notes, graduation status |
| `courses` | Course keys, course labels, credits, prerequisites |
| `student_courses` | Student course history and completion status |
| `risk_features` | Academic risk input features |
| `support_features` | Student support input features |
| `build_summary` | Dataset summary metrics |

### 4. Predictive Student Models

The project includes two model-based analysis components:

#### Academic Risk Model

Uses a TabNet classifier artifact stored in:

```text
SP2/Proposal_Algorithm12.zip
```

This model predicts student academic risk using features such as:

- Attendance
- Participation
- Quizzes
- Assignments
- Midterm
- Projects

#### Student Support Model

Uses an autoencoder model stored in:

```text
SP2/autoencoder_model.h5
```

It analyzes support-related features such as:

- Study hours per week
- Social media usage
- Sleep duration
- Physical exercise
- Family support
- Financial stress
- Peer pressure
- Mental stress level
- Diet quality
- Cognitive distortions

### 5. Retrieval Ranking

The RAG pipeline combines several retrieval strategies:

- Dense vector retrieval.
- BM25 keyword retrieval.
- Reciprocal Rank Fusion (RRF).
- Parent document aggregation.
- Deep retrieval fallback.
- Collection routing using collection prototypes.

### 6. Conversation Memory

The notebook includes memory functions to support follow-up questions, such as:

```text
ماهي المواد التي درسها الطالب 2216908؟
ما المتطلبات السابقة لها؟
```

The second question can be interpreted using the previous conversation context.

---

## Project Structure

```text
UJ_ADVISOR/
│
├── UJADV_final.ipynb
│   └── Main notebook containing routing, RAG, SQL logic, models, memory, and testing.
│
├── SP2.zip
│   └── Compressed folder containing model artifacts, databases, and vector stores.
│
├── decision_routing_eval_results (2).xlsx
│   └── Decision router evaluation summary.
│
├── retrieval_eval_RESULT.xlsx
│   └── Retrieval evaluation results for 100 questions.
│
├── ragas_results (1).xlsx
│   └── Detailed RAGAS evaluation results.
│
├── ragas_details (2).csv
│   └── Detailed RAGAS metrics by question.
│
├── ragas_summary (2).csv
│   └── RAGAS metric summary.
│
├── UJ_100_questions_decision_eval_dataset.xlsx
│   └── Main 100-question evaluation dataset.
│
└── UJ_decision_eval_50_questions.xlsx
    └── 50-question decision evaluation subset.
```

After extracting `SP2.zip`, the folder contains important artifacts such as:

```text
SP2/
├── ae_columns.json
├── autoencoder_model.h5
├── preprocess_info.json
├── Proposal_Algorithm12.zip
├── scaler.pkl
├── scaler gh.pkl
├── student_db_updated_no_training (1) (1) (1).db
├── collection_prototypes_e5 (1).json
├── vector_database_updated (1).zip
├── chroma_students_dbv22 (1).zip
└── chroma_db_v2 (1).zip
```

---

## System Architecture

The main flow is:

```text
User Arabic Question
        │
        ▼
Text Normalization + Autocorrect
        │
        ▼
Decision Router
        │
        ├── RAG Retrieval from ChromaDB
        ├── SQL Student Database Functions
        ├── Risk / Support Predictive Models
        └── Tavily Web Search Fallback
        │
        ▼
Evidence Fusion
        │
        ▼
Arabic Final Answer
        │
        ▼
Conversation Memory Update
```

---

## Data and Artifacts

### SQLite Database

Main database file:

```text
SP2/student_db_updated_no_training (1) (1) (1).db
```

The database includes:

- 215 students.
- 50 courses.
- 8,595 student-course records.
- Risk and support feature tables.

### Vector Databases

The project includes Chroma vector database files used for semantic retrieval:

```text
SP2/vector_database_updated (1).zip
SP2/chroma_students_dbv22 (1).zip
SP2/chroma_db_v2 (1).zip
```

### Model Files

Risk model:

```text
SP2/Proposal_Algorithm12.zip
```

Support model:

```text
SP2/autoencoder_model.h5
```

Scalers and preprocessing files:

```text
SP2/scaler.pkl
SP2/scaler gh.pkl
SP2/preprocess_info.json
SP2/ae_columns.json
```

---

## Installation

This project was developed in a notebook environment such as Google Colab or Jupyter Notebook.

Install the main dependencies:

```bash
pip install chromadb sentence-transformers pandas tqdm
pip install langchain langchain-community llama-index transformers accelerate
pip install rank_bm25 pytorch-tabnet tavily-python openai joblib requests
pip install tensorflow
```

Optional, depending on your environment:

```bash
pip install langchain-chroma langchain-huggingface
```

---

## Environment Variables

Before running the notebook, set your API keys as environment variables.

```bash
export OPENAI_API_KEY="your_openai_api_key"
export OPENAI_MODEL="gpt-4o-mini"
export OPENAI_TEMPERATURE="0.0"
export OPENAI_MAX_OUTPUT_TOKENS="250"
export TAVILY_API_KEY="your_tavily_api_key"
```

In Python or Colab:

```python
import os

os.environ["OPENAI_API_KEY"] = "your_openai_api_key"
os.environ["TAVILY_API_KEY"] = "your_tavily_api_key"
```


---

## How to Run

### 1. Extract the Project Files

Extract the main project ZIP file.

```bash
unzip UJ_ADVISOR.zip
cd UJ_ADVISOR
```

Then extract the artifact folder:

```bash
unzip SP2.zip -d SP2
```

### 2. Update File Paths

The notebook currently uses Colab-style paths such as:

```python
/content/student_db_updated_no_training (1) (1) (1).db
/content/vector_database_updated.zip
/content/Proposal_Algorithm12.zip
/content/autoencoder_model.h5
```

If running locally, update them to relative paths such as:

```python
DB_PATH = "SP2/student_db_updated_no_training (1) (1) (1).db"
CHROMA_ZIP_PATH = "SP2/vector_database_updated (1).zip"
RISK_MODEL_ZIP = "SP2/Proposal_Algorithm12.zip"
SUPPORT_MODEL_PATH = "SP2/autoencoder_model.h5"
```

### 3. Run Notebook Cells

Open:

```text
UJADV_final.ipynb
```

Run the notebook cells in order.

The main function for answering questions is:

```python
rag_student_answer(question, protos)
```

Example:

```python
result = rag_student_answer("ما شروط التحويل الداخلي؟", protos)
print(result["answer"])
```

---

## Example Questions

### Academic Regulations

```python
rag_student_answer("ما تعريف الخطة الدراسية حسب اللوائح؟", protos)
```

```python
rag_student_answer("ما شروط التحويل الداخلي؟", protos)
```

### Degree Plan Questions

```python
rag_student_answer("كم عدد ساعات خطة الذكاء الاصطناعي؟", protos)
```

```python
rag_student_answer("كم عدد مقرر حر في الخطة؟", protos)
```

### Student Record Questions

```python
rag_student_answer("كم الساعات المكتملة للطالبة 2216908؟", protos)
```

```python
rag_student_answer("اعرض بيانات الطالبة 2216908", protos)
```

### Course Questions

```python
rag_student_answer("من الطالبات اللي ما اجتازوا COURSE_037؟", protos)
```

```python
rag_student_answer("كم عدد الطلاب المسجلين في مقرر CEAI 321؟", protos)
```

### Training Questions

```python
rag_student_answer("هل الطالب 2216908 مؤهل للتدريب؟", protos)
```

```python
rag_student_answer("في احد باقي عليه تدريب ولا كلهم خلصوا؟", protos)
```

### Risk and Support Analysis

```python
rag_student_answer("حلل الطالب 2216908 واذكر التوصية وفق الأنظمة", protos)
```

```python
rag_student_answer("ما مستوى خطورة الطالب 2216908؟", protos)
```

### Activities and Offices

```python
rag_student_answer("اي النوادي الموجودة؟", protos)
```

```python
rag_student_answer("أين مكتب عضو هيئة التدريس أحلام؟", protos)
```

---

## Evaluation Files

The repository includes evaluation files for retrieval, routing, and answer quality.

### Decision Routing Evaluation

File:

```text
decision_routing_eval_results (2).xlsx
```

Summary:

| Metric | Score |
|---|---:|
| Mode accuracy | 0.90 |
| RAG decision accuracy | 0.92 |
| SQL decision accuracy | 0.94 |
| Model decision accuracy | 0.88 |
| Exact decision match | 0.84 |

### RAGAS Evaluation

Files:

```text
ragas_results (1).xlsx
ragas_details (2).csv
ragas_summary (2).csv
```

Summary metrics:

| Metric | Score |
|---|---:|
| Answer relevancy | 0.715989 |
| Context precision | 0.729944 |
| Faithfulness | 0.893446 |

### Retrieval Evaluation

File:

```text
retrieval_eval_RESULT.xlsx
```

Contains retrieval metrics across 100 evaluation questions, including:

- Router accuracy.
- Collection hit rate.
- Collection precision.
- Parent and chunk retrieval counts.
- MRR-style hint metrics.

---

## Important GitHub Notes

Before uploading this project to GitHub:

1. **Remove all hardcoded API keys** from the notebook.
2. **Rotate any exposed keys** if the notebook was ever shared publicly or uploaded before cleanup.
3. Add a `.gitignore` file to avoid committing unnecessary cache files.
4. Consider not uploading large model/vector database files directly to GitHub. Use Git LFS or provide download instructions instead.
5. Avoid committing private or sensitive student data unless it is synthetic or approved for sharing.

Recommended `.gitignore`:

```gitignore
__pycache__/
.ipynb_checkpoints/
*.pyc
*.pkl
*.h5
*.pt
*.db
*.sqlite3
*.zip
.env
.DS_Store
desktop.ini
```

If the model, database, or vector files are required for the project to run, upload them using Git LFS or provide them separately.

---

## Limitations

- The notebook uses several absolute `/content/...` paths that need to be changed for local execution.
- Some files are large binary artifacts and may not be suitable for normal GitHub storage.
- Answer quality depends on the quality of retrieved context and the LLM used.
- The system is optimized for Arabic academic advising questions.
- Web fallback requires a Tavily API key.
- Predictive results should be treated as decision-support outputs, not final academic judgments.

---

## Future Improvements

Possible improvements:

- Convert the notebook into a clean Python package.
- Add a Streamlit or Gradio user interface.
- Add a `requirements.txt` file.
- Add unit tests for SQL functions, router logic, and retrieval functions.
- Move configuration paths into a `.env` or `config.yaml` file.
- Separate model loading, database access, retrieval, and routing into different modules.
- Add role-based privacy controls for student information.
- Add clearer logging and error handling.
- Add a deployment guide.

---

## Suggested Repository Layout

A cleaner future GitHub structure could look like this:

```text
UJ_ADVISOR/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── UJADV_final.ipynb
├── src/
│   ├── config.py
│   ├── router.py
│   ├── rag.py
│   ├── sql_tools.py
│   ├── models.py
│   ├── memory.py
│   └── utils.py
├── data/
│   └── README.md
├── artifacts/
│   └── README.md
└── evaluations/
    ├── decision_routing_eval_results.xlsx
    ├── retrieval_eval_RESULT.xlsx
    └── ragas_summary.csv
```

---

## License

Add your license here. For example:

```text
MIT License
```

or keep the project private if it contains sensitive academic/student data.

---

## Authors

Developed as part of the UJ Advisor academic advising project.
