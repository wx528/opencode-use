---
title: 04 — PR review
scenario_id: 04-pr-review
status: draft
opencode_version: 1.18.18
related_example: ../../examples/04-pr-review/
---

# PR review

## 目标

把"看 diff、找问题、给建议"交给一个**只读**的 reviewer subagent,主代理负责汇总。

## 用到的能力

- agents: `reviewer`(自定义 subagent,`hidden: true`)
- commands: 无
- skills: 无
- MCP: 无
- permissions: 读权限全开,`edit: deny`,`bash: ask`(谨慎),`webfetch: allow`

## 工作流

1. 用户:"review PR #42"
2. 主代理拿 diff(本地 / `gh pr diff` / `webfetch` raw diff)
3. 主代理委派 `reviewer` subagent,只传 diff 文本(不传整个仓库)
4. `reviewer` 输出编号 review:文件:行号 + 问题 + 修复建议
5. 主代理把 review 整理后回给用户

## 配置要点

**`hidden: true`** —— subagent 不出现在 `@` 菜单:

```json
{
  "agent": {
    "reviewer": {
      "mode": "subagent",
      "hidden": true,
      "permission": { "edit": "deny", "bash": "ask" }
    }
  }
}
```

**`webfetch: allow`** —— reviewer 抓外部 API 文档查正确用法:

```json
{
  "permission": {
    "webfetch": "allow"
  }
}
```

## 完整示例

完整可跑配置在仓库根的 `examples/04-pr-review/`(见其 `README.md`):

```bash
cd examples/04-pr-review
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- diff 太大(>几百行)时,**只传相关片段**,不要把整个 diff 塞给 reviewer,token 会爆。
- `hidden: true` 不等于"安全" —— 仍可被 `task` 调用。安全靠 permission,不是 hidden。
- 想让 reviewer 自动 `gh pr diff`,要给它 `bash: ask` 而非 deny,否则连抓 diff 都不行。