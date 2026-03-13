# 02 — Kernel

> Le kernel est la partie du système qui ne change pas — indépendamment du projet, du modèle, de l'équipe. C'est ce qui rend le comportement agent **prévisible et auditable**.

---

## Pourquoi un kernel immuable

L'immutabilité du kernel n'est pas une contrainte technique — c'est une décision philosophique.

Un système dont toutes les parties peuvent être modifiées n'offre aucune garantie. Si `alignment.md` peut être édité, "ne jamais exécuter une action irréversible sans confirmation" devient une suggestion plutôt qu'une garantie. Si `00-reasoning.md` peut être supprimé, la transparence du raisonnement devient optionnelle.

Le kernel résout ce problème par l'invariance : **5 fichiers, jamais modifiés, que les layers adaptables doivent respecter sans exception**. Tout ce qui doit être adapté au projet passe par les layers adaptables — ce qui laisse au kernel son rôle de garant universel.

---

## Les 5 composants

| Fichier | Page | Rôle |
|---|---|---|
| `rules/tier-0/00-reasoning.md` | [00-reasoning.md](./00-reasoning.md) | Protocole de raisonnement — premier token |
| `system/alignment.md` | [alignment.md](./alignment.md) | Contraintes constitutionnelles |
| `system/orchestrator.md` | [orchestrator.md](./orchestrator.md) | Routage contextuel sélectif |
| `system/auto-learning.md` | [auto-learning.md](./auto-learning.md) | Apprentissage inter-session |
| `rules/tier-0/11-agent-behavior.md` | [11-agent-behavior.md](./11-agent-behavior.md) | Comportement et communication |

---

## Ce que le kernel garantit collectivement

1. **Transparence** : chaque décision expose son raisonnement (`00-reasoning.md`)  
2. **Sécurité** : certaines actions ne seront jamais exécutées sans confirmation (`alignment.md`)  
3. **Pertinence** : seule l'information nécessaire est chargée, au bon moment (`orchestrator.md`)  
4. **Apprentissage** : les événements significatifs sont capturés et classifiés (`auto-learning.md`)  
5. **Cohérence** : le format de réponse est structuré et reproductible (`11-agent-behavior.md`)  

Ces garanties sont **cumulatives** — elles ne s'annulent pas mutuellement. Un rôle agressif de performance doit quand même respecter alignment. Une session avec contexte réduit maintient quand même le protocole de raisonnement.

---

## Ce que le kernel n'est pas

Le kernel n'est **pas une configuration par défaut** que les layers adaptables viennent surcharger. C'est une couche de contraintes que les layers adaptables doivent **respecter**, pas contourner.

La hiérarchie est stricte :
```
alignment.md > tout skill > tout rôle > tout prompt utilisateur
```

Aucune entrée dans un Tier 1 Skill ne peut autoriser ce qu'alignment interdit. Aucun rôle ne peut override une contrainte de sécurité. Ce n'est pas une convention — c'est la définition même du kernel.


---

**Navigation** — [← Context Budget](../01-system/context-budget.md) · [↑ Index](../README.md) · [Skill 00 — Reasoning →](./00-reasoning.md)
