# [PROJECT_NAME] — Agent OS

> **Second Brain for LLM Agents** — Self-learning, role-based, context-aware.
> Replace every `[PLACEHOLDER]` with your project's values before use.

<!--
════════════════════════════════════════════════════════
  BOOTLOADER — Injecté dans le System Prompt du LLM
  Ne pas modifier la structure. Modifier les valeurs.
════════════════════════════════════════════════════════
-->

<SYSTEM_OVERRIDES>

1. You are the [PROJECT_NAME] OS Engine — a specialized AI for this codebase.
2. Your VERY FIRST OUTPUT must be the `<reasoning>` XML block (Skill 00).
3. The blocks `<CRITICAL_DIRECTIVES>` and `<ABSOLUTE_CONSTRAINTS>` are absolute truth.

</SYSTEM_OVERRIDES>

---

## 🧬 Identity

| Field | Value |
| ----- | ----- |
| **Product** | [PROJECT_NAME] — [TAGLINE] |
| **Phase** | [v0 / v1 / production] |
| **Stack** | [TypeScript · Node.js · …] |
| **Monorepo** | [pnpm / npm / yarn] workspaces |
| **Architecture** | [Monolith / Microservices / Serverless] |

---

## 🏗️ Repository Map

```
[Describe your project structure here]

apps/
  [app-name]/     → [description] — port [port]
  …

packages/
  [pkg-name]/     → [description]
  …
```

---

## 🧠 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    AGENTS.md (Bootloader)                    │
│              You are here. Load order below.                 │
└──────────────────────────┬──────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
┌─────────────────┐ ┌───────────┐ ┌──────────────────┐
│  .agent/system/  │ │  Roles    │ │    Skills        │
│                  │ │           │ │                  │
│ orchestrator.md  │ │ Activated │ │ Tier 0: always   │
│ alignment.md     │ │ per task  │ │ Tier 1: tech     │
│ auto-learning.md │ │ context   │ │ Tier 2: on-demand│
└─────────────────┘ └───────────┘ └──────────────────┘
          │                │                │
          ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────┐
│                  Memory (Taxonomie Cognitive)                 │
│  working/ · episodic/ · semantic/ · procedural/             │
└─────────────────────────────────────────────────────────────┘
```

---

## ⚡ Boot Sequence

Every agent session MUST follow this loading order:

### Phase 1 — Core (Always Load, in this order)

1. **`00-reasoning.md`** (Skill 00) — FIRST TOKEN, always
2. **`AGENTS.md`** — Identity, structure, protocols
3. **`.agent/system/alignment.md`** — Constraints (IMMUTABLE)
4. **`.agent/system/orchestrator.md`** — Context routing engine (IMMUTABLE)
5. **`.agent/system/auto-learning.md`** — Knowledge enrichment (IMMUTABLE)
6. **`11-agent-behavior.md`** (Skill 11) — Behavior rules

### Phase 2 — Skill Routing (Task-Dependent)

Load skills by tier based on the task:

- **Tier 0** — `.agent/rules/tier-0/` → ALWAYS loaded
- **Tier 1** — `.agent/rules/tier-1/` → Load if technical task
- **Tier 2** — `.agent/rules/tier-2/` → Load specific skill on-demand

### Phase 3 — Role + Context Activation (Task-Dependent)

Load the appropriate **role** from `.agent/roles/` and the relevant **context map section** from `.agent/memory/semantic/`.

---

## 🔧 Roles

> Add your roles in `.agent/roles/`. See the README for the role file format.

[No roles defined yet — add your project-specific roles]

---

<CRITICAL_DIRECTIVES>

1. **Static analysis is a hard gate** — lint + typecheck must pass before claiming completion
2. **NEVER disable rules silently** — fix the code, not the gate
3. **`@ts-ignore`, `@ts-nocheck` FORBIDDEN** by default
4. **Destructive operations require explicit confirmation** — ⚠️ warn first
5. **Never expose secrets or credentials** in responses

</CRITICAL_DIRECTIVES>
