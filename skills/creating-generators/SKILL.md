---
name: creating-generators
description: Use when a request is about building or refactoring a generator with generator-sdk, runtime contexts such as full/embed under generator-workbench, PanelSchema, template pages, or old-generator standardization.
---

# Creating Generators

## Quick Identification

Use this skill when the request is clearly about a generator rather than a normal page.

### Strong Triggers

- the user explicitly says `generator`, `build a generator`, `create a generator`
- generator types such as `house generator`, `frame generator`, `pendant generator`, `nameplate generator`
- the user mentions `generator-sdk`, `runtime contract`, `PanelSchema`, `full/embed` context, template page embedding, or old generator standardization
- the task is to add generator platform capabilities such as login, SVG export, Open in Studio, credits, billing, cloud save, or history

### Weak Triggers

- `left tools + center canvas + right parameter panel`
- `canvas in the middle`
- `parameter panel`
- `appKey`
- `history`

If only weak triggers appear, ask one clarifying question first:

`Is this a parameter-driven generator or customization tool that needs to run inside the generator platform shell, with capabilities such as login, billing, credits, export, full/embed runtime contexts, template-page embedding, or old-generator standardization?`

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
- `Generator Runtime Contract` handles state, panel schema, export, and host integration across `full` / `embed` contexts

For new generators, default to the standard shell path:

- the runtime starter provides the generator runtime structure
- `generator-workbench` provides the default `full` host context, and the default host shell should use `mode: 'shell'` unless the user explicitly wants the classic split sidebar layout
- `generator-workbench` route `?mode=embed` provides the default `embed` host context when embedding is needed
- generator-specific business logic stays inside runtime, rendering, and panel-schema modules

For a new generator in initial development, `generator-workbench` is mandatory for the default `full` context.

The AI must not build a custom full-page shell unless the user explicitly opts out of the official host shell.

Missing MCP, starter code, or examples is not a waiver for skipping `generator-workbench`.

`full/embed` in this skill means runtime host contexts, not mandatory separate HTML entry files.

When `generator-workbench` is used, the default expectation is:

- `full` context: the workbench shows shell-owned chrome such as top bar / export / publish actions according to the chosen capability profile
- `embed` context: the workbench route uses `?mode=embed`, hides shell-owned chrome, and keeps bridge-compatible behavior for the host page
- runtime code must adapt to these contexts, but does not need to re-implement shell UI

For the default `html` path:

- do not require separate `full` / `embed` HTML entry files unless the host architecture explicitly needs them
- do not introduce `full.html` as a default concept
- if the project needs a dedicated embed page, treat that as an explicit entry strategy choice rather than the default standard
- do not turn the generator business into a Vue project by default
- if Vue is needed on the default `html` path, load it by CDN instead of switching to a Vue-project scaffold
- once `generator-workbench` is used on the default `html` path, default generator business UI to `atomm-ui`
- on that default path, load `atomm-ui` by CDN instead of switching to another component library or a package-managed UI stack unless the user explicitly asks for it
- once runtime business UI uses `atomm-ui` under `generator-workbench`, make sure the styles are available in the actual runtime mount root; page-level `<head>` CSS alone is not sufficient when the runtime is mounted into a Shadow Root or another isolated root
- keep `appKey` in a top-level `config.json` instead of hardcoding it into business modules

If the task involves templates, template pages, sub-generators, preset parameters, publish/import template, or partial-parameter editing, template capability must use a unified template protocol instead of generator-private JSON.

When the task specifically involves `generator-workbench`, determine the capability profile before assuming template capability:

- `workbench-basic`: shell capabilities only; login / export / Studio / credits / billing, but no cloud save, history, template publishing, or customize flow
- `workbench-cloud`: `workbench-basic` plus cloud save / restore and history, but still no template publishing or customize flow
- `workbench-template`: publish as template, template page embedding, customize flow, template import/export, sub-generators, preset parameters, or partial-parameter editing

Using `generator-workbench` does **not** by itself require template protocol. Only turn on template-authoring capability when the request explicitly needs publish-template, template-page embed, customize, template import/export, or a similar flow.

## Default Workflow

Ask only one question at a time. Use this single workflow as the authority for execution:

