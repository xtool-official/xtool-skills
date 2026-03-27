---
name: creating-generators
description: Use when creating or refactoring a generator that uses generator-sdk, runtime/full/embed, PanelSchema, template pages, or old generator standardization.
---

# Creating Generators

## Quick Identification

Use this skill when the request is clearly about a generator rather than a normal page.

### Strong Triggers

- the user explicitly says `generator`, `build a generator`, `create a generator`
- generator types such as `house generator`, `frame generator`, `pendant generator`, `nameplate generator`
- the user mentions `generator-sdk`, `runtime contract`, `PanelSchema`, `full/embed`, template page embedding, or old generator standardization
- the task is to add generator platform capabilities such as login, SVG export, Open in Studio, credits, billing, cloud save, or history

### Weak Triggers

- `left tools + center canvas + right parameter panel`
- `canvas in the middle`
- `parameter panel`
- `appKey`
- `history`

If only weak triggers appear, ask one clarifying question first:

`Does this need generator-sdk, runtime/full/embed, template page embedding, or old generator standardization?`

If the answer is no, do not use this skill.

### Exclusions

Do not use this skill for:

- normal campaign pages, showcase pages, marketing landing pages, or form pages
- general UI or styling work with no generator runtime or embedding semantics
- editor-like pages that do not need `generator-sdk`, runtime, `PanelSchema`, or template hosting

## Overview

The goal is not "build a page first". The default goal is "build or evolve a generator that can be consumed by different hosts and can keep evolving".

There are two capability layers:

- `generator-sdk` handles platform capabilities
- `Generator Runtime Contract` handles `full/embed`, state, panel schema, export, and host integration

For new generators, default to the standard shell path:

- the runtime starter provides the generator runtime structure
- the full-mode host mounts `generator-workbench`
- generator-specific business logic stays inside runtime, rendering, and panel-schema modules

Do not hand-write a custom full-page shell for a new generator unless the user explicitly asks for a reduced-scope build, a custom shell, or a runtime-only delivery.

If the task involves templates, template pages, sub-generators, preset parameters, publish/import template, or partial-parameter editing, template capability must use a unified template protocol instead of generator-private JSON.

## Default Workflow

Ask only one question at a time. Use this single workflow as the authority for execution:

1. confirm this is really a generator task
2. bootstrap `generator-sdk-mcp`
3. determine task framing: new build / compatibility refactor / standardization refactor / reduced-scope task
4. ask the minimum branch-specific questions
5. implement the generator runtime with starter + `generator-sdk` + runtime + `PanelSchema`
6. for new generators, default the full-mode host to `generator-workbench`; only skip it for explicit reduced-scope / custom-shell / runtime-only requests, and use the template protocol when needed
7. apply the completion gate and wrap up with the required status fields

## MCP Bootstrap

Do not assume the user has already installed `generator-sdk-mcp`.

Before the first generator question:

1. check whether the current AI tool already has a working `generator-sdk-mcp` / `generator-sdk` MCP server
2. if missing, inspect `https://generator-sdk.pages.dev/zh/mcp/`
3. if installation is possible in the current environment, complete the MCP setup for the user
4. verify that the MCP server or generator tools are actually available
5. if installation is blocked, continue through the documentation fallback path instead of blocking the task

Verification should confirm at least one of the following:

- the MCP server is visible to the AI tool
- generator-specific tools such as `generate_code`, `generate_runtime_starter`, `query_documentation`, or `list_apis` can be discovered

Documentation fallback:

- `https://generator-sdk.pages.dev/zh/guide/`
- runtime contract related docs
- template docs when template-authoring capability is involved

Missing MCP is not a reason to downgrade the task into generic frontend page work.

## Task Framing

Old-generator work must be framed explicitly before implementation.

### Hard Framings

- `compatibility refactor`: keep existing features, interactions, styles, and export behavior stable while adding needed capabilities with minimal change
- `standardization refactor`: converge to the standard generator shape with `generator-sdk`, complete runtime, `full/embed`, `PanelSchema`, and template protocol when needed

If the user says "refactor the old generator and integrate generator-sdk" but does not say which framing applies, ask first. Do not silently default to a lightweight bridge wrapper.

If the user requires unchanged behavior plus `generator-sdk` integration and does not authorize staged delivery, default to `standardization refactor`.

### Reduced-Scope Tasks

Not every task needs full standardization immediately. These are valid reduced-scope tasks:

- `sdk-only integration`
- `runtime-only integration`
- `template-only integration`
- `compatibility-only refactor`

For reduced-scope tasks, say explicitly that the result is partial and do not claim standardization completion.

## New Generator Branch

For a new generator, ask the minimum required questions in this order:

1. generator name
2. tech stack: `html` or `vue`
3. required SDK capabilities
4. whether template-authoring capability is needed

Default behavior:

- auto-generate a development `appKey` in the form `dev_<random>`
- prefer a runtime starter
- enable both `full` and `embed`
- expose `window.__GENERATOR_RUNTIME__`
- provide `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, `export`, and `subscribe`
- for new generators, default `full` mode to a host page that mounts `generator-workbench`
- keep `embed` mode as pure runtime rendering without shell-level platform UI
- only skip `generator-workbench` when the user explicitly asks for a reduced-scope build, a custom shell, or a runtime-only delivery

Default recommendations:

- rapid vibe coding or static delivery -> `html`
- engineered project or Vue ecosystem -> `vue`
- `html` -> `starter-html-runtime`
- `vue` -> `starter-vue-runtime`

If template capability is needed, enable the `template` feature, use a unified template protocol, and keep template pages parameter-driven through `panelFilter`.

## Refactor Branch

For an old generator, ask the minimum required questions in this order:

1. `compatibility refactor` or `standardization refactor`
2. code location / repository
3. whether the existing production `appKey` must be reused
4. current technical form: `html` / `vue` / monolithic page / bridge / partial runtime
5. what must remain unchanged
6. which platform or runtime capabilities must be added
7. whether templates or old template data are involved
8. whether staged delivery is allowed

Default recommendations:

- prefer progressive refactoring over complete rewrite
- keep the existing `appKey`, parameter semantics, and export behavior stable unless the user asks to change them
- reuse old rendering and state logic if possible
- keep old entry points, old bridge, and old template data compatible unless the user agrees to retire them

Refactor strategy order:

1. adapter compatibility
2. runtime wrapper
3. starter-based restructuring
4. complete rewrite

Do not default to a complete rewrite unless the user explicitly wants to start over.

## Hard Constraints

These are hard constraints, not just defaults:

1. `generator-sdk` handles platform capabilities, not page shell or embedding protocol
2. for new generators, the default full-mode host must mount `generator-workbench` unless the user explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery
3. do not hand-write a custom top bar, template action shell, login entry, or floating export shell for a new generator when `generator-workbench` should be used
4. a standard generator must expose runtime interfaces instead of only exporting a page
5. the parameter panel must come from `PanelSchema`, and hosts must be able to consume partial parameters through `PanelFilter`
6. template pages consume runtime; they do not copy generator-private DOM or private business logic
7. template import must take effect through runtime `setState()` / `patchState()` + `panelFilter`
8. template JSON must use a unified template protocol; do not invent generator-private field names freely
9. in `embed` mode, do not render top navigation or shell-level platform entries, and do not depend on global `body` layout
10. before the completion gate is satisfied, do not claim "completed to the full standard"

## Completion Gate

Do not claim any of the following unless this gate is satisfied:

- `the standardization refactor is complete`
- `this old generator has already become a standard generator`
- `this has been completed to the full creating-generators standard`

`standardization refactor complete` requires all of the following:

- the required platform capabilities for this task are integrated through `generator-sdk`
- the complete `Generator Runtime Contract` is exposed
- working `full` / `embed` dual entry points are provided, unless the user explicitly waives one
- for new generators, the full-mode host uses `generator-workbench`, unless the user explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery
- `window.__GENERATOR_RUNTIME__` is exposed
- `getPanelSchema()` is provided and can be filtered by the host
- if templates are involved, a unified template protocol is integrated
- compatibility, migration, or CMS-related notes are included

If the current work is only:

- a compatibility refactor
- a staged delivery
- `sdk-only`, `runtime-only`, or `template-only` integration
- partial runtime or lightweight `PanelSchema`

then say so explicitly and also state that standardization has not yet been completed.

## Required Wrap-Up

After a generator task, the final response must include:

- `Task framing`: new build / compatibility refactor / standardization refactor / reduced-scope task
- `Current stage`: for old generator work, Stage 1/2/3/4 or equivalent partial state; for new generators, `not completed` or `reached standardization completion state`
- `Completed items`
- `Incomplete items`
- `Can standardization be claimed`: yes / no
- `Blockers or risks`
- `Compatibility and migration notes`
- `MCP path used`: existing MCP / installed MCP / documentation fallback

If `Can standardization be claimed = no`, explicitly say that the result has not yet reached the standardization completion state.

## Common Mistakes

- treating weak signals as automatic trigger without one clarifying question
- asking a non-technical user to install `generator-sdk-mcp` manually before checking whether the AI can do it
- installing MCP without verifying that the server or tools are actually available
- stopping the task because MCP is unavailable instead of using documentation fallback
- creating a custom full-page shell for a new generator instead of mounting `generator-workbench`
- treating "minimal SDK capabilities" as permission to skip `generator-workbench`
- treating `generator-workbench` as a later enhancement instead of the default full-mode host path for new generators
- generating only `full` and forgetting `embed`
- missing `getPanelSchema()`
- hardcoding the panel so the host cannot filter fields
- rendering navigation or platform buttons in `embed`
- rewriting an old generator before checking compatibility constraints
- deriving the development `appKey` from the generator name instead of using `dev_<random>`
- inventing private template JSON instead of using a unified template protocol
- saying Stage 1/2 or reduced-scope delivery already equals standardization completion

## Implementation References

- See [reference.md](reference.md) for capability mapping, runtime interface, bridge mapping, template protocol, and directory skeleton
- See `https://generator-sdk.pages.dev/zh/mcp/` for MCP installation/configuration
- Use `https://generator-sdk.pages.dev/zh/guide/` as the documentation fallback when MCP cannot be installed
