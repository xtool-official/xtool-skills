# Creating Generators Reference

## Runtime And Platform Capability Layers

| Layer | Responsible For | Not Responsible For |
|---|---|---|
| `generator-sdk` | login, cloud save, history, credits, billing, export, Studio | page shell, parameter panel structure, template page embedding |
| `Generator Runtime Contract` | `full/embed`, state, parameter schema, export interfaces, events | login dialogs, sharing dialogs, CMS, publishing |
| `generator-workbench` | official shell, top bar, template actions, login entry, floating export actions, mounting runtime panel/canvas | generator business state, custom rendering logic |
| host page | navigation, page layout, template page structure, calling the runtime | defining the generator's internal business state |

## SDK Capability Selection And MCP Feature Mapping

| User-Level Capability | MCP `features` |
|---|---|
| login | `auth` |
| SVG export | `export` |
| Open in Studio | `export` |
| credits | `credits` |
| unified billing | `billing` |
| cloud save | `cloud` |
| history | `history` |
| template-authoring capability | `template` |

## `appKey` Rules

- new generators default to a generated development `appKey`
- use the format `dev_<random>`
- write it into `GeneratorSDK.init({ appKey, env: 'prod' })`
- do not derive the value from the generator name
- old-generator refactors should reuse the production `appKey` by default unless the user wants to change it

Example:

```ts
GeneratorSDK.init({
  appKey: 'dev_xxx',
  env: 'prod',
})
```

## Starter Selection Rules

| Scenario | Default Starter |
|---|---|
| pure HTML / CDN / rapid vibe coding | `starter-html-runtime` |
| Vue 3 engineered generator | `starter-vue-runtime` |

For old-generator refactors, treat a starter as a structural reference rather than a full overwrite plan.

For new generators, the default shell path is:

- starter generates runtime structure
- the full-mode host mounts `generator-workbench`
- generator-specific code stays in runtime and rendering modules

Only skip this path when the user explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery.

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

## Old Generator Refactor Strategies

| Strategy | Suitable Scenario | Recommendation |
|---|---|---|
| `adapter` compatibility | old bridge still works and only lacks standard runtime exposure | preferred |
| runtime wrapper | old rendering and state can be reused; standard host capabilities are missing | preferred |
| starter-based restructuring | project structure is messy but business logic is reusable | alternative |
| complete rewrite | old code is unmaintainable or target changed completely | last resort |

## Recommended Mapping From Old Bridge To Runtime

| Old Bridge Capability | Runtime Equivalent |
|---|---|
| `getSnapshot()` | `getState()` |
| `applySnapshot()` | `setState()` |
| `getExportData()` | `export()` |
| `_sync()` | `subscribe()` + `state-change` |

## Minimum Directory Skeleton

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

For progressive refactors, the directory does not need to change immediately, but responsibilities should gradually separate into:

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

Prefer `field.bind.path` as the stable field identifier.

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

Template rules:

1. use a unified protocol, not generator-private JSON
2. template import must apply through runtime `setState()` / `patchState()` + `panelFilter`
3. do not implement template import by directly replacing DOM
4. do not make template capability private to one generator only

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

## Embed Rules

In `embed` mode, the generator should:

- avoid top navigation
- avoid shell-level platform entries such as login or credits
- not charge by itself
- not depend on global `body` layout
- respond to container size changes
- share the same business state as `full` mode

## Template Page Rules

A template page should:

- mount the canvas
- get and filter `PanelSchema`
- synchronize state and export through runtime
- use `sdk.template` helpers in template import/export scenarios

A template page should not:

- recreate generator business parameter logic
- read generator-private DOM directly
- assume a fixed internal field set
- define another private template JSON protocol
