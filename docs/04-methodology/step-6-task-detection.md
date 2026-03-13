# Étape 6 — Enrichir la Task Detection Table

> **Objectif** : Rendre l'orchestrateur efficace sur votre projet spécifique. Sans ce mapping, l'orchestrateur doit inférer quelles ressources charger depuis le texte de la requête — résultat : chargement erroné ou chargement en bloc.

---

## Ce que la Task Detection Table fait

La table mappe des **signaux lexicaux** (mots-clés dans la requête) vers des **ressources à charger** (skills, context maps, roles). C'est le moteur de routage de l'orchestrateur.

**Sans la table** : l'orchestrateur charge les Tier 1 Skills génériques pour toutes les requêtes techniques. Il ne sait pas qu'une requête sur "modifier PaymentService" devrait aussi charger `semantic/services.md#payment-service`.

**Avec la table** : chaque requête déclenche le chargement précis des ressources nécessaires et uniquement celles-ci.

---

## Format

Dans `AGENTS.md`, dans la section Task Detection Table :

```markdown
| Signal keywords | Domain | Tier 1 Skills | Tier 2 Skills | Context Map |
|---|---|---|---|---|
| schema, model, migration, prisma | Database | `04-database` | — | `semantic/data-models.md` |
| endpoint, route, controller, handler, api | API | `05-api`, `03-security` | — | `semantic/api-modules.md` |
| auth, jwt, session, passkey, oauth, login | Auth | `03-security` | `auth-flows` | `semantic/auth-architecture.md` |
| payment, stripe, charge, refund, webhook | Payment | `03-security` | `stripe-integration` | `semantic/services.md#payment-service` |
| bug, error, exception, crash, failing | Debug | — | `17-problem-resolution` | `memory/episodic/known-pitfalls.md` |
| test, spec, coverage, jest, vitest | Testing | `06-testing` | — | — |
| deploy, ci, cd, pipeline, dockerfile | Infra | — | `infra-deploy` | `semantic/infra-architecture.md` |
```

---

## Règles de conception

**Signal keywords** :
- Préférer les mots-clés du domaine projet plutôt que les mots-clés génériques
- Inclure les synonymes courants (ex: "endpoint, route, controller, handler")
- Éviter les mots trop génériques ("code", "function", "file") qui matchent tout

**Tier 1 Skills** :
- Lister uniquement les skills Tier 1 *supplémentaires* à ceux déjà chargés systématiquement
- Ex: si `01-typescript` est toujours chargé, ne pas le répéter dans chaque entrée

**Context Map** :
- Utiliser le format `semantic/fichier.md#section-id` pour le chargement sectionnel
- Laisser `—` si aucune carte n'est pertinente (pas de section non documentée)

---

## ✅ Contrat vs Convention

| | Contrat (obligatoire) | Convention (recommandé) |
|---|---|---|
| Entrées | ≥ 1 Tier 1 Skill OU ≥ 1 Context Map par entrée | 5–10 entrées couvrant les domaines habituels |
| Keywords | Mots du domaine projet, pas génériques | Inclure synonymes via virgules |
| Sections | Utiliser `#section-id` si la section existe | Laisser le fichier entier si incertitude |

---

## ✅ Checkpoint

- [ ] 5–10 entrées dans la Task Detection Table
- [ ] Chaque domaine de tâches habituel du projet a au moins une entrée
- [ ] Chaque entrée a au moins un Skill Tier 1 ou une Context Map
- [ ] Test de routage : pour une requête type "ajouter un endpoint de paiement", tracer manuellement le routage exact — les ressources chargées doivent être précises et pertinentes

---

**Navigation** — [← Étape 5 — Roles](./step-5-roles.md) · [↑ Index](../README.md) · [05 — Philosophie →](../05-philosophy/README.md)
