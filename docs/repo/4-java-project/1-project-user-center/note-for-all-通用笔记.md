# 🌟

------

## 什么是yarn？什么是npm？

- `yarn`、`npm` 都是JavaScript的包管理工具，一般使用起来二选一，本地同时安装两个是没问题的；
- 用于：自动化安装、配置、更新和卸载代码包；
- `yarn` 因为并行下载的特性，速度会更快；

------

## Packaging ：项目打包的两种方式

​	Jar & War

​	这两种格式都是 Java 的归档文件格式，用于将项目的编译后的字节码文件、相关的资源文件（如属性文件）、依赖库等打包在一起；

1. Jar（Java Archive）
   - Jar是Java归档文件的标准格式
   - 可以通过简单地执行 `java -jar yourapp.jar` 来运行应用，无需部署到外部 Web 服务器。
   - Jar 包更适合微服务架构，因为它们支持独立部署和运行。

2. War（Web Application Archive）
   - 专门用于Java Web应用的归档格式
   - War 文件在结构上有特定的要求，例如它必须包含一个 WEB-INF 目录，该目录下包含 web.xml 部署描述符；
   - War 包依赖于外部服务器提供运行环境；
3. 根据应用场景选择？
   - 如果是一个独立的 Spring Boot 应用，打算通过执行 Jar 文件来运行，那么选择 Jar 包是比较合适的；
   - 如果需要部署到外部的 Java EE 应用服务器或 Servlet 容器，并且可能需要使用服务器提供的特定服务或资源，那么选择 War 包会更加合适。

------

## 如何实现前/后端集成？

🔗 Ant Design Pro 官方文档：后端集成 部分，地址链接：https://pro.ant.design/zh-CN/docs/openapi

1. OpenAPI：`RESTful` 风格接口文档；
2. 网络请求
3. 代理
4. 调试

------

## OpenAPI 和 Swagger UI

​	OpenAPI 定义了 API 的标准化描述，而 Swagger UI 则是一种实现，它使用 OpenAPI 规范文件来创建一个交互式的 API 文档界面。

1. OpenAPI：**接口规范**

   - 原名 “`Swagger`规范”，用于描述 `RESTful API`;

   - 使用 JSON 或 YAML 来定义 API 的整个结构
     - 包括可用的端点（URLs）、操作（HTTP 方法）、输入参数、输出响应、认证方法；

   - OpenAPI 规范允许开发者定义如何使用和消费一个 API，使得 API 的设计、开发、测试和文档化流程更加标准化和简化；

2. Swagger UI：**开源工具**，根据 OpenAPI（Swagger）规范动态生成美观的 API 文档
   - Swagger UI 通过读取 OpenAPI 规范文件，自动生成一个包含所有API端点、参数和其他信息的可视化界面；
     - 用户可以在浏览器中查看每个 API 的详细信息，并直接在 UI 中发送请求来测试 API 的响应；

------

## 手动添加maven依赖

- 在maven仓库中查找依赖，挑选合适的版本后，从相应页面中复制依赖引用方式到pom.xml中，点击刷新maven；

- 在添加依赖时要注意不同版本的冲突问题，其中可能有坑，需要自己排查清楚；







