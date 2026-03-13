# memory/procedural/ — Patterns éprouvés

> La mémoire procédurale capitalise les **raisonnements réussis** — pas juste les résultats, mais les processus de décision qui ont mené à ces résultats. C'est la réponse au problème de l'agent qui ne tire pas de leçons de ses succès.

---

## Nature de la mémoire procédurale

Un pattern qui a fonctionné une fois est une observation. Un pattern qui a fonctionné trois fois avec des résultats mesurables est une règle. La mémoire procédurale formalise cette progression et rend les règles accessibles aux sessions futures.

Source : Zelikman et al. (2022) — *STaR* (Self-Taught Reasoner). Un modèle qui génère des rationalisations de ses propres réponses correctes, puis s'entraîne sur ces rationalisations, améliore ses performances sur des classes de problèmes similaires. La mémoire procédurale implémente la version textuelle de ce principe.

---

## Différence avec memory/episodic/

| | episodic/ | procedural/ |
|---|---|---|
| Direction | Erreurs → règles d'évitement | Succès → patterns réutilisables |
| Déclencheur | Un bug, un échec, un impact production | Un pattern complexe réussi de manière remarquable |
| Format | Factuel et négatif ("ne jamais faire X") | Prescriptif et positif ("pour Y faire Z") |
| Transition | Rester en episodic/ | Migrer vers Tier 2 Skill si confidence > 0.9 sur 3+ instances |

---

## Format d'entrée

```markdown
---
**Pattern**: [Nom descriptif du pattern]
**Context**: [Conditions d'application — quand ce pattern est pertinent]
**Problem**: [Quel problème ce pattern résout]
**Solution** (raisonnement complet):
  Étape 1 : [...]
  Étape 2 : [...]
  Étape 3 : [...]
**Evidence**: [Résultat concret obtenu — mesurable si possible]
**Confidence**: [0.0–1.0]
**Observations**: [Nombre de fois où ce pattern a été appliqué avec succès]
**Tags**: [#database, #caching, #api...]
---
```

**La section Solution** doit contenir le **raisonnement**, pas juste le résultat. Si la solution est "utiliser un index composite", expliquer *pourquoi* un index composite et pas un index simple, dans ce contexte précis.

---

## ✅ Exemple d'entrée correcte

```markdown
---
**Pattern**: Optimistic Locking pour les mises à jour concurrentes
**Context**: Entité fréquemment mise à jour par plusieurs utilisateurs simultanément,
  sans besoin de verrouillage strict (panier d'achat, compteurs d'audience)
**Problem**: Les mises à jour concurrentes sans coordination peuvent produire
  des Lost Updates — la dernière écriture écrase les écritures précédentes.
**Solution**:
  Étape 1 : Ajouter une colonne `version INTEGER DEFAULT 0` à l'entité
  Étape 2 : Inclure `version` dans le SELECT lors de la lecture
  Étape 3 : Dans l'UPDATE : `WHERE id = $id AND version = $version_lu`
  Étape 4 : Si 0 rows affected → conflit détecté → retry avec backoff exponentiel
  Étape 5 : Incrémenter `version` dans l'UPDATE réussi
  Pourquoi pas un lock pessimiste : le taux de conflit est faible (<5% du temps),
  un lock pessimiste bloquerait toutes les lectures pour une minorité de cas.
**Evidence**: Éliminé 100% des lost updates sur le panier d'achat (3M transactions/jour)
  sans augmentation significative de latence (< 2% de retries).
**Confidence**: 0.9
**Observations**: 3 (OrderService, CartService, InventoryService)
**Tags**: #database #concurrency #postgresql #locking
---
```

---

## Cycle de vie d'un pattern

```
Observation (1 fois)     → NOTE ⚠️  — ne pas intégrer, observer
Pattern (2-3 fois)       → Entrée procedural/ avec confidence 0.6–0.8
Règle établie (3+ fois)  → Confidence > 0.9 → Candidat pour migration en Tier 2 Skill
```

**Migration vers Tier 2 Skill** : quand un pattern atteint confidence > 0.9 sur ≥ 3 observations, le transformer en skill formel dans `.agent/rules/tier-2/`. L'entrée `procedural/` devient une référence vers le skill.

---

**Navigation** — [← memory/episodic/](./memory-episodic.md) · [↑ Index](../README.md) · [memory/working/ →](./memory-working.md)
