# opencode-use Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 `opencode-use` 仓库建成一份个人备忘型 MkDocs wiki,围绕 4 个 opencode 工作流场景 + 3 篇概念参考 + 1 篇实验记录,每篇场景配可运行的子示例项目。

**Architecture:** 仓库根是 MkDocs 站点源;`docs/` 下分 `scenarios/`、`concepts/`、`experiments/` 三类;`examples/` 是平行的可运行子项目,每个可独立 `cd && opencode` 验证。MkDocs 用 material 主题,`exclude_docs` 排除 `examples/` 与 `docs/superpowers/`。

**Tech Stack:**
- MkDocs ≥ 1.5 + mkdocs-material ≥ 9.0 (Python)
- Git (仓库已 init,remote = `git@github.com:wx528/opencode-use.git`)
- opencode (用于子示例的人工验证,不在本计划自动跑)

## Global Constraints

来自 spec,逐条引用,所有任务隐式遵守:

- **结构**:`docs/scenarios/`、`docs/concepts/`、`docs/experiments/` 三类;`examples/` 平行;`docs/superpowers/` 放设计与计划
- **MkDocs 配置**:`docs_dir: docs`、`site_dir: site`、主题 `material`、`exclude_docs` 排除 `examples/**` 与 `docs/superpowers/**`、`markdown_extensions` 至少含 `admonition`、`pymdownx.tabbed`、`codehilite`、`toc with permalink`、`attr_list`、`plugins: [search]`
- **场景文件命名**:`docs/scenarios/NN-name.md` ↔ `examples/NN-name/`,编号严格一致,两位数前缀
- **场景 frontmatter**:`title`、`scenario_id`、`status` (`draft|working|verified`)、`opencode_version`、`related_example`
- **示例 README 必须有**:目标、对应场景文档链接(相对路径)、运行步骤、文件树
- **链接约定**:`examples/` 在 `docs_dir` 之外,**不能**写 `[X](../../examples/X/README.md)` 这种 MkDocs 站内链接 —— `--strict` 模式必失败。场景文档指向子示例时统一用:(a) frontmatter `related_example` 记路径(纯字符串,不影响构建),(b) 代码块 `cd examples/X && opencode`,(c) `!!! note` 说明为什么不是超链接
- **语言**:中文叙述 + 关键术语保留英文(subagent、permission、`.opencode/agent/` 等)
- **权限字段**:用 `permission`(已替代废弃的 `tools`)
- **opencode.json 必须含**:`"$schema": "https://opencode.ai/config.json"`
- **git**:不提交 `site/`;提交信息形如 `docs(scenarios): add 01-debug-bug`
- **不写**:CI、自动部署、`mike`、changelog、LICENSE、`requirements.txt` 之外的依赖

---

## File Structure

新增/修改的文件清单(任务创建它们时再列精确路径):

| 文件 | 所属任务 | 用途 |
|---|---|---|
| `.gitignore` | T1 | 排除 site/、__pycache__/ 等 |
| `requirements.txt` | T1 | 锁 MkDocs 依赖 |
| `mkdocs.yml` | T1 起,T8 收尾 | 站点配置,nav 在 T8 完整化 |
| `README.md` | T1 起,T8 收尾 | GitHub landing,T8 含完整 TOC |
| `docs/index.md` | T1 起,T8 收尾 | 首页,T8 含完整目录 |
| `docs/concepts/agents.md` | T2 | 内置代理 + mode |
| `docs/concepts/permissions.md` | T2 | 三动作 + 字段 |
| `docs/concepts/subagent-vs-primary.md` | T2 | 三种 mode 区别 |
| `docs/scenarios/01-debug-bug.md` | T3 | 场景 01 文档 |
| `docs/scenarios/02-add-feature.md` | T4 | 场景 02 文档 |
| `docs/scenarios/03-refactor.md` | T5 | 场景 03 文档 |
| `docs/scenarios/04-pr-review.md` | T6 | 场景 04 文档 |
| `docs/experiments/permission-patterns.md` | T7 | 实验记录 |
| `examples/01-debug-bug/README.md` | T3 | 子示例入口 |
| `examples/01-debug-bug/.opencode/opencode.json` | T3 | 子示例配置 |
| `examples/01-debug-bug/.opencode/agent/tester.md` | T3 | tester subagent 定义 |
| `examples/02-add-feature/README.md` | T4 | 子示例入口 |
| `examples/02-add-feature/.opencode/opencode.json` | T4 | 子示例配置 |
| `examples/02-add-feature/.opencode/agent/feature-builder.md` | T4 | feature-builder subagent 定义 |
| `examples/03-refactor/README.md` | T5 | 子示例入口 |
| `examples/03-refactor/.opencode/opencode.json` | T5 | 子示例配置 |
| `examples/03-refactor/.opencode/agent/refactor-planner.md` | T5 | refactor-planner subagent 定义 |
| `examples/04-pr-review/README.md` | T6 | 子示例入口 |
| `examples/04-pr-review/.opencode/opencode.json` | T6 | 子示例配置 |
| `examples/04-pr-review/.opencode/agent/reviewer.md` | T6 | reviewer subagent 定义 |

