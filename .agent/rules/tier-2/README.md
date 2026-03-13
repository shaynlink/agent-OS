# Tier 2 — Domain-Specific On-Demand Skills

Place specialized, domain-specific skills here. These are loaded only when the task requires them.

## Naming Convention

Files should follow: `NN-skill-name.md` (two-digit prefix, kebab-case)

## When to Use Tier 2

Tier 2 skills are for knowledge that is:
- Highly specialized (e.g., a specific infrastructure provider, payment system, legal framework)
- Not needed for every technical task
- Too verbose to load in the general architecture context

## Examples

| File | Purpose |
|---|---|
| `20-infra-provider.md` | Cloud provider specifics (AWS, GCP, Scaleway...) |
| `21-payments.md` | Stripe, Mollie, or other payment integrations |
| `22-legal-compliance.md` | GDPR, CCPA, local regulations |
| `23-ml-patterns.md` | Machine learning patterns and evaluation |

## Loading Rule

Referenced in `orchestrator.md` Task Detection Table. Only load when the signal matches.
