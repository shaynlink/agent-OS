# Études de performance — Données et conclusions

> Cette page extrait les données brutes des recherches sur lesquelles l'Agent OS se base, et traduit ce que leurs conclusions impliquent concrètement pour l'utilisation du système.

---

## Liu et al. (2023) — Lost in the Middle

**Référence** : Liu, N. et al. *Lost in the Middle: How Language Models Use Long Contexts*. ACL 2023.

### Ce que l'étude mesure

Performance de plusieurs LLMs (GPT-3.5, GPT-4, Claude, Longchat) sur des tâches de question-answering multi-document, en faisant varier la **position** du document pertinent dans le contexte.

### Résultats clés

| Position du document pertinent | Performance (accuracy) |
|---|---|
| Début du contexte (primacy) | ~80% |
| Milieu du contexte | ~55% (dégradation de 25-30 points) |
| Fin du contexte (recency) | ~75% |

La dégradation est observable sur **tous les modèles testés**, avec des magnitudes différentes. Les modèles plus grands (GPT-4) dégradent moins, mais la courbe en U reste présente.

### Ce que ça implique pour l'Agent OS

1. **Skill 00 en position 1** : le protocole de raisonnement occupe la position maximale de primauté → influence maximale.
2. **Tier 1 avant Tier 2** : les standards les plus importants sont chargés avant les skills spécialisés.
3. **Chargement sélectif obligatoire** : charger 100k tokens de contexte pour une tâche simple dilue l'attention sur les 2k tokens pertinents → dégradation mesurable et prévisible.

### Limite de l'application

L'étude porte sur des tâches de retrieval. Les tâches de raisonnement et de génération de code ont des courbes de performance différentes. La conclusion directionnelle (moins en position centrale = moins de poids) reste robuste, mais les magnitudes exactes varient.

---

## Jiang et al. (2023) — LLMLingua

**Référence** : Jiang, H. et al. *LLMLingua: Compressing Prompts for Accelerated Inference of Large Language Models*. EMNLP 2023.

### Ce que l'étude mesure

Impact de la **compression sélective** du contexte sur la performance. L'étude compresse des prompts jusqu'à 20x en supprimant les tokens à faible importance informationnelle.

### Résultats clés

- Compression 4x : performance maintenue ou améliorée sur la majorité des benchmarks
- Compression 10x : dégradation visible mais modérée (5-15%)
- Compression 20x : dégradation significative (20-30%)

La conclusion centrale : **supprimer du contexte non-pertinent améliore la performance jusqu'à un seuil** — le modèle "focus" mieux sur l'information restante.

### Ce que ça implique pour l'Agent OS

Le chargement sélectif par signal (Task Detection Table) n'est pas une optimisation de performance — c'est une condition de fonction correcte. Charger du contexte non-pertinent dégrade activement les réponses sur les informations pertinentes.

---

## Shinn et al. (2023) — Reflexion

**Référence** : Shinn, N. et al. *Reflexion: Language Agents with Verbal Reinforcement Learning*. NeurIPS 2023.

### Ce que l'étude mesure

Performance d'un agent qui génère des réflexions verbales sur ses erreurs et les stocke pour les sessions futures, comparée à un agent sans cette capacité.

### Résultats clés

Sur les benchmarks HotpotQA et AlfWorld :
- Agent sans reflexion : performance plateau après 1-2 tentatives
- Agent avec reflexion verbale : amélioration continue sur 3-5 tentatives, +15-25% d'accuracy finale

### Ce que ça implique pour l'Agent OS

La Verbal RL Loop n'est pas une fonctionnalité secondaire — c'est le mécanisme principal d'amélioration inter-session. Un Agent OS sans protocol `auto-learning` actif perd le principal avantage différentiel du système.

---

## Wang et al. (2022) — Self-Consistency

**Référence** : Wang, X. et al. *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR 2023.

### Ce que l'étude mesure

Performance de la vérification de cohérence interne (multi-sampling + vote majoritaire) sur des benchmarks de raisonnement arithmétique et de logique.

### Résultats clés

- Chain-of-thought seul : ~60-70% accuracy sur GSM8K
- Self-consistency (5 samples) : ~78-85% accuracy — gain de 10-15 points
- Self-consistency (40 samples) : ~90%+ accuracy

### Ce que ça implique pour l'Agent OS

Le Self-Consistency Gate de l'orchestrateur implémente une version allégée de ce principe : pas de multi-sampling, mais une vérification structurée de cohérence interne avant finalisation. Le gain en accuracy n'est pas équivalent au multi-sampling, mais le coût est zéro token supplémentaire.

---

## Wang et al. (2024) — MoA (Mixture of Agents)

**Référence** : Wang, J. et al. *Mixture-of-Agents Enhances Large Language Model Capabilities*. Preprint 2024.

### Ce que l'étude mesure

Performance de l'agrégation de réponses de multiples LLMs (ou d'un seul LLM avec plusieurs "perspectives") comparée à une réponse unique.

### Résultats clés

- Single agent : performance baseline
- Multi-agent (4 agents, même modèle) : +8-12% sur MMLU, AlpacaEval
- Multi-agent (4 agents, modèles différents) : +15-20%

### Ce que ça implique pour l'Agent OS

Le Role Deliberation Protocol implémente cette logique avec un seul modèle : chaque rôle analyse indépendamment, les tensions sont surfacées, une synthèse est produite. Ce n'est pas aussi performant que le multi-sampling avec plusieurs modèles, mais c'est réalisable avec une seule API call.

---

## Wallace et al. (2024) — Instruction Hierarchy

**Référence** : Wallace, E. et al. *The Instruction Hierarchy: Training LLMs to Prioritize Privileged Instructions*. Preprint 2024.

### Ce que l'étude mesure

Résistance des LLMs aux injections d'instructions — cas où des instructions utilisateur overrident des instructions système.

### Résultats clés

- GPT-3.5 sans fine-tuning : 68% des injections réussissent à overrider les instructions système
- GPT-4 sans fine-tuning : 45% des injections réussissent
- Modèles fine-tunés avec hiérarchie explicit : < 10%

### Ce que ça implique pour l'Agent OS

Sans déclaration explicite de la hiérarchie (alignment.md + protocole de conflit), un agent est significativement vulnérable aux requêtes utilisateur qui contredisent les instructions système. Le système de détection et de déclaration des conflits réduit ce risque — mais ne l'élimine pas complètement sans fine-tuning.


---

**Navigation** — [← 07 — Benchmarks](./README.md) · [↑ Index](../README.md) · [Modes de Défaillance →](./known-failure-modes.md)
