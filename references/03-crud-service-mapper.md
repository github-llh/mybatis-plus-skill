# CRUD、Mapper 与 Service

## 分层规则

- Mapper 通常继承 `BaseMapper<Entity>`；复杂 SQL 可保留 XML 或注解 SQL。
- Service 是否继承 `IService<Entity>` 取决于项目既有风格；不要在同一模块混用两套抽象。
- `ServiceImpl<Mapper, Entity>` 适合项目已经统一使用 MyBatis-Plus Service 层封装的场景。
- Controller 不直接拼 Wrapper 查询数据库，除非项目已有明确模式；优先通过 Service 保留权限、租户、审计和事务。

## 常用方法注意

- `getById`、`selectById` 不会自动校验租户和权限，需确认插件或业务层条件覆盖。
- `saveBatch`、`updateBatchById` 要确认批量大小、事务边界和失败处理。
- `removeById` 在逻辑删除启用时通常是逻辑删除；物理删除必须明确业务原因。
- `list`、`selectList` 不带分页时要确认数据量上限。
- `page`、`selectPage` 返回 `Page<T>` / `IPage<T>`，DTO 映射可用项目既有转换工具。

## 分页示例

```java
Page<User> page = Page.of(query.getPageNo(), query.getPageSize());
LambdaQueryWrapper<User> wrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getTenantId, tenantId)
    .like(StringUtils.hasText(query.getUsername()), User::getUsername, query.getUsername())
    .orderByDesc(User::getCreateTime);

IPage<User> result = userMapper.selectPage(page, wrapper);
```

## 流式与大数据量

- 流式查询或大结果集处理前确认连接生命周期、事务边界和异常关闭。
- 不要用 `selectList` 一次性加载无上限数据再在内存中过滤。
