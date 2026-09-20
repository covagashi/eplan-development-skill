# EPLAN agent skills — for EPLAN Electric P8 & EEC Pro

[![skills.sh](https://skills.sh/b/covagashi/eplan-development-skill)](https://skills.sh/covagashi/eplan-development-skill)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

[Agent Skills](https://skills.sh) for EPLAN development — install once, and the
**router** loads the right skill for whatever you're automating: **EPLAN Electric
P8** (C# scripting, the EPLAN API, Remote Client) or **EPLAN EEC Pro** (typicals,
the mechatronic model, the formula language, generation).

Teaches your agent the traps that only show up in production — silent compile
failures, the command-blocking issue, dispose discipline, the CS0234 namespaces
that are reachable anyway — plus how to drive EEC Pro's model → configuration →
generation pipeline. Distilled from working code and live testing against a real
installation, not from the manual alone.

---

## Install

### skills.sh (any agent)

```bash
npx skills add covagashi/eplan-development-skill
```

### As a Claude Code plugin

```
/plugin marketplace add covagashi/eplan-development-skill
/plugin install eplan@eplan-skills        # router + all skills
/plugin install eplan-development@eplan-skills   # P8 only
/plugin install eec-pro@eplan-skills             # EEC Pro only
```

Update later with `/plugin marketplace update eplan-skills`.

### Manually

```bash
git clone https://github.com/covagashi/eplan-development-skill
```

```powershell
# Windows - personal skills, available in every project
xcopy /E /I eplan-development-skill\skills\eplan-p8\eplan-development "$env:USERPROFILE\.claude\skills\eplan-development"
xcopy /E /I eplan-development-skill\skills\eec-pro\eec-pro-development "$env:USERPROFILE\.claude\skills\eec-pro-development"
```

```bash
# macOS / Linux
cp -r eplan-development-skill/skills/eplan-p8/eplan-development ~/.claude/skills/
cp -r eplan-development-skill/skills/eec-pro/eec-pro-development ~/.claude/skills/
```

For a single project, copy into `<your-project>/.claude/skills/` instead.

---

## The MCP servers

The skills are **host-agnostic** — useful on their own. Paired with the MCP
servers from [eplan-rag-mcp](https://github.com/covagashi/eplan-rag-mcp) the agent
can also *query the EPLAN documentation* and *drive a running EPLAN*.

This repo ships a [`.mcp.json`](.mcp.json) with the three **remote doc RAGs**
(already deployed, no local data needed):

| Server | Endpoint | Covers | Search |
|---|---|---|---|
| `eplan-rag` | `rag2026.covaga.xyz/mcp` | EPLAN P8 docs 2026 | Semantic |
| `eplan-wiki-2027` | `rag2027.covaga.xyz/mcp` | EPLAN P8 docs 2027 | Keyword (FTS5) |
| `eecpro-rag` | `rageecpro.covaga.xyz/mcp` | EEC Pro 2026 docs | Semantic, 36 categories |

Or register them by hand:

```bash
claude mcp add eplan-rag       --transport http https://rag2026.covaga.xyz/mcp
claude mcp add eplan-wiki-2027 --transport http https://rag2027.covaga.xyz/mcp
claude mcp add eecpro-rag      --transport http https://rageecpro.covaga.xyz/mcp
```

(Older setups: `claude mcp add eecpro-rag -- npx mcp-remote https://rageecpro.covaga.xyz/mcp`,
with `cmd /c` before `npx` on Windows.)

The **local action server** (`eplan`, ~200 tools: run actions/scripts inside a
running EPLAN, introspect the API live) installs separately — it needs Python +
`pythonnet` on the EPLAN machine. See
[eplan-rag-mcp](https://github.com/covagashi/eplan-rag-mcp#local-eplan-automation-p8).

---

## What's inside

```
eplan-development-skill/
├── .claude-plugin/
│   └── marketplace.json        # /plugin marketplace add works on this repo
├── .mcp.json                   # the three remote doc-RAG MCP servers
├── router/
│   └── SKILL.md                # detects P8 vs EEC Pro + task → loads the right skill
└── skills/
    ├── eplan-p8/
    │   └── eplan-development/
    │       ├── SKILL.md        # the three dev models, lookup order, golden rules
    │       └── references/     # script-basics, actions, core-classes,
    │                           # api-data-access, e3d-installation-spaces,
    │                           # eec-typicals, remoting, pitfalls, integration
    └── eec-pro/
        └── eec-pro-development/
            ├── SKILL.md        # model → configure → generate pipeline, golden rules
            └── references/
                └── doc-categories.md   # the 36-category map of the EEC Pro doc index
```

Each `SKILL.md` is the entry point; the agent pulls in only the reference file
that matches the task, so it costs little context until needed.

## Coverage

- **EPLAN Electric P8 2022–2027** — with the version-specific notes called out:
  ribbon API since 2022, remoting defaults 2023 vs "Remote Client Access" + gRPC
  in 2025, the `...Netu` assemblies in 2027, .NET Framework 4.8.1 targeting.
- **EPLAN EEC Pro 2026** — mechatronic model, typicals/variants/conditions,
  formula language, Form-UI, import formats, scripting, commands, Job Server, and
  all generation targets (ECAD/P8, Pro Panel, PLC, Graph2D, text/Office, SAP).

## Pairs well with

- **[eplan-rag-mcp](https://github.com/covagashi/eplan-rag-mcp)** — the MCP
  servers: a local one that lets the agent *execute* inside EPLAN, plus the three
  remote doc RAGs configured in `.mcp.json`. These skills teach the agent to write
  correct code; the servers give it hands. Neither requires the other.
- **[eplan-ctxmenu-kit](https://github.com/covagashi/eplan-ctxmenu-kit)** — a
  worked example of the context-menu material: adding right-click entries and
  reading the row the user clicked.

## License

MIT — see [LICENSE](LICENSE).
