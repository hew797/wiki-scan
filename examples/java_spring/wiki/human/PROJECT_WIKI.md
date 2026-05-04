# 🐶 Spring PetClinic (样例分析输出)

> 💡 **这是 `wiki_scan` AI Agent 生成的“人读版本”** 

## 1. 系统这是干什么用的？
这是一个基于 Spring Boot 开源的“宠物医院管理系统”标杆项目。用于管理宠物主人（Owner）、宠物（Pet）、兽医（Vet）及其看诊记录（Visit）。

## 2. 存在哪些业务流程模块？
| 模块名称 | 功能描述 | 操作页面位置 |
|---|---|---|
| **兽医中心** / Vets | 展示医院当前所有的兽医名单及其专长 | `/vets.html` (Thymeleaf) |
| **客户中心** / Owner | 登记新主人，按姓氏搜索主人，为主人名下挂靠宠物 | `/owners/find` |
| **诊疗模块** / Visit | 为具体的宠物新增疾病诊断记录 | `/owners/{id}/pets/{petId}/visits/new` |

## 3. 开发快速上手极简指南
如果你要在 10 分钟内增加一个新的表和页面，请按以下步骤操作：

1. **查表约束**：所有表结构在 `src/main/resources/db/mysql/schema.sql` (开发时默认用 h2)。不要用 JPA 的 `ddl-auto`。
2. **控制器逻辑**：参考 `OwnerController.java`，直接依赖注入 `XxxRepository`，不要再创建 `XxxService` 类。
3. **注意越权漏洞**：写表单提交接口时，切记在 Controller 加入：
   ```java
   @InitBinder
   public void setAllowedFields(WebDataBinder dataBinder) {
       dataBinder.setDisallowedFields("id");
   }
   ```
4. **寻找页面模板**：去 `src/main/resources/templates/` 里直接修改 Thymeleaf，页面会自动刷新。
