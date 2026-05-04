# L3 未知逻辑库与禁止修改声明 🔒

> **AI 行为准则**：在遇到以下标记为 `[LOCKED]` 的对象时，绝对**禁止**在未明确业务含义的情况下擅自重构或修改内部逻辑！修改这些部分极易引发线上生产事故。

## 1. 疑似测试用途的高危系统探针
- **位置**: `org.springframework.samples.petclinic.system.CrashController` 中的 `triggerException()`
- **当前行为**: 直接抛出一个 `RuntimeException`("Expected: controller used to showcase what happens when an exception is thrown")。
- **未知风险**: `wiki_scan` 推测这是一个故意引发异常的测试路由，或者用作展示全局 `@ControllerAdvice` 异常拦截器的行为展示。在向外部暴露系统之前，应确认此接口是否需要在生产环境中进行 IP 白名单控制或删除。
- 🔒 **状态**: `[LOCKED] 待确认后解锁`

## 2. DDL 初始化的执行时机黑盒
- **位置**: `src/main/resources/application.properties` 中定义的 `spring.sql.init.data-locations=classpath*:db/${database}/data.sql`
- **风险**: 系统完全依赖 SQL 脚本预埋数据，并且启动自动重置。若未来对接外部生产持久化 DB（如正式 MySQL），需要明确知道 `data.sql` 不会对生产数据进行不可逆的 TRUNCATE 或覆盖！
- 🔒 **状态**: `[LOCKED]` 除非收到变更数据库的明确指令，不要修改这里。