---

## Task 1: Repo skeleton & MkDocs bootstrap

**Files:**
- Create: `.gitignore`
- Create: `requirements.txt`
- Create: `mkdocs.yml`
- Create: `docs/index.md` (placeholder)
- Create: `README.md` (placeholder)

**Interfaces:**
- Consumes: 无
- Produces: 一个能 `mkdocs build --strict` 通过的最小站点

- [ ] **Step 1: 写 `.gitignore`**

```gitignore
# MkDocs
/site/
/.cache/

# Python
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/
.env

# IDE / OS
.vscode/
.DS_Store
Thumbs.db
```

- [ ] **Step 2: 写 `requirements.txt`**

```
mkdocs>=1.5
mkdocs-material>=9.0
```

- [ ] **Step 3: 写 `mkdocs.yml`(初始版,T8 会扩 nav)**

```yaml
site_name: opencode-use
site_description: 个人备忘,记录 opencode 用法的工作流与踩坑
docs_dir: docs
site_dir: site

theme:
  name: material
  features:
    - content.code.copy
    - content.tabs.link
    - search.suggest
    - palette.toggle
  palette:
    - media: "(prefers-color-scheme: light)"
      scheme: default
      toggle:
        icon: material/brightness-7
        name: 切换到暗色
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      toggle:
        icon: material/brightness-4
        name: 切换到亮色

markdown_extensions:
  - admonition
  - pymdownx.tabbed
  - codehilite
  - toc:
      permalink: true
  - attr_list

plugins:
  - search

exclude_docs: |
  examples/**
  superpowers/**

nav:
  - 首页: index.md
```

注意:`exclude_docs` 用绝对路径(相对 `docs_dir`),所以写 `superpowers/**` 而非 `docs/superpowers/**`。

- [ ] **Step 4: 写 `docs/index.md`(最小 placeholder,T8 扩)**

```markdown
---
title: 首页
---

# opencode-use

个人备忘,记录 opencode 用法的工作流与踩坑。

> 内容建设中,见 `docs/superpowers/specs/` 与 `docs/superpowers/plans/`。
```

- [ ] **Step 5: 写 `README.md`(GitHub landing,T8 扩)**

```markdown
# opencode-use

个人备忘型 wiki,记录 [opencode](https://opencode.ai) 的工作流、配置与踩坑。

## 本地预览

```
pip install -r requirements.txt
mkdocs serve
```

打开 http://127.0.0.1:8000

## 目录

- `docs/` — MkDocs 源文件
- `examples/` — 可运行的 opencode 子示例(每个可独立 `cd && opencode` 验证)
- `docs/superpowers/specs/` — 设计文档
- `docs/superpowers/plans/` — 实施计划
```

