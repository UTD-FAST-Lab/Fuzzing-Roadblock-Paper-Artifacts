# BlockerAnalyzer

BlockerAnalyzer collects the roadblock set from the raw campaign, generates a
mechanism hypothesis for each roadblock, then classifies those hypotheses into
mechanism categories. The code lives in three folders — **`scripts/`** (the
pipeline), **`tools/`** (measurement tools), and **`.claude/agents/`** (the LLM analysis agents).

## Project structure

```
BlockerAnalyzer/
├── CLAUDE.md
├── scripts/            # analysis pipeline
│   └── utils/          #   shared libraries
├── tools/              # evidence-test measurement tools
├── .claude/
│   ├── agents/         # generator, distiller, categorizer
│   └── settings.json   # grants Bash(*) so agents run shell commands unprompted
```
## `.claude/agents/` — agents

- **`generator.md`** (Opus) — analyzes one roadblock in isolation and writes its mechanism hypotheses (`.analysis.json`: hypotheses + evidence trail + falsifiability). Diffs winner-resolving vs loser-blocking seed bytes at the decisive gate; does not assign categories.
- **`distiller.md`** (Sonnet) — normalizes each hypothesis into a structured signature (closed gate slots + an open mechanism summary), one card at a time.
- **`categorizer.md`** (Sonnet) — for one decisive-shape, collapses the candidate mechanisms to the set the campaign data can discriminate and emits the evidence test (mechanism menu + measurement descriptors + deterministic decision rules) that can be used to label each roadblock.

**Push-mode:** `scripts/study_units.py evidence-per-branch --target T --branch-id M`
assembles the structured prompt (BLOCKER / TRIAL VECTOR / DECISIVE PAIRS / SOURCE
CONTEXT / BRANCH SEEDS / MECHANISM CONTEXT / TASK) and feeds it to `generator`.
The agent never queries the DB itself — the prompt IS the auditable evidence record.

## `scripts/` — analysis pipeline

- **`subject_significance.py`** — tests whether each (target, fuzzer-A, fuzzer-B) pair diverges significantly (AUC + final-coverage Mann-Whitney U-test); defines the canonical targets, fuzzers, and pairs.
- **`study_units.py`** — walks the coverage reports and admits input-dependent branches into the roadblock DB; hosts the per-branch evidence-prompt command.
- **`build_candidates.py`** — aggregates the decisive (≥8/8) fuzzer pairs per branch into the candidate roadblock set.
- **`select_representatives.py`** — dedups candidates by decisive-shape × source-region to one representative each.
- **`seed_bisect.py`** — Docker bisection that finds which corpus seeds resolve vs block each roadblock.
- **`callers_index.py`** — builds a per-target caller index (callee → its callers) for source context.
- **`per_role_coverage.py`** — llvm-cov dumps of winner-resolving vs loser-blocking seeds (the source-context overlay).
- **`evidence_prompt.py`** — assembles the structured evidence prompt for one roadblock (fed to the generator agent).
- **`run_hypothesis_fanout.py`** — builds the prompt manifest + per-roadblock prompt files for the agent fan-out.
- **`db_query.py`** — agent-facing database queries (seed lineage, extra seeds).

## `scripts/utils/` — shared libraries

- **`blocker_db.py`** — SQLite schema + connection helper for the roadblock database.
- **`seed_utils.py`** — dependency-free seed/byte helpers (count parsing, hex dumps, byte diffs).
- **`extract_functions.py`** — maps source functions to line ranges via `llvm-cov export`.

## `tools/` — evidence-test measurement tools

- **`operand_enrichment.py`** — target/decoy byte enrichment (`target_enrich`, `decoy_enrich`) of the I2S corpus vs the baseline corpus.
- **`value_distance_reached.py`** — closest Hamming `min_distance` any seed reaches to the gate comparand (value-profile gradient).
- **`seed_size_and_tags.py`** — median seed `size` + structural token count (`tags`) per corpus (assembly depth).
- **`depth_reach.py`** — post-gate execution-count ratio (`hit_ratio`) of resolving vs blocking seeds.
- **`corpus_size_ratio.py`** — winner/loser corpus-size ratio (`corpus_ratio`) at the branch. 