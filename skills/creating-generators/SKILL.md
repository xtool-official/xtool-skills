---
name: creating-generators
description: Use when a request is about building or refactoring a generator with generator-sdk, runtime/full/embed, PanelSchema, template pages, or old-generator standardization.
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
- the full entry mounts `generator-workbench`, and the default host shell should use `mode: 'shell'` unless the user explicitly wants the classic split sidebar layout
- generator-specific business logic stays inside runtime, rendering, and panel-schema modules

For a new generator in initial development, `generator-workbench` is mandatory for `full` mode by default.

The AI must not build a custom full-page shell unless the user explicitly opts out of the official host shell.

Missing MCP, starter code, or examples is not a waiver for skipping `generator-workbench`.

For the default `html` path:

- `index.html` is the main entry for `full`
- `embed.html` is the entry for `embed`
- do not introduce `full.html` as a separate default entry unless the user explicitly requests it
- do not turn the generator business into a Vue project by default
- if Vue is needed on the default `html` path, load it by CDN instead of switching to a Vue-project scaffold
- once `generator-workbench` is used on the default `html` path, default generator business UI to `atomm-ui`
- on that default path, load `atomm-ui` by CDN instead of switching to another component library or a package-managed UI stack unless the user explicitly asks for it
- once runtime business UI uses `atomm-ui` under `generator-workbench`, make sure the styles are available in the actual runtime mount root; page-level `<head>` CSS alone is not sufficient when the runtime is mounted into a Shadow Root or another isolated root
- keep `appKey` in a top-level `config.json` instead of hardcoding it into business modules

If the task involves templates, template pages, sub-generators, preset parameters, publish/import template, or partial-parameter editing, template capability must use a unified template protocol instead of generator-private JSON.

## Default Workflow

Ask only one question at a time. Use this single workflow as the authority for execution:

1. confirm this is really a generator task
2. bootstrap `generator-sdk-mcp`
3. determine task framing: new build / compatibility refactor / standardization refactor / reduced-scope task
4. if this is a new generator in initial development, lock `full` mode to `generator-workbench` by default
5. only unlock that decision if the user explicitly says not to use the official host shell, or explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery
6. for a new generator, prefer the standard default path instead of asking configuration questions too early
7. on that default path, prefer `generator-workbench` `shell` mode so the runtime owns the full workspace area
8. on the default `html` path, use `index.html` for `full`, `embed.html` for `embed`, keep `config.json` at the project root, and do not add `full.html`
9. once `generator-workbench` is chosen on the default `html` path, default generator business UI to `atomm-ui` by CDN
10. when runtime business UI uses `atomm-ui` under `generator-workbench`, ensure the styles are injected into the actual runtime mount root instead of assuming page-level `<head>` CSS can cross Shadow DOM boundaries
11. only ask follow-up questions when the user explicitly requests a non-default path, a reduced scope, a custom shell, a real Vue project scaffold, another UI stack, or extra platform capabilities outside the default bundle
12. implement the generator runtime with starter + `generator-sdk` + runtime + `PanelSchema`
13. for new generators, keep the `generator-workbench` path even when MCP is missing, and use the template protocol when needed
14. treat `generate_code` as a reduced-scope helper rather than the main new-generator path
15. apply the completion gate and wrap up with the required status fields

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

### Official Host Shell Rule

If the task is a new generator in initial development, `full` mode must use `generator-workbench`.

Assume the official host shell is required unless the user explicitly states one of the following:

- do not use the official host shell
- use a custom shell
- runtime-only delivery
- reduced-scope build without the official host shell

The following do NOT count as a waiver:

- silence
- ambiguity
- "build a simple version first"
- "build a demo first"
- missing MCP or starter code
- "we can integrate `generator-workbench` later"

If there is no explicit waiver, building a custom full-page shell is a violation of this skill.

For a new generator in initial development, do not ask configuration questions by default when a safe standard path already exists.

