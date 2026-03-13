# Mémoire & Retrieval

> Comment les systèmes cognitifs organisent la mémoire, et comment les LLMs peuvent récupérer de l'information pertinente dans de larges corpus. Ces quatre papers justifient l'architecture `memory/` et le format retrieval-aware de `memory/semantic/`.

---

## Patil et al. (2023) — Gorilla

**Référence complète** : Patil, S. G., Zhang, T., Wang, X., & Gonzalez, J. E. (2023). *Gorilla: Large Language Model Connected with Massive APIs*. Preprint (arXiv:2305.15334).

### Ce que l'étude mesure

Performance d'un LLM sur la sélection et l'utilisation correcte d'APIs (HuggingFace, TorchHub, TensorHub) dans deux conditions : avec documentation injectée dans le contexte vs. avec retrieval ciblé depuis un document index.

### Résultats clés

- **Hallucination API avec documentation exhaustive** : 15-30% d'erreurs
- **Hallucination API avec retrieval ciblé** : 6-12% d'erreurs (-50%)
- Le modèle fine-tuné avec retrieval dépasse GPT-4 sans retrieval sur ce benchmark

**Conclusion** : injecter l'intégralité de la documentation produit plus d'erreurs que du retrieval ciblé — l'information non-pertinente crée du bruit qui interfère avec l'information pertinente.

### Traduction dans l'Agent OS

**Décision** : La Task Detection Table de `AGENTS.md` + le format `<section id="">` de `memory/semantic/`.

Au lieu d'injecter toute la documentation du codebase, l'orchestrateur :
1. Détecte les signaux dans la requête (`payment`, `stripe`, `webhook`)
2. Charge uniquement la section correspondante depuis `semantic/`

Ce principe est la fondation du chargement sélectif : moins de contexte non-pertinent = moins de bruit = meilleures décisions sur le contexte pertinent.

---

## Lewis et al. (2020) — RAG

**Référence complète** : Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., ... & Kiela, D. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. NeurIPS 2020.

### Ce que l'étude mesure

Performance d'un modèle qui combine **génération** (LLM) et **retrieval** (document index) comparée à un modèle purement paramétrique (sans retrieval).

### Résultats

Sur Natural Questions (open-domain QA) :
- Modèle paramétrique seul : 44.5%
- RAG (retrieval + génération) : 56.8% (+12.3 pts)

Le RAG réduit également significativement les hallucinations factuelles sur des domaines où le modèle est moins entraîné.

### Traduction dans l'Agent OS

**Décision** : Architecture `memory/semantic/` comme base de connaissance curatée à injection sélective.

`memory/semantic/` n'est pas un système RAG complet (pas d'embeddings, pas de vector store) — c'est une approximation curatée et manuellement maintenue qui implémente le principe fondamental de RAG : **injecter de la connaissance externe dans le contexte au moment de la requête** plutôt que de se fier uniquement aux poids du modèle.

**Limite de l'implémentation** : pour un codebase suffisamment large (> 100k lignes), les cartes sémantiques manuelles ne peuvent pas couvrir exhaustivement l'ensemble. Un vrai système RAG vectoriel est nécessaire en complément pour l'indexation exhaustive.

---

## Sumers et al. (2023) — CoALA

**Référence complète** : Sumers, T. R., Yao, S., Narasimhan, K., & Griffiths, T. L. (2023). *Cognitive Architectures for Language Agents*. TMLR 2024.

### Ce que l'étude propose

Un cadre théorique pour conceptualiser les systèmes d'agents LLMs en termes de **mémoires cognitives** issues des sciences cognitives. CoALA (Cognitive Architectures for Language Agents) propose une taxonomie des types de mémoire et de leurs rôles dans un système d'agent.

### Taxonomie des mémoires (CoALA)

| Type | Nature | Durée de vie | Rôle |
|---|---|---|---|
| **Working memory** | Court terme, explicite | Session | État de la tâche en cours |
| **Episodic memory** | Long terme, événements | Permanente | Expériences passées récupérables |
| **Semantic memory** | Long terme, faits | Permanente | Connaissance structurée du monde |
| **Procedural memory** | Long terme, implicite | Permanente | Actions et compétences |

### Traduction dans l'Agent OS

**Décision** : Architecture `memory/` à 4 sous-répertoires.

La structure `memory/working/`, `memory/episodic/`, `memory/semantic/`, `memory/procedural/` est une implémentation directe de la taxonomie CoALA :

```
memory/working/    ← CoALA Working Memory  : volatile, session-scoped
memory/episodic/   ← CoALA Episodic Memory : bugs, RCAs, leçons
memory/semantic/   ← CoALA Semantic Memory : cartes de connaissance codebase
memory/procedural/ ← CoALA Procedural Memory : patterns éprouvés, recettes
```

Cette segmentation n'est pas arbitraire — elle reflète les propriétés distinctes de chaque type de mémoire en sciences cognitives : durée de vie, mode de récupération, et rôle dans le traitement de l'information.

---

## Anderson (2004) — ACT-R

**Référence complète** : Anderson, J. R., Bothell, D., Byrne, M. D., Douglass, S., Lebiere, C., & Qin, Y. (2004). *An integrated theory of the mind*. Psychological Review, 111(4), 1036.

### Ce que la théorie propose

ACT-R (Adaptive Control of Thought — Rational) est un modèle de l'architecture cognitive humaine qui distingue plusieurs types de mémoire avec des propriétés différentes — notamment la distinction entre **mémoire déclarative** (faits et expériences) et **mémoire procédurale** (règles d'action).

### Traduction dans l'Agent OS

**Décision** : La distinction fonctionnelle entre `memory/episodic/`, `memory/semantic/`, et `memory/procedural/`.

ACT-R formalise ce que l'intuition suggère : les faits mémorisés (semantic/), les expériences mémorisées (episodic/), et les compétences mémorisées (procedural/) ne sont pas interchangeables — ils ont des modes d'acquisition, de récupération, et de dégradation différents.

En pratique pour l'Agent OS :
- Une leçon apprise (episodic) est une observation d'un événement passé
- Un pattern éprouvé (procedural) est une compétence extraite de répétition
- Une carte de connaissance (semantic) est un fait structurel sur le système

Mettre une compétence en mémoire épisodique (ou inversement) n'est pas une erreur fatale, mais c'est sous-optimal : les modes de récupération et les critères de mise à jour diffèrent.

---

**Navigation** — [← Multi-Agent & Hiérarchie](./multi-agent.md) · [↑ Index](../README.md) · [07 — Benchmarks →](../07-benchmarks/README.md)
