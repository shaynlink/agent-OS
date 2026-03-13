# Auto-Learning — Verbal RL Loop

> La mémoire d'un système n'est pas ce qu'il sait au départ — c'est ce qu'il apprend en opérant. `auto-learning.md` formalise le protocole par lequel chaque session enrichit la base de connaissance du système.

---

## Le problème de l'agent qui stagne

Un agent sans protocole d'apprentissage répète les mêmes erreurs, réinvente les mêmes solutions, et converge vers les mêmes patterns génériques — indépendamment de l'expérience accumulée. Ce n'est pas une question d'intelligence : c'est une question d'architecture. Sans mécanisme de capture et de consolidation, les leçons apprises en session N sont inaccessibles en session N+1.

Shinn et al. (2023) — *Reflexion* — formalisent ce problème et sa solution : un agent qui génère des **réflexions verbales** sur ses erreurs et succès, et qui stocke ces réflexions accessible aux sessions futures, améliore ses performances sans fine-tuning. L'apprentissage se produit non pas dans les poids du modèle, mais dans la **structure du contexte**.

---

## Le déclencheur — Événements apprenables

| Événement | Action | Destination |
|---|---|---|
| Bug découvert ou résolu | Error Signal → RCA 5 Whys | `memory/episodic/known-pitfalls.md` |
| Pattern complexe réussi | Success Signal → extraction | `memory/procedural/` |
| Nouveau module / service créé | Update knowledge map | `memory/semantic/[domaine].md` |
| Nouveau schéma / modèle ajouté | Update schema section | `memory/semantic/[domaine].md` |
| Nouvelle convention établie | Enrichissement skill | `rules/tier-1/` ou `tier-2/` |
| Vulnérabilité de sécurité trouvée | Anti-pattern ajouté | Skill sécurité Tier 1 |

---

## Verbal RL — Error Signal

**Quand** : une tâche échoue, un bug est trouvé, un test ne passe pas.

```markdown
**Episode**: [date, tâche, contexte]
**What happened**: [description factuelle — pas d'interprétation]
**Root cause**: [5 Whys — aller à la cause racine, pas au symptôme]
**Verbal reflection**: "La prochaine fois, je dois..."
**Extracted rule**: [règle positive et actionnelle]
**Confidence**: [0.0–1.0]
**Destination**: memory/episodic/known-pitfalls.md
```

**Exemple concret** :
```markdown
**Episode**: 2026-03-13, Migration PostgreSQL, contexte: ajout colonne nullable
**What happened**: La migration a échoué en production sur une table avec 50M rows.
                   Elle a fonctionné en dev (table vide).
**Root cause**:
  1. Pourquoi la migration a-t-elle échoué ? → Lock timeout sur table large
  2. Pourquoi le lock ? → L'opération ALTER TABLE acquiert un lock exclusif
  3. Pourquoi non détecté en dev ? → La table dev était vide
  4. Pourquoi la table dev était vide ? → Pas de seed de données représentatif
  5. Pourquoi pas de seed représentatif ? → Aucune convention de test de migration sur données volumineuses
**Verbal reflection**: "La prochaine fois, je dois tester les migrations sur un dump
                       production avant de déployer, ou utiliser ADD COLUMN ... DEFAULT
                       avec une valeur nulle pour éviter le lock."
**Extracted rule**: Toute migration ALTER TABLE sur une table > 1M rows doit être testée
                    sur un dump production ou utiliser des patterns non-locking.
**Confidence**: 0.9
**Destination**: memory/episodic/known-pitfalls.md
```

---

## Verbal RL — Success Signal

**Quand** : une approche complexe fonctionne remarquablement bien — pas une solution triviale, mais une décision non évidente qui a produit un résultat mesurable.

Source : Zelikman et al. (2022) — *STaR* (Self-Taught Reasoner).

```markdown
**Pattern name**: [nom descriptif]
**Conditions**: [quand appliquer ce pattern — contexte précis]
**Steps**: [rationalisation complète — pas juste le résultat]
**Evidence**: [résultat obtenu, mesurable si possible]
**Confidence**: [0.0–1.0]
**Destination**: memory/procedural/ ou rules/tier-X/
```

---

## Scoring de confiance

Avant d'intégrer toute connaissance, un score de confiance est assigné :

```
Score = f(occurrences, sources, vérifiabilité)

Score < 0.6   → NOTE ⚠️   — Marquer "PROVISIONAL — À CONFIRMER"
Score 0.6–0.8 → PATTERN 🟡 — Intégrer avec tag d'avertissement
Score > 0.8   → RULE ✅   — Intégrer comme règle ferme
```

**Règle critique** : une pratique observée **une seule fois** ne devient jamais une RULE directement, même avec une confiance subjective de 1.0. La confiance est une fonction de la répétition, pas de la conviction.

---

## Workflow d'enrichissement

```
① DETECT   → Identifier l'événement apprenable
② VERIFY   → Confirmer contre les fichiers réels (pas d'inférence)
③ CLASSIFY → Erreur → episodic/ | Succès → procedural/ | Structure → semantic/
④ SCORE    → Assigner le score de confiance
⑤ FORMAT   → Écrire au format Verbal RL avec référence source
⑥ UPDATE   → Ajouter au fichier memory/ approprié
⑦ VALIDATE → Vérifier : pas de duplication, pas de contradiction avec l'existant
```

---

## Freshness — Fraîcheur des connaissances

Chaque entrée dans `memory/` doit inclure `<!-- Updated: YYYY-MM-DD -->`.

```
Entrée > 30 jours sans changement de code associé → vérifier la fraîcheur
Chemin de fichier référencé qui n'existe plus → marquer pour mise à jour
Version de librairie changée significativement → vérifier les breaking changes
```

Une base de connaissance contenant des informations obsolètes est pire qu'une base vide : elle induit activement en erreur.

---

## Ce qu'auto-learning ne fait pas

Le protocole n'automatise pas la mise à jour de la mémoire. La décision de capturer une information, de la scorer, et de la persister est une **décision délibérée**, pas un processus automatique. L'automatisation introduit du bruit — des informations capturées mécaniquement sans curation — ce qui dégrade la qualité de la base de connaissance plus vite qu'elle ne l'enrichit.

L'agent propose des mises à jour. L'humain valide. La mémoire est curatée, pas générée.


---

**Navigation** — [← Orchestrator](./orchestrator.md) · [↑ Index](../README.md) · [Agent Behavior →](./11-agent-behavior.md)
