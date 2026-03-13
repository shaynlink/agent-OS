# Limites et non-usages

> Un système honnête documente ses propres limites. Utiliser l'Agent OS sur des cas pour lesquels il n'a pas été conçu produit des résultats pires qu'un simple prompt — parce que la structure donne une fausse impression de fiabilité.

---

## Non-usages formels

### Sessions one-shot sans continuité

**Cas** : utiliser un LLM pour des tâches ponctuelles sans suite — génération de texte, analyse d'un fichier unique, réponse à une question isolée.

**Pourquoi le système n'est pas adapté** : le ROI de l'Agent OS est une fonction de l'accumulation inter-sessions. La mémoire épisodique accumule les leçons, la mémoire sémantique documente l'architecture, les skills s'affinent. Pour une session unique, le coût de setup est supérieur au bénéfice.

**Ce qui est adapté** : un prompt structuré simple, sans infrastructure.

---

### Projets sans conventions établies

**Cas** : utiliser l'Agent OS sur un projet en phase d'exploration où les conventions architecturales ne sont pas encore stabilisées.

**Pourquoi le système n'est pas adapté** : les Tier 1 Skills encodent des conventions. Si les conventions changent à chaque sprint, les skills sont obsolètes en permanence — et les mises à jour continues coûtent plus qu'ils n'apportent. Pire : un agent avec des skills obsolètes est plus dangereux qu'un agent sans skills — il applique des conventions incorrectes avec confiance.

**Ce qui est adapté** : établir les conventions humainement d'abord, puis les encoder dans le système une fois stabilisées.

---

### Remplacement d'un système RAG sur large codebase

**Cas** : utiliser `memory/semantic/` comme système de retrieval principal sur un codebase de 500k+ lignes.

**Pourquoi le système n'est pas adapté** : `memory/semantic/` est une carte **curatée manuellement** au niveau des modules et des domaines. Maintenir manuellement une couverture exhaustive d'un codebase large est intennable — les mises à jour sont trop fréquentes, le volume trop important.

**Ce qui est adapté** : un système de retrieval vectoriel (embeddings + vector store) pour l'indexation exhaustive, avec `memory/semantic/` comme couche de connaissance architecturale haut-niveau au-dessus.

---

### Modèles avec instruction-following faible

**Cas** : utiliser l'Agent OS avec des modèles dont la capacité à suivre des instructions structurées est limitée (modèles < 7B paramètres, modèles non-instruct, modèles anciens).

**Pourquoi le système n'est pas adapté** : l'Agent OS repose sur la capacité du modèle à :
1. Produire un bloc `<reasoning>` structuré en premier token
2. Respecter une hiérarchie d'instructions avec résolution explicite des conflits
3. Maintenir la cohérence du comportement sur des contextes longs

Ces capacités nécessitent un niveau d'instruction-following robuste, disponible typiquement à partir de GPT-4-level, Claude 3+, Gemini 1.5+. Avec des modèles plus faibles, le kernel génère du bruit — des instructions non suivies, une structure ignorée, des conflits non déclarés.

**Ce qui est adapté** : un prompt optimisé pour le modèle cible, sans la complexité de l'Agent OS.

---

### Automatisation complète de la mise à jour mémoire

**Cas** : générer automatiquement les mises à jour de `memory/` après chaque session, sans revue humaine.

**Pourquoi le système n'est pas adapté** : la mémoire automatiquement générée capture du signal **et du bruit indistinctement**. Un bug non résolu peut être catégorisé comme une solution. Une inférence incorrecte peut être stockée comme un fait. Un anti-pattern peut être capturé comme un pattern réussi.

La mémoire non curatée se dégrade — et une mémoire dégradée induit activement en erreur plutôt que d'aider. Le coût d'une entrée mémoire incorrecte est supérieur au coût d'une entrée manquante.

**Ce qui est adapté** : l'agent propose des mises à jour structurées (format Verbal RL) ; l'humain valide avant intégration.

---

## Limites structurelles non-adressables

### Le modèle reste la fondation

L'Agent OS améliore l'environnement informationnel — il ne compense pas les limitations intrinsèques du modèle. Si le modèle halluciné sur un domaine technique spécifique, ni les skills ni les rôles ni la mémoire n'élimineront cette hallucination. Ils peuvent la réduire (par Evidence Before Action + Self-Consistency Gate), mais pas l'éliminer.

### Latence de curation

La mémoire ne peut pas être aussi fraîche que le codebase en mouvement. Il y a toujours un décalage entre ce que le code est et ce que la mémoire dit qu'il est. Minimiser ce décalage nécessite une discipline de curation après chaque session significative — ce qui est une discipline organisationnelle, pas une garantie technique.

### Context window non garantie

L'orchestrateur gère le budget contextuel de manière heuristique — il charge ce qui semble pertinent selon les signaux détectés. Un signal mal formulé dans la requête peut manquer une ressource nécessaire, ou en charger une inutile. Le Self-Consistency Gate détecte certaines de ces erreurs, mais pas toutes.

### Le noyau lui-même est une hypothèse

Les 5 fichiers du kernel représentent les meilleures pratiques connues au moment de leur création. Ils intègrent des conclusions de recherches de 2022-2024. Certaines de ces conclusions seront révisées. Le kernel doit être mis à jour périodiquement pour intégrer les avancées — ce qui implique une interruption et une migration pour tous les projets qui l'utilisent.


---

**Navigation** — [← Hiérarchie de Vérité](./hierarchy-of-truth.md) · [↑ Index](../README.md) · [06 — Science →](../06-science/README.md)
