# Codings of non-technical workers' use of AI on data
  [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23896530.svg)](https://doi.org/10.5281/zenodo.23896530)

Version 1, 2 November 2026. Author: Dragos Tomescu. Coding by Claude (Anthropic), under Dragos's direction.

A hand-built set of labels for 199 workers who do some data or number work outside a technical job. It records how they say they use AI on that work, what they say it does for them, what worries them, and what they do about it. It holds labels only, with no interview text.

## Source and licence

The labels describe interviews from the workforce split of the [Anthropic Interviewer dataset](https://huggingface.co/datasets/Anthropic/AnthropicInterviewer) (1,000 of the 1,250 interviews in the release), published by Anthropic under CC-BY. Join on `transcript_id` (for example `work_0002`) to read the original text.

This dataset is released under CC-BY 4.0. If you use it, credit Anthropic for the interviews and this dataset for the labels. Suggested citation: *Tomescu, D. (2026). Codings of non-technical workers' use of AI on data, derived from the Anthropic Interviewer dataset (workforce split). Version 1.* https://doi.org/10.5281/zenodo.23896530

## Files

| File | What it holds |
|---|---|
| `data/codings_non_technical_people.csv` | One row per person, 199 rows, 36 columns |
| `data/codebook_v3.csv` | What each column means (column, group, meaning, note) |
| `METHOD.md` | The full method and findings, with figures in `figures/` |
| `CITATION.cff` | Citation details for the "Cite this repository" button |
| `LICENSE` | CC-BY 4.0 |

## Which rows to use

The file holds the 199 people in the widest version of the analysis ("version B"). The column `in_main_analysis` marks the 188 people in the main analysis ("version A").

| Subset | Rows |
|---|---|
| Main analysis (`in_main_analysis` = 1) | 188 |
| Version B (`in_version_B` = 1, all rows) | 199 |
| Of the 188: use AI on data (`stance` is `uses` or `partly`) | 157 |
| Of the 188: avoid it or wish to use it (`avoids_or_wishes` = 1) | 31 |

The 11 people in B but not A were borderline cases, judged against a looser bar. Report which version you use.

## Columns

Binary codes are 1 if the person said it, 0 if not. There are no missing values.

| Group | Columns | Notes |
|---|---|---|
| Identifier | `transcript_id` | Matches the source dataset |
| Design | `pool`, `in_main_analysis`, `in_version_B` | `pool`: `core pool` (passed the screen) or `rescued` (re-read from the dropped pile) |
| Stance | `stance`, `avoids_or_wishes` | `uses`, `partly`, `avoids` (chooses not to) or `wishes` (has not used AI on data but wants to) |
| Use | `U0` to `U8` | For example `U1` formulas, `U2` cleaning data, `U4` interpreting or finding patterns, `U5` calculations |
| Help | `H1` to `H5` | `H1` saves time. `H5` is a hoped-for benefit |
| Concern | `C1` to `C8` | `C1` wrong or silent errors, `C2` checking costs as much as doing the work, `C3` privacy or policy |
| Coping step | `P1` to `P8` | `P1` checks the output, `P3` keeps high-stakes work manual, `P6` a human signs off |

The full definitions are in `data/codebook_v3.csv`. Codes are not mutually exclusive, so one person can carry several. Percentages built on overlapping groups, such as the uses, will not sum to 100.

## How the labels were made

1. A keyword screen cut the 1,000 interviews to 494. Each was labelled from a short card, then 207 of the dropped ones were re-read in full, which added 61 people ("rescued").
2. Claude read and coded every interview. Each pool was split at random into a first half (used to build the codebook) and a second half (held back, then coded against predictions written down first). Who counted as relevant was decided after the split, so the second half dropped more people than the first, especially among the rescued group (30 of 31 stayed relevant in the first half against 18 of 31 in the second). Compare across versions A and B before relying on a small difference.
3. Dragos set the scope and checked small samples at each stage (5 checks of 9 to 15 transcripts). These checks changed the work, but no person re-coded the interviews.

The full method and findings are in [METHOD.md](METHOD.md).

## Known limits

- **One reader.** A second Claude agent re-coded 20 transcripts blind and agreed on 86% of the concern codes. This is a check by the same kind of model, not an independent human check.
- **Not representative.** These are people who mention data work, in a keyword-screened set. "Non-technical" is Claude's reading of each job intro, and Dragos agreed with all 15 he checked.
- **Say, not do.** The labels record what people said in an interview, not what they do. A 0 means the person did not mention it, not that it is untrue.
- **Small groups.** Several codes are rare (`H4`, `P7`), and some uses have fewer than 25 people. Treat gaps between them as hints.

## Reproduce a few numbers

```python
import pandas as pd

d = pd.read_csv("data/codings_non_technical_people.csv")
a = d[d.in_main_analysis == 1]                      # 188 people
users = a[a.stance.isin(["uses", "partly"])]        # 157 people

print(len(a), len(users))                            # 188 157
print(a.H1.sum(), a.C1.sum(), a.C2.sum(), a.P3.sum())  # 114 101 40 69
print(users.P1.sum())                                # 87 check the output
worried = users[users.C1 == 1]
print(worried.P1.sum(), len(worried))                # 64 of 87 worried users check
print(users[users.U4 == 1].P1.agg(["sum", "count"])) # 24 of 54 interpreters check
```
