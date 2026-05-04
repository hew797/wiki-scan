# Skill: wiki_scan — AI 驱动的遗留项目知识 OS

> **版本**：v1.0.0
> **作者**：wiki_scan contributors
> **输出目录**：`wiki/`（在项目根目录自动创建）
> **适配器**：见 `adapters/` 目录，按技术栈选择

---

## 🎯 使命

让 AI 在 5-30 分钟内，对任何遗留代码库生成一套**可持续维护的双轨知识库**：
- `wiki/human/` → 给人读（叙事型，Markdown）
- `wiki/ai/` → 给 AI 读（结构化，按任务精准路由）

---

## 🛠️ 工具兼容说明（执行前必读）

本 Skill 使用以下**抽象指令**，AI 在执行时需映射到自己平台的实际工具：

| 抽象指令 | 含义 | Gemini CLI | Cursor | Windsurf | Claude |
|---|---|---|---|---|---|
| `[LIST] 目录` | 列出目录结构 | `list_dir` | 直接描述目录 | `list_directory` | 描述目录结构 |
| `[READ] 文件路径` | 读取文件内容 | `view_file` | `@文件名` | `read_file` | 提供文件内容 |
| `[SHELL] 命令` | 执行 Shell 命令 | `run_command` | 终端执行 | `run_command` | ⚠️ 见下方说明 |
| `[WRITE] 路径 : 内容` | 写入文件 | `write_to_file` | 直接创建/编辑 | `write_file` | 生成内容后手动创建 |

> **Claude 用户注意**：Claude Projects / Claude.ai 无法直接执行 Shell 命令。
> 遇到 `[SHELL]` 指令时，Claude 会输出命令文本，**由用户在终端手动执行后把结果粘贴回来**。
> 推荐使用 `adapters/tools/claude.md` 中的"半自动模式"。

详细工具配置见 `adapters/tools/` 目录。

---

## 🏗️ 输出结构（固定契约）

```
wiki/
├── _progress.json                ← 断点续传控制器（格式见 schema/_progress.schema.json）
├── human/
│   ├── PROJECT_WIKI.md           ← 综合手册（功能清单 + 架构图 + 快速上手）
│   └── BUSINESS_QA.md            ← AI 自动生成的业务疑点问卷
└── ai/
    ├── _index.json               ← AI 路由总控（格式见 schema/_index.schema.json）
    ├── L0_profile.md             ← 第0层：技术画像（技术栈 + 编码约束 · AI每次必读）
    ├── L1_architecture.md        ← 第1层：系统架构（模块图 + 数据流向）
    ├── L1_user_flows.md          ← 第1层：用户旅程图（前端状态机 + API调用链）
    ├── L2_api/                   ← 第2层：接口文档
    │   ├── _catalog.md           ←   API 全量索引
    │   └── [ModuleName].md       ←   按业务域分文件
    ├── L2_data/                  ← 第2层：数据模型
    │   ├── _catalog.md           ←   表全量索引
    │   └── [EntityName].md       ←   逐表详情（含 JSON Blob 内部结构）
    ├── L2_components/            ← 第2层：UI组件（前端项目）
    │   ├── _catalog.md           ←   组件全量索引
    │   └── [ComponentName].md    ←   进组件详情（Props/Events/API调用）
    ├── L3_constraints.md         ← 第3层：红线规则 + 已知风险 + 技术债
    └── L3_unknown.md             ← 第3层：未知逻辑库（含禁止触碰声明）
```

---

## ⚡ 执行流程（5 个阶段）

### Phase 0：项目侦测（必须最先执行，禁止跳过）

```
输入：项目根目录路径
动作：
  1. [LIST] 根目录 → 识别技术栈信号（pom.xml/package.json/requirements.txt）
  2. [READ] 构建配置文件 → 提取版本信息
  3. [SHELL] find src -type f | wc -l → 统计规模，判断项目尺寸
  4. [READ] 随机 3-5 个核心文件 → 识别编码规范
  5. 根据技术栈 → 加载对应适配器

输出（输出到控制台，不写文件）：
  - 侦测报告（技术栈 / 规模 / 关键约束 / 已识别风险）
  - 定制扫描计划（分批次，每批预计 Token 消耗）

⏸️ 展示报告后暂停，等待用户确认后再执行后续阶段
```

**技术栈识别信号**：

| 文件 | 推断技术栈 | 适配器 |
|---|---|---|
| `pom.xml` | Java Maven | `adapters/java_spring.md` |
| `build.gradle` | Java Gradle | `adapters/java_spring.md` |
| `requirements.txt` / `pyproject.toml` | Python | `adapters/universal.md` |
| `package.json` | Node.js | `adapters/universal.md` |
| 其他 | 通用 | `adapters/universal.md` |

