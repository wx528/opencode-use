---
title: 02 — 加新功能
scenario_id: 02-add-feature
status: draft
opencode_version: 1.18.18
related_example: ../../examples/02-add-feature/
---

# 加新功能

## 目标

主代理 + `feature-builder` subagent 协作:主代理负责理解需求、整合、review;subagent 负责单点实现。

## 用到的能力

- agents: `build`(主代理,默认)、`feature-builder`(自定义 subagent)
- commands: `feat`(自定义,把"委派 subagent"包成一条命令)
- skills: 无
- MCP: 无
- permissions: 主代理全开;`feature-builder` 与场景 01 相同(`task: deny`)

## 工作流

1. 用户描述需求:`/feat 给 User 加一个 avatar_url 字段`
2. 主代理解析命令,把 spec 传给 `feature-builder`
3. `feature-builder` 单点实现:改 model、写 migration、补测试、跑测试
4. `feature-builder` 返回报告:改了哪些文件、测试结果、未决问题
5. 主代理读报告,决定接受 / 追问 / 提 PR

## 配置要点

**自定义命令** —— 见 `docs/concepts/commands.md`(待写):

```json
{
  "command": {
    "feat": {
      "description": "Implement a feature via feature-builder subagent",
      "template": "Use the feature-builder subagent to implement: $ARGUMENTS"
    }
  }
}
```

**`$ARGUMENTS` 替换**:用户在 `/feat 之后输入的所有内容`。

**`default_agent: "build"`** —— 显式声明主代理。

## 完整示例

完整可跑配置在仓库根的 `examples/02-add-feature/`(见其 `README.md`):

```bash
cd examples/02-add-feature
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `feature-builder` 的 prompt 要明确"不要扩大范围、不要 refactor 不相关代码",否则 subagent 容易越权。
- command 文件形式未在本场景演示,见未来 `concepts/commands.md`。
- 想让多个 subagent 协作(plan → feature-builder → tester),可以放开 `task: allow`,但要小心 `subagent_depth`。
