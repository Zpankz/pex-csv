# KNOWLEDGE_GRAPH_ROLE.md

> This file documents this repository's role in the unified
> CICM/ANZCA medical knowledge graph. The canonical skill lives at:
> `MAK95-Vault/.claude/skills/medical-knowledge-graph/`.
> The skill ingests this repository as part of its build pipeline.

## Role

Confidence-weighted LO ↔ SAQ edge index: MAPPING_INDEX.md (471 LOs → 2,766 SAQ matches) + mapping_relationships.json (relational form). Build pass P2/P3 consumes both. CCM-RUBRIC.json supplies the R1–R10 rubric criterion nodes. EXAM_INTELLIGENCE_DOSSIER.md feeds temporal-recency properties (year_first_asked, year_last_asked, n_times_asked) for the Pareto core scoring.

## Source of truth

- Skill manifest: `MAK95-Vault/.claude/skills/medical-knowledge-graph/SKILL.md`
- Build pipeline: `scripts/build_graph.py`
- Ontology (homoiconic): `ontology/{nodes,edges,properties}.yaml`
- Self-improvement loop: `gepa/README.md`

## Branch

Development branch for this initiative: `claude/medical-knowledge-graph-UjThY`