1. confirm this is really a generator task
2. bootstrap `generator-sdk-mcp`
3. determine task framing: new build / compatibility refactor / standardization refactor / reduced-scope task
4. if this is a compatibility refactor / standardization refactor / reduced-scope task, enter the Refactor Branch question sequence before implementation
5. if that refactor already uses `generator-workbench`, and the request is about adopting a newer workbench version or enabling a newer workbench capability, run the Workbench Upgrade Audit before implementation
6. if a refactor involves `generator-workbench`, determine the capability profile first: `workbench-basic` / `workbench-cloud` / `workbench-template`
7. if this is a new generator in initial development, default the capability profile to `workbench-basic`; only switch to `workbench-cloud` when cloud/history are explicitly needed, and to `workbench-template` when template-authoring flows are explicitly needed
8. if this is a new generator in initial development, lock the default `full` context to `generator-workbench`
9. only unlock that decision if the user explicitly says not to use the official host shell, or explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery
10. for a new generator, prefer the standard default path instead of asking configuration questions too early
11. on that default path, prefer `generator-workbench` `shell` mode so the runtime owns the full workspace area
12. treat `full/embed` as runtime host contexts first; only discuss separate entry files when the host architecture explicitly requires them
13. once `generator-workbench` is chosen on the default `html` path, default generator business UI to `atomm-ui` by CDN
14. when runtime business UI uses `atomm-ui` under `generator-workbench`, ensure the styles are injected into the actual runtime mount root instead of assuming page-level `<head>` CSS can cross Shadow DOM boundaries
15. only ask follow-up questions when the user explicitly requests a non-default path, a reduced scope, a custom shell, a real Vue project scaffold, another UI stack, extra platform capabilities outside the default bundle, a dedicated embed entry strategy, or the task does not yet clarify whether template publishing / template page embedding / customize flow are required
16. implement the generator runtime with starter + `generator-sdk` + runtime + `PanelSchema`
17. for new generators, keep the `generator-workbench` path even when MCP is missing
18. only enable the `template` feature when the request explicitly needs publish-template, template-page embed, customize, template import/export, sub-generators, preset parameters, or partial-parameter editing
19. treat `generate_code` as a reduced-scope helper rather than the main new-generator path
20. apply the completion gate and wrap up with the required status fields

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
- `standardization refactor`: converge to the standard generator shape with `generator-sdk`, complete runtime context support for `full/embed`, `PanelSchema`, and template protocol when needed

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

If the task is a new generator in initial development, the default `full` context must use `generator-workbench`.

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
- default context strategy: `generator-workbench` hosts `full`; `generator-workbench?mode=embed` hosts `embed` when embedding is needed
- default entry strategy: do not require a separate `embed.html` or `full.html` unless the host architecture explicitly needs it
- default platform bundle: `auth`, `credits`, `billing`, `export`
- explicit extra platform capabilities: `cloud`, `history`
- explicit template capability: `template` only when publish / template page / customize / template import-export is required
- app key config: top-level `config.json`
- runtime scope: support both `full` context and `embed` context
- runtime interfaces: `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, and `subscribe`
- export: registered via `sdk.export.register()` on the SDK side; `export` is NOT a method on the runtime object
- browser runtime exposure: `window.__GENERATOR_RUNTIME__`
- Vue usage on the default `html` path: CDN only, not a Vue-project scaffold
- business UI library after `generator-workbench` integration on the default `html` path: `atomm-ui`
- business UI component delivery on that path: CDN only unless the user explicitly requests another UI stack or an engineered scaffold

Ask follow-up questions only when at least one of the following is true:

1. the user explicitly requests a real `vue` project scaffold instead of the default `html` path
2. the user explicitly requests a reduced-scope build
3. the user explicitly requests a custom shell or runtime-only delivery
4. the user explicitly requests extra platform capabilities such as `cloud` or `history`
5. the request uses `generator-workbench` but does not yet clarify whether template publishing / template page embedding / customize flow are required
6. the request explicitly requires a dedicated embed page or non-default entry strategy
7. the request is actually an old-generator refactor rather than a new build

Default behavior:

- prefer `starter-html-runtime`
- keep `config.json` at the outermost directory and read `appKey` from it
- make the runtime support both `full` context and `embed` context
- expose `window.__GENERATOR_RUNTIME__`
- provide `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, and `subscribe` on the runtime object; register export via `sdk.export.register()`, not as a runtime method
- for new generators, default the host shell to `generator-workbench` in `mode: 'shell'` for `full` context
- keep `embed` context as workbench-hosted `?mode=embed` by default, with runtime rendering adapted to that context and without shell-level platform UI
- only add a dedicated `embed` page or another explicit entry file when the embedding architecture truly requires it
- default the workbench bundle to `auth`, `credits`, `billing`, and `export`
- only add `cloud` and `history` when the user explicitly needs cloud drafts or history flows
- only add `template` when the user explicitly needs publish-template, template-page embed/customize, template import/export, sub-generators, preset-parameter templates, or partial-parameter editing
- keep the default `html` path as a plain HTML runtime project; if Vue is used there, load it via CDN instead of turning the project into a Vue scaffold
- once `generator-workbench` is integrated on the default `html` path, default business UI to `atomm-ui`, and keep that UI path CDN-based unless the user explicitly asks otherwise
- only skip `generator-workbench` when the user explicitly waives the official host shell by asking for a reduced-scope build, a custom shell, or a runtime-only delivery

