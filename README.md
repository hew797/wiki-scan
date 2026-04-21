# wiki_scan — AI-Powered Knowledge OS for Legacy Codebases

[![Version](https://img.shields.io/badge/version-1.0.0-blue)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Language](https://img.shields.io/badge/language-Chinese%20%2F%20English-orange)]()

> **30 秒能用起来。适配任何 AI Agent（Gemini / Cursor / Claude / GPT-4）。**

---

## 它解决什么问题？

你接手一个老项目，没有文档，没有人讲解，要在一周内独立开发。

wiki_scan 让 AI 自动扫描源码，在 5-30 分钟内生成一套**双轨知识库**：
- **给人看**：功能清单、架构图、业务问卷（引导你填写 AI 读不出的业务逻辑）
- **给 AI 看**：结构化路由索引，AI 按任务类型精准加载，不浪费 Token

---

## 30 秒上手

**第一步**：把主 Skill 文件告诉你的 AI Agent

```
请阅读并执行以下 Skill 文件：[wiki_scan.md 的路径]
项目根目录：[你的项目路径]
```

**第二步**：等待 AI 输出"Phase 0 侦测报告"，确认后继续

**第三步**：5-30 分钟后（取决于项目规模），你会得到：

```
your-project/
└── wiki/
    ├── human/
    │   ├── PROJECT_WIKI.md    ← 给人读的综合手册
    │   └── BUSINESS_QA.md    ← AI 自动生成的业务问卷
    └── ai/
        ├── _index.json        ← AI 路由总控
        ├── L0_profile.md      ← 技术约束（AI每次必读）
        ├── L1_architecture.md ← 系统架构
        └── ...（按项目规模自动生成）
```

---

## 支持哪些项目？

| 技术栈 | 支持状态 | 适配器 |
|---|---|---|
| Java + Spring Boot | ✅ 完整支持 | [adapters/java_spring.md](adapters/java_spring.md) |
| 任意技术栈（通用） | ✅ 基础支持 | [adapters/universal.md](adapters/universal.md) |
| Python / Django | 🚧 实验性 | 计划中 |
| Node.js / Express | 🚧 实验性 | 计划中 |

---

## 核心设计：3 个不同于其他工具的地方

### ① 操作清单 vs 业务规则，严格区分
代码能告诉你"有一个保存接口"，但告诉不了你"保存时必须选择关键岗位"。
wiki_scan 会区分这两类知识，让 AI 推断的规则自动生成待确认的业务问卷。

### ② 未知逻辑必须声明"禁止触碰"
识别出高复杂度、无注释、业务意图不明的代码区域，生成 `L3_unknown.md`，
明确标注 🔒 禁止修改，直到业务意图被确认。防止 AI 在不知情时改出 Bug。

### ③ 模块迭代前必做依赖预检
梳理每个模块前自动检查"上游依赖 / 下游影响 / 并行风险"，
防止梳理完 A 模块后因 B 模块联动导致知识作废。

---

## 输出规范

所有输出均遵循以下 JSON Schema 约定：
- [`schema/_index.schema.json`](schema/_index.schema.json) — AI 路由总控格式
- [`schema/_progress.schema.json`](schema/_progress.schema.json) — 进度断点格式

---

## 样例输出

查看 [`examples/java_spring/`](examples/java_spring/) — 一个真实 Java/Spring 项目的完整扫描输出样例

---

## 兼容的 AI 工具

| AI 工具 | 支持等级 | Shell 执行 | 配置文件 |
|---|---|---|---|
| **Gemini CLI** | ✅ 完整原生 | 全自动 | 无需配置 |
| **Cursor** | ✅ 完整支持 | 全自动 | [adapters/tools/cursor.md](adapters/tools/cursor.md) |
| **Windsurf (Cascade)** | ✅ 完整支持 | 全自动 | [adapters/tools/windsurf.md](adapters/tools/windsurf.md) |
| **Continue.dev** | ✅ 完整支持 | 全自动 | [adapters/tools/continue.md](adapters/tools/continue.md) |
| **Claude Projects** | ✅ 半自动 | 手动粘贴 | [adapters/tools/claude.md](adapters/tools/claude.md) |
| **ChatGPT** | ⚠️ 手动模式 | 手动粘贴 | 同 claude.md |

> 不同工具的激活方式不同，详见 [`adapters/tools/`](adapters/tools/) 目录。

---

## 运行要求

| 能力 | 必须 | 说明 |
|---|---|---|
| 读取文件 | ✅ | `view_file` 或等价工具（各工具见 adapters/tools/）|
| 执行 Shell 命令 | ✅ | `run_command` 或等价工具（Claude 下可手动执行）|
| 写入文件 | ✅ | `write_to_file` 或等价工具 |
| 网络访问 | ❌ 不需要 | 纯本地离线扫描 |
| 特定框架 | ❌ 不需要 | 通过适配器支持任意技术栈 |

---

## 目录结构

```
wiki_scan/
├── README.md             ← 你正在读的这个文件
├── wiki_scan.md          ← 主 Skill（AI 执行入口）
├── CHANGELOG.md          ← 版本记录
├── LICENSE               ← MIT 开源协议
├── schema/
│   ├── _index.schema.json     ← AI路由总控 JSON Schema
│   └── _progress.schema.json  ← 进度断点 JSON Schema
├── adapters/
│   ├── java_spring.md         ← Java + Spring Boot 深度适配
│   └── universal.md           ← 通用技术栈适配器
├── examples/
│   └── java_spring/           ← 完整示例输出
│       ├── wiki/              ← 样例知识库输出
│       └── README.md
└── docs/
    ├── design.md              ← 设计理念与架构决策
    └── faq.md                 ← 常见问题
```

---

## 贡献 / 反馈

发现问题？有新的技术栈需要支持？欢迎提 Issue 或 PR。

---

*wiki_scan v1.0.0 — 让 AI 读懂你的老项目。*
