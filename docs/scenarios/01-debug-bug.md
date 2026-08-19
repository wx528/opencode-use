---
title: 01 — 跑测试 + 找 bug
scenario_id: 01-debug-bug
status: working
opencode_version: 1.18.18
related_example: ../../examples/01-debug-bug/
---

# 跑测试 + 找 bug

## 目标

把"写测试、跑测试、报告失败、给修复建议"这套工作交给 `tester` subagent。

## 用到的能力

- agents: `tester`(自定义 subagent)
- commands: 无
- skills: 无
- MCP: 无
- permissions: `read/edit/bash: allow`,`task: deny`(防止 tester 自己再开 subagent)

## 工作流

1. 主代理(`build`)收到任务:"给这个函数补测试"
2. 主代理用 `task` 工具调用 `tester` subagent,把目标文件路径传过去
3. `tester` 写测试 → 跑 pytest → 收集失败
4. `tester` 返回结构化报告:失败用例名、重现步骤、修复建议
5. 主代理读报告,决定采纳 / 追问 / 自己改

## 配置要点

**推荐的 modern 写法** —— 见 [permissions](../concepts/permissions.md):

```json
{
  "agent": {
    "tester": {
      "description": "Tester / QA - writes & runs tests, finds bugs, suggests fixes",
      "mode": "subagent",
      "temperature": 0.1,
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

文件形式(`.opencode/agent/tester.md`,正文即 prompt):

```markdown
---
description: Tester / QA - ...
mode: subagent
temperature: 0.1
permission:
  read: allow
  edit: allow
  bash: allow
  task: deny
---

You are the Tester. ...
```

两种形式的取舍见 [agents](../concepts/agents.md) 与 [subagent vs primary](../concepts/subagent-vs-primary.md)。

## 完整示例

完整可跑配置在仓库根的 `examples/01-debug-bug/`(见其 `README.md`):

```bash
cd examples/01-debug-bug
opencode
# "用 tester agent 帮我写 3 个 unit test 覆盖 src/,然后跑一遍"
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `examples/01-debug-bug/.opencode/opencode.json` 故意保留了**已废弃的 `tools` 字段**作为反面教材,不要照抄:它只能表达"能/不能",无法表达 `ask`,也不支持 pattern 匹配;schema 里它的说明就是 `@deprecated Use 'permission' field instead`。另外注意 `permission` 没有 `write` 这个 key,写文件归 `edit` 管。
- `temperature: 0.1` 比默认低,让测试输出更稳定(避免随机性掩盖 bug)。
- `task: deny` 重要 —— tester 自己开 subagent 会导致上下文膨胀。
- opencode 升级后(主目录 `.opencode/config.json` 字段变化),把本场景 `status` 降回 `working` 跑一遍,再升回 `verified`。
