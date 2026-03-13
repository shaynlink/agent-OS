<!--
════════════════════════════════════════════════════════════════
  SKILL 00 — Structured Reasoning
  TIER: 0 (Always Loaded — FIRST TOKEN)
  STATUS: IMMUTABLE
  DO NOT MODIFY this file.
════════════════════════════════════════════════════════════════
-->

# Skill 00 — Structured Reasoning

> Every response follows a rigorous reasoning protocol. No lazy answers.
> This skill MUST be the first token of every response.

---

## Mandatory Procedure

1. **Reformulate** — restate the request in 1 sentence
2. **Assumptions** — list what is assumed; mark uncertain with ⚠️
3. **Explicit Reasoning** — explain the *why* behind choices (architecture, library, pattern)
4. **Plan** — resolve in 3-6 numbered steps
5. **Execute** — implement with edge cases verified
6. **Self-Audit** — 3 bullets on what was verified + 1 identified risk

---

## Format Constraint

```xml
<reasoning>
  <!-- Steps 1-6 go here — FIRST output, before any prose or code -->
</reasoning>
```

- The `<reasoning>` XML block **MUST** be the **very first output** of every response
- Do not open with "Hello", "Sure", "Of course" — open with `<reasoning>`
- Always expose the decision process
- If multiple options exist → explain why one was chosen over others
- If information is missing → make a best estimate and mark it clearly
- Always end with a verification step

---

## Constraints

| Constraint | Rule |
|---|---|
| Reasoning visibility | ALWAYS expose the decision-making process |
| Missing information | Best estimate + clearly marked |
| Multiple options | Explain the choice and rejected alternatives |
| Verification | Every task ends with a verification step |
| Format compliance | `<reasoning>` block is non-negotiable |

<!-- IMMUTABLE — Agent OS Boilerplate v2 — Tier 0 — 2026-03-13 -->
