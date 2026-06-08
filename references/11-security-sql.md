# SQL 安全与漏洞预防

## 高风险入口

- `last`、`apply`、`inSql`、`notInSql`、`exists`、`notExists`、`setSql`。
- 字符串列名形式的 `orderBy`、`groupBy`、`select`。
- 动态表名、数据权限 SQL 片段、租户忽略表配置。
- XML Mapper 中的 `${}` 拼接。

## 安全规则

- 用户输入只能作为参数值绑定，不作为字段名、表名、SQL 片段。
- 前端排序字段、筛选字段必须映射到后端白名单。
- `Wrapper` 只能保证参数绑定场景的安全，不能自动保护开发者手写 SQL 片段。
- `BlockAttackInnerInterceptor`、非法 SQL 拦截器是兜底能力，不是业务校验。
- SQL 打印、分析日志中可能包含敏感数据，生产环境需脱敏或关闭。

## 排查清单

1. 是否存在 `${}`、`last(requestParam)`、`apply(rawInput)`。
2. 是否存在前端传字段名后直接进入 Wrapper 字符串列名。
3. 更新、删除是否有主键、租户、业务范围等条件。
4. 多租户和数据权限是否能覆盖自定义 XML。
5. 动态表名是否完全由后端可控规则生成。

## 安全排序示例

```java
Map<String, SFunction<User, ?>> sortColumns = Map.of(
    "createTime", User::getCreateTime,
    "username", User::getUsername
);

SFunction<User, ?> sortColumn = sortColumns.getOrDefault(query.getSort(), User::getCreateTime);
wrapper.orderBy(true, query.isAsc(), sortColumn);
```
