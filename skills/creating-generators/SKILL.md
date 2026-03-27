---
name: creating-generators
description: Use when creating, scaffolding, extending, refactoring, migrating, or standardizing a generator, including requests like "generator", "house generator", "frame generator", "pendant generator", "nameplate generator", "left tools + center canvas + right parameter panel", "template page embedding", "generator-sdk", "appKey", "runtime contract", "PanelSchema", "full/embed", or adding platform capabilities such as login, SVG export, Open in Studio, credits, billing, cloud save, or history. Prefer this skill over generic frontend page workflows whenever the request is for a generator.
---

# Creating Generators

## Quick Identification

Use this skill immediately if any of the following applies:

- the user asks to "build a generator" or "develop a type of generator"
- the user mentions "house generator / frame generator / pendant generator / nameplate generator"
- the user asks for a "left tools + center canvas + right parameter panel" layout or a similar three-column generator workspace
- the user mentions `generator-sdk`, `appKey`, `runtime contract`, `PanelSchema`, or `full/embed`
- the user requires the generator to support both a full page and template page embedding
- the user wants the generator to integrate login, SVG export, Open in Studio, credits, unified billing, cloud save, or history

If the request matches both "generic frontend page development" and "generator development", prefer this skill.

If the request only describes a "left-center-right layout + canvas + parameter panel" but does not explicitly say it is a generator, first ask whether it needs `generator-sdk`, runtime support, `full/embed`, or template page embedding. If not, do not misuse this skill.

## Minimum Matching Template

If another AI only scans the first 30 lines, it should at least catch this rule:

```text
As long as the user mentions "generator / build a generator / make a generator / frame generator / pendant generator / nameplate generator / left-center-right + canvas + parameter panel / generator-sdk / appKey / PanelSchema / full/embed / Open in Studio / cloud save / history / template page embedding / old generator refactor", prefer this skill and do not handle it as generic page development first.
```

## Common Conversational Trigger Phrases

Pay special attention to these natural-language expressions:

- direct intent: `build a generator`, `make a generator`, `help me create a generator`, `develop a generator`
- generator types: `house generator`, `frame generator`, `pendant generator`, `nameplate generator`, `template generator`
- layout expressions: `tools on the left, canvas in the middle, parameters on the right`, `three-column workspace`, `canvas in the center with a right-side parameter panel`
- platform capabilities: `integrate login`, `integrate SVG export`, `integrate Open in Studio`, `add credits`, `cloud save`, `history`
- runtime expressions: `support full/embed`, `template page embedding`, `connect the runtime contract`, `connect PanelSchema`
- refactor expressions: `refactor the old generator`, `upgrade the old generator`, `connect the old generator to the sdk`, `standardize the old generator`

## First Response Rules

Once a request is identified as a generator request, do not start with generic page implementation, component selection, or campaign page layout work.

You must first route it through the generator workflow and ask only one question at a time:

- first determine whether it is a "new generator" or an "old generator refactor"
- for the new-generator branch, ask at minimum: generator name -> auto-generate development `appKey` -> `html` / `vue` -> SDK capabilities
- for the refactor branch, ask at minimum: task framing (`compatibility refactor` / `standardization refactor`) -> old code location -> existing `appKey` / production identifier -> current technical form -> compatibility constraints

## Typical Trigger Examples

Should trigger:

- Help me build a house generator
- Build a pendant generator in HTML
- Make a frame generator with tools on the left, canvas in the center, and parameters on the right
- Integrate Open in Studio into this generator
- Make this generator support both `full` and `embed`
- Refactor an old generator into a standard generator that supports runtime and template pages

Should not trigger:

- Build a normal static campaign page
- Build a house showcase page, but not a generator
- Fix the styles of a normal form page
- Build a marketing landing page that only displays product information

## When Not To Use This Skill

Do not use this skill by default in the following cases:

- it is explicitly stated that the task is "not a generator", and is only a normal page, campaign page, showcase page, or form page
- the request only involves general UI, styling, or layout details and does not involve `generator-sdk`, runtime, `full/embed`, or template page embedding
- the request is only about normal frontend component development, with no generator state, canvas, parameter panel, or host embedding semantics