- [ ] **Step 6: 创建虚拟环境并安装依赖**

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows PowerShell
pip install -r requirements.txt
```

期望:`Successfully installed mkdocs-... mkdocs-material-...`。若 `python` 不在 PATH,改用 `py -m venv .venv`。

- [ ] **Step 7: 验证 `mkdocs build --strict`**

```bash
mkdocs build --strict
```

期望:`INFO - Documentation built in X seconds`,无 WARNING,无 ERROR。`site/` 目录生成。

- [ ] **Step 8: 提交**

```bash
git add .gitignore requirements.txt mkdocs.yml docs/index.md README.md
git commit -m "chore: bootstrap MkDocs skeleton"
```

---

## Task 2: Concepts — three reference pages

**Files:**
- Create: `docs/concepts/agents.md`
- Create: `docs/concepts/permissions.md`
- Create: `docs/concepts/subagent-vs-primary.md`
- Modify: `mkdocs.yml`(nav 增加 Concepts 区)

**Interfaces:**
- Consumes: T1 的 mkdocs.yml 骨架
- Produces: 3 篇概念参考,可独立读,`mkdocs build --strict` 通过

- [ ] **Step 1: 写 `docs/concepts/agents.md`**

```markdown
---
title: Agents(代理)
---

# Agents(代理)

opencode 把"一个被调用的 LLM 实例"叫 agent。每个 agent 有自己的 prompt、permission、可选 model。

## 内置 agent

| 名字 | 用途 |
|---|---|
| `build` | 默认主代理,能读写文件、跑命令 |
| `plan` | 只读 + 思考,默认禁止 edit,适合先规划后执行 |
| `general` | 与 build 类似但偏通用子任务 |
| `explore` | 快速探索代码库,只读 |
| `title` / `summary` / `compaction` | 隐藏的内部 agent,不要手动调用 |

## mode 三种取值

- `primary`:可作为入口,可被用户直接使用,`@` 菜单可见
- `subagent`:只能被其他 agent 通过 `task` 工具调用,不在用户 `@` 菜单(除非不 hidden)
- `all`:两种都行

## 何时自定义

- 反复出现的同一类任务(如"跑测试"、"PR review"),抽成 subagent
- 想用不同的 temperature / model / permission
- 想给团队成员固定 prompt(团队文档场景)

## 自定义两种形式

**内联**:`opencode.json` 里 `agent.<name>.{...}`
**文件**:`.opencode/agent/<name>.md` 或 `.opencode/agents/<name>.md`,frontmatter 写字段,正文 = prompt
```

- [ ] **Step 2: 写 `docs/concepts/permissions.md`**

```markdown
---
title: Permissions(权限)
---

# Permissions(权限)

opencode 用 permission 控制 agent 能做什么。

## 三种动作

- `allow` — 无需确认直接放行
- `ask` — 每次调用前问用户确认
- `deny` — 直接拒绝,不可被覆盖

## 配置位置

- 顶层 `permission`:**所有 agent 默认值**
- 每个 agent 自己 `permission`:**覆盖顶层**(per-agent 优先)

## 重要:已废弃的 `tools`

旧版用 `agent.X.tools = { read: true, write: true, ... }`。**已废弃**,改用:

```json
{
  "agent": {
    "tester": {
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

## 模式匹配

per-tool value 可以是对象 `{ pattern: action }`。**最后匹配胜出**,宽规则放前、窄规则放后:

```json
{
  "permission": {
    "bash": {
      "git *": "allow",
      "rm *": "deny",
      "*": "ask"
    }
  }
}
```

## 常用 key

`read, edit, glob, grep, list, bash, task, external_directory, todowrite, question, webfetch, websearch, lsp, doom_loop, skill`

其中 `todowrite, question, webfetch, websearch, doom_loop` 只接受扁平动作,不能用 pattern 对象。

## `external_directory`

允许/拒绝访问项目目录外的路径:

```json
{
  "permission": {
    "external_directory": {
      "~/secrets/**": "deny",
      "*": "allow"
    }
  }
}
```

## 限制子代理嵌套

`subagent_depth`(顶层配置,默认 1):最大子代理嵌套层数。`1` = 子代理不能再开子代理,防递归爆炸。
```

- [ ] **Step 3: 写 `docs/concepts/subagent-vs-primary.md`**

