# 自动填充字段

## 基本规则

- 自动填充通过实现 `MetaObjectHandler` 完成。
- 实体字段必须使用 `@TableField(fill = FieldFill.INSERT)`、`UPDATE` 或 `INSERT_UPDATE` 标记。
- 填充器需要被 Spring 管理，例如 `@Component` 或配置为 `@Bean`。
- 自动填充适合创建时间、更新时间、创建人、更新人、租户等审计字段，不替代业务参数校验。

```java
@Component
public class AuditMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.strictInsertFill(metaObject, "createTime", LocalDateTime.class, LocalDateTime.now());
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.strictUpdateFill(metaObject, "updateTime", LocalDateTime.class, LocalDateTime.now());
    }
}
```

## 常见失败原因

- 实体字段没有 `@TableField(fill = ...)`。
- 字段名或字段类型与 `strictInsertFill` / `strictUpdateFill` 参数不一致。
- 自定义 Mapper 方法参数名不符合 MyBatis-Plus 填充识别规则；更新实体通常需要使用 `@Param("et")`。
- 调用 `update(Wrapper<T> updateWrapper)` 时没有实体对象，自动填充不会生效。
- 批量、自定义 SQL、XML 更新可能绕过预期填充路径，需要按调用链确认。

## 更新建议

- 对普通实体更新，优先传入实体对象并保留 Wrapper 条件：`update(entity, wrapper)`。
- 对纯 Wrapper 更新，需要手动 `set` 审计字段。
- 审计字段来源必须来自可信上下文，不接收前端自由传入。
