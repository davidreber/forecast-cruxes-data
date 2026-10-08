# forecast-cruxes data release

Data release for the paper "Can LLMs Anticipate Their Cruxes?" (David Reber,
James Reber, Ari Holtzman, Victor Veitch; University of Chicago; arXiv preprint
forthcoming). It holds every model output behind the paper's headline
analyses: the forecasts, the scouting reports, the zero-shot (introspected)
and many-shot (extracted) cruxes exactly as the models produced them, with
repeats kept, the second introspection samples used as noise references, and
the matching criteria.

Companion repositories:

- the package, [forecast-cruxes](https://github.com/davidreber/forecast-cruxes)
  (import name `cruxes`, v0.4.0), which selects, scores and reports;
- the verification, [forecast-cruxes-verify](https://github.com/davidreber/forecast-cruxes-verify)
  (command `cruxes-verify`), which reruns the package on this release and
  checks the result against the numbers the paper prints. Those numbers ship
  with the verification repository, not here.

## Scope

This release reproduces the paper's headline analyses only: the section
"Results: Predicted against Discovered Cruxes" and the matching-criteria
table, that is both datasets, all three strictness levels and 5 to 25 cruxes
a side, plus the March Madness within-pool split (the run at 50 a side) and
the March Madness coarser-cut sweep.

It does not cover the appendix experiments: one-shot cruxes,
specificity/coarsening, evidence matching, the open-weight replication, the
contested games, and the cross-model grid. Their inputs are not here.

## Build

Built 2026-09-24T18:19:39+00:00 by the authors' builder,
`artifact_builder/build_release.py` in the verification repository, from our
internal runs, read only. The layout follows `docs/DATA_RELEASE_SPEC.md` in
the verification repository (spec version 2). This is the build the
verification repository was run against. LICENSE, README.md,
REDISTRIBUTION.md and `provenance/` are not outputs of the build; they are
listed in MANIFEST.json alongside the dataset files.

Apart from `provenance/`, the directory holds the paper's inputs and nothing
computed from them. The paper's numbers are results of running the package on
these inputs.

## Layout

```
datasets/march_madness/    59 games of the 2026 NCAA men's tournament (127 files)
datasets/metaculus/        15 binary Metaculus questions (458 files)
method/                    matching_criteria.json (1 file)
provenance/                the authors' recorded reranker scores (222 files + README)
MANIFEST.json              SHA-256, size and role of every file
```

Each dataset directory has:

- `dataset.json`: description, the question ids, the inclusion rule, the
  internal source of every file with its SHA-256, and the builder's checks
  (pool sizes, repeats removed).
- `questions.json`: one record per question with its outcome and source.
- `introspected.json`: the zero-shot cruxes, every premise as the model
  produced it, in recorded order, repeats kept.
- `extracted.json`: the many-shot cruxes, likewise, repeats kept.
- `noise_reference/introspected.json`: an independent second introspection
  sample with the same model, prompts and schema; the verifier's tolerances
  are measured from runs on it.
- `provenance/introspection_metadata*.json`: metadata of the two
  introspection runs.
- `forecasts/`: the forecasts the extracted cruxes were drawn from.

The package removes repeated premise texts before it selects (texts equal
after collapsing whitespace runs to one space, case sensitive, no fuzzy
matching; the first occurrence is kept) and reports how many it removed.

**March Madness** (`datasets/march_madness/`): 59 games, every matchup with a
known winner that both pools cover. Introspected: gpt-5.4-nano asked in
advance what would decide each matchup, five prompts, one call each
(4,182 premises, 63 to 86 per game; noise reference 4,307). Extracted:
gpt-5.4-mini extraction from pairs of disagreeing forecasts (9,504 premises,
137 to 197 per game; 88 repeats in 36 games). `forecasts/` has one file per
game (59); `scouting_reports/` one file per team (61 teams). Outcomes (winner
and round) are in `questions.json`.

**Metaculus** (`datasets/metaculus/`): 15 binary questions open in February
2026. Introspected: gpt-5.4, eleven prompts, 55 calls (4,727 premises, 243 to
359 per question; noise reference 4,605, same protocol). Extracted:
gpt-5.4-mini extraction from pairs of disagreeing forecasts by an automated
forecaster (gpt-5-nano with web search) (21,129 premises, 1,074 to 1,714 per
question; 321 repeats in 8 questions). `forecasts/<question_id>/<run>/`
holds 448 forecast files; one question has two forecast directories, and one
recorded forecast file (`2026-03-01_english-wikipedia-least/forecast15.json`)
was left out because it forecasts a different question. The Metaculus files
(`metaculus_post_ids.json`, `fetch_metaculus.py`, `metaculus_snapshot/`) are
described below.

**method/matching_criteria.json**: the three matching criteria, instruction
texts verbatim from the study's reranker prompt file; `resolution` is the
primary criterion.

**provenance/**: see `provenance/README.md`. These are results, not inputs;
nothing in the verification reads them unless asked to.

## Metaculus questions

What ships for each of the 15 questions: our question id (the first 80
characters of a slug of the Metaculus title as it read when we forecast), the
Metaculus post id (`datasets/metaculus/metaculus_post_ids.json`, copied into
`source.metaculus_post_id` of `questions.json`; 15 of 15 known), our recorded
title (in `questions.json`, and quoted in our forecast files), our forecasts
and our premises. The titles are the Metaculus titles; they ship because the
paper's appendix prints them and they are needed to identify the questions.

What does not ship: the question's text (description, resolution criteria,
fine print), its resolution and the community forecast. These are Metaculus
content, marked in `questions.json` as pending written permission from
Metaculus, and the `outcome` field of every Metaculus question is `null` on
purpose. If Metaculus gives permission they go into
`datasets/metaculus/metaculus_snapshot/` (now a placeholder README). See
REDISTRIBUTION.md.

`datasets/metaculus/fetch_metaculus.py` (standard library only) reads the
Metaculus API for each post id and writes one JSON file per question to
`datasets/metaculus/metaculus_fetched/` by default. The API refuses anonymous
requests, so it needs a token (free: Metaculus account settings, API access),
from `METACULUS_API_TOKEN` or `METACULUS_API_KEY` or `--env-file`. What an
ordinary account token is shown (checked 2026-09-24): the title, ids, type,
status, open, close and resolve times and tournaments of every question, and
the text of open questions; not the text or resolution of resolved questions,
and no community forecast for any question tried, resolved or open. All 15 of
ours are resolved, so the script cannot fill the gap for readers; fuller
access is by request to Metaculus. Each fetched file lists what the API
withheld.

Three post ids are ambiguous: two posts carry the identical title, the one in
the Spring 2026 FutureEval Bot Tournament (where the other 12 questions are)
is chosen, and the other is listed in `alternative_post_ids`. Nothing we
recorded settles them.

| question id | chosen post | alternative |
|---|---|---|
| `will-ali-khamenei-cease-to-be-supreme-leader-of-iran-before-april-1-2026` | 42232 | 42148 (in no tournament) |
| `will-any-political-party-or-coalition-acquire-a-supermajority-in-the-2026-hungar` | 42231 | 40005 (in no tournament) |
| `will-the-uk-increase-the-qualifying-period-for-settlement-to-10-years-before-may` | 42230 | 42135 (Metaculus Cup Spring 2026, the post 2 of our 30 forecasts link) |

Verification reads none of this: it reads the premises and nothing of the
question text, ids or outcomes. `cruxes-verify check` ignores
`datasets/*/metaculus_fetched/`, so fetching inside this directory does not
break the check.

## How to verify

```
git clone https://github.com/davidreber/forecast-cruxes-data RELEASE
pip install "forecast-cruxes[gpu] @ git+https://github.com/davidreber/forecast-cruxes"
pip install "git+https://github.com/davidreber/forecast-cruxes-verify"

cruxes-verify check --artifact RELEASE                     # manifest hashes only
cruxes-verify all   --artifact RELEASE --work-dir WORK     # prepare, score, report, compare
```

`WORK` is an empty directory. The scoring stage runs the reranker and needs a
CUDA GPU; `--array-task-id i --num-array-tasks n` splits it across jobs
(see the verification repository's README). Adding `--recorded-scores RELEASE/provenance/scores` to `compare` (or
`all`) enables the raw-score check against the authors' recorded scores.

## MANIFEST.json

Lists every file in the release with its SHA-256, size in bytes and role, and
records `release_status`, the build time, the builder and the internal source
paths the builder read (`internal_sources`, paths on the authors' cluster,
kept for provenance). `cruxes-verify check` fails on any missing, changed or
unlisted file.

## Glossary

| in the files | in the paper |
|---|---|
| premise | crux |
| introspected | zero-shot cruxes (predicted) |
| extracted | many-shot cruxes (discovered) |
| noise reference | second introspection sample |
| criterion `equivalence` | strict matching level |
| criterion `resolution` | middle matching level ("moderate" in `matching_criteria.json`) |
| criterion `debate` | broad matching level |

## Licence

The authors' own outputs in this repository are licensed under
[CC BY 4.0](LICENSE). Metaculus question titles and ids are used under the
terms described in [REDISTRIBUTION.md](REDISTRIBUTION.md).

## Citation

```bibtex
@misc{reber2026cruxes,
  title  = {Can {LLMs} Anticipate Their Cruxes?},
  author = {Reber, David and Reber, James and Holtzman, Ari and Veitch, Victor},
  year   = {2026},
  note   = {arXiv preprint forthcoming. Data: https://github.com/davidreber/forecast-cruxes-data}
}
```
