# Engineering Exploration Skill

这是一个用于 AI IDE 的工程探索类 Skill。它的目标是让 AI 在需求、方案和边界尚未说清楚时，先进入“探索/设计/规划”状态，而不是立刻创建文件、修改代码或执行有副作用的命令。

# 创作与测试环境
- 该Skill使用Codex Skill Creator进行创作，并通过测试，达到了显性的效果
- 在Trea中进行测试，相较于原始环境的询问，采用该Skill能够得到项目更细致的询问

## 功能

Engineering Exploration 适合这些场景：

- 先聊方案、讨论思路、构思、探索、架构设计、技术选型
- 设计某个功能、模块、服务、插件、工具或 Skill
- 在实现前梳理需求、约束、接口、数据流、安全、错误处理和测试
- 比较多个技术路线，并给出推荐方案
- 将同一套“先探索、后实现”的工作流迁移到其他 AI 编程助手
- 当用户说“先不写代码”“不要创建代码”“不要修改代码”时，保持只读探索状态

它会要求 AI 在明确收到实现授权前，不主动创建、编辑、删除、移动文件，不安装依赖，不运行迁移、部署或其他修改性命令。用户明确说“帮我实现”“按这个方案做”“落地到代码”“make the changes”之后，才切换回正常编码流程。

## 使用方式

显式调用最稳定：

```text
Use $engineering-exploration 帮我讨论这个功能怎么设计，先不要写代码。
```

也可以用自然语言触发：

```text
我们先聊方案：这个项目要加一个会员积分系统，怎么设计比较好？
```

隐式触发依赖 Codex 对 Skill metadata 的判断，不是强保证。如果任务边界很重要，建议显式写 `$engineering-exploration`。

## 安装

本仓库提供的是打包好的 Skill：

```text
engineering-exploration.zip
```

在 Windows PowerShell 中可以解压到 Codex 用户 skills 目录：

```powershell
Expand-Archive .\engineering-exploration.zip -DestinationPath "$env:USERPROFILE\.codex\skills" -Force
```

安装后建议新开一个 Codex 会话，让 Skill metadata 重新加载。

## Skill 架构

zip 包内部结构如下：

```text
engineering-exploration/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── capability-design.md
    ├── engineering-design-dimensions.md
    ├── exploration-boundaries.md
    └── platform-portability.md
```

架构思路是“主入口保持精简，细节按需加载”：

- `SKILL.md` 放触发描述、探索模式边界、输出契约和引用文件导航。
- `agents/openai.yaml` 放 Codex UI 需要的显示名称、简短说明和默认调用 prompt。
- `references/` 放更细的设计清单和平台迁移说明，只有在相关任务中才需要读取。

## 文件说明

| 文件 | 作用 |
| --- | --- |
| `SKILL.md` | Skill 主文件。frontmatter 里的 `name` 和 `description` 负责触发；正文定义探索模式、允许和禁止的动作、输出形态、实现授权门槛，以及需要时读取哪些 reference。 |
| `agents/openai.yaml` | Codex UI metadata。包含显示名 `Engineering Exploration`、短描述和默认 prompt：`Use $engineering-exploration ...`。 |
| `references/exploration-boundaries.md` | 处理“讨论、规划、实现”之间的边界。说明什么时候保持探索、什么时候要确认、什么时候才算明确授权实现。 |
| `references/engineering-design-dimensions.md` | 软件工程设计维度清单。覆盖目标、用户价值、约束、架构、接口、安全可靠性、错误处理、测试和实现就绪标准。 |
| `references/capability-design.md` | 能力设计参考。用于设计功能模块、服务能力、工作流、集成、API 操作、插件、工具或 Skill，强调边界、接口、执行、状态、权限、观测、错误和生命周期。 |
| `references/platform-portability.md` | 平台迁移参考。说明如何把这套探索工作流适配到 Codex、CodeBuddy、Work Buddy、Trae、Cursor、Claude、Copilot、Gemini CLI 等不同 AI 编程平台。 |

## 验证

安装后可以运行 Codex skill 校验脚本：

```powershell
python -X utf8 "$env:USERPROFILE\.codex\skills\.system\skill-creator\scripts\quick_validate.py" "$env:USERPROFILE\.codex\skills\engineering-exploration"
```

期望输出：

```text
Skill is valid!
```

推荐测试这几类 prompt：

```text
Use $engineering-exploration 帮我讨论一个文件上传模块怎么设计，先不要写代码。
```

```text
我们先聊方案：这个功能怎么设计比较好？
```

```text
这个方案不错，下一步呢？
```

```text
按这个方案做，帮我实现。
```

前三个应该保持探索或先询问确认，最后一个才应该进入实现流程。
