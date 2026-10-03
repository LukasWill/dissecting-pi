# Contributing to Dissecting π

Thanks for wanting to help. The project's value is **trustworthy answers to "why" questions**, so contributions are judged on rigor and clarity, not on size.

## Ways to contribute

1. **Reproduce a module.** Run it on your hardware and open an issue titled `Reproduction: <module id>` with your numbers, hardware, and versions. This is the most useful thing a newcomer can do.
2. **Propose a question.** Open a Discussion: "Why does π0.5 do X?" Good questions become modules.
3. **Write a dissection.** Pick an issue labeled `good-first-dissection` or an accepted proposal and follow the [module template](docs/module-template.md).
4. **Fix and improve.** Clearer explanations, better diagrams, bug fixes in the harness.

## Rules for results

- Write your hypothesis before you run the experiment.
- Follow the evaluation protocol in the [roadmap](ROADMAP.md#2-founding-design-decisions): ≥ 3 seeds, ≥ 50 eval episodes per task per seed, 95% intervals, raw logs committed.
- Change one thing at a time. If you had to change two, say so.
- Null and negative results are welcome and get merged.
- Pin upstream versions (LeRobot / openpi commit) in the module.

## Code style

Readable over clever. A learner should be able to read any file top to bottom. Comments explain *why*, not *what*.

## Conduct

Be kind, assume good faith, and remember most people here are learning.
