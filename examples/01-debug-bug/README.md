# examples/01-debug-bug

## 目标

演示用 `tester` subagent 跑测试找 bug 的工作流。

## 对应场景文档

[docs/scenarios/01-debug-bug.md](../../docs/scenarios/01-debug-bug.md)

## 运行步骤

```bash
cd examples/01-debug-bug
opencode
# 在 prompt 中:
# "用 tester agent 帮我写 3 个 unit test 覆盖 src/,然后跑一遍"
```

## 文件树

```
01-debug-bug/
├── README.md
└── .opencode/
    ├── opencode.json
    └── agent/
        └── tester.md
```

## 备注

- `opencode.json` 故意用了**废弃的 `tools` 字段**,作为对比示例。
- 实际推荐写法见 `docs/scenarios/01-debug-bug.md` 与 `docs/concepts/permissions.md`。
