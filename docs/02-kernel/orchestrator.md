# Orchestrator — Routage contextuel sélectif

> L'orchestrateur est le mécanisme qui rend le système efficace plutôt que simplement chargé. Il répond à la question : que charger, quand, et dans quel ordre ?

---

## Le problème du chargement indiscriminé

Charger tout le contexte disponible pour chaque requête est une stratégie intuitive mais contre-productive. Jiang et al. (2023) — *LLMLingua* — quantifient l'impact : compresser un contexte de manière sélective ameliore la performance sur des benchmarks de raisonnement, même quand les informations supprimées semblent potentiellement utiles.

La raison : l'attention des transformers est une ressource finie. Chaque token de contexte non pertinent compète pour cette ressource avec les tokens pertinents. Dans un contexte de 100k tokens dont 90k ne concernent pas la tâche en cours, les 10k pertinents sont structurellement désavantagés.

L'orchestrateur résout ce problème par **sélection a priori** : charger uniquement ce qui est nécessaire, au moment où c'est nécessaire, dans la quantité minimale suffisante.

---

## Phase 1 — Détection de signaux

L'orchestrateur analyse le prompt pour des **signaux** — patterns lexicaux qui indiquent le domaine de la tâche. Ces signaux sont définis dans la Task Detection Table de `AGENTS.md`.

```
Requête : "Ajouter un endpoint pour récupérer les commandes d'un utilisateur"

Signal détecté : "endpoint", "route"
→ Domain : API
→ Tier 1 Skills à charger : 05-api-standards, 03-security
→ Context Map : semantic/api-modules.md#orders-module
→ Rôle à activer : backend-engineer
```

**Règle de sélection** :
```
targeted section > full file > index summary > rien
```

Un signal absent de la table = ressource non chargée. Le système opère avec le kernel seul. C'est acceptable.

---

## Phase 2 — Tier Routing

```
TIER 0 — TOUJOURS chargé (premier)
  → 00-reasoning.md + 11-agent-behavior.md

TIER 1 — Chargé si signal technique détecté
  → tous les fichiers .agent/rules/tier-1/*.md

TIER 2 — Chargé on-demand, un skill ciblé
  → .agent/rules/tier-2/[skill-spécifique].md

RULE : Skill 00 est en position 1. Jamais décalé.
```

**Pourquoi Tier 1 est chargé en entier** (contrairement à Tier 2) : les standards techniques du projet sont interdépendants. Un check de type TypeScript implique les règles de sécurité, qui impliquent les conventions d'API. Séparer artificielle ces dépendances augmente le risque de violation inter-skill.

**Pourquoi Tier 2 est chargé à l'unité** : les skills de Tier 2 sont par définition spécialisés et rarement nécessaires simultanément. Charger tous les Tier 2 pour chaque requête technique consommerait 15-30k tokens de contexte supplémentaires pour des informations non pertinentes à la tâche.

---

## Phase 3 — Chargement sectionnel de la mémoire sémantique

Le format retrieval-aware (`<!-- INDEX --> + <section id="">`) permet à l'orchestrateur de charger uniquement la section pertinente :

```
Signal : "modifier PaymentService"
→ Charger <section id="payment-service"> depuis semantic/services.md
→ PAS le fichier semantic/services.md entier

Signal : "créer un nouveau service de notification"
→ Charger le fichier semantic/services.md entier
  (création implique connaissance du contexte complet)
```

---

## Phase 4 — Role Deliberation Protocol

Activé quand 2+ rôles sont nécessaires simultanément. Source : Wang et al. (2024) — *MoA* (Mixture of Agents).

```markdown
### Analyse indépendante
[backend-engineer]: "De mon point de vue architecture, la solution est X.
                    Le risque d'une approche différente est Y."
[security-reviewer]: "De mon point de vue sécurité, la contrainte est Z.
                     L'approche X crée une surface d'attaque sur A."

### Détection de tension
⚠️ TENSION : [backend-engineer] optimise la latence avec bulk queries,
              [security-reviewer] exige une validation par record.
Résolution : Security > Performance (alignment.md §1)
Plan : pagination avec validation unitaire, cache Redis en compensatoire.

### Synthèse
[implémentation qui respecte les deux contraintes]
```

La valeur de ce protocole est que les tensions sont **surfacées avant** l'implémentation plutôt que découvertes lors d'une revue de code.

---

## Phase 5 — Self-Consistency Gate

Source : Wang et al. (2022) — *Self-Consistency*. Avant de finaliser toute réponse contenant du code ou des décisions architecturales :

### Check 1 — Cohérence interne
- [ ] La réponse se contredit-elle ?
- [ ] Les types TypeScript sont-ils cohérents entre les fichiers ?
- [ ] Les imports référencent-ils des exports qui existent réellement ?

### Check 2 — Cross-référence
- [ ] Chaque assertion "le service X fait Y" a-t-elle été vérifiée dans un fichier ?
- [ ] Le pattern proposé est-il cohérent avec les patterns dans `memory/semantic/` ?
- [ ] Si un test est référencé, existe-t-il réellement ?

### Check 3 — Confiance déclarée
```
CERTAIN  (vu dans un fichier)           → affirmer
PROBABLE (inféré de la structure)       → "probablement — à vérifier"
INCERTAIN (non vérifié)                 → "je suggère de vérifier"
```

---

## Skill Compliance Engine

Après chaque changement de code, l'agent **doit** :
1. Référencer au moins un skill dans la réponse : `📚 Skill NN — Nom`
2. Appliquer les standards du skill **avant** d'implémenter
3. Si aucun skill ne couvre le cas : `🔴 SKILL GAP — déclarer et créer le skill manquant`

Ce mécanisme garantit que les standards ne sont pas seulement chargés, mais **activement appliqués et traçables**.


---

**Navigation** — [← Alignment](./alignment.md) · [↑ Index](../README.md) · [Auto-Learning →](./auto-learning.md)
