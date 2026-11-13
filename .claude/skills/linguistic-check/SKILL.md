---
name: linguistic-check
description: Check the wording of use cases and user stories against da Silva's (2021) linguistic patterns and 38 guidelines, adapted to KRUZE's writing rules. Covers terminology consistency, strong verbs, specific nouns, actor perspective, active voice, branching in steps, UI language and trigger events. Use after writing or changing a UC or stories, or as a cross-document terminology sweep.
user-invocable: true
argument-hint: "[UC-NNN | story file or epic folder | all]"
allowed-tools: Read, Grep, Glob
agent: linguistic-review
---

Run a linguistic check on `$ARGUMENTS`.

---

## Step 0 — Read references
- `.claude/skills/_shared/references/linguistic-guidelines.md` (Parts A and B)
- `.claude/skills/create-use-case/references/writing-rules.md`
- `.claude/skills/create-use-case/templates/uc-template.md`
- `docs/product/backlog/GLOSSARY.md`

## Step 1 — Determine scope
| Argument | Documents to check | Comparison set for consistency |
|---|---|---|
| `UC-NNN` | `docs/use-cases/UC-NNN-*.md` | Glossary, plus the other UCs with the same **Interface** value |
| A story file or `epic-N-*` folder | The story files | Glossary, plus the parent UC(s) the stories trace to |
| `all` | Every file in `docs/use-cases/` (skip `INDEX.md`) | Glossary, plus the whole set |
| Empty | Ask the user which document to check | — |

For `all`, check terminology, consistency and the G32 conventions first. Then report per-document findings only at **Critical** severity, so the report stays readable.

## Step 2 — Build the terminology register
Before judging individual sentences, list every **actor term** and every **business entity term** used in scope:
- Actor terms come from Metadata, step subjects and "As a …".
- Entity terms are the nouns that are objects of steps.

For each term, record:
- where it occurs
- the matching glossary term, or "not in glossary"
- any synonyms or variant spellings found (L2, L4, G1, G2, G10)

This register drives the Critical consistency findings.

## Step 3 — Check use cases
Work through each section in order and apply the Part B mapping:

| Section | Guidelines |
|---|---|
| Title | G8, G9, G10, G11 |
| Metadata | G2, G3, G14 (one Primary Actor; secondary actors are external systems or persons) |
| Preconditions and postconditions | L1, L3, G10 (business state in specific nouns, no technical terms) |
| Main flow, per step | G35 (subject = defined actor or "the system"), L3 (active voice), G37/L5 (no branching), G38 + Table 3 (verbs and UI terms), G9/G10, B.2 vague terms |
| Main flow, as a whole | G23, G24 (one happy path, in time order). If the system starts the flow: G15, G16, G36 trigger phrasing. G32 opening and closing compared with sibling UCs. |
| AF/EF | G29 name, G31 classification, Rules 19–20 (branch, rejoin and detection steps exist and match), G19/G21/G26 cross-reference phrasing, G25 (overly complex AF → separate UC) |
| Business rules | L1, L3, G10 (precise, validatable wording, no vague terms) |

Do not check: Implementation Notes or Implemented By, which are technical by design; G5, G12, G13, G18, G20 or G22.

## Step 4 — Check user stories (if in scope)
Apply Part B.1 only: role (G2, G3), want clause (G8–G11, L3), benefit (L1), and acceptance criteria (L4, G10, G37; UI terms as Suggestions only).

## Step 5 — Produce the report
Use `.claude/skills/linguistic-check/templates/linguistic-review-report.md`. Every finding gives:
- the location
- the quoted text
- the guideline ID(s)
- a concrete rewrite that respects the KRUZE rules

Return the report in the conversation. Do not write files. If the user wants the report saved, the calling session stores it under `docs/reviews/`.

---

## Retrospective
After completing this task, or after a review session with the user, look back at the conversation for corrections or feedback the user gave. If the user pointed out false positives, missed patterns or preferred phrasings that this skill or `linguistic-guidelines.md` Part B does **not already capture**:
1. List each lesson learned: what was wrong and what the correct approach is.
2. Propose specific changes to this skill file or to Part B of the reference. Never change Part A, which is a faithful summary of the paper.
3. Propose any user-specific preferences to save to memory.

**Do not apply the changes.** Present the proposals and wait for the user to approve them.
