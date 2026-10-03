# 0001 · Project name: "Dissecting π"

- **Status:** accepted
- **Date:** 2026-10-03

## Context

The project needs a name that (a) signals its angle: understanding *why*, not following a recipe; (b) is memorable and searchable; (c) survives the field moving on; (d) doesn't imply an affiliation it doesn't have. The working slogan "from zero to hero" was also on the table as a name.

## Options considered

| Name | Angle | Memorable | Search / collision | Ages well | Affiliation risk |
|---|---|---|---|---|---|
| **dissecting-pi** | ✅ "dissect" says *take apart to understand* | ✅ short, vivid | ⚠️ "pi" alone is ambiguous (math, Raspberry Pi), but the phrase "dissecting pi" had no collisions on GitHub, arXiv or PyPI (checked 2026-10-03) | ⚠️ tied to the π family; mitigated because π is a *family* (π0 → π0.5 → π0.6 → π0.7), not one checkpoint | ⚠️ uses a company's model name; mitigated with an explicit disclaimer |
| dissecting-vla | ✅ | ➖ generic | ✅ "VLA" is a strong search term | ✅ survives any model | ✅ none |
| vla-autopsy | ✅ | ✅ | ❌ collides with the *Autopsy* forensics tool and "verbal autopsy" (openVA) | ✅ | ✅ |
| zero-to-hero-vla | ❌ sounds procedural | ✅ | ❌ strongly associated with Karpathy's *Neural Networks: Zero to Hero*; zero2robot uses the same idea | ✅ | ✅ |
| pi-from-scratch | ❌ sounds like a reimplementation tutorial | ➖ | ➖ | ⚠️ | ⚠️ |

## Decision

- **Repository slug:** `dissecting-pi` (the π symbol can't be used in GitHub or PyPI names)
- **Display name:** *Dissecting π*
- **Tagline:** *From zero to hero in vision-language-action models.* The slogan stays as the tagline, where it reads as a promise rather than a borrowed brand.
- **Python package:** `dissectpi` (free on PyPI as of 2026-10-03). We avoid `nanopi` because NanoPi is an existing single-board-computer brand. "nano-π" is used only as the *name of the small model* in prose.

## Why π over the generic "VLA"

1. **The model-organism framing.** Biology students dissect a frog to learn anatomy shared by all vertebrates. π0.5 is the most widely used open VLA design, and its parts (VLM prefix, action expert, flow matching, FAST, knowledge insulation) recur across the field. Naming the organism makes the method concrete.
2. **Memorability beats breadth for a new project.** "Dissecting VLA" is a description; "Dissecting π" is a name. Search terms ("VLA", "vision-language-action", "pi0.5", "openpi", "lerobot") go in the GitHub description and topics, where search actually looks.
3. **Scope still extends.** Phase 5 (world-action models, hierarchy) is framed as *"Beyond π"*, comparing other designs against the π reference. A comparative-anatomy chapter fits the name.

## Consequences

- README and repo description must include "vision-language-action" and "VLA" explicitly for discoverability.
- Every page footer / README states: *independent educational project, not affiliated with or endorsed by Physical Intelligence.* We don't use PI's logo or visual identity.
- **Revisit trigger:** if the π family stops being released openly, or if more than half of the modules end up studying non-π architectures, reconsider a rename to `dissecting-vla` (a GitHub rename keeps redirects).
