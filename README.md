# AI Spoke Compass


Ask plain-English questions about how Norwegian workplaces use AI and what holds them back. An AI agent looks up the answer in official Statistics Norway data and cites every figure.

Ask questions in plain English; a Claude agent answers by calling validated Python tools over
the real **Statistics Norway table 14944: Obstacles to enterprise AI use by industry and size (2026)** dataset (CC-BY-4.0). Every number in an answer comes from a tool
result, and the app shows which tools ran, with their inputs and outputs.

## Example questions

- What are the top three reasons large Norwegian enterprises give for not using AI?
- Do bigger companies worry more about privacy and legal clarity than small ones?
- Which industry reports the biggest skills gap for AI?
- How fast has work use of generative AI grown among 55-64 year olds since 2024?

## How it works

| Tool | Operation | What it does |
|---|---|---|
| `rank_ai_obstacles` | aggregate | Rank the obstacles to AI use (contents) by the share of enterprises citing them, in per cent (column '2026'). Always filter ONE industry (SIC2025), using '_T Total' for all industries, and ONE size class (employed persons, e.g. 'Total' or '100 employed or more'). 'Has considered to use artifical intelligence technology' is a share of enterprises, not an obstacle: report it separately. |
| `obstacle_by_company_size` | aggregate | Compare ONE obstacle (contents) across enterprise size classes (employed persons), in per cent (column '2026'). Always filter ONE industry (SIC2025), using '_T Total' for all industries. The 'Total' group is all sizes combined, not a size class. |
| `obstacle_by_industry` | aggregate | Compare ONE obstacle (contents) across industries (SIC2025), in per cent (column '2026'). Always filter ONE size class (employed persons). '_T Total' is all industries combined. |
| `genai_work_use_by_year` | aggregate | Percentage reporting use of generative AI tools for professional (work) purposes in the last three months, by survey year (2024-2026). Always filter ONE sex ('Both sexes', 'Females', 'Males') and ONE age group ('16-74 years' for everyone). SSB's base for this percentage is unconfirmed: values exceed SSB's overall 'have used generative AI' share, so they are most likely shares of generative-AI users, not of the population. Say so instead of calling it a share of all people. |
| `genai_use_by_age` | aggregate | Compare age groups on the percentage reporting generative-AI use for work and for private purposes. Always filter ONE year and ONE sex. '16-74 years' is the all-ages total, not an age group. SSB's base for this percentage is unconfirmed: values exceed SSB's overall 'have used generative AI' share, so they are most likely shares of generative-AI users, not of the population. Say so instead of calling it a share of all people. |

- The model (`claude-opus-5-5`) only chooses tools and arguments. Arguments are validated against
  the real data (allowed metrics, filter values, date ranges, feature bounds); it never writes
  or runs code.
- Each question is limited to 6 model turns and 8 tool calls.
- See [docs/architecture.md](docs/architecture.md) for details.

## Quickstart

Requires Python 3.11+.

```bash
python -m venv .venv
```

Activate it (`.venv\Scripts\Activate.ps1` on Windows, `source .venv/bin/activate` elsewhere),
then:

```bash
pip install -e ".[dev]"
```

```bash
python scripts/fetch_data.py
```

```bash
streamlit run app.py
```

For the chat tab, set `ANTHROPIC_API_KEY` in your environment or in
`.streamlit/secrets.toml` (see `.streamlit/secrets.toml.example`). The About and Data & sources
tabs work without a key.

The developer tabs (**Run a tool** and **Evaluation**) are hidden for client demos. To show them,
set `"show_developer_tabs": true` in `app_config.json`. Evals also run from the command line
(`python scripts/run_eval.py`). The About tab text is in `docs/about.md`.

## Tests and evaluation

```bash
pytest
```

Tests run offline on SYNTHETIC fixtures with a fake Anthropic client. After downloading the
data, the real-data evals run too.

```bash
python scripts/pin_expectations.py
```

```bash
python scripts/run_eval.py
```

`pin_expectations.py` recomputes the expected answers from the raw data with pyarrow and plain
Python, independently of the tool code. `run_eval.py --agent` also runs agent-level evals
(calls the API, costs tokens).

## Data and attribution

Statistics Norway table 14944: Obstacles to enterprise AI use by industry and size (2026), https://www.ssb.no/en/statbank/table/14944 (licence: CC-BY-4.0). Full details, inspection evidence and limitations:
[docs/data_sources.md](docs/data_sources.md). The data is downloaded at setup time and is not
committed to this repository.

## Deployment

The app is ready for Streamlit Community Cloud: point it at `app.py`; `requirements.txt` installs
the package, and you put `ANTHROPIC_API_KEY` in the app's Secrets settings. The data is not in
this repository. On the first start, the app downloads both Statistics Norway tables
automatically, validates them and caches them. **Download / refresh data** in the sidebar fetches
them again later.
