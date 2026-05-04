# L2 API 路由档案: Owner & Pets

## 核心接口列表归档

| URL 路径 | METHOD | 作用描述 | 对应 Controller | 当前代码状态 |
|---|---|---|---|---|
| `/owners/new` | GET | 展示“新增主任”表单视图 | OwnerController.initCreationForm | ✅ 清晰规范 |
| `/owners/new` | POST | 提交保存新实体，重定向到详情页 | OwnerController.processCreationForm | ✅ 依赖 `@Valid` 且自带防越权 |
| `/owners/find` | GET | 搜索表单入口 | OwnerController.initFindForm | ✅ 清晰 |
| `/owners` | GET | 执行模糊搜索。若只查到 1 人直接跳转；查到多人返回列表。 | OwnerController.processFindForm | ✅ 清晰规范 |
| `/owners/{ownerId}` | GET | 展示主人面板（顺带拉出他关联的所有宠物） | OwnerController.showOwner | ✅ 清晰 |
| `/owners/{ownerId}/pets/new` | GET/POST | 在此主人名下新增宠物 | PetController.initCreationForm | ✅ 清晰规范 |

## 发现的“待确认”业务规则 🤖❓
- 🤖 [依赖校验]: 当保存一个新建的 Owner 时，代码没有任何层面的“手机号唯一性校验”或者“重复人员去重机制”。由于缺乏 Service 层，如果这在业务中是必须的，可能会引发脏数据。
  - **建议动作**: 在接下来的业务 QA 中询问人类项目负责人。

## 调用关系防腐
**警告**：修改此处 Controller 的逻辑时，注意不要破坏与 `/vets` 或底层的强耦合。目前未发现横向跨领域调用。
