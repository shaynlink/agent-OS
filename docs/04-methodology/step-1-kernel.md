# Étape 1 — Copier le Kernel

> **Objectif** : Ancrer le système dans votre projet. Cette étape est mécanique — copier, vérifier, ne pas modifier.

---

## Ce que vous faites

```bash
# Depuis le répertoire agent-boilerplate
cp -r .agent/ /votre-projet/.agent/
cp AGENTS.md /votre-projet/AGENTS.md
```

---

## Les 5 fichiers kernel à vérifier

| Fichier | Chemin | Statut requis |
|---|---|---|
| Reasoning | `.agent/rules/tier-0/00-reasoning.md` | Présent, non modifié |
| Alignment | `.agent/system/alignment.md` | Présent, non modifié |
| Orchestrator | `.agent/system/orchestrator.md` | Présent, non modifié |
| Auto-Learning | `.agent/system/auto-learning.md` | Présent, non modifié |
| Agent Behavior | `.agent/rules/tier-0/11-agent-behavior.md` | Présent, non modifié |

---

## Structure attendue après l'étape 1

```
votre-projet/
  AGENTS.md                    ← À configurer à l'étape 2
  .agent/
    system/
      alignment.md             ← KERNEL — NE PAS TOUCHER
      orchestrator.md          ← KERNEL — NE PAS TOUCHER
      auto-learning.md         ← KERNEL — NE PAS TOUCHER
    rules/
      tier-0/
        00-reasoning.md        ← KERNEL — NE PAS TOUCHER
        11-agent-behavior.md   ← KERNEL — NE PAS TOUCHER
      tier-1/                  ← Vide pour l'instant (étape 3)
      tier-2/                  ← Vide pour l'instant
    roles/                     ← Vide pour l'instant (étape 5)
    memory/
      working/
      episodic/
      semantic/                ← Vide pour l'instant (étape 4)
      procedural/
```

---

## ✅ Checkpoint

- [ ] Les 5 fichiers kernel sont présents dans les chemins attendus
- [ ] Aucun de ces fichiers n'a été modifié (vérifier avec `git diff` si le projet est sous contrôle de version)
- [ ] La structure `.agent/memory/` est créée avec ses 4 sous-dossiers

> [!CAUTION]
> Si vous avez modifié `alignment.md` ou `orchestrator.md` pour "adapter" quelque chose — revenez en arrière. Toute adaptation projet-spécifique passe par les layers (étapes 3-6). Modifier le kernel invalide ses garanties.

---

**Navigation** — [← 04 — Méthodologie](./README.md) · [↑ Index](../README.md) · [Étape 2 — AGENTS.md →](./step-2-agents-md.md)
