# examples/02-add-feature

## 目标

演示"加新功能"工作流:主代理收到功能 spec → 委派给 `feature-builder` subagent → 整合。

## 对应场景文档

[docs/scenarios/02-add-feature.md](../../docs/scenarios/02-add-feature.md)

## 运行步骤

```bash
cd examples/02-add-feature
opencode
# 方式 A:在 prompt 中直接说:
#   "加一个 foo() 函数,接受字符串返回反转"
# 方式 B:用自定义命令:
#   /feat 加一个 foo() 函数,接受字符串返回反转
```

## 文件树

```
02-add-feature/
├── README.md
└── .opencode/
    ├── opencode.json
    └── agent/
        └── feature-builder.md
```

## 备注

- `feature-builder` 用文件形式定义 prompt(`.opencode/agent/feature-builder.md`)。
- `command.feat` 提供快捷入口,把"调用 subagent"包成一个命令。
- 主代理(`build`)默认入口由 `default_agent` 指定。