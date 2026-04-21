# 示例输出说明

> 本目录包含 `wiki_scan v1.0.0` 对一个标准 Java/Spring Boot + Vue 2.x 项目的完整扫描输出样例。

## 对应样例项目特征

- 语言/框架：Java 8 + Spring Boot 2.3.x + Vue 2.x（ES5 强制）
- 项目性质：企业管理系统（HRD SaaS，评估与配置类模块为主）
- Java 文件：~100 个
- 前端组件：~100 个
- API Controller：~15 个
- 数据库表：~13 张
- 扫描耗时：约 25 分钟（5 个批次）

---

## 输出目录结构

```
java_spring/
└── wiki/
    ├── _progress.json           ← 断点记录（status: COMPLETE）
    ├── human/
    │   ├── PROJECT_WIKI.md      ← 综合手册（功能清单 + 架构图 + 快速上手）
    │   └── BUSINESS_QA.md       ← AI 自动生成的业务疑点问卷（~14 道）
    └── ai/
        ├── _index.json          ← 路由总控（~15 条路由规则）
        ├── L0_profile.md        ← 技术画像（编码约束 + 架构特殊约定）
        ├── L1_architecture.md   ← 系统架构（三层架构 + 数据流 + ER图）
        ├── L1_user_flows.md     ← 用户旅程图（前端状态机 + 初始化调用链）
        ├── L2_api/
        │   ├── _catalog.md      ← 全量 API 索引（按业务域分类）
        │   ├── CoreModuleA.md   ← 配置层接口（24个接口 + 双清单）
        │   ├── ApplicationAPI.md ← 应用层接口（评估执行 + 数据查询）
        │   └── SubsystemAPI.md  ← 子系统接口（AI训练 + 成长驱动）
        ├── L2_data/
        │   ├── _catalog.md      ← 全量表索引（~13张表 + ER关系图）
        │   └── CoreTables.md    ← 核心表字段详情 + JSON Blob 内部结构
        ├── L2_components/
        │   ├── _catalog.md      ← 全量组件索引（~100个，按层级分类）
        │   └── CoreComponents.md ← 核心组件 Props/Events/API调用链文档
        ├── L3_constraints.md    ← 红线规则 + 语法 Cheatsheet + 技术债清单
        └── L3_unknown.md        ← 禁止触碰声明（4 条）+ 安全风险（1 处）
```

---

## 典型产出亮点（说明 wiki_scan 生成了哪些"第一次被文档化"的内容）

### ① 核心 JSON Blob 内部结构首次文档化
某配置表有一个 `longtext` 类型的大 JSON 字段，存储了系统最核心的配置数据。
扫描前：所有人只知道"这个字段很重要，不敢动"。
扫描后：JSON 内部所有 Key 及其业务含义被完整逆向，写入 `L2_data/CoreTables.md`。

### ② 评分阈值规则首次被显式化
系统中存在三段评分线（淘汰线 / 待定线 / 通过线），这些数值硬编码在前端组件默认值中。
扫描后：这些阈值被提取出来，写入组件文档，避免后续开发者在不知情的情况下修改默认值。

### ③ 动态组件加载机制的约束被识别
前端通过 `fetch()` 动态加载 `.vue` 源码字符串并运行，导致任何 ES6+ 语法都会造成运行时白屏（无编译期报错）。
扫描后：这条隐形约束被写入 `L3_constraints.md`，并自动生成了 ES5 语法速查表。

### ④ API Key 硬编码安全风险自动识别
扫描发现一处硬编码在 Controller 里的第三方 AI 服务 API Key。
扫描后：自动写入 `L3_unknown.md`，标注为 ☢️ 安全风险，并给出迁移建议。

---

## 注意

本样例中的 API 文件名（`CoreModuleA.md` 等）为匿名化处理，实际使用时以真实业务域命名。
