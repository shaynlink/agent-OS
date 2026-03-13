# Agent OS — Methodology

> **The complete step-by-step methodology** — what happens, in what order, and why.
> This documents the v2 system behavior, from boot to learning.

---

## System Architecture at a Glance

```
AGENTS.md                          ← Bootloader (customize this)
.agent/
  system/
    alignment.md                   ← Core constraints [IMMUTABLE]
    orchestrator.md                ← Context routing engine [IMMUTABLE]
    auto-learning.md               ← Knowledge protocol [IMMUTABLE]
  rules/
    tier-0/                        ← Always loaded (first tokens)
      00-reasoning.md              ← FIRST TOKEN — mandatory
      11-agent-behavior.md         ← Safety & communication
    tier-1/                        ← Technical core (if technical task)
    tier-2/                        ← Domain-specific (on-demand)
  roles/                           ← Role presets (task-activated)
  memory/
    working/                       ← Session-scoped (volatile)
    episodic/                      ← Lessons learned, bugs, RCAs
    semantic/                      ← Codebase knowledge maps
    procedural/                    ← Proven patterns and workflows
```

---

## Phase 1 — Boot Sequence

Every session follows this **deterministic boot order**:

```
① Skill 00 (Reasoning)  ← ABSOLUTE FIRST — primacy bias (Liu 2023)
② AGENTS.md             ← Project identity, stack, repository map
③ alignment.md          ← Core constraints + conflict detection
④ orchestrator.md       ← Context routing engine
⑤ auto-learning.md      ← Learning protocol
⑥ Skill 11 (Agent Behavior) ← Safety and communication
```

> **Why Skill 00 first?** Liu (2023) — *Lost in the Middle* proves that LLMs give more weight to tokens at the beginning and end of context. Placing the reasoning protocol first maximizes its influence.

---

## Phase 2 — Task Analysis (Orchestrator)

The orchestrator analyzes the user request for **signals** to route the appropriate resources:

### 2.1 — Skill Tier Routing

```
TIER 0   → Always loaded (00-reasoning + 11-agent-behavior)
TIER 1   → Loaded if task involves code, architecture, security, database, API, or testing
TIER 2   → Loaded on-demand when domain-specific signals are detected
```

**Loading rule**: Load the minimum set of skills that covers the task domain. Never load all skills.

### 2.2 — Selective Context Loading

The orchestrator loads **sections** of context maps, not entire files (Gorilla + RAG):

```
Signal: "modify [entity X]"        → load section #entity-x from semantic map
Signal: "create a new [entity]"    → load the full relevant context map
Signal: "debug [specific issue]"   → load targeted section + episodic/known-pitfalls.md

Priority: targeted section > full file > index summary
```

### 2.3 — Retrieval-Aware Context Format

All files in `memory/semantic/` must use the INDEX format:

```markdown
## INDEX
- [Entity A](#entity-a) — keywords
- [Entity B](#entity-b) — keywords

## <section id="entity-a"> Entity A
[Full content]
</section>
```

---

## Phase 3 — Role Activation

### Single Role

Load the role file for the relevant expertise domain. The role adds:
- Domain-specific principles
- Standards and anti-patterns
- Checklist for quality

### Multi-Role Deliberation (MoA — Wang 2024)

When 2+ roles are needed, apply the **Role Deliberation Protocol**:

```markdown
### Phase 1 — Independent Analysis (per role)
  [role-a]: "From my [domain A] angle, the problem is..."
  [role-b]: "From my [domain B] angle, the risk is..."

### Phase 2 — Conflict Identification
  "⚠️ TENSION: [Role A] suggests X, [Role B] requires Y"
  "Resolution: Y wins because [Safety/Architecture] > [other concern]"

### Phase 3 — Synthesis
  "Synthesized response considering both perspectives..."
```

---

## Phase 4 — Execution

### 4.1 — Skill Compliance

Before writing any code:
1. Identify applicable skills from the loaded tiers
2. Apply their standards to the implementation
3. Reference the skill in the response: `📚 Skill NN — [Name]`

