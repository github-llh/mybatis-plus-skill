# 依赖与配置

## 触发证据

- Maven/Gradle 依赖包含 `com.baomidou:mybatis-plus-boot-starter`、`mybatis-plus-spring-boot3-starter`、`mybatis-plus-core`、`mybatis-plus-extension`、`mybatis-plus-generator`。
- Java 导入包含 `com.baomidou.mybatisplus.*`。
- 配置类包含 `MybatisPlusInterceptor`、`PaginationInnerInterceptor`、`MetaObjectHandler`、`IdentifierGenerator`。
- YAML/Properties 包含 `mybatis-plus.*` 配置。

## Spring Boot 配置要点

- 优先复用项目现有 `@MapperScan`、数据源、事务管理器和 MyBatis 配置。
- `mapper-locations` 只在存在 XML Mapper 时配置；不要为了简单 CRUD 新增 XML。
- `type-aliases-package`、`type-handlers-package` 应与现有包结构一致。
- `global-config.db-config` 涉及主键、逻辑删除、字段策略时属于公共契约，修改前确认影响范围。
- 多数据源项目不要随意替换 starter 或拦截器配置，先确认每个数据源的 `SqlSessionFactory` 是否都挂载 MyBatis-Plus 插件。

## 最小配置示例

```java
@Configuration
@MapperScan("com.example.modules.**.mapper")
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

## 排查顺序

1. 依赖版本是否与 Spring Boot 主版本兼容。
2. Mapper 是否被扫描，接口是否继承 `BaseMapper<Entity>`。
3. 实体表名、主键、字段策略是否与数据库一致。
4. 插件是否注册在实际使用的 `SqlSessionFactory` 上。
5. XML Mapper 路径和 namespace 是否匹配 Mapper 接口。
