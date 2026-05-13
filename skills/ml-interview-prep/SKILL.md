---
name: ml-interview-prep
description: End-to-end AI/ML interview preparation for builders. Reads a job description and user background, produces a gap analysis, personalized study plan, theory Q&A bank, hands-on coding exercises, and an optional Streamlit dashboard shareable with interviewers. Use when preparing for any AI/ML engineering, research, or applied science role.
---

# AI/ML Interview Prep — Builder Mode

A skill for preparing AI/ML practitioners for technical interviews. Covers the full stack: theory depth, implementation from scratch, system design, and shareable artifacts. The output is always tailored — never a generic checklist.

---

## Phase 0 — Context Gathering

Before generating anything, collect the following. Ask everything in a single message, not one at a time.

**From the job description:** role title and level (IC3 / Senior / Staff / Principal / MLE / Research Scientist), company type (big tech / startup / research lab / infra / product), required vs. preferred skills, domain (NLP, CV, RecSys, Search, RL, MLOps, GenAI, etc.), and any stated interview format.

**From the user:** years of experience and current role, strongest and weakest technical areas (specific, not vague), timeline to interview, and whether they want a shareable artifact.

Do not proceed past Phase 0 without at least: role level, domain, and self-assessed strengths/gaps.

---

## Phase 1 — Gap Analysis

Produce a structured gap analysis before any study material.

Tag every technical skill from the JD as `[core]` (required / repeated), `[nice]` (preferred / bonus), or `[implicit]` (not stated but always expected at this level). For each core and implicit skill, rate the user's level: **Strong** (has done it in production), **Familiar** (used it but couldn't derive it), or **Gap** (little to no experience).

Produce a priority table sorted by: core/implicit × Gap first, then core × Familiar, then nice × Gap. This order drives everything that follows.

---

## Phase 2 — Study Plan

Generate a time-boxed plan calibrated to the timeline.

- **< 1 week:** top-3 gaps only + one coding exercise per day
- **1–2 weeks:** all gaps + system design + one full mock
- **2–4 weeks:** full coverage + one shareable project

Split each topic into three blocks: **Theory** (concepts, derivations, questions), **Coding** (implement or debug), and **Applied** (production failure modes, trade-offs). Cap theory at ≤ 40% of total prep time.

---

## Phase 3 — Theory Preparation

For each priority topic, produce:

**Concept card:** plain-English explanation first, then the key equation or pseudocode. Include two "explain to a PM" sentences and two "explain to a systems engineer" sentences.

**Question bank — three tiers:**
- *Tier 1 — Definition* (phone screen): "What is X and when would you use it?" Answer in 2–4 sentences, no hedging, ends with a concrete example.
- *Tier 2 — Depth* (on-site): "Walk me through how X works under the hood." Answer traces the mechanism, names failure modes, mentions 1–2 alternatives.
- *Tier 3 — Edge cases* (separates senior candidates): "When does X break down?" Answer names 2–3 concrete failure conditions, each linked to a mitigation.

**Common traps:** 2–3 things candidates typically get wrong on this topic. Be specific, not generic.

---

## Phase 4 — Coding Preparation

### Exercise types
Pick the type that fits the topic: implement from scratch (no library for the core), debug a broken implementation, extend a working one, optimize for production (vectorize / batch / reduce memory), end-to-end mini pipeline, or LLM/API exercise.

### API call policy
Ask upfront whether the user has API keys available.

- **Keys available:** use live calls for all LLM-related exercises. Real outputs surface real failure modes that mocks cannot replicate. Always show the call, a token/cost estimate, and how to swap model or provider.
- **No keys:** generate deterministic mock responses that are realistic enough to exercise the surrounding logic. Mark them clearly so the user knows where to swap in a real call later.
- For non-LLM topics (gradient descent, NDCG, attention math), mock data is always the right default.

### Mock data generation
Every exercise includes a seeded `generate_mock_data()` function. Requirements:
- Fixed seed so runs are reproducible; configurable size constant at the top of the file
- Realistic distributions for the domain — TREC-skewed relevance labels, Beta-distributed scores, clustered embeddings — not uniform noise
- Include at least one data quality problem if the exercise involves cleaning
- Accompanied by a `describe_mock_data()` that prints shape, dtypes, and a few sample rows

### Exercise format
Ask the user whether they prefer **Python scripts** or **Jupyter notebooks** before generating exercises. Both formats follow the same content structure — the difference is execution context.

**Python script format** (`exercises/ex01_topic.py`):
- All imports at the top with a comment listing non-stdlib dependencies
- `generate_mock_data()` called in `__main__` so running the file produces visible output
- TODOs marked with a one-line hint, never left blank
- A final assertion that validates output against an expected range
- Reference solution in a separate `_solutions/` file, not inline