## Overview

Whether you are creating a new generator or refactoring an old one, the default goal is not "to build a complete page", but "to build a standard generator that can be consumed by different hosts and can evolve over time".

Two layers of capability must both be satisfied:

- platform capabilities are integrated through `generator-sdk`
- embedding capabilities are exposed through the `Generator Runtime Contract`

If the user explicitly mentions templates, template pages, sub-generators, preset parameters, allowing only part of the parameters to be adjustable, publishing templates, or importing templates, then standard template capability must also be enabled instead of improvising a temporary JSON structure and UI logic.

## Task Framing And Completion State

Requests involving old generators must first distinguish between two task framings, and they must not be mixed together:

- `compatibility refactor`: the goal is to keep existing features, interactions, styles, and export behavior stable while adding SDK integration, a bridge adapter, or partial runtime with as few changes as possible
- `standardization refactor`: the goal is to converge the old generator into a standard generator structure that ultimately satisfies `generator-sdk`, the complete runtime contract, `full` / `embed`, `PanelSchema` / `PanelFilter`, and template protocol support when needed

If the user says both "old generator refactor" and "integrate `generator-sdk`" but does not clearly say whether this is "minimum compatibility work" or a "standardization refactor", you must first ask which framing applies. Do not assume "do a little first and see later".

If the user explicitly requires:

- all features, interactions, and styles must remain unchanged
- integrate `generator-sdk`
- follow the `creating-generators` skill

but does not say "staged delivery is allowed" or "only add part of it first", then the default interpretation should be `standardization refactor`, not just a lightweight bridge wrapper.

## Standardization Completion Gate

The following gate is used to restrict completion claims by AI.

If any of the following is missing, do not claim:

- "the standardization refactor is complete"
- "this has been completed to the full `creating-generators` standard"
- "this old generator has already become a standard generator"

`standardization refactor complete` requires at least all of the following:

- the required platform capabilities for this task have been integrated through `generator-sdk`
- the complete `Generator Runtime Contract` has been exposed
- working `full` / `embed` dual entry points have been provided; if the user explicitly waives one of them, this must be stated separately in the wrap-up
- `window.__GENERATOR_RUNTIME__` has been exposed
- `getPanelSchema()` has been provided, and the host can consume partial parameters through `PanelFilter`
- if the task involves templates, template pages, sub-generators, or template import/export, a unified template protocol has been integrated instead of a private JSON format
- compatibility, migration, or CMS-related notes have been added

If the current work only completes a compatibility refactor, SDK integration, partial runtime, a lightweight `PanelSchema`, or a stage 1/2 delivery, it must be explicitly described as:

- "staged delivery has been completed"
- "the compatibility refactor has been completed"
- "the standardization refactor has not yet been completed"

Do not describe "partially aligned with the skill" as "completed according to the skill".

## When To Use

This skill must be used in the following scenarios:

- creating a new generator
- refactoring an existing generator or cleaning up an old generator written by someone else through vibe coding
- adding `generator-sdk` capabilities to an old generator
- adding `full` / `embed` / runtime interfaces to an old generator
- making the generator run in both a full page host and a template page host
- generating an HTML or Vue starter
- designing a template page integration where the parameter panel renders only part of the fields
- the user only says "help me build a generator / frame generator / pendant generator"

Do not skip this skill in these scenarios:

- "build only the full page first, embed can come later"
- "hardcode the platform buttons in the HTML first"
- "build a separate parameter panel just for the template page"
- "generate a complete page first, then add the runtime protocol later"
- "keep stacking features onto the old generator first, we can standardize it later"

## Automatic Flow

After identifying an intent to "develop a generator" or "refactor a generator", do not require the developer to add a fixed prompt manually. Enter the following guided flow directly and ask only one question at a time.

### Step 0: Identify The Task Type First

You must first determine which kind of task this is:

