# memory/working/ — État de session

> La mémoire de travail est la seule partie du système qui est **volatile par conception**. Elle capture l'état en cours d'une session active, et est invalidée à la fin de cette session.

---

## Nature et rôle

La mémoire de travail répond à un besoin spécifique des sessions longues ou complexes : maintenir un état de tâche accessible sans avoir à tout recharger depuis le début à chaque échange.

Pour une session de refactoring sur plusieurs modules, par exemple :
- Quels modules ont été modifiés
- Quelles décisions architecture ont été prises et pourquoi
- Quels problèmes sont en attente de résolution
- Quel est l'état actuel de la tâche

Sans mémoire de travail, un agent dans une longue session doit inférer son état de contexte depuis l'historique de conversation — qui peut lui-même être tronqué si la context window est saturée.

---

## Format recommandé

```markdown
<!-- working/session-[date].md -->
<!-- SESSION: [description de la tâche] -->
<!-- Started: YYYY-MM-DD HH:MM -->

## Contexte de la session
[Objectif, scope, contraintes de la session]

## État actuel
[Où en est-on — checkpoint de progression]

## Décisions prises
- [Décision 1] — Raison : [...]
- [Décision 2] — Raison : [...]

## En attente
- [ ] [Action à compléter]
- [ ] [Question à résoudre]

## Fichiers modifiés
- `path/to/file.ts` — [ce qui a changé et pourquoi]
```

---

## Différence avec les autres mémoires

| Type | Durée de vie | Contenu | Mise à jour |
|---|---|---|---|
| `working/` | Session — volatile | État de la tâche en cours | Continu pendant la session |
| `episodic/` | Permanente | Bugs et leçons capitalisées | Après événement significatif |
| `procedural/` | Permanente | Patterns éprouvés | Sur promotion (confidence > 0.9) |
| `semantic/` | Permanente | Cartes de connaissance | Après changement architectural |

---

## Politique de cycle de vie

**Création** : au début d'une session complexe > 1 heure, ou quand la tâche implique plusieurs fichiers interdépendants.

**Mise à jour** : en continu pendant la session — après chaque sous-tâche complétée.

**Archivage** : à la fin de la session, décider :
- Si la session a produit des leçons → migrer vers `episodic/`
- Si la session a validé un pattern → migrer vers `procedural/`
- Si la session a modifié l'architecture → mettre à jour `semantic/`
- Si rien de notable → supprimer le fichier de travail

**Purge** : les fichiers `working/` de plus de 7 jours sans activité peuvent être supprimés.

---

## Quand ne pas utiliser working/

Pour les sessions courtes (< 30 min) ou les tâches simples (1-2 fichiers, objectif unique), la mémoire de travail ajoute de la friction sans apport. L'historique de conversation suffit comme état.

La heuristique : créer un fichier `working/` si vous ressentez le besoin de "où en était-on ?" après une interruption de 30 minutes.

---

**Navigation** — [← memory/procedural/](./memory-procedural.md) · [↑ Index](../README.md) · [04 — Méthodologie →](../04-methodology/README.md)
