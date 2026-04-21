# Continue.dev 适配指南

> Continue.dev 是开源的 IDE AI 扩展（支持 VS Code / JetBrains），支持全自动执行。

---

## 激活方式

### Step 1：配置 Custom Slash Command

在 Continue 配置文件（`~/.continue/config.json`）中添加：

```json
{
  "customCommands": [
    {
      "name": "wiki_scan",
      "prompt": "请阅读并执行以下路径的 wiki_scan 技能文件：{wiki_scan_path}/wiki_scan.md\n\n工具映射：\n- [LIST] = list_directory\n- [READ] = read_file\n- [SHELL] = run_terminal_command\n- [WRITE] = write_file\n\n项目路径：{current_dir}",
      "description": "扫描当前项目，生成 AI 知识库"
    }
  ]
}
```

### Step 2：在 Continue Chat 中执行

打开 Continue Chat 面板（`Cmd/Ctrl + L`），输入：
```
/wiki_scan
```

或直接输入：
```
请按照 wiki_scan.md 的流程扫描当前项目，从 Phase 0 开始。
```

---

## Continue 工具映射详情

| wiki_scan 指令 | Continue 实际工具 |
|---|---|
| `[LIST] 目录` | `@codebase` 或 `list_directory` |
| `[READ] 文件路径` | `@文件名` 或 `read_file` |
| `[SHELL] 命令` | `run_terminal_command` |
| `[WRITE] 路径 : 内容` | `write_file` 或直接编辑 |

---

## 注意事项

- Continue 支持多种 LLM 后端（OpenAI / Claude / Ollama / 本地模型），选择上下文窗口 ≥ 32K 的模型以支持大型项目分析
- 推荐配合 `@codebase` 语义搜索功能加速 Phase 0 侦测
- 使用本地模型时，wiki_scan 的分批策略尤为重要（本地模型 Token 限制更严格）
