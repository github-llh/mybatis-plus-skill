# 插件与核心能力

## 拦截器配置

MyBatis-Plus 现代插件通常通过 `MybatisPlusInterceptor` 添加多个 inner interceptor。新增或调整插件前，先确认项目已有配置类和顺序。

```java
MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
interceptor.addInnerInterceptor(new TenantLineInnerInterceptor(tenantLineHandler));
interceptor.addInnerInterceptor(new DynamicTableNameInnerInterceptor());
interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
```

## 常见插件

- 分页插件：设置 `DbType`，保留页大小上限，避免无分页查询。
- 乐观锁插件：实体字段使用 `@Version`，更新失败要反馈并发冲突。
- 多租户插件：租户 ID 来自可信上下文；超级管理员、后台任务、跨租户查询需要明确豁免策略。
- 数据权限插件：权限规则集中生成，不散落在 Controller 或 Mapper 调用处。
- 动态表名插件：动态表名必须由白名单、日期分片或租户上下文生成，不接收自由文本。
- 防全表更新与删除插件：兜底拦截高风险 SQL，不能替代业务 where 条件。
- 非法 SQL 拦截：开启前验证既有复杂 SQL，避免误拦。
- SQL 分析与打印：开发环境辅助定位，生产环境注意性能和敏感数据。

## 安全边界

- Wrapper 高危 API 的 SQL 片段不得来自用户输入。
- 排序字段、动态表名、数据权限字段必须白名单。
- 插件不应绕过认证、授权、数据权限、租户隔离和审计。
