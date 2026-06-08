# 注解与实体

## 常用注解

- `@TableName`：表名、schema、`autoResultMap`、排除属性。
- `@TableId`：主键字段与 `IdType`。
- `@TableField`：字段名、是否存在、填充策略、条件策略、TypeHandler。
- `@TableLogic`：逻辑删除字段。
- `@Version`：乐观锁版本字段。
- `@EnumValue`：枚举持久化值。
- `@OrderBy`：实体默认排序，谨慎用于通用查询。

## 实体规则

- 数据库字段和 Java 字段命名转换优先沿用项目全局配置；不要在实体上重复标注全部字段。
- 审计字段如 `createTime`、`updateTime`、`createBy`、`updateBy` 可用 `FieldFill` 配合 `MetaObjectHandler`，但业务字段不要依赖自动填充掩盖参数缺失。
- JSON、枚举、加密字段使用统一 TypeHandler，并确认查询、更新、导出链路都能处理。
- 逻辑删除字段要确认默认值、删除值、唯一索引策略和历史数据兼容。
- 乐观锁字段更新失败时不能静默吞掉，应按项目模式返回并发冲突或重试。

## 示例

```java
@TableName(value = "sys_user", autoResultMap = true)
public class User {
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    private String username;

    @TableField(fill = FieldFill.INSERT)
    private Long createBy;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime updateTime;

    @TableLogic
    private Integer deleted;

    @Version
    private Integer version;
}
```
