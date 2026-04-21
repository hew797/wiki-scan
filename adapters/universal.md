# 适配器：通用技术栈

> 当 Phase 0 未匹配到专属适配器时使用。
> 适用：Python、Node.js、Ruby、Go、任意其他技术栈。

---

## Phase 0：通用技术栈识别

```bash
# 识别项目结构
list_dir [项目根目录]

# 统计核心代码文件数
find . -name "*.py" -not -path "*/node_modules/*" -not -path "*/.git/*" | wc -l  # Python
find . -name "*.js" -not -path "*/node_modules/*" -not -path "*/.git/*" | wc -l  # JS
find . -name "*.go" -not -path "*/vendor/*" | wc -l                              # Go
find . -name "*.rb" | wc -l                                                       # Ruby

# 读取主配置文件（取第一个存在的）
# Python: requirements.txt / pyproject.toml / setup.py
# Node.js: package.json
# Go: go.mod
# Ruby: Gemfile
```

**通用规模分级**：

| 代码文件数 | 级别 | 建议 |
|---|---|---|
| < 30 | 🟢 小型 | 可全量扫描 |
| 30-100 | 🟡 中型 | 按模块分 3-5 批 |
| > 100 | 🟠+ 大型 | 只扫核心路径，其余按需 |

---

## Phase 2：通用 API / 路由提取策略

不同框架查找路由的方法：

```bash
# Python / Django
grep -rn "urlpatterns\|path(\|url(" --include="*.py" | grep -v "#" | head -30
grep -rn "def get\|def post\|def put\|def delete\|@api_view" --include="*.py" | head -30

# Python / FastAPI / Flask
grep -rn "@app.route\|@router\." --include="*.py" | head -30

# Node.js / Express
grep -rn "app.get\|app.post\|router.get\|router.post" --include="*.js" | grep -v "//\|node_modules" | head -30

# Go / Gin
grep -rn "\.GET\|\.POST\|\.PUT\|\.DELETE\|\.Handle" --include="*.go" | grep -v "//" | head -30

# Ruby / Rails
grep -rn "get\|post\|put\|delete\|resources\|namespace" config/routes.rb | head -30
```

---

## Phase 3：通用数据模型提取策略

```bash
# Python SQLAlchemy
grep -rl "db.Model\|Base\)" --include="*.py" | head -20

# Django ORM
grep -rl "models.Model" --include="*.py" | head -20

# Mongoose (Node.js)
grep -rl "new Schema\|mongoose.Schema" --include="*.js" | grep -v node_modules | head -20

# Go GORM
grep -rl "gorm.Model\|gorm:\"" --include="*.go" | head -20

# Prisma
find . -name "schema.prisma" 2>/dev/null
```

---

## 通用 L0_profile 模板

```markdown
# L0 技术画像 — [项目名]

## 运行时版本
| 技术 | 版本 | 来源 |
...

## 编码约束（从代码采样推断）
| 约束 | 说明 |
...

## 架构特殊约定（非显而易见）
| 约定 | 影响 |
...
```

---

## Phase 0 输出模板（通用）

```markdown
## 📊 项目侦测报告 — [项目名]

| 项目 | 值 |
|---|---|
| 技术栈 | [语言] [版本] + [框架] [版本] |
| 数据库 | [MySQL / PostgreSQL / MongoDB / SQLite] |
| 核心文件数 | [N] 个 → 规模：[小/中/大] |
| API 入口文件 | [识别到的路由文件列表] |

### 识别到的特殊约束
- [约束1]

### 扫描计划
...

⏸️ 确认后开始执行？
```
