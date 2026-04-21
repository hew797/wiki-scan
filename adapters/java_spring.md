# 适配器：Java + Spring Boot

> 当 Phase 0 侦测到 `pom.xml` 时自动加载此适配器。
> 补充主 Skill `wiki_scan.md` 中各阶段的技术栈专属扫描命令。

---

## Phase 0 补充：技术栈信号读取

```bash
# 提取技术栈版本
grep -E "groupId|artifactId|version" pom.xml | head -20

# 统计规模
find src/main/java -name "*.java" | wc -l
find src/main/resources -name "*.vue" | wc -l   # 如含前端

# 识别 Controller
grep -rl "@RestController" src/main/java --include="*.java" | sed 's/.*\///;s/\.java//' | sort

# 识别 Entity
grep -rl "@TableName\|@Table\|@Entity" src/main/java --include="*.java" | sed 's/.*\///;s/\.java//'
```

**规模分级**：

| Java 文件数 | 级别 | 扫描策略 |
|---|---|---|
| < 50 | 🟢 小型 | 全量扫描，单批完成 |
| 50-200 | 🟡 中型 | 按模块分批，4-6批 |
| 200-500 | 🟠 大型 | 按业务域分批，8-12批 |
| > 500 | 🔴 超大型 | 仅扫核心路径，明确告知用户范围 |

---

## Phase 0 补充：Spring Boot 特有约束探测

```bash
# 鉴权方式
grep -rn "@PreAuthorize\|SecurityContext\|@Login\|getUser\|getCurrentUser" src/main/java --include="*.java" | head -5

# ORM 方式（决定 Phase 3 的扫描策略）
grep -rn "JdbcTemplate\|@Mapper\|BaseMapper\|JpaRepository\|selectSql\|insertSql" src/main/java --include="*.java" | head -5

# 表名常量
grep -rn "TBL_\|TABLE_NAME\|\"as_\|\"tb_\|\"t_" src/main/java --include="*.java" | grep "static final" | head -20
```

**ORM 模式识别结果**：

| 识别到 | 推断 ORM | Phase 3 策略 |
|---|---|---|
| 自定义 `selectSql` / `insertSql` 等方法 | 项目自研 DAO 工具 | 搜索表名常量（TBL_/TABLE_）+ SQL 字符串 |
| `@Mapper` / `BaseMapper` | MyBatis | 扫描 Mapper 接口 + XML 映射文件 |
| `JpaRepository` | Spring JPA | 扫描 Repository 接口 + @Entity 类 |
| `JdbcTemplate` | 原生 JDBC | 搜索 SQL 字符串 |

---

## Phase 2 补充：API 提取命令

```bash
# 获取所有 Controller 的路由方法列表
grep -n "@GetMapping\|@PostMapping\|@RequestMapping\|@PutMapping\|@DeleteMapping" \
  src/main/java/path/to/YourController.java

# 快速生成接口名单（每个 Controller 分别执行）
grep -n "public Map\|public List\|public Object\|public String\|public void" \
  src/main/java/path/to/YourController.java | grep -v "//"
```

**文档模板（每个 Controller 对应一个 L2_api 文件）**：

```markdown
# [业务域]API — [ControllerName]

**路由前缀**：`/your/prefix`

## 操作清单（代码确认）
| 操作 | 方法 | 路径 | 鉴权 |
...

## 业务规则清单（推断 · 待确认）
| 规则 | 代码证据 | 置信度 | 状态 |
...

## 模块依赖预检（v4）
| 上游依赖 | 下游波及 | 并行风险 | 结论 |
...
```

---

## Phase 3 补充：数据模型提取命令

```bash
# 搜索所有表名常量（适配常见命名风格）
grep -rn '"as_\|"tb_\|"t_\|TBL_\|TABLE_NAME' src/main/java --include="*.java" \
  | grep -v 'import\|//\|logger\|log\.' | grep '"[a-z]' | head -30

# MyBatis 项目：所有 Mapper 接口
find src/main/java -name "*Mapper.java" | sort

# JPA 项目：所有 Entity 类
grep -rl "@Entity\|@Table" src/main/java --include="*.java"

# JSON Blob 字段追踪（最重要！）
grep -rn "\.get(\"" src/main/java --include="*.java" \
  | grep -v "import\|//" | grep "Map\|JSONObject" | head -30
```

**JSON Blob 高危触发条件**（触发后写入 L3_unknown.md）：

- String 类型字段名含 `config`、`analysis`、`data`、`json`、`blob`、`meta`
- 字段被 `JSON.parse()` / `ObjectMapper.readValue()` / `JSONObject.fromObject()` 处理

---

## Phase 4 补充：前端（Vue 2.x ES5）提取命令

```bash
# 按文件大小排序，优先扫最大的
find src/main/resources/vue -name "*.vue" -exec wc -l {} + | sort -rn | head -15

# 提取组件的 Props 和关键 API 调用
grep -n "props:\|fetch\|postData\|JsUlits\|\$emit\|module.exports" \
  src/main/resources/vue/path/to/Component.vue | head -30
```

**ES5 合规检查**（每个组件必做）：

```bash
grep -n "const \|let \|=>\|async \|await \|import \|export default" \
  src/main/resources/vue/path/to/Component.vue
# 有结果 = 违规，需要标注到 L3_constraints.md
```

**Vue 2.x 动态组件特殊约定**（如项目使用服务端动态加载组件模式）：
- 某些老项目通过 `fetch('/path/to/Component.vue')` 拉取 .vue 源码字符串，在运行时解析并渲染
- 此模式下任何 ES6+ 语法（const/let/箭头函数等）都会导致**运行时白屏且无编译期报错**
- 扫描时需要重点检查此约束，并写入 `L3_constraints.md`

---

## Phase 0 输出模板（Java/Spring 专用）

```markdown
## 📊 项目侦测报告 — [项目名]

| 项目 | 值 |
|---|---|
| 技术栈 | Java [版本] + Spring Boot [版本] |
| ORM | [自研DAO工具 / MyBatis / JPA / JDBC] |
| 鉴权 | [Apache Shiro / Spring Security / JWT / 无] |
| Java 文件数 | [N] 个 → 规模：[小/中/大/超大] |
| Vue 组件数 | [N] 个（如有） |
| Controller 数 | [N] 个 |
| Entity/Table 数 | [N] 个 |

### 识别到的特殊约束
- [约束1]
- [约束2]

### 扫描计划
- 批次1：P1（技术画像+架构）预计 [N] 分钟
- 批次2：P2（[模块名]API）预计 [N] 分钟
...

⏸️ 确认后开始执行？
```
