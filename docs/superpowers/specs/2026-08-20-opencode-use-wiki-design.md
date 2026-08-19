# opencode-use Wiki — Design

**Date:** 2026-08-20
**Status:** Approved
**Owner:** Scott (个人项目)

## Purpose

把 `opencode-use` 仓库建成一份**个人备忘型 wiki**,记录探索 opencode 用法的工作流、可运行示例与踩坑。受众是项目作者本人(短期可能扩展为团队参考)。

## Goals & Non-Goals

**Goals**
- 场景驱动:每篇文档以一个真实工作流为主线,讲清"用哪些 agent/command/skill/MCP/permission 组合"。
- 可运行示例:每个场景配一个能直接 `cd && opencode` 跑起来的子项目,验证配置写法真实可用。
- 个人备忘友好:中文为主、关键术语保留英文,容许 `draft` 状态内容长期存在。
- 升级可追溯:每篇文档标注对应的 opencode 版本,升级时回查。

**Non-Goals**
- 不做对外发布 / SEO / 多语言切换。
- 不写 CI、自动部署、版本化文档(`mike`)、changelog。
- 不追求示例代码优雅,只追求"最短可跑"。
- 不替代官方文档;只解释官方文档没说清或自己踩坑的点。

## Architecture

```
opencode-use/
├── docs/                          # MkDocs 源文件
│   ├── index.md                   # 首页 + 总目录 + 维护状态
│   ├── scenarios/                 # 场景文档
│   │   ├── 01-debug-bug.md
│   │   ├── 02-add-feature.md
│   │   ├── 03-refactor.md
│   │   └── 04-pr-review.md
│   ├── concepts/                  # 单点参考
│   │   ├── agents.md
│   │   ├── permissions.md
│   │   └── subagent-vs-primary.md
│   └── experiments/               # 探索性记录
│       └── permission-patterns.md
├── examples/                      # 可运行子项目
│   ├── 01-debug-bug/
│   │   ├── README.md
│   │   └── .opencode/
│   │       ├── opencode.json
│   │       └── agent/
│   │           └── tester.md
│   ├── 02-add-feature/
│   ├── 03-refactor/
│   └── 04-pr-review/
├── mkdocs.yml
├── requirements.txt
├── docs/superpowers/specs/        # 设计文档与计划
└── .gitignore                     # 排除 site/、__pycache__ 等
```

**MkDocs 配置要点**
- 主题:`material`(信息密度高、有搜索、暗色模式、tabs)
- `docs_dir: docs`、`site_dir: site`
- `nav:` 显式列出(避免自动排序乱)
- `exclude_docs:` 排除 `examples/**` 与 `docs/superpowers/**`
- `markdown_extensions:` `admonition`、`tabbed`、`codehilite`、`toc with permalink`、`attr_list`
- `plugins:` `search`(够用,不开 i18n)

## Components & Contracts

### 场景文档模板(`docs/scenarios/NN-name.md`)

```markdown
---
title: 调试 Bug
scenario_id: 01-debug-bug
status: draft            # draft | working | verified
opencode_version: 0.x.x
related_example: ./examples/01-debug-bug/
---

# 调试 Bug

## 目标
一段话描述场景要解决什么、什么时候会用。

## 用到的能力
- agents: tester、general ...
- commands: ...
- skills: ...
- MCP: ...
- permissions: 关键约束

## 工作流
1. 第一步:用什么 prompt / 命令
2. 第二步:看 subagent 返回什么
3. ...

## 配置要点
粘关键片段(.opencode/agent/tester.md、permission 片段等),
用 ``` 标注文件路径。

## 完整示例
[examples/01-debug-bug/README.md](../examples/01-debug-bug/README.md)

## 踩坑与备注
自由记录。
```

**frontmatter 字段**
- `title`:在 nav 中显示的中文标题
- `scenario_id`:与 `examples/` 子目录编号对齐,给脚本处理留口子
- `status`:`draft` | `working` | `verified`
- `opencode_version`:当前 opencode 版本(`opencode --version`)
- `related_example`:相对路径,**应**与子目录编号一致(目前手动维护,后续可加脚本校验)

### 子示例模板(`examples/NN-name/`)

```
examples/01-debug-bug/
├── README.md                # 入口:目标、对应场景文档、运行步骤、文件树
└── .opencode/
    ├── opencode.json
    └── agent/
        └── tester.md
