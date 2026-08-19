---
title: 首页
---

# opencode-use

个人备忘,记录 opencode 用法的工作流与踩坑。

> 受众:项目作者本人(短期可能扩展为团队参考)。

## 场景(每个配可运行示例)

- [01 — 跑测试 + 找 bug](scenarios/01-debug-bug.md) — `tester` subagent + 低 temperature
- [02 — 加新功能](scenarios/02-add-feature.md) — `feature-builder` + `/feat` command
- [03 — 重构](scenarios/03-refactor.md) — `plan` 模式 + `refactor-planner` + `build` 禁用
- [04 — PR review](scenarios/04-pr-review.md) — 隐藏的 `reviewer` subagent

## Concepts

- [Agents](concepts/agents.md) — 内置代理 + mode 三种取值
- [Permissions](concepts/permissions.md) — 三动作、已废弃的 `tools`、模式匹配
- [subagent vs primary](concepts/subagent-vs-primary.md) — 两种使用方式 + `hidden`

## Experiments

- [permission patterns 对比](experiments/permission-patterns.md)

## 维护状态

- 最近更新:见 `git log`
- 当前 opencode 版本:见各场景 frontmatter `opencode_version`

## 如何贡献(给自己看)

1. 在 `docs/scenarios/` 起新文件,从模板起手,标 `status: draft`
2. 在 `examples/NN-name/` 起可运行子项目
3. `cd && opencode` 手动验证
4. 回场景文档补全"工作流"、"配置要点"
5. `status: working` → 提交
6. 真实任务用过后 → `status: verified`