# memory/semantic/ — Cartes de connaissance

> `memory/semantic/` contient la représentation structurée du codebase que l'agent utilise pour raisonner sur l'architecture sans avoir à lire chaque fichier à chaque session.

---

## Nature et rôle

La mémoire sémantique capture la **structure et les relations** du projet : quels modules existent, quelles sont leurs responsabilités, comment ils interagissent, quelles conventions s'appliquent localement. C'est la réponse à la question : "qu'est-ce que l'agent doit savoir sur ce projet pour ne pas avoir à le redécouvrir à chaque session ?"

Sans `memory/semantic/`, l'agent infère l'architecture depuis les fichiers qu'il peut lire dans la session courante. Cette inférence est souvent correcte pour des projets simples, et souvent incorrecte pour des projets complexes où les conventions implicites, les décisions historiques, et les interdépendances non-évidentes ne sont pas visibles dans un fichier isolé.

---

## Format obligatoire — Retrieval-Aware

L'orchestrateur charge des **sections** de ces fichiers, pas les fichiers entiers. Ce mécanisme ne fonctionne que si les fichiers respectent le format retrieval-aware :

```markdown
<!-- auth-architecture.md — Retrieval-Aware Format -->
<!-- Updated: YYYY-MM-DD -->

## INDEX
- [Auth Module](#auth-module) — jwt, session, passkey, oauth, middleware
- [Token Service](#token-service) — rotation, blacklist, redis
- [RBAC System](#rbac-system) — permissions, roles, policies

## <section id="auth-module"> Auth Module

### Responsabilité
[Description précise de ce que ce module fait]

### Fichiers clés
- `apps/api/src/modules/auth/` — module principal
- `apps/api/src/middleware/auth.middleware.ts` — guard express

### Conventions locales
- Les guards exportent une fonction, pas une classe
- Les erreurs d'auth sont toujours 401, jamais 403 (distinction refusée au client)

### ⚠️ Points d'attention
- Ne pas logger les tokens JWT en entier — tronquer à 20 chars
- La rotation de token est asymétrique : le refresh token ne tourne pas au même rythme

</section>
```

---

## ✅ Bonne entrée sémantique

```markdown
## <section id="payment-service"> Payment Service

### Responsabilité
Orchestration des paiements via Stripe. Traduit les événements Stripe en événements
domaine internes. N'expose jamais les objets Stripe directement aux autres modules.

### Fichiers clés
- `apps/api/src/services/payment.service.ts` — service principal
- `apps/api/src/webhooks/stripe.webhook.ts` — handler webhook
- `apps/api/src/types/payment.types.ts` — types internes (PAS les types Stripe)

### Dépendances
- Redis (via `CacheService`) — idempotence des webhooks
- `OrderService` — notifié après paiement confirmé
- `NotificationService` — email de confirmation

### Conventions locales
- Les erreurs Stripe → convertir en `PaymentError` interne avant propagation
- Webhook endpoint : `/api/webhooks/stripe` — signature vérifiée via `stripe.webhooks.constructEvent`
- Idempotency Key : toujours `order-{orderId}` pour éviter les doubles charges

### ⚠️ Pièges connus
- NE PAS logger les PaymentIntents complets — contiennent des métadonnées client sensibles
- Les webhooks peuvent arriver dans le désordre — le handler doit être idempotent
- Stripe peut envoyer plusieurs fois le même événement — Redis guard obligatoire

</section>
```

**Pourquoi c'est bon** : spécifique, vérifiable, actionnable. Contient les pièges connus localement, pas seulement la structure.

---

## ❌ Mauvaise entrée sémantique

```markdown
## Payment

On utilise Stripe pour gérer les paiements. Le service payment est dans `services/`.
Il envoie des emails après paiement.
```

**Pourquoi c'est mauvais** : n'indique pas les fichiers exacts, les conventions locales, les pièges, les dépendances. L'agent inferera les détails — et se trompera sur les points non-évidents.

---

## ⚠️ Pièges courants

**Piège 1 — Oublier le format INDEX**

Sans la section INDEX, l'orchestrateur ne peut pas charger une section ciblée. Il doit charger le fichier entier — ce qui consomme beaucoup plus de budget contextuel.

**Piège 2 — Entrées obsolètes**

Une carte sémantique qui décrit une architecture qui a changé il y a 3 mois est pire qu'une carte absente. Elle induit activement en erreur. Ajouter `<!-- Updated: YYYY-MM-DD -->` et vérifier régulièrement.

**Piège 3 — Trop granulaire**

Documenter chaque fichier individuellement produit une carte inutilisable — trop volumineuse pour être chargée sélectivement, trop détaillée pour rester maintenue. Documenter au niveau des **modules / domaines**, pas des fichiers.

**Piège 4 — Répliquer la documentation officielle**

`memory/semantic/` documente les **décisions et conventions locales**, pas ce que fait Stripe ou Prisma en général. L'agent connaît ces librairies. Ce qu'il ne connaît pas, c'est comment *ce projet* les utilise.

---

## Règles de mise à jour

| Événement | Action |
|---|---|
| Nouveau module créé | Ajouter une section dans le fichier de domaine approprié |
| Service significativement refactorisé | Mettre à jour la section correspondante |
| Convention locale établie | L'ajouter dans "Conventions locales" de la section |
| Piège découvert | L'ajouter dans "⚠️ Pièges connus" de la section |
| Fichier > 30 jours sans code change | Vérifier la fraîcheur de l'entrée |

**Priorité de déclenchement** : après chaque session qui modifie significativement l'architecture, mettre à jour `memory/semantic/` avant de clôturer la session.


---

**Navigation** — [← Roles](./roles.md) · [↑ Index](../README.md) · [memory/episodic/ →](./memory-episodic.md)
