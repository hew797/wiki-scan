# Windsurf 适配指南

> Windsurf (Cascade) 具备完整的文件读写和终端能力，wiki_scan 可**全自动运行**。

---

## 激活方式

在 Windsurf Cascade 面板中直接输入：

```
请阅读 [wiki_scan.md 的完整路径] 并按照其中的执行流程，
对以下项目生成知识库：[你的项目路径]

工具映射：
- [LIST] → list_directory 或直接分析项目结构
- [READ] → read_file
- [SHELL] → run_command
- [WRITE] → write_file 或 create_file
```

---

## Windsurf 工具映射详情

| wiki_scan 指令 | Windsurf Cascade 实际调用 |
|---|---|
| `[LIST] 目录` | `list_directory(path)` |
| `[READ] 文件路径` | `read_file(path)` |
| `[SHELL] 命令` | `run_command(cmd)` |
| `[WRITE] 路径 : 内容` | `write_file(path, content)` |

---

## Windsurf Rules 配置（持久生效）

在项目根目录创建 `.windsurfrules` 文件（或在 Cascade 设置中添加 Global Rule）：

```
当用户要求"扫描项目知识库"时，读取并执行 wiki_scan/wiki_scan.md 中的完整流程。

工具映射规则：
- [LIST] = list_directory
- [READ] = read_file
- [SHELL] = run_command
- [WRITE] = write_file

严格遵守 wiki_scan 中的所有阶段约束、禁止项和安全红线。
```

---

## 注意事项

- Windsurf Cascade 支持多步骤 Agent 执行，适合 wiki_scan 的分批扫描模式
- 建议在项目根目录打开 Windsurf，确保相对路径正确
- 大型项目请按 Phase 分批，避免单次请求超出上下文限制
