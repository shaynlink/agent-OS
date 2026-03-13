# 07 — Benchmarks & Risques

> Cette section documente les résultats mesurés de recherches sur lesquelles l'Agent OS se base, les modes de défaillance observés, et les risques opérationnels à anticiper avant utilisation en production.

---

## Pages

| Page | Contenu |
|---|---|
| [performance-studies.md](./performance-studies.md) | Études de performance LLM en contexte long — données brutes et conclusions |
| [known-failure-modes.md](./known-failure-modes.md) | Modes de défaillance documentés du système |
| [problems-and-dangers.md](./problems-and-dangers.md) | Problèmes structurels et dangers opérationnels |

---

## Lecture obligatoire avant production

> [!IMPORTANT]
> Lire [problems-and-dangers.md](./problems-and-dangers.md) entièrement avant de déployer un agent configuré avec ce boilerplate sur des tâches critiques ou irréversibles.

---

## Positionnement de cette section

L'Agent OS intègre des conclusions de recherches empiriques sur les LLMs. Ces conclusions ne sont pas des vérités absolues — ce sont des résultats expérimentaux dans des conditions précises, avec des modèles précis, sur des benchmarks spécifiques.

Cette section :
1. Documente les papers sur lesquels le système se base, avec les chiffres réels
2. Identifie où les conclusions pourraient ne pas tenir en dehors des conditions expérimentales
3. Documente les modes de défaillance observés dans des déploiements réels
4. Évalue les risques indépendamment du discours du système sur lui-même


---

**Navigation** — [← 06 — Science](../06-science/memory-retrieval.md) · [↑ Index](../README.md) · [Études de Performance →](./performance-studies.md)
