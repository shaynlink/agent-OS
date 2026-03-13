# Le Problème Fondamental

> Ce document ne présente pas un outil — il articule un problème structurel dans la façon dont les LLMs sont actuellement intégrés dans les workflows de développement, et en quoi cette intégration est sous-optimale par construction.

---

## Le LLM comme processeur sans état

Un LLM déployé sans infrastructure contextuelle est, du point de vue informationnel, un **processeur sans état** : il peut exécuter des transformations complexes sur l'information qui lui est présentée, mais il ne conserve aucun état entre les exécutions, ne dispose d'aucune connaissance du système sur lequel il opère, et ne peut pas apprendre de ses opérations passées.

Cette absence d'état n'est pas une limitation du modèle — elle est consubstantielle à l'architecture transformer. Les poids du modèle encodent des compétences générales ; ils n'encodent pas l'état d'un projet spécifique. Attendre qu'un LLM soit "naturellement cohérent" sur un projet complexe sans infrastructure contextuelle revient à attendre qu'un processeur produise des résultats sans OS — techniquement possible pour des tâches suffisamment simples, structurellement impossible pour des tâches impliquant des conventions, des dépendances, et une mémoire inter-session.

---

## Les 8 dysfonctions observées

### 1. Amnésie inter-session

Chaque session repart de zéro. Les décisions architecturales prises lors de la session précédente, les conventions établies, les leçons apprises, les bugs résolus — tout est inaccessible sans réinjection explicite. Un développeur qui rejoint un projet apprend en quelques semaines. Un LLM sans infrastructure contextuelle "apprend" à chaque session, puis "oublie" à la fermeture.

**Impact mesurable** : les conventions doivent être réexpliquées dans chaque session qui les concerne — temps gaspillé, incohérences introduites quand elles ne le sont pas.

### 2. Incohérence inter-session

Deux sessions sur le même problème peuvent produire deux solutions architecturalement incompatibles. Session A utilise un pattern event-driven ; session B utilise du polling. Sans mémoire persistante des conventions, chaque session est une décision indépendante, non contrainte par les décisions précédentes.

**Impact mesurable** : revue de code nécessaire pour détecter les incohérences architecturales introduites par des sessions LLM successives sur des composants interdépendants.

### 3. Généralisme non-calibré

Un LLM sans rôle activé répond depuis le point de vue du développeur générique médian de son pré-entraînement. Ce développeur générique n'a pas les priorités d'un security reviewer, ni les contraintes d'un data architect, ni les standards d'un API designer. Les réponses sont techniquement plausibles mais manquent de l'affûtage d'une expertise spécifique.

### 4. Dégradation contextuelle

La performance d'un LLM sur des tâches complexes nécessitant de l'information distribuée dans un contexte long est non-linéaire. Liu et al. (2023) démontrent qu'elle suit une **courbe en U** : forte sur les 20% premiers et les 20% derniers du contexte, significativement dégradée sur les 60% du milieu. Un contexte de 100k tokens non structuré est moins performant qu'un contexte de 20k tokens bien sélectionné.

### 5. Surfacing d'hypothèses implicites

Sans mécanisme forçant la déclaration des hypothèses, le modèle infère des faits non vérifiés — structure de fichiers, contrats d'API, schéma de base de données — et produit des réponses basées sur ces inférences. Les inférences sont souvent correctes pour des projets à structure canonique ; elles sont souvent incorrectes pour des projets où les conventions dévient du standard.

### 6. Absence de capitalisation

Les bugs découverts et résolus, les patterns qui fonctionnent, les anti-patterns identifiés — aucun de ces enseignements ne persiste entre les sessions. Un bug récurrent sera "résolu" encore et encore, sans que la résolution de la session N+1 bénéficie de la résolution de la session N.

### 7. Résolution non-déterministe des conflits d'instructions

Quand un système prompt, un rôle, et une requête utilisateur entrent en conflit, le modèle résout le conflit de manière non-déterministe selon Wallace et al. (2024). Une requête utilisateur peut overrider une contrainte système si elle est formulée avec suffisamment d'autorité. Sans hiérarchie explicite et protocole de déclaration, les conflits sont résolus silencieusement — et parfois de manière incorrecte.

### 8. Confiance non-calibrée

Un modèle sans protocole de déclaration de confiance présente ses inférences avec la même assurance que ses certitudes. L'utilisateur ne peut pas distinguer ce que le modèle sait avec certitude de ce qu'il infère de manière plausible mais non vérifiée.

---

## Ce que ces dysfonctions ont en commun

Aucune de ces 8 dysfonctions n'est une limitation du modèle au sens strict. Elles sont toutes des **conséquences de l'absence d'infrastructure**. Un modèle puissant dans un environnement informationnel pauvre produit des résultats pauvres. Le même modèle dans un environnement structuré produit des résultats structurés.

La solution n'est pas un meilleur modèle. La solution est une meilleure ingénierie de l'environnement dans lequel le modèle opère.

---

## Ce que l'Agent OS résout structurellement

| Dysfonction | Mécanisme de résolution | Composant |
|---|---|---|
| Amnésie inter-session | Mémoire structurée persistante | `memory/episodic/` + `memory/semantic/` |
| Incohérence inter-session | Standards encodés et rechargés systématiquement | Tier 1 Skills |
| Généralisme non-calibré | Activation de rôles spécialisés | `roles/` + Role Deliberation |
| Dégradation contextuelle | Chargement sélectif par signal et par section | `orchestrator.md` |
| Hypothèses implicites | Protocole Evidence Before Action | `alignment.md` |
| Absence de capitalisation | Verbal RL Loop — capture systématique | `auto-learning.md` |
| Conflits non-déterministes | Hiérarchie de vérité explicite + déclaration | `alignment.md` — Conflict Detection |
| Confiance non-calibrée | Score de confiance obligatoire | `orchestrator.md` — Check 3 |

---

## Ce que l'Agent OS ne résout pas

Il ne résout pas les limitations liées aux **capacités du modèle** : hallucinations factuelles sur des domaines hors du pré-entraînement, raisonnement mathématique complexe sur de longs horizons, ou tâches nécessitant de l'information en temps réel.

Il ne se substitue pas à un **système RAG** sur un codebase massif : pour 500k+ lignes, le retrieval vectoriel reste nécessaire. `memory/semantic/` est une carte curatée, pas un index exhaustif.

Il ne remplace pas **la revue humaine** : la confiance dans l'output de l'agent doit rester proportionnelle au niveau de risque de la tâche, même avec un Agent OS bien configuré.


---

**Navigation** — [← 05 — Philosophie](./README.md) · [↑ Index](../README.md) · [Design Tensions →](./design-tensions.md)
