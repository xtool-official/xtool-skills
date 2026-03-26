# Creating Generators Reference

## Runtime And Platform Capability Layers

| Layer | Responsible For | Not Responsible For |
|---|---|---|
| `generator-sdk` | login, cloud save, history, credits, billing, export, Studio | page shell, parameter panel structure, template page embedding |
| `Generator Runtime Contract` | `full/embed` modes, state, parameter schema, export interfaces, events | login dialogs, sharing dialogs, CMS, publishing |
| host page | navigation, left-side layout, template page structure, calling the runtime | defining the generator's internal business state |

## Step 0: Route By Task Type

After identifying an intent to "develop a generator", first determine which type of task it is:

1. create a new generator
2. refactor an existing generator
3. `compatibility refactor`
4. `standardization refactor`
5. add platform capabilities to an existing generator
6. add runtime / `full` / `embed` to an existing generator
7. add template-authoring capability to an existing generator

By default, do not equate "develop a generator" with "generate a new runtime starter".

## Task Framing And Completion State

Requests involving old generators must first distinguish between two task framings:

- `compatibility refactor`: the goal is to keep existing features, interactions, styles, and export behavior stable while adding SDK integration, an adapter, or partial runtime with minimal changes, and staged delivery is allowed
- `standardization refactor`: the goal is to converge the old generator into a standard generator that ultimately satisfies `generator-sdk`, the complete runtime contract, `full` / `embed`, `PanelSchema` / `PanelFilter`, and template protocol support when needed

If the user says both "old generator refactor" and "integrate `generator-sdk`" but does not make clear whether this is "minimum compatibility work" or a "standardization refactor", you must first ask for the framing and must not default to "do a little first and see later".

If the user explicitly requires:

- keep existing features, interactions, styles, and export behavior unchanged
- integrate `generator-sdk`
- execute according to the `creating-generators` skill

but does not authorize "staged delivery" or "only add part of it first", the default interpretation should be `standardization refactor`, not stopping at a lightweight bridge wrapper.

## Standardization Completion Gate

The following gate is used to restrict completion claims.

If any of the following is missing, you must not claim:

- "the standardization refactor is complete"
- "this has been completed to the full `creating-generators` standard"
- "this old generator has already become a standard generator"

`standardization refactor complete` requires at least all of the following:

1. the required platform capabilities for this task have been integrated through `generator-sdk`
2. the complete `Generator Runtime Contract` has been exposed
3. working `full` / `embed` dual entry points have been provided; if the user explicitly waives one of them, this must be stated separately in the wrap-up
4. `window.__GENERATOR_RUNTIME__` has been exposed
5. `getPanelSchema()` has been provided, and the host can consume partial parameters through `PanelFilter`
6. if the task involves templates, template pages, sub-generators, or template import/export, a unified template protocol has been integrated instead of a private JSON format
7. compatibility, migration, or CMS-related notes have been added

If the current work only completes a compatibility refactor, SDK integration, partial runtime, a lightweight `PanelSchema`, or Stage 1/2 delivery, it must be explicitly described as:

- "staged delivery has been completed"
- "the compatibility refactor has been completed"
- "the standardization refactor has not yet been completed"

Do not describe "partially aligned with the skill" as "completed according to the skill".

## Default Question Flow For The New-Generator Branch

Ask in the following order, and ask only one question at a time:

1. generator name
2. real platform `appKey`
3. tech stack: `html` or `vue`
4. SDK capability selection
5. whether template-authoring capability is needed

After the questions are completed, enter generation directly and do not require the developer to manually append fixed prompt wording.

## Default Question Flow For The Refactor Branch

Ask in the following order, and ask only one question at a time:

1. is this a `compatibility refactor` or a `standardization refactor`?
2. what is the old generator name?
3. where is the old code directory or repository?
4. what is the current or existing real `appKey`, and must it be reused?
5. what is the current technical form: `html` / `vue` / monolithic page / bridge / partial runtime?
6. which capabilities already exist: login, export, Studio, credits, cloud save, history, templates?
7. which capabilities should be added this time, and which should not?
8. must it remain compatible with the old entry point, old template data, old bridge, and old export results?
9. is staged refactoring allowed?

