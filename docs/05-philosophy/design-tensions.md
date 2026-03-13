# Design Tensions — Tensions architecturales assumées

> Un système bien conçu ne résout pas toutes les tensions — il les identifie, choisit explicitement un côté, et documente pourquoi. Ces 3 tensions structurent l'ensemble des décisions d'architecture de l'Agent OS.

---

## Tension 1 — Immutabilité vs Adaptabilité

### L'énoncé de la tension

Un kernel immuable garantit la cohérence comportementale universelle. Un système entièrement adaptable maximise la flexibilité project-spécifique. Ces deux propriétés sont incompatibles dans un seul composant.

### La résolution choisie

**Séparation stricte** : immutabilité pour le kernel (5 fichiers), adaptabilité totale pour les layers (tout le reste). Le kernel ne garantit que les propriétés qui doivent être vraies pour **tout projet, tout modèle, tout contexte** : raisonnement structuré, contraintes de sécurité, chargement sélectif, apprentissage, comportement communicationnel.

### Pourquoi ce côté

La valeur principale d'un kernel est sa **prévisibilité**. Un agent qui a chargé `alignment.md` se comportera de manière identifiable sur des questions de sécurité — indépendamment du projet, du rôle activé, ou de la formulation de la requête. Cette prévisibilité est la fondation sur laquelle une équipe peut bâtir une confiance raisonnée dans l'agent.

Un kernel que l'on peut modifier "pour ce cas particulier" n'est pas un kernel — c'est une configuration parmi d'autres. Sa valeur de garantie s'effondre dès la première exception.

### La contre-force gérée

La flexibilité ne disparaît pas — elle est **relocalisée** vers les layers adaptables. Si le kernel semble trop contraignant pour un cas projet-spécifique, le bon diagnostic est : un Tier 1 Skill manque, ou un Role n'a pas été défini. Pas : le kernel doit changer.

---

## Tension 2 — Mémoire curatée vs Mémoire automatisée

### L'énoncé de la tension

Une mémoire automatiquement mise à jour scalable et ne nécessite pas de discipline humaine. Une mémoire curatée manuellement produit de meilleures informations mais nécessite un effort humain continu et ne scale pas.

### La résolution choisie

**Curation humaine obligatoire** pour toute intégration dans la mémoire persistante (`episodic/`, `semantic/`, `procedural/`). L'agent peut proposer des mises à jour — l'humain valide avant intégration.

### Pourquoi ce côté

La mémoire automatisée a un défaut structurel : **elle capitalise les erreurs aussi bien que les réussites**. Un modèle qui génère automatiquement des entrées mémoire après chaque session va capturer des inférences incorrectes, des bugs non résolus présentés comme des solutions, et des anti-patterns non identifiés comme tels.

La curation humaine est un filtre de qualité. Il coûte du temps, mais il garantit que la mémoire ne se dégrade pas — ce qui serait pire que l'absence de mémoire.

Concrètement : une base mémoire de 50 entrées curatées vaut plus qu'une base de 500 entrées automatisées dont 30% sont des approximations incorrectes.

### La contre-force gérée

Le coût de la curation est adressé par la **structure du protocole Verbal RL** : l'agent fait le travail de formulation (RCA, scoring, format) ; l'humain fait le travail de validation (correct / à corriger / à supprimer). Ce n'est pas 100% du travail humain — c'est la validation finale d'un travail déjà structuré.

---

## Tension 3 — Exhaustivité contextuelle vs Performance contextuelle

### L'énoncé de la tension

Charger plus de contexte augmente la probabilité que l'information nécessaire soit disponible. Charger plus de contexte dégrade la performance sur les informations critiques (Liu 2023 — courbe en U).

### La résolution choisie

**Chargement sélectif par signal** : charger uniquement ce qui est nécessaire à la tâche en cours, dans la quantité minimale suffisante.

```
targeted section > full file > index summary > nothing
```

### Pourquoi ce côté

L'utilité marginale d'un token de contexte supplémentaire décroît à mesure que le contexte s'allonge. Le coût marginal en attention du modèle reste constant. Au-delà d'un certain seuil, chaque token additionnel est plus susceptible de diluer l'attention sur un token existant que d'apporter de l'information utile.

L'intuition inverse — "plus de contexte = meilleure réponse" — est vérifiée pour des contextes courts, puis inversée pour des contextes longs. Le seuil dépend du modèle (approximativement autour de 20-50k tokens selon les benchmarks), mais la tendance est robuste.

### La contre-force gérée

Le risque du chargement sélectif est de **charger la mauvaise section**. Ce risque est adressé par :
1. La Task Detection Table — mapping signaux → ressources, maintenu humainement
2. Le Self-Consistency Gate — vérification cross-référence avant finalisation
3. La contrainte "Evidence Before Action" d'alignment — forcer la vérification quand l'information disponible est insuffisante

Si une section chargée est insuffisante pour la tâche, le comportement correct est de déclarer l'insuffisance et de demander davantage d'information — pas d'inférer silencieusement.

---

## La méta-tension

Ces 3 tensions partagent une structure commune : elles opposent **garantie vs flexibilité**, **qualité vs volume**, **précision vs exhaustivité**. Dans chaque cas, l'Agent OS choisit le premier terme et relativise le second.

Ce choix est cohérent avec la position philosophique centrale : **il vaut mieux faire moins, mais le faire correctement et de manière vérifiable**, que faire plus avec une incertitude croissante sur la qualité.

Ce n'est pas un compromis de facilité — c'est un choix architectural délibéré adapté au contexte d'un système qui opère sur du code en production.


---

**Navigation** — [← Problème Fondamental](./fundamental-problem.md) · [↑ Index](../README.md) · [Hiérarchie de Vérité →](./hierarchy-of-truth.md)
