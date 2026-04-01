# xtool-skills

xTool 工作流技能仓库。

[English README](README.md)

`creating-generators` 是一个用于用 `generator-sdk`、runtime API、`full` / `embed`、`PanelSchema` 和 `generator-workbench` 来创建或改造 generator 的工作流 skill。

## 在 Claude 中安装和使用

### 安装

先添加 marketplace：

```bash
claude plugin marketplace add xtool-official/xtool-skills
```

然后安装 skill：

```bash
claude plugin install creating-generators@xtool-skills
```

如果 Claude 没有立即识别新安装或更新后的 plugin，可以执行：

```bash
/reload-plugins
```

### 使用

安装后，直接用自然语言描述 generator 任务即可，例如：

- "Build a frame generator"
- "Create a pendant generator with generator-sdk"
- "Refactor this old generator to support both `full` and `embed`"
- "Add template import/export capability using the runtime"

Claude 应该会在匹配 generator 任务时自动使用 `creating-generators`。

## 在 Cursor 中安装和使用

### 安装

```bash
mkdir -p .cursor/skills/creating-generators
cp skills/creating-generators/SKILL.md .cursor/skills/creating-generators/SKILL.md
cp skills/creating-generators/reference.md .cursor/skills/creating-generators/reference.md
```

如果你想让本机所有项目都能使用，可以改为安装到用户目录：

```bash
mkdir -p ~/.cursor/skills/creating-generators
cp skills/creating-generators/SKILL.md ~/.cursor/skills/creating-generators/SKILL.md
cp skills/creating-generators/reference.md ~/.cursor/skills/creating-generators/reference.md
```

Cursor 也支持在 `Settings -> Rules -> Add Rule -> Remote Rule (Github)` 中通过 GitHub 仓库导入：

<https://github.com/xtool-official/xtool-skills>

如果 Cursor 没有立即识别新安装或更新后的 skill：

- 先新开一个 Agent chat
- 还不生效再重启 Cursor

### 使用

直接用自然语言描述 generator 任务，或者手动通过 `/creating-generators` 调用，也可以用 `@creating-generators` 附加到当前对话。

示例：

- "Create a new pendant generator for me"
- "Build a frame generator with generator-sdk"
- "Refactor this old generator to support both `full` and `embed`"
- "Add template import/export capability using the runtime"
- "Make the parameter panel filterable through `PanelFilter`"

## 仓库结构

```text
xtool-skills/
├── .claude-plugin/
│   └── marketplace.json
├── README.md
├── README.zh-CN.md
└── skills/
    └── creating-generators/
        ├── SKILL.md
        └── reference.md
```
