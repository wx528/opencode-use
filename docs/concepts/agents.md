---
title: Agents(代理)
---

# Agents(代理)

opencode 把"一个被调用的 LLM 实例"叫 agent。每个 agent 有自己的 prompt、permission、可选 model。

## 内置 agent

| 名字 | 用途 |
|---|---|
| `build` | 默认主代理,能读写文件、跑命令 |
| `plan` | 只读 + 思考,默认禁止 edit,适合先规划后执行 |
| `general` | 与 build 类似但偏通用子任务 |
| `explore` | 快速探索代码库,只读 |
| `title` / `summary` / `compaction` | 隐藏的内部 agent,不要手动调用 |

## mode 三种取值

- `primary`:可作为入口,可被用户直接使用,`@` 菜单可见
- `subagent`:只能被其他 agent 通过 `task` 工具调用,不在用户 `@` 菜单(除非不 hidden)
- `all`:两种都行

## 何时自定义

- 反复出现的同一类任务(如"跑测试"、"PR review"),抽成 subagent
- 想用不同的 temperature / model / permission
- 想给团队成员固定 prompt(团队文档场景)

## 自定义两种形式

**内联**:`opencode.json` 里 `agent.<name>.{...}`
**文件**:`.opencode/agent/<name>.md` 或 `.opencode/agents/<name>.md`,frontmatter 写字段,正文 = prompt