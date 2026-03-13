# Roles

Place project-specific agent role files here.

## What is a Role?

A role activates a specific professional perspective for the AI. It defines:
- The domain of expertise
- Standards and principles for that domain
- Decision-making criteria from that perspective

## Naming Convention

Files follow: `role-name.md` (kebab-case)

## Role File Template

```markdown
# Role — [Role Name]

> [One-line description of this role's expertise]

## Perspective

[What lens does this role bring to a problem?]

## Standards

[Domain-specific rules, patterns, and conventions]

## Decision Criteria

[How does this role prioritize and evaluate tradeoffs?]

<!-- Source: [reference] -->
<!-- Updated: YYYY-MM-DD -->
```

## Multi-Role Activation

When 2+ roles are needed (e.g., "backend engineer + security expert"), the orchestrator
runs the **Role Deliberation Protocol** (see `system/orchestrator.md`):

1. Each role analyzes independently
2. Conflicts are surfaced explicitly
3. A synthesized response is produced

## How to Load Roles

Reference in `AGENTS.md` under the Phase 2 — Role Activation section.