Use the following defaults unless the user explicitly asks otherwise:

- generator type: standard new generator
- framework: `html`
- starter: `starter-html-runtime`
- official host shell: `generator-workbench` with `mode: 'shell'` by default
- feature bundle: `auth`, `credits`, `billing`, `export`, `template`
- full entry: `index.html`
- embed entry: `embed.html`
- `full.html`: unused by default unless the user explicitly requests it
- app key config: top-level `config.json`
- runtime scope: `full` + `embed`
- runtime interfaces: `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, `export`, and `subscribe`
- browser runtime exposure: `window.__GENERATOR_RUNTIME__`
- Vue usage on the default `html` path: CDN only, not a Vue-project scaffold
- business UI library after `generator-workbench` integration on the default `html` path: `atomm-ui`
- business UI component delivery on that path: CDN only unless the user explicitly requests another UI stack or an engineered scaffold

Ask follow-up questions only when at least one of the following is true:

1. the user explicitly requests a real `vue` project scaffold instead of the default `html` path
2. the user explicitly requests a reduced-scope build
3. the user explicitly requests a custom shell or runtime-only delivery
4. the user explicitly requests extra platform capabilities such as `cloud` or `history`
5. the request is actually an old-generator refactor rather than a new build

Default behavior:

- prefer `starter-html-runtime`
- use `index.html` as the `full` entry
- use `embed.html` as the `embed` entry
- do not add `full.html` unless the user explicitly asks for it
- keep `config.json` at the outermost directory and read `appKey` from it
- enable both `full` and `embed`
- expose `window.__GENERATOR_RUNTIME__`
- provide `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, `export`, and `subscribe`
- for new generators, default the full entry to a host page that mounts `generator-workbench` in `mode: 'shell'`
- keep `embed` mode as pure runtime rendering without shell-level platform UI
- enable the `workbench-standard` feature bundle: `auth`, `credits`, `billing`, `export`, `template`
- keep the default `html` path as a plain HTML runtime project; if Vue is used there, load it via CDN instead of turning the project into a Vue scaffold
- once `generator-workbench` is integrated on the default `html` path, default business UI to `atomm-ui`, and keep that UI path CDN-based unless the user explicitly asks otherwise
- only skip `generator-workbench` when the user explicitly waives the official host shell by asking for a reduced-scope build, a custom shell, or a runtime-only delivery

Default recommendations:

- default new-generator path -> `html` + `starter-html-runtime`
- Vue-dependent UI on the default path -> keep `html` + load Vue by CDN
- component-library UI on the default path after `generator-workbench` integration -> use `atomm-ui` + CDN
- explicit request for a dedicated Vue-project scaffold -> `vue` + `starter-vue-runtime`
- default SDK bundle -> `auth`, `credits`, `billing`, `export`, `template`
- explicit extra platform capabilities -> add `cloud` and/or `history`

If templates are part of the request, keep using the default `template` feature, use a unified template protocol, and keep template pages parameter-driven through `panelFilter`.

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

### Workbench Upgrade Audit Rule

When the task involves an old generator that already uses `generator-workbench`, and the request is about:

- adopting a newer `generator-workbench` version
- enabling a capability newly added to `generator-workbench`
- debugging why a CDN-updated generator did not fully gain a new workbench capability

you must treat this as a **workbench upgrade audit** before proposing implementation.

In that case, do this in order:

1. read `https://static-res.atomm.com/scripts/js/generator-sdk/generator-workbench/upgrade-manifest.json`
2. if the CDN manifest cannot be read, use MCP documentation as the fallback source for upgrade rules
3. identify the relevant release window, such as `0.1.5 -> 0.1.6`
4. classify the target capability into:
   - `auto-adopted`
   - `host-action-required`
   - `runtime-action-required`
5. scan the target generator for the required host/runtime markers
6. output a generator-specific upgrade checklist before implementation

