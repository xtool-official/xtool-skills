# xtool-skills

Public Claude plugin marketplace for xTool skills.

This repository publishes installable Claude skills through a standard plugin marketplace. It currently includes:

- `creating-generators`: a workflow skill for creating, scaffolding, extending, and refactoring generators

## What Is `creating-generators`

`creating-generators` is a specialized skill for generator-style applications rather than generic page development.

Use it when the task is about:

- building a new generator
- refactoring an existing generator
- adding `full` / `embed` runtime modes
- exposing runtime APIs such as `mount()`, `getState()`, `setState()`, and `getPanelSchema()`
- supporting template-authoring flows with filtered parameter panels
- standardizing generator projects that use a shared platform SDK or runtime contract

It is especially useful for requests like:

- "Help me build a generator"
- "Build a frame generator"
- "Refactor the old generator into a standard runtime structure"
- "Add `full` / `embed` support and panel schema support to the generator"

## Install

1.1 Method 1: Manual Installation

Add this marketplace:

```bash
claude plugin marketplace add xtool-official/xtool-skills
```

Install the skill:

```bash
claude plugin install creating-generators@xtool-skills
```

Reload plugins if needed:

```bash
/reload-plugins
```

1.2 Method 2: Let AI Install Them

```text
Please help me install the skills related to creating-generators.
1. Add the marketplace source (if not already added):
   xtool-official/xtool-skills
2. Then install the following plugins:
   - creating-generators
Tell me the result after installation.
```

## How To Use

After installation, you do not invoke the skill manually by file path. Simply describe the generator task in natural language, for example:

- "Create a new pendant generator for me"
- "Refactor this old generator to support both `full` and `embed` modes"
- "Add template import and export capabilities to the generator"
- "Make the parameter panel filterable through `PanelFilter`"

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
- what counts as complete versus partial delivery

For the exact skill behavior, see `skills/creating-generators/SKILL.md`.