```markdown
---
title: subagent vs primary
---

# subagent vs primary

agent 有两种使用方式,差别体现在能不能被用户直接调用。

## primary

- 用户输入时直接选用(`@primary-agent` 或默认)
- 可作为会话入口
- `default_agent` 必须指向一个 primary

## subagent

- 不能直接被用户选,必须由其他 agent 用 `task` 工具调用
- 默认不在 `@` 自动完成里(除非 `hidden: false`)
- 适合"专责小角色":tester、reviewer、refactor-planner 等

## `hidden`

subagent 的可见性:

- `hidden: false`(默认)— 出现在 `@` 菜单,但仍需通过 `task` 调用
- `hidden: true` — 完全不出现,只能通过 `task` 调用

## 委派流程

```
primary (build)
  └─ task(subagent: tester)  ← permission 决定它能干什么
        └─ 返回结果给 primary
```

`permission.task` 控制能否发起委派。`"deny"` = 这个 agent 不能调用任何 subagent。

## `subagent_depth`

顶层配置,默认 1,防止子代理无限递归。设为 2 = subagent 可再开一层 subagent。
```

- [ ] **Step 4: 更新 `mkdocs.yml` nav**

把 `nav` 改成:

```yaml
nav:
  - 首页: index.md
  - Concepts:
      - agents: concepts/agents.md
      - permissions: concepts/permissions.md
      - subagent vs primary: concepts/subagent-vs-primary.md
```

- [ ] **Step 5: 验证**

```bash
mkdocs build --strict
```

期望:无错误。`site/concepts/` 下有三个 .html。

- [ ] **Step 6: 提交**

```bash
git add docs/concepts/ mkdocs.yml
git commit -m "docs(concepts): add agents, permissions, subagent-vs-primary"
```

---

## Task 3: Scenario 01 — 跑测试 + 找 bug

**Files:**
- Create: `docs/scenarios/01-debug-bug.md`
- Create: `examples/01-debug-bug/README.md`
- Create: `examples/01-debug-bug/.opencode/opencode.json`
- Create: `examples/01-debug-bug/.opencode/agent/tester.md`

**Interfaces:**
- Consumes: T1 的 MkDocs 骨架,T2 的 Concepts 内容(场景文档会链向 Concepts)
- Produces: 场景 01 的文档 + 一个 `cd examples/01-debug-bug && opencode` 可跑通的子示例

- [ ] **Step 1: 写 `examples/01-debug-bug/.opencode/agent/tester.md`**

```markdown
---
description: Tester / QA - writes & runs tests, finds bugs, suggests fixes
mode: subagent
temperature: 0.1
permission:
  read: allow
  edit: allow
  bash: allow
  task: deny
---

You are the Tester. When delegated, write unit/integration tests, run them
(via bash/pytest/etc), report failures with repro steps, suggest fixes.
Be ruthless about coverage and correctness.
```

- [ ] **Step 2: 写 `examples/01-debug-bug/.opencode/opencode.json`**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "tester": {
      "tools": {
        "read": true,
        "write": true,
        "edit": true,
        "bash": true,
        "task": false
      }
    }
  }
}
```

> 注意:此处有意**保留已废弃的 `tools` 字段**作为"踩坑示例"。prompt 由 `.opencode/agent/tester.md` 的 body 提供(不要在 opencode.json 里再写 `prompt:`,文件形式已自带)。

- [ ] **Step 3: 写 `examples/01-debug-bug/README.md`**

```markdown
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
```

- [ ] **Step 4: 写 `docs/scenarios/01-debug-bug.md`**

> 执行前先跑 `opencode --version` 拿到真实版本号,替换下面 `0.x.x`。

```markdown
---
title: 01 — 跑测试 + 找 bug
scenario_id: 01-debug-bug
status: working
opencode_version: <填 opencode --version 输出>
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
- permissions: `read/write/edit/bash: allow`,`task: deny`(防止 tester 自己再开 subagent)

## 工作流

1. 主代理(`build`)收到任务:"给这个函数补测试"
2. 主代理用 `task` 工具调用 `tester` subagent,把目标文件路径传过去
3. `tester` 写测试 → 跑 pytest → 收集失败
4. `tester` 返回结构化报告:失败用例名、重现步骤、修复建议
5. 主代理读报告,决定采纳 / 追问 / 自己改

## 配置要点

**推荐的 modern 写法** —— 见 `docs/concepts/permissions.md`:

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

文件形式:

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

## 完整示例

完整可跑配置在仓库根的 `examples/01-debug-bug/`(见其 `README.md`):

