# Architecture

```mermaid
flowchart LR
    U[User] --> UI[Streamlit app.py]
    UI --> A[Agent: bounded Claude loop]
    A -- tool_use --> R[Tool registry: Pydantic validation]
    R --> O[Operations: pandas / scikit-learn]
    O --> D[(validated parquet)]
    F[scripts/fetch_data.py] --> D
    R -- tool_result / is_error --> A
    A --> UI
```

| Component | File | Responsibility |
|---|---|---|
| Data layer | `src/ai_spoke_compass/data.py` | Download from the documented source, hash, validate schema and row count, cache as parquet |
| Operations | `src/ai_spoke_compass/operations.py` | Deterministic pandas/scikit-learn computations |
| Tools | `src/ai_spoke_compass/tools.py` | Bind operations to inspected columns; Pydantic input models built from observed values and ranges |
| Agent | `src/ai_spoke_compass/agent.py` | Manual Messages API loop on claude-opus-5-5; progress events; tool errors returned as is_error |
| UI | `app.py` | Streamlit chat with progress states, charts, a direct tool runner, evals and data provenance |
| Evaluation | `src/ai_spoke_compass/evaluation.py` | Pinned tool-level checks and agent-level checks |

## Why an agent, and how it is bounded

Questions are open-ended (which tool, which filters, which date) and often need more than one step, so a small tool-using agent fits. It stays bounded: 6 model turns, 8 tool calls, validated inputs, and no code execution. Every number in an answer comes from a tool result.

- Manual Messages API loop: the app controls the budget and streams progress to the UI.
- Tool results go back in one user message per turn; failures are `is_error` results the
  model can correct.
- `stop_reason` handling: `refusal` (with server-side fallback enabled:
  True), `max_tokens`, and the turn limit all end the run with a clear
  status.

## Tool inventory

| Tool | Operation | Bindings |
|---|---|---|
| `rank_ai_obstacles` | aggregate | group=`contents`, metrics=`['2026']`, filterable=`['industry (SIC2025)', 'employed persons']`, eval_filters=`{'industry (SIC2025)': '_T Total', 'employed persons': '100 employed or more'}`, eval_question=`What is the most cited obstacle to AI use among Norwegian enterprises with 100 or more employees?` |
| `obstacle_by_company_size` | aggregate | group=`employed persons`, metrics=`['2026']`, filterable=`['contents', 'industry (SIC2025)']`, eval_filters=`{'contents': 'Concerns regarding violation of data protection and privacy', 'industry (SIC2025)': '_T Total'}`, eval_question=`Which enterprise size class most often cites data protection and privacy concerns as an obstacle to AI?` |
| `obstacle_by_industry` | aggregate | group=`industry (SIC2025)`, metrics=`['2026']`, filterable=`['contents', 'employed persons']`, eval_filters=`{'contents': 'Lack of relevant expertise', 'employed persons': 'Total'}`, eval_question=`In which industry do enterprises most often cite lack of relevant expertise as an obstacle to AI?` |
| `genai_work_use_by_year` | aggregate | group=`year`, metrics=`['Used generative AI tools for professional (work) purposes']`, filterable=`['sex', 'age']`, eval_filters=`{'sex': 'Both sexes', 'age': '45-54 years'}`, eval_question=`In which year was work use of generative AI highest among 45-54 year olds?` |
| `genai_use_by_age` | aggregate | group=`age`, metrics=`['Used generative AI tools for professional (work) purposes', 'Used generative AI tools for private purposes']`, filterable=`['year', 'sex']`, eval_filters=`{'year': 2026, 'sex': 'Both sexes'}`, eval_question=`Which age group used generative AI for work most in 2026?` |

Input models are generated from the loaded data, so the JSON schema the model sees lists real
values and ranges, and invalid arguments are rejected before any computation.

## Data flow and validation

1. `fetch_data.py` downloads from the documented source, records SHA-256 hashes in a manifest,
   validates column presence and types and the minimum row count, and writes parquet.
2. The app and scripts load only that validated file. If it is missing, they say so and offer
   the download; they never substitute invented data.

## Evaluation

- Tool-level: expected values pinned by an independent implementation (pyarrow + plain
  Python).
- Agent-level (needs a key): the expected tool is called and the pinned value appears in the
  answer.

## Trade-offs

- Generic, configurable operations instead of bespoke code per tool: easier to validate and
  test, at the cost of some domain-specific nuance.
- Fixed tools instead of model-generated code: safer and reproducible, but questions outside
  the tool set are declined rather than improvised.