- create a new generator
- refactor an existing generator
- compatibility refactor
- standardization refactor
- add platform capabilities to an existing generator
- add runtime / `full` / `embed` to an existing generator
- add template-authoring capability to an existing generator

If the developer does not state this proactively, ask for the task type first instead of defaulting to "create a new generator".

### Branch A: New Generator Flow

#### Step 1: Confirm The Generator Name

You must ask for the generator name first.

The generator name is used for:

- page title
- directory name / example name
- default copy
- optional slug / code suggestions

#### Step 2: Generate A Default Development `appKey`

You must make the following clear:

- for new generators, generate a development `appKey` automatically
- use the format `dev_<random>`
- write it directly into `GeneratorSDK.init({ appKey })`
- do not block the developer just because the real platform `appKey` is not ready yet

Do not ask the developer for a real platform `appKey` in the new-generator branch unless they explicitly want to pin a specific value.

If the task is refactoring an existing generator, ask whether the existing production `appKey` must be reused.

#### Step 3: Confirm The Tech Stack

Ask the developer whether to use:

- `html`
- `vue`

Default recommendation:

- rapid vibe coding / static asset delivery -> `html`
- engineered projects / Vue ecosystem -> `vue`

#### Step 4: Ask Which SDK Capabilities Need To Be Integrated

Ask as a multi-select among the following capabilities:

- login
- SVG export
- Open in Studio
- credits
- unified billing
- cloud save
- history

After selection, map them to the `features` parameter of `generator-sdk-mcp`.

#### Step 5: Confirm Whether Template-Authoring Capability Is Needed

After the first four questions, you must determine whether template-authoring capability is needed.

If the user explicitly mentions any of the following keywords:

- template
- template page
- sub-generator
- preset parameters
- only part of the parameters may be adjusted
- publish template
- import template

then the default behavior is:

- automatically enable the `template` feature
- generate a unified template JSON protocol by default
- generate a "publish template / import template" interaction skeleton by default
- require template consumers to trim parameters through `panelFilter` by default

If the user does not explicitly mention the above scenarios, it is enough to remain "template-ready"; do not force a template button UI to be generated.

#### Step 6: Confirm Whether To Use A Runtime Starter

The default recommendation is to use a runtime starter, and it should be preferred.

If the developer does not explicitly refuse, handle it directly as "use a runtime starter":

- `html` -> `starter-html-runtime`
- `vue` -> `starter-vue-runtime`

#### Step 7: Automatically Enable `full` / `embed` Dual Mode

This is a default requirement and does not need to be separately requested by the developer.

As long as this is a new generator, you must generate by default:

- `full`
- `embed`

and it must support the template page scenario with canvas on the left and a partial parameter panel on the right by default.

#### Step 8: Automatically Integrate Runtime Interfaces

A new generator must include the following interfaces by default, without requiring the developer to ask for them manually:

- `mount({ mode, target, container, state, panelFilter })`
- `getState()`
- `setState()`
- `patchState()`
- `getPanelSchema()`
- `export(action)`
- `subscribe(listener)`

#### Step 9: Guide The Upload Process After Completion

After code generation is completed, you must prompt the developer to open the xtool generator upload page:

- [xtool generator upload](https://www.atomm.com/generator-upload)

Also tell the user that the resources need to be packaged as a zip file, and the zip file must contain an HTML file.

- `appKey`
- generator name
- sharing-growth related configuration items
- credits / free-usage related configuration items

### Branch B: Old Generator Refactor Flow

#### Step 1: Confirm The Generator Name And Code Location

You must first confirm:

- generator name
- repository or directory of the code
- whether the current work targets the main entry, embedded entry, or template page host

If the developer does not provide the location of the old code, ask for the path or repository information first. Do not assume the directory structure.

#### Step 1.5: Confirm The Refactor Framing

Before entering implementation for the old generator, you must make clear which of the following this task is:

- `compatibility refactor`: only requires no regressions plus some added capabilities, and allows staged delivery
- `standardization refactor`: requires ultimately reaching the completion state of a standard generator

If the user does not make this explicit, you must ask:

- "Is this only a minimal compatibility upgrade, or should it converge to a standard generator structure in one pass?"

Before this is confirmed, do not automatically treat "refactor the old generator" as "only add a lightweight runtime bridge".

#### Step 2: Confirm The Real `appKey` And Production Identifier

You must confirm:

- whether a real `appKey` already exists in production
- whether the current `appKey` must be reused
- whether CMS configuration, credits configuration, or sharing configuration already exists

When refactoring an old generator, the default preference is to reuse the existing real identifier rather than generating a new `appKey`.

#### Step 3: Identify The Current Technical Form

You must ask or inventory which form the current generator belongs to:

- `html`
- `vue`
- monolithic full page
- existing `window.__GENERATOR_BRIDGE__`
- partial runtime already exists
- multi-page or host-coupled structure

Do not treat the old generator by default as a blank project that can directly adopt a starter.

#### Step 4: Inventory Existing Capabilities And Gaps

You must make the current state and target gap explicit:

- which of login, export, Studio, credits, cloud save, and history already exist
- whether `embed` already exists
- whether a template page or template JSON already exists
- where current state management, export logic, and parameter panel logic live
- which capabilities should be added this time and which should not

#### Step 5: Confirm Compatibility Constraints

You must make the following clear:

- whether the existing UI and export results must remain consistent
- whether the old entry URL or embedding method must be preserved
- whether old bridge / postMessage / template data must remain compatible
- whether directory reorganization is allowed
- whether staged refactoring is allowed instead of a one-shot rewrite

If these constraints are not confirmed, do not go straight into the path of "rewrite it into a new starter".

#### Step 6: Confirm Template-Authoring Capability And Old Template Handling

If the old generator already has template capability, you must make clear:

- whether old template data should continue to be supported
- whether old template format should be converted or abandoned
- how template import maps into runtime `setState()` / `patchState()` + `panelFilter`

If the user mentions template pages, publishing templates, importing templates, sub-generators, preset parameters, or allowing only part of the parameters to be adjustable, enable the `template` feature by default.

#### Step 7: Choose The Refactor Strategy

By default, prefer "progressive refactoring", evaluating in the following order:

1. `adapter` compatibility: if the old bridge can still be reused, map it to the runtime first
2. runtime wrapper: reuse the old core rendering and state, and only add standard interfaces and host capabilities
3. starter-based refactor: keep the business logic and reorganize the project structure
4. complete rewrite: use only when the old code is unmaintainable or the target has changed too much

Do not default to a complete rewrite unless the developer explicitly requests starting over.

#### Step 8: Define Staged Delivery And Acceptance Criteria

For old generator refactors, the default expectation is to define stage goals first:

- Stage 1: no regression in existing features
- Stage 2: fill in runtime interfaces
- Stage 3: fill in `full` / `embed`
- Stage 4: fill in template capability or platform capability

You must provide at least one acceptance checklist covering:

- whether old core features remain intact
- whether both `full` / `embed` run correctly
- whether the host consumes parameters through `PanelSchema` + `PanelFilter`
- whether template import takes effect through the runtime
- whether CMS / `appKey` / historical data require migration notes

If this task is confirmed as a `standardization refactor`, then staged delivery is allowed only as an execution path and not as the final completion framing.

In other words:

- you may finish Stage 1/2 first and continue later
- but before Stage 3/4 is complete, you must not claim "the standardization refactor is complete"

#### Step 9: Output Refactor Wrap-Up Notes After Completion

After the refactor is completed, you must output:

- refactor strategy
- which old capabilities were preserved
- which old implementations were replaced
- whether a bridge compatibility layer is still retained
- whether CMS / data / template migration actions are needed

If production configuration is involved, also prompt the developer to check the CMS page:

- [xtool CMS admin](https://dev-web.makeblock.com/xcs-cms/index.html#/aimake/generator-management)

## Core Rules

1. `generator-sdk` is only responsible for platform capabilities, not for the page shell or embedding protocol.
2. Whether creating or refactoring, if the target is a standard generator, it must expose runtime interfaces instead of only exporting a page.
3. The parameter panel must come from `PanelSchema`, and template pages must render partial parameters through `PanelFilter`.
4. Template pages consume the runtime protocol and do not copy the generator's internal DOM directly.
5. New generators should prefer a runtime starter instead of assembling the structure from blank HTML.
6. Old generators should prefer progressive refactoring and should not default to a full rewrite.
7. For new generators, auto-generate a development `appKey`; for refactors, confirm whether the existing production `appKey` must be reused.
8. Whenever template-authoring capability is involved, a unified template protocol must be enabled, and template JSON field names must not be freely invented.
9. Template import must take effect through runtime `setState()` / `patchState()` + `panelFilter`.
10. Do not implement template import as "directly replace the DOM".
11. Do not make template capability private to the current generator only.
12. When refactoring an old generator, prefer keeping the existing `appKey`, core parameter semantics, and export behavior stable unless the developer explicitly asks to change them.
13. Before the "standardization completion gate" is satisfied, do not claim "completed to the full standard".
14. "Followed the skill" does not mean "did part of it"; if only staged delivery is done, the current stage must be explicitly stated.
15. If the user has not authorized staged delivery, do not treat "SDK + lightweight bridge" as the default final state of the task.

## Default Workflow

1. Identify the intent to "develop a generator" or "refactor a generator"
2. Determine first whether it is "new" or "refactor"
3. New branch: ask for name -> auto-generate development `appKey` -> tech stack -> SDK capabilities -> template-authoring capability -> default to a runtime starter
4. Refactor branch: ask for name and code location -> `appKey` / production identifier -> current technical form -> existing capabilities -> compatibility constraints -> template handling -> refactor strategy
5. Integrate `generator-sdk`
6. Implement or fill in the `Generator Runtime Contract`
7. Produce `PanelSchema`
8. If template-authoring capability is needed, enable the `template` feature and output a publish/import template skeleton or compatibility plan
9. Verify that the template page can render only the canvas and part of the parameters
10. Output the CMS configuration entry, migration notes, and acceptance checklist

## Minimum Deliverables

If the target is a standardized generator, it must by default have at least:

- `mount({ mode, target, container, state, panelFilter })`
- `getState()`
- `setState()`
- `patchState()`
- `getPanelSchema()`
- `export(action)`
- `subscribe(listener)`

If it is a progressive refactor of an old generator, you must additionally make clear at least:

- whether the old bridge adapter is retained
- which old entry points remain compatible
- which capabilities are filled in by stage

If the user requires a `standardization refactor`, then the above "minimum deliverables" are only the lower bound and not a substitute for the completion gate; completion claims must still follow the "standardization completion gate".

## Structural Constraints

Recommended directory skeleton:

```txt
src/
  core/
  runtime/
  pages/
    full.*
    embed.*
  platform/
    sdk.*
```

For a pure HTML generator, you must still separate at least:

- generator core state and rendering logic
- the `full` page entry
- the `embed` page or runtime entry
- the `window.__GENERATOR_RUNTIME__` registration logic

For a progressive refactor of an old generator, it is acceptable to keep the existing directory first, but the following boundaries should gradually be split clearly:

- core state and rendering logic
- runtime adapter layer
- platform capability integration layer
- host entry layer

## Platform Capability Integration Rules

The following capabilities must be implemented through `generator-sdk`:

- login
- cloud save / restore
- history
- credits
- unified billing
- download to local
- open in Studio

Do not hardcode these capabilities as custom postMessage protocols or page-private logic.

## How To Explain `appKey` To Developers

When explaining `appKey`, use the following facts:

- `appKey` is written into `GeneratorSDK.init({ appKey, env: 'prod' })`
- for new generators, the default generated value is a development `appKey` in the form `dev_<random>`
- for old generators, the production `appKey` may need to be reused
- later CMS sharing-growth and credits / free-usage configuration may still be built around the real platform identifier

Do not explain `appKey` as "an alias of the generator name".

## Embedding Rules

In `embed` mode, the generator must:

- not render the top navigation
- not render shell-level entries such as login / sharing / credits
- not charge by itself
- not depend on the global `body` layout
- respond to container size changes
- share the same business state as `full` mode

## Template Page Integration Rules

A template page should do only four things:

- mount the canvas
- get and filter `PanelSchema`
- synchronize state and export through the runtime
- consume `sdk.template.toRuntimeSnapshot()` or `sdk.template.applyToRuntime()` in template import scenarios

A template page should not:

- handwrite another set of business parameter logic
- read the generator's internal DOM directly
- assume a generator always contains a certain fixed field
- design another template JSON protocol by itself

## Default Question Order

If the developer only says "help me build a generator", ask in this order:

1. Is this a new generator or a refactor of an existing one?
2. If it is new: what is the generator name?
3. If it is a refactor: is this a `compatibility refactor` or a `standardization refactor`?
4. If it is a refactor: where is the old code directory or repository?
5. If it is a new generator: auto-generate a development `appKey`. If it is a refactor: ask whether the existing production `appKey` should be reused.
6. Use `html` or `vue`? If it is an old generator, also ask whether the current form is a full page, bridge-based, or partial-runtime structure.
7. Which SDK capabilities need to be integrated? Multi-select: login, SVG export, Open in Studio, credits, unified billing, cloud save, history.
8. Is template-authoring capability needed? If the task involves templates, template pages, sub-generators, preset parameters, or allowing only part of the parameters to be adjusted, enable the `template` feature by default.
9. If it is an old generator, must it remain compatible with the old entry point, old template data, old bridge, or existing export results?

After asking these questions, do not require the developer to add fixed prompt wording. Execute the default constraints for the corresponding branch directly.

If the developer says "refactor the old generator and integrate `generator-sdk`", upgrade the default question order to:

1. Is this a `compatibility refactor` or a `standardization refactor`?
2. Where is the old code directory or repository?
3. What is the current production `appKey`? Should the original `appKey` be reused in production?
4. What is the current technical form: `html` / `vue` / monolithic page / bridge / partial runtime?
5. Which capabilities must remain completely unchanged?
6. Which platform capabilities need to be integrated through `generator-sdk`?
7. Does this involve templates / template pages / compatibility for old template data?
8. Is staged delivery allowed? If yes, what is the final target stage?

## Required Wrap-Up Template

After completing a generator task, the final response must explicitly include the following fields and must not only say "completed":

- `Task framing`: new build / compatibility refactor / standardization refactor
- `Current stage`: if it is an old generator refactor, fill in Stage 1/2/3/4; if it is new, fill in "not completed" or "reached the standardization completion state"
- `Completed items`
- `Incomplete items`
- `Can standardization be claimed`: yes / no
- `Blockers or risks`
- `Compatibility and migration notes`

If `Can standardization be claimed = no`, you must additionally include:

- "The current result has not yet reached the standardization completion state and should not be described as a completed standard generator migration."

## Implementation References

- See [reference.md](reference.md) for the runtime protocol
- See `generator-sdk/docs/guide/runtime-contract.md` for platform capability references
- See `generator-sdk/generator-sdk-mcp/README.md` for the MCP entry

## Common Mistakes

- generating only the full page and not providing an `embed` entry
- missing `getPanelSchema()` in the runtime interfaces
- hardcoding the parameter panel in the page so it cannot be filtered
- still rendering navigation and platform buttons in `embed` mode
- extending a new generator with old bridge thinking instead of implementing runtime directly
- rewriting the old generator immediately without first checking the current state and compatibility constraints
- deriving the generated development `appKey` from the generator name instead of using a random `dev_<random>` value
- inventing JSON fields freely in template scenarios instead of using a unified template protocol
- completing only Stage 1/2 but saying in the wrap-up that the standardization refactor has already been completed according to the skill
- defaulting to an "SDK + lightweight bridge" state without the user's authorization for staged delivery
- failing to distinguish `compatibility refactor` from `standardization refactor`, causing the target and acceptance framing to become confused
