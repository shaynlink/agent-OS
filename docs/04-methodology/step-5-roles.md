# Étape 5 — Définir les Roles

> **Objectif** : Activer le bon prisme d'expertise selon la tâche. Un rôle n'est pas un profil de poste — c'est une **perspective** qui oriente la lecture d'un problème et les critères de décision dans les trade-offs.

---

## Ce qu'un rôle fait (et ne fait pas)

**Un rôle fait** :
- Orienter la perspective ("tout input est potentiellement hostile")
- Définir des critères de priorisation des trade-offs (Security > Performance)
- Apporter des standards domaine-spécifiques que les Tier 1 Skills ne couvrent pas

**Un rôle ne fait pas** :
- Redéfinir les contraintes constitutionnelles d'`alignment.md`
- Ajouter des règles qui s'appliquent universellement (→ Tier 1 Skill)
- Exprimer un niveau hiérarchique (rôle `senior-developer` = sans valeur)

---

## Format

```markdown
# Role — [Nom de l'expertise]

> [Une phrase : quel prisme ce rôle active]

## Perspective
[Comment ce rôle cadre un problème — ce qu'il voit en premier]

## Standards
[Règles et priorités propres à ce domaine]

## Decision Criteria
[Ordre de priorité explicite pour les trade-offs]
Security > Architecture > Performance > DX

<!-- Updated: YYYY-MM-DD -->
```

**Contrat** : un rôle sans "Decision Criteria" est incomplet. Sans critères de priorisation, le rôle n'oriente pas les décisions difficiles — il se contente de décrire un domaine.

---

## ✅ Bon rôle vs ❌ Mauvais rôle

✅ **Apporte un prisme distinct** :
```markdown
# Role — Security Reviewer

> Évalue chaque décision depuis le prisme : est-ce exploitable ?

## Perspective
Tout input utilisateur est hostile jusqu'à preuve du contraire.
La surface d'attaque minimale est une contrainte de design, pas une optimisation.

## Standards
- Vérifier la validation à la frontière avant d'analyser la logique métier
- Principe du moindre privilège : accorder les permissions minimales nécessaires
- OWASP Top 10 comme checklist de review systématique

## Decision Criteria
Security > Architecture > Performance > DX
```

❌ **Trop générique, pas de prisme** :
```markdown
# Role — Full Stack Developer

> Développe des features end-to-end.

## Standards
- Écrire du bon code
- Faire fonctionner le frontend et le backend
```

---

## Rôles recommandés par type de projet

| Projet | Rôles utiles |
|---|---|
| API / Backend | `backend-engineer`, `security-reviewer`, `data-architect` |
| Frontend | `frontend-engineer`, `ux-engineer`, `accessibility-reviewer` |
| Fullstack | Les deux catégories ci-dessus |
| Data | `data-engineer`, `ml-engineer`, `analytics-engineer` |

Limiter à **6–8 rôles maximum**. Au-delà, les périmètres se chevauchent et le Role Deliberation Protocol génère des tensions artificielles.

---

## ✅ Checkpoint

- [ ] Au moins 2 rôles couvrant les domaines d'expertise clés du projet
- [ ] Chaque rôle a une section "Decision Criteria" avec ordre de priorité explicite
- [ ] Test : pour chaque paire de rôles, citer 2 décisions qu'ils prendraient différemment sur le même problème

---

**Navigation** — [← Étape 4 — memory/semantic/](./step-4-memory-semantic.md) · [↑ Index](../README.md) · [Étape 6 — Task Detection →](./step-6-task-detection.md)
