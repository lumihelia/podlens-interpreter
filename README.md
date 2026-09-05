# PodLens Interpreter

中文 · [English](./README.en.md)

一个以证据为起点的 Agent Skill，用于把播客、访谈、论文和其他长内容整理成可追溯的观点还原、可读的白话解释，以及可以继续编辑和发布的内容包。

PodLens Interpreter 来自 PodLens 的方法体系，目前作为独立 Skill 分发。运行发生在支持 Skill 的 Agent 内，这个仓库本身不包含独立 Web 应用、本地服务或模型 API 配置。

**当前 Skill 版本：** `1.1.0`

## 它如何工作

整个流程按三个阶段向前推进：

1. **Faithful Reconstruction / 忠实还原**：从原文中找出核心问题、关键发现、整体立场与不确定性，并给每条发现留下证据锚点。
2. **Plain-Language Retelling / 白话重述**：沿着 Stage 1 已经建立的证据结构，把原内容讲清楚。
3. **Content Pack / 内容包**：从经过核验的还原结果继续生成可以编辑和发布的内容资产。

Skill 最核心的是一条完整的证据链：

```text
原始内容
    -> 证据锚点
    -> finding
    -> 白话重述
    -> 可发布内容
```

当前 v1.1.0 在 Stage 1 增加了 anchor-first 流程：先提取原文中的逐字引用、时间戳、页码或章节位置，再生成 finding。找不到足够锚点的 finding 会被舍弃，不会用较低置信度继续保留一个缺少证据的判断。

每次完整运行最后都会出现一个简短的 `Audit`，用于检查证据追溯、人物归因、不确定性表达与文字约束。

## 适合处理什么

适合的输入包括：

- 播客与访谈 transcript
- YouTube 字幕，以及粘贴进来的 `.srt` / `.vtt` 文本
- 研究论文与学术文本
- 技术报告
- 长文章、essay、lecture 与 talk transcript

完整三阶段流程更适合约 800 词以上的材料。按照当前 Skill 规则，更短的输入默认只执行 Stage 1。超过约 12,000 词的长材料会先沿章节、时间戳、说话人或其他自然边界分段，再合并 findings。

输出语言跟随当前对话使用的语言；明确指定其他语言时，以指定语言为准。

Skill 本身不要求单独配置 API key。实际使用的模型与工具由承载它的 Agent 环境提供。

## 输出结构

### Standard Mode

用于播客、访谈、文章与 lecture：

- Core Question
- 带证据锚点的 Core Findings
- Core Positions
- 白话重述
- X post / thread
- LinkedIn post
- Newsletter intro
- 5 个 Follow-up Angles
- Audit

### Paper Mode

用于论文与技术报告：

- Core Question
- 带证据锚点的 Core Findings
- Core Positions
- 白话重述
- Research Brief
- Evidence Table
- Business and Creator Angles
- Audit

从原文进一步延伸出来的应用与选题会标记自己来自哪一条 Stage 1 finding，让后续推演和原作者真正说过的话保持清楚边界。

## 安装

`SKILL.md` 采用可移植的 Agent Skills 结构。不同 Agent 的发现路径有所不同。

### BotLearn SkillHunt

```text
skillhunt podlens-interpreter
```

`skillhunt` 目前仍是 BotLearn `install` 命令的 alias。

### Claude Code

项目级：

```text
.claude/skills/podlens-interpreter/SKILL.md
```

个人 / 全局：

```text
~/.claude/skills/podlens-interpreter/SKILL.md
```

### Codex

Skill 作为独立目录放在 `$CODEX_HOME/skills` 下。默认位置是：

```text
~/.codex/skills/podlens-interpreter/SKILL.md
```

早期 README 中「把整份 SKILL.md 内容加入 `AGENTS.md`」的方式已经不是当前首选安装路径。

### Cursor

项目级可以放在：

```text
.cursor/skills/podlens-interpreter/SKILL.md
.agents/skills/podlens-interpreter/SKILL.md
```

Cursor 目前也会发现 Claude 与 Codex skill 目录中的兼容 Skill。

### Windsurf

项目级：

```text
.windsurf/skills/podlens-interpreter/SKILL.md
```

跨 Agent 的项目级位置：

```text
.agents/skills/podlens-interpreter/SKILL.md
```

Windsurf 全局 Skill 默认位于：

```text
~/.codeium/windsurf/skills/podlens-interpreter/SKILL.md
```

对于支持 Agent Skills 的环境，可复用单元都是一个名为 `podlens-interpreter/` 的 Skill 目录，其中包含本仓库的 `SKILL.md`。

## 调用

Agent 成功发现 Skill 后，直接给出材料和任务即可，例如：

```text
使用 PodLens Interpreter 处理这份 transcript，按照完整的三阶段结构输出。
```

支持显式 Skill 调用的 Agent 也可以通过自己的 slash command 或 Skill picker 启动。

## 示例

仓库目前保留三类示例：

- `examples/demo_a_*`：早期的紧凑 podcast fixture
- `examples/demo_b_*`：早期的紧凑 paper fixture
- `example-output-alphago.md`：基于 2016 年 AlphaGo Nature 论文生成的一份更长 Paper Mode 示例

两组 compact demo 来自早期 SkillHunt 提交阶段，输入长度都低于当前 v1.1 对完整三阶段流程的建议长度。它们适合观察早期输出形态，不作为当前 `SKILL.md` 的合规测试样本。

`example-output-alphago.md` 同样标注为 v1 时期的运行结果。它仍然可以用于观察较完整的 Paper Mode 与证据锚定方式；当前执行规范以 `SKILL.md` v1.1.0 为准。

## 和 PodLens 的关系

[PodLens](https://github.com/lumihelia/PodLens) 是完整的 Python interpretation 与 publishing workspace，包含 CLI、本地 editorial workbench、私有 personal mapping，以及中英文静态站发布流程。

PodLens Interpreter 从同一套方法体系中抽出一个可以独立安装的 Agent workflow：先还原来源，再保持证据可追溯，然后继续生成下游内容。运行 PodLens Interpreter 不依赖 PodLens 应用本身。

## 仓库结构

- `SKILL.md`：当前 Skill 指令与 metadata
- `README.md`：中文仓库说明
- `README.en.md`：英文仓库说明
- `examples/`：早期紧凑 demo fixtures
- `example-output-alphago.md`：较长的 Paper Mode 示例

## License

[MIT](LICENSE)
