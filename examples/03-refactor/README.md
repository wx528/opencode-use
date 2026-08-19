# examples/03-refactor

## 目标

演示"重构"工作流:用 `plan` 主代理(只读) + `refactor-planner` subagent 出方案,人工 review 后再执行。

## 对应场景文档

[docs/scenarios/03-refactor.md](../../docs/scenarios/03-refactor.md)

## 运行步骤

```bash
cd examples/03-refactor
opencode
# 在 prompt 中:
# "把 src/legacy/ 拆成 service + repository 两层"
# 主代理(plan)会调用 refactor-planner 出方案,然后停下来等你确认。
```

## 文件树

```
03-refactor/
├── README.md
└── .opencode/
    ├── opencode.json
    └── agent/
        └── refactor-planner.md
```

## 备注

- `default_agent: "plan"` —— 进入"只规划不执行"模式。
- `build` 被 `disable: true` 屏蔽,防止误改文件。
- `refactor-planner` 的 `edit/bash: deny` 是双保险:即使被委派也无法写文件。