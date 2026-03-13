# Tier 1 Skills — Technical Core

> Les Tier 1 Skills encodent les standards techniques non-négociables du projet. Ce ne sont pas des guidelines — ce sont des contrats de qualité que l'agent applique à chaque changement de code.

---

## Contrat

**Quand créer un Tier 1 Skill** : quand un standard technique s'applique à ≥ 60% des tâches techniques du projet.

**Format** :
```markdown
# Skill NN — [Nom]

> [Une phrase : ce que ce skill impose et garantit]

## [Section 1]
[Règles concrètes, vérifiables, pas de principes vagues]

## [Section 2]
[...]

<!-- Source: [référence — paper, RFC, documentation officielle] -->
<!-- Updated: YYYY-MM-DD -->
```

**Contrat de contenu** : chaque skill doit contenir au moins un critère de validation objectif. La question test : *"Comment l'agent sait-il que ce code respecte ce skill ?"* Si la réponse est vague, le skill est incomplet.

**Limite** : 3–6 skills en Tier 1 maximum. Au-delà, fragmenter en Tier 2.

**Budget contextuel** : ≤ 500 tokens par skill. Un Tier 1 chargé systématiquement ne peut pas être verbeux.

---

## ✅ Bon Tier 1 Skill

```markdown
# Skill 03 — TypeScript Strict

> Tous les fichiers .ts du projet opèrent en strict mode. Aucune exception non documentée.

## Types
- `any` interdit sans commentaire justificatif marqué `// justified: [raison]`
- `@ts-ignore` interdit — corriger le type, pas le diagnostic
- `@ts-nocheck` interdit dans tous les cas
- Toutes les fonctions async ont un type de retour explicite : `Promise<T>`
- Pas d'assertions `as Type` sans `instanceof` guard en amont

## Validation
- Zod obligatoire à toutes les frontières d'entrée : routes API, form handlers, event processors
- Les schémas Zod sont co-localisés avec les routes qui les consomment
- Pas de validation inline (règles dispersées dans le handler) — schéma déclaré séparément

## Erreurs
- Pas de `catch {}` vide — chaque catch loggue ou propage
- Les erreurs Zod sont converties en 400 Bad Request avec détail des champs invalides
- Les erreurs non-Zod non attendues sont converties en 500 avec log structuré

<!-- Source: TypeScript Handbook — https://www.typescriptlang.org/tsconfig#strict -->
<!-- Updated: 2026-03-13 -->
```

**Pourquoi c'est bon** : chaque règle est vérifiable. L'agent peut scanner un fichier et déterminer s'il respecte le skill ou non.

---

## ❌ Mauvais Tier 1 Skill

```markdown
# Skill 03 — Code Quality

> Écrire du bon code propre et maintenable.

## Règles
- Garder le code lisible
- Utiliser des noms de variables descriptifs
- Éviter la duplication
- Respecter les bonnes pratiques TypeScript
```

**Pourquoi c'est mauvais** : aucun critère n'est vérifiable. "Lisible", "descriptif", "bonnes pratiques" ne peuvent pas être évalués contre un fichier réel. Ce skill ne contraint pas le comportement — il le laisse indéterminé.

---

## ⚠️ Pièges courants

**Piège 1 — Mettre en Tier 1 ce qui appartient en Tier 2**

Un skill d'intégration Stripe n'est pertinent que pour les tâches liées aux paiements — pas pour une tâche de refactoring de la couche auth. Le placer en Tier 1 le charge systématiquement, gaspillant du budget contextuel sur des sessions où il n'ajoute rien.

**Piège 2 — Skills trop larges**

Un Tier 1 "Backend Standards" qui couvre TypeScript, sécurité, API, database et tests en un seul fichier dépasse rapidement 2000 tokens. Fragmenter en skills distincts : `01-typescript.md`, `03-security.md`, `05-api.md`, `06-testing.md`.

**Piège 3 — Règles sans source**

Un skill sans `<!-- Source: -->` est une règle dont l'origine est inconnue. Au fil du temps, personne ne se souvient pourquoi la règle existe, ce qui rend difficile de décider si elle doit changer. **Toute règle dans un Tier 1 Skill doit avoir une source traçable**.

**Piège 4 — Conflits entre skills**

Si `04-database.md` dit "utiliser des transactions pour toute opération multi-step" et `05-api.md` dit "garder les handlers légers — déléguer à la couche service", ces deux règles ne se contredisent pas mais peuvent entrer en tension. Les conflits potentiels doivent être documentés dans les skills concernés.

---

## Exemples de skills Tier 1 par domaine

| Domaine | Fichier | Ce qu'il couvre |
|---|---|---|
| Langage | `01-typescript.md` | Strict mode, types, pas d'`any`, Zod |
| Architecture | `02-clean-architecture.md` | Séparation des couches, dépendances dirigées |
| Sécurité | `03-security.md` | Authentification, autorisation, secrets, OWASP |
| Base de données | `04-database.md` | Transactions, indexes, N+1, migrations |
| API | `05-api-standards.md` | REST conventions, codes d'erreur, versioning |
| Tests | `06-testing.md` | Pyramide de tests, coverage minimal, fixtures |


---

**Navigation** — [← 03 — Layers Adaptables](./README.md) · [↑ Index](../README.md) · [Tier 2 Skills →](./tier-2-skills.md)
