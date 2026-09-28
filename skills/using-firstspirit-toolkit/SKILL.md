---
name: using-firstspirit-toolkit
description: "Use when starting any session that involves FirstSpirit CMS work — establishes what skills are available and when to invoke them."
---

# FirstSpirit AI Toolkit

## First: is this FirstSpirit work?

This toolkit is loaded at session start. Depending on the assistant, it may load
only inside a FirstSpirit project, or in **every** project — so before you use any
of it, decide whether the current task actually involves FirstSpirit CMS.

- **Not FirstSpirit work?** Ignore this file and the skills below entirely. Do not
  mention them, and do not let them influence your answer — respond as if the
  toolkit were not loaded.
- **FirstSpirit work?** Apply the rule below before acting.

## Rule

When the task involves FirstSpirit, check whether one of the skills below fits before you act, and invoke the most specific match rather than working from general knowledge — FirstSpirit's APIs and conventions are easy to get subtly wrong from memory. You do not need to invoke a skill before asking a clarifying question or reading the code to understand the task; do it before you write or change FirstSpirit code, templates, or configuration.

## Available Skills

### Templating (`skills/templating/`)
- **firstspirit-templating-reference** _(beta)_ — Use when you need the exact FirstSpirit template syntax, the right GOM input component, the datatype a component yields and how to read it in an output channel, a validation/visibility rule, a database-schema fact, or whether something is deprecated.
  Covers: `$CMS_*$` tags and system objects (`#global`, `#nav`, `#row`), string operations and escaping, the Navigation and PageGroup header functions, content projection, every `CMS_INPUT_*` / `FS_*` component with its datatype, database schemas (column types and which component maps onto which, foreign keys, the KEY column, queries, Remote Data, Entity vs Dataset), rules (`Ruleset.xml`), identifier and casing rules, deprecated → current components, how to read `GomSource.xml` / `ChannelSource` files.

### Project (`skills/project/`)
- **firstspirit-api-reference** _(beta)_ — Use when you need to know which Access API interface a store element has, how the stores nest, which agent yields a store or service, how to load an element by UID, or how to write an `fs` query — the object-model map for scripts and modules.
  Covers: The store and element hierarchy as text trees, `SpecialistsBroker` and the agents (`StoreAgent`, `QueryAgent`, `OperationAgent`, `BrokerAgent` …), `requestSpecialist` vs `requireSpecialist`, loading elements by UID and type, form-value access (`FormData`, `FormField`), the `fs` query language, lock/save/unlock and release rationale.
- **firstspirit-scripting** _(beta)_ — Use when writing, reviewing or debugging a FirstSpirit BeanShell script — which `context` object a script type gets, BeanShell syntax and its traps, logging, and safe Access-API patterns for reading and writing elements and datasets.
  Covers: Script types and their context hierarchy (`BaseContext`, `ProjectScriptContext`, `ClientScriptContext`, `GenerationContext` …) as a diagram, BeanShell language notes, logging and debugging, conventions, common patterns (elements, form data, datasets that persist only changed values, relation lists mutated in place), real-world script shapes.
- **firstspirit-rest-api** _(beta)_ — Use when performing CMS operations through the FirstSpirit REST API — creating or editing templates, managing pages and sections, writing form-field values, uploading media, running scripts, or searching content — and when a call answers 4xx/5xx and you need the meaning.
  Covers: Endpoint groups for content management, content templates (GOM/form creation and the section-id drift between module versions), content catalogue and search, Global Content Areas, media upload rules, language-path PATCH semantics, the error-code table (403, 409, 410, 413, 429) and the write-order (dependencies first) it forces.

### Deployment (`skills/deployment/`)
- **firstspirit-external-sync-export** _(beta)_ — Use when exporting or importing a FirstSpirit project with fs-cli (FSDevTools / external synchronisation) — installation, connection and authentication, the export and import commands and their options, and what the error messages mean.
  Covers: fs-cli setup, connection modes and credentials handling, `export` and `import` commands with filters and the resulting file layout, external-sync file names, troubleshooting of the common failures.

## Platform Tool Mappings

If you are running on a harness listed below, read the corresponding reference for how abstract operations map to that harness's real tool names:

- **Codex:** read `skills/using-firstspirit-toolkit/references/codex-tools.md`
- **Cursor:** read `skills/using-firstspirit-toolkit/references/cursor-tools.md`
- **Gemini CLI:** read `skills/using-firstspirit-toolkit/references/gemini-tools.md`
- **GitHub Copilot:** read `skills/using-firstspirit-toolkit/references/copilot-tools.md`

Claude Code users: native tool names match the skill descriptions directly.
