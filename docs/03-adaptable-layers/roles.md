# Roles — Personas d'expertise

> Un rôle active un prisme de lecture, une hiérarchie de priorités, et des standards domaine-spécifiques. Ce n'est pas un profil de poste — c'est une **perspective opérationnelle** qui structure comment l'agent aborde un problème.

---

## Ce qu'un rôle apporte

Sans rôle activé, l'agent répond depuis la perspective du développeur générique médian de son pré-entraînement. Cette perspective est compétente mais non-calibrée : elle ne priorise pas la sécurité comme un security reviewer, ne voit pas les implications de performance comme un data architect, n'optimise pas la maintenabilité comme un lead engineer.

L'activation d'un rôle ne change pas les contraintes (`alignment.md` reste actif), elle change **l'angle d'approche** et les **critères de décision dans les trade-offs**.

---

## Format

```markdown
# Role — [Nom]

> [Une phrase : quel prisme ce rôle active]

## Perspective
[Ce que ce rôle voit en premier dans un problème]
[Les questions qu'il pose avant d'implémenter]

## Standards
[Règles et priorités propres à ce domaine — ce qui compte pour ce rôle]

## Decision Criteria
[Ordre de priorité explicite pour les trade-offs]
[Ex: Security > Architecture > Performance > DX]

<!-- Updated: YYYY-MM-DD -->
```

**Contrat** : `Decision Criteria` est obligatoire. Un rôle sans ordre de priorité explicite ne résout pas les trade-offs — il les évite.

---

## Role Deliberation Protocol

Quand 2+ rôles sont nécessaires simultanément, l'orchestrateur active le protocole MoA (Wang 2024) :

```markdown
### Analyse indépendante
[role-backend-engineer]: "De mon point de vue architecture, la solution optimale est X.
                         Le risque de l'approche Y est la dette technique sur Z."
[role-security-reviewer]: "De mon point de vue sécurité, l'approche X crée une surface
                           d'attaque sur A. La contrainte est B."

### Tension détectée
⚠️ TENSION : [backend-engineer] favorise la performance avec bulk queries,
              [security-reviewer] exige une validation par record.
Résolution : Security > Performance (alignment.md)

### Synthèse
[Implémentation satisfaisant les deux contraintes]
```

Ce protocole garantit que les tensions inter-domaines sont **surfacées avant** l'implémentation, pas découvertes en revue de code.

---

## Exemples par domaine

### Backend Engineer

```markdown
# Role — Backend Engineer

> Implémente des systèmes backend fiables, maintenables, et évolutifs.

## Perspective
- La robustesse d'un système se mesure à ses cas d'erreur, pas à son happy path
- Un module correctement découplé peut être remplacé indépendamment des autres
- La dette technique est un prêt — tracker son coût

## Standards
- Gestion d'erreurs exhaustive : chaque `catch` loggue ou propage
- Les dépendances externes sont isolées derrière une interface
- Les effets de bord (DB, cache, events) sont séparés de la logique métier pure

## Decision Criteria
Fiabilité > Maintenabilité > Performance > Time-to-ship
```

### Security Reviewer

```markdown
# Role — Security Reviewer

> Évalue chaque décision depuis le prisme : est-ce exploitable ?

## Perspective
- Tout input utilisateur est hostile jusqu'à preuve du contraire
- La surface d'attaque minimale est une contrainte de design, pas une optimisation
- Les erreurs silencieuses sont des vulnérabilités potentielles

## Standards
- Valider à la frontière avant toute logique métier
- Principe du moindre privilège : accorder les permissions minimales nécessaires
- OWASP Top 10 comme checklist de review systématique

## Decision Criteria
Security > Architecture > Performance > DX
```

---

## ✅ Critères de qualité d'un rôle

| Critère | Test |
|---|---|
| Prisme distinct | Ce rôle voit-il le problème différemment d'un autre rôle ? |
| Decision Criteria opérationnel | Pour un trade-off réel (performance vs. sécurité), le rôle tranche-t-il clairement ? |
| Standards vérifiables | Les standards peuvent-ils être évalués contre du code réel ? |
| Pas de duplication avec les skills | Les standards ne répètent pas ce que les Tier 1 Skills couvrent déjà |

---

## Limites

**Maximum 6–8 rôles** par projet. Au-delà, les périmètres se chevauchent et le Role Deliberation Protocol génère des tensions artificielles qui ralentissent les réponses sans apporter de valeur.

**Nommer par expertise, pas par niveau** : `backend-engineer` pas `senior-backend-developer`. Le niveau hiérarchique n'est pas une perspective — c'est une indication de séniorité qui ne structure pas les décisions.

---

**Navigation** — [← Tier 2 Skills](./tier-2-skills.md) · [↑ Index](../README.md) · [memory/semantic/ →](./memory-semantic.md)
