# 04 — Méthodologie d'Adoption

> Le protocole d'adoption en 6 étapes pour transformer un projet existant avec l'Agent OS. Chaque étape a un objectif précis, un livrable concret, et un checkpoint avant de passer à la suivante.

---

## Principle directeur

L'adoption est **progressive et vérifiable**. Chaque étape produit un résultat observable — pas un "c'est configuré quelque part". Si vous ne pouvez pas répondre au checkpoint d'une étape, ne passez pas à la suivante.

L'ordre est contraint : le kernel doit exister avant les skills, les skills avant les rôles, les rôles avant la Task Detection Table. Une adoption dans le désordre produit un système qui paraît configuré mais se comporte de manière incohérente.

---

## Les 6 étapes

| Étape | Page | Livrable | Durée estimée |
|---|---|---|---|
| **1** — Copier le kernel | [step-1-kernel.md](./step-1-kernel.md) | 5 fichiers kernel présents, non modifiés | 5 min |
| **2** — Remplir AGENTS.md | [step-2-agents-md.md](./step-2-agents-md.md) | Aucun `[PLACEHOLDER]` restant | 30–60 min |
| **3** — Créer les Tier 1 Skills | [step-3-tier1-skills.md](./step-3-tier1-skills.md) | 3–6 fichiers dans `tier-1/` | 2–4h |
| **4** — Peupler memory/semantic/ | [step-4-memory-semantic.md](./step-4-memory-semantic.md) | 2–5 cartes de connaissance | 2–4h |
| **5** — Définir les Roles | [step-5-roles.md](./step-5-roles.md) | Rôles couvrant les domaines clés | 1–2h |
| **6** — Enrichir la Task Detection Table | [step-6-task-detection.md](./step-6-task-detection.md) | 5–10 mappings signal → ressources | 1h |

---

## Indicateur de maturité

```
Niveau 0 — Kernel seul          : comportement générique mais sécurisé
Niveau 1 — + AGENTS.md          : agent conscient du contexte projet
Niveau 2 — + Tier 1 Skills      : agent qui applique les standards
Niveau 3 — + memory/semantic/   : agent qui connaît l'architecture
Niveau 4 — + Roles              : agent avec expertise contextuelle
Niveau 5 — + Task Detection     : orchestration efficace et sélective
```

Chaque niveau est fonctionnel indépendamment. Ne pas viser niveau 5 immédiatement — atteindre niveau 3 solide est souvent plus utile qu'un niveau 5 superficiel.

---

**Navigation** — [← memory/working/](../03-adaptable-layers/memory-working.md) · [↑ Index](../README.md) · [Étape 1 — Kernel →](./step-1-kernel.md)
