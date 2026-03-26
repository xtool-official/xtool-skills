# xtool-skills

Public Claude plugin marketplace for xTool skills.

This repository publishes installable Claude skills through a standard plugin marketplace. It currently includes:

- `creating-generators`: a workflow skill for creating, scaffolding, extending, and refactoring generators

## What Is `creating-generators`

`creating-generators` is a specialized skill for generator-style applications instead of generic page development.

Use it when the task is about:

- building a new generator
- refactoring an existing generator
- adding `full` / `embed` runtime modes
- exposing runtime APIs such as `mount()`, `getState()`, `setState()`, `getPanelSchema()`
- supporting template-authoring flows with filtered parameter panels
- standardizing generator projects that use a shared platform SDK or runtime contract

It is especially useful for requests like:

- "帮我做一个生成器"
- "做一个相框生成器"
- "把旧生成器重构成标准 runtime 结构"
- "给生成器补 full/embed 和参数面板 schema"

## Install

Add this marketplace:

```bash
claude plugin marketplace add xtool-official/xtool-skills
```

Install the skill:

```bash
claude plugin install creating-generators@xtool-official-xtool-skills
```

Reload plugins if needed:

```bash
/reload-plugins
```

## How To Use

After installation, you do not invoke the skill manually by file path. You simply describe the generator task in natural language, for example:

- "帮我新建一个挂件生成器"
- "把这个旧生成器改成支持 full/embed 双模式"
- "给生成器增加模板导入导出能力"
- "把参数面板改成可通过 PanelFilter 裁剪"

Claude should automatically use the `creating-generators` skill when the request matches generator work.

## Repository Structure

```text
xtool-skills/
├── .claude-plugin/
│   └── marketplace.json
└── skills/
    └── creating-generators/
        ├── SKILL.md
        └── reference.md
```

## Skill Scope

`creating-generators` focuses on workflow guidance and delivery rules:

- when to treat a task as generator work
- what to ask before implementation
- how to distinguish new builds from refactors
- how to define runtime, panel schema, and template boundaries
- what counts as complete vs partial delivery

For the exact skill behavior, see `skills/creating-generators/SKILL.md`.
