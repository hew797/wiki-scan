# Claude 适配指南（半自动模式）

> Claude Projects / Claude.ai **无法直接执行 Shell 命令**。
> wiki_scan 在 Claude 下采用"AI 生成命令 + 用户手动执行 + 粘贴结果"的**半自动模式**。
> 同样适用于 ChatGPT 等无 Shell 权限的 AI 工具。

---

## 激活方式

### Step 1：创建 Claude Project

在 Claude.ai 中创建一个新 Project，在 **Project Instructions** 中粘贴以下内容：

```
你是一个代码库知识扫描专家，执行 wiki_scan v1.0.0 技能。

核心行为规则：
1. 遇到 [SHELL] 指令时，输出命令文本并等待用户粘贴执行结果，不要假设结果
2. 遇到 [WRITE] 指令时，生成完整文件内容，告知用户文件路径，由用户创建
3. 每个 Phase 完成后主动暂停，确认用户已保存输出再继续
4. 严格遵守 wiki_scan 的所有禁止项（不写入密钥、不覆盖已确认内容等）
5. 分批处理，绝不尝试一次性读取整个项目

工具映射：
- [LIST] → 请用户执行 `find [目录] -type f -maxdepth 3 | head -50`，粘贴结果
- [READ] → 请用户提供文件内容（复制粘贴 或 cat 命令结果）
- [SHELL] → 输出命令，等待用户手动执行并粘贴结果
- [WRITE] → 生成文件内容，告知用户在 [路径] 创建此文件
```

### Step 2：上传 wiki_scan 核心文件到 Project

在 Project 文件中上传：
- `wiki_scan.md`
- 对应技术栈的适配器（如 `adapters/java_spring.md`）

### Step 3：开始扫描

在对话中输入：
```
我的项目是 [技术栈]，位于本地 [路径]。
请按照 wiki_scan.md 中的流程，从 Phase 0 开始扫描。
需要什么信息告诉我，我来提供。
```

---

## 半自动执行流程示例

**Claude 说**：
> 请在终端执行以下命令并把结果粘贴給我：
> ```bash
> find src/main/java -name "*.java" | wc -l
> ```

**你执行后粘贴**：
> 99

**Claude 继续**：
> 检测到 99 个 Java 文件，属于中型项目规模。开始 Phase 1...

---

## 提高效率的技巧

1. **一次性提供多个文件**：让 Claude 告诉你要读哪些文件，你批量 `cat` 并粘贴

   ```bash
   # Claude 说要读这几个文件，你一次性执行：
   cat src/main/java/com/xxx/controller/UserController.java \
       src/main/java/com/xxx/service/UserService.java
   ```

2. **预先提供目录树**：在开始时提供整个目录结构，节省大量来回

   ```bash
   find . -type f \( -name "*.java" -o -name "*.vue" -o -name "*.py" \) \
     -not -path "*/node_modules/*" -not -path "*/.git/*" | sort
   ```

3. **文件内容批量导入**：把需要扫描的文件用脚本一次性输出

   ```bash
   grep -rn "@RequestMapping\|@GetMapping\|@PostMapping" \
     src/main/java --include="*.java" > /tmp/api_scan.txt
   cat /tmp/api_scan.txt  # 粘贴这个给 Claude
   ```

---

## 适用场景

Claude 半自动模式特别适合：
- 对代码理解要求高的项目（Claude 语义理解能力强）
- 扫描结果需要反复确认和讨论的场景
- 无法安装 IDE 插件的环境（只有浏览器）
