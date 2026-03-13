<!--
════════════════════════════════════════════════════════════════
  ORCHESTRATOR — Context Routing Engine
  STATUS: IMMUTABLE
  DO NOT MODIFY this file. Customize by adding signals to the
  Task Detection Table and adjusting Skill Tier routing.
  All project-specific routing belongs in AGENTS.md or roles.
════════════════════════════════════════════════════════════════
-->

# Orchestrator — Context Routing Engine

> The orchestrator decides **what to load, when, and how much** — preventing context bloat and ensuring the right expertise is activated for each task.
>
> Source: LLMLingua (Jiang 2023), Lost in the Middle (Liu 2023), Gorilla (Patil 2023)

---

## Phase 1 — Task Detection

Analyze the user request for **signals** to determine:
1. The task **domain** (database, API, frontend, security, etc.)
2. The **roles** to activate
3. The **context map sections** to load
4. The **Skill Tiers** to activate

### Task Detection Table

Fill this table with signals specific to your project after initial setup:

| Signal keywords | Domain | Tier 1 Skills | Tier 2 Skills | Context Map |
|---|---|---|---|---|
| [e.g. schema, model, collection] | Database | [e.g. 04-database] | — | [e.g. database-models.md] |
| [e.g. endpoint, route, controller] | API | [e.g. 05-api, 03-security] | — | [e.g. api-modules.md] |
| [e.g. bug, error, incident] | Debug | [e.g. 17-problem-resolution] | — | [contextual] |
| [add your signals] | … | … | … | … |

---

## Phase 2 — Skill Tier Routing

> Source: LLMLingua (Jiang 2023) — Load only what's needed.

```
TIER 0 — ALWAYS loaded (loaded FIRST, before everything else)
  → .agent/rules/tier-0/00-reasoning.md    ← THE FIRST TOKEN
  → .agent/rules/tier-0/11-agent-behavior.md

TIER 1 — Loaded if task is technical
  → .agent/rules/tier-1/*.md (all technical core skills)

TIER 2 — Loaded on-demand, one skill at a time
  → .agent/rules/tier-2/[specific-skill].md

RULE: Skill 00 is always Position 1 in the context window.
      Benefits from primacy bias (Liu 2023 — Lost in the Middle).
```

---

## Phase 3 — Selective Context Loading

> Source: Gorilla (Patil 2023), RAG (Lewis 2020)

Load **sections** of context maps, not entire files when possible:

```
Signal: "modify [entity X]" → load section #entity-x from the semantic map
Signal: "create a new [entity]" → load the FULL relevant context map
Signal: "debug [specific issue]" → load targeted section + episodic/known-pitfalls.md

Priority: targeted section > full file > index summary
```

**Context Map Format (Retrieval-Aware)**:

Each file in `memory/semantic/` should use this structure:

```markdown
<!-- [filename].md — Retrieval-Aware Format -->

## INDEX
- [Entity A](#entity-a) — keywords
- [Entity B](#entity-b) — keywords

## <section id="entity-a"> Entity A
[Full content here]
</section>
```

---

## Phase 4 — Role Deliberation Protocol

> Source: MoA (Wang 2024), MetaGPT (Hong 2023)

When 2+ roles are activated simultaneously:

```markdown
### Phase 1 — Analysis (per role, independently)
  [role-name]: "From my [domain] perspective, the problem is..."
  [role-name]: "From my [domain] perspective, the risk is..."

### Phase 2 — Conflict Identification
  "⚠️ TENSION DETECTED: [Role A] suggests X, [Role B] requires Y"
  "Resolution: Y wins because [Skill N] > [convention]"

### Phase 3 — Synthesis
  "Synthesized response taking both perspectives into account..."

RULE: When conflicts arise → Hierarchy of Truth applies:
  Security > Architecture > Performance > Developer Experience
```

---

## Phase 5 — Self-Consistency Gate (Pre-Commit)

> Source: Self-Consistency (Wang 2022), Hallucination Survey (Huang 2023)

**Before finalizing any response with code or architectural decisions**, run:

### Check 1 — Internal Consistency
- [ ] Does the response contradict any loaded context?
- [ ] Are TypeScript types consistent across files?
- [ ] Do imports reference exports that actually exist?

### Check 2 — Cross-Reference Verification
- [ ] If I state "service X does Y" → did I verify the file?
- [ ] If I propose a pattern → is it consistent with existing patterns in the context maps?
- [ ] If I reference a test → does that test actually exist?

### Check 3 — Confidence Assertion
```
CERTAIN (seen in file) → state it
PROBABLE (inferred from structure) → "probably, verify"
UNCERTAIN (not verified) → "I suggest verifying"
```

---

## Context Budget Manager

| Available Context | Loading Strategy |
|---|---|
| 200k+ tokens | Tier 0+1+2 + roles + full memory sections |
| 100-200k | Tier 0+1 + relevant roles + targeted sections |
| 32-100k | Tier 0 + alignment + primary role + Tier 1 headers only |
| <32k | Tier 0 + alignment ONLY — maximum caution |

---

## Skill Compliance Engine

After every code change, the agent MUST:

1. Apply at least **one skill** from `.agent/rules/`
2. Reference the skill in the response: `📚 Skill [N] — [Name]`
3. Apply the skill's standards BEFORE implementing
4. If no skill applied → 🔴 CRITICAL — declare the gap

<!-- IMMUTABLE — Agent OS Boilerplate v2 — 2026-03-13 -->
