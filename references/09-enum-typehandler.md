# 枚举与字段类型处理器

## 枚举自动映射

- 枚举入库值优先用 `@EnumValue` 标记唯一持久化字段。
- 展示给前端的枚举名称、描述、国际化文案不要和数据库持久化值混为一谈。
- 修改枚举持久化值属于数据兼容变更，需确认历史数据和迁移方案。

```java
public enum UserStatus {
    ENABLED(1, "启用"),
    DISABLED(0, "禁用");

    @EnumValue
    private final int code;
    private final String text;
}
```

## JSON 与复杂类型

- JSON 字段可用 MyBatis-Plus 内置 JSON TypeHandler 或项目统一 TypeHandler。
- 字段级 TypeHandler 常需要实体 `@TableName(autoResultMap = true)` 才能在查询结果映射中生效。
- 更新 JSON 字段时确认是整体替换还是局部合并；MyBatis-Plus 默认不会理解业务级 patch 语义。

```java
@TableName(value = "sys_user", autoResultMap = true)
public class User {
    @TableField(typeHandler = JacksonTypeHandler.class)
    private UserProfile profile;
}
```

## 自定义 TypeHandler

- 覆盖 null、空字符串、非法 JSON、未知枚举值和历史数据格式。
- 加密字段 TypeHandler 需要明确密钥来源、脱敏显示和查询条件是否支持。
- 不要在 TypeHandler 中吞异常，否则排查数据污染会非常困难。
