# 工具适配器目录

> 每个文件对应一个 AI 工具平台。选择你使用的工具，按照对应文件的说明激活 wiki_scan。

---

## 支持的工具

| 工具 | 支持等级 | 说明文件 | Shell 执行 |
|---|---|---|---|
| **Gemini CLI** | ✅ 完整原生支持 | 无需额外配置，直接使用 | ✅ 全自动 |
| **Cursor** | ✅ 完整支持 | [cursor.md](cursor.md) | ✅ 全自动 |
| **Windsurf (Cascade)** | ✅ 完整支持 | [windsurf.md](windsurf.md) | ✅ 全自动 |
| **Claude Projects** | ✅ 半自动支持 | [claude.md](claude.md) | ⚠️ 手动执行命令 |
| **Continue.dev** | ✅ 完整支持 | [continue.md](continue.md) | ✅ 全自动 |
| **GPT-4 / ChatGPT** | 🔧 需要插件 | 见 claude.md 中的手动模式 | ⚠️ 需 Code Interpreter |

---

## 快速判断

```
你在用 VS Code / JetBrains 里嵌入的 AI？
  ├── Cursor IDE      → cursor.md
  ├── Windsurf IDE    → windsurf.md
  └── Continue.dev    → continue.md

你在用网页版 AI?
  ├── Claude.ai       → claude.md
  └── ChatGPT         → claude.md（手动模式同样适用）

你在用命令行？
  └── Gemini CLI      → 无需配置，直接执行
```