**Jupyter notebook format** (`exercises/ex01_topic.ipynb`):
- Cell 1 — imports and constants
- Cell 2 — `generate_mock_data()` + `describe_mock_data()`, output visible immediately
- Cell 3 — problem statement in markdown (context, goal, constraints)
- Cells 4–N — skeleton cells with `# TODO:` hints, one logical chunk per cell so the user can run incrementally
- Final code cell — assertion block validating the output
- Final markdown cell — follow-up questions (complexity, scaling, failure modes)
- Reference solution in a separate `ex01_topic_solution.ipynb`, or in collapsed/hidden cells if the user prefers single-file

Notebook exercises are preferred when the topic involves visualization (loss curves, attention heatmaps, embedding projections) or iterative exploration (prompt tuning, hyperparameter sweeps). Script format is preferred for timed coding mocks that simulate a real interview environment.

### Interview simulation
After exercises are complete, run a timed mock: present the problem cold, let the user solve it, then critique in order — correctness → edge cases → complexity → communication.

---

## Phase 5 — System Design / ML Design

Required for Senior+ roles. Generate a design prompt matched to the company domain, with realistic scale numbers and a latency or freshness constraint.

Evaluate the user's design using this rubric:

| Dimension | What to look for |
|---|---|
| Problem framing | Did they clarify before diving in? Did they define success metrics? |
| Data | Training data sourcing, labeling, freshness, distribution shift |
| Modeling | Justified model choice, baseline before complexity |
| Offline eval | Metric selection, held-out set, slice analysis |
| Online eval | A/B test design, guardrail metrics, rollout strategy |
| Production | Serving latency, fallback, monitoring, retraining cadence |
| Trade-offs | Did they name trade-offs or just pick one option? |

---

## Phase 6 — Core Scripts

Before building exercises or the dashboard, create a small `prep/` library that everything else imports from. Never duplicate mock data or metric logic across files.

| Script | Responsibility |
|---|---|
| `prep/data.py` | Central mock data factory — `generate_mock_data(topic, n, seed)` dispatches to topic-specific generators |
| `prep/metrics.py` | Reusable metric implementations (NDCG, MRR, precision@k, etc.) — numpy only, consistent signature |
| `prep/llm_client.py` | Thin LLM wrapper — `call_llm(prompt, model, provider, mock)` — logs tokens and cost, raises clearly when no key found |
| `prep/eval.py` | `run_experiment(fn, dataset, metric)` — applies a function to every row and returns a results DataFrame |
| `prep/state.py` | Read/write a local `prep_state.json` tracking exercise status, self-ratings, and mock scores |

Create `data.py` and `metrics.py` before any exercise file. Create `llm_client.py` only if LLM topics are in scope — and only after confirming API key availability. Create `state.py` only if building the dashboard.

Standard folder layout:
```
interview-prep/
  prep/             # shared library
  exercises/        # one file per exercise, imports from prep/
  _solutions/       # hidden until unlocked
  dashboard.py      # imports from prep/, never inlines logic
  prep_state.json   # auto-generated
```

---

## Phase 7 — Shareable Artifacts

### Option A — Streamlit dashboard
`dashboard.py` imports from `prep/` for all data and metrics — no inlined logic.

Panels: gap heatmap (topics × theory/coding/design, colored by readiness), question bank (filterable by topic and tier, answers expandable), exercise tracker (status + self-rating per exercise, backed by `state.py`), experiment results (metric scores and time-to-solve across exercises), and — if LLM exercises exist — an LLM experiment explorer (side-by-side prompt/response comparison with cost breakdown).

### Option B — Jupyter notebook
One notebook, structured as one cell group per topic: concept → mock data → skeleton → validation assert. A final cell contains a self-assessment table the user fills in. The notebook must run top-to-bottom without errors.

### Option C — One-page summary
A reference sheet per topic: key equations, one implementation snippet from the reference solution, three interview questions with model answers, and red flags to avoid.

---

## Delivery Defaults

- Always produce the gap analysis before any study content
- Create `prep/data.py` and `prep/metrics.py` before writing any exercise
- Ask about API keys before writing `llm_client.py` — don't assume mock mode
- Generate at least one coding exercise per `[core]` topic
- When running mock interviews, ask the question and wait for the user's answer before critiquing — never pre-answer
- When the user finishes an exercise, ask "What would you do differently?" before giving feedback
- Flag bad-fit mismatches plainly (e.g., "this role is MLOps-heavy but you have no infra experience at Staff level")

---

## Tone and Style

- **Calibrate to level:** entry-level needs more scaffolding; staff-level gets harder edge cases and broader system scope
- **Be a tough but fair interviewer:** "this answer would not pass a Google loop" is more useful than "pretty good"
- **Name company culture when known:** Meta values speed and ownership, Google values rigor and citations, startups value scope and pragmatism — frame questions accordingly
- **Never generate a generic study list:** every item must trace back to a specific gap from the user's profile
