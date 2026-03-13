# Context Layers — Kernel et Layers Adaptables

> La séparation kernel/adaptable n'est pas une organisation de fichiers — c'est un contrat de stabilité. Elle définit ce qui peut changer et ce qui ne peut pas.

---

## Pourquoi deux couches aux propriétés opposées

Un système contextuel qui peut être entièrement modifié perd sa valeur principale : la **prévisibilité**. Si `alignment.md` peut être édité, ses garanties ne sont plus des garanties — ce sont des suggestions. Si `00-reasoning.md` peut être remplacé, il n'y a plus de premier token garanti.

À l'inverse, un système entièrement fixe est inutilisable sur un projet réel — il ne peut pas capturer les conventions spécifiques, l'architecture propre, les patterns établis.

La solution est une **séparation stricte entre ce qui garantit et ce qui capte** :

```
┌─────────────────────────────────────────────────────────────────┐
│  KERNEL                                                         │
│  Garantit : cohérence comportementale, sécurité, raisonnement  │
│  Propriété : IMMUTABLE — même fichier pour tout projet          │
├─────────────────────────────────────────────────────────────────┤
│  LAYERS ADAPTABLES                                              │
│  Capte : conventions, architecture, expertise, leçons           │
│  Propriété : ÉVOLUTIF — spécifique à chaque projet             │
└─────────────────────────────────────────────────────────────────┘
```

---

## Le Kernel — 5 fichiers, 5 responsabilités distinctes

| Fichier | Responsabilité | Ce qu'il garantit |
|---|---|---|
| `rules/tier-0/00-reasoning.md` | Protocole de raisonnement | Chaque réponse expose son processus décisionnel |
| `system/alignment.md` | Contraintes constitutionnelles | Certaines actions ne seront jamais exécutées sans confirmation |
| `system/orchestrator.md` | Routage contextuel | Le contexte est chargé sélectivement, pas en bloc |
| `system/auto-learning.md` | Apprentissage inter-session | Les événements significatifs alimentent la mémoire |
| `rules/tier-0/11-agent-behavior.md` | Comportement et communication | Le format de réponse est structuré et auditable |

Ces 5 fichiers sont **fonctionnellement indépendants** — chacun couvre une dimension distincte du comportement agent. Ensemble, ils forment un système complet de garanties comportementales qui s'applique **quel que soit le contenu des layers adaptables**.

---

## Les Layers Adaptables — Anatomie

### Tier 1 — Technical Core Skills

**Nature** : Standards techniques non-négociables du projet, applicables à ≥ 60% des tâches.

**Pourquoi dans les layers adaptables et non dans le kernel** : Les standards techniques sont intrinsèquement projet-spécifiques. Un projet TypeScript strict a des contraintes différentes d'un projet Python ou Go. Un projet avec PostgreSQL a des patterns différents d'un projet MongoDB. Encoder ces standards dans le kernel les rendrait inapplicables à d'autres projets.

**Contrat** : tout code produit sous un Tier 1 Skill doit respecter ses standards sans exception documentée.

### Tier 2 — Domain-Specific Skills

**Nature** : Skills spécialisés, pertinents pour < 30% des tâches.

**Pourquoi on-demand** : Charger systématiquement tous les Tier 2 dans chaque session consomme du budget contextuel pour des informations souvent non pertinentes, et dilue le signal des informations critiques. Jiang et al. (2023) démontrent que le contexte superflu dégrade les performances autant que le contexte insuffisant.

### Roles — Personas d'expertise

**Nature** : Prisme d'expertise activé selon le type de tâche.

**Ce qu'un Role fait** : Il ne redéfinit pas les contraintes (alignment > role dans la hiérarchie). Il oriente la **perspective** — comment le problème est cadré, quels trade-offs sont prioritaires, quelles questions sont posées en premier.

**Ce qu'un Role ne fait pas** : Override alignment. Un rôle "Move Fast" ne peut pas autoriser des actions irréversibles sans confirmation.

### memory/ — Les 4 couches de mémoire

| Couche | Portée | Volatilité | Rôle |
|---|---|---|---|
| `working/` | Session courante | Volatile — effacée à chaque session | État temporaire, tâche en cours |
| `episodic/` | Inter-session | Persistante, append-only | Bugs, erreurs, RCAs, leçons |
| `semantic/` | Inter-session | Persistante, mise à jour sur changements | Architecture, schemas, API, modules |
| `procedural/` | Inter-session | Persistante, enrichie par succès | Patterns éprouvés, recettes |

---

## Règle d'extension

Quand un besoin spécifique émerge, la question à se poser est :

> **Ce besoin doit-il être garanti pour tout projet (→ kernel) ou capté pour ce projet spécifique (→ layers adaptables) ?**

Si la réponse est "pour ce projet", la couche adaptable appropriée est :

```
Standard technique universel  → Tier 1
Expertise domaine spécifique  → Tier 2
Perspective / priorisation    → Role
Connaissance architecture     → memory/semantic/
Leçon apprise                 → memory/episodic/
Pattern réussi                → memory/procedural/
```

**Ce qui n'appartient à aucune de ces catégories ne devrait probablement pas être dans le système.**

---

## Anti-pattern : le kernel étendu

Le piège le plus courant : modifier `alignment.md` ou `orchestrator.md` pour y ajouter des contraintes ou des signaux projet-spécifiques.

```
❌ Modifier alignment.md pour y ajouter "Ne jamais utiliser class components React"
✅ Créer .agent/rules/tier-1/01-react-standards.md avec cette règle
```

```
❌ Ajouter des entrées projet-spécifiques dans orchestrator.md
✅ Remplir la Task Detection Table dans AGENTS.md
```

Le kernel modifié n'est plus universel — il perd son principal avantage. Et il ne peut plus être mis à jour depuis l'upstream sans conflits.


---

**Navigation** — [← Boot Sequence](./boot-sequence.md) · [↑ Index](../README.md) · [Session Flow →](./session-flow.md)
