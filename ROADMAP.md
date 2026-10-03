# Roadmap

> Living document. Last revised: 2026-10-03.
> Milestone dates are targets for a part-time solo maintainer. They will move, and when they do, this file says so.

## 1. North star

**A learner who finishes Dissecting π can take a new robot and task, decide what data to collect and how, choose and justify each part of a π-style VLA, train it, improve it with RL, and explain every choice with evidence.**

### What success looks like

| Horizon | Signal |
|---|---|
| 3 months | v0.1–v0.2 shipped; at least one module reproduced by someone other than the maintainer |
| 6 months | Data-engine and RL phases have first modules; ≥ 3 external contributors; modules cited in issues/discussions on openpi or LeRobot |
| 12 months | Used as onboarding material by at least one lab or course; results tables referenced by others |

Stars are a lagging indicator. **External reproductions** are the leading one, because they prove the modules work as teaching material and not just on the maintainer's machine.

### Non-goals

- Not a new state-of-the-art VLA, and not a leaderboard entry.
- Not a framework competing with LeRobot or openpi. We use their formats and models.
- Not large-scale pretraining from scratch. We study pretraining *decisions* at small scale and say clearly where small-scale results may not transfer.
- Not a paper digest. Every module must contain an experiment.

---

## 2. Founding design decisions

Each decision gets a short record in [`docs/decisions/`](docs/decisions/) when it is made or changed.

| # | Decision | Choice | Why |
|---|---|---|---|
| D1 | Reference model | π0.5 (openpi `pi05`, LeRobot `pi05`) | Most widely used open VLA design; flow-matching action expert, FAST, and knowledge insulation are all public and documented |
| D2 | Framework | PyTorch, LeRobotDataset format | Largest beginner community, real-robot support, π0.5 port exists in LeRobot; openpi (JAX) is used as a cross-reference |
| D3 | Learning model | **nano-π**: small VLM (SmolVLM2 class) + small action expert, every π0.5 choice behind a flag | Ablations must be cheap enough to run many seeds; SmolVLA proves this shape works at ~0.5B |
| D4 | Primary benchmark | LIBERO, starting with LIBERO-10 (long-horizon) plus a held-out variation split | Standard for π0.5 and most VLA papers, so numbers are comparable; LIBERO-10 is hard enough for nano-π that ablations aren't saturated |
| D5 | Evaluation protocol | ≥ 3 training seeds, ≥ 50 episodes per task per seed, report mean and 95% intervals, commit raw rollout logs | Small VLA comparisons are dominated by evaluation noise; see Module 0.2 |
| D6 | Real robot | SO-101 arm (Phase 4) | Low cost, well supported by LeRobot, already used for π0.5 fine-tuning by the community |
| D7 | License | Apache-2.0 | Matches openpi and LeRobot |

**Known risk on D4:** big models saturate standard LIBERO (π0.5 reports high-90s success rates). If nano-π also saturates a suite, we move that module to a harder split or a perturbed variant (e.g. LIBERO-Plus-style perturbations) rather than report meaningless ties.

---

## 3. Curriculum

Legend: 🟢 single 24 GB GPU · 🟡 rented large GPU · 🔴 real robot · ⭐ priority for embodied-RL roles

### Phase 0 · Foundations: "Before you cut, learn to measure"

| ID | Question | Deliverable |
|---|---|---|
| 0.1 | What actually happens in one π0.5 forward pass? | Shape-annotated walk-through notebook on dummy inputs; **code map** linking each block to openpi + LeRobot lines 🟢 |
| 0.2 | How do I know a change helped? | Eval harness; simulation of how CI width shrinks with episodes & seeds; "minimum detectable difference" table ⭐ 🟢 |
| 0.3 | Why do normalization and action spaces break so many fine-tunes? | Quantile vs mean/std normalization, absolute vs delta actions, joint vs end-effector space: what silently fails 🟢 |
| 0.4 | What is in a robot dataset? | LeRobotDataset anatomy, visualizing episodes, spotting bad demos by eye 🟢 |

### Phase 1 · Anatomy by ablation: the model