```

**`examples/NN-name/README.md` 模板**
- 一句话目标
- 对应场景文档链接(相对路径)
- 运行步骤(`cd` 到该目录、`opencode` 命令)
- 文件树(`tree` 输出或手写)

### 关键约束

- `examples/` 里**不放场景解释**,只放配置和最短 README
- 场景文档里**不放完整可运行代码**(只贴片段),完整配置去 `examples/` 看
- 两边都从 `scenario_id` 出发维护,改一边要提醒另一边
- 编号同步:`scenarios/NN-name.md` ↔ `examples/NN-name/`,手动保持

## Content Seeding (首期)

**4 个场景(种子):**

| # | 场景 | 主要考察点 |
|---|---|---|
| 01 | 跑测试 + 找 bug | subagent `tester`、permission 控 `bash`、低 temperature |
| 02 | 加新功能 | 主代理 + `general` subagent 协作、自定义 command |
| 03 | 重构 | `plan` 代理、`disable` 内置代理、文件权限边界 |
| 04 | PR review | 读权限为主、`bash` 谨慎、`hidden: true` 隐藏 subagent |

**3 个概念页(场景反复用到):**

| 文件 | 内容 |
|---|---|
| `concepts/agents.md` | 内置代理(build/plan/general/explore)区别、何时自定义、mode 三种 |
| `concepts/permissions.md` | 三种动作、`tools` 已废弃改 `permission`、per-agent 覆盖、`external_directory`、`subagent_depth` |
| `concepts/subagent-vs-primary.md` | subagent/primary/all 区别、`@` 自动完成、`hidden` 用法 |

**1 篇实验记录(模板):**
- `experiments/permission-patterns.md`:对比几种 permission 写法(全 allow vs 模式匹配 vs 嵌套 deny)

**`docs/index.md` 首页:**
- 一句话定位:"个人备忘,记录 opencode 用法的工作流与踩坑"
- 场景总目录(链 4 篇)
- 概念总目录(链 3 篇)
- 实验总目录
- 维护状态(最近更新日期、当前 opencode 版本)
- "如何贡献"(实际是给自己的 6 步流程)

## Conventions

### 命名
- 文件名 kebab-case:`debug-bug.md`,不用空格或中文
- 场景与子示例编号用两位数前缀 `NN-`
- 编号严格一致:`docs/scenarios/01-debug-bug.md` ↔ `examples/01-debug-bug/`

### status 流转
- `draft`:只有思路或片段,example 可能跑不通
- `working`:example 跑通过,文档可能糙、有 TODO
- `verified`:文档 + example 都稳定,至少手动验证过一次升级兼容

### 版本与同步
- 每篇 frontmatter 写 `opencode_version`(取 `opencode --version`)
- opencode 升级时:把当前 `verified` 降为 `working`,跑一遍 example,再升回 `verified`
- `experiments/` 不强制 `status`,允许永久 `draft`

### git 习惯
- 提交粒度:"一篇场景写完 + example 跑通" 为单位
- 提交信息:`docs(scenarios): add 01-debug-bug` / `examples(01): add tester agent`
- 不提交 `site/`(`.gitignore` 排除)

### 新增场景的标准流程
1. 在 `docs/scenarios/` 创建 `NN-name.md`(从模板起手),标 `status: draft`
2. 在 `examples/NN-name/` 创建真实可跑配置
3. `cd examples/NN-name && opencode` 手动验证
4. 回场景文档补全"工作流"、"配置要点"
5. 改 frontmatter `status: working`,提交
6. 跑过一次以上真实任务后改 `status: verified`

### 不写什么(YAGNI)
- 不写 CI / 自动部署(GitHub Pages)
- 不写贡献指南 / LICENSE / 多语言切换
- 不写 changelog,wiki 本身就是 changelog
- 不引入 `mike` 做版本化文档
- `examples/` 不追求代码优雅,**最短可跑优先**

## Open Questions

无。设计已与作者确认,4 节全部 approve。