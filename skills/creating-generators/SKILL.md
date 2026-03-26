---
name: creating-generators
description: Use when creating, scaffolding, extending, refactoring, migrating, or standardizing a generator, including requests like "生成器", "相框生成器", "挂件生成器", "铭牌生成器", "full/embed", "runtime contract", "PanelSchema", "模板页嵌入", or parameter-panel driven generator work.
---

# Creating Generators

## When To Use

Use this skill whenever the task is about a generator rather than a generic page or component.

Typical signals:

- the user explicitly says "生成器"
- the task mentions generator types such as frame, pendant, badge, sign, or template generator
- the user wants a left tools + center canvas + right parameters workspace
- the task mentions `full` / `embed`, runtime contracts, parameter schemas, template pages, or template import/export
- the task is about standardizing or refactoring an older generator project

If a request matches both "normal frontend work" and "generator work", prefer this skill.

## First Response Rules

Do not jump straight into generic UI implementation.

First identify which kind of generator task this is:

1. create a new generator
2. refactor an existing generator
3. compatibility-oriented refactor
4. standardization-oriented refactor
5. add platform capabilities
6. add runtime or `full` / `embed`
7. add template-authoring support

Ask one question at a time until the task type is clear.

## Questions For New Generators

Collect these in order:

1. generator name
2. real `appKey` or platform identifier, if the host platform requires one
3. tech stack: `html` or `vue`
4. required platform capabilities
5. whether template-authoring capability is needed

Platform capabilities usually include:

- auth
- export
- open in studio
- credits
- billing
- cloud save
- history

If the user mentions templates, template pages, sub-generators, preset parameters, or partially editable parameters, treat that as template-authoring support.

## Questions For Existing Generators

Collect these in order:

1. is this a compatibility refactor or a standardization refactor
2. generator name
3. current repository or directory
4. existing real `appKey` or platform identifier, if any
5. current technical form: `html`, `vue`, single-page app, bridge-based app, or partial runtime
6. current capabilities already implemented
7. capabilities to add in this round
8. compatibility constraints
9. whether staged delivery is allowed

Do not assume refactoring means a full rewrite.

## Default Design Rules

For generator work, prefer these boundaries:

- platform SDK handles platform features
- runtime handles state, mounting, panel schema, export, and subscriptions
- host pages consume the runtime instead of reading internal DOM structure

For new generators, default expectations are:

- support both `full` and `embed`
- expose a runtime entry
- expose a parameter schema
- make the parameter panel filterable

For refactors, default expectations are:

- preserve core behavior unless the user explicitly asks to change it
- reuse working state/rendering logic when possible
- add adapters before considering a full rewrite

## Standard Runtime Surface

A standardized generator should converge toward these capabilities:

- `mount({ mode, target, container, state, panelFilter })`
- `getState()`
- `setState()`
- `patchState()`
- `getPanelSchema()`
- `export(action)`
- `subscribe(listener)`

If the work has not reached this shape yet, do not claim the generator is fully standardized.

## Template Rules

If template support is in scope:

- do not invent a private template format without reason
- apply imported template data through runtime state APIs
- let template pages consume filtered panel schema instead of duplicating generator-specific form logic

## Completion Rules

At the end of generator work, explicitly state:

- task type
- current phase
- completed items
- remaining items
- whether standardization can be claimed
- risks or blockers
- compatibility or migration notes

If only partial work is done, say so clearly instead of implying full completion.

## Common Mistakes

- treating a generator like a generic landing page
- building only a single full page and skipping embed mode
- hardcoding the parameter panel instead of exposing schema
- coupling host pages to generator DOM internals
- rewriting old generators before checking compatibility constraints
- claiming "standardized" when only SDK wiring or partial runtime work is done

## Reference

See `reference.md` for runtime, template, and panel-schema reference guidance.