Do not assume that updating the CDN URL means the generator has fully adopted the capability.

If the manifest says the capability is `host-action-required` or `runtime-action-required`, the generator must update its own host glue code, runtime adapter, config, or tests.

The upgrade checklist must explicitly state:

- what the shell update gives automatically
- what the generator host must still add
- what the runtime must still implement
- what smoke tests must be re-run

Default recommendations:

- prefer progressive refactoring over complete rewrite
- keep the existing `appKey`, parameter semantics, and export behavior stable unless the user asks to change them
- reuse old rendering and state logic if possible
- keep old entry points, old bridge, and old template data compatible unless the user agrees to retire them
- when the old generator already uses `generator-workbench`, prefer a manifest-driven upgrade audit before changing workbench timing or shell internals

Refactor strategy order:

1. adapter compatibility
2. runtime wrapper
3. starter-based restructuring
4. complete rewrite

Do not default to a complete rewrite unless the user explicitly wants to start over.

## Hard Constraints

These are hard constraints, not just defaults:

1. `generator-sdk` handles platform capabilities, not page shell or embedding protocol
2. for a new generator in initial development, the default full-mode host must mount `generator-workbench` unless the user explicitly waives the official host shell
3. do not hand-write a custom top bar, template action shell, login entry, or floating export shell for a new generator when `generator-workbench` should be used
4. if the user has not explicitly waived the official host shell, the AI must not create a custom `full` page shell for a new generator in initial development
5. if the user has not explicitly waived the official host shell, the AI must not add custom shell-level top bars, login buttons, export buttons, Studio buttons, or floating action shells in `full` mode
6. missing MCP, starter code, or examples is not permission to skip `generator-workbench`
7. for a new generator, any custom full-page shell requires an explicit user waiver that must be quoted in the final wrap-up
8. a standard generator must expose runtime interfaces instead of only exporting a page
9. the parameter panel must come from `PanelSchema`, and hosts must be able to consume partial parameters through `PanelFilter`
10. template pages consume runtime; they do not copy generator-private DOM or private business logic
11. template import must take effect through runtime `setState()` / `patchState()` + `panelFilter`
12. template JSON must use a unified template protocol; do not invent generator-private field names freely
13. in `embed` mode, do not render top navigation or shell-level platform entries, and do not depend on global `body` layout
14. on the default `html` path, `index.html` is the full entry and `embed.html` is the embed entry
15. do not introduce `full.html` as a separate default entry unless the user explicitly requests it
16. on the default `html` path, do not turn generator business code into a Vue project; use Vue by CDN if needed
17. once `generator-workbench` is integrated on the default `html` path, default generator business UI to `atomm-ui`; do not silently switch to another component library or a package-managed UI stack
18. keep `atomm-ui` on that default path CDN-based unless the user explicitly requests another delivery mode or an engineered scaffold
19. keep `appKey` in a top-level `config.json`; do not scatter or hardcode it through business modules
20. before the completion gate is satisfied, do not claim "completed to the full standard"
21. when `generator-workbench` is used and runtime business UI depends on `atomm-ui`, the implementation must ensure `atomm-ui` styles are available in the actual runtime mount root; page-level CDN CSS alone is not sufficient when runtime mounts into Shadow DOM or another isolated root

## Completion Gate

Do not claim any of the following unless this gate is satisfied:

- `the standardization refactor is complete`
- `this old generator has already become a standard generator`
- `this has been completed to the full creating-generators standard`

`standardization refactor complete` requires all of the following:

- the required platform capabilities for this task are integrated through `generator-sdk`
- the complete `Generator Runtime Contract` is exposed
- working `full` / `embed` dual entry points are provided, unless the user explicitly waives one
- for new generators in initial development, the full-mode host uses `generator-workbench`, unless the user explicitly waives the official host shell
- `window.__GENERATOR_RUNTIME__` is exposed
- `getPanelSchema()` is provided and can be filtered by the host
- if templates are involved, a unified template protocol is integrated
- when runtime business UI uses `atomm-ui`, `full` mode under `generator-workbench` is verified for actual runtime component styles, not just shell styles
- compatibility, migration, or CMS-related notes are included

