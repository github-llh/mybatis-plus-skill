# 条件构造器更新

## 部分更新

- 普通“有值才更新”使用 `LambdaUpdateWrapper#set(condition, column, value)`。
- 需要显式更新为 `null` 时，先确认 DTO 能区分“未传”和“传 null”；不能区分时不要猜测。
- `updateById(entity)` 会受字段策略影响，null 字段通常不会更新，具体以项目全局配置和字段注解为准。

## 示例

```java
LambdaUpdateWrapper<User> wrapper = Wrappers.lambdaUpdate(User.class)
    .set(StringUtils.hasText(dto.getNickname()), User::getNickname, dto.getNickname())
    .set(dto.getStatus() != null, User::getStatus, dto.getStatus())
    .eq(User::getId, id)
    .eq(User::getTenantId, tenantId);

int rows = userMapper.update(null, wrapper);
```

## 防误更新

- 更新和删除必须包含主键、租户、业务范围或其他明确条件。
- `BlockAttackInnerInterceptor` 只能兜底全表更新删除，不能替代业务条件。
- `setSql` 只用于受控表达式，例如原子自增；用户输入必须作为参数绑定或经过白名单。

## 并发与事务

- 使用 `@Version` 后，更新失败要按并发冲突处理。
- 多表状态更新优先放在 Service 事务内，避免只更新一部分。
