# Étape 2 — Remplir AGENTS.md

> **Objectif** : Configurer l'identité du projet. Sans cette étape, l'agent opère sans contexte — il ne sait pas dans quel projet il se trouve, quelle est la stack, qui sont les composants.

---

## Ce que vous remplissez

`AGENTS.md` contient 3 blocs à compléter. Chaque `[PLACEHOLDER]` doit être remplacé.

### Bloc 1 — Identité

```markdown
| Field | Value |
| ----- | ----- |
| **Product** | [Nom de votre produit + tagline courte] |
| **Phase** | [v0 / v1 / beta / production] |
| **Stack** | [TypeScript · Next.js · PostgreSQL · Redis · ...] |
| **Monorepo** | [pnpm / npm / yarn] workspaces |
| **Architecture** | [Monolith / Microservices / Serverless] |
```

**Ce qui compte** : stack précise (versions si pertinentes), phase (conditionne les décisions de prudence), architecture (conditionne les patterns recommandés).

### Bloc 2 — Repository Map

```markdown
apps/
  api/          → API REST Express — port 3001
  web/          → Frontend Next.js — port 3000

packages/
  database/     → Client Prisma + schémas
  auth/         → Module d'authentification partagé
  ui/           → Composants partagés
```

**Ce qui compte** : les rôles de chaque app/package, pas seulement leurs noms. L'agent doit comprendre quelle couche fait quoi sans avoir à inférer depuis les fichiers.

### Bloc 3 — Task Detection Table

La table qui mappe les signaux de requête vers les ressources à charger. Complétée à l'étape 6 — laisser vide pour l'instant ou copier le template.

```markdown
| Signal keywords | Domain | Tier 1 Skills | Tier 2 | Context Map |
|---|---|---|---|---|
| [À remplir à l'étape 6] | | | | |
```

---

## ✅ Checkpoint

- [ ] Aucun `[PLACEHOLDER]` restant dans `AGENTS.md`
- [ ] La stack est précise : framework + ORM + cache + auth si applicable
- [ ] Le Repository Map décrit les rôles, pas seulement la structure de dossiers
- [ ] Test : demander à votre LLM "quel est le stack du projet ?" — la réponse doit être immédiate et précise

---

**Navigation** — [← Étape 1 — Kernel](./step-1-kernel.md) · [↑ Index](../README.md) · [Étape 3 — Tier 1 Skills →](./step-3-tier1-skills.md)
