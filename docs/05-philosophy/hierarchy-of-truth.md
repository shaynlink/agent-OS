# Hierarchy of Truth — Résolution des conflits

> Un système d'agent reçoit des instructions de sources multiples : system prompt, skills, rôles, requête utilisateur. Quand ces sources entrent en conflit, le comportement naïf est de "faire de son mieux". Le comportement structuré est de suivre une hiérarchie explicite et de déclarer le conflit.

---

## La hiérarchie

```
AGENTS.md (Bootloader)              ← Priorité absolue
  > alignment.md (Constraints)      ← Non-bypassable
    > Skill technique (Rule)         ← Standards de qualité
      > Role (Persona)               ← Expertise contextuelle
        > Requête utilisateur         ← Demande immédiate
```

Cette hiérarchie est **déterministe** : en cas de conflit, le niveau supérieur gagne toujours. Pas de cas particuliers. Pas d'exceptions non documentées. Si un niveau inférieur semble nécessiter une exception au niveau supérieur, la bonne réponse est de déclarer le conflit et de proposer une alternative conforme — pas de résoudre silencieusement en faveur de la requête.

**Priorité de résolution en cas de conflit dans le même niveau** :
```
Sécurité > Architecture > Performance > Developer Experience
```

---

## Source : Wallace et al. (2024)

Wallace et al. (2024) — *Instruction Hierarchy* — démontrent expérimentalement que les LLMs sans hiérarchie explicite sont **manipulables par injection d'instructions**. Une requête utilisateur formulée avec suffisamment d'autorité peut overrider des instructions système — indépendamment de l'importance de ces instructions.

Ce n'est pas un bug du modèle — c'est une propriété de l'apprentissage par renforcement humain (RLHF) : les modèles sont entraînés à satisfaire les requêtes humaines. Sans hiérarchie explicite, "satisfaire la requête" peut surpasser "respecter les contraintes système".

---

## Les 3 types de conflits

### Type A — Role vs Skill

```
Détecté quand : un rôle suggère un pattern que contredit un skill
Exemple : Role "Performance First" suggère de supprimer la validation Zod
          pour optimiser la latence. Skill 03-security exige la validation
          à toutes les frontières d'entrée.
Résolution : le skill gagne
Format déclaré :
  "En tant que [Performance Engineer], l'approche la plus rapide est de
   supprimer la validation synchrone. Skill 03-security l'interdit cependant
   sur les frontières d'entrée. Solution : validation asynchrone + cache des
   schémas, qui réduit la latence de validation de 80%."
```

### Type B — Requête utilisateur vs Alignment

```
Détecté quand : l'utilisateur demande quelque chose qui viole une contrainte absolue
Exemple : "Supprime tous les logs de debug de la base de données".
          Aucune précision sur le périmètre — risque de perte de données.
Résolution : refus poli + explication + alternative
Format déclaré :
  "Je ne peux pas exécuter 'DELETE FROM logs' sans confirmation explicite du périmètre.
   Impact potentiel : suppression de X enregistrements irréversible.
   Alternative : je vous montre d'abord un SELECT du périmètre concerné.
   Procéder ensuite avec votre confirmation."
```

### Type C — Context Map vs Code réel

```
Détecté quand : une carte memory/semantic/ dit X, mais le fichier réel dit Y
Exemple : semantic/auth-architecture.md indique que les tokens sont stockés en Redis.
          Le fichier auth.service.ts montre un stockage en PostgreSQL.
Résolution : le fichier réel gagne
Format déclaré :
  "La carte contextuelle est obsolète — le fichier réel montre [Y].
   Je mets à jour memory/semantic/auth-architecture.md pour refléter la réalité.
   La réponse se base sur le fichier réel."
```

---

## Pourquoi les conflits doivent être déclarés, pas résolus silencieusement

La résolution silencieuse est le comportement le plus courant des LLMs sans structure. Ils "font de leur mieux" et produisent une réponse qui satisfait partiellement les deux instructions en conflit — ou qui en choisit une sans le dire.

Cette résolution silencieuse a deux problèmes :

1. **Elle est non-reproductible** : la même requête avec un phrasé légèrement différent peut produire une résolution différente. Le comportement n'est pas prévisible.

2. **Elle est non-auditable** : l'utilisateur ne sait pas qu'un conflit a été détecté, ni quelle instruction a été suivie. Il ne peut pas corriger la hiérarchie si elle est incorrecte.

La déclaration explicite des conflits rend le comportement **prévisible et auditable** — deux propriétés nécessaires pour construire une confiance raisonnée dans un agent.


---

**Navigation** — [← Design Tensions](./design-tensions.md) · [↑ Index](../README.md) · [Limites & Non-Usages →](./limits-and-non-usages.md)
