<!--
════════════════════════════════════════════════════════════════
  AUTO-LEARNING — Knowledge Enrichment Protocol
  STATUS: IMMUTABLE
  DO NOT MODIFY this file. Knowledge is added to the memory/
  directories, not to this file.
════════════════════════════════════════════════════════════════
-->

# Auto-Learning — Knowledge Enrichment Protocol

> Every session that modifies the codebase should **enrich the knowledge base**.
> "The discovery of today prevents the bug of tomorrow."
>
> Source: Reflexion (Shinn 2023), STaR (Zelikman 2022), Self-Improve (Huang 2022)

---

## Learning Triggers

| Event | Action | Destination |
|---|---|---|
| Bug discovered or fixed | Verbal RL — Error Signal → RCA | `memory/episodic/known-pitfalls.md` |
| Remarkable pattern worked | Verbal RL — Success Signal | `memory/procedural/` or `rules/tier-X/` |
| New module / service created | Update knowledge map | `memory/semantic/[domain].md` |
| New schema / model added | Update schema map | `memory/semantic/[domain].md` |
| New convention established | Update relevant skill | `rules/tier-1/` or `tier-2/` |
| Security vulnerability found | Add anti-pattern | `rules/tier-1/` security skill |
| Problem resolved (RCA done) | Full RCA entry | `memory/episodic/rca-log.md` |

---

## Verbal RL Protocol

> Source: Reflexion (Shinn 2023) — 3 required components: Evaluator + Self-Reflector + Memory

### Error Signal (Episodic Negative)

**WHEN**: a task fails, a bug is found, a test doesn't pass

```markdown
**Episode**: [date, task, context]
**What happened**: [factual description]
**Why it failed**: [5 Whys RCA]
**Verbal reflection**: "Next time, I must..."
**Extracted rule**: [actionable positive rule]
**Confidence**: [score — see below]
**Destination**: memory/episodic/known-pitfalls.md
```

### Success Signal (Procedural Positive — STaR)

**WHEN**: a complex approach works remarkably well

```markdown
**Pattern name**: [name]
**Conditions**: [when to apply it]
**Steps**: [full rationalization]
**Evidence**: [result obtained]
**Confidence**: [score — see below]
**Destination**: memory/procedural/ or rules/tier-X/
```

---

## Confidence Scoring

> Source: Self-Improve (Huang 2022) — Avoid amplifying miscalibrated knowledge

Before integrating any new knowledge, assign a confidence score:

```
Score = f(occurrences, sources, verifiability)

Score < 0.6   → NOTE     ⚠️  Mark as "provisional — to confirm"
Score 0.6-0.8 → PATTERN  🟡  Integrate with a warning tag
Score > 0.8   → RULE     ✅  Integrate as a firm rule

RULE: NEVER crystallize a practice observed only once as a RULE directly.
```

---

## Enrichment Workflow

```
① DETECT    → Identify the learnable event
② VERIFY    → Confirm against real codebase files
③ CLASSIFY  → Error → episodic/ | Success → procedural/ | Structure → semantic/
④ SCORE     → Assign confidence score
⑤ FORMAT    → Write in Verbal RL format with source reference
⑥ UPDATE    → Add to appropriate memory/ file
⑦ VALIDATE  → Check: no duplication, no contradiction with existing knowledge
```

---

## Knowledge Quality Standards

Every knowledge entry must be:

| Criterion | Description |
|---|---|
| **Factual** | Based on verified behavior, not assumption |
| **Sourced** | Reference the file, line, or event that triggered it |
| **Actionable** | Expressed as a rule or step the agent can apply next time |
| **Current** | Reflects the current state of the codebase |
| **Concise** | Maximum 5 lines per entry |
| **RCA-backed** | For errors: root cause identified, not just symptom |

---

## Knowledge Freshness

Each context map entry should include `<!-- Updated: YYYY-MM-DD -->`.

```
Date > 30 days without code changes → Verify against actual codebase
File path referenced no longer exists → Flag for update
Library version changed significantly → Check for breaking changes
```

<!-- IMMUTABLE — Agent OS Boilerplate v2 — 2026-03-13 -->
