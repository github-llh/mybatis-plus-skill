---
name: mybatis-plus-skill
description: >-
  MyBatis-Plus 后端开发技能。仅当后端项目明确使用 MyBatis-Plus 依赖或语法时激活，
  例如 com.baomidou.mybatisplus 依赖、mybatis-plus-boot-starter、
  MybatisPlusInterceptor、BaseMapper、IService、ServiceImpl、LambdaQueryWrapper、
  LambdaUpdateWrapper、Wrappers、SimpleQuery、Db、Model、Page、IPage、@TableName、@TableId、@TableField、
  @TableLogic、@Version、MetaObjectHandler、IdType、Wrapper 条件构造器、
  代码生成器、分页插件、乐观锁、多租户、数据权限、动态表名、防全表更新与删除、
  SQL 分析和打印、非法 SQL 拦截、SQL 注入检查、字段类型处理器等 MyBatis-Plus 能力。
  若项目使用 MyBatis-Flex、原生 MyBatis、JPA 或其他 ORM，且没有 MyBatis-Plus
  依赖或语法证据，不要激活本技能。
---

# MyBatis-Plus 后端开发技能

## 工作方式

1. 先确认项目存在 MyBatis-Plus 证据：`com.baomidou.mybatisplus` 依赖、`mybatis-plus-boot-starter`、`MybatisPlusInterceptor`、`BaseMapper`、`IService`、`ServiceImpl`、`LambdaQueryWrapper`、`LambdaUpdateWrapper`、`Wrappers`、`Page`、`IPage` 或 `com.baomidou.mybatisplus.*` 导入。
2. 如果只看到 `com.mybatisflex`、MyBatis-Flex 的 `QueryChain` / `UpdateChain` / `TableDef`，或原生 MyBatis 的 `SqlSession` / XML Mapper，不使用本技能。
3. 确认是 MyBatis-Plus 后，再识别任务主题，只读取对应 reference；不要一次性加载全部文档。
4. 优先复用项目既有 Controller、Service、Mapper、DTO、异常、事务、权限、租户和审计模式。
5. 默认简单 CRUD 优先 `BaseMapper` / `IService` 既有封装；动态条件优先 `LambdaQueryWrapper` / `LambdaUpdateWrapper`，避免手写字段名字符串。
6. 涉及 API、DTO、SQL、数据库字段、权限、租户、事务、数据源、动态表名、插件拦截器、业务规则时，先梳理完整调用链和影响范围。
7. 保持最小、安全、可回滚修改；未明确要求不新增依赖、不迁移数据、不改变公共契约。

## 快速路由

| 任务主题 | 优先读取 |
|----------|----------|
| 官网功能地图、选择 reference | `references/00-index.md` |
| Maven/Gradle 依赖、Spring Boot 配置、Mapper 扫描 | `references/01-dependency-configuration.md` |
| `@TableName`、`@TableId`、`@TableField`、逻辑删除、乐观锁、自动填充、类型处理器 | `references/02-annotations-entity.md` |
| CRUD、Mapper、Service、批量操作、分页返回 | `references/03-crud-service-mapper.md` |
| `QueryWrapper`、`LambdaQueryWrapper`、动态条件、排序、分组、子查询 | `references/04-wrapper-query.md` |
| `UpdateWrapper`、`LambdaUpdateWrapper`、部分更新、显式置空、防误更新 | `references/05-wrapper-update.md` |
| 分页、乐观锁、多租户、动态表名、数据权限、防全表更新、非法 SQL、SQL 分析与打印 | `references/06-plugins-core.md` |
| 代码生成器、主键策略、自定义 ID、字段类型处理器、高级扩展 | `references/07-generator-advanced.md` |
| ActiveRecord、Db Kit、SimpleQuery、静态工具查询 | `references/08-ar-db-simplequery.md` |
| 枚举自动映射、JSON TypeHandler、自定义类型处理器 | `references/09-enum-typehandler.md` |
| 自动填充字段、`MetaObjectHandler`、审计字段填充失败排查 | `references/10-auto-fill.md` |
| SQL 注入预防、高风险 Wrapper API、安全漏洞规避 | `references/11-security-sql.md` |
| 自动维护 DDL、多数据源、复杂扩展边界 | `references/12-ddl-datasource-extension.md` |

## 高优先级规则

- 条件字段引用优先使用 lambda：`User::getName`、`User::getStatus`；只有项目既有模式或动态列名经过白名单校验时，才使用字符串列名。
- “有值才拼条件”使用 Wrapper 的 condition 参数，避免在外层堆叠大量 `if`。

```java
LambdaQueryWrapper<User> wrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getTenantId, tenantId)
    .like(StringUtils.hasText(dto.getName()), User::getName, dto.getName())
    .eq(dto.getStatus() != null, User::getStatus, dto.getStatus());
```

- “有值才更新”优先使用 `LambdaUpdateWrapper#set(boolean condition, SFunction<T, ?> column, Object val)`，并保留 `where` 条件。

```java
LambdaUpdateWrapper<User> update = Wrappers.lambdaUpdate(User.class)
    .set(StringUtils.hasText(dto.getName()), User::getName, dto.getName())
    .set(dto.getStatus() != null, User::getStatus, dto.getStatus())
    .eq(User::getId, id)
    .eq(User::getTenantId, tenantId);
```

- 需要显式更新为 `null` 时，不要用普通判空条件跳过；先确认 DTO 是否能区分“未传字段”和“传了 null”，必要时用专门的 patch 字段模型或显式 `set(column, null)`。
- `last`、`apply`、`inSql`、`exists`、`setSql` 等能拼接 SQL 的能力只用于受控 SQL 片段；禁止拼接未校验用户输入。
- 分页查询必须保留分页参数上限和排序白名单；不要把分页插件当作权限或租户隔离。
- 批量写入、删除、更新前确认事务边界、租户条件、逻辑删除和防全表更新插件是否覆盖。
- `SimpleQuery`、`Db`、ActiveRecord 适合小范围工具化查询或既有项目模式；涉及权限、租户、审计的业务流程仍优先走 Service。
- 新增代码生成器、SQL 注入器、DDL 自动维护、多数据源配置时属于高影响范围，必须先确认可回滚方案。

## 常用导入

```java
import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.LambdaUpdateWrapper;
import com.baomidou.mybatisplus.core.toolkit.Wrappers;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.baomidou.mybatisplus.extension.service.IService;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.baomidou.mybatisplus.core.metadata.IPage;
```