```bash
cd examples/01-debug-bug
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `examples/01-debug-bug/.opencode/opencode.json` 故意保留了**已废弃的 `tools` 字段**作为反面教材,不要照抄。
- `temperature: 0.1` 比默认低,让测试输出更稳定(避免随机性掩盖 bug)。
- `task: deny` 重要 —— tester 自己开 subagent 会导致上下文膨胀。
- opencode 升级后(主目录 `.opencode/config.json` 字段变化),把本场景 `status` 降回 `working` 跑一遍,再升回 `verified`。
```

- [ ] **Step 5: 验证 JSON 语法**

```bash
python -c "import json; json.load(open('examples/01-debug-bug/.opencode/opencode.json'))"
```

期望:无输出(成功)。若 `json.JSONDecodeError`,检查引号/逗号。

- [ ] **Step 6: 验证 MkDocs 构建**

```bash
mkdocs build --strict
```

期望:无错误。`site/scenarios/01-debug-bug/` 存在。

- [ ] **Step 7: 提交**

```bash
git add examples/01-debug-bug/ docs/scenarios/01-debug-bug.md
git commit -m "docs(scenarios): add 01-debug-bug with tester subagent"
```

---

## Task 4: Scenario 02 — 加新功能

**Files:**
- Create: `docs/scenarios/02-add-feature.md`
- Create: `examples/02-add-feature/README.md`
- Create: `examples/02-add-feature/.opencode/opencode.json`
- Create: `examples/02-add-feature/.opencode/agent/feature-builder.md`

**Interfaces:**
- Consumes: T1、T2、T3 产出的导航与概念页
- Produces: 场景 02 的文档 + 可运行子示例

- [ ] **Step 1: 写 `examples/02-add-feature/.opencode/agent/feature-builder.md`**

```markdown
---
description: Feature builder - implements a single well-scoped feature end-to-end
mode: subagent
temperature: 0.2
permission:
  read: allow
  edit: allow
  bash: allow
  task: deny
---

You are the Feature Builder. You receive a single feature spec (what to build,
where, acceptance criteria) and implement it end-to-end: read existing code,
write new code, run existing tests, write new tests for your code, report
what you did and any open questions. Do not refactor unrelated code. Do not
expand scope. If the spec is ambiguous, ask via your final report instead of
guessing.
```

- [ ] **Step 2: 写 `examples/02-add-feature/.opencode/opencode.json`**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "build",
  "agent": {
    "feature-builder": {
      "mode": "subagent",
      "temperature": 0.2,
      "permission": {
        "read": "allow",
        "edit": "allow",
        "bash": "allow",
        "task": "deny"
      }
    }
  },
  "command": {
    "feat": {
      "description": "Implement a feature via feature-builder subagent",
      "template": "Use the feature-builder subagent to implement: $ARGUMENTS"
    }
  }
}
```

> prompt 来自 `.opencode/agent/feature-builder.md` 的 body,opencode.json 不重复声明。

- [ ] **Step 3: 写 `examples/02-add-feature/README.md`**

```markdown
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
```

- [ ] **Step 4: 写 `docs/scenarios/02-add-feature.md`**

> 执行前先跑 `opencode --version`,替换下面 `0.x.x`。

```markdown
---
title: 02 — 加新功能
scenario_id: 02-add-feature
status: working
opencode_version: <填 opencode --version 输出>
related_example: ../../examples/02-add-feature/
---

# 加新功能

## 目标

主代理 + `feature-builder` subagent 协作:主代理负责理解需求、整合、review;subagent 负责单点实现。

## 用到的能力

- agents: `build`(主代理,默认)、`feature-builder`(自定义 subagent)
- commands: `feat`(自定义,把"委派 subagent"包成一条命令)
- skills: 无
- MCP: 无
- permissions: 主代理全开;`feature-builder` 与场景 01 相同(`task: deny`)

## 工作流

1. 用户描述需求:`/feat 给 User 加一个 avatar_url 字段`
2. 主代理解析命令,把 spec 传给 `feature-builder`
3. `feature-builder` 单点实现:改 model、写 migration、补测试、跑测试
4. `feature-builder` 返回报告:改了哪些文件、测试结果、未决问题
5. 主代理读报告,决定接受 / 追问 / 提 PR