If `generator-workbench` is not used for a new generator in initial development, the final wrap-up must include the explicit user waiver. Otherwise, standardization cannot be claimed.

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
- `Official host shell used`: yes / no
- `If no, explicit user waiver`: quote the user request or say `none`
- `Workbench integration evidence`: file paths, MCP path, or integration approach

If `Can standardization be claimed = no`, explicitly say that the result has not yet reached the standardization completion state.

## Hard Stop: Official Host Shell Violations

Stop immediately and revise the approach if any of the following happens during a new generator initial build without an explicit waiver:

- creating a custom `full` page shell
- creating a custom `index.html` shell for `full` mode instead of the standard entry that mounts `generator-workbench`
- adding custom top bars, login buttons, export buttons, or Studio buttons in the shell
- treating `generator-workbench` as a later enhancement
- using "start simple first" or "just a demo first" as justification
- introducing `full.html` as another default full entry
- converting the default `html` path into a Vue project instead of using Vue by CDN
- switching the default `html` + `generator-workbench` path to another component library or package-managed UI stack instead of keeping `atomm-ui` + CDN
- hardcoding `appKey` in runtime or business modules instead of reading the top-level `config.json`

If any of these occur, discard that shell plan and return to the `generator-workbench` path.

## Common Mistakes

- treating weak signals as automatic trigger without one clarifying question
- asking a non-technical user to install `generator-sdk-mcp` manually before checking whether the AI can do it
- installing MCP without verifying that the server or tools are actually available
- stopping the task because MCP is unavailable instead of using documentation fallback
- asking new-generator users for `appKey`, framework, SDK features, or template support before applying the standard default path
- creating a custom full-page shell for a new generator instead of mounting `generator-workbench`
- forgetting that `index.html` is the default `full` entry and `embed.html` is the default `embed` entry
- adding `full.html` by default even though the user did not ask for it
- treating "minimal SDK capabilities" as permission to skip `generator-workbench`
- treating `generator-workbench` as a later enhancement instead of the default full-mode host path for new generators
- treating user silence as permission to skip the official host shell
- treating missing MCP as permission to build a custom full shell
- building a demo shell first and planning to replace it with `generator-workbench` later
- adding shell-level login/export/Studio actions outside `generator-workbench` without an explicit waiver
- using `generate_code` as the main skeleton path for a new generator instead of `generate_runtime_starter`
- generating only `full` and forgetting `embed`
- missing `getPanelSchema()`
- hardcoding the panel so the host cannot filter fields
- rendering navigation or platform buttons in `embed`
- after integrating `generator-workbench` on the default `html` path, still generating business UI with another component library or local package-managed UI stack instead of `atomm-ui` + CDN
- assuming that loading `atomm-ui` CSS in `index.html` automatically styles runtime business UI inside `generator-workbench`
- forgetting that `generator-workbench` may mount runtime panel or canvas into a Shadow Root or another isolated style root
- generating runtime business UI with `atomm-ui` components but without a runtime-side style injection strategy
- rewriting an old generator before checking compatibility constraints
- turning the default `html` path into a Vue project instead of keeping it HTML-first and loading Vue via CDN when needed
- hardcoding `appKey` in source code instead of reading the outermost `config.json`
- inventing private template JSON instead of using a unified template protocol
- saying Stage 1/2 or reduced-scope delivery already equals standardization completion

## Implementation References

- See [reference.md](reference.md) for capability mapping, runtime interface, bridge mapping, template protocol, and directory skeleton
- See `https://generator-sdk.pages.dev/zh/mcp/` for MCP installation/configuration
- Use `https://generator-sdk.pages.dev/zh/guide/` as the documentation fallback when MCP cannot be installed
