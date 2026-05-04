# Java / Spring Boot 样例: Spring PetClinic

本区示例展示了使用 `wiki_scan` 对经典的标杆级开源项目 **[Spring PetClinic](https://github.com/spring-projects/spring-petclinic)** 执行扫描后，AI 自动吐出的真实成果记录。

如果你熟悉这个“宠物医院”项目，你可以直接点开下方的生成结果，感受一下 AI 是如何**精准捕捉技术红线**并**提出深层次业务疑问**的：

- **防腐防瞎改**：看 [`wiki/ai/L0_profile.md`](wiki/ai/L0_profile.md) 是如何一眼识破项目故意没用 `@Service` 分层，并立下警告红线的。
- **高危探针拦截**：看 [`wiki/ai/L3_unknown.md`](wiki/ai/L3_unknown.md) 是如何敏锐锁定出那个负责抛异常的测试路线（CrashController），并且锁死禁止碰扯的。
- **业务反问（最核心！）**：看 [`wiki/human/BUSINESS_QA.md`](wiki/human/BUSINESS_QA.md) 里，AI 根本不满足于写点 CRUD 注释，而是反问人类：“如果前台录入了同手机号的 Owner 该怎么办？代码里没防范！”。

这就证明了，你的 AI 理清大盘之后，它完全具备了**防御能力**和**架构心智**。

> **如何阅读**：
>  - 人类来看：`wiki/human/` 下的文件
>  - 机器阅读：`wiki/ai/` 下的文件
