# MyBatis-Plus Development Skill

[中文](README.md) | English

An Agent Skill for backend projects that clearly use MyBatis-Plus dependencies or syntax. It helps write, troubleshoot, and improve Java persistence-layer code.

## Introduction

This skill activates only when a backend project has clear MyBatis-Plus evidence, such as:

- `com.baomidou.mybatisplus` dependencies or imports
- `mybatis-plus-boot-starter` / `mybatis-plus-spring-boot3-starter`
- `BaseMapper`, `IService`, `ServiceImpl`
- `LambdaQueryWrapper`, `LambdaUpdateWrapper`, `Wrappers`
- `Page`, `IPage`, `MybatisPlusInterceptor`
- `@TableName`, `@TableId`, `@TableField`, `@TableLogic`, `@Version`
- `MetaObjectHandler`, `IdentifierGenerator`, `@EnumValue`
- Pagination, optimistic locking, tenant, data permission, dynamic table name, block attack, and illegal SQL plugins

It should not activate for plain MyBatis, MyBatis-Flex, JPA, or other ORM tasks unless the project contains clear MyBatis-Plus dependency or syntax evidence.

## Core Capabilities

- Dependencies and Spring Boot configuration
- Mapper, Service, `BaseMapper`, `IService`, `ServiceImpl`
- CRUD, pagination, batch operations, stream queries
- `QueryWrapper`, `LambdaQueryWrapper`, `UpdateWrapper`, `LambdaUpdateWrapper`
- Dynamic conditions, sort whitelists, partial updates, explicit null updates
- Entity annotations, ID strategies, logical delete, optimistic locking, auto-fill
- Enum mapping, JSON fields, custom TypeHandlers
- Pagination, optimistic locker, tenant, data permission, and dynamic table name plugins
- Block attack, illegal SQL interception, SQL analysis and printing
- SQL injection prevention and high-risk Wrapper API boundaries
- Code generator, custom ID generator, SQL injector
- ActiveRecord, Db Kit, SimpleQuery
- Auto DDL, multiple data sources, and advanced extension boundaries

## Usage Principles

1. Confirm the project really uses MyBatis-Plus first.
2. Reuse existing Controller, Service, Mapper, DTO, transaction, permission, tenant, and audit patterns.
3. Prefer lambda wrappers for queries and updates; avoid raw column-name strings.
4. Use whitelists or controlled generation for SQL fragments, dynamic table names, sort fields, and data permission fields.
5. Before changing API, DTO, SQL, database fields, permissions, tenants, plugins, DDL, or data sources, trace the full call chain and impact scope.
6. Keep changes minimal, safe, and reversible by default; do not add dependencies, migrate data, or change public contracts unless explicitly requested.

## Common Examples

### Dynamic Query

```java
LambdaQueryWrapper<User> wrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getTenantId, tenantId)
    .like(StringUtils.hasText(query.getUsername()), User::getUsername, query.getUsername())
    .eq(query.getStatus() != null, User::getStatus, query.getStatus())
    .orderByDesc(User::getCreateTime);

IPage<User> page = userMapper.selectPage(Page.of(query.getPageNo(), query.getPageSize()), wrapper);
```

### Partial Update

```java
LambdaUpdateWrapper<User> update = Wrappers.lambdaUpdate(User.class)
    .set(StringUtils.hasText(dto.getNickname()), User::getNickname, dto.getNickname())
    .set(dto.getStatus() != null, User::getStatus, dto.getStatus())
    .eq(User::getId, id)
    .eq(User::getTenantId, tenantId);

int rows = userMapper.update(null, update);
```

When a field must be explicitly updated to `null`, first confirm the DTO can distinguish "missing field" from "provided null"; do not guess with ordinary null checks.

### Entity Annotations

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

### Plugin Configuration

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

## References

See the `references/` directory for details:

| Document | Description |
|----------|-------------|
| [00-index.md](references/00-index.md) | Official feature map and reference routing |
| [01-dependency-configuration.md](references/01-dependency-configuration.md) | Dependencies, Spring Boot configuration, Mapper scanning |
| [02-annotations-entity.md](references/02-annotations-entity.md) | Entity annotations, IDs, logical delete, optimistic locking |
| [03-crud-service-mapper.md](references/03-crud-service-mapper.md) | CRUD, Mapper, Service, pagination, batch operations |
| [04-wrapper-query.md](references/04-wrapper-query.md) | Query wrappers, dynamic conditions, sorting, grouping |
| [05-wrapper-update.md](references/05-wrapper-update.md) | Update wrappers, partial updates, explicit null updates |
| [06-plugins-core.md](references/06-plugins-core.md) | Plugin core, pagination, locking, tenant, data permission |
| [07-generator-advanced.md](references/07-generator-advanced.md) | Code generator, ID generator, SQL injector |
| [08-ar-db-simplequery.md](references/08-ar-db-simplequery.md) | ActiveRecord, Db Kit, SimpleQuery |
| [09-enum-typehandler.md](references/09-enum-typehandler.md) | Enum mapping, JSON fields, TypeHandlers |
| [10-auto-fill.md](references/10-auto-fill.md) | Auto-fill fields and MetaObjectHandler |
| [11-security-sql.md](references/11-security-sql.md) | SQL safety, vulnerability prevention, high-risk APIs |
| [12-ddl-datasource-extension.md](references/12-ddl-datasource-extension.md) | Auto DDL, multiple data sources, extension boundaries |

## Common Imports

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

## Installation

Install the whole directory into any Agent Skills compatible tool. `SKILL.md` and `references/` must be installed together.

```bash
mybatis-plus-skill/
├── SKILL.md
├── README.md
├── README.en.md
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

## Official Documentation

- Introduction: https://baomidou.com/introduce/
- Installation: https://baomidou.com/getting-started/install/
- Wrapper: https://baomidou.com/guides/wrapper/
- Persistence interfaces: https://baomidou.com/guides/data-interface/
- Plugins: https://baomidou.com/plugins/
- Security and CVE prevention: https://baomidou.com/reference/about-cve/
