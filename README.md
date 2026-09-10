# eplan-development — a Claude Code skill for EPLAN Electric P8

Teaches Claude how to develop with **EPLAN Electric P8**: C# scripting, the EPLAN API,
and Remote Client automation — including the traps that only show up in production
(silent compile failures, the command-blocking issue, dispose discipline, the CS0234
namespaces that are reachable anyway).

Distilled from working code and from live testing against a real installation, not from
the manual alone. Every claim marked "measured" was checked on a running EPLAN.

**This skill is host-agnostic.** It carries no dependency on any particular MCP server,
RAG, or wrapper: it teaches how to write the code and how to find the identifiers,
whatever is driving EPLAN on your side — a script you paste into EPLAN's script manager,
an MCP server, a Remote Client app, or a build pipeline.

---

## Install

### As a plugin (recommended)

Inside Claude Code:

```
/plugin marketplace add covagashi/eplan-development-skill
/plugin install eplan-development@eplan-skills
```

Update later with `/plugin marketplace update eplan-skills`.

### Manually (copy the skill folder)

```bash
git clone https://github.com/covagashi/eplan-development-skill
```

```powershell
# Windows - personal skill, available in every project
xcopy /E /I eplan-development-skill\skills\eplan-development "$env:USERPROFILE\.claude\skills\eplan-development"
```

```bash
# macOS / Linux
cp -r eplan-development-skill/skills/eplan-development ~/.claude/skills/eplan-development
```

For a single project, copy to `<your-project>/.claude/skills/eplan-development` instead.

Then restart Claude Code. The skill loads by itself on EPLAN-related tasks, or you can
invoke it explicitly with `/eplan-development`.

---

## What's inside

```
eplan-development-skill/
├── .claude-plugin/
│   ├── marketplace.json          # so /plugin marketplace add works on this repo
│   └── plugin.json
└── skills/eplan-development/
    ├── SKILL.md                  # entry point: the three dev models, lookup order, golden rules
    └── references/
        ├── script-basics.md      # script structure, [Start]/[DeclareAction]/[DeclareMenu], deployment
        ├── actions-reference.md  # CommandLineInterpreter + a catalog of verified actions
        ├── core-classes.md       # Progress, PathMap, Settings, MultiLangString, ribbon, context menus
        ├── api-data-access.md    # parts DB (MDPartsManagement), properties, symbol enumeration
        ├── e3d-installation-spaces.md  # reaching DataModel/HEServices by reflection; 3D spaces
        ├── eec-typicals.md       # generating a project from an EEC One typical workbook, without EEC
        ├── remoting.md           # EplanRemoteClient, dynamic ports, headless, Cogineer
        ├── pitfalls.md           # blocking, threading, dispose, and compile errors that look like hangs
        └── integration-patterns.md    # HTTP, SignalR, forwarding EPLAN system messages outward
```

Claude reads `SKILL.md` first and pulls in only the reference file that matches the task,
so the whole thing costs very little context until it is actually needed.

---

## A taste of what it prevents

- **`RegisterScript` vs `ExecuteScript`.** `[DeclareAction]`, `[DeclareEventHandler]` and
  `[DeclareMenu]` install hooks and need `RegisterScript`; a `[Start]`-only script needs
  `ExecuteScript` and registering it just earns a spurious warning.
- **Compile errors are silent.** The engine accepts an old C# (C# 5 on 2026: no `?.`, no
  `$"..."`, no `nameof`). A script that fails to compile still "succeeds" — the caller
  just times out. The `CS####` line is sitting in EPLAN's message tree the whole time.
- **CS0234 is a compile-time limit, not a capability limit.** `using
  Eplan.EplApi.DataModel;` does not compile in a script, yet all 26 `Eplan.EplApi.*`
  namespaces and 606 public types are reachable at runtime by reflection — and the
  assembly names are *not* a clean version cutoff, so scan `AppDomain` before guessing.
- **EPLAN actions are pseudo-asynchronous.** Without an active message loop, code after
  `oCLI.Execute(...)` can hang forever.

---

## Coverage

EPLAN Electric P8 **2022–2027**, with the version-specific notes called out where they
matter: the ribbon API since 2022, remoting on by default in 2023 versus "Remote Client
Access" + gRPC in 2025, the `...Netu`-suffixed managed assemblies in 2027, and
.NET Framework 4.8.1 targeting.

## Pairs well with

- **[eplan-rag-mcp](https://github.com/covagashi/eplan-rag-mcp)** — an MCP server that
  lets Claude *execute* EPLAN actions live, plus documentation RAGs. This skill teaches
  Claude to write correct code; that one gives it hands. Neither requires the other.
- **[eplan-ctxmenu-kit](https://github.com/covagashi/eplan-ctxmenu-kit)** — a worked
  example of the context-menu material in `core-classes.md`: adding your own right-click
  entries and reading the row the user clicked.

## License

MIT — see [LICENSE](LICENSE).
