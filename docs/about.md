### What this app is for

Teams that want more people to use AI at work usually need to answer two questions first: **what
stops people and companies from adopting AI**, and **who is already using it**. AI Spoke Compass
answers those questions in plain English, with evidence.

You type a question such as *"What are the top three reasons large Norwegian enterprises give for not
using AI?"*. The assistant finds the answer in official statistics and replies with the numbers,
where they came from, and what they do and don't show. Use it to decide where training, guidance
or support will help most.

### What the AI agent does

The assistant is an **AI agent**: Claude (Anthropic) doesn't just generate text, it decides what
to look up and then reads the results before it answers.

1. **Understands the question.** It works out which data and which slice of it the question is
   about, for example a company size, an industry, an age group or a year.
2. **Chooses a tool.** It picks one of five purpose-built analysis tools (listed below) and fills
   in the filters, such as *"100 employed or more"* or *"45-54 years"*.
3. **Runs it on the real data.** The tool calculates the result directly from the Statistics Norway
   tables. The AI never makes up numbers or runs code of its own.
4. **Checks and continues.** It reads the result. If it needs more, it calls another tool, for
   example to compare company sizes after ranking obstacles. If a request is invalid, the tool
   explains why, and the agent corrects itself and retries.
5. **Answers with evidence.** It writes a short answer that cites the source, and points out
   caveats such as what a percentage is a share of.

Under each answer, **"How this answer was computed"** shows every tool the agent called, the exact
filters it used, the result it got, and the model turns and tokens used.

### The five analysis tools

| Tool | What it answers |
|---|---|
| Rank AI obstacles | Which obstacles to AI are cited most, for one industry and company size |
| Obstacle by company size | How one obstacle (e.g. privacy concerns) varies between small and large enterprises |
| Obstacle by industry | Which industries cite one obstacle (e.g. lack of expertise) most |
| Work use of generative AI by year | How work use of generative AI changed from 2024 to 2026 for one age group |
| Generative-AI use by age | How work and private use of generative AI differ between age groups in one year |

### Built-in safeguards

- **Limited scope:** the agent can only use these five tools on the two published datasets. It
  cannot browse the web, run code or touch any other system.
- **Validated inputs:** every tool checks its filters against the values that actually occur in
  the data, so a misspelled industry or age group is rejected rather than guessed.
- **Bounded effort:** at most 6 model turns and 8 tool calls per question, which keeps the response
  time and cost predictable.
- **Transparent results:** each answer shows exactly which calculations produced its numbers.
- **Tested calculations:** every tool is checked against expected answers that a separate script
  calculates independently from the same data.

### The data

Two public tables from **Statistics Norway (SSB)**, used under the CC BY 4.0 licence. They are
downloaded from SSB's official API and checked against the expected structure before use:

- **Table 14944:** obstacles to the use of AI technology in Norwegian enterprises (10+ employees),
  by industry and company size, 2026.
- **Table 14365:** use of generative AI tools for work and private purposes, by sex and age group,
  2024-2026.

The **Data & sources** tab shows each table's licence, its known limitations and its first rows.

### What it does not do

- These are **national statistics for Norway**, not data about any one company or its employees.
- The obstacle data covers one year (2026), and the generative-AI data covers three (2024-2026).
  The app describes these figures; it does not forecast.
- Statistics Norway defines what each percentage is a share of. For the generative-AI table the
  exact base is not confirmed here, and the assistant is instructed to say so when it uses
  those figures.
