# Apprentissage & Auto-amélioration

> Comment un LLM peut apprendre sans fine-tuning. Les trois papers de cette section justifient la Verbal RL Loop et le Self-Consistency Gate — les deux mécanismes qui permettent à l'agent de s'améliorer inter-session et intra-session.

---

## Shinn et al. (2023) — Reflexion

**Référence complète** : Shinn, N., Cassano, F., Labash, B., Gopinath, A., Narasimhan, K., & Yao, S. (2023). *Reflexion: Language Agents with Verbal Reinforcement Learning*. NeurIPS 2023.

### Ce que l'étude mesure

Performance d'un agent qui génère des **réflexions verbales** sur ses erreurs après chaque tentative, stocke ces réflexions dans une mémoire épisodique, et les recharge aux sessions suivantes.

Benchmarks : HotpotQA (question-answering), AlfWorld (navigation), HumanEval (génération de code).

### Résultats

| Condition | HotpotQA | HumanEval |
|---|---|---|
| Sans reflexion (baseline) | 60% | 67.0% |
| Avec réflexion verbale (3 tentatives) | 73% (+13pts) | 80.1% (+13pts) |
| Avec réflexion verbale (5 tentatives) | 80%+ | 91.0%+ |

La progression est **continue** sur plusieurs tentatives — contrairement aux baselines qui plateaux après 1-2 tentatives.

### Traduction dans l'Agent OS

**Décision** : `auto-learning.md` — Verbal RL Error Signal.

Le protocole RCA 5 Whys dans `memory/episodic/` est directement inspiré de Reflexion. Quand un bug est résolu, l'agent génère une réflexion verbale structurée et l'extrait as une règle stockée en mémoire épisodique.

```
Bug découvert → RCA 5 Whys → "La prochaine fois je dois..."
→ Extracted rule → memory/episodic/known-pitfalls.md
→ Rechargé lors des sessions où le signal correspond
```

**Différence avec Reflexion** : la mémoire est persistante entre les sessions (pas seulement dans la session courante). Une leçon apprise en session N est disponible en session N+100.

### Limite critique

Reflexion améliore les performances sur des tâches avec **feedback clair** (test qui passe / ne passe pas, navigation qui réussit / échoue). Pour des tâches ambiguës sans signal de succès/échec binaire, l'amélioration est moins robuste.

---

## Zelikman et al. (2022) — STaR

**Référence complète** : Zelikman, E., Wu, Y., Mu, J., & Goodman, N. D. (2022). *STaR: Bootstrapping Reasoning With Reasoning*. NeurIPS 2022.

### Ce que l'étude mesure

Un modèle qui génère des **rationalisations** de ses propres réponses correctes, puis s'entraîne sur ces rationalisations, améliore ses performances sur des problèmes similaires. Le processus est bootstrap : de bonnes réponses → de bonnes rationalisations → de meilleures réponses.

### Résultats

Sur des benchmarks de raisonnement arithmétique et logique (CommonsenseQA, GSM8K) :
- Baseline (sans rationalisations) : ~48%
- Avec STaR (5 itérations de bootstrap) : ~73% (+25 pts)

### Traduction dans l'Agent OS

**Décision** : `auto-learning.md` — Verbal RL Success Signal.

Quand un pattern complexe fonctionne remarquablement, l'agent génère une rationalisation complète : pourquoi ce pattern, pourquoi pas les alternatives, dans quelles conditions l'appliquer. Cette rationalisation est stockée dans `memory/procedural/`.

```
Pattern réussi → Rationalisation complète (conditions + étapes + pourquoi)
→ memory/procedural/ (si confidence > 0.6)
→ Tier 2 Skill (si confidence > 0.9, vu ≥ 3 fois)
```

**Pourquoi la rationalisation, pas juste le résultat** : le champ `**Solution** (raisonnement complet)` dans le format de `memory/procedural/` est directement inspiré de STaR. Un pattern sans rationalisation est una boîte noire — réutilisable dans le bon contexte, inutilisable ailleurs.

---

## Wang et al. (2022) — Self-Consistency

**Référence complète** : Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E., Narang, S., Chowdhery, A., & Zhou, D. (2022). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR 2023.

### Ce que l'étude mesure

Performance du **vote majoritaire** sur plusieurs rationalisations (chain-of-thought) indépendantes pour la même question, comparée à une rationalisation unique.

### Résultats

| Condition | GSM8K | SVAMP | AQuA |
|---|---|---|---|
| CoT seul (greedy) | 56.5% | 69.9% | 45.3% |
| Self-consistency (5 samples) | 72.6% (+16pts) | 83.8% (+14pts) | 54.7% (+9pts) |
| Self-consistency (40 samples) | 87.3% (+31pts) | 93.7% (+24pts) | 71.6% (+26pts) |

Les gains sont **robustes et additifs** : plus de samples → plus de performance, jusqu'à un plateau.

### Traduction dans l'Agent OS

**Décision** : `orchestrator.md` — Self-Consistency Gate (Phase 5).

L'implémentation dans l'Agent OS est une version allégée : pas de multi-sampling (coût trop élevé), mais une **vérification structurée de cohérence interne** avant finalisation :

```
Check 1 : la réponse se contredit-elle ?
Check 2 : les assertions ont-elles été vérifiées contre les fichiers réels ?
Check 3 : le niveau de confiance est-il déclaré (CERTAIN / PROBABLE / INCERTAIN) ?
```

Ce n'est pas aussi performant que 40 samples de Self-Consistency, mais le coût est zéro token supplémentaire. La qualité du check dépend de la rigueur du modèle à appliquer le protocole — d'où son inclusion dans `orchestrator.md` comme étape obligatoire.

---

**Navigation** — [← Attention & Contexte](./context-attention.md) · [↑ Index](../README.md) · [Multi-Agent & Hiérarchie →](./multi-agent.md)
