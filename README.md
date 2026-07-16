# Fuzzing-Roadblock-Paper-Artifacts

The artifact lives in two top-level folders: **`code/`** and **`data/`**.

## `code/` — scripts

- **`BlockerAnalyzer/`** — the analysis pipeline: builds the roadblock set from the raw
  campaign, generates the root-cause hypothesis for each roadblock, and groups the
  hypotheses into the final root-cause categories.
- **`dockers/`** — the per-target build files (a build image and a `.cov` coverage image per subject).

## `data/` — data

- **`prompts/`** — the generated evidence prompt for every roadblock.
- **`hypotheses/`** — the per-roadblock hypothesis (agent analysis) for every prompt.
- **`raw_db/`** — the raw campaign tables (branches, seeds, study subjects) exported from the fuzzing runs.
- **`roadblock_facts.csv`** — the flat fact table over all roadblocks.
- **`raw_to_final_category_map.csv`** — maps each raw category to its final category.
- **`roadblock_category_examples.pdf`** — roadblock example per final category.
