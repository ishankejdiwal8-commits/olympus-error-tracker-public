# Challenge Submission Errors — Rejection Log & Pre-Build Checklist

**Purpose:** every rejection below was a technically strong pipeline that answered a *different problem* than the one posed. Score never rescues any of them — these are checked before the metric is looked at. Read this **before writing code**, not before submitting.

Last updated: 2026-08-10

---

## The three-sentence gate

Before the first line of a challenge pipeline, write these three sentences. If any one cannot be completed honestly, the design is already rejected.

1. **Supervision:** "The supervised signal comes from `<train label column>`, consumed by `<module>` via `<loss>`."
2. **Generation:** "The output tokens are emitted by `<trained module>` in a decode loop that does `<step / sampling / stopping rule>`."
3. **Isolation:** "Every `.fit()` in this pipeline sees train only; test appears exclusively inside `.transform()` and the final `predict()`."

If sentence 1 names no label, sentence 2 names a lookup/template/n-gram table, or sentence 3 has an exception — stop and rebuild.

---

## Error 1 — Inference-only / transductive pipeline (rule 1.1, 4.2, 5.1, 3.8)

**Verdict:** "Technically excellent, but it is an inference-only + transduction pipeline, which 1.1 rules out. Reject."

Six violations in one entry. **Each is disqualifying on its own.**

| # | Pattern | Concrete form in the rejected entry | Rule |
|---|---|---|---|
| 1 | No supervised training | Only train's `text` column used, for TF-IDF vocabulary; no train label required | 1.1 |
| 2 | Frozen encoder with nothing trained on top | bge-small used as the entire signal | 1.1 |
| 3 | Pseudo-labelling / self-training on eval texts | Self-training loop over the evaluation set | 4.2 |
| 4 | Calibrating output to the test distribution | IPF constraints forcing per-group positive counts and per-topic positive rates derived from **test** candidate counts onto emitted scores | 4.2 |
| 5 | Exploiting generator invariants | Assuming "the generator emits ~half positives" | 5.1 |
| 6 | Cross-run caching of per-example artifacts | Encoder similarities written to `.cache_ztd`, reloaded next run | 3.8 |

**Root cause:** the disqualification was visible in the file header ("no train label is required") before any modelling code was read.

**Note on frozen pretrained encoders:** legitimate as *features*. Illegitimate as the whole model. Something supervised must be trained on top of them using train labels.

---

## Error 2 — Fake generation (sequence-to-sequence requirement)

**Verdict:** "Bypasses genuine sequence-to-sequence learning by using an algorithmic, greedy n-gram assembly to generate static, pre-computed response portfolios... violates the requirement for actual ML-based sequence generation."

The defect: **the outputs existed before inference ran.** Inference only chose among them, using packet length plus a basic classifier.

Banned substitutes — any one sinks the entry, even if something *is* trained elsewhere in the pipeline:

- **Pre-computed output portfolios** — a fixed pool of candidate strings built at author/fit time. The output space must not be enumerable before seeing the input.
- **Selection-as-generation** — routing on cheap surrogate features (length, size, a class label, a nearest-neighbour hit) to pick a canned answer. A classifier that picks a string is a classifier, not a generator.
- **Algorithmic/statistical text assembly** — greedy n-gram stitching, Markov chains, template filling, rule-based composition.
- **Retrieval-only** — copying the nearest training target verbatim. Retrieval is legitimate only as *conditioning* for a decoder that still generates token by token.
- **Trained model present but off the output path** — a real model that only scores, ranks, or reranks candidates produced by one of the above. The trained model must emit the tokens.

**Required instead:** a conditional seq2seq model (transformer or fine-tuned pretrained LM) trained on train pairs with teacher forcing and token-level cross-entropy; at inference, decode conditioned on the actual input.

---

## Error 3 — Test isolation violated

**Verdict:** "Submission still builds unsupervised corpus statistics and embeddings from train and test together before fitting/scoring. This violates test isolation."

**"Unsupervised" is not a defence.** Test isolation is about information flow, not label access. IDF weights, vocabulary, embedding fits, PCA bases, scaler means — each encodes the test distribution into the model. At deployment the test rows don't exist yet, so a pipeline that needs them in order to fit cannot run.

**The rule:** the test set is **write-only until the final `predict()`**. Every `.fit()` / `.fit_transform()` sees train only; test reaches `.transform()` and nothing else.

```python
# REJECTED
vec.fit(pd.concat([train.text, test.text]))   # test inside the fit

# REQUIRED
vec.fit(train.text)
Xtr = vec.transform(train.text)
Xte = vec.transform(test.text)                 # test only ever transformed
```

Also counts as pooling: vocabulary built from the union, embeddings trained or adapted on the union, normalisation statistics over the union, dimensionality reduction fitted on the union, nearest-neighbour indexes built over train+test rather than train alone.

---

## Pre-submit audit — run all of it, every time

A clean bill on one section is worth nothing if another fails. These are independent gates.

### A. Supervision (Error 1)
- [ ] Name the train label column consumed and the loss that consumes it.
- [ ] No self-training or pseudo-labelling on evaluation texts, at any stage.
- [ ] No output calibration derived from test counts, rates, or distributions (IPF, quantile matching, rank normalisation to a target positive rate).
- [ ] No assumption about the label distribution or generator behaviour ("~half positives", balanced classes, fixed group sizes).
- [ ] No cache directory or pickle written between runs (`.cache_*`, `*.pkl` beside the output) that lets a later run skip training.
- [ ] Delete every cache, run end-to-end from a clean checkout, confirm identical output.

### B. Generation (Error 2)
- [ ] Grep for any list/dict/pickle of candidate output strings materialised before inference — none may sit on the output path.
- [ ] The output path contains a decode loop over model logits, not an index into a collection.
- [ ] Perturb one input: the output must change in a way no length or class bucket could explain.
- [ ] Feed an input unlike anything in train: it must still produce novel token sequences rather than snapping to a known string.
- [ ] The training objective is token-level cross-entropy over target tokens, not a classification or ranking loss over pre-built candidates.

### C. Isolation (Error 3)
- [ ] Grep every `.fit(` and `.fit_transform(` — confirm no test data, and no `concat`/`union`/`vstack` of train and test, reaches any of them.
- [ ] Grep every read of the test file: each must lie on the final predict path.
- [ ] Sanity test: run the whole pipeline with the test set replaced by a single row. Training artifacts must be byte-identical to the full run.

---

## Why this keeps happening

Four rejections in this family (2026-08-06, 08-07, 08-08, 08-10). The audit above was in place for the last three and did not stop them, because it was run *after* the code was written — by which point the design was already the problem. The three-sentence gate at the top exists to move the check to design time. Fill it in first.