| ID | Question | The switch | Reading |
|---|---|---|---|
| Q1 | **Why an action expert?** | flow-matching expert vs FAST tokens (autoregressive) vs binned tokens vs regression head | π0, FAST |
| Q2 | Why action chunks, and why 50? | chunk length H ∈ {1, 5, 10, 25, 50}; executed steps per chunk; temporal ensembling | π0, Real-Time Chunking |
| Q3 | Why flow matching? | flow vs DDPM-style diffusion vs MSE; # integration steps {1, 2, 5, 10} | π0 |
| Q4 | Why that attention mask? | blockwise (prefix never attends to actions) vs fully causal vs fully bidirectional | π0 |
| Q5 | **Why stop the gradient?** | knowledge insulation on/off; also measure VLM's language/VQA retention and training speed | Knowledge Insulation |
| Q6 | How should robot state enter the model? | discretized text in prefix (π0.5) vs continuous token (π0) vs no state; probe causal confusion | π0.5 |
| Q7 | How much does pretraining matter? | random init vs VLM init vs VLA-pretrained init, at several data sizes | π0, π0.5 |
| Q8 | Freeze, LoRA, or full fine-tune? | which parts to train; LoRA rank; compute/memory/success trade-off | — |
| Q9 | How should flow time τ be injected? | adaRMSNorm (π0.5) vs concat + MLP (π0) | π0.5 |
| Q10 | Why co-train with web / VQA data? | co-training ratio vs language-following on unseen instructions | π0.5 |

**Bridge 1 (🟡):** reproduce Q1, Q5 and Q8 with LoRA on real `pi05_base`. The module states explicitly where nano-π's answer did or didn't hold at 4B.

### Phase 2 · The data engine: the data

| ID | Question | Experiment |
|---|---|---|
| D1 | How many demos do I need? | success vs # demos (10 → 500), per task difficulty; fit scaling curves |
| D2 | Quality or quantity? | inject pauses, jitter, failed-then-recovered segments; filter with heuristics (jerk, idle time, path length) vs influence-based curation (CUPID) |
| D3 | What kind of diversity helps? | state diversity vs action consistency between demonstrators | 
| D4 | **What mixture ratio?** | task : related-task : general data; uniform vs Re-Mix-style optimized weights; builds on Q10 |
| D5 | **Can I automate demo collection?** | scripted / motion-planner demos in sim; MimicGen-style augmentation from 10 seed demos; value of a generated demo vs a human one |
| D6 | How do I label data cheaply? | VLM-based success detection and subtask annotation; measure label error and its effect on training |
| D7 | How do I collect real demos efficiently? 🔴 | SO-101 teleop protocol, reset automation, live quality checks during recording, demos per hour as a tracked metric |

### Phase 3 · Post-training & RL: beyond imitation ⭐

| ID | Question | Experiment |
|---|---|---|
| R1 | Corrections or more demos? | HG-DAgger-style interventions vs extra demos, at equal human time |
| R2 | Can a VLA learn from its own failures offline? | advantage-conditioned training on autonomous rollouts (RECAP idea from π*0.6) |
| R3 | Cheapest RL that works? | steer the policy by learning over the initial flow noise (DSRL), leaving the VLA frozen |
| R4 | Full online RL for a flow VLA? | PPO / GRPO-style fine-tuning via stochastic flow sampling (πRL), many parallel sim envs 🟡 |
| R5 | Where do rewards come from? | sparse success, learned success detectors, VLM judges; detect and document reward hacking |
| R6 | Does RL-improved behavior survive the real world? 🔴 | sim-trained R3/R4 policies on SO-101 with sim-to-real gap analysis |

### Phase 4 · Real robot: deployment 🔴

Latency budget and async inference; real-time chunking; camera placement and calibration; safety limits; failure taxonomy from real rollouts. Includes a full SO-101 run of the pipeline: collect → curate → fine-tune π0.5 → RL-improve → deploy.

### Phase 5 · Beyond π: frontier (scoped later)

