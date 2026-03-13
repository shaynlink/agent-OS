# 06 — Science

> Cette section traduit les recherches empiriques sur les LLMs en décisions d'architecture concrètes. Chaque paper est présenté sous l'angle de ce qu'il implique pour l'Agent OS — pas de résumé académique, mais une traduction opérationnelle.

---

## Positionnement épistémique

Les papers ci-dessous sont des **références de justification**, pas des vérités absolues. Leurs conclusions :
- Ont été obtenues dans des conditions expérimentales précises
- Se généralisent généralement bien, mais avec des nuances selon le modèle et le contexte
- Évoluent — certaines conclusions de 2022-2023 ont été nuancées par des travaux plus récents

Chaque décision d'architecture de l'Agent OS est justifiée par au moins un paper. Si la conclusion d'un paper change, la décision correspondante doit être réévaluée.

---

## Les 11 papers — Organisation thématique

| Thème | Page | Papers |
|---|---|---|
| **Attention & contexte** | [context-attention.md](./context-attention.md) | Liu 2023, Jiang 2023 |
| **Apprentissage & auto-amélioration** | [learning-improvement.md](./learning-improvement.md) | Shinn 2023, Zelikman 2022, Wang 2022 |
| **Multi-agent & hiérarchie** | [multi-agent.md](./multi-agent.md) | Wang 2024, Wallace 2024 |
| **Mémoire & retrieval** | [memory-retrieval.md](./memory-retrieval.md) | Patil 2023, Lewis 2020, Sumers 2023, Anderson 2004 |

---

## Tableau de correspondance — Paper → Composant

| Paper | Décision d'architecture | Composant |
|---|---|---|
| Liu 2023 — *Lost in the Middle* | Skill 00 en position primaire | `tier-0/00-reasoning.md` — premier token |
| Jiang 2023 — *LLMLingua* | Chargement sélectif par signal | `orchestrator.md` — Phase 2 & 3 |
| Shinn 2023 — *Reflexion* | Verbal RL Loop Error Signal | `auto-learning.md` + `memory/episodic/` |
| Zelikman 2022 — *STaR* | Verbal RL Loop Success Signal | `auto-learning.md` + `memory/procedural/` |
| Wang 2022 — *Self-Consistency* | Self-Consistency Gate pré-commit | `orchestrator.md` — Phase 5 |
| Wang 2024 — *MoA* | Role Deliberation Protocol | `orchestrator.md` — Phase 4 |
| Wallace 2024 — *Instruction Hierarchy* | Hiérarchie de vérité + conflict detection | `alignment.md` |
| Patil 2023 — *Gorilla* | Format retrieval-aware + Task Detection | `memory/semantic/` + `AGENTS.md` |
| Lewis 2020 — *RAG* | Chargement sectionnel ciblé | `memory/semantic/` — format INDEX |
| Sumers 2023 — *CoALA* | Taxonomie des 4 types de mémoire | Architecture `memory/` |
| Anderson 2004 — *ACT-R* | Mémoire procédurale et épisodique | `memory/procedural/` + `memory/episodic/` |

---

**Navigation** — [← Limites & Non-Usages](../05-philosophy/limits-and-non-usages.md) · [↑ Index](../README.md) · [Attention & Contexte →](./context-attention.md)