## 配置要点

**自定义命令** —— 见 `docs/concepts/commands.md`(待写):

```json
{
  "command": {
    "feat": {
      "description": "Implement a feature via feature-builder subagent",
      "template": "Use the feature-builder subagent to implement: $ARGUMENTS"
    }
  }
}
```

**`$ARGUMENTS` 替换**:用户在 `/feat 之后输入的所有内容`。

**`default_agent: "build"`** —— 显式声明主代理。

## 完整示例

完整可跑配置在仓库根的 `examples/02-add-feature/`(见其 `README.md`):

```bash
cd examples/02-add-feature
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `feature-builder` 的 prompt 要明确"不要扩大范围、不要 refactor 不相关代码",否则 subagent 容易越权。
- command 文件形式未在本场景演示,见未来 `concepts/commands.md`。
- 想让多个 subagent 协作(plan → feature-builder → tester),可以放开 `task: allow`,但要小心 `subagent_depth`。
```

- [ ] **Step 5: 验证**

```bash
python -c "import json; json.load(open('examples/02-add-feature/.opencode/opencode.json'))"
mkdocs build --strict
```

期望:均无错。

- [ ] **Step 6: 提交**

```bash
git add examples/02-add-feature/ docs/scenarios/02-add-feature.md
git commit -m "docs(scenarios): add 02-add-feature with feature-builder + /feat command"
```

---

## Task 5: Scenario 03 — 重构

**Files:**
- Create: `docs/scenarios/03-refactor.md`
- Create: `examples/03-refactor/README.md`
- Create: `examples/03-refactor/.opencode/opencode.json`
- Create: `examples/03-refactor/.opencode/agent/refactor-planner.md`

**Interfaces:**
- Consumes: T1、T2、T3、T4 产出
- Produces: 场景 03 的文档 + 可运行子示例(展示 plan 模式 + disable 内置代理 + 文件权限边界)

- [ ] **Step 1: 写 `examples/03-refactor/.opencode/agent/refactor-planner.md`**

```markdown
---
description: Refactor planner - reads code, proposes a step-by-step refactor plan, never edits
mode: subagent
temperature: 0.3
permission:
  read: allow
  grep: allow
  glob: allow
  edit: deny
  bash: deny
  task: deny
---

You are the Refactor Planner. You receive a code smell or refactor goal.
You read the relevant code and produce a numbered step-by-step plan:
which files change, in what order, what risks, what tests to add before/after.
You never edit files. You only plan. If you need to run something, say so in
the plan and stop.
```

- [ ] **Step 2: 写 `examples/03-refactor/.opencode/opencode.json`**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "default_agent": "plan",
  "agent": {
    "plan": {
      "permission": {
        "edit": "deny",
        "bash": "ask"
      }
    },
    "build": {
      "disable": true
    },
    "refactor-planner": {
      "mode": "subagent",
      "temperature": 0.3,
      "permission": {
        "read": "allow",
        "grep": "allow",
        "glob": "allow",
        "edit": "deny",
        "bash": "deny",
        "task": "deny"
      }
    }
  }
}
```

> prompt 来自 `.opencode/agent/refactor-planner.md` 的 body。

- [ ] **Step 3: 写 `examples/03-refactor/README.md`**

```markdown
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
```

- [ ] **Step 4: 写 `docs/scenarios/03-refactor.md`**

> 执行前先跑 `opencode --version`,替换下面 `0.x.x`。

```markdown
---
title: 03 — 重构
scenario_id: 03-refactor
status: working
opencode_version: <填 opencode --version 输出>
related_example: ../../examples/03-refactor/
---

# 重构

## 目标

把"重构"强制切成两阶段:**先规划、再执行**,并通过权限边界确保 plan 阶段不能动文件。

## 用到的能力

- agents: `plan`(内置,默认主代理)、`refactor-planner`(自定义 subagent,只读)、`build` 被禁用
- commands: 无
- skills: 无
- MCP: 无
- permissions: 详见下文,核心是 `edit: deny` + `bash: ask/deny`

## 工作流

