# Agent OS — Philosophy

> **The core ideas that explain WHY the system works the way it does.**
> Distilled from `reemx_agent_system_architecture.md` + 80 scientific papers.

---

## 1. The Fundamental Problem

LLMs (GPT, Claude, Gemini, Llama…) are **amnesiac generalists**:

| Problem | Consequence |
|---|---|
| **Amnesia between sessions** | Every conversation starts from zero. The agent "forgets" conventions, architectural decisions, past mistakes |
| **No specialization** | A raw LLM doesn't know your stack, your patterns, your domain constraints |
| **Inconsistency** | Two sessions produce contradictory results (session A uses Pattern X, session B uses Pattern Y) |
| **No organizational memory** | Resolved bugs, lessons learned, team conventions are lost |
| **No adaptive role** | The agent doesn't know if it should answer as a backend engineer, UX designer, or legal expert |
| **Cognitive overload** | Too much information kills information — context must be loaded intelligently |
| **Context bloat** | Loading all skills in full degrades performance — Liu (2023) proves a U-shaped degradation |
| **Untyped memory** | Without episodic/semantic/procedural distinction, the agent doesn't know how to update or exploit its knowledge |

### The Vision

> **Transform a generalist LLM into a permanent team member** who knows the project intimately, respects conventions, learns from mistakes, and adapts to the required role — **independently of the model used** (GPT-4, Claude, Gemini, Llama, Mistral).

### The Solution

A **structured file system** (`.agent/`) that acts as an **operating system for AI agents**:

```
Raw LLM       = a processor without an OS
.agent/       = the operating system
AGENTS.md     = the bootloader (BIOS)
```

---

## 2. Core Principles

| Principle | Description |
|---|---|
| **Context is Code** | Context is treated with the same rigor as source code: versioned, reviewed, maintained |
| **Convention Over Configuration** | Follow existing patterns rather than inventing new ones each session |
| **Knowledge Compounds** | Today's discovery prevents tomorrow's bug. Every session enriches the base |
| **Graceful Degradation** | The system works from 200k tokens to <32k — it degrades cleanly |
| **Role-Based Expertise** | The agent doesn't know everything — it activates the relevant expertise for the task |
| **Safety First** | When in doubt, the safest option. Never a shortcut at the expense of safety |
| **Evidence-Based Reasoning** | Verify before guessing. Read the file before modifying it |
| **Minimal Blast Radius** | The smallest possible change that correctly solves the problem |
| **LLM-Agnostic** | The system works identically regardless of which LLM executes it |
| **Memory Taxonomy** | Each type of memory has its own update rules — Sumers (2023), CoALA |
| **Progressive Loading** | Load only relevant skills for the task — Jiang (2023), LLMLingua |
| **Retrieval-Aware Context** | Target a specific section of a context map > loading the entire file — Patil (2023), Gorilla |

---

## 3. The Operating System Paradigm

The `.agent/` system contains **no executable code**. It's a set of Markdown files that **program the LLM's behavior** by structuring its input context.

```
┌─────────────────────────────────────────────────┐
│          LLM (any model)                        │
│                                                 │
│  ┌───────────────────────────────────────────┐  │
│  │            .agent/ (Context OS)           │  │
│  │                                           │  │
│  │  "What I know"    → memory/semantic/      │  │
│  │  "What I do"      → Skills (tiered)       │  │
│  │  "Who I am"       → Roles + Deliberation  │  │
│  │  "How I do it"    → memory/procedural/    │  │
│  │  "What I lived"   → memory/episodic/      │  │
│  │  "What is sacred" → alignment.md          │  │
│  └───────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

---

## 4. Hierarchy of Truth

When two instructions conflict, the resolution is **deterministic** and **explicitly declared** (Wallace 2024 — Instruction Hierarchy):

```
AGENTS.md (Bootloader)           ← ABSOLUTE priority
  > alignment.md (Constraints)  ← Never bypassable
    > Specific Skill (Rule)      ← Technical standards
      > Role (Persona)           ← Contextual expertise
        > User Prompt            ← User request
```

> **Rule**: A role can NEVER contradict a security skill. A user can NEVER bypass alignment constraints. **Every conflict must be detected and declared explicitly** — never resolved silently.

**Priority in case of conflict**:
```
Security > Architecture > Performance > Developer Experience
```

---

## 5. The Four Memory Types

Based on cognitive science (Sumers 2023 — CoALA, Anderson 2004 — ACT-R):

| Memory Type | Location | Content | Volatility |
|---|---|---|---|
| **Working** | `memory/working/` | Current session state, active task | Session-scoped (volatile) |
| **Episodic** | `memory/episodic/` | Past events: bugs, RCAs, lessons | Persistent, append-only |
| **Semantic** | `memory/semantic/` | Domain knowledge: architecture, schemas, APIs | Persistent, updated on code changes |
| **Procedural** | `memory/procedural/` | How-to: proven patterns, recipes, workflows | Persistent, updated on success signals |

Each type has **distinct update rules** — that's why they're separated.

---

## 6. Scientific Foundation

| Paper | Applied in |
|---|---|
| Liu 2023 — *Lost in the Middle* | Skill Tier system (primacy bias) |
| Jiang 2023 — *LLMLingua* | Progressive context loading |
| Shinn 2023 — *Reflexion* | Verbal RL Loop in auto-learning |
| Zelikman 2022 — *STaR* | Success Signal extraction |
| Wang 2022 — *Self-Consistency* | Pre-commit gate in orchestrator |
| Wang 2024 — *MoA* | Multi-role deliberation protocol |
| Patil 2023 — *Gorilla* + Lewis 2020 — *RAG* | Retrieval-aware context maps |
| Wallace 2024 — *Instruction Hierarchy* | Alignment conflict detection |
| Sumers 2023 — *CoALA* | Memory taxonomy |
| Anderson 2004 — *ACT-R* | Procedural memory patterns |
