# sdd.skill
故事驱动开发：将 PRD 拆分为故事，端到端实现，并审查代码质量。

## 功能概述

此技能为你的 AI 编程助手添加四个命令：

| 命令     | 用途                                          |
| -------- | --------------------------------------------- |
| `update` | 根据新需求修订现有 PRD 或故事                 |
| `create` | 将 PRD 拆分为可独立实现的故事                 |
| `start`  | 端到端实现一个故事（代码、测试、文档）        |
| `review` | 审查暂存的 git 变更的类型安全、风格和代码质量 |

## 为什么用故事驱动？

大型功能很难一次性交付。此技能强制执行一套规范流程：

1. **编写 PRD** — 在 `docs/prd/` 中记录需求。
2. **创建故事** — 技能将 PRD 拆分为小型、可独立交付的故事，存放在 `docs/story/`。
3. **逐个实现故事** — AI 助手读取故事，编写代码，运行测试，并标记完成。
4. **提交前审查** — 捕获类型问题、魔法数字、重复逻辑和风格违规。

每个故事文件遵循一致的结构：目标、验收标准、功能要点和测试用例设计 — 全部使用自然语言描述，故事文件中不包含代码。

## 安装

### 自动安装
复制以下提示词并发送给你的 AI 编程助手：

> 下载 https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md 并保存到本项目的 `.agents/skills/story/SKILL.md`。

### 手动安装
**Linux / macOS：**

```bash
mkdir -p .agents/skills/story && curl -fsSL -o .agents/skills/story/SKILL.md https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md
```

**Windows (PowerShell)：**

```powershell
New-Item -ItemType Directory -Force -Path .agents\skills\story | Out-Null; Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md" -OutFile ".agents\skills\story\SKILL.md"
```

支持自定义技能的 AI 编程助手会自动识别该技能。

## 使用方法

在你的 AI 编程助手中自然地引用该技能：

```
# 从 PRD 创建故事
/story create stories from docs/prd/my-feature.md

# 开始实现指定故事
/story start story 2.1

# 用新需求更新故事
/story update story 3.2 — add pagination support to the list API

# 审查暂存的变更
/story review my staged changes
```

## 文件约定

### PRD 文件

```
docs/prd/<主题>.md          # 初始版本
docs/prd/<主题>-v2.md       # 修订版本（旧版本保留）
```

### 故事文件

```
docs/story/<M>.<N>-<status>.md
```

- `M` — 主故事编号（从 1 开始）
- `N` — 子故事编号（0 = 概览，1+ = 子故事）
- `status` — `plan` | `wip` | `done`

示例：
- `1.0-plan.md` — 主故事 1 概览
- `1.1-wip.md` — 主故事 1 的子故事 1（进行中）
- `1.2-done.md` — 主故事 1 的子故事 2（已完成）

### 故事文件结构

每个故事文件包含以下章节：

```markdown
# Story M.N — 简短标题

## 主要目标
此故事要实现的目标。

## 验收标准
- [ ] 标准 1
- [ ] 标准 2

## 功能要点
用自然语言描述需要实现的内容。

## 单元测试用例设计
用自然语言描述的测试场景。

## 行为测试用例设计
用自然语言描述的端到端测试场景。
```

## 审查检查项

`review` 命令会分析暂存的差异，检查以下问题：

- **类型安全** — 禁止 `as any`，禁止宽泛的 `Record<string, any>`，优先使用类型推断而非重复定义
- **冗余逻辑** — 提取重复代码，合并复制粘贴的分支
- **魔法值** — 禁止硬编码数字、路由字符串或标识符
- **代码风格** — 禁止内联样式、空 catch 块、生产代码中的 console.log

## 自定义

SKILL.md 文件是自包含的，你可以根据项目需要进行调整：

- 编辑审查规则以匹配团队的编码规范
- 调整故事文件结构以适应你的工作流
- 添加项目特定的澄清问题模板
- 更改文件路径约定（`docs/prd/`、`docs/story/`）

## 前置要求

- 任何支持自定义技能的 AI 编程助手

## 许可证

MIT