Default recommendations:

- default new-generator path -> `html` + `starter-html-runtime`
- Vue-dependent UI on the default path -> keep `html` + load Vue by CDN
- component-library UI on the default path after `generator-workbench` integration -> use `atomm-ui` + CDN
- explicit request for a dedicated Vue-project scaffold -> `vue` + `starter-vue-runtime`
- default SDK bundle -> `auth`, `credits`, `billing`, `export`
- explicit extra platform capabilities -> add `cloud` and/or `history`
- explicit template flow -> add `template`

If templates are part of the request, keep using the `template` feature, use a unified template protocol, and keep template pages parameter-driven through `panelFilter`.

If templates are **not** part of the request, do not introduce template publishing UI, template JSON protocol work, or customize flow just because `generator-workbench` is used.

## Refactor Branch

For an old generator, ask the minimum required questions in this order:

1. `compatibility refactor` or `standardization refactor`
2. code location / repository
3. whether the existing production `appKey` must be reused
4. current technical form: `html` / `vue` / monolithic page / bridge / partial runtime
5. what must remain unchanged
6. which workbench capability profile applies: `workbench-basic`, `workbench-cloud`, or `workbench-template`
7. which platform or runtime capabilities must be added
8. whether publish-template, template pages, customize flow, or old template data are involved
9. whether staged delivery is allowed

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

## Workbench Capability Profiles

When the task involves `generator-workbench`, classify the target into one of these profiles before deciding whether template capability is required:

- `workbench-basic`: login / export / Studio / credits / billing only
- `workbench-cloud`: `workbench-basic` plus cloud save / restore and history
- `workbench-template`: publish as template, template page embedding, customize flow, template import/export, sub-generators, preset parameters, or partial-parameter editing

Rules:

1. Using `generator-workbench` does **not** automatically require `workbench-template`
2. Do not add template publishing UI, template JSON work, or customize flow to a `workbench-basic` or `workbench-cloud` task
3. Only enable the `template` feature when the user explicitly needs a template flow or the request clearly depends on it

## Old Generator Refactor Stages

Use these stage labels only for old-generator work. They describe refactor progress inside this skill and are separate from the broader repository developer-journey stages.

| Stage | Meaning | Claim This Only When |
|---|---|---|
| `Stage 1` | platform baseline connected | `appKey` strategy is confirmed and the required `generator-sdk` capabilities for the current step are wired, but runtime standardization is still incomplete |
| `Stage 2` | runtime surface exposed | the runtime exposes or clearly wires `mount`, `getState`, `setState`, `patchState`, `getPanelSchema`, `export`, `subscribe`, and `window.__GENERATOR_RUNTIME__` |
| `Stage 3` | host-context alignment verified | the required `full/embed` behavior for this task is working, `PanelSchema`/`PanelFilter` consumption is wired, and the chosen host path or `generator-workbench` profile is verified |
| `Stage 4` | standardization completion state reached | the Completion Gate in this skill is satisfied, including template protocol and compatibility/migration notes when applicable |

If the delivery is compatibility-only, staged, or otherwise does not map cleanly to one of these stages, use an `equivalent partial state` label instead of inventing a stage number.

