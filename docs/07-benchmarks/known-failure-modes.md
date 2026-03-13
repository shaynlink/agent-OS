# Known Failure Modes — Modes de défaillance documentés

> Cette page catalogue les façons spécifiques dont le système peut échouer — pas théoriquement, mais basées sur des patterns observés dans des déploiements réels d'agents LLM avec infrastructure contextuelle similaire.

---

## Mode 1 — Context Rot (Pourrissement du contexte)

**Description** : Au fil des sessions, `memory/semantic/` et les Tier 1 Skills s'éloignent progressivement de la réalité du codebase. Le décalage est invisible de l'extérieur — l'agent continue d'opérer, mais base ses réponses sur des informations obsolètes.

**Signal d'alerte** :
- L'agent recommande des patterns que le codebase a abandonnés
- Les chemins de fichiers dans les réponses ne correspondent pas aux fichiers réels
- Les conventions recommandées par les skills contredisent ce que fait le code existant

**Déclencheur typique** : refactoring majeur, migration de framework, changement d'architecture — sans mise à jour correspondante de la mémoire.

**Prévention** : Intégrer la mise à jour de `memory/` dans le Definition of Done des tâches d'architecture significatives. Audit mensuel de fraîcheur.

**Détection au runtime** : Type C conflict (Context Map vs Real Code). L'agent doit déclarer la discordance quand il lit un fichier qui contredit sa mémoire.

---

## Mode 2 — Skill Conflict Accumulation

**Description** : Avec le temps, les Tier 1 Skills peuvent accumuler des règles qui entrent en contradiction silencieuse. Chaque règle était cohérente au moment de son ajout — mais leur combinaison crée des tensions non documentées.

**Exemple** :
```
Skill 03-security.md : "Valider tous les inputs Zod à la frontière avant tout traitement"
Skill 05-api.md : "Les handlers REST doivent être < 20 lignes — déléguer à la couche service"
```

Ces deux règles ne se contredisent pas dans l'absolu, mais créent une tension sur l'emplacement de la validation Zod — dans le handler (limite les lignes) ou dans le service (sort de la frontière).

**Signal d'alerte** : l'agent déclare fréquemment des Type A conflicts (Role vs Skill) sur des combinaisons similaires.

**Prévention** : Lors de l'ajout d'une règle dans un skill, scanner les autres skills pour des tensions potentielles. Documenter les interactions connues.

---

## Mode 3 — Role Explosion

**Description** : Trop de rôles définis, avec des périmètres qui se chevauchent. L'orchestrateur active plusieurs rôles sur la même tâche, le Role Deliberation Protocol génère des tensions artificielles, et la synthèse est compromis.

**Signal d'alerte** : les réponses commencent systématiquement par des sections de délibération longues sur des tâches simples.

**Prévention** : Maximum 6-8 rôles. Chaque rôle doit avoir un périmètre clair et non-chevauchant avec les autres. Si deux rôles produisent des analyses similaires, les fusionner.

---

## Mode 4 — Memory Bloat (Gonflement de la mémoire)

**Description** : `memory/episodic/` et `memory/procedural/` accumulent indéfiniment — sans archivage des entrées obsolètes, sans consolidation des patterns redondants. Le fichier devient trop volumineux pour être chargé sélectivement.

**Signal d'alerte** : `known-pitfalls.md` dépasse 500 lignes. L'orchestrateur charge le fichier en entier, consommant >10k tokens de budget contextuel.

**Prévention** :
- Audit semestriel de la mémoire — archiver les entrées > 6 mois non référencées
- Consolider les patterns similaires (confidence > 0.9 sur 3+ observations → skill Tier 2)
- Séparer `known-pitfalls.md` en domaines si le fichier dépasse 200 lignes

---

## Mode 5 — Signal Table Drift

**Description** : La Task Detection Table dans `AGENTS.md` ne couvre plus les patterns de requêtes actuels. L'orchestrateur rate des signaux pertinents, charge les mauvaises ressources, ou ne charge rien.

**Signal d'alerte** :
- L'agent charge Tier 1 Skills génériques pour des tâches qui devraient charger une section sémantique spécifique
- Les réponses manquent de contexte architectural sur des domaines pourtant documentés en `memory/semantic/`

**Prévention** : Revoir la Task Detection Table après chaque sprint qui ajoute un nouveau domaine fonctionnel.

---

## Mode 6 — Kernel Drift (par fork)

**Description** : Quand un projet forke le boilerplate et modifie les fichiers kernel, le fork s'éloigne progressivement du kernel upstream. Les mises à jour du kernel upstream ne peuvent plus être intégrées sans conflits.

**Signal d'alerte** : `alignment.md` ou `orchestrator.md` contiennent des sections projet-spécifiques.

**Prévention** : Toute modification projet-spécifique appartient aux layers adaptables. Si le kernel semble insuffisant, c'est le signal qu'un skill ou un rôle manque — pas que le kernel doit changer.

**Récupération** : Identifier les modifications projet-spécifiques dans le kernel, les migrer vers les layers appropriés, et réinitialiser le kernel depuis l'upstream.

---

## Mode 7 — False Confidence Loop

**Description** : L'agent génère une réponse incorrecte, la déclare CERTAIN (parce qu'il a "vu" l'information dans un contexte lié), et l'utilisateur intègre cette réponse dans la mémoire. À la session suivante, l'agent charge cette information mémoire, renforce sa confiance, et l'erreur se perpétue.

**Ce qui rend ce mode particulièrement dangereux** : il est auto-renforçant. Chaque session qui utilise l'information incorrecte en renforce la présence dans la mémoire, rendant la correction de plus en plus difficile.

**Prévention** : La curation humaine obligatoire de toute entrée mémoire est la seule protection efficace. Un humain qui valide une entrée mémoire doit être capable de vérifier l'information contre le codebase réel — pas seulement de juger si la formulation est plausible.

---

## Tableau de synthèse

| Mode | Détectabilité | Sévérité | Prévention principale |
|---|---|---|---|
| Context Rot | Faible (progressive) | Haute | Mise à jour dans le DoD |
| Skill Conflict | Moyenne | Moyenne | Scan inter-skills à l'ajout |
| Role Explosion | Haute | Faible | Limite 6-8 rôles max |
| Memory Bloat | Haute (taille fichier) | Moyenne | Audit semestriel |
| Signal Table Drift | Moyenne | Moyenne | Mise à jour après chaque sprint |
| Kernel Drift | Haute | Haute | Zero modification kernel |
| False Confidence Loop | Très faible | Très haute | Curation humaine obligatoire |


---

**Navigation** — [← Études de Performance](./performance-studies.md) · [↑ Index](../README.md) · [Problèmes & Dangers →](./problems-and-dangers.md)
