# 03 — Layers Adaptables

> Les layers adaptables sont la surface d'extension du système. Chaque layer a un contrat précis : quoi y mettre, dans quel format, selon quelle règle de mise à jour.

---

## Principe

Les layers adaptables capturent ce qui est spécifique à un projet : ses conventions techniques, son architecture, ses leçons apprises, ses patterns éprouvés, ses personas d'expertise. Sans eux, l'agent opère avec le kernel seul — fonctionnel mais générique. Avec eux, il devient un expert du projet.

La qualité des layers adaptables détermine **directement** la qualité de l'agent sur le projet. Un kernel bien configuré avec des layers pauvres produit un agent correct mais générique. Des layers riches et précis produisent un agent qui connaît le projet au niveau d'un développeur senior qui a travaillé dessus depuis le début.

---

## Les 7 layers

| Layer | Page | Fréquence de mise à jour |
|---|---|---|
| Tier 1 Skills | [tier-1-skills.md](./tier-1-skills.md) | Rare — à la création + sur changement de convention majeure |
| Tier 2 Skills | [tier-2-skills.md](./tier-2-skills.md) | Rare — ajout on-demand selon les besoins |
| Roles | [roles.md](./roles.md) | Rare — stable une fois les rôles définis |
| memory/semantic/ | [memory-semantic.md](./memory-semantic.md) | À chaque nouveau module / changement d'architecture |
| memory/episodic/ | [memory-episodic.md](./memory-episodic.md) | À chaque bug significatif résolu |
| memory/procedural/ | [memory-procedural.md](./memory-procedural.md) | Quand un pattern atteint confidence > 0.8 |
| memory/working/ | [memory-working.md](./memory-working.md) | Session-scoped — volatile |

---

## Règle d'extension générale

Avant d'ajouter quelque chose dans les layers adaptables, répondre à ces 3 questions :

1. **Est-ce projet-spécifique ?** Si c'est universel → probablement dans le kernel, pas ici.
2. **Est-ce la bonne couche ?** Standard technique → Tier 1. Expertise domaine → Tier 2 ou Role. Connaissance → memory/.
3. **Est-ce vérifiable ?** Chaque entrée doit pouvoir être évaluée contre le codebase réel.

---

## Convention de nommage

| Layer | Format | Exemple |
|---|---|---|
| Tier 1 | `NN-kebab-name.md` | `03-security.md`, `05-api-standards.md` |
| Tier 2 | `kebab-name.md` | `stripe-integration.md`, `redis-caching.md` |
| Roles | `role-name.md` | `backend-engineer.md`, `security-reviewer.md` |
| memory/semantic/ | `domain.md` | `auth-architecture.md`, `data-models.md` |
| memory/episodic/ | `known-pitfalls.md` ou `rca-[date].md` | `rca-2026-03-13.md` |
| memory/procedural/ | `pattern-name.md` | `optimistic-locking.md` |


---

**Navigation** — [← Agent Behavior](../02-kernel/11-agent-behavior.md) · [↑ Index](../README.md) · [Tier 1 Skills →](./tier-1-skills.md)