Preferred `equivalent partial state` labels:

- `compatibility refactor complete`
- `sdk-only integration complete`
- `runtime-only integration complete`
- `template-only integration complete`
- `staged delivery in progress`

## Hard Constraints

These are hard constraints, not just defaults:

1. `generator-sdk` handles platform capabilities, not page shell or embedding protocol
2. for a new generator in initial development, the default `full` host context must mount `generator-workbench` unless the user explicitly waives the official host shell
3. do not hand-write a custom top bar, template action shell, login entry, or floating export shell for a new generator when `generator-workbench` should be used
4. if the user has not explicitly waived the official host shell, the AI must not create a custom `full` page shell for a new generator in initial development
5. if the user has not explicitly waived the official host shell, the AI must not add custom shell-level top bars, login buttons, export buttons, Studio buttons, or floating action shells in the default `full` context
6. missing MCP, starter code, or examples is not permission to skip `generator-workbench`
7. for a new generator, any custom full-page shell requires an explicit user waiver that must be quoted in the final wrap-up
8. a standard generator must expose runtime interfaces instead of only exporting a page
9. the parameter panel must come from `PanelSchema`, and hosts must be able to consume partial parameters through `PanelFilter`
10. template pages consume runtime; they do not copy generator-private DOM or private business logic
11. template import must take effect through runtime `setState()` / `patchState()` + `panelFilter`
12. template JSON must use a unified template protocol; do not invent generator-private field names freely
13. in `embed` context, do not render top navigation or shell-level platform entries, and do not depend on global `body` layout
14. do not equate `full/embed` support with mandatory separate `index.html` / `embed.html` files
15. do not introduce `full.html` as a default concept; only use extra entry files when the host architecture explicitly requires them
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
- the runtime correctly supports both `full` context and `embed` context, unless the user explicitly waives one
- for new generators in initial development, the default `full` host context uses `generator-workbench`, unless the user explicitly waives the official host shell
- `window.__GENERATOR_RUNTIME__` is exposed
- `getPanelSchema()` is provided and can be filtered by the host
- if templates are involved, a unified template protocol is integrated
- when runtime business UI uses `atomm-ui`, the workbench-hosted `full` context is verified for actual runtime component styles, not just shell styles
- compatibility, migration, or CMS-related notes are included
- `config.json` exists at the project root with at least an `appKey` field
- the generator entry script reads `appKey` from `config.json` at runtime, not from a hardcoded string literal in source code

If `generator-workbench` is not used for a new generator in initial development, the final wrap-up must include the explicit user waiver. Otherwise, standardization cannot be claimed.

If the current work is only:

- a compatibility refactor
- a staged delivery
- `sdk-only`, `runtime-only`, or `template-only` integration
- partial runtime or lightweight `PanelSchema`

then say so explicitly and also state that standardization has not yet been completed.

## Validation Checklist

When the task touches `generator-workbench`, the final delivery must include the applicable validation checklist.

### `workbench-basic`

- verify that login / export / Studio / credits / billing match the requested scope
- verify that template publishing UI was not added when not requested
- verify that cloud/history flows were not added when not requested

### `workbench-cloud`

- verify that the runtime exposes a serializable `snapshot`
- verify that `getCloudSaveOptions()` or an equivalent host path exists when cloud save is in scope
- verify whether `cover` is provided; if not, explicitly state that cloud save is state-only without cover enhancement
- verify restore/delete behavior if history is enabled

### `workbench-template`

- verify the source of `generatorImage`, `generatorTag`, `template`, and `originImageUrl` when applicable
- verify that template JSON excludes private UI state
- state the embed level explicitly: `embed-basic`, `embed-canvas-only`, or `embed-canvas-panel`
- verify customize / template restore behavior when requested

## Behavioral Smoke Tests

Before claiming `workbench` integration is complete, verify ALL of the following behavioral points — not just code-level declarations.

### Pre-flight (all profiles)

- `workbench.sdk` is assigned to the workbench element (not just instantiated in a local variable)
- `workbench.runtime` is assigned to the workbench element
- `workbench.config.atommProEnv` is set (not `undefined`)
- `workbench.config.atommProDomain` is set (not `undefined`)
- `workbench.mount()` is called **after** all three assignments above
- `window.__GENERATOR_RUNTIME__` is assigned and points to the same runtime object

