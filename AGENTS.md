# AGENTS.md — Directives for Autonomous Contributors

> **Target Profile**: AI-Native Systems Developer / Analyst  
> **Repository Purpose**: Living portfolio, system specs, ethos, and engineering dossier. Not a corporate resume.

---

## 1. Prime Directives & Hard Constraints

1. **Max 500 Lines per File (Hard Ceiling)**:
   - **No file may exceed 500 lines.**
   - If a document approaches ~400 lines, split and refactor modularly into child docs or subdirectories.
   - Delete cruft aggressively. Favor density, signal, and precision over length.

2. **Ground Truth & Zero Fluff**:
   - Never fabricate skills, roles, metrics, or technologies.
   - Missing data? Flag it explicitly: `TODO(dev): confirm metric/context`.
   - Purge corporate HR speak ("results-driven self-starter", "leveraged synergies"). Write like an engineer drafting an RFC, design doc, or post-mortem.

3. **Dual-Audience Optimization (Human & Agent)**:
   - All markdown documents must be clean, structured, and parseable by both humans and downstream LLMs.
   - Use standard YAML frontmatter for machine indexing.
   - Link references using relative paths across the repo.

---

## 2. Directory Topology

Keep docs modular and structured so agents can locate and cross-reference them effortlessly:

- `projects/` — Flagship builds, architectural breakdowns, case studies.
  - Seed projects: `avascry.md`, `changestate.md`, `avabatt.md`, `avathings-applet.md`, `avathings-web.md`, `locklogs.md`.
- `ethos/` — Operating principles, mental models, decision heuristics, AI-native philosophies.
  - Initial seeds: `ai-native.md`, `systems-thinking.md`.
- `skills/` — Hard capability specs, toolchains, integration profiles.
- `experience/` — Roles, engagements, operational impact, battle scars.
- `lab/` — Agent experiments, bench tests, prototypes, rapid tinkerings.

---

## 3. Agent Operating Modes

When acting as an agent in this repo, adopt one of these operational lenses:

- **`architect`**: Organizes repo topology, enforces file limits, handles doc decomposition, ensures cross-linking.
- **`chronicler`**: Converts raw notes and interview answers into punchy, technical narratives.
- **`critic`**: Red-teams content for credibility, technical depth, and fluff elimination.
- **`linter`**: Audits line counts, dead links, frontmatter schema, and code block formatting.

---

## 4. Document Standards

### Frontmatter Schema
Every content markdown doc must begin with YAML frontmatter:

```yaml
---
title: "Document Title"
type: "system | ethos | experiment | spec"
tags: [tag1, tag2]
status: "draft | stable | deprecated"
last_updated: YYYY-MM-DD
---
```

### Style Heuristics
- **Format**: GitHub Flavored Markdown (GFM).
- **Architecture**: Use Mermaid diagrams (`flowchart`, `sequenceDiagram`) for topologies and pipelines instead of wordy paragraphs.
- **Code**: Provide real code, config snippets, or terminal sessions over abstract descriptions.
