# ActiveRecord、Db Kit 与 SimpleQuery

## 使用边界

- `Model<T>`、`Db`、`SimpleQuery` 都能减少样板代码，但不要绕过项目 Service 层中的权限、租户、审计、事务和缓存逻辑。
- 已有项目如果统一使用 Mapper/Service，不要为了少写代码临时引入 ActiveRecord 风格。
- 静态工具适合管理脚本、轻量查询、字段投影和内部工具；用户接口主流程仍建议走 Service。

## ActiveRecord

- 实体继承 `Model<Entity>` 后可调用 `insert`、`updateById`、`selectById` 等方法。
- 实体方法只表达持久化动作，不应塞入复杂业务规则。
- 逻辑删除、乐观锁和自动填充仍受全局配置、实体注解和插件影响。

```java
@TableName("sys_user")
public class User extends Model<User> {
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;
    private String username;
}

User user = new User();
user.setUsername("admin");
user.insert();
```

## Db Kit

- `Db` 提供通用 CRUD 能力，适合无 Service 的小工具场景。
- 使用前确认事务上下文和数据权限来源；不要在 Controller 中直接调用 `Db` 绕过业务层。

## SimpleQuery

- `SimpleQuery` 适合快速取某一列、转 Map、按条件投影。
- 复杂查询、关联查询、分页排序仍优先 Wrapper、Mapper XML 或项目既有查询封装。
- 返回集合前确认数据量边界，避免无分页大查询。
