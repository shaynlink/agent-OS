# Étape 4 — Peupler memory/semantic/

> **Objectif** : Donner à l'agent une carte vérifiable du codebase. Sans cette étape, l'agent infère l'architecture depuis les fichiers qu'il lit dans la session courante — et se trompe sur les conventions implicites, les interdépendances, et les pièges locaux.

---

## Format obligatoire — Retrieval-Aware

L'orchestrateur charge des **sections** de ces fichiers, pas les fichiers entiers. Ce mécanisme ne fonctionne que si les fichiers respectent le format suivant :

```markdown
<!-- services.md — Retrieval-Aware Format -->
<!-- Updated: 2026-03-13 -->

## INDEX
- [PaymentService](#payment-service) — stripe, webhook, charge, refund
- [NotificationService](#notification-service) — email, sms, push
- [AuthService](#auth-service) — jwt, session, refresh

## <section id="payment-service"> PaymentService
### Responsabilité
[...]
### Fichiers clés
[...]
### Conventions locales
[...]
### ⚠️ Pièges connus
[...]
</section>

## <section id="notification-service"> NotificationService
[...]
</section>
```

**Les 3 éléments obligatoires** :
1. `<!-- Updated: YYYY-MM-DD -->` en tête de fichier
2. Section `## INDEX` avec mots-clés par section (utilisés pour la détection de signaux)
3. Balises `<section id="">` et `</section>` encadrant chaque section

---

## Quoi documenter

Documenter au niveau des **domaines / modules**, pas des fichiers individuels.

| À documenter | Exemples |
|---|---|
| Services métier critiques | `PaymentService`, `AuthService`, `OrderService` |
| Architecture de données | Schémas Prisma, relations, contraintes |
| APIs exposées | Endpoints, contrats, versioning |
| Intégrations externes | Stripe, Sendgrid, S3 — avec les conventions locales d'usage |
| Points d'attention connus | Bugs récurrents, limitations, "ne jamais faire X" |

**Ne pas documenter** : la documentation officielle des librairies (l'agent la connaît), les implémentations triviales, les fichiers de configuration génériques.

---

## Avec quoi commencer

Au départ du projet, créer **2 fichiers** :
1. `semantic/architecture.md` — vue d'ensemble : couches, services principaux, flux de données
2. `semantic/[domaine-critique].md` — le module le plus complexe ou le plus risqué du projet

Enrichir à chaque session qui modifie significativement l'architecture.

---

## ✅ Checkpoint

- [ ] Au moins 2 fichiers dans `memory/semantic/`
- [ ] Chaque fichier a la section `## INDEX` avec des mots-clés
- [ ] Les balises `<section id="">` sont présentes et fermées
- [ ] Chaque section "⚠️ Pièges connus" existe — même si elle est à remplir plus tard
- [ ] Test : l'orchestrateur peut charger une section isolée (ex: `#payment-service`) sans charger le fichier entier

---

**Navigation** — [← Étape 3 — Tier 1 Skills](./step-3-tier1-skills.md) · [↑ Index](../README.md) · [Étape 5 — Roles →](./step-5-roles.md)
