# Cursor 适配指南

> Cursor 内置终端 + 文件操作能力，wiki_scan 可**全自动运行**。

---

## 激活方式 A：Rules 文件（推荐，持久生效）

在项目根目录创建 `.cursor/rules/wiki_scan.mdc`，写入以下内容：

```
---
description: 激活 wiki_scan 知识库扫描技能
globs:
alwaysApply: false
---

@wiki_scan/wiki_scan.md 中的说明执行知识库扫描。

工具映射（Cursor 专用）：
- [LIST] → 使用 Composer 描述目录结构
- [READ] → 使用 @文件名 引用文件
- [SHELL] → 在 Composer 的终端中执行命令
- [WRITE] → 直接在 Composer 中创建或编辑文件

执行时始终遵循 wiki_scan.md 中的所有阶段约束和禁止项。
```

激活后在 Cursor Composer 输入：
```
请帮我扫描这个项目并生成知识库，项目路径是 [你的项目根目录]
```

---

## 激活方式 B：直接在 Composer 使用（一次性）

打开 Cursor Composer（`Cmd/Ctrl + I`），直接输入：

```
请阅读 [wiki_scan.md 的完整路径] 并按照其中的执行流程，
对以下项目生成知识库：[你的项目路径]
```

---

## Cursor 工具映射详情

| wiki_scan 指令 | Cursor 实际操作 |
|---|---|
| `[LIST] 根目录` | Composer 自动分析项目结构 |
| `[READ] path/to/File.java` | `@File.java` 或直接读取 |
| `[SHELL] grep -rn ...` | 在终端标签页执行，粘贴结果给 Composer |
| `[WRITE] wiki/ai/L0.md` | Composer 直接创建文件 |

> 💡 建议开启 Cursor 的 **Agent 模式**（而非普通 Chat 模式），Agent 模式可以自动执行终端命令，无需手动切换。

---

## 注意事项

- Cursor 的 Context 窗口有限制，大型项目（300+ 文件）建议分批执行，每次只处理一个 Phase
- 如果 Composer 中断，用 `_progress.json` 告知 AI 从哪里继续：
  ```
  请读取 wiki/_progress.json，从中断处继续执行 wiki_scan
  ```
