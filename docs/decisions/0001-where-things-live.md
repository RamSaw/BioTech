# 0001 — Where things live, and notebooks vs scripts

Date: 2026-11-21. Status: proposed.

## Where things live

The rule: **anything I want to review, diff, or be judged on goes in Git. Anything large and binary
goes on the Hugging Face Hub. Google Drive keeps only conversational material.**

| Thing                                 | Lives in             | Why                                                              |
| ------------------------------------- | -------------------- | ---------------------------------------------------------------- |
| Track, worklogs, decisions, specs     | Git, Markdown        | Diffable, reviewable, renders on GitHub, readable as plain text  |
| Library code                          | Git, `.py`           | Diffable, lintable, importable, testable                         |
| Notebooks (thin drivers)              | Git, outputs cleared | Already the rule in `development_guide.md`                       |
| Raw + processed datasets under ~50 MB | Git                  | Reproducibility without an external dependency that can break    |
| Datasets over ~50 MB                  | HF Hub dataset repo  | Versioned, public, `load_dataset` works in Colab with no auth    |
| Model weights                         | HF Hub model repo    | Native `from_pretrained`, model cards, the field's default shelf |
| Deep-research / strategy prose        | Google Drive         | Already there, rarely edited, no benefit from diffing            |

Rejected alternatives, briefly:

- **Weights in Git / Git LFS.** LFS quotas are small and painful, binary blobs bloat clones forever,
  and nothing in the ecosystem reads weights from a GitHub URL natively. HF does all of this.
- **Weights or datasets on Drive.** Needs auth, so neither a recruiter nor a stranger's Colab can
  fetch them, and there is no model card. Fine as a personal backup, wrong as the primary home.
- **Kaggle for the dataset.** Works, but requires an API key for programmatic access, which breaks
  the "clone and run" property. HF needs no token for public repos.

A useful side effect: HF Hub is a plain public HTTPS endpoint, so this session can fetch published
weights and datasets directly. Drive cannot be reached the same way without connector auth, and a
Colab runtime cannot be reached at all.

## Notebooks vs scripts

Both, with a clear division of labour — this resolves the diffing-versus-plots tension rather than
picking a side:

- **All real logic lives in `.py` modules** under the project (`hla_binding_prediction/src/`):
  preprocessing, dataset construction, splitting, training loops, metrics, attribution. Diffable,
  lintable by the existing CI, reviewable in a PR, and reusable between notebooks.
- **Notebooks are thin drivers.** Clone the repo, import the module, call three functions, plot. A
  notebook cell should be a call and a chart, not a definition. `development_guide.md` already says
  this ("No Plain Code in Cells", "Modularize Logic") — the current notebooks do not follow it,
  since every function is defined inline. Phase 1 is a natural moment to extract them.

This keeps the thing notebooks are genuinely good at — plots and narrative next to the code — while
the reviewable substance sits in files that produce readable diffs.

## Colab

- **Do I need Colab for this project?** Mostly no. ESM-2 8M and 35M on ~40k short peptides is small;
  it trains on CPU in a manageable time and on any free T4 in minutes. TPUs are irrelevant here —
  they pay off for large-scale pretraining, not for fine-tuning a 35M-parameter model, and the
  PyTorch/TPU path costs more friction than it returns. Reach for Colab when moving to ESM-2 650M,
  or when a sweep makes CPU time annoying.
- **Workflow when Colab is needed:** push `.py` changes to the branch → in Colab, `git clone` (or
  `pip install -e`) the repo → run a short driver notebook → push artifacts to HF Hub. Artifacts
  come back to this session via HF, not via Colab.
- **Integration honestly:** there is none. No tool here can drive a Colab runtime, read its state,
  or pull its outputs. The repo and HF Hub are the handoff points, and that is the whole reason for
  the split above. Treat Colab as a GPU rental that happens to render plots, not as the project's
  home.
