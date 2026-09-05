# PodLens Interpreter

[中文](./README.md) · English

An evidence-grounded Agent Skill for turning long-form sources into traceable reconstructions, readable retellings, and publishable content packs.

PodLens Interpreter is a standalone skill built from the PodLens methodology. It runs inside a compatible agent; there is no separate web app, local service, or model API configuration in this repository.

**Current skill version:** `1.1.0`

## What the skill does

The workflow has three stages, in order:

1. **Faithful Reconstruction** — reconstruct the source's question, findings, positions, and uncertainty from evidence anchors.
2. **Plain-Language Retelling** — explain the argument in continuous prose while staying inside the evidence established in Stage 1.
3. **Content Pack** — turn the verified reconstruction into reusable publishing assets.

The evidence chain is the core of the skill:

```text
source segment
    -> anchor
    -> finding
    -> retelling
    -> publishable asset
```

Stage 1 now uses an anchor-first pass. Verbatim quotes, timestamps, page markers, or section markers are collected before findings are synthesized. A finding without a sufficient anchor is omitted rather than softened into an unsupported claim.

Every completed run ends with a compact `Audit` section covering evidence traceability, attribution, uncertainty, and style checks.

## Supported material

The skill is designed for long-form sources such as:

- podcast and interview transcripts
- YouTube subtitles or pasted `.srt` / `.vtt` text
- research papers and academic text
- technical reports
- long articles, essays, lectures, and talks

A full three-stage run is most useful from roughly 800 words upward. Shorter material defaults to Stage 1 only under the current skill rules. Sources above roughly 12,000 words are handled in natural chunks before the findings are merged.

Output follows the language of the current conversation unless another language is requested explicitly.

No API key is required by the skill itself. The agent hosting the skill supplies the model and available tools.

## Output modes

### Standard mode

For podcasts, interviews, articles, and lectures:

- Core Question
- Core Findings with evidence anchors
- Core Positions
- plain-language retelling
- X post or thread
- LinkedIn post
- newsletter intro
- five follow-up angles
- Audit

### Paper mode

For research papers and technical reports:

- Core Question
- Core Findings with evidence anchors
- Core Positions
- plain-language retelling
- Research Brief
- Evidence Table
- Business and Creator Angles
- Audit

Follow-up and application ideas are labeled as derived from specific Stage 1 findings so they do not become source claims by accident.

## Installation

`SKILL.md` follows the portable Agent Skills pattern. Installation paths differ by host.

### BotLearn SkillHunt

```text
skillhunt podlens-interpreter
```

`skillhunt` remains an alias of BotLearn's current skill installation command.

### Claude Code

Project-level:

```text
.claude/skills/podlens-interpreter/SKILL.md
```

Personal/global:

```text
~/.claude/skills/podlens-interpreter/SKILL.md
```

### Codex

Install the skill as its own directory under `$CODEX_HOME/skills`; the default location is:

```text
~/.codex/skills/podlens-interpreter/SKILL.md
```

The earlier instruction to paste the skill into `AGENTS.md` is no longer the preferred installation path.

### Cursor

Project-level locations include:

```text
.cursor/skills/podlens-interpreter/SKILL.md
.agents/skills/podlens-interpreter/SKILL.md
```

Cursor also discovers compatible skills from Claude and Codex skill directories.

### Windsurf

Project-level:

```text
.windsurf/skills/podlens-interpreter/SKILL.md
```

Cross-agent project location:

```text
.agents/skills/podlens-interpreter/SKILL.md
```

Global Windsurf skills live under:

```text
~/.codeium/windsurf/skills/podlens-interpreter/SKILL.md
```

For any compatible host, the reusable unit is the `podlens-interpreter/` skill directory containing this repository's `SKILL.md`.

## Invoking the skill

Once discovered by the host agent, a request can simply name the workflow and provide source material, for example:

```text
Use PodLens Interpreter on this transcript and produce the standard three-stage output.
```

Compatible agents may also expose the skill through their own slash-command or skill picker.

## Examples

The repository contains three kinds of examples:

- `examples/demo_a_*` — an early compact podcast fixture
- `examples/demo_b_*` — an early compact paper fixture
- `example-output-alphago.md` — a longer Paper Mode run on the 2016 AlphaGo Nature paper

The two compact demo inputs predate the current v1.1 minimum-length behavior and are shorter than the present recommendation for a full three-stage run. They are useful for seeing the early output shape, not as compliance fixtures for the current `SKILL.md` contract.

`example-output-alphago.md` is also labeled as a v1-era run. It remains useful as a concrete evidence-grounded Paper Mode example, while the current output contract is defined by `SKILL.md` v1.1.0.

## Relationship to PodLens

[PodLens](https://github.com/lumihelia/PodLens) is the larger Python interpretation and publishing workspace with a CLI, local editorial workbench, private personal mapping, and bilingual static-site publishing.

PodLens Interpreter extracts one portable workflow from the same methodological family: reconstruct the source first, keep claims traceable, then produce downstream content. It does not depend on the PodLens application to run.

## Repository map

- `SKILL.md` — current executable skill instructions and metadata
- `README.md` — Chinese repository overview
- `README.en.md` — English repository overview
- `examples/` — compact early demo fixtures
- `example-output-alphago.md` — longer Paper Mode example

## License

[MIT](LICENSE)
