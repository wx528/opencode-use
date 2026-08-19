---
title: subagent vs primary
---

# subagent vs primary

agent 有两种使用方式,差别体现在能不能被用户直接调用。

## primary

- 用户输入时直接选用(`@primary-agent` 或默认)
- 可作为会话入口
- `default_agent` 必须指向一个 primary

## subagent

- 不能直接被用户选,必须由其他 agent 用 `task` 工具调用
- 默认不在 `@` 自动完成里(除非 `hidden: false`)
- 适合"专责小角色":tester、reviewer、refactor-planner 等

## `hidden`

subagent 的可见性:

- `hidden: false`(默认)— 出现在 `@` 菜单,但仍需通过 `task` 调用
- `hidden: true` — 完全不出现,只能通过 `task` 调用

## 委派流程

```
primary (build)
  └─ task(subagent: tester)  ← permission 决定它能干什么
        └─ 返回结果给 primary
```

`permission.task` 控制能否发起委派。`"deny"` = 这个 agent 不能调用任何 subagent。

## `subagent_depth`

顶层配置,默认 1,防止子代理无限递归。设为 2 = subagent 可再开一层 subagent。
