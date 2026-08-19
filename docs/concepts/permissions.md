---
title: Permissions(权限)
---

# Permissions(权限)

opencode 用 permission 控制 agent 能做什么。

## 三种动作

- `allow` — 无需确认直接放行
- `ask` — 每次调用前问用户确认
- `deny` — 直接拒绝,不可被覆盖

## 配置位置

- 顶层 `permission`:**所有 agent 默认值**
- 每个 agent 自己 `permission`:**覆盖顶层**(per-agent 优先)

## 重要:已废弃的 `tools`

旧版用 `agent.X.tools = { read: true, write: true, ... }`。**已废弃**,改用:

```json
{
  "agent": {
    "tester": {
      "permission": {
        "read": "allow",
        "edit": "allow",
        "bash": "allow",
        "task": "deny"
      }
    }
  }
}
```

## 模式匹配

per-tool value 可以是对象 `{ pattern: action }`。**最后匹配胜出**,宽规则放前、窄规则放后:

```json
{
  "permission": {
    "bash": {
      "git *": "allow",
      "rm *": "deny",
      "*": "ask"
    }
  }
}
```

## 常用 key

`read, edit, glob, grep, list, bash, task, external_directory, todowrite, question, webfetch, websearch, lsp, doom_loop, skill`

其中 `todowrite, question, webfetch, websearch, doom_loop` 只接受扁平动作,不能用 pattern 对象。

## `external_directory`

允许/拒绝访问项目目录外的路径:

```json
{
  "permission": {
    "external_directory": {
      "~/secrets/**": "deny",
      "*": "allow"
    }
  }
}
```

## 限制子代理嵌套

`subagent_depth`(顶层配置,默认 1):最大子代理嵌套层数。`1` = 子代理不能再开子代理,防递归爆炸。
