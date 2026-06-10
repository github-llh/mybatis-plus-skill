# MyBatis-Plus 开发技能

[English](README.en.md) | 中文

一个后端项目专用的 Agent Skill，用于在明确使用 MyBatis-Plus 依赖或语法时，辅助编写、排查和优化 Java 持久层代码。

## 简介

此技能只在后端项目存在 MyBatis-Plus 证据时触发，例如：

- `com.baomidou.mybatisplus` 依赖或导入
- `mybatis-plus-boot-starter` / `mybatis-plus-spring-boot3-starter`
- `BaseMapper`、`IService`、`ServiceImpl`
- `LambdaQueryWrapper`、`LambdaUpdateWrapper`、`Wrappers`
- `Page`、`IPage`、`MybatisPlusInterceptor`
- `@TableName`、`@TableId`、`@TableField`、`@TableLogic`、`@Version`
- `MetaObjectHandler`、`IdentifierGenerator`、`@EnumValue`
- 分页、乐观锁、多租户、数据权限、动态表名、防全表更新与删除、非法 SQL 拦截等插件配置

不会因普通 MyBatis、MyBatis-Flex、JPA 或其他 ORM 任务触发；除非项目中能看到明确的 MyBatis-Plus 依赖或语法证据。

## 核心能力

- 依赖与 Spring Boot 配置
- Mapper、Service、`BaseMapper`、`IService`、`ServiceImpl`
- CRUD、分页、批量、流式查询
- `QueryWrapper`、`LambdaQueryWrapper`、`UpdateWrapper`、`LambdaUpdateWrapper`
- 动态条件、排序白名单、部分更新、显式置空
- 实体注解、主键策略、逻辑删除、乐观锁、自动填充
- 枚举自动映射、JSON 字段、自定义 TypeHandler
- 分页插件、乐观锁插件、多租户插件、数据权限插件、动态表名插件
- 防全表更新与删除、非法 SQL 拦截、SQL 分析和打印
- SQL 注入预防、高风险 Wrapper API 使用边界
- 代码生成器、自定义 ID 生成器、SQL 注入器
- ActiveRecord、Db Kit、SimpleQuery
- 自动维护 DDL、多数据源和高级扩展边界

## 使用原则

1. 先确认项目确实使用 MyBatis-Plus。
2. 优先复用项目既有 Controller、Service、Mapper、DTO、事务、权限、租户和审计模式。
3. 查询与更新优先使用 lambda wrapper，避免手写字段名字符串。
4. 涉及 SQL 片段、动态表名、排序字段、数据权限时必须做白名单或受控生成。
5. 涉及 API、DTO、SQL、数据库字段、权限、租户、插件、DDL、多数据源时，先梳理调用链和影响范围。
6. 默认最小、安全、可回滚修改；未明确要求不新增依赖、不迁移数据、不改变公共契约。

## 索引性能设计

- `SKILL.md` 只保留高置信触发证据、排除条件、核心工作方式和 reference 路由，避免默认加载大量官网细节。
- `references/00-index.md` 维护官网功能地图和关键词到 reference 的精确路由，用于在触发后快速选择 1-2 个相关文档。
- 具体语法、插件、安全边界和高级扩展放在独立 reference 中，按需读取，减少上下文占用。
- `agents/openai.yaml` 提供短展示名和短描述，便于工具列表和技能市场快速索引。

## 常用示例

### 动态查询

```java
LambdaQueryWrapper<User> wrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getTenantId, tenantId)
    .like(StringUtils.hasText(query.getUsername()), User::getUsername, query.getUsername())
    .eq(query.getStatus() != null, User::getStatus, query.getStatus())
    .orderByDesc(User::getCreateTime);

IPage<User> page = userMapper.selectPage(Page.of(query.getPageNo(), query.getPageSize()), wrapper);
```

### 部分更新

```java
LambdaUpdateWrapper<User> update = Wrappers.lambdaUpdate(User.class)
    .set(StringUtils.hasText(dto.getNickname()), User::getNickname, dto.getNickname())
    .set(dto.getStatus() != null, User::getStatus, dto.getStatus())
    .eq(User::getId, id)
    .eq(User::getTenantId, tenantId);

int rows = userMapper.update(null, update);
```

需要显式更新为 `null` 时，必须先确认 DTO 能区分“未传字段”和“传了 null”，不能用普通判空逻辑猜测。

### 实体注解

```java
@TableName(value = "sys_user", autoResultMap = true)
public class User {
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    private String username;

    @TableField(fill = FieldFill.INSERT)
    private Long createBy;

    @TableLogic
    private Integer deleted;

    @Version
    private Integer version;
}
```

### 插件配置

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new TenantLineInnerInterceptor(tenantLineHandler));
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
    return interceptor;
}
```

## 参考文档

详细信息请查阅 `references/` 目录：

| 文档 | 说明 |
|------|------|
| [00-index.md](references/00-index.md) | 官网功能地图与 reference 对照 |
| [01-dependency-configuration.md](references/01-dependency-configuration.md) | 依赖、Spring Boot 配置、Mapper 扫描 |
| [02-annotations-entity.md](references/02-annotations-entity.md) | 实体注解、主键、逻辑删除、乐观锁 |
| [03-crud-service-mapper.md](references/03-crud-service-mapper.md) | CRUD、Mapper、Service、分页、批量 |
| [04-wrapper-query.md](references/04-wrapper-query.md) | 查询 Wrapper、动态条件、排序、分组 |
| [05-wrapper-update.md](references/05-wrapper-update.md) | 更新 Wrapper、部分更新、显式置空 |
| [06-plugins-core.md](references/06-plugins-core.md) | 插件主体、分页、乐观锁、租户、数据权限 |
| [07-generator-advanced.md](references/07-generator-advanced.md) | 代码生成器、ID 生成器、SQL 注入器 |
| [08-ar-db-simplequery.md](references/08-ar-db-simplequery.md) | ActiveRecord、Db Kit、SimpleQuery |
| [09-enum-typehandler.md](references/09-enum-typehandler.md) | 枚举自动映射、JSON、TypeHandler |
| [10-auto-fill.md](references/10-auto-fill.md) | 自动填充字段、MetaObjectHandler |
| [11-security-sql.md](references/11-security-sql.md) | SQL 安全、漏洞预防、高风险 API |
| [12-ddl-datasource-extension.md](references/12-ddl-datasource-extension.md) | 自动 DDL、多数据源、高级扩展边界 |

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

## 安装与使用

将整个目录安装到支持 Agent Skills 的工具目录中，必须同时包含 `SKILL.md` 和 `references/`。

```bash
mybatis-plus-skill/
├── SKILL.md
├── README.md
├── README.en.md
├── agents/
│   └── openai.yaml
└── references/
    ├── 00-index.md
    ├── 01-dependency-configuration.md
    ├── 02-annotations-entity.md
    ├── 03-crud-service-mapper.md
    ├── 04-wrapper-query.md
    ├── 05-wrapper-update.md
    ├── 06-plugins-core.md
    ├── 07-generator-advanced.md
    ├── 08-ar-db-simplequery.md
    ├── 09-enum-typehandler.md
    ├── 10-auto-fill.md
    ├── 11-security-sql.md
    └── 12-ddl-datasource-extension.md
```

## 官方文档入口

- 介绍：https://baomidou.com/introduce/
- 安装：https://baomidou.com/getting-started/install/
- 条件构造器：https://baomidou.com/guides/wrapper/
- 持久层接口：https://baomidou.com/guides/data-interface/
- 插件：https://baomidou.com/plugins/
- 预防安全漏洞：https://baomidou.com/reference/about-cve/
