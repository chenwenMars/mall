# Repository Guidelines

## 项目结构与模块职责

本仓库是基于 Java 17、Spring Boot 3.5 的 Maven 多模块后端项目：

- `mall-admin`、`mall-portal`、`mall-search`：管理后台、前台商城及 Elasticsearch 商品搜索接口。
- `mall-common`、`mall-security`：通用工具、统一响应及认证授权。
- `mall-mbg`：MyBatis 生成的模型、Mapper 及代码生成配置。
- `mall-demo`：框架集成示例。

各模块的源码位于 `src/main/java`，配置及 Mapper XML 位于 `src/main/resources`，测试通常位于 `src/test/java`。`document/` 存放 SQL、Docker 配置、部署脚本及图片资源。前端项目由独立仓库维护。

## 构建、测试与本地开发

安装 JDK 17 和 Maven 后，在仓库根目录执行：

- `mvn clean install -Ddocker.skip=true`：构建全部模块并安装到本地 Maven 仓库，跳过 Docker 镜像构建；默认跳过测试。
- `mvn test -DskipTests=false`：显式启用并运行各模块测试。
- `mvn -pl mall-portal -am test -DskipTests=false`：测试商城模块及其依赖模块。
- `mvn -pl mall-admin spring-boot:run -Dspring-boot.run.profiles=dev`：安装依赖后启动管理后台；可将模块名替换为 `mall-portal` 或 `mall-search`。

## 编码风格与命名规范

使用四个空格缩进，类名采用 `UpperCamelCase`，方法和字段采用 `lowerCamelCase`。沿用 `Pms`、`Oms`、`Ums`、`Sms` 业务前缀及 `Controller`、`Service`、`ServiceImpl`、`Dao`、`Param` 等后缀。遵守 Controller → Service → Mapper/DAO 分层，优先复用公共设施。自定义 SQL 放在模块的 `src/main/resources/dao/`；生成代码应通过调整生成器输入更新。目前未配置统一格式化或静态检查工具。

## 测试要求

使用 `spring-boot-starter-test` 提供的 JUnit 5，测试类命名为 `*Test` 或 `*Tests`。现有 `@SpringBootTest` 集成测试依赖相应服务及测试数据，运行前需准备环境。对变更行为补充有效的回归断言；目前未配置覆盖率门槛。管理模块的旧测试位于 `src/test/com/...`，不属于 Maven 默认测试源码目录；新增测试统一放入 `src/test/java`。

## 提交与合并请求

历史提交使用简短的中文或英文描述，也有 `Update <文件名>`，未体现统一的 Conventional Commits 格式。每次提交聚焦单一变更。PR 应说明行为变化、影响模块、关联问题、配置或数据库结构调整，以及验证命令和结果；涉及接口或界面变化时，提供请求示例或截图。

## 配置与调试准则

通过本地配置覆盖 `application-dev.yml`／`application-prod.yml` 中的环境参数，禁止提交真实凭据。初始化数据库时导入 `document/sql/mall.sql`；管理后台需要 MySQL、Redis，其他模块还需准备其配置中声明的服务。

涉及 API 或业务规则时，先查现有实现或官方文档，语义不明确时向维护者确认。校验集中在入口或数据边界，业务流程保持平铺，分步修复根因并验证。禁止吞掉异常、伪造成功或擅自增加兜底默认值。明确获批的兜底必须具备 Warn/Error 日志、文档说明及配置开关。
