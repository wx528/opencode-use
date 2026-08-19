# opencode-use

个人备忘型 wiki,记录 [opencode](https://opencode.ai) 的工作流、配置与踩坑。

## 本地预览

```bash
pip install -r requirements.txt
mkdocs serve
```

打开 http://127.0.0.1:8000

## 仓库结构

```
.
├── docs/                       # MkDocs 源文件
│   ├── scenarios/              # 场景文档
│   ├── concepts/               # 单点参考
│   ├── experiments/            # 探索性记录
│   └── superpowers/            # 设计文档与实施计划
├── examples/                   # 可运行的 opencode 子项目
│   ├── 01-debug-bug/
│   ├── 02-add-feature/
│   ├── 03-refactor/
│   └── 04-pr-review/
├── mkdocs.yml
├── requirements.txt
└── README.md
```

## 内容索引

### 场景

- [01 — 跑测试 + 找 bug](docs/scenarios/01-debug-bug.md)
- [02 — 加新功能](docs/scenarios/02-add-feature.md)
- [03 — 重构](docs/scenarios/03-refactor.md)
- [04 — PR review](docs/scenarios/04-pr-review.md)

### Concepts

- [Agents](docs/concepts/agents.md)
- [Permissions](docs/concepts/permissions.md)
- [subagent vs primary](docs/concepts/subagent-vs-primary.md)

### Experiments

- [permission patterns 对比](docs/experiments/permission-patterns.md)

## 子示例运行方式

```bash
cd examples/01-debug-bug
opencode
```

## 升级 opencode 后的回查流程

每个场景 frontmatter 有 `opencode_version` 字段。升级后:

1. 把所有 `verified` 降为 `working`
2. 逐个 `cd examples/NN-name && opencode` 跑过
3. 没问题 → 升回 `verified`