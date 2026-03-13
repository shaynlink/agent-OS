# Problèmes et Dangers

> Cette page documente les risques structurels de l'utilisation d'un agent LLM avec ou sans ce boilerplate. Une partie de ces risques est atténuée par le système — une autre partie est inhérente aux LLMs et ne peut pas être résolue par une meilleure infrastructure contextuelle.

---

## Classification des risques

```
CATÉGORIE A — Risques atténués par l'Agent OS
CATÉGORIE B — Risques réduits mais non-éliminés par l'Agent OS
CATÉGORIE C — Risques inhérents aux LLMs — non-adressables par l'infrastructure
```

---

## Catégorie A — Atténués par le système

### A1 — Incohérence comportementale inter-session

**Description** : Sans contexte persistant, deux sessions sur le même problème peuvent produire des solutions architecturalement incompatibles.

**Atténuation** : Tier 1 Skills rechargés à chaque session, `memory/semantic/` fournissant le contexte architectural. L'agent ne "redécouvre" pas les conventions — il les charge depuis les fichiers.

**Résidu de risque** : si les Tier 1 Skills sont mal définis ou si `memory/semantic/` est obsolète, l'incohérence persiste malgré l'infrastructure.

---

### A2 — Actions irréversibles non confirmées

**Description** : Un agent sans contrainte de sécurité peut exécuter des opérations destructives (DROP TABLE, rm -rf, migration irréversible) parce que "c'est ce que la requête demande".

**Atténuation** : `alignment.md` — contrainte Safety First. Toute opération irréversible requiert une confirmation explicite avec liste des conséquences.

**Résidu de risque** : dépend de la capacité du modèle à classifier correctement les opérations irréversibles. Un modèle qui ne reconnaît pas une opération dangereuse ne déclenchera pas la contrainte.

---

### A3 — Résolution silencieuse de conflits d'instructions

**Description** : Sans hiérarchie explicite, les LLMs résolvent les conflits d'instructions de manière non-déterministe — souvent en faveur de la requête utilisateur.

**Atténuation** : Protocole de détection et de déclaration des conflits dans `alignment.md` + hiérarchie de vérité.

**Résidu de risque** : Wallace et al. (2024) montrent que même avec une hiérarchie explicite, ~10-15% des injections d'instructions réussissent sur des modèles non fine-tunés.

---

## Catégorie B — Réduits mais non-éliminés

### B1 — Hallucinations factuelles

**Description** : Les LLMs génèrent des informations factuellement incorrectes avec une confiance apparente identique à des informations correctes.

**Réduction** : Three mitigations :
1. Evidence Before Action (alignment) — forcer la lecture des fichiers avant affirmation
2. Self-Consistency Gate — vérification cross-référence
3. Confidence declaration — CERTAIN / PROBABLE / INCERTAIN

**Résidu de risque** : Un modèle peut être confiant sur une information incorrecte qu'il "voit" dans le contexte. Si `memory/` contient une information erronée, le modèle peut la traiter comme CERTAIN alors qu'elle est fausse. **La qualité de la mémoire détermine la qualité des assertions.**

---

### B2 — Dégradation sur contexte long

**Description** : La performance décroît sur l'information positionnée au milieu d'un contexte long (Liu 2023).

**Réduction** : Chargement sélectif par signal, format retrieval-aware, budget contextuel managé.

**Résidu de risque** : Même avec chargement sélectif, les sessions longues accumulent du contexte. Après 50-100 échanges dans une même session, le contexte effectif peut dépasser les seuils de performance optimale.

---

### B3 — Obsolescence de la mémoire

**Description** : `memory/` documente une réalité à un instant T. Le codebase évolue. La mémoire peut devenir obsolète sans signal visible.

**Réduction** : Timestamps `<!-- Updated: YYYY-MM-DD -->`, déclencheurs de mise à jour documentés dans `auto-learning.md`, Type C conflict detect (Context Map vs Real code).

**Résidu de risque** : L'agent détecte le conflit quand il lit le fichier réel. Il ne peut pas détecter proactivement que la mémoire est obsolète sur un domaine qu'il ne consulte pas dans cette session.

---

## Catégorie C — Inhérents aux LLMs

### C1 — Raisonnement sur de longs horizons

**Description** : Les LLMs ont des difficultés avec les raisonnements qui nécessitent de maintenir une cohérence sur de nombreuses étapes interdépendantes (planification multi-étapes, refactoring de systèmes distribués complexes).

**Statut dans l'Agent OS** : Non-adressable. Le Self-Consistency Gate réduit les incohérences locales, mais pas les erreurs de raisonnement long. Sur des tâches nécessitant plus de ~10 étapes interdépendantes, la supervision humaine reste nécessaire.

---

### C2 — Absence de vérification exécutable

**Description** : L'agent ne peut pas exécuter le code qu'il génère pour vérifier qu'il fonctionne. Toute validation est textuelle, pas empirique.