After asking these questions, perform a gap analysis before implementation. Do not jump straight into a rewrite.

## `appKey` Rules

- `appKey` must be the real platform `appKey`
- it will be written directly into:

```ts
GeneratorSDK.init({
  appKey: 'REAL_APP_KEY',
  env: 'prod',
})
```

- later CMS sharing-growth and credits / free-usage configuration will also be built around this identifier
- do not treat a name-derived value as the real `appKey`
- when refactoring an old generator, the default preference is to reuse the existing production `appKey`

## SDK Capability Selection And MCP Feature Mapping

| Developer Selection | MCP `features` |
|---|---|
| login | `auth` |
| SVG export | `export` |
| Open in Studio | `export` |
| credits | `credits` |
| unified billing | `billing` |
| cloud save | `cloud` |
| history | `history` |
| template-authoring capability | `template` |

## Starter Selection Rules

| Scenario | Default Starter |
|---|---|
| pure HTML / CDN / rapid vibe coding | `starter-html-runtime` |
| Vue 3 engineered generator | `starter-vue-runtime` |

If it is a new generator and the developer does not explicitly refuse, use a runtime starter by default.

If it is an old generator refactor, the starter should be treated as a structural reference rather than the only plan that overwrites the old project.

## Old Generator Refactor Strategies

| Strategy | Suitable Scenario | Default Recommendation |
|---|---|---|
| `adapter` compatibility | the old bridge still works and only lacks a standard runtime entry | preferred |
| runtime wrapper | the old rendering and state can still be reused and only standard host capabilities are missing | preferred |
| starter-based refactor | the old project structure is messy, but the business logic can still be extracted and reused | alternative |
| complete rewrite | the old code is unmaintainable, or the target has changed completely | last resort |

By default, prefer progressive refactoring and do not default to a complete rewrite.

If the task framing is `standardization refactor`, then staged delivery is allowed only as an execution path and not as the final completion framing.

## Recommended Mapping From Old Bridge To Runtime

| Old Bridge Capability | Runtime Equivalent |
|---|---|
| `getSnapshot()` | `getState()` |
| `applySnapshot()` | `setState()` |
| `getExportData()` | `export()` |
| `_sync()` | `subscribe()` + `state-change` |

If the old bridge is still in service, you may first add an adapter layer and then gradually converge toward a standard runtime implementation.

## Template-Authoring Capability Trigger Rules

As long as the developer explicitly mentions any of the following scenarios, enable the `template` feature by default:

- template
- template page
- sub-generator
- preset parameters
- only part of the parameters may be adjusted
- publish template
- import template

After enabling it, add the following by default:

1. `sdk.template.build(...)`
2. `sdk.template.download(...)`
3. `sdk.template.parse(...)`
4. `sdk.template.applyToRuntime(...)`
5. a "publish template / import template" interaction skeleton

If this is an old generator refactor, you must additionally ask:

1. whether the old template format should be retained
2. whether old template data needs to be converted
3. how template import maps into runtime `setState()` / `patchState()` + `panelFilter`

## Standard Runtime Interface

```ts
interface GeneratorRuntime {
  mount(options: MountOptions): MountedInstance | Promise<MountedInstance>
  getState(): GeneratorState
  setState(nextState: GeneratorState): void | Promise<void>
  patchState(patch: StatePatch): void | Promise<void>
  getPanelSchema(options?: { panelFilter?: PanelFilter }): PanelSchema
  export(action: ExportAction): Promise<ExportResult | null>
  subscribe(listener: RuntimeEventListener): () => void
}
```

If the goal is a standardized generator, whether it is newly created or refactored, it should ultimately converge to this set of interfaces.

## Minimum Generator Directory Skeleton

```txt
src/
  core/
    state.ts
    renderer.ts
    panel-schema.ts
    export.ts
  runtime/
    index.ts
    mount-canvas.ts
    mount-panel.ts
  pages/
    full.ts
    embed.ts
  platform/
    sdk.ts
```

If it is a progressive refactor of an old generator, it is acceptable not to change the directory structure immediately, but the following responsibilities should gradually be split clearly:

