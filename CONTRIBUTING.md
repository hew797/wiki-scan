# Contributor's Guide

感谢你考虑为 wiki_scan 做贡献！

---

## 贡献方式

### 🐛 报告 Bug

提 Issue 时请包含：
1. 你使用的 AI 工具（Cursor / Gemini CLI / Claude / ...）
2. 项目技术栈（Java Spring / Python Django / ...）
3. 触发问题的操作步骤
4. 期望行为 vs 实际行为

### 💡 新增技术栈适配器

1. 复制 `adapters/universal.md` 作为起点
2. 按照目标技术栈的框架惯例定制 Phase 0-4 的扫描命令
3. 在 `README.md` 的支持表格中添加对应行
4. 提 PR，附上在真实项目上的测试截图

### 🛠️ 新增 AI 工具适配

1. 复制 `adapters/tools/cursor.md` 作为参考
2. 填写该工具的工具映射表和激活方式
3. 在 `adapters/tools/README.md` 和主 `README.md` 中添加该工具
4. 提 PR

---

## 代码规范

- 所有文档使用 Markdown 格式
- 文件名全小写加下划线（snake_case）
- 不包含任何私有项目信息（业务名称、表名、组件名）
- 示例代码中的项目路径使用 `[你的项目路径]` 占位

---

## 版本规范

遵循 [Semantic Versioning](https://semver.org/)：

- `PATCH`（x.x.1）：文档修正、错别字、命令修复
- `MINOR`（x.1.0）：新增适配器、新增工具支持
- `MAJOR`（1.0.0）：主 Skill 协议变更、输出格式变更
