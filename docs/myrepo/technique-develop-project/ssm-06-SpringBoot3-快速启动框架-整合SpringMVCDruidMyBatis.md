# Part06 - SpringBoot3 - 快速启动框架 - 整合SpringMVC|Druid|MyBatis

> [!NOTE]
>
> - 激动人心的 SpringBoot3！之前的 SSM 如果走微服务真的会配置地狱，而 SpringBoot 快速启动！！！【Spring + Boot】快速搭建一个 Spring 家族的项目，同时尽量少写/不写 配置文件！！！！！！
> - **SpringBoot官方文档地址**：https://docs.spring.io/spring-boot/appendix/application-properties/index.html#appendix.application-properties
> - ⬆️ 学完 SSM 之后，SpringBoot3 直接读文档其实就可以上手了！



## 一、SpringBoot3 介绍

SpringBoot 帮我们简单、快速地创建一个独立的、生产级别的 **Spring 应用（说明：SpringBoot底层是Spring）**，大多数 SpringBoot 应用只需要编写少量配置即可快速整合 Spring 平台以及第三方技术！

SpringBoot的主要目标是：

- 为所有 Spring 开发提供更快速、可广泛访问的入门体验。
- 开箱即用，设置合理的默认值，但是也可以根据需求进行适当的调整。
- 提供一系列大型项目通用的非功能性程序（如嵌入式服务器、安全性、指标、运行检查等）。
- 约定大于配置，基本不需要主动编写配置类、也不需要 XML 配置文件。

**总结：简化开发，简化配置，简化整合，简化部署，简化监控，简化运维。**

| 系统要求 - 技术&工具 | 版本（or later）                |
| -------------------- | ------------------------------- |
| maven                | 3.6.3 or later 3.6.3 或更高版本 |
| Tomcat               | 10.0+                           |
| Servlet              | 9.0+                            |
| JDK                  | 17+                             |



## 二、SpringBoot3 快速入门

##### A. 步骤

> 1. 创建Maven工程
> 2. 添加依赖(springboot 父工程依赖 , web启动器依赖)
> 3. 编写启动引导类(springboot 项目运行的入口)
> 4. 编写处理器 Controller
> 5. 启动项目

##### B. Maven 配置 pom.xml

###### Maven配置：1）继承父工程 GAV

SpringBoot 可以帮我们方便的管理项目依赖 , 在 SpringBoot 提供了一个名为 **spring-boot-starter-parent** 的工程，里面已经对各种常用依赖的版本进行了管理，我们的项目需要以这个项目为父工程，这样我们就不用操心依赖的版本问题了，需要什么依赖，直接引入坐标(不需要添加版本)即可！

```XML
<!--所有springboot项目都必须继承自 spring-boot-starter-parent-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.0.5</version>
</parent>
```

###### Maven配置：2）依赖配置 web启动器

为了让Spring Boot帮我们完成各种自动配置，我们必须引入Spring Boot提供的**自动配置依赖**，我们称为**启动器**。因为我们是web项目，这里引入web启动器，在 pom.xml 文件中加入如下依赖：

```XML
<dependencies>
		<!--web开发的场景启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

##### C. 创建启动类｜启动方法

> [!TIP]
>
> ⚠️ 注意： IoC和DI注解需要在启动类的同包或者子包下方可生效！无需指定，约束俗称。

1. **@SpringBootApplication** 是一个特殊的注解，用于标识一个 SpringBoot 应用程序的入口类。

   包含以下三个注解的功能：

   1. @Configuration：将该类标识为应用程序的配置类。

      - 允许使用 Java 代码定义和配置 Bean。

   2. @EnableAutoConfiguration：启用 SpringBoot 的自动配置机制。

      - 根据项目的依赖项自动配置 Spring 应用程序的行为。

      - 自动配置根据类路径、注解和配置属性等条件来决定要使用的功能和配置。

   3. @ComponentScan：自动扫描并加载应用程序中的组件，如控制器、服务、存储库等。

      - 默认扫描 @SpringBootApplication 注解所在类的包及其子包中的组件。

2. **SpringApplication.run()** 方法是启动 SpringBoot 应用程序的关键步骤。

   作用：创建应用程序上下文、自动配置应用程序、启动应用程序，并处理命令行参数，使应用程序能够运行和提供所需的功能。

```Java
package com.boyan;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApplication {
    // 开始入口
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```

##### D. 快速启动成功！✅

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot3-quickstart01-url.png" alt="image-20240619101444010" style="width:33%;" />

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot-quickstart-terminal.png" alt="image-20240619101601779" style="width:100%;" />

> [!NOTE]
>
> 源代码 - 模块目录：ssm-project / springboot-part [Intellij IDEA 空项目] / **springboot-base-quickstart-01**



## 三、SpringBoot3 统一配置文件 application.yaml｜.properties

### 3.1 application 配置文件

1. application 配置文件 是 springboot 为所有框架统一提供配置参数的位置，要求：key 不重复，才能清晰管理；
2. 自动加载配置文件：在Spring Boot工程的 src/main/resources 目录下；

3. yaml 格式比 properties 好，因为 yaml 是有层次结构的，可以继承的文件格式，可读性、表述性好，缩进也不易出错；	

4. 按照官方描述，使用 springboot 提供的配置，修改程序的参数，key 是固定的：[SpringBoot3 官方文档](https://docs.spring.io/spring-boot/appendix/application-properties/index.html#appendix.application-properties) 

5. 自定义配置：外部配置，和 SSM 中引入外部文件的值一样，但是直接 **@Value** 使用就可以，会自动扫描文件；

#### 3.1.1 yaml 🆚 properties

- 如果同时存在 application.properties | application.yml(.yaml) , properties 的优先级更高；
- 与`.properties` 文件相比，YAML格式的优势：具有更好的层次结构表示、自我描述性、注释支持、多行文本表示、复杂数据类型支持和更好的可读性。
- 选择：简单的配置可以使用`.properties`，而复杂的配置可以选择YAML以获得更多的灵活性和可读性

#### 3.1.2 yaml 语法说明

> 1. 数据结构用树形结构呈现，通过缩进来表示层级，
> 2. 连续的项目（集合）通过减号 ” - ” 来表示
> 3. 键值结构里面的key/value对用冒号 ” : ” 来分隔，后面加一个空格再写值；

#### 3.1.3 application.yaml 配置文件示例

```YAML
# 这里是注释，properties 中就不能编写注释
# 1）springboot 配置  2）自定义 配置 -> key 必须唯一不然就混乱了。
server:
	port: 80
# 对应 properties 中的 server.port=80 (固定的 key，配置启动端口号)
```

#### 3.1.4 配置数据读取方式

##### 1. 单个值 @Value("${key必须写全所有层级的}")｜直接在属性上添加即可；

##### 2. 批量值读取 @ConfigurationProperties(prefix = "通用的前缀") ｜类的属性名 = 最后一个key的值

优势1：方便，不用一个一个读取；

优势2：可以给集合类型赋值；



### 3.2 搭建模块测试 配置文件 数据读取





## 四、SpringBoot3 整合 SpringMVC|Druid|MyBatis







## 五、SpringBoot3 项目打包和运行















