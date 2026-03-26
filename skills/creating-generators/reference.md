# Creating Generators Reference

## Recommended Architecture Split

Keep the responsibilities separate:

- platform SDK: auth, billing, history, export, or cloud capabilities
- generator runtime: state, mount lifecycle, panel schema, export bridge, subscriptions
- host pages: layout, navigation, template page shell, runtime consumption

## Suggested Task Classification

When a generator request arrives, classify it into one of these buckets:

1. create a new generator
2. refactor an existing generator
3. compatibility refactor
4. standardization refactor
5. add platform capabilities
6. add runtime support
7. add template-authoring support

## Runtime Shape

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

## Starter Guidance

Default suggestions:

- `html`: use when delivery is lightweight, CDN-based, or fast-prototyping oriented
- `vue`: use when the project is already framework-based or needs stronger engineering structure

For existing generators, a starter should be a reference, not an excuse to overwrite working business logic.

## Refactor Strategy Order

Prefer these strategies in order:

1. adapter compatibility
2. runtime wrapper around existing logic
3. restructure around a starter while preserving core logic
4. full rewrite only as a last resort

## Panel Filtering

If the host only needs part of the parameter panel, filter the schema instead of building a second bespoke form.

```ts
const panelFilter = {
  includeFields: ["params.text", "params.color"],
  excludeFields: ["params.debugMode"],
  readonlyFields: ["document.width", "document.height"]
}
```

## Template Guidance

If templates are in scope:

- preserve a stable template format
- import templates through runtime state APIs
- use filtered parameter schema on template pages
- avoid template-page logic that depends on private DOM structure

Example shape:

```ts
interface GeneratorTemplateDefinition {
  type: "generator-template"
  version: "1.0.0"
  generatorId: string
  defaults: Record<string, unknown>
  panelFilter: PanelFilter
  adjustableFields: TemplateFieldOption[]
}
```

## Completion Gate

Do not claim a generator is fully standardized unless it has:

- runtime APIs in place
- a clear `full` / `embed` story when required
- parameter schema support
- template flow support when template use cases are required
- compatibility or migration notes for refactors