1. `default_agent: "plan"` —— 主代理默认进入只读思考模式
2. 用户提重构需求(例:"拆 src/legacy/")
3. 主代理委派 `refactor-planner` 读代码,产出编号方案
4. 主代理读方案,确认风险与步骤后**返回给用户**
5. 用户手动确认后,切到 `build` agent(或新建 session 用默认 `build`)执行

## 配置要点

**禁用 build 代理**:

```json
{
  "agent": {
    "build": { "disable": true }
  }
}
```

**`plan` 代理默认禁止 edit**,但可显式覆盖:

```json
{
  "agent": {
    "plan": {
      "permission": { "bash": "ask" }
    }
  }
}
```

**`refactor-planner` 双重 deny**:

```json
{
  "permission": {
    "edit": "deny",
    "bash": "deny"
  }
}
```

不依赖"主代理不去调用",而是 subagent 自己就没法改。

## 完整示例

完整可跑配置在仓库根的 `examples/03-refactor/`(见其 `README.md`):

```bash
cd examples/03-refactor
opencode
```

!!! note "为什么这里不是超链接"
    `examples/` 在 `docs/` 之外,不参与 MkDocs 构建,写成相对链接会让
    `mkdocs build --strict` 报 "target not found"。路径同时记在 frontmatter
    `related_example`,在 GitHub 上按路径点进去即可。

## 踩坑与备注

- `disable: true` 是顶层禁用,不是隐藏 —— 用户仍能 `@build` 选到(直到 opencode 完全移除该入口)。要做"硬墙"还是靠 permission。
- 双层 deny(主代理 + subagent)更稳:即使 plan 模式被绕过,subagent 自己也没法动。
- 想自动化"plan → 用户确认 → build"流程,可以加一个 `/refactor` command 把两阶段串起来。
```

- [ ] **Step 5: 验证**

```bash
python -c "import json; json.load(open('examples/03-refactor/.opencode/opencode.json'))"
mkdocs build --strict
```

- [ ] **Step 6: 提交**

```bash
git add examples/03-refactor/ docs/scenarios/03-refactor.md
git commit -m "docs(scenarios): add 03-refactor with plan mode + refactor-planner"
```

---

## Task 6: Scenario 04 — PR review

**Files:**
- Create: `docs/scenarios/04-pr-review.md`
- Create: `examples/04-pr-review/README.md`
- Create: `examples/04-pr-review/.opencode/opencode.json`
- Create: `examples/04-pr-review/.opencode/agent/reviewer.md`

**Interfaces:**
- Consumes: T1–T5 产出
- Produces: 场景 04 的文档 + 可运行子示例(展示读权限为主 + 谨慎 bash + hidden subagent)

- [ ] **Step 1: 写 `examples/04-pr-review/.opencode/agent/reviewer.md`**

```markdown
---
description: PR reviewer - reads diff, checks style/correctness/security, reports findings
mode: subagent
hidden: true
temperature: 0.2
permission:
  read: allow
  grep: allow
  glob: allow
  edit: deny
  bash: ask
  webfetch: allow
  task: deny
---

You are the PR Reviewer. Given a PR diff (or file list + diff), you:
- spot bugs and logic errors
- call out style/idiom violations
- flag security/correctness concerns (input validation, error handling, race conditions)
- suggest concrete fixes (patches, not vague advice)

You do NOT edit files. You output a numbered review with file:line references.
Use `bash` only to run the project's test suite if asked; otherwise stay read-only.
```

- [ ] **Step 2: 写 `examples/04-pr-review/.opencode/opencode.json`**

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "reviewer": {
      "mode": "subagent",
      "hidden": true,
      "temperature": 0.2,
      "permission": {
        "read": "allow",
        "grep": "allow",
        "glob": "allow",
        "edit": "deny",
        "bash": "ask",
        "webfetch": "allow",
        "task": "deny"
      }
    }
  }
}
```

> prompt 来自 `.opencode/agent/reviewer.md` 的 body。

- [ ] **Step 3: 写 `examples/04-pr-review/README.md`**

```markdown
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
```

- [ ] **Step 4: 写 `docs/scenarios/04-pr-review.md`**

> 执行前先跑 `opencode --version`,替换下面 `0.x.x`。

