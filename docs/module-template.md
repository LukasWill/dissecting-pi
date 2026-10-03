# Module template

Copy this file to `modules/<id>-<short-slug>/README.md` (e.g. `modules/q01-why-action-expert/README.md`).
Keep the headings. A module without the **Result** section filled in with real numbers is a draft.

---

# <ID> · <The question, phrased the way a beginner would ask it>

> **One-line answer:** <filled in after the experiment, e.g. "Because X; on LIBERO-10 it is worth +N ± M points.">
> **Transfer status:** nano-π only · replicated on π0.5 · contradicted on π0.5
> **Compute:** <measured GPU-hours and GPU type for the full module>

## 1. Question

Why does this part exist? What would a reasonable person do instead?

## 2. Mechanism

- The code that implements it in nano-π (`dissectpi/...`), annotated.
- The matching code in openpi and/or LeRobot (file + line links pinned to a commit).
- A diagram if it helps.

## 3. Hypothesis

- What the original paper(s) claim, with a citation.
- What we expect to see, written **before** running the experiment.

## 4. Experiment

| | |
|---|---|
| Switch | `<config.flag>` ∈ {…} |
| Benchmark / split | |
| Training data | |
| Seeds | ≥ 3 |
| Eval episodes | ≥ 50 per task per seed |
| Everything else | held fixed; list anything that couldn't be |

Exact commands to reproduce:

```bash
# one command per variant
```

## 5. Result

Table with mean success and 95% interval per variant. Plot. Link to the raw rollout logs in `results/`.
Report what happened, including null or contradicting results.

## 6. Knob: what to do on your robot

"If your setup looks like ___, choose ___ because ___." Include the failure signs that tell you the knob is set wrong.

## 7. Go deeper

Papers, code, and open questions this module didn't answer (good candidates for new modules).
