# Session Flow — Flux complet d'une session

> Du prompt à la réponse : ce qui se passe à chaque étape, pourquoi, et ce qui garantit la qualité du résultat.

---

## Vue d'ensemble

```mermaid
flowchart TD
    A([Prompt utilisateur]) --> BOOT

    subgraph BOOT ["PHASE 1 — BOOT"]
        B1["00-reasoning.md\n→ Activation du protocole\nde raisonnement structuré"]
        B2["AGENTS.md\n→ Identité projet,\nstack, repository map"]
        B3["alignment.md\n→ Contraintes actives,\nhiérarchie de vérité"]
        B4["orchestrator.md\n→ Moteur de routage\ncontextuel prêt"]
        B5["auto-learning.md\n→ Protocole d'apprentissage\nactif"]
        B6["11-agent-behavior.md\n→ Comportement et\ncommunication"]
        B1 --> B2 --> B3 --> B4 --> B5 --> B6
    end

    subgraph DETECT ["PHASE 2 — DÉTECTION"]
        C1["Analyse des signaux\ndans le prompt"]
        C2{"Signaux\ndétectés"}
        C1 --> C2
        C2 -->|schema, route, auth...| C3["Tier 1 Skills\ncorrespondants"]
        C2 -->|signal domaine spéc.| C4["Tier 2 Skill\nciblé"]
        C2 -->|expertise req.| C5["Role\nactivé"]
        C2 -->|entity mentionnée| C6["Section semantic/\nciblée"]
    end

    subgraph DELIB ["PHASE 3 — DÉLIBÉRATION"]
        D1{"Nombre\nde rôles ?"}
        D2["Analyse\nindépendante\npar rôle"]
        D3["Détection\ndes tensions\nexplicite"]
        D4["Synthèse\nconvergente"]
        D1 -->|rôle unique| GATE
        D1 -->|2+ rôles| D2 --> D3 --> D4
    end

    subgraph GATE ["PHASE 4 — SELF-CONSISTENCY GATE"]
        E1["Check 1 : cohérence\ninterne de la réponse"]
        E2["Check 2 : cross-référence\navec fichiers réels"]
        E3["Check 3 : niveau\nde confiance déclaré"]
        E1 --> E2 --> E3
    end

    subgraph EXEC ["PHASE 5 — EXÉCUTION"]
        F1["Implémentation avec\nréférence skill explicite"]
        F2["📚 Skill NN — Nom appliqué"]
        F1 --> F2
    end

    subgraph LEARN ["PHASE 6 — APPRENTISSAGE"]
        G1{"Type\nd'événement ?"}
        G2["Error Signal\n→ memory/episodic/"]
        G3["Success Signal\n→ memory/procedural/"]
        G4["New Structure\n→ memory/semantic/"]
        G1 --> G2 & G3 & G4
    end

    BOOT --> DETECT
    C3 & C4 & C5 & C6 --> DELIB
    D4 --> GATE
    GATE --> EXEC
    EXEC --> LEARN
    LEARN --> Z([Réponse livrée])
```

---

## Phase 1 — Boot

La séquence de boot est **déterministe et non-interruptible**. L'ordre exact est prescrit et justifié dans [boot-sequence.md](./boot-sequence.md). Aucun élément des layers dynamiques n'est chargé avant la fin du boot.

---

## Phase 2 — Détection

L'orchestrateur scanne le prompt pour des **signaux** — mots-clés ou patterns qui indiquent le domaine de la tâche. Ces signaux sont mappés dans la Task Detection Table de `AGENTS.md`.

La détection produit une liste de ressources à charger. La règle de priorité est :
```
section ciblée > fichier complet > résumé d'index
```

Un signal absent de la table = ressource non chargée. Ce n'est pas une erreur — c'est le comportement attendu. Le modèle opère avec les skills génériques du kernel.

---

## Phase 3 — Délibération

Activée uniquement si 2+ rôles sont nécessaires simultanément. Le protocole MoA (Wang 2024) garantit que les tensions entre perspectives sont **surfacées explicitement** plutôt que résolues silencieusement par le modèle.

Format de délibération :
```markdown
[role-a]: "From [domain A], le problème est X, le risque est Y"
[role-b]: "From [domain B], la contrainte est Z"
⚠️ TENSION : [role-a] suggère A, [role-b] requiert B
Résolution : B gagne — Security > Performance (alignment.md)
Synthèse : [implémentation qui satisfait les deux contraintes]
```

---

## Phase 4 — Self-Consistency Gate

Avant toute finalisation, 3 vérifications mandatory :

**Check 1 — Cohérence interne**
- La réponse se contredit-elle ?
- Les types TypeScript sont-ils cohérents entre les fichiers ?
- Les imports référencent-ils des exports qui existent ?

**Check 2 — Cross-référence**
- Chaque assertion sur le code a-t-elle été vérifiée dans un fichier réel ?
- Le pattern proposé est-il cohérent avec les patterns existants dans `memory/semantic/` ?

**Check 3 — Confiance déclarée**
```
CERTAIN  → vu dans un fichier réel
PROBABLE → inféré de la structure, non vérifié
INCERTAIN → non vérifié — recommander la vérification explicite
```

---

## Phase 5 — Exécution

L'implémentation doit référencer explicitement le skill appliqué : `📚 Skill NN — Nom`. Si aucun skill n'est applicable, déclarer : `🔴 SKILL GAP — aucun skill couvre ce cas. Créer .agent/rules/tier-1/XX-[domaine].md`.

---

## Phase 6 — Apprentissage

Après chaque session significative, le protocole Verbal RL (Shinn 2023) décide quoi capturer en mémoire. Le scoring de confiance détermine le type d'entrée :

```
Confidence < 0.6  → NOTE (provisional)
Confidence 0.6–0.8 → PATTERN (avec tag de prudence)
Confidence > 0.8  → RULE (intégrée comme règle ferme)
```

La règle critique : **une observation unique ne devient jamais une RULE directement**. Même avec une confiance de 0.9, si elle n'a été observée qu'une fois, elle reste PATTERN.


---

**Navigation** — [← Context Layers](./context-layers.md) · [↑ Index](../README.md) · [Context Budget →](./context-budget.md)