---

### Phase 1：技术画像 + 系统架构

```
适配器负责：具体的扫描命令和提取规则（见 adapters/）

通用要求：
  - 输出 wiki/ai/L0_profile.md（技术栈版本 + 强制编码约束，精简，<50行）
  - 输出 wiki/ai/L1_architecture.md（模块图 + 数据流 + 核心依赖关系，含 Mermaid）
  - 输出 wiki/ai/L1_user_flows.md（前端入口组件的视图状态机 + 初始化API调用链）
```

---

### Phase 2：API / 接口层提取

```
适配器负责：如何找到 Controller / Router / View，如何提取路由

通用要求（每个模块必须）：
  ① 模块依赖预检（进入前必做）：
     - 上游依赖：此模块数据从哪来？（查 FROM 子句/依赖注入）
     - 下游波及：修改此处会影响哪里？（被调用次数）
     - 并行风险：是否有其他模块正在梳理？

  ② 双清单输出：
     清单 A — 操作列表（代码可直接读出，打 ✅ 代码确认）
     清单 B — 业务规则（AI推断，打 🤖 推断 + ❓待确认）

  ③ 清单B每条「待确认」自动在 BUSINESS_QA.md 生成对应问题
```

---

### Phase 3：数据模型提取

```
适配器负责：如何找到实体类 / 数据库表定义

通用要求：
  - 所有表名 + 字段 + 类型 → wiki/ai/L2_data/_catalog.md
  - 每张核心表的完整字段 + JSON Blob 内部 Key 结构 → CoreTables.md
  - 发现 JSON Blob 字段且无注释 → 自动触发写入 L3_unknown.md
```

---

### Phase 4：前端组件提取（前端项目专属）

```
适配器负责：如何找到组件文件，如何提取 Props/Events/API调用

通用要求：
  - 按文件行数排序，优先扫前 10 大组件
  - 对每个组件：提取 Props / Events / mounted时的API调用链
  - 检查 ES5/ES6 语法合规（如适用）
```

---

### Phase 5：约束层 + 最终输出

```
输出 wiki/ai/L3_constraints.md：
  - 全量红线规则（从 Phase 0-4 发现的约束汇总）
  - 已知风险点表格
  - 技术债清单
  - 建议行动列表（按优先级）

输出 wiki/ai/L3_unknown.md：（触发条件见下）
  - 方法行数 > 150行 且无充分注释
  - 存在 TODO/FIXME/HACK 但未说明原因
  - JSON Blob 字段的 Key 被读取但无文档
  - 用户在 BUSINESS_QA 中回答「不清楚」
  每条必须包含：位置 / 行为描述 / 被调用次数 / 🔒禁止修改声明 / 解锁条件

输出 wiki/human/PROJECT_WIKI.md：
  - 系统定位（一句话）
  - 功能模块清单（操作层面）
  - 全系统数据流 Mermaid 图
  - 开发快速上手清单（改代码前5步）
  - 完整知识库目录树

更新 wiki/ai/_index.json 和 wiki/_progress.json（status: COMPLETE）
```

---

## 🛑 执行禁止项

| 禁止 | 原因 |
|---|---|
| 跳过 Phase 0 | 会产生错误的技术假设，后续全部作废 |
| 一次性全量读取所有源文件 | Token 爆炸，大项目直接失败 |
| **自行裁剪范围或仅提取 Top N 文件** | **生成的数据字典与 API 库必须是 100% 物理全量的。任何自作主张的截断都会导致知识库出现逻辑死角，危及自动化修改** |
| 在文档中写入 API Key / 密码 | 安全红线 |
| 覆盖已有 `[已确认✅]` 标记的内容 | 保护人工积累的业务知识 |
| 人看版和 AI 看版写完全相同内容 | 双轨的意义在于各司其职 |

---

## 🔄 后续维护

| 触发时机 | 只需重跑 |
|---|---|
| 新增 / 修改接口 | Phase 2 对应模块文件 |
| 新增 / 修改表字段 | Phase 3 对应实体文件 |
| 新增前端组件 | Phase 4 对应组件文件 |
| 发现新高危逻辑 | 手动追加到 L3_unknown.md |
| 业务 QA 填写完成 | 将答案回写到 L1_architecture / L2_api 对应位置 |

---

*wiki_scan v1.0.0 — 双轨分层。L0→L3 按需路由。适配器扩展任意技术栈。*
