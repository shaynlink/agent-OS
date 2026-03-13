# memory/episodic/ — Leçons apprises et RCAs

> La mémoire épisodique capture les événements significatifs du passé pour éviter qu'ils se reproduisent. C'est la réponse au problème #6 : un agent sans capitalisation répète les mêmes erreurs indéfiniment.

---

## Nature de la mémoire épisodique

La mémoire épisodique est directionnelle : elle documente les **erreurs, leurs causes racines, et les règles qui permettent de les éviter**. Ce n'est pas un journal de session — c'est une base de données de contre-exemples opérationnels.

Source : Shinn et al. (2023) — *Reflexion*. Un agent qui génère des réflexions verbales sur ses erreurs et les stocke accessiblement améliore ses performances futures sur les mêmes classes de problèmes sans fine-tuning.

---

## Fichier principal : `known-pitfalls.md`

Format d'une entrée :

```markdown
---
**Episode**: [date] — [tâche] — [contexte projet]
**What happened**: [description factuelle — pas d'interprétation, pas de jugement]
**Root cause** (5 Whys):
  1. Pourquoi X s'est produit ? → Parce que Y
  2. Pourquoi Y ? → Parce que Z
  3. Pourquoi Z ? → ...
  4. Pourquoi ... ? → ...
  5. Cause racine : [la vraie cause, pas le symptôme]
**Verbal reflection**: "La prochaine fois je dois..."
**Extracted rule**: [règle positive, actionnelle, formulée pour l'avenir]
**Confidence**: [0.0–1.0]
**Tags**: [#database, #migration, #locking...]
---
```

---

## ✅ Exemple d'entrée correcte

```markdown
---
**Episode**: 2026-03-13 — Migration PostgreSQL — ajout colonne sur table orders
**What happened**: La migration a échoué en production avec un lock timeout sur la table
  `orders` (50M rows). Elle avait fonctionné sans erreur en développement.
**Root cause** (5 Whys):
  1. Pourquoi le timeout ? → `ALTER TABLE ADD COLUMN` acquiert un lock exclusif
  2. Pourquoi le lock ? → PostgreSQL verrouille la table pour réécrire les métadonnées
  3. Pourquoi non détecté en dev ? → La table de dev contenait 0 rows
  4. Pourquoi 0 rows en dev ? → Aucun seed de données représentatif du volume production
  5. Cause racine : absence de convention de test de migration sur données volumineuses
**Verbal reflection**: "La prochaine fois je dois tester les migrations sur un dump
  production ou utiliser des patterns non-locking (ADD COLUMN ... DEFAULT NULL)."
**Extracted rule**: Toute migration ALTER TABLE sur une table > 1M rows doit être testée
  sur un dump production ou utiliser des opérations non-locking.
**Confidence**: 0.9
**Tags**: #database #migration #postgresql #locking #production
---
```

---

## ❌ Entrée de mauvaise qualité

```markdown
Bug #47 — Migration a planté. Problème de lock. À ne plus faire.
```

**Pourquoi c'est mauvais** : pas de cause racine, pas de règle extraite, pas de contexte. Dans 6 mois, personne — humain ou agent — ne peut exploiter cette entrée pour éviter le même problème.

---

## Règles de gestion

**Append-only** : ne jamais supprimer une entrée, même si le bug est corrigé. Un bug corrigé dans le code peut réapparaître dans un autre contexte — l'entrée reste pertinente.

**Tags obligatoires** : permettent à l'orchestrateur de charger uniquement la section pertinente (`#database`, `#auth`, `#payment`) plutôt que le fichier entier.

**Seuil de création** : not every bug warrants an entry. Créer une entrée pour les bugs qui :
- Ont surpris (symptôme et cause non évidents)
- Ont causé un impact mesurable (temps de résolution > 1h, impact production)
- Peuvent se reproduire dans un contexte différent

---

## Gestion du volume (Memory Bloat)

Quand `known-pitfalls.md` dépasse ~200 lignes :
1. Séparer par domaine : `db-pitfalls.md`, `auth-pitfalls.md`, `infra-pitfalls.md`
2. Archiver les entrées > 6 mois sans code associé (déplacer dans `episodic/archive/`)
3. Consolider les entrées similaires — si 3 entrées ont la même cause racine, les fondre en une RULE dans un Tier 1 Skill

---

**Navigation** — [← memory/semantic/](./memory-semantic.md) · [↑ Index](../README.md) · [memory/procedural/ →](./memory-procedural.md)
