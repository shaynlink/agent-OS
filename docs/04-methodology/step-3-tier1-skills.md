# Étape 3 — Créer les Tier 1 Skills

> **Objectif** : Encoder les standards techniques non-négociables du projet sous forme de contrats vérifiables que l'agent applique à chaque changement de code.

---

## Règle de décision — Tier 1 vs Tier 2

**Tier 1** : skill chargé à chaque tâche technique. Critère : s'applique à ≥ 60% des requêtes.  
**Tier 2** : skill chargé on-demand. Critère : s'applique à < 30% des requêtes.

Si un skill couvre "comment utiliser Stripe", il ne s'applique pas à une tâche de refactoring de la couche auth → Tier 2. Si un skill couvre "les types TypeScript sont stricts", il s'applique à toute tâche qui touche du code → Tier 1.

---

## Format des Tier 1 Skills

```markdown
# Skill NN — [Nom]

> [Une phrase : ce que ce skill impose et garantit]

## [Section domaine]
- [Règle concrète et vérifiable]
- [Règle concrète et vérifiable]

## [Section 2]
- [...]

<!-- Source: [référence — paper, RFC, doc officielle] -->
<!-- Updated: YYYY-MM-DD -->
```

**Contrat de contenu** : chaque règle doit être **évaluable contre un fichier réel**. Question test : "Comment l'agent sait-il que ce code respecte cette règle ?" Si la réponse est vague, la règle est incomplète.

---

## Skills recommandés par stack

| Domaine | Nom suggéré | Ce qu'il couvre |
|---|---|---|
| Langage | `01-typescript.md` | strict, any interdit, Zod à la frontière |
| Architecture | `02-clean-architecture.md` | séparation des couches, dépendances |
| Sécurité | `03-security.md` | auth, OWASP, secrets, validation |
| Base de données | `04-database.md` | transactions, N+1, migrations |
| API | `05-api-standards.md` | REST, codes erreur, versioning |
| Tests | `06-testing.md` | pyramide, coverage, fixtures |

Commencer par **3 skills** couvrant langage, sécurité, et le domaine le plus risqué du projet. Ajouter les autres au fil des sessions.

---

## ✅ Bon skill vs ❌ Mauvais skill

✅ **Spécifique, vérifiable, actionnable** :
```markdown
# Skill 03 — Security

## Authentification
- Toute route privée doit vérifier le token via `authMiddleware` avant d'atteindre le handler
- Les tokens JWT ne sont jamais loggués en entier — tronquer à 20 chars maximum
- Toute opération sensible (changement de mot de passe, suppression de compte) requiert
  une re-authentification, même si l'utilisateur a un token valide

## Validation
- Zod obligatoire sur tous les body, params et query strings de routes API
- Schéma déclaré séparément du handler, nommé `[ResourceName]Schema`
```

❌ **Vague, non-actionnable** :
```markdown
# Skill 03 — Security

## Règles
- Sécuriser les endpoints
- Valider les inputs utilisateur
- Ne pas exposer de secrets
```

---

## ✅ Checkpoint

- [ ] 3–6 fichiers créés dans `.agent/rules/tier-1/`
- [ ] Chaque skill contient au moins un critère évaluable par fichier
- [ ] Chaque skill a un `<!-- Source: -->` et `<!-- Updated: -->`
- [ ] Aucun skill Tier 1 ne dépasse 500 tokens (≈ 375 mots)

---

**Navigation** — [← Étape 2 — AGENTS.md](./step-2-agents-md.md) · [↑ Index](../README.md) · [Étape 4 — memory/semantic/ →](./step-4-memory-semantic.md)
