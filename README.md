# Agent OS — Boilerplate v2

> Infrastructure contextuelle qui transforme un LLM générique en expert spécialisé, cohérent et apprenante sur un projet donné.

---

## Documentation complète → [docs/](./docs/README.md)

| Section | Contenu |
|---|---|
| [01 — Système](./docs/01-system/README.md) | Architecture, boot sequence, flux de session, budget contextuel |
| [02 — Kernel](./docs/02-kernel/README.md) | Les 5 composants immuables — reasoning, alignment, orchestrator, auto-learning, behavior |
| [03 — Layers Adaptables](./docs/03-adaptable-layers/README.md) | Tier 1/2 Skills, Roles, memory/ — contrats d'extension |
| [04 — Méthodologie](./docs/04-methodology/README.md) | Protocole d'adoption en 6 étapes |
| [05 — Philosophie](./docs/05-philosophy/README.md) | Problème fondamental, tensions assumées, hiérarchie de vérité, limites |
| [06 — Science](./docs/06-science/README.md) | 11 papers traduits en décisions d'architecture concrètes |
| [07 — Benchmarks & Risques](./docs/07-benchmarks/README.md) | Études de performance, modes de défaillance, problèmes et dangers |

---

## Quickstart

```bash
# 1. Copier dans votre projet
cp -r agent-boilerplate/.agent/ votre-projet/.agent/
cp agent-boilerplate/AGENTS.md votre-projet/AGENTS.md

# 2. Remplir AGENTS.md — PROJECT_NAME, stack, repository map, Task Detection Table
# 3. Créer .agent/rules/tier-1/ — standards techniques du projet
# 4. Peupler .agent/memory/semantic/ — cartes de connaissance (format INDEX + sections)
# 5. Définir .agent/roles/ — personas d'expertise
# 6. Enrichir la Task Detection Table — signaux → skills → context maps
```

Protocole détaillé : [docs/04-methodology/README.md](./docs/04-methodology/README.md)

---

## Structure

```
AGENTS.md                          ← Bootloader              [CONFIGURER]
.agent/
  system/
    alignment.md                   ← Contraintes absolues    [KERNEL — NE PAS MODIFIER]
    orchestrator.md                ← Routage contextuel      [KERNEL — NE PAS MODIFIER]
    auto-learning.md               ← Apprentissage           [KERNEL — NE PAS MODIFIER]
  rules/
    tier-0/
      00-reasoning.md              ← Premier token           [KERNEL — NE PAS MODIFIER]
      11-agent-behavior.md         ← Comportement            [KERNEL — NE PAS MODIFIER]
    tier-1/                        ← Skills techniques       [CRÉER]
    tier-2/                        ← Skills domaine          [CRÉER]
  roles/                           ← Rôles d'expertise       [CRÉER]
  memory/
    working/                       ← Session — volatile
    episodic/                      ← Leçons, RCAs
    semantic/                      ← Cartes codebase         [ALIMENTER]
    procedural/                    ← Patterns éprouvés
```

---

## Avant utilisation en production

Lire : [07-benchmarks/problems-and-dangers.md](./docs/07-benchmarks/problems-and-dangers.md)

---

## License

Libre d'usage, d'adaptation et de redistribution. Attribution appréciée, non requise.