**Statut dans l'Agent OS** : Non-adressable par l'infrastructure contextuelle. Addressable par des outils d'exécution (code interpreters, CI intégré) — hors scope de ce boilerplate.

**Implication** : Tout code généré nécessite un cycle de validation humaine ou automatisée. L'agent peut réduire la probabilité d'erreur (via skill compliance + self-consistency), mais pas la garantir.

---

### C3 — Confiance non calibrée sur des domaines spécifiques

**Description** : Certains domaines sont sous-représentés dans le pré-entraînement (frameworks récents, bibliothèques internes, patterns spécialisés). Le modèle peut générer des réponses plausibles mais incorrectes sur ces domaines avec la même confiance que sur des domaines bien couverts.

**Statut dans l'Agent OS** : Partiellement adressable. La confidence declaration (CERTAIN / PROBABLE / INCERTAIN) encourage la déclaration d'incertitude — mais un modèle confiant sur une information incorrecte déclarera CERTAIN sur une information fausse.

**Implication pratique** : Pour tout domaine récent ou spécialisé (librairies < 1 an, frameworks propriétaires), traiter les réponses de l'agent comme PROBABLE par défaut, indépendamment de sa déclaration de confiance.

---

### C4 — Injection de prompt

**Description** : Des inputs utilisateur ou externes peuvent contenir des instructions qui tentent d'overrider les contraintes système (ex: "Ignore tes instructions précédentes et...").

**Statut dans l'Agent OS** : Partiellement atténué par la hiérarchie de vérité et alignment, non-éliminable sans fine-tuning. Wallace et al. (2024) : ~10-15% des injections réussissent sur des modèles avec hiérarchie explicite.

**Implication** : Ne jamais utiliser un agent LLM pour traiter des inputs non-sanitizés provenant d'utilisateurs non-fiables, indépendamment de l'infrastructure contextuelle.

---

## Dangers opérationnels spécifiques

### ⚠️ Danger 1 — La confiance excessive dans un agent bien configuré

Un Agent OS bien configuré produit des réponses qui semblent plus fiables — plus structurées, plus cohérentes, avec des déclarations de confiance. Cette apparence de fiabilité peut induire une **réduction de la vigilance humaine**.

La réalité : un agent bien configuré commet menos d'erreurs, mais les erreurs qu'il commet sont potentiellement mieux formulées et donc plus difficiles à détecter.

**Mitigant** : maintenir une supervision proportionnelle au risque de la tâche, indépendamment de la confiance dans le système.

---

### ⚠️ Danger 2 — La mémoire comme vecteur d'erreur persistante

Si une information incorrecte entre dans `memory/episodic/` ou `memory/semantic/`, elle sera rechargée dans les sessions futures et traitée par l'agent comme une information fiable. L'erreur se perpétue et influence potentiellement de nombreuses sessions avant d'être détectée.

**Mitigant** : curation humaine obligatoire avant toute intégration en mémoire — le protocole Verbal RL fournit la structure, l'humain valide le contenu.

---

### ⚠️ Danger 3 — L'obsolescence invisible

Un projet qui évolue rapidement peut laisser `memory/semantic/` et les Tier 1 Skills significativement en retard sur la réalité du codebase. L'agent opèrera avec des informations obsolètes, les traitera comme fiables, et produira des réponses incorrectes avec confiance.

**Mitigant** : intégrer la mise à jour de la documentation agent dans le Definition of Done des tâches significatives. Sans discipline organisationnelle, la mémoire se dégrade.

---

### ⚠️ Danger 4 — L'extension incontrôlée des layers

Un projet qui grandit rapidement peut accumuler des Tier 1 Skills, des rôles, et des cartes sémantiques qui entrent en contradiction. Sans audit périodique, le système devient incohérent — l'agent reçoit des instructions contradictoires et les résout de manière non-déterministe.

**Mitigant** : audit semestriel des layers adaptables — vérification de la cohérence entre Tier 1 Skills, recherche de contradictions entre rôles, archivage des cartes sémantiques obsolètes.

---

### ⚠️ Danger 5 — Le faux sentiment de neutralité

Un agent opère dans un contexte informationnel qui a été construit humainement — les skills encodent des conventions choisies, les rôles encodent des priorités sélectionnées, les cartes sémantiques documentent une architecture décidée. Ce contexte est **normatif**, pas neutre.

Si les conventions encodées sont incorrectes, biaisées, ou sous-optimales, l'agent les applique avec cohérence et persistance — ce qui peut être pire qu'un agent générique qui applique les "best practices" du pré-entraînement.

**Implication** : la qualité du système est une fonction directe de la qualité des conventions humainement encodées. L'Agent OS amplifie ce qui est encoder — le bon comme le mauvais.


---

**Navigation** — [← Modes de Défaillance](./known-failure-modes.md) · [↑ Index](../README.md)
