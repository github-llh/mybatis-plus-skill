# 条件构造器查询

## 优先级

1. 类型安全查询优先 `LambdaQueryWrapper<T>` 或 `Wrappers.lambdaQuery(Entity.class)`。
2. 复杂动态 SQL 若 Wrapper 可读性明显下降，保留 XML 并用参数绑定。
3. 字符串列名只允许用于受控白名单映射，例如前端排序字段到数据库列。

## 动态条件

Wrapper 多数方法都有 `condition` 参数，适合“有值才拼条件”。

```java
LambdaQueryWrapper<User> wrapper = Wrappers.lambdaQuery(User.class)
    .eq(User::getTenantId, tenantId)
    .like(StringUtils.hasText(query.getName()), User::getName, query.getName())
    .in(!CollectionUtils.isEmpty(query.getStatusList()), User::getStatus, query.getStatusList())
    .between(query.getStartTime() != null && query.getEndTime() != null,
        User::getCreateTime, query.getStartTime(), query.getEndTime());
```

## 排序与分组

- 前端传排序字段时必须做白名单映射，不直接传入 `orderByAsc(String column)`。
- `groupBy`、`having` 涉及 SQL 片段时优先 XML 或受控模板。
- `last("limit 1")` 只能追加到 SQL 末尾，且不会做参数绑定；不要拼用户输入。

## 高风险 API

- `apply`、`last`、`inSql`、`notInSql`、`exists`、`notExists` 会引入 SQL 片段，必须确认输入完全受控。
- 用户自由输入只能作为参数值，不作为字段名、表名、SQL 片段。
- 多租户、数据权限不要靠 Controller 临时加条件，应优先统一插件或 Service 层策略。
