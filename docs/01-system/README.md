# 01 — Système

## Vue d'ensemble

L'Agent OS n'est pas un framework d'exécution — c'est un **protocole de structuration du contexte**. Il ne modifie pas le modèle, ses poids, ni ses capacités intrinsèques. Il modifie systématiquement l'environnement informationnel dans lequel le modèle opère.

Un LLM sans structure contextuelle produit des réponses issues de son pré-entraînement général — valides dans l'absolu, mais décontextualisées par rapport à un projet donné. L'Agent OS résout cet écart non pas en améliorant le modèle, mais en **rendant l'environnement suffisamment contraint et suffisamment riche** pour que le modèle produise des comportements cohérents avec le projet.

Ce n'est pas une configuration. C'est une **ingénierie de l'espace d'entrée**.

---

## Pages

| Page | Description |
|---|---|
| [boot-sequence.md](./boot-sequence.md) | Séquence de boot déterministe — ordre de chargement et justification |
| [context-layers.md](./context-layers.md) | Séparation Kernel / Adaptable — principe et règles |
| [session-flow.md](./session-flow.md) | Flux complet d'une session — du prompt à la réponse |
| [context-budget.md](./context-budget.md) | Gestion du budget contextuel et dégradation gracieuse |

---

## Architecture

```
AGENTS.md                                    ← Point d'entrée unique (bootloader)
.agent/
  system/
    alignment.md                             ← Contraintes constitutionnelles   [KERNEL]
    orchestrator.md                          ← Routage contextuel sélectif      [KERNEL]
    auto-learning.md                         ← Protocole d'apprentissage        [KERNEL]
  rules/
    tier-0/
      00-reasoning.md                        ← Premier token, toujours          [KERNEL]
      11-agent-behavior.md                   ← Comportement et communication    [KERNEL]
    tier-1/                                  ← Standards techniques du projet   [ADAPTABLE]
    tier-2/                                  ← Skills domaine-spécifiques       [ADAPTABLE]
  roles/                                     ← Personas d'expertise             [ADAPTABLE]
  memory/
    working/                                 ← État de session — volatile
    episodic/                                ← Leçons apprises, RCAs
    semantic/                                ← Cartes de connaissance codebase
    procedural/                              ← Patterns éprouvés
```

---

## Principe fondamental : deux couches aux propriétés opposées

```
┌─────────────────────────────────────────────────────┐
│  KERNEL (5 fichiers IMMUTABLES)                     │
│  Garanties comportementales universelles             │
│  Identique pour tout projet, tout modèle             │
├─────────────────────────────────────────────────────┤
│  LAYERS ADAPTABLES                                   │
│  Connaissance et conventions du projet               │
│  Évoluent avec le codebase                           │
└─────────────────────────────────────────────────────┘
```

La valeur du kernel vient de son **invariance**. Tout projet qui utilise ce boilerplate partage le même noyau comportemental — ce qui rend le comportement de l'agent prévisible et auditable, indépendamment de ce qui est chargé dans les layers adaptables.

La valeur des layers adaptables vient de leur **précision**. Plus ils sont remplis avec des informations exactes sur le projet, plus le comportement de l'agent s'aligne sur les contraintes réelles — moins il comble les lacunes avec des inférences génériques.


---

**Navigation** — [← Index docs](../README.md) · [↑ Index](../README.md) · [Boot Sequence →](./boot-sequence.md)
