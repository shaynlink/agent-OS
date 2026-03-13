# Attention & Contexte

> Comment les LLMs utilisent (et gaspillent) leur contexte. Les deux papers de cette section justifient le principe central de l'Agent OS : **charger moins, mais mieux ciblé**.

---

## Liu et al. (2023) — Lost in the Middle

**Référence complète** : Liu, N., Lin, K., Hewitt, J., Paranjape, A., Hopkins, M., Beigi, H., & Liang, P. (2023). *Lost in the Middle: How Language Models Use Long Contexts*. Transactions of the ACL.

### Ce que l'étude mesure

Performance de plusieurs LLMs (GPT-3.5-Turbo, GPT-4, Claude 1.3, Longchat-13b) sur des tâches de question-answering multi-document. La variable manipulée : la **position** du document pertinent dans le contexte — début, milieu, ou fin.

### Résultats

```
Position début (primacy)   → ~80% accuracy
Position milieu            → ~55% accuracy  (−25 points)
Position fin (recency)     → ~75% accuracy
```

La courbe en U est observée sur **tous les modèles testés**, avec des amplitudes différentes. Les modèles plus récents et plus larges atténuent l'effet, mais ne l'éliminent pas.

**Résultat nuancé** : la dégradation est plus prononcée sur des contextes de 4k+ tokens. Pour des contextes < 2k tokens, l'effet position est moins significatif.

### Traduction dans l'Agent OS

**Décision 1** : `00-reasoning.md` en position 1 (premier token).
Le protocole de raisonnement structuré est le composant le plus important du kernel. Le placer en position primaire maximise son influence sur chaque réponse.

**Décision 2** : Le kernel est chargé **avant** les layers adaptables.
Les 5 fichiers kernel (contraintes absolues, identité projet) bénéficient de la primauté — les skills et rôles chargés ensuite s'inscrivent dans ce cadre déjà établi.

**Décision 3** : Le chargement sélectif est une nécessité, pas une optimisation.
Charger 100k tokens de contexte pour diluer les 5k tokens critiques au milieu = dégradation mesurable et prévisible. Sans sélection, le système sabote lui-même ses composants clés.

### Limites de l'application

L'étude porte sur des tâches de retrieval factuel. Les tâches de génération de code et de raisonnement complexe ont des courbes de performance différentes — la conclusion directionnelle reste robuste, mais les magnitudes exactes varient.

---

## Jiang et al. (2023) — LLMLingua

**Référence complète** : Jiang, H., Wu, Q., Lin, C.-Y., Yang, Y., & Lü, L. (2023). *LLMLingua: Compressing Prompts for Accelerated Inference of Large Language Models*. EMNLP 2023.

### Ce que l'étude mesure

Impact de la **compression sélective** du contexte sur la performance. LLMLingua utilise un petit modèle pour scorer l'importance de chaque token et supprimer les tokens à faible importance informationnelle.

### Résultats

| Taux de compression | Retention de performance |
|---|---|
| 2x (50% de tokens supprimés) | ~98% — quasi-transparente |
| 4x (75% supprimés) | ~92% — légère dégradation |
| 10x (90% supprimés) | ~83% |
| 20x (95% supprimés) | ~72% |

**Conclusion centrale** : supprimer du contexte non-pertinent peut **améliorer** la performance jusqu'à un seuil. Le modèle "se concentre" mieux sur l'information restante.

### Traduction dans l'Agent OS

**Décision** : Le chargement sélectif par signal (Task Detection Table → Section ciblée dans semantic/) est fondé sur ce principe.

La règle de chargement `targeted section > full file > index summary` découle directement de LLMLingua : charger uniquement ce qui est pertinent à la tâche, pas le fichier entier par précaution.

**Application concrète** :
```
Requête : "modifier le PaymentService"
→ Charger <section id="payment-service"> (≈ 500 tokens)
→ PAS semantic/services.md entier (≈ 5000 tokens)
Gain : 90% de budget contextuel préservé, performance maintenue ou améliorée
```

### Ce que ça implique pour la conception des fichiers

Chaque fichier `memory/semantic/` doit être découpé en sections précises (`<section id="">`). Un fichier sans sections ne peut pas être chargé sélectivement — l'orchestrateur doit le charger en entier, ce qui augmente le bruit contextuel.

---

**Navigation** — [← 06 — Science](./README.md) · [↑ Index](../README.md) · [Apprentissage & Auto-amélioration →](./learning-improvement.md)
