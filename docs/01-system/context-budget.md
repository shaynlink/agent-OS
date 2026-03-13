# Context Budget — Gestion du budget contextuel

> Le budget contextuel n'est pas une contrainte technique à contourner — c'est un signal de conception. Savoir combien de contexte charger est aussi critique que savoir quoi charger.

---

## Le paradoxe du contexte

L'intuition est de charger le plus possible — "plus de contexte = meilleure réponse". Les recherches empiriques invalident cette intuition dans les deux sens :

**Contexte insuffisant** : le modèle comble les lacunes avec des inférences génériques issues du pré-entraînement. Ces inférences sont souvent raisonnables, mais rarement alignées avec les conventions spécifiques du projet.

**Contexte excessif** : Liu et al. (2023) démontrent une **courbe de performance en U**. Au-delà d'un seuil, chaque token de contexte supplémentaire dilue l'attention du modèle sur les informations critiques. Un contexte de 200k tokens où l'information clé est au milieu est moins performant qu'un contexte de 20k tokens où l'information clé est au début.

**La conclusion opérationnelle** : charger moins, mais mieux ciblé.

---

## Niveaux de dégradation

```
┌──────────────────┬───────────────────────────────────────────────────────┐
│ Contexte dispo   │ Stratégie de chargement                               │
├──────────────────┼───────────────────────────────────────────────────────┤
│ 200k+ tokens     │ Tier 0+1+2 + rôles + sections mémoire complètes       │
├──────────────────┼───────────────────────────────────────────────────────┤
│ 100–200k         │ Tier 0+1 + rôles pertinents + sections ciblées        │
├──────────────────┼───────────────────────────────────────────────────────┤
│ 32–100k          │ Tier 0 + alignment + rôle principal + headers Tier 1  │
├──────────────────┼───────────────────────────────────────────────────────┤
│ < 32k            │ Tier 0 + alignment ONLY                                │
│                  │ → Refuser les refactors complexes, multi-fichiers      │
│                  │ → Maximum de prudence, minimum d'action                │
└──────────────────┴───────────────────────────────────────────────────────┘
```

**Règle invariante** : MOINS de contexte = PLUS de prudence. Jamais moins.

---

## Pourquoi la dégradation est "gracieuse"

La clé est que le **kernel est toujours chargé**. Même à < 32k tokens, les garanties fondamentales restent actives :
- Le protocole de raisonnement structuré est en place
- Les contraintes constitutionnelles sont actives
- Le comportement sécurisé est garanti

Ce qui disparaît en contexte contraint, c'est la spécialisation projet — les standards techniques, les cartes de connaissance, les patterns spécifiques. L'agent opère de manière plus générique, mais pas de manière non-sécurisée.

C'est une dégradation de **qualité**, pas de **sécurité**.

---

## Chargement sectionnel — le mécanisme principal

Le format retrieval-aware de `memory/semantic/` (INDEX + `<section id="">`) permet de charger **une section** d'une carte de connaissance plutôt que le fichier entier.

```
Signal "modifier le PaymentService"
→ Charger section #payment-service de semantic/services.md (≈ 500 tokens)
→ PAS le fichier semantic/services.md entier (≈ 8000 tokens)
Économie : 94% du budget contextuel préservé
```

La précision des signaux dans la Task Detection Table détermine directement l'efficacité de ce mécanisme.

---

## Indicateurs de mauvaise gestion du budget

| Signal | Problème probable |
|---|---|
| Les réponses deviennent génériques en fin de session | Context window saturée — les skills récemment chargés écrasent les fichiers kernel |
| L'agent ignore des règles pourtant chargées | Ces règles sont au milieu d'un contexte trop long — biais du milieu (Liu 2023) |
| Performances dégradées sur des sessions longues | Accumulation non-gérée dans `memory/working/` |
| L'agent "oublie" des conventions en milieu de tâche | Tier 1 Skills chargés mais noyés dans un contexte plus volumineux |

---

## Règle de conception des fichiers

Chaque fichier du système doit être conçu avec son **coût en budget contextuel** comme contrainte de premier ordre :

- Skills Tier 1 : ≤ 500 tokens chacun (règles denses, pas de prose)
- Sections semantic/ : ≤ 1000 tokens par section
- Roles : ≤ 300 tokens (perspective, standards, critères — dense)
- Tier 2 Skills : pas de limite stricte (chargés on-demand, rarement)

Un skill Tier 1 de 3000 tokens est un problème architectural, pas juste une question de style.


---

**Navigation** — [← Session Flow](./session-flow.md) · [↑ Index](../README.md) · [02 — Kernel →](../02-kernel/README.md)
