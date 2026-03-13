# 05 — Philosophie

> La philosophie du système répond à la question que la technique ne peut pas répondre seule : pourquoi ces choix, plutôt que d'autres, s'appliqueraient dans ce domaine ?

---

## Pages

| Page | Contenu |
|---|---|
| [fundamental-problem.md](./fundamental-problem.md) | Le problème que le système résout, articulé sans simplification |
| [design-tensions.md](./design-tensions.md) | Les 3 tensions architecturales assumées et leur résolution |
| [hierarchy-of-truth.md](./hierarchy-of-truth.md) | Comment les conflits d'instructions sont résolus |
| [limits-and-non-usages.md](./limits-and-non-usages.md) | Ce pour quoi ce système n'est pas conçu |

---

## Position philosophique

L'Agent OS repose sur un postulat central : **la qualité d'un agent est déterminée par la qualité de son environnement informationnel**, pas uniquement par ses capacités intrinsèques.

Ce postulat a des implications directes :

1. Améliorer le modèle sous-jacent est orthogonal à améliorer le système contextuel — les deux contributions sont additives.
2. Un modèle "moins capable" dans un environnement bien structuré surpassera souvent un modèle "plus capable" sans structure.
3. Les problèmes de cohérence, d'amnésie, et d'incohérence comportementale ne sont pas des limitations du modèle — ce sont des symptômes d'un environnement informationnel non structuré.

Cette position distingue l'Agent OS d'une simple collection de prompts. Les prompts améliorent une interaction. Le Context OS améliore l'environnement.


---

**Navigation** — [← Étape 6 — Task Detection](../04-methodology/step-6-task-detection.md) · [↑ Index](../README.md) · [Problème Fondamental →](./fundamental-problem.md)
