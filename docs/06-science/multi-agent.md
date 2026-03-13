# Multi-Agent & Hiérarchie d'Instructions

> Comment plusieurs perspectives améliorent les décisions, et comment les conflits d'instructions se résolvent. Ces deux papers justifient le Role Deliberation Protocol et la Hierarchy of Truth de l'Agent OS.

---

## Wang et al. (2024) — MoA (Mixture of Agents)

**Référence complète** : Wang, J., Wang, J., Athiwaratkun, B., Zhang, C., & Zou, J. (2024). *Mixture-of-Agents Enhances Large Language Model Capabilities*. Preprint (arXiv:2406.04692).

### Ce que l'étude mesure

Performance de l'agrégation de réponses de multiples LLMs — chaque agent analyse indépendamment, puis un agent agrégateur synthétise. Comparaison avec un agent unique de même capacité.

### Résultats

| Condition | AlpacaEval 2.0 | MT-Bench | FLASK |
|---|---|---|---|
| GPT-4 seul (baseline) | 57.5% | 9.0 | 95.5% |
| MoA (4 agents, même modèle) | 65.1% (+7.6) | 9.2 | 96.4% |
| MoA (4 agents, modèles mixtes) | 74.3% (+16.8) | 9.7 | 97.5% |

L'effet est **cumulatif** : plus d'agents avec des perspectives différentes → meilleure agrégation.

### Traduction dans l'Agent OS

**Décision** : `orchestrator.md` — Role Deliberation Protocol (Phase 4).

L'implémentation dans l'Agent OS adapte MoA à un contexte single-model / single-API :

```markdown
Activation : 2+ rôles nécessaires simultanément pour une tâche

[backend-engineer] analyse indépendamment
→ "Approche X pour la performance. Risque : Y"

[security-reviewer] analyse indépendamment
→ "Contrainte Z. L'approche X crée une surface d'attaque sur A"

⚠️ TENSION détectée → déclarée explicitement
Résolution : Security > Performance (alignment.md)
Synthèse : implémentation satisfaisant les deux contraintes
```

**Ce que l'adaptation perd** : la diversité entre agents différents (meilleure dans l'étude). Un seul modèle jouant 2 rôles n'est pas équivalent à 2 modèles distincts.

**Ce que l'adaptation gagne** : pas de latence supplémentaire, pas de coût API multiplié. Le gain est directionnel, pas de même magnitude que l'étude originale.

### Quand ne pas activer le Role Deliberation

Activer la délibération inutilement produit des tensions artificielles et ralentit les réponses. Le protocole se justifie uniquement quand :
1. Deux domaines d'expertise ont des priorités potentiellement conflictuelles
2. La décision a des implications pour les deux domaines

Pour une tâche purement technique sans ambiguïté de priorisation, un seul rôle activé est plus efficace.

---

## Wallace et al. (2024) — Instruction Hierarchy

**Référence complète** : Wallace, E., Xiao, K., Leike, R., Weng, L., Heidecke, J., & Beutel, A. (2024). *The Instruction Hierarchy: Training LLMs to Prioritize Privileged Instructions*. Preprint (arXiv:2404.13208).

### Ce que l'étude mesure

La **vulnérabilité des LLMs aux injections d'instructions** : dans quelle mesure une instruction utilisateur peut-elle overrider des instructions système, même quand le système prompt dit explicitement de ne pas le faire ?

### Résultats

| Modèle | % d'injections réussies (override system prompt) |
|---|---|
| GPT-3.5 sans entraînement à la hiérarchie | ~68% |
| GPT-4 sans entraînement à la hiérarchie | ~45% |
| Modèles fine-tunés avec Instruction Hierarchy | ~8-12% |

**Conclusion** : sans mécanisme explicite de hiérarchie, près de la moitié des injections réussissent même sur les meilleurs modèles disponibles.

### Traduction dans l'Agent OS

**Décision** : `alignment.md` — Hiérarchie de vérité + Conflict Detection Protocol.

La hiérarchie explicite déclarée dans `alignment.md` :
```
AGENTS.md > alignment.md > Skill > Role > Requête utilisateur
```

Et le protocole de déclaration des conflits (Type A, B, C) imposent que tout conflit soit :
1. Détecté explicitement
2. Résolu selon la hiérarchie
3. Déclaré dans la réponse

**Limitation importante** : l'étude montre que la réduction maximale (à ~8-12%) nécessite du **fine-tuning**. Un modèle standard sans fine-tuning avec une hiérarchie déclarée dans le prompt obtient une meilleure résistance que sans hiérarchie — mais pas les mêmes résultats qu'un modèle fine-tuné. La hiérarchie textuelle est une atténuation, pas une protection complète.

### Implication pratique

Ne jamais faire confiance à un agent LLM non fine-tuné pour traiter des inputs non-sanitizés provenant d'utilisateurs non-fiables, même avec une hiérarchie explicite dans le prompt. L'Instruction Hierarchy de l'Agent OS réduit le risque d'injection dans un contexte de développement de bonne foi — elle ne constitue pas une protection contre des attaques délibérées.

---

**Navigation** — [← Apprentissage & Auto-amélioration](./learning-improvement.md) · [↑ Index](../README.md) · [Mémoire & Retrieval →](./memory-retrieval.md)