Missing any one of the above will cause the avatar click to be unresponsive.

### `workbench-basic` behavioral verification

- Avatar / user icon click → login modal appears
  - requires: `atommProEnv` + `atommProDomain` + `sdk` auth initialized + `workbench.sdk` assigned before `mount()`
- Export button click → download dialog or SDK export flow triggered
  - requires: `sdk.export.register()` called with a provider; `exportEnabled: true`
- Template publishing UI is **not** visible (`templateEnabled` absent or `false`)

### `workbench-cloud` behavioral verification

- All `workbench-basic` checks above
- Cloud save trigger → `getCloudSaveOptions()` returns an object with `{ title, snapshot }`
  - verify `snapshot` is JSON-serializable (no circular refs, no DOM nodes)
  - if `cover` is not provided, state this explicitly
- History button is visible and `historyEnabled: true` is set in config

### `workbench-template` behavioral verification

- All `workbench-cloud` checks above
- "Publish as Template" button is visible → `templateEnabled: true` must be in config
- Clicking "Publish as Template" → dialog opens
  - requires: runtime emits `params_change` with a `data.cover` field when state changes
  - requires: runtime emits `template_publish_media_change` with `generatorImage` when a template is selected
  - verify: `generatorImage`, `generatorTag`, `cover`, `originImageUrl` sources are documented (from runtime events or `getTemplatePublishPayload`)
- `select_template` event is emitted when a template preset card is clicked
- Customize / restore flow: runtime correctly handles `setState()` from an imported template snapshot

## Required Wrap-Up

After a generator task, the final response must include:

- `Task framing`: new build / compatibility refactor / standardization refactor / reduced-scope task
- `Current stage`: for old generator work, `Stage 1/2/3/4` from `Old Generator Refactor Stages`, or an explicit equivalent partial state; for new generators, `not completed` or `reached standardization completion state`
- `Completed items`
- `Incomplete items`
- `Can standardization be claimed`: yes / no
- `Blockers or risks`
- `Compatibility and migration notes`
- `MCP path used`: existing MCP / installed MCP / documentation fallback
- `Official host shell used`: yes / no
- `If no, explicit user waiver`: quote the user request or say `none`
- `Workbench integration evidence`: file paths, MCP path, or integration approach

After completing `generator-workbench` integration, generate `__tests__/workbench-integration.test.ts` using the test template from `reference.md` § Integration Test Template. Replace import paths to point to the actual runtime and config factories. Keep only the test blocks that match the current profile:

- `workbench-basic` → remove template-only and cloud-only blocks
- `workbench-cloud` → remove template-only blocks
- `workbench-template` → keep all blocks

Do **not** import anything from `@atomm-developer/generator-workbench/testing`. The generated test file must be self-contained and runnable with `npx vitest run`.

If `Can standardization be claimed = no`, explicitly say that the result has not yet reached the standardization completion state.

## Hard Stop: Official Host Shell Violations

Stop immediately and revise the approach if any of the following happens during a new generator initial build without an explicit waiver:

- creating a custom `full` page shell
- creating shell-owned `full` chrome in runtime code instead of using the standard workbench host context
- adding custom top bars, login buttons, export buttons, or Studio buttons in the shell
- treating `generator-workbench` as a later enhancement
- using "start simple first" or "just a demo first" as justification
- treating separate `full` / `embed` entry files as the mandatory default implementation
- converting the default `html` path into a Vue project instead of using Vue by CDN
- switching the default `html` + `generator-workbench` path to another component library or package-managed UI stack instead of keeping `atomm-ui` + CDN
- hardcoding `appKey` in runtime or business modules instead of reading the top-level `config.json`

If any of these occur, discard that shell plan and return to the `generator-workbench` path.

## Common Failure Patterns

The following symptoms indicate specific missing implementation steps. Use this table to diagnose and fix integration issues before delivery.

