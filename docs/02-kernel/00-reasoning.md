# 00-reasoning — Primacy Bias Gate

> Le premier token n'est pas anodin. Il détermine le cadre dans lequel toute la réponse sera construite.

---

## Pourquoi le raisonnement doit être le premier token

Liu et al. (2023) — *Lost in the Middle* — documentent un phénomène robuste : les LLMs accordent significativement plus de poids aux informations en position primaire (début du contexte) et finale (fin du contexte) qu'aux informations au milieu. Ce biais de position est indépendant de l'importance sémantique de l'information.

En conséquence, le protocole le plus important du système — le cadre de raisonnement — doit occuper la position 1. Si le bloc `<reasoning>` est généré après même une phrase introductive, son influence sur la réponse est réduite.

L'implication opérationnelle : **aucun texte ne précède le bloc `<reasoning>`**. Pas "Bien sûr", pas "Voici ma réponse", pas d'introduction. Le premier caractère de chaque réponse est `<`.

---

## Le protocole en 6 étapes

```xml
<reasoning>
  <!-- Étape 1 — Reformulation -->
  <!-- Reformuler la requête en une phrase. Pas paraphraser — reformuler.
       Identifier ce qui est implicite dans la demande. -->

  <!-- Étape 2 — Hypothèses -->
  <!-- Lister ce qui est assumé. Marquer l'incertain avec ⚠️
       Une hypothèse non marquée est une assertion — traiter différemment. -->

  <!-- Étape 3 — Raisonnement explicite -->
  <!-- Expliquer le POURQUOI des choix : architecture, librairie, pattern.
       Si plusieurs options existent → pourquoi cette option et pas les autres. -->

  <!-- Étape 4 — Plan -->
  <!-- 3–6 étapes numérotées. Vérifier les cas limites avant d'exécuter. -->

  <!-- Étape 5 — Exécution -->
  <!-- Implémenter avec les cas limites en tête. -->

  <!-- Étape 6 — Auto-audit -->
  <!-- 3 bullets : ce qui a été vérifié.
       1 risque identifié — même si la solution est correcte. -->
</reasoning>
```

---

## Ce que ce protocole évite

**Les réponses réflexes** : sans `<reasoning>`, le modèle active le path le plus probable dans son distribution — qui est souvent correct pour des cas simples, mais peut être catastrophique sur des cas où la complexité est cachée (migration de données, refactoring d'interfaces publiques, modification de logique d'authentification).

**La confiance non calibrée** : un modèle sans protocole de raisonnement explicite ne distingue pas entre ce qu'il sait avec certitude et ce qu'il infère. Le bloc `<reasoning>` force cette distinction via les marqueurs `CERTAIN / PROBABLE / INCERTAIN`.

**Les hypothèses implicites** : toute réponse repose sur des hypothèses. Sans l'étape 2, ces hypothèses restent implicites — non vérifiables, non contestables. Les déclarer explicitement permet à l'utilisateur de corriger immédiatement les mauvaises hypothèses avant que l'implémentation ne soit construite dessus.

---

## Contraintes non négociables

| Contrainte | Règle |
|---|---|
| Position | Premier token — toujours |
| Présence | Obligatoire — pas optionnel selon la complexité |
| Contenu | Toutes les 6 étapes — pas de raccourci |
| Décision exposée | Si plusieurs options → choix motivé et alternatives rejetées documentées |
| Vérification | Chaque tâche se termine par une étape de validation |

---

## Ce que Skill 00 ne garantit pas

`00-reasoning.md` garantit la **structure** du raisonnement — pas sa **correction**. Un raisonnement structuré peut mener à une mauvaise conclusion si les hypothèses de base sont incorrectes ou si l'information disponible est insuffisante.

La protection contre les erreurs de raisonnement est assurée par d'autres mécanismes : le Self-Consistency Gate (orchestrator), la vérification Evidence Before Action (alignment), et le scoring de confiance (auto-learning).


---

**Navigation** — [← 02 — Kernel](./README.md) · [↑ Index](../README.md) · [Alignment →](./alignment.md)
