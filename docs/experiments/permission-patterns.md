---
title: Experiment — permission patterns 对比
---

# permission patterns 对比

记录几种 permission 写法的优缺点,作为未来选择参考。

## 写法 A:全 allow,顶层一刀切

```json
{
  "permission": "allow"
}
```

- ✅ 零摩擦,任何 subagent 都能做任何事
- ❌ 几乎从不"是你想要的"

## 写法 B:per-tool 三动作

```json
{
  "permission": {
    "read": "allow",
    "edit": "allow",
    "bash": "allow",
    "task": "deny"
  }
}
```

- ✅ 简单清晰,适合"我信任这个 subagent"的场景(场景 01/02)
- ❌ bash 全 allow 风险大,任何 `rm -rf` 都行

## 写法 C:bash 模式匹配 + 顶层 deny

```json
{
  "permission": {
    "edit": "allow",
    "bash": {
      "git *": "allow",
      "pytest *": "allow",
      "rm *": "deny",
      "*": "ask"
    }
  }
}
```

- ✅ 白名单 + 默认 ask,安全且实用
- ❌ 写起来长;规则顺序敏感(最后匹配胜出)

## 写法 D:子代理嵌套控深度

顶层加 `subagent_depth: 2`,允许 subagent 再开一层 subagent,做"planner → executor"二级委派。

```json
{
  "subagent_depth": 2,
  "permission": {
    "task": "allow"
  }
}
```

- ✅ 支持复杂多 agent 编排
- ❌ 调试困难;token 消耗爆炸

## 我的偏好(更新于 2026-08-20)

场景 01/02 用 B;场景 03/04 用 C;复杂编排才用 D。

(每次升级后回来看,如有调整改这里。)
