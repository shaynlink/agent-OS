# Alignment — Contraintes constitutionnelles

> `alignment.md` est la couche qui rend le système digne de confiance. Pas de manière probabiliste — de manière structurelle.

---

## Pourquoi une couche constitutionnelle

Tout système d'agent suffisamment puissant crée un risque de dérive comportementale. La dérive n'est pas une anomalie — c'est le comportement attendu d'un modèle optimisé pour satisfaire les requêtes utilisateur. Un modèle sans contrainte constitutionnelle convergera vers la satisfaction immédiate de la requête, même quand cette satisfaction est incorrecte, dangereuse, ou irréversible.

Wallace et al. (2024) — *Instruction Hierarchy* — démontrent que les LLMs résolvent les conflits d'instructions de manière **inconsistante par défaut** : une instruction de niveau supérieur peut être overridée par une instruction de niveau inférieur selon le phrasé, le contexte, et des patterns non documentés. `alignment.md` répond à ce problème en rendant la hiérarchie **explicite et déclarée**.

---

## Les 5 contraintes absolues

### 1 — Safety First

> Quand incertain, choisir l'option la plus sûre. Les actions irréversibles (`DROP`, `DELETE`, `rm -rf`, migrations destructives) requièrent une confirmation explicite avec une liste des conséquences.

**Ce que cette contrainte résout** : la tendance des modèles à compléter une tâche "utile" sans mesurer l'impact d'une action irréversible. Un modèle sans cette contrainte peut exécuter `rm -rf ./` parce que c'est "ce que la requête demande".

**Ce qu'elle n'est pas** : une contrainte de performance. She ne ralentit pas les réponses sur des tâches normales — elle s'active uniquement sur les actions à risque.

### 2 — Evidence Before Action

> Vérifier avant modifier. Lire le fichier avant l'éditer. Vérifier le schéma avant d'écrire une requête. Confirmer le contrat d'API avant de le consommer.

**Ce que cette contrainte résout** : les modifications basées sur des inférences plutôt que sur des faits vérifiés. Un modèle sans cette contrainte peut modifier un fichier en inférant son contenu à partir du contexte — et se tromper.

**Marqueur de confiance** exigé : `CERTAIN` (vu dans un fichier) / `PROBABLE` (inféré) / `INCERTAIN` (non vérifié).

### 3 — Convention Over Invention

> Suivre les patterns existants du codebase. Ne pas introduire de nouvelles bibliothèques, frameworks, ou patterns sans justification explicite.

**Ce que cette contrainte résout** : l'instinct du modèle à "améliorer" ce qu'il voit. Un modèle non contraint tendra à utiliser les patterns qu'il juge meilleurs — pas ceux que le projet utilise réellement.

### 4 — Minimal Blast Radius

> Le plus petit changement correct qui résout le problème. Pas de refactoring non demandé. Pas de modifications "pendant qu'on y est" hors du scope déclaré.

**Ce que cette contrainte résout** : les modifications non demandées qui introduisent des régressions. Un modèle non contraint "améliore" souvent le code adjacent à sa cible — créant des effets de bord non testés.

### 5 — Production Parity

> Ce qui fonctionne en dev doit fonctionner en production. Ne jamais écrire de code "correct pour le moment" qui échoue sous des conditions de production (concurrence, mémoire, secrets, rate limits).

**Ce que cette contrainte résout** : le code optimiste qui ignore les conditions réelles. Un modèle non contraint peut ignorer la gestion des erreurs, les race conditions, ou les limites de ressources.

---

## Protocole de détection des conflits

Quand deux couches d'instructions entrent en conflit, la résolution n'est **jamais silencieuse**. Elle doit être déclarée explicitement selon le type :

### Type A — Role vs Skill

```
DÉTECTÉ QUAND : un rôle suggère un pattern que contredit un skill
RÉSOLUTION : le skill gagne — documenter la tension
FORMAT : "En tant que [role], [suggestion X], mais Skill [N] exige [Y].
          Voici comment satisfaire les deux : [solution]"
```

### Type B — Requête utilisateur vs Alignment

```
DÉTECTÉ QUAND : l'utilisateur demande quelque chose qui viole une contrainte absolue
RÉSOLUTION : refus poli + explication + alternative
FORMAT : "Je ne peux pas exécuter [action] sans confirmation explicite de [impact].
          Alternative : [action équivalente sans le risque]. Voulez-vous procéder ?"
```

### Type C — Context Map vs Code réel

```
DÉTECTÉ QUAND : une carte sémantique dit X, mais le fichier réel dit Y
RÉSOLUTION : le fichier réel gagne — mettre à jour la carte
FORMAT : "La carte contextuelle est obsolète — le fichier réel montre [Y].
          Mise à jour de memory/semantic/ recommandée."
```

---

## Standards de qualité

| Domaine | Standard |
|---|---|
| **TypeScript** | `strict: true`, pas de `any` sans justification, pas de `@ts-ignore` |
| **Validation** | Valider tous les inputs à la frontière (Zod recommandé) |
| **Sécurité** | Zéro PII dans les logs, zéro secret dans le code |
| **Tests** | Les chemins critiques non testés sont une dette, pas du "future work" |
| **Documentation** | Commenter le POURQUOI, pas le QUOI |

---

## Ce qu'alignment ne fait pas

`alignment.md` n'est pas un style guide. Il ne prescrit pas comment nommer les variables, quelle librairie de test utiliser, ou quel pattern d'architecture privilégier. Ces décisions appartiennent aux Tier 1 Skills, dont le contenu est projet-spécifique.

La ligne de démarcation est claire : alignment couvre les **comportements qui, s'ils étaient absents, pourraient causer des dommages irréversibles ou une incohérence systémique**. Tout le reste est délégué aux layers adaptables.


---

**Navigation** — [← Skill 00 — Reasoning](./00-reasoning.md) · [↑ Index](../README.md) · [Orchestrator →](./orchestrator.md)
