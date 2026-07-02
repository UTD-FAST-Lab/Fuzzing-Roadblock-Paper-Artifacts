# Fuzzing-Roadblock-Paper-Artifacts

Artifact bundle for a root-cause categorized fuzzing-roadblock benchmark: a set of
fuzzer-discriminating roadblocks (branches a coverage-guided fuzzer variant reaches
that another does not), each labeled with the program feature and fuzzer mechanism
that explains the gap.

The artifact lives in two top-level folders: **`code/`** and **`data/`**.

## `code/` — scripts

- **Roadblock collection** — building the roadblock set from the raw campaign data.
- **Hypothesis generation** — generating the feature/mechanism hypothesis for each roadblock.
- **Categorization** — grouping the hypotheses into the final mechanism categories.
- **Experiments** — the measurement and figure scripts behind the paper's results.

## `data/` — data

- **`prompts/`** — the generated evidence prompt for every roadblock.
- **`hypotheses/`** — the per-roadblock hypothesis (agent analysis) for every prompt.
- **Roadblock facts table** — the flat fact table over all roadblocks.
