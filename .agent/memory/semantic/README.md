# Memory — Semantic Memory

This directory holds structural knowledge about the project: codebase maps, domain models, API contracts.

## Retrieval-Aware Format

All files in this directory MUST use the Retrieval-Aware Format for selective loading:

```markdown
<!-- [filename].md — Retrieval-Aware Format -->
<!-- Updated: YYYY-MM-DD -->

## INDEX
- [Entity A](#entity-a) — keywords, tags
- [Entity B](#entity-b) — keywords, tags

## <section id="entity-a"> Entity A

[Full content here]

</section>
```

## Suggested Files

| File | Purpose |
|---|---|
| `codebase-overview.md` | High-level architecture map |
| `domain-models.md` | Data entities and their relationships |
| `api-contracts.md` | Endpoints, request/response shapes |
| `infrastructure.md` | Deployment, services, configuration |
| `dependencies.md` | Key libraries and their usage patterns |

## Loading Rule

The orchestrator loads **sections** of these files using the INDEX, not entire files.
This keeps context focused and avoids the Lost-in-the-Middle problem.
