# Tier 2 Skills — Domain-Specific Skills

> Les Tier 2 Skills encodent des connaissances hautement spécialisées qui s'appliquent à une minorité des tâches. Leur spécificité est leur valeur — et la raison pour laquelle ils ne doivent pas être chargés systématiquement.

---

## Différence fondamentale avec Tier 1

| | Tier 1 | Tier 2 |
|---|---|---|
| Chargement | Systématique sur toute tâche technique | On-demand via signal dans la requête |
| Scope | ≥ 60% des tâches | < 30% des tâches |
| Taille | ≤ 500 tokens | Pas de limite stricte |
| Contenu | Standards universels du projet | Expertise domaine-spécifique |

Un skill Tier 1 mal placé (trop spécialisé) gaspille du budget contextuel sur toutes les sessions qui n'en ont pas besoin. Un skill Tier 2 bien ciblé ajoute de la précision sur les sessions qui le nécessitent, sans coût sur les autres.

---

## Quand créer un Tier 2 Skill

**Signal positif** : une intégration externe, une bibliothèque critique, ou un pattern complexe revient suffisamment souvent pour justifier d'être documenté — mais pas assez souvent pour être en Tier 1.

**Signal négatif** : un skill Tier 1 dépasse 500 tokens parce qu'il couvre trop de cas. Fragmenter : les règles universelles restent en Tier 1, les cas spécialisés passent en Tier 2.

**Exemples de bons candidats Tier 2** :
- `stripe-integration.md` — webhook signature, idempotency keys, PaymentIntent lifecycle
- `redis-caching.md` — patterns de cache, TTL, invalidation, race conditions
- `auth-flows.md` — flows OAuth, PKCE, passkeys, refresh token rotation
- `file-upload.md` — multipart, chunking, S3 presigned URLs, virus scanning
- `background-jobs.md` — queues, retry policies, dead-letter, concurrence

---

## Format

```markdown
# Skill [Nom] — [Domaine]

> [Une phrase : ce que ce skill couvre et garantit]

## [Section 1]
[Règles concrètes et vérifiables pour ce domaine]

## [Section 2 — Pièges connus]
- ⚠️ [Anti-pattern à éviter avec explication]
- ⚠️ [...]

## [Section 3 — Références]
- [Lien documentation officielle]
- [Lien vers section semantic/ correspondante]

<!-- Source: [référence] -->
<!-- Updated: YYYY-MM-DD -->
```

La section **"Pièges connus"** est recommandée — les Tier 2 Skills couvrent des intégrations où les erreurs non-évidentes sont fréquentes.

---

## ✅ Bon Tier 2 Skill

```markdown
# Skill Stripe — Payment Integration

> Toute interaction avec l'API Stripe respecte les contrats d'idempotence, de gestion d'erreurs,
> et de sécurité webhook définis ici.

## Idempotency
- Utiliser `Idempotency-Key: order-{orderId}` sur tous les appels de création de charge
- Ne jamais réutiliser une clé pour un montant différent — Stripe la rejette silencieusement
- Les PaymentIntents sont idempotents sur `amount` + `currency` + `customer`

## Webhooks
- Toujours vérifier la signature : `stripe.webhooks.constructEvent(body, sig, secret)`
- Utiliser `rawBody` (Buffer), pas le body parsé — la signature est sur le payload brut
- Idempotence : logger l'event ID dans Redis avant traitement, rejeter les doublons

## ⚠️ Pièges connus
- `amount` est en centimes, pas en euros — `1000` = 10.00€
- `PaymentIntent.confirm()` peut aboutir à `requires_action` — implémenter le flow 3DS
- Les webhooks peuvent arriver hors-ordre — le handler doit être stateless sur l'état Stripe

<!-- Source: https://stripe.com/docs/api, https://stripe.com/docs/webhooks -->
<!-- Updated: 2026-03-13 -->
```

---

## Activation via Task Detection Table

Les Tier 2 Skills sont référencés dans la Task Detection Table de `AGENTS.md`. L'orchestrateur les charge uniquement quand le signal correspondant est détecté.

```markdown
| payment, stripe, charge, refund, webhook | Payment | `03-security` | `stripe-integration` | ... |
```

**Règle** : si un Tier 2 Skill n'est pas dans la Task Detection Table, il ne sera jamais chargé. Le créer sans l'enregistrer revient à ne pas le créer.

---

**Navigation** — [← Tier 1 Skills](./tier-1-skills.md) · [↑ Index](../README.md) · [Roles →](./roles.md)
