# SNNEED
A Siamese Neural Network for Embedded Edit Distance

SNNEED maps strings to vectors so that the distance between two vectors approximates the
normalised Levenshtein similarity of the two strings. It is trained only on synthetic string pairs
and evaluated, without retraining, on synthetic strings and on protein domains written as
amino-acid (AA), secondary-structure (SS) and 3Di sequences.

This repository holds a standalone Colab notebook that reproduces the evaluation of the bachelor
thesis *Embedded Edit Distance: Levenshtein Approximation with a Siamese Neural Network for String
Similarity Search* by Melissa Analytis (TU Dresden, Chair of Bioinformatics, 2026). Section numbers
in the notebook refer to that thesis.

## Contents

```
SNNEED/
├── notebooks/
│   └── SNNEED.ipynb          the full pipeline: data, training, evaluation, figures
├── data/
│   ├── cath_s20_train70.csv.gz
│   ├── cath_s20_test30.csv.gz
│   └── cath_s20_3di.csv.gz
└── main_public.pdf           the thesis
```

## Running the notebook

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/katzemelli/SNNEED/blob/main/notebooks/SNNEED.ipynb)

1. Open the notebook in Colab with the badge above.
2. Select a GPU: *Runtime → Change runtime type → GPU*. The notebook also runs on a CPU, but
   training and the ESM-2 embeddings are much slower there.
3. Run all cells: *Runtime → Run all*.

The first cell fetches the data by itself. It uses files that are already present, otherwise it
downloads them from this repository, and if that fails it opens an upload dialog. It then checks
that every file contains the columns the notebook reads.

The notebook

* builds the four evaluation datasets (Synth, 3Di, SS, AA), their exact relevance sets and the
  decile-balanced pair sets,
* trains SNNEED three times (seeds 0, 1, 2) on 30,000 synthetic pairs,
* evaluates SNNEED, frozen ESM-2 (`facebook/esm2_t12_35M_UR50D`), 3-gram Dice and a length-ratio
  baseline with Spearman correlation, AUROC, MAP@10 and high-range RMSE,
* writes `colab40_results_raw.csv` (one row per method, dataset and seed) and
  `colab40_results_mean.csv` (means over seeds),
* draws the result heatmaps (thesis Figures 4.1, 4.2, 4.5 and 4.6).

## Data

| File | Columns read | Content |
|---|---|---|
| `cath_s20_train70.csv.gz` | `domain_id`, `aa_seq`, `ss_seq` | CATH-S20 domains, amino-acid and secondary-structure sequences |
| `cath_s20_test30.csv.gz`  | `domain_id`, `aa_seq`, `ss_seq` | the remaining CATH-S20 domains, same format |
| `cath_s20_3di.csv.gz`     | `domain_id`, `3di`              | 3Di sequences of the same domains |

The two AA/SS files are combined into one collection; the train/test split is not used by the
evaluation. Sequences are kept if they use only the expected alphabet (20 amino-acid letters for
AA and 3Di, `H`, `L`, `S` for SS) and have a length between 50 and 200. Two named domains are
exempt from the length rule (thesis Section 2.4.1).

Other data can be used in place of these files as long as it has the same file names and columns.
Results will then differ from the thesis.

### Sources and licences

* **CATH-S20**, release 4.3.0. CATH is distributed under the
  [Creative Commons Attribution 4.0 licence](https://creativecommons.org/licenses/by/4.0/).
  Sillitoe, I. et al. (2021). CATH: increased structural coverage of functional space.
  *Nucleic Acids Research* 49(D1), D266–D273. https://doi.org/10.1093/nar/gkaa1079
* **3Di sequences** use the structural alphabet of Foldseek and were provided by the Chair of
  Bioinformatics, TU Dresden.
  van Kempen, M. et al. (2024). Fast and accurate protein structure search with Foldseek.
  *Nature Biotechnology* 42, 243–246. https://doi.org/10.1038/s41587-023-01773-0
