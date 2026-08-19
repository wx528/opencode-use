# examples/04-pr-review

## 目标

演示 PR review 工作流:主代理拿到 diff → 委派只读 `reviewer` subagent → 输出结构化 review。

## 对应场景文档

[docs/scenarios/04-pr-review.md](../../docs/scenarios/04-pr-review.md)

## 运行步骤

```bash
cd examples/04-pr-review
opencode
# 在 prompt 中:
# "review 一下 PR #42 的 diff"
# 主代理会用 reviewer subagent 给结构化反馈。
```

## 文件树

```
04-pr-review/
├── README.md
└── .opencode/
    ├── opencode.json
    └── agent/
        └── reviewer.md
```

## 备注

- `reviewer` 是 `hidden: true`,不污染 `@` 菜单 —— 用户根本意识不到它的存在,只能通过 `task` 调用。
- `edit: deny` + `bash: ask` = 默认完全只读,跑命令需要用户确认。
- `webfetch: allow` 允许 reviewer 抓外部文档/issue 链接验证 API 用法。