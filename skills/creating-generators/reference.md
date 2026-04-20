# Creating Generators Reference

## Runtime And Platform Capability Layers

| Layer | Responsible For | Not Responsible For |
|---|---|---|
| `generator-sdk` | login, cloud save, history, credits, billing, export, Studio | page shell, parameter panel structure, template page embedding |
| `Generator Runtime Contract` | `full/embed`, state, parameter schema, export interfaces, events | login dialogs, sharing dialogs, CMS, publishing |
| `generator-workbench` | official shell, top bar, template actions, login entry, floating export actions, mounting runtime hosts | generator business state, custom rendering logic |
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

## Default Workbench Standard Bundle

For a new generator in initial development, the default feature bundle is:

- `auth`
- `credits`
- `billing`
- `export`
- `template`

Use this bundle unless the user explicitly asks for a reduced-scope build or explicitly requires extra platform capabilities such as `cloud` or `history`.

## `appKey` Rules

- keep `appKey` in a top-level `config.json`
- do not hardcode `appKey` inside runtime or business modules
- load `appKey` from `config.json` before initializing `GeneratorSDK`
- old-generator refactors should reuse the production `appKey` by default unless the user wants to change it

Example:

```json
{
  "appKey": "dev_xxx"
}
```

```ts
import config from '../config.json'

GeneratorSDK.init({
  appKey: config.appKey,
  env: 'prod',
})
```

## Starter Selection Rules

| Scenario | Default Starter |
|---|---|
| default HTML path / CDN / rapid vibe coding | `starter-html-runtime` |
| explicit dedicated Vue-project scaffold | `starter-vue-runtime` |

For old-generator refactors, treat a starter as a structural reference rather than a full overwrite plan.

For new generators, the default shell path is:

- starter generates runtime structure
- the full entry mounts `generator-workbench`, and new generators should default that host shell to `mode: 'shell'` unless the user explicitly wants the classic split sidebar layout
- generator-specific code stays in runtime and rendering modules
- new generators default to `html` + `starter-html-runtime`
- on the default `html` path, `index.html` is the `full` entry and `embed.html` is the `embed` entry
- `full.html` is not used by default; only add it when the user explicitly asks for it
- on the default `html` path, keep the project HTML-first; if Vue is needed, load it by CDN rather than converting the project into a Vue scaffold
- once `generator-workbench` is integrated on the default `html` path, business UI should default to `atomm-ui`
- on that default path, keep the business UI CDN-based instead of switching to another component library or a package-managed UI stack unless the user explicitly asks for it
- new generators default to the `workbench-standard` feature bundle unless the user explicitly requests a reduced-scope or expanded platform bundle

For a new generator in initial development, the official host shell is mandatory by default.

Do not replace `generator-workbench` with a custom full-page shell unless the user explicitly waives the official host shell.

Only skip this path when the user explicitly requests a reduced-scope build, a custom shell, or a runtime-only delivery.

## Default Business UI Stack After Workbench Integration

When the default new-generator path uses `generator-workbench` + `html`:

- keep the generator project HTML-first
- if the runtime business UI needs a component library, default to `atomm-ui`
- use CDN delivery for that default path unless the user explicitly requests another UI stack or an engineered scaffold
- do not silently introduce a second component library just because `generator-workbench` is already mounted

Important scope rule:

- `generator-workbench` auto-loads Vue and `atomm-ui` for the shell itself
- runtime business UI is still a separate concern
- if runtime business UI mounts its own Vue sub-app or needs direct `atomm-ui` usage, explicitly reuse the CDN path for business UI as well

## Runtime Style Host Rule

When the default new-generator path uses `generator-workbench` and runtime business UI uses `atomm-ui`:

- treat `generator-workbench` shell styles and runtime business UI styles as separate concerns
- do not assume page-level `<head>` CSS can style runtime business UI across Shadow DOM boundaries
- resolve the actual style host from the runtime mount container
- if runtime mounts into normal document DOM, inject or reuse the stylesheet from `document.head`
- if runtime mounts into a Shadow Root or another isolated root, inject the stylesheet into that actual root
- keep the runtime-side style injection idempotent so repeated mounts do not create duplicate `link[data-atomm-ui-runtime]` nodes

Recommended CDN addresses for the default path:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.prod.js"></script>
<link rel="stylesheet" href="https://static-res.atomm.com/scripts/js/generator-sdk/atomm-ui/dist-browser/atomm-ui.min.css" />
<script src="https://static-res.atomm.com/scripts/js/generator-sdk/atomm-ui/dist-browser/atomm-ui.js"></script>
```

Only deviate from this default when the user explicitly asks for:

- another UI library
- a package-managed delivery mode
- a dedicated Vue-project scaffold

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

## Workbench Upgrade Audit

When an old generator already uses `generator-workbench`, do not treat a shell upgrade as "CDN only" by default.

Use `https://static-res.atomm.com/scripts/js/generator-sdk/generator-workbench/upgrade-manifest.json` as the primary source of truth for upgrade requirements.

If the CDN manifest cannot be read, fall back to MCP upgrade documentation instead of assuming the capability is shell-only.

### Required Audit Flow

1. identify the generator's current workbench integration shape
2. read the target release entry from the CDN manifest URL above
3. classify each relevant capability into:
   - `auto-adopted`
   - `host-action-required`
   - `runtime-action-required`
4. compare the target generator against the manifest's `detectionHints`, `requiredChanges`, and `validation`
5. output a generator-specific upgrade checklist before implementation

### Interpretation Rules

- `auto-adopted` means a CDN shell update is usually enough
- `host-action-required` means the generator must update host glue code, config, routes, or CDN asset wiring
- `runtime-action-required` means the generator must update runtime contract behavior, runtime adapter logic, or runtime events

### Typical Workbench Upgrade Gaps

The most common old-generator upgrade misses are:

- enabling a new shell capability in config but not exposing the required host helpers
- updating the workbench CDN URL without updating `workbench-host.js`
- adding cloud/history in the shell while the runtime cannot provide stable `getState()` before mount
- enabling autosave without emitting observable runtime state changes
- failing to dispatch `runtime-cloud-save-request` for runtime-owned save payloads
- enabling invite or purchase flows without wiring the required `atomm-pro` config or CDN assets

### Minimum Upgrade Checklist Output

For an old-generator workbench upgrade, the skill should output a checklist that includes:

- current generator workbench version or integration assumption
- target workbench version
- capabilities that are automatically gained
- host-side changes still required
- runtime-side changes still required
- smoke tests to rerun

### Example Questions The Audit Should Answer

- Does this generator really gain the new capability automatically after the CDN update?
- Does the host need new `workbench.config` fields?
- Does the runtime need `subscribe()` or `runtime-cloud-save-request`?
- Are route bootstrap behaviors such as `gid` creation now required?
- Are new CDN assets required for shell-owned UI such as `atomm-pro` flows?

## Minimum Directory Skeleton

```txt
config.json
index.html
embed.html
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
    index.ts
    embed.ts
  platform/
    sdk.ts
```

For a new generator in initial development, `index.html` is the main `full` entry and should mount `generator-workbench` through the standard host path, defaulting to `mode: 'shell'` so the runtime owns the full workspace layout unless the user explicitly wants the classic split sidebar shell or waives the official host shell.

`embed.html` is the standard `embed` entry.

Do not add `full.html` as another default entry unless the user explicitly requests it.

`generate_code` is not the primary skeleton path for a new generator in initial development. Prefer `generate_runtime_starter` for the first implementation pass, and use `generate_code` only for reduced-scope snippets or `sdk-only` examples.

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
