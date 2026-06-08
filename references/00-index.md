# 官网功能地图

本文件用于对齐 MyBatis-Plus 官网侧边栏功能，避免技能只覆盖少量常用 API。处理相关任务时，先按主题读取对应 references 文件，不要一次性加载全部文档。

## 指南

| 官网主题 | 本技能参考文件 | 处理提示 |
|----------|----------------|----------|
| 代码生成器 | `07-generator-advanced.md` | 优先确认项目已有模板、包名、命名规则和输出目录 |
| 代码生成器配置 | `07-generator-advanced.md` | 不覆盖现有实体、Mapper、XML，生成前确认可回滚 |
| 持久层接口 | `03-crud-service-mapper.md` | `Mapper extends BaseMapper<Entity>`，XML 只补复杂 SQL |
| 条件构造器 | `04-wrapper-query.md`、`05-wrapper-update.md` | 优先 lambda wrapper，动态列名必须白名单 |
| 流式查询 | `03-crud-service-mapper.md` | 大结果集避免一次性加载；确认连接、事务和资源关闭 |
| 批量操作 | `03-crud-service-mapper.md` | 关注批量大小、事务边界、唯一键冲突和失败回滚 |
| 自动映射枚举 | `09-enum-typehandler.md` | 使用 `@EnumValue` 或统一 TypeHandler |
| 自动填充字段 | `10-auto-fill.md` | `MetaObjectHandler` 只填审计字段，不替代业务校验 |
| 自动维护 DDL | `12-ddl-datasource-extension.md` | 属高风险结构变更，未明确批准不要执行 |
| 主键生成策略 | `02-annotations-entity.md`、`07-generator-advanced.md` | 明确 `IdType`、数据库自增、雪花 ID 或自定义生成器 |
| 自定义 ID 生成器 | `07-generator-advanced.md` | 确认分布式唯一性、时钟回拨和兼容旧数据 |
| 逻辑删除支持 | `02-annotations-entity.md`、`06-plugins-core.md` | 查询、唯一索引、恢复数据和物理删除要分清 |
| 多数据源支持 | `12-ddl-datasource-extension.md` | 优先复用项目现有动态数据源方案 |
| SQL 分析与打印 | `06-plugins-core.md` | 开发调试可用，生产输出需谨慎脱敏 |
| 数据安全保护 | `11-security-sql.md`、`06-plugins-core.md` | 关注 SQL 注入、全表更新删除、数据权限和租户隔离 |
| SQL 注入器 | `07-generator-advanced.md` | 扩展通用方法前确认全局影响和命名冲突 |
| 字段类型处理器 | `09-enum-typehandler.md` | JSON、枚举、加密字段优先统一 TypeHandler |
| 高级特性 | `06-plugins-core.md`、`07-generator-advanced.md` | 涉及插件链路时确认拦截器顺序 |
| Mybatis X 插件 | `07-generator-advanced.md` | 仅作为 IDE 辅助，不把插件生成结果当作无需审查的代码 |

## 插件

| 官网主题 | 本技能参考文件 | 处理提示 |
|----------|----------------|----------|
| 插件主体 | `06-plugins-core.md` | Spring Boot 中通常配置 `MybatisPlusInterceptor` |
| 分页插件 | `06-plugins-core.md` | 必须设置数据库类型或确认自动识别可靠 |
| 乐观锁插件 | `06-plugins-core.md` | 实体 `@Version`，更新失败要处理并发语义 |
| 多租户插件 | `06-plugins-core.md` | 租户条件必须来自可信上下文，不来自前端自由传入 |
| 数据权限插件 | `06-plugins-core.md` | 权限 SQL 由统一策略生成，不散落在 Controller |
| 动态表名插件 | `06-plugins-core.md` | 表名后缀必须受控，避免用户输入参与拼接 |
| 数据变动记录插件 | `06-plugins-core.md` | 关注审计字段、操作者、变更前后值和脱敏 |
| 非法 SQL 拦截插件 | `06-plugins-core.md` | 开启后要验证既有复杂 SQL 是否被误拦 |
| 防全表更新与删除插件 | `06-plugins-core.md` | 写操作必须保留主键、租户、业务范围等条件 |

## 参考

| 官网主题 | 本技能参考文件 | 处理提示 |
|----------|----------------|----------|
| 使用配置 | `01-dependency-configuration.md` | 对齐 Spring Boot、Mapper 扫描、全局配置 |
| 注解配置 | `02-annotations-entity.md` | 表、主键、字段、逻辑删除、乐观锁、填充 |
| 代码生成器配置 | `07-generator-advanced.md` | 生成前确认覆盖策略和包结构 |
| 预防安全漏洞 | `11-security-sql.md` | Wrapper 高危 API 必须校验用户输入 |
| 常见问题 | 对应主题 reference | 先确认项目版本与现有配置，再定位问题 |

## 扩展能力

| 官网/常见主题 | 本技能参考文件 | 处理提示 |
|---------------|----------------|----------|
| ActiveRecord | `08-ar-db-simplequery.md` | Entity 继承 `Model<T>`，不适合复杂业务流程绕过 Service |
| Db Kit | `08-ar-db-simplequery.md` | 静态工具适合脚本或简单查询，权限和事务要另行确认 |
| SimpleQuery | `08-ar-db-simplequery.md` | 适合字段投影、map/list 快速转换，避免替代复杂查询 |
| 枚举属性 | `09-enum-typehandler.md` | `@EnumValue` 与 Jackson 展示值分开 |
| JSON 字段 | `09-enum-typehandler.md` | `autoResultMap = true` 与 TypeHandler 配套 |
| 审计字段自动填充 | `10-auto-fill.md` | `update(Wrapper)` 不会自动填充实体字段 |

## 官方入口

- 介绍：https://baomidou.com/introduce/
- 安装：https://baomidou.com/getting-started/install/
- 持久层接口：https://baomidou.com/guides/data-interface/
- 条件构造器：https://baomidou.com/guides/wrapper/
- 流式查询：https://baomidou.com/guides/stream-query/
- 批量操作：https://baomidou.com/guides/batch-operation/
- 自动映射枚举：https://baomidou.com/guides/auto-convert-enum/
- 自动填充字段：https://baomidou.com/guides/auto-fill-field/
- 自动维护 DDL：https://baomidou.com/guides/auto-ddl/
- 主键生成策略：https://baomidou.com/guides/id-generator/
- 逻辑删除支持：https://baomidou.com/guides/logic-delete/
- 分页插件：https://baomidou.com/plugins/pagination/
- 乐观锁插件：https://baomidou.com/plugins/optimistic-locker/
- 多租户插件：https://baomidou.com/plugins/tenant/
- 数据权限插件：https://baomidou.com/plugins/data-permission/
- 动态表名插件：https://baomidou.com/plugins/dynamic-table-name/
- 非法 SQL 拦截插件：https://baomidou.com/plugins/illegal-sql-intercept/
- 防全表更新与删除插件：https://baomidou.com/plugins/block-attack/
- 预防安全漏洞：https://baomidou.com/reference/about-cve/