- **World-action models:** does predicting the future (video or latent) alongside actions help on the same harness? Compare a WAM-style policy against nano-π at equal data and compute.
- **Hierarchy:** π0.5's high-level subtask prediction, made explicit and ablated.
- **Next π versions:** dissect newly released components as they become public.

---

## 4. Milestones

Week numbers count from project start (week 1 = 2026-10-05).

| Release | Target | Contents | Launch action |
|---|---|---|---|
| **v0.1 "First dissection"** | week 2–3 | 0.1 anatomy + code map, 0.2 harness, **Q1** with full results | Public announcement (LeRobot Discord, X, LinkedIn, openpi discussion) |
| v0.2 "The model" | week 8 | 0.3, Q2–Q7, Bridge 1 started | Blog post: "10 things we measured about π0.5" style write-up |
| v0.3 "The data" | week 14 | D1–D5 | Release generated datasets on Hugging Face |
| v0.4 "Beyond imitation" | week 22 | R1–R4 | Write-up aimed at embodied-RL audience |
| v1.0 "Zero to hero" | week 30+ | Real-robot end-to-end run, Phase 4, polish, external reproductions | — |

### Next two weeks (v0.1), concretely

1. **Days 1–3:** read π0.5 in openpi (`pi0.py`, `pi0_config.py`, `gemma.py`) and LeRobot (`src/lerobot/policies/`, the pi0 / pi05 policies). Produce the anatomy diagram and code map in `docs/anatomy/`.
2. **Days 4–6:** LIBERO harness, headless rendering, seeded evaluation, interval reporting. Sanity check with a public π0.5 LIBERO checkpoint.
3. **Days 7–10:** nano-π v0 with switchable action heads (flow expert / FAST / regression). Train on LIBERO-10.
4. **Days 11–14:** Q1 runs (3 heads × 3 seeds), plots, module write-up, launch.

---

## 5. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Compute is too expensive | nano-π first; π0.5 only for Bridge replications; publish measured GPU-hours per module so learners can budget |
| Small-model findings don't transfer to 4B | Bridge modules test transfer explicitly; every nano-π result carries a "transfer status" tag |
| Benchmark saturation | Harder splits / perturbations (D4 note above); report ceiling effects openly |
| Noisy results lead to wrong conclusions | Protocol D5; negative and null results are published as-is |
| Upstream API churn (LeRobot, openpi) | Pin versions per release; CI smoke test runs one tiny training step |
| Scope creep | Non-goals above; Phase 5 stays unscheduled until v0.4 ships |
| Solo-maintainer burnout | Module template makes contributions self-contained; label `good-first-dissection` issues from v0.1 |

---

## 6. Reading list (core)

- π0: [A Vision-Language-Action Flow Model for General Robot Control](https://arxiv.org/abs/2410.24164)
- π0.5: [a Vision-Language-Action Model with Open-World Generalization](https://arxiv.org/abs/2504.16054)
- [FAST: Efficient Action Tokenization for Vision-Language-Action Models](https://arxiv.org/abs/2501.09747)
- [Knowledge Insulating Vision-Language-Action Models](https://arxiv.org/abs/2505.23705)
- [Real-Time Execution of Action Chunking Flow Policies](https://arxiv.org/abs/2506.07339)
- [SmolVLA: A Vision-Language-Action Model for Affordable and Efficient Robotics](https://arxiv.org/abs/2506.01844)
- [Data Quality in Imitation Learning](https://arxiv.org/abs/2306.02437)
- [Re-Mix: Optimizing Data Mixtures for Large Scale Imitation Learning](https://arxiv.org/abs/2408.14037)
- [CUPID: Curating Data your Robot Loves with Influence Functions](https://arxiv.org/abs/2506.19121)
- [Steering Your Diffusion Policy with Latent Space Reinforcement Learning](https://arxiv.org/abs/2506.15799) (DSRL)
- [πRL: Online RL Fine-tuning for Flow-based Vision-Language-Action Models](https://arxiv.org/abs/2510.25889)
- [π*0.6: a VLA That Learns From Experience](https://arxiv.org/abs/2511.14759) (RECAP)
