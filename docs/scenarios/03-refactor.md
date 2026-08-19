---
title: 03 — 重构
scenario_id: 03-refactor
status: draft
opencode_version: 1.18.18
related_example: ../../examples/03-refactor/
---

# 重构

## 目标

把"重构"强制切成两阶段:**先规划、再执行**,并通过权限边界确保 plan 阶段不能动文件。

## 用到的能力

- agents: `plan`(内置,默认主代理)、`refactor-planner`(自定义 subagent,只读)、`build` 被禁用
- commands: 无
- skills: 无
- MCP: 无
- permissions: 详见下文,核心是 `edit: deny` + `bash: ask/deny`

## 工作流

1. `default_agent: "plan"` —— 主代理默认进入只读思考模式
2. 用户提重构需求(例:"拆 src/legacy/")
3. 主代理委派 `refactor-planner` 读代码,产出编号方案
4. 主代理读方案,确认风险与步骤后**返回给用户**
5. 用户手动确认后,切到 `build` agent(或新建 session 用默认 `build`)执行

## 配置要点

**禁用 build 代理**:

```json
{
  "agent": {
    "build": { "disable": true }
  }
}
```

**`plan` 代理默认禁止 edit**,但可显式覆盖:

```json
{
  "agent": {
    "plan": {
      "permission": { "bash": "ask" }
    }
  }
}
```

**`refactor-planner` 双重 deny**:

```json
{
  "permission": {
    "edit": "deny",
    "bash": "deny"
  }
}
```

不依赖"主代理不去调用",而是 subagent 自己就没法改。

## 完整示例

完整可跑配置在仓库根的 `examples/03-refactor/`(见其 `README.md`):

```bash
cd examples/03-refactor
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `disable: true` 是顶层禁用,不是隐藏 —— 用户仍能 `@build` 选到(直到 opencode 完全移除该入口)。要做"硬墙"还是靠 permission。
- 双层 deny(主代理 + subagent)更稳:即使 plan 模式被绕过,subagent 自己也没法动。
- 想自动化"plan → 用户确认 → build"流程,可以加一个 `/refactor` command 把两阶段串起来。