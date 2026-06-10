---
name: mybatis-plus-skill
description: >-
  MyBatis-Plus 后端开发技能。仅当项目明确出现 com.baomidou.mybatisplus、
  mybatis-plus-boot-starter、BaseMapper、IService、ServiceImpl、
  LambdaQueryWrapper、MybatisPlusInterceptor、@TableName、@TableId、
  @TableField、@TableLogic、@Version、MetaObjectHandler、Page/IPage、
  MyBatis-Plus 插件或代码生成器等依赖和语法时激活。若只有 MyBatis-Flex、
  原生 MyBatis、JPA 或其他 ORM 证据，不要激活。
---

# MyBatis-Plus 后端开发技能

## 工作方式

1. 先确认项目存在 MyBatis-Plus 证据：`com.baomidou.mybatisplus`、`mybatis-plus-boot-starter`、`BaseMapper`、`IService`、`ServiceImpl`、`LambdaQueryWrapper`、`LambdaUpdateWrapper`、`Wrappers`、`MybatisPlusInterceptor`、`Page`、`IPage` 或 `com.baomidou.mybatisplus.*` 导入。
2. 如果只看到 `com.mybatisflex`、MyBatis-Flex 的 `QueryChain` / `UpdateChain` / `TableDef`，或原生 MyBatis 的 `SqlSession` / XML Mapper，不使用本技能。
3. 确认是 MyBatis-Plus 后，先按任务主题读取 `references/00-index.md` 或对应 reference；不要一次性加载全部文档。
4. 优先复用项目既有 Controller、Service、Mapper、DTO、异常、事务、权限、租户和审计模式。
5. 涉及 API、DTO、SQL、数据库字段、权限、租户、事务、数据源、动态表名、插件拦截器、业务规则时，先梳理完整调用链和影响范围。
6. 保持最小、安全、可回滚修改；未明确要求不新增依赖、不迁移数据、不改变公共契约。

## 快速路由

| 任务主题 | 优先读取 |
|----------|----------|
| 官网功能地图、关键词路由、选择 reference | `references/00-index.md` |
| Maven/Gradle 依赖、Spring Boot 配置、Mapper 扫描 | `references/01-dependency-configuration.md` |
| 实体注解、主键、逻辑删除、乐观锁、自动填充、类型处理器 | `references/02-annotations-entity.md` |
| CRUD、Mapper、Service、分页、批量、流式查询 | `references/03-crud-service-mapper.md` |
| 查询 Wrapper、动态条件、排序、分组、子查询 | `references/04-wrapper-query.md` |
| 更新 Wrapper、部分更新、显式置空、防误更新 | `references/05-wrapper-update.md` |
| 分页、乐观锁、租户、权限、动态表名、防全表更新、SQL 分析 | `references/06-plugins-core.md` |
| 代码生成器、ID 生成器、SQL 注入器、高级扩展 | `references/07-generator-advanced.md` |
| ActiveRecord、Db Kit、SimpleQuery | `references/08-ar-db-simplequery.md` |
| 枚举、JSON、自定义 TypeHandler | `references/09-enum-typehandler.md` |
| 自动填充、`MetaObjectHandler`、审计字段 | `references/10-auto-fill.md` |
| SQL 注入预防、高风险 Wrapper API、安全漏洞 | `references/11-security-sql.md` |
| 自动 DDL、多数据源、复杂扩展边界 | `references/12-ddl-datasource-extension.md` |

## 高优先级规则

- 字段引用优先使用 lambda；只有项目既有模式或动态列名经过白名单校验时，才使用字符串列名。
- 动态查询和更新优先使用 Wrapper 的 condition 参数；“有值才更新”用 `LambdaUpdateWrapper#set(condition, column, value)` 并保留 `where` 条件。
- 需要显式更新为 `null` 时，不要用普通判空条件跳过；先确认 DTO 是否能区分“未传字段”和“传了 null”。
- `last`、`apply`、`inSql`、`exists`、`setSql` 等能拼接 SQL 的能力只用于受控 SQL 片段；禁止拼接未校验用户输入。
- 分页查询必须保留分页参数上限和排序白名单；不要把分页插件当作权限或租户隔离。
- 批量写入、删除、更新前确认事务边界、租户条件、逻辑删除和防全表更新插件是否覆盖。
- `SimpleQuery`、`Db`、ActiveRecord 适合小范围工具化查询或既有项目模式；涉及权限、租户、审计的业务流程仍优先走 Service。
- 新增代码生成器、SQL 注入器、DDL 自动维护、多数据源配置时属于高影响范围，必须先确认可回滚方案。
