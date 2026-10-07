# AGENTS.md — Directives for Autonomous Contributors

> **Target Profile**: AI-Native Systems Developer / Analyst  
> **Repository Purpose**: Living portfolio, system specs, ethos, and engineering dossier. Not a corporate resume.

---

## 1. Prime Directives & Hard Constraints

1. **Max 500 Lines per File (Hard Ceiling)**:
   - **No file may exceed 500 lines.**
   - If a document approaches ~400 lines, split and refactor modularly into child docs or subdirectories.
   - Delete cruft aggressively. Favor clarity and signal over length.

2. **Ground Truth & Zero Fluff**:
   - Never fabricate skills, roles, metrics, or technologies.
   - Missing data? Flag it explicitly: `TODO(dev): confirm metric/context`.
   - Purge corporate HR speak ("results-driven self-starter", "leveraged synergies"). Write like a sharp engineer explaining their work to a smart friend (see §5).

3. **Dual-Audience Optimization (Human & Agent)**:
   - All markdown documents must be clean, structured, and parseable by both humans and downstream LLMs. Humans get plain prose; agents get the structure around it.
   - No YAML frontmatter. It reads as clutter to humans on GitHub.
   - Link references using relative paths across the repo.

---

## 2. Directory Topology

Keep docs modular and structured so agents can locate and cross-reference them effortlessly:

- `projects/` — Flagship builds, architectural breakdowns, case studies.
  - Seed projects: `avascry.md`, `changestate.md`, `avabatt.md`, `avathings-applet.md`, `avathings-web.md`, `locklogs.md`.
- `infrastructure/` — Physical lab topology, compute partitioning, power resiliency, network specs.
- `ethos/` — Operating principles, mental models, decision heuristics, AI-native philosophies.
  - Initial seeds: `ai-native.md`, `systems-thinking.md`.
- `skills/` — Hard capability specs, toolchains, integration profiles.
- `experience/` — Roles, engagements, operational impact, battle scars.
- `lab/` — Agent experiments, bench tests, prototypes, rapid tinkerings.

---

## 3. Agent Operating Modes

When acting as an agent in this repo, adopt one of these operational lenses:

- **`architect`**: Organizes repo topology, enforces file limits, handles doc decomposition, ensures cross-linking.
- **`chronicler`**: Converts raw notes and interview answers into plain, concrete stories a non-specialist can follow (see §5).
- **`critic`**: Red-teams content for credibility, jargon, unverified claims, and fluff. Flags anything a smart non-specialist would bounce off.
- **`linter`**: Audits line counts, dead links, and code block formatting.

---

## 4. Document Standards

### Style Heuristics
- **Format**: GitHub Flavored Markdown (GFM).
- **Architecture**: Use Mermaid diagrams (`flowchart`, `sequenceDiagram`) for topologies and pipelines in deep-dive docs. Keep them out of the README and project intros.
- **Code**: Provide real code, config snippets, or terminal sessions over abstract descriptions.

---

## 5. Voice & Persona

The reader is a smart human who is not a specialist: a hiring manager, a peer, a curious stranger. Specs and schemas are for agents. Prose is for people.

- **Persona**: First person ("I built", "I decided"), plain and warm, a little dry. Confident without bragging.
- **Lead with what it does for someone.** Say what the thing is and why anyone would care before any internals. Mechanics go in linked deep-dive docs, not the README or a project's opening paragraph.
- **Plain words over jargon.** If a term needs a PhD, swap it or explain it in a clause. No invented-sounding branding ("harmonic dispersion", "Apex", "invariant-driven paradigm") unless it is the real name of a real thing.
- **Concrete over grand.** Real commands, real numbers, real constraints. "Typically 1–25 ms on a downclocked laptop" beats "sub-millisecond, enterprise-grade".
- **No superlatives or unverified metrics.** If a number is not measured, leave it out or mark it `TODO(dev): confirm`. Claims must survive the owner reading them aloud.
- **Short sentences, short sections.** Emoji section headers are fine in READMEs and top-level pages. Use them sparingly in deep technical docs.
- **Reference voice**: `tdg/compstate/README.md` (ChangeState). Match its tone: direct, friendly, example-first.

**Before/after**

> ❌ "Autonomous prime-cadenced hardware actuation and thermal envelope manager leveraging sysfs/ACPI kernel interfaces."
>
> ✅ "An automatic resource manager for Linux. It keeps machines cooler and quieter by capping CPU clocks. No third-party dependencies."
