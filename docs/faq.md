# 常见问题 FAQ

---

## Q: 我的项目有几千个文件，会把 AI 的 Token 耗尽吗？

不会，前提是 AI 遵循 wiki_scan 的分批策略。

wiki_scan 在 Phase 0 输出扫描计划时，会按规模分级，每批处理一个模块。
AI 不会一次性读所有文件。对于超大型项目（500+ 文件），请只扫描核心路径，其余模块按需补扫。

---

## Q: 扫描中断了，可以继续吗？

可以。wiki_scan 使用 `wiki/_progress.json` 记录断点。

下次会话时，告诉 AI：
```
请读取 wiki/_progress.json，从中断处继续执行 wiki_scan。
```

AI 会自动识别已完成的阶段，只执行剩余部分。

---

## Q: 生成的文档有错误怎么办？

直接修改对应 Markdown 文件即可，在修改处标注 `[已人工修正]`。

对于有把握的修正，**不需要**重新运行 wiki_scan。

对于大范围错误（比如 AI 误解了系统架构），重跑 Phase 1 即可：
```
请重跑 wiki_scan 的 Phase 1，重新生成 L1_architecture.md 和 L1_user_flows.md。
```

---

## Q: 如何处理 BUSINESS_QA.md？

直接在文件里的 `> 答：` 后填写。然后告诉 AI：
```
BUSINESS_QA.md 中的 Q4 和 Q6 我已经填写，请将答案归入知识库。
```

AI 会将答案回写到对应的 L1/L2 文档中，并从 L3_unknown.md 中移除对应的未知项（如已解锁）。

---

## Q: 我需要支持一个新的技术栈（如 Laravel）怎么办？

复制 `adapters/universal.md`，按照 Laravel 的框架惯例（路由文件位置、Model 位置、ORM配置方式）定制 Phase 0-4 的扫描命令。

欢迎通过 PR 提交新的适配器。

---

## Q: wiki_scan 和 README 生成工具有什么区别？

README 生成器的目标是"帮人了解项目"。
wiki_scan 的目标是"帮 AI 在项目里安全开发"。

核心区别：
- wiki_scan 生成的 `L3_unknown.md` 是 README 生成器完全不会有的——它是给 AI 的"禁区地图"
- wiki_scan 生成的 `_index.json` 是路由控制器，让 AI 按任务精准加载，不是给人翻阅的

---

## Q: 可以用于闭源商业项目吗？

可以。wiki_scan 使用 MIT 协议，允许商用。
生成的 `wiki/` 目录内容属于你的项目，与 wiki_scan 的开源协议无关。