- `core`: state, rendering, export, parameter schema
- `runtime`: standard interfaces and adapter layer
- `pages`: `full` / `embed` entries
- `platform`: `generator-sdk` integration

## `PanelFilter` Example

```ts
const panelFilter = {
  includeFields: ['params.text', 'params.color'],
  excludeFields: ['params.debugMode', 'params.advancedPath'],
  readonlyFields: ['document.width', 'document.height'],
}
```

For `includeFields` / `excludeFields` / `readonlyFields`, it is recommended to prioritize `field.bind.path`.

## Standard Template Protocol

```ts
interface GeneratorTemplateDefinition {
  type: 'generator-template'
  version: '1.0.0'
  generatorId: string
  defaults: Record<string, unknown>
  panelFilter: PanelFilter
  adjustableFields: TemplateFieldOption[]
}
```

Rules:

1. template JSON must use a unified protocol, and field names must not be invented freely
2. template import must take effect through runtime `setState()` / `patchState()` + `panelFilter`
3. do not implement template import as "directly replace the DOM"
4. do not make template capability private to the current generator only

## Typical Template Page Integration

```ts
await runtime.mount({
  mode: 'embed',
  target: 'canvas',
  container: canvasContainer,
  state,
})

await runtime.mount({
  mode: 'embed',
  target: 'panel',
  container: panelContainer,
  state,
  panelFilter,
})
```

## Default Constraints For The New-Generator Branch

As long as a new generator is being created, add the following requirements by default:

1. support both `full` and `embed`
2. expose `window.__GENERATOR_RUNTIME__`
3. implement `getState`, `setState`, `patchState`, `getPanelSchema`, `export`, and `subscribe`
4. the parameter panel must be able to render partial parameters through `PanelFilter`
5. the template page needs to support a left-side canvas and a right-side partial parameter panel

## Default Constraints For The Refactor Branch

As long as an old generator is being refactored, add the following requirements by default:

1. first perform current-state inventory and gap analysis, then enter implementation
2. prefer keeping the existing `appKey`, core parameter semantics, and export behavior stable
3. prefer compatibility with the old entry point, old template data, and old bridge unless the developer explicitly asks to retire them
4. prefer reusing the old rendering and state logic, and fill in the runtime contract first
5. staged delivery is allowed; a one-shot full migration is not required

## Default Constraints On AI

When AI generates or refactors a generator, the default requirements are:

1. all platform capabilities go through `generator-sdk`
2. hosts consume the generator through the runtime rather than private DOM structure
3. template pages reuse parameters through `PanelSchema` + `PanelFilter`
4. do not turn an old generator into only a single full page
5. do not rewrite the old project before confirming compatibility constraints
6. before the "standardization completion gate" is satisfied, do not claim "completed to the full standard"
7. if only staged delivery is completed, explicitly state the current stage and remaining items

## Acceptance Checklist For Refactor Scenarios

Check at least the following:

1. whether old core features have no regressions
2. whether both `full` / `embed` run correctly
3. whether `PanelSchema` can be consumed and filtered by the host
4. whether template import takes effect through the runtime
5. whether agreed old entry points or bridge compatibility are retained
6. whether CMS / data / template migration notes have been added

## Required Wrap-Up Template

After completing a generator task, the final response must explicitly include the following fields:

- `Task framing`: new build / compatibility refactor / standardization refactor
- `Current stage`: if it is an old generator refactor, fill in Stage 1/2/3/4; if it is new, fill in "not completed" or "reached the standardization completion state"
- `Completed items`
- `Incomplete items`
- `Can standardization be claimed`: yes / no
- `Blockers or risks`
- `Compatibility and migration notes`

If `Can standardization be claimed = no`, you must additionally include:

- "The current result has not yet reached the standardization completion state and should not be described as a completed standard generator migration."

## Post-Completion Upload Wrap-Up

After the code is completed, prompt the developer by default to open:

- [xtool generator upload](https://www-dev.atomm.com/generator-upload)

Also tell the user that the resources need to be packaged as a zip file, and the zip file must contain an HTML file.

If this is an old generator refactor, also explain:

- whether the original `appKey` is reused
- whether CMS capability switches need to be changed
- whether template or historical data migration is involved