### 4.2 — Self-Consistency Gate (Pre-Commit)

Before finalizing any response with code or architectural decisions (Wang 2022 — Self-Consistency):

**Check 1 — Internal Consistency**
- [ ] Does the response contradict any loaded context?
- [ ] Are types/interfaces consistent across files?
- [ ] Do imports reference exports that actually exist?

**Check 2 — Cross-Reference Verification**
- [ ] If I state "service X does Y" → did I verify the file?
- [ ] If I propose a pattern → is it consistent with existing patterns?

**Check 3 — Confidence Assertion**
```
CERTAIN  (seen in file)           → state it
PROBABLE (inferred from structure) → "probably, verify"
UNCERTAIN (not verified)          → "I suggest verifying"
```

---

## Phase 5 — Post-Execution Learning (Verbal RL Loop)

After every significant task, apply the **Verbal RL protocol** (Shinn 2023 — Reflexion):

### Error Signal → `memory/episodic/`

```markdown
**Episode**: [date, task, context]
**What happened**: [factual description]
**Root cause**: [5 Whys RCA]
**Verbal reflection**: "Next time, I must..."
**Extracted rule**: [actionable positive rule]
**Confidence**: [0.0-1.0]
```

### Success Signal → `memory/procedural/`

```markdown
**Pattern name**: [name]
**Conditions**: [when to apply it]
**Steps**: [full rationalization]
**Evidence**: [result obtained]
**Confidence**: [0.0-1.0]
```

### Confidence Scoring Before Integration

```
Score < 0.6   → NOTE ⚠️  → provisional, mark "TO CONFIRM"
Score 0.6-0.8 → PATTERN 🟡 → integrate with warning tag
Score > 0.8   → RULE ✅  → integrate as firm rule

Rule: NEVER crystallize a practice observed only once as a RULE directly.
```

---

## Instruction Conflict Detection Protocol

Whenever two instruction layers contradict each other, **explicitly declare** the conflict:

| Conflict Type | When Detected | Resolution |
|---|---|---|
| **Type A** — Role vs Skill | Role suggests a pattern contrary to a skill | Skill wins. Document the tension |
| **Type B** — User vs Alignment | User requests something that violates ABSOLUTE_CONSTRAINTS | Polite refusal + explanation + alternative |
| **Type C** — Context Map vs Real Code | Context map says X but the file says Y | The file wins. Update the context map |

---

## Graceful Degradation (Context Budget)

| Available Context | Loading Strategy |
|---|---|
| 200k+ tokens | Tier 0+1+2 + roles + full memory sections |
| 100–200k | Tier 0+1 + relevant roles + targeted sections |
| 32–100k | Tier 0 + alignment + primary role + Tier 1 headers only |
| <32k | **Tier 0 + alignment ONLY** — maximum caution, refuse complex refactors |

> **Rule**: LESS context = MORE caution. Never less.

---

## The Enrichment Workflow

```
DETECT   → Identify the learnable event (bug, success, new module)
VERIFY   → Confirm against actual codebase files (not assumption)
CLASSIFY → Error → episodic/ | Success → procedural/ | Structure → semantic/
SCORE    → Assign confidence score (< 0.6 / 0.6-0.8 / > 0.8)
FORMAT   → Write in Verbal RL format with source reference
UPDATE   → Add to the appropriate memory/ file
VALIDATE → Check: no duplication, no contradiction with existing knowledge
```

---

## The Complete Session Lifecycle

```
User sends request
  ↓
① Boot: Skill00 → AGENTS.md → alignment → orchestrator → auto-learning → Skill11
  ↓
② Task Analysis: detect signals → route skills → load context sections
  ↓
③ Role Activation: load role(s) → deliberation if multi-role
  ↓
④ Execution: apply skills → implement → Self-Consistency Gate
  ↓
⑤ Learning: Verbal RL → score → update memory/
  ↓
Response delivered
```
