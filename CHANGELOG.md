# CHANGELOG

所有显著变更将记录于此文件。
格式遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
版本遵循 [Semantic Versioning](https://semver.org/)。

---

## [1.0.0] — 2026-04-21

### 新增
- 主 Skill 文件 `wiki_scan.md`（精简版，约 200 行，框架层）
- Java/Spring Boot 专属适配器 `adapters/java_spring.md`
- 通用技术栈适配器 `adapters/universal.md`
- 输出格式 JSON Schema：`schema/_index.schema.json`、`schema/_progress.schema.json`
- 三大核心机制：
  - ① 操作/规则双清单（区分「代码可读出的操作」和「需人工确认的业务规则」）
  - ② 未知逻辑强制禁止触碰（`L3_unknown.md` + 🔒 标注体系）
  - ③ 模块迭代前必做依赖预检（上游依赖 / 下游波及 / 并行风险三问）
- MIT 开源协议

### 设计决策
- 双轨分层：`wiki/human/`（叙事型，人读）+ `wiki/ai/`（结构化，AI按需路由）
- 适配器模式：主 Skill 只定义通用协议，技术栈细节下沉到适配器
- 断点续传：`_progress.json` 支持跨会话断点重启

---

## 规划中

### [1.1.0] — 计划中
- Python/Django 完整适配器
- Node.js/Express 完整适配器
- 样例项目（Java/Spring）完整输出

### [1.2.0] — 计划中
- L4 知识确认层（BUSINESS_QA 回填→自动归档机制）
- L2.5 高危方法卡（方法级索引，解决大型项目粒度问题）
- `_dependency_map.json`（代码变更→wiki更新联动）

### [2.0.0] — 长期规划
- 多语言 README（英文版）
- VSCode Extension / IDE 插件
- CI/CD 集成（代码变更自动触发知识库增量更新）
