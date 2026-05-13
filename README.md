# AI Career Advisor - Agentic AI System

The AI Career Advisor is a single-agent ReAct system that autonomously
orchestrates a multi-step career guidance workflow. Given a resume or skill
description as input, the agent extracts skills, matches the candidate against
five AI job market archetypes, retrieves semantically similar job postings via
RAG, generates a personalised recommendation, fetches current job openings and
assembles a salary-informed career report - all without manual intervention
between steps.

This project represents the synthesis of the full analytical pipeline built
across all previous projects: the skill taxonomy (Project 3), job market
archetypes and DistilBERT embeddings (Project 4), and the structured
recommendation templates informed by the fine-tuning work of Project 5.

---

## How to Run the Project

**1. Clone the repository**

    git clone https://github.com/n1n4ch/agentic-workflows.git
    cd agentic-workflows

**2. Install dependencies**

    pip install -r requirements.txt

**3. Copy required files from previous projects**

The following file must be present in the project folder before running
the notebook. 

    # From Project 4 (deep-learning-systems/)
    cp ../deep-learning-systems/adzuna_ai_jobs_europe_enriched.csv ./

**4. Open and run the notebook**

    jupyter notebook agentic_system.ipynb

Run all cells in order via **Kernel → Restart & Run All**

> Building the FAISS index encodes 1,088 job descriptions with DistilBERT
> and takes approximately 10–20 minutes on CPU. An internet connection is
> required on first run to download DistilBERT weights from HuggingFace
> (~250MB).

---

## Project Structure

| File | Description |
|------|-------------|
| `agentic_system.ipynb` | Main notebook - agent implementation and execution |
| `adzuna_ai_jobs_europe_enriched.csv` | Job postings for FAISS index - copy from Project 4 |
| `Agentic_AI_System_Design_Report.pdf` | Written report with citations |
| `requirements.txt` | Python dependencies (generated via `pip freeze`) |

---

## System Design

**Agent type:** Single-agent ReAct (Reason + Act)

**Reasoning loop:** The agent cycles through Thought → Action → Observation
at each step, logging every decision explicitly. The full reasoning trace
is included in the career report output.

**Memory:** A state dictionary persists across all six steps within a session,
accumulating skills, archetype match, RAG context, recommendation, live jobs,
and salary data. There is no cross-session memory - each run starts fresh.

**Tools:**

| Tool | Description |
|------|-------------|
| `extract_skills()` | Extracts skills from text using the 50-skill Project 3 taxonomy |
| `match_archetype()` | Cosine similarity against 5 archetype centroids from Project 4 |
| `rag_retrieve()` | FAISS semantic search over 1,088 European job postings |
| `generate_recommendation()` | Template-based career guidance conditioned on fit level |
| `fetch_live_jobs()` | Live Adzuna API query for current job openings |
| `get_salary_range()` | DACH salary statistics from Project 2 |

**Safeguards:**
- Empty or very short inputs trigger an error message and terminate immediately
- Inputs with no detectable skills trigger a guidance response without proceeding to matching or job fetching
- Adzuna API failures are caught gracefully - the agent continues without live jobs rather than crashing

---

## The Five AI Job Market Archetypes

Derived from k-means clustering of European job postings in Projects 3 and 4:

| # | Archetype | Fit score threshold |
|---|-----------|-------------------|
| 0 | Data & Analytics Generalist | ≥ 0.4 strong |
| 1 | Cloud ML Engineer | ≥ 0.4 strong |
| 2 | MLOps & GenAI Engineer | ≥ 0.4 strong |
| 3 | AI Automation & Integration | ≥ 0.4 strong |
| 4 | Deep Learning & AI Research | ≥ 0.4 strong |

Fit levels: **strong** (≥0.4) · **partial** (0.2–0.4) · **none** (<0.2)

---

## Key Findings from Execution

Five test scenarios were run covering strong fit, partial fit, no fit, an
edge case and a deliberate failure case. Notable findings:

- An automotive engineer with Python and computer vision correctly matched
  to Deep Learning & AI Research (score=0.5) despite no formal AI background -
  validating skill-based over label-based matching
- Single-skill inputs produce partial fit scores and generic but correct
  recommendations, demonstrating graceful degradation
- The no-fit pathway differentiates between candidates who should pursue
  technical upskilling and those who may benefit from AI applications in
  their existing field

---

## Data Bias and Responsible Use

The AI Career Advisor is designed exclusively as a candidate-facing career
orientation tool and must not be used for hiring screening or applicant
ranking. Archetype centroids were derived from UK and DACH job postings and
reflect those markets' hiring norms - candidates from different educational
backgrounds or non-English-speaking countries may be disadvantaged by
terminology mismatches. Under the EU AI Act, any deployment in a recruitment
context requires classification as a high-risk AI system. Skill extraction
is based on a narrow 50-skill keyword taxonomy and cannot capture the full
range of a candidate's competencies.

---

## Requirements

Regenerate with:

    pip freeze > requirements.txt
