# 代码生成器与高级扩展

## 代码生成器

- 生成前确认包名、模块名、表前缀、实体父类、Controller/Service/Mapper 命名规则、XML 输出目录。
- 不覆盖现有文件，除非用户明确批准并有可回滚方案。
- 生成结果必须审查注解、字段类型、逻辑删除、乐观锁、填充字段和权限入口。
- 代码生成器配置不应固化到业务代码里；优先放在独立工具、测试或开发脚本中。

## 主键与 ID

- `IdType.AUTO` 依赖数据库自增。
- `IdType.ASSIGN_ID` 常用于雪花 ID。
- 自定义 `IdentifierGenerator` 前确认分布式唯一性、时钟回拨、历史数据兼容和序列化格式。

## SQL 注入器

- 自定义通用 Mapper 方法会影响所有使用该 Mapper 基类的模块。
- 方法名、SQL 片段、参数绑定必须与现有 XML/注解 SQL 避免冲突。
- 新增全局方法前优先确认是否能用现有 `BaseMapper`、Wrapper 或 XML 完成。

## 字段类型处理器

- JSON、枚举、加密字段优先使用统一 TypeHandler。
- `@TableName(autoResultMap = true)` 常用于启用字段级 TypeHandler 映射。
- TypeHandler 要覆盖读、写、空值、异常值和历史数据兼容。

## 自动维护 DDL

- DDL 变更属于高风险操作，未明确批准不要执行。
- 需要先确认数据库类型、环境、迁移工具、回滚脚本和发布窗口。