```markdown
---
title: 04 — PR review
scenario_id: 04-pr-review
status: working
opencode_version: <填 opencode --version 输出>
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
```

- [ ] **Step 5: 验证**

```bash
python -c "import json; json.load(open('examples/04-pr-review/.opencode/opencode.json'))"
mkdocs build --strict
```

- [ ] **Step 6: 提交**

```bash
git add examples/04-pr-review/ docs/scenarios/04-pr-review.md
git commit -m "docs(scenarios): add 04-pr-review with hidden reviewer subagent"
```

---

## Task 7: Experiment — permission patterns

**Files:**
- Create: `docs/experiments/permission-patterns.md`
- Modify: `mkdocs.yml`(nav 加 Experiments)

**Interfaces:**
- Consumes: T2 的 `concepts/permissions.md`(会链向它)
- Produces: 一篇对比多种 permission 写法的实验记录

- [ ] **Step 1: 写 `docs/experiments/permission-patterns.md`**

```markdown
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
- **结论**:个人 demo 之外不要用。

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

## 我的偏好(更新于 <填今天日期 YYYY-MM-DD>)

场景 01/02 用 B;场景 03/04 用 C;复杂编排才用 D。

(每次升级后回来看,如有调整改这里。)
```

- [ ] **Step 2: 更新 `mkdocs.yml` nav**

把 `nav` 改成:

```yaml
nav:
  - 首页: index.md
  - Concepts:
      - agents: concepts/agents.md
      - permissions: concepts/permissions.md
      - subagent vs primary: concepts/subagent-vs-primary.md
  - Experiments:
      - permission patterns: experiments/permission-patterns.md
```

- [ ] **Step 3: 验证**

```bash
mkdocs build --strict
```

- [ ] **Step 4: 提交**

```bash
git add docs/experiments/ mkdocs.yml
git commit -m "docs(experiments): add permission-patterns comparison"
```

---

## Task 8: Final wiring — full nav, full index, README TOC

**Files:**
- Modify: `mkdocs.yml`(nav 完整化,加所有 scenarios)
- Modify: `docs/index.md`(完整目录)
- Modify: `README.md`(完整 TOC)

**Interfaces:**
- Consumes: T1–T7 产出
- Produces: 完整可导航的 wiki,`mkdocs build --strict` 干净通过

- [ ] **Step 1: 更新 `mkdocs.yml` nav**

把 `nav` 改成:

```yaml
nav:
  - 首页: index.md
  - Scenarios:
      - 01 — 跑测试 + 找 bug: scenarios/01-debug-bug.md
      - 02 — 加新功能: scenarios/02-add-feature.md
      - 03 — 重构: scenarios/03-refactor.md
      - 04 — PR review: scenarios/04-pr-review.md
  - Concepts:
      - agents: concepts/agents.md
      - permissions: concepts/permissions.md
      - subagent vs primary: concepts/subagent-vs-primary.md
  - Experiments:
      - permission patterns: experiments/permission-patterns.md
```

- [ ] **Step 2: 写 `docs/index.md`(完整版)**

```markdown
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
```

- [ ] **Step 3: 更新 `README.md`(完整版)**

```markdown
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
```

- [ ] **Step 4: 最终验证**

```bash
mkdocs build --strict
```

期望:
- 无 WARNING、无 ERROR
- `site/index.html` 存在
- 所有 nav 项可点击,无 404
- 内部链接(`scenarios/...`、`concepts/...`、`experiments/...` 之间的)全部解析
- `examples/**` 在 `docs_dir` 外,**不**用 MkDocs 链接;走 frontmatter `related_example` + 代码块/admonition(由 T3 起的统一模式)
- `site/` 下确认无 `examples/` 与 `superpowers/` 残留(可在 build 后 `Get-ChildItem site -Recurse` 抽查)

- [ ] **Step 5: 全量 commit**

```bash
git status --short
git add mkdocs.yml docs/index.md README.md
git diff --cached --stat
git commit -m "docs: complete wiki nav, index, and README TOC"
```

期望:`git diff --cached --stat` 显示 3 个文件改动,无意外文件。

- [ ] **Step 6: 推送(可选,等用户确认)**

**不要自动 push**。完成后问用户是否 `git push -u origin main`。