| Symptom | Cause | Fix |
|---|---|---|
| Avatar click → nothing happens | `atommProEnv` or `atommProDomain` not set; or `workbench.sdk` not assigned to the element before `mount()` | Set both fields in `workbench.config`; assign `sdk` to `workbench.sdk` before calling `workbench.mount()` |
| "Publish as Template" button is invisible | `templateEnabled` not set to `true` in config | Add `templateEnabled: true` to `workbench.config` |
| "Publish as Template" button click → dialog does not open | Runtime never emits `params_change` with a `cover` field, or `template_publish_media_change` never fires | Implement runtime event emission for these events; or configure `getTemplatePublishPayload` in the workbench config |
| Export button click → nothing happens | `sdk.export.register()` not called; or `exportEnabled: false` or missing | Call `sdk.export.register()` with a provider that returns export data; ensure `exportEnabled: true` |
| Cloud save → title is always `undefined` | `getCloudSaveOptions()` is missing or returns no `title` field | Implement `getCloudSaveOptions` and return `{ title, snapshot }` with a non-empty `title` |
| `config.json` missing / `appKey` hardcoded | AI concluded `config.json` is optional because `GeneratorSDK.init()` only reads `appKey` from its call-site parameter — it does **NOT** automatically read `config.json`. `config.json` is enforced by this skill, not by the SDK. Without it, production deployments cannot inject the real `appKey` without modifying source code. | Create `config.json` at project root with `{ "appKey": "..." }`. Read it in the entry script: `fetch('./config.json').then(r => r.json()).then(cfg => GeneratorSDK.init({ appKey: cfg.appKey }))`, or use `import config from '../config.json'` for bundled projects. |

## Common Mistakes

### High-Risk (stop and fix immediately)

- treating weak signals as automatic trigger without one clarifying question
- asking a non-technical user to install `generator-sdk-mcp` manually before checking whether the AI can do it
- installing MCP without verifying that the server or tools are actually available
- stopping the task because MCP is unavailable instead of using documentation fallback
- asking new-generator users for `appKey`, framework, SDK features, or template support before applying the standard default path
- enabling template capability just because `generator-workbench` is used, even though the user only asked for shell-level or cloud-level capabilities
- forcing publish-template UI, template JSON work, or customize flow into a `workbench-basic` or `workbench-cloud` task
- creating a custom full-page shell for a new generator instead of mounting `generator-workbench`
- treating `full/embed` as mandatory dual HTML entry files instead of runtime host contexts
- adding `full.html` or `embed.html` by default even though the actual host architecture does not require them
- treating "minimal SDK capabilities" as permission to skip `generator-workbench`
- treating `generator-workbench` as a later enhancement instead of the default full-mode host path for new generators
- treating user silence as permission to skip the official host shell
- treating missing MCP as permission to build a custom full shell
- building a demo shell first and planning to replace it with `generator-workbench` later
- adding shell-level login/export/Studio actions outside `generator-workbench` without an explicit waiver
- using `generate_code` as the main skeleton path for a new generator instead of `generate_runtime_starter`
- implementing only the workbench-hosted `full` context and forgetting that the runtime still needs `embed` context support
- missing `getPanelSchema()`
- hardcoding the panel so the host cannot filter fields
- rendering navigation or shell-level platform buttons in `embed` context
- after integrating `generator-workbench` on the default `html` path, still generating business UI with another component library or local package-managed UI stack instead of `atomm-ui` + CDN
- assuming that loading `atomm-ui` CSS in `index.html` automatically styles runtime business UI inside `generator-workbench`
- forgetting that `generator-workbench` may mount runtime panel or canvas into a Shadow Root or another isolated style root
- generating runtime business UI with `atomm-ui` components but without a runtime-side style injection strategy
- hardcoding `appKey` in source code instead of reading the outermost `config.json`
- inventing private template JSON instead of using a unified template protocol
- saying Stage 1/2 or reduced-scope delivery already equals standardization completion

### Lower-Risk (review before delivery)

- rewriting an old generator before checking compatibility constraints
- turning the default `html` path into a Vue project instead of keeping it HTML-first and loading Vue via CDN when needed
- copying complex iframe-host glue code from a legacy project into a generator that does not actually need it

## Implementation References

- See [reference.md](reference.md) for capability mapping, runtime interface, bridge mapping, template protocol, and directory skeleton
- See `https://generator-sdk.pages.dev/zh/mcp/` for MCP installation/configuration
- Use `https://generator-sdk.pages.dev/zh/guide/` as the documentation fallback when MCP cannot be installed
