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
	port: 80								# 对应 properties 中的 server.port=80 (固定的 key，配置启动端口号)
  servlet:
    context-path: /boot		# 项目根路径
```

#### 3.1.4 配置数据读取方式

##### 1. 单个值 @Value("${key必须写全所有层级的}")｜直接在属性上添加即可；

##### 2. 批量值读取 @ConfigurationProperties(prefix = "通用的前缀") ｜类的属性名 = 最后一个key的值

- 优势1：方便，不用一个一个读取；

- 优势2：可以给集合类型赋值；



### 3.2 搭建模块：配置文件中数据读取

需求：测试两种方式从 application.yaml 文件中获取数据；

两个 url 均测试成功： http://localhost:80/data/show1 , http://localhost:80/data/show2 

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot3-readyaml-terminal.png" alt="image-20240619134720995" style="width:100%;" />

> [!NOTE]
>
> 源代码 - 模块目录：ssm-project / springboot-part [Intellij IDEA 空项目] / **springboot-base-application-02**



### 3.3 多开发环境的配置文件切换、激活｜开发 测试 生产

#### 需求

在Spring Boot中，可以使用多环境配置来根据不同的运行环境（如开发、测试、生产）加载不同的配置。SpringBoot支持多环境配置让应用程序在不同的环境中使用不同的配置参数，例如数据库连接信息、日志级别、缓存配置等。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot3-%E5%A4%9A%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE.png" alt="image-20240619151345706" style="width:80%;" />

以下是常见方法，通用思路是：通过`spring.profiles.active`属性指定当前的环境。

- .properties 文件

​	将应用程序的配置参数分离到不同的属性文件中，每个环境对应一个属性文件。

​	例如，创建`application-dev.properties`、`application-prod.properties`和`application-test.properties`等文件。

​	在这些文件中，可以定义各自环境的配置参数，如数据库连接信息、端口号等。然后，在`application.properties`中通过`spring.profiles.active`属性指定当前使用的环境。Spring Boot会根据该属性来加载对应环境的属性文件，覆盖默认的配置。

- .yaml 文件

​	与属性文件类似，可以将配置参数分离到不同的YAML文件中，每个环境对应一个文件。

​	例如，创建`application-dev.yml`、`application-prod.yml `和 `application-test.yml`等文件。

​	同样，通过`spring.profiles.active`属性指定当前的环境，Spring Boot会加载相应的YAML文件。

- 激活：命令行参数 - 动态指定

​	通过命令行参数来指定当前的环境，例如，可以使用`--spring.profiles.active=dev`来指定使用开发环境的配置。

#### 配置

> 创建开发、测试、生产三个环境的配置文件。 多环境配置（基于 方式2 实践）

application-dev.yml（开发）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///dev
      username: root
      password: root
```

application-test.yml（测试）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///test
      username: root
      password: root
```

application-prod.yml（生产）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///prod
      username: root
      password: root
```
application.yml【环境激活】

```YAML
# 8080 端口总是被占用，所以使用 80 端口
server:
  port: 80

spring:
  # 配置 DataSource[没有删除，但会自动使用dev模式的配置数据]
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://jdbc:mysql://localhost:3306/mybatis-example
      user: root
      password: 1234568
  # 开发环境模式激活
  profiles:
    active: dev #,test 可以配置激活多个模式[eg. 同时打开：数据库配置，连接池...]
    						# 优先级：外部 覆盖 内部
```

【测试】激活 dev 模式，控制台运行结果

- ⚠ 通过@Controller 层配置的 @ConfigurationProperties(prefix = "") 批量读取，测试通过。
- @Value单个取值过于冗余，放弃。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot3-%E6%BF%80%E6%B4%BB%E5%A4%9A%E7%A7%8D%E5%BC%80%E5%8F%91%E6%A8%A1%E5%BC%8F%E9%85%8D%E7%BD%AE-terminal.png" alt="image-20240619161352537" style="width:100%;" />

#### 优先级

- 同时在 application.yaml 和 外部配置文件中的 key：**以外部配置为优先**；

- **注意 :**

  如果设置了spring.profiles.active，并且和application有重叠属性，以active设置优先。

  如果设置了spring.profiles.active，和application无重叠属性，application设置依然生效！

> [!NOTE]
>
> 源代码 - 模块目录：ssm-project / springboot-part [Intellij IDEA 空项目] / **springboot-base-application-02**





## 四、SpringBoot3 整合 SpringMVC|Druid|MyBatis|AOP|TX

> [!NOTE]
>
> 源代码 - 模块目录：ssm-project / springboot-part [Intellij IDEA 空项目] / **springboot-integration-web-druid-mybatis-03**

全部搭建完成之后，项目目录结构和两个配置文件全貌：

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springboot3-integration-structure.png" alt="image-20240620010557175" style="width:50%;" />

### 🌟 完整 pom.xml 依赖配置

不包括 springboot 父项目已经配置好的

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--所有springboot项目都必须继承自 spring-boot-starter-parent-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
    </parent>

    <groupId>com.boyan</groupId>
    <artifactId>springboot-integration-web-druid-mybatis-03</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>

        <!-- TX 整合配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!-- AOP 整合配置 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <!--web 开发的场景启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 数据库相关配置启动器 jdbctemplate 事务相关-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <!-- druid 启动器  -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-3-starter</artifactId>
            <version>1.2.18</version>
        </dependency>

        <!-- mysql 驱动类-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>

        <!-- mybatis spring|启动器 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.1</version>
        </dependency>
      
        <!-- spring-jdbc 模块 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>

        <!-- lombok 注解 -->
        <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>

        <!-- @Resource 等分离出来的注解 -->
        <dependency>
            <groupId>jakarta.platform</groupId>
            <artifactId>jakarta.jakartaee-web-api</artifactId>
            <version>9.1.0</version>
            <scope>compile</scope>
        </dependency>
      
    </dependencies>

    <!--    SpringBoot应用打包插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 🌟 完整 application.yaml 配置内容

```yaml
# web 相关配置
server:
  # 端口号
  port: 80
  # 上下文路径｜项目根路径 [http://localhost:80/boot/]
  servlet:
    context-path: /boot


spring:
  web:
    resources:
      # 静态资源处理：1）springboot自动按照默认的静态资源路径扫描 2）⬇️静态资源处理地址配置，如果设置，会覆盖默认值
      static-locations: classpath:/static/,classpath:/public/,classpath:/META-INF/resources/,classpath:/resources/

# durid 连接池配置 spring.datasourse.type|druid
  datasource:
    # 连接池类型
    type: com.alibaba.druid.pool.DruidDataSource
    # druid 的其他属性配置 springboot3 整合情况下,数据库连接信息必须在 druid 属性下!
    druid:
      url: jdbc:mysql://localhost:3306/mybatis-example
      username: root
      password: 12345678
      driver-class-name: com.mysql.cj.jdbc.Driver
      # 初始化时建立物理连接的个数
      initial-size: 5
      # 连接池的最小空闲数量
      min-idle: 5
      # 连接池最大连接数量
      max-active: 20
      # 获取连接时最大等待时间，单位毫秒
      max-wait: 60000
      # 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
      test-while-idle: true
      # 既作为检测的间隔时间又作为testWhileIdel执行的依据
      time-between-eviction-runs-millis: 60000
      # 销毁线程时检测当前连接的最后活动时间和当前时间差大于该值时，关闭当前连接(配置连接在池中的最小生存时间)
      min-evictable-idle-time-millis: 30000
      # 用来检测数据库连接是否有效的sql 必须是一个查询语句(oracle中为 select 1 from dual)
      validation-query: select 1
      # 申请连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-borrow: false
      # 归还连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-return: false
      # 是否缓存preparedStatement, 也就是PSCache,PSCache对支持游标的数据库性能提升巨大，比如说oracle,在mysql下建议关闭。
      pool-prepared-statements: false
      # 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
      max-pool-prepared-statement-per-connection-size: -1
      # 合并多个DruidDataSource的监控数据
      use-global-data-source-stat: true

# mybatis 配置
mybatis:
  # setting配置
  configuration:
    auto-mapping-behavior: full
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
  # 配置别名
  type-aliases-package: com.boyan.pojo
  # mapper xml位置
  mapper-locations: classpath:/mapper/*.xml

logging:
  level:
    root: debug

```



### 4.1 SpringMVC 整合

- 使用 SpringMVC 就像以前一样，需要配置类就加，比如 Interceptor 还是需要 WebMvcConfig 配置类；

#### 4.1.1 静态资源处理

默认的静态资源路径为：

- classpath:/META-INF/resources/
- classpath:/resources/
- classpath:/static/
- classpath:/public/

我们只要静态资源放在这些目录中任何一个，SpringMVC 都会帮我们处理。 我们习惯会把静态资源放在 classpath:/static/ 目录下。

#### 4.1.2 自定义拦截器 Interceptor

1. 拦截器声明

```Java
package com.boyan.interceptor;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

@Component
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor拦截器的preHandle方法执行....");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor拦截器的postHandle方法执行....");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor拦截器的afterCompletion方法执行....");
    }
}
```

2. 拦截器配置

​	正常使用配置类，只要保证，**配置类要在启动类的同包或者子包方可生效！**

```Java
package com.atguigu.config;

import com.atguigu.interceptor.MyInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Autowired
    private MyInterceptor myInterceptor ;

    /**
     * /**  拦截当前目录及子目录下的所有路径 /user/**   /user/findAll  /user/order/findAll
     * /*   拦截当前目录下的以及子路径   /user/*     /user/findAll
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myInterceptor).addPathPatterns("/**");
    }
}
```



### 4.2 Druid 连接池 整合

> [!TIP]
>
> 只需要基本配置参数，因为 SpringBoot3 现在和 Druid 连接池的适配性其实不是很好，看一下 github 上 alibaba/druid 的 Issues 就可见一些常见问题。暂时 SpringBoot2 + Druid 兼容性还是可以的，想用的话用 2 的版本。

具体配置请见上面的配置信息！

##### ⚠️ Druid 与 Springboot3 不完全适配问题！

> [!WARNING]
>
> 最新版本的druid-spring-boot-3-starter已经兼容！使用1.2.20 以上的版本就不需要配置这个文件了！

通过源码分析，druid-spring-boot-3-starter 目前最新版本是1.2.18，虽然适配了 SpringBoot3，但缺少自动装配的配置文件

需要手动在resources目录下创建META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports

文件内容如下：

```java
com.alibaba.druid.spring.boot3.autoconfigure.DruidDataSourceAutoConfigure
```



### 4.3 MyBatis 整合｜@Mapper @Select("sql语句")

Mapper接口 用法更新：

```java
package com.boyan.mapper;

import com.boyan.pojo.UserBoot;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import java.util.List;

@Mapper
public interface UserMapper {
    @Select("SELECT * FROM user")
    List<UserBoot> queryAll();
}
```



### 4.4 AOP 整合

依赖导入:

```XML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

直接使用aop注解即可: 

```Java
@Component
@Aspect
public class LogAdvice {

    @Before("execution(* com..service.*.*(..))")
    public void before(JoinPoint joinPoint){
        System.out.println("LogAdvice.before");
        System.out.println("joinPoint = " + joinPoint);
    }

}
```



### 4.5 TX 整合

依赖导入:

```XML
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

注：SpringBoot项目会自动配置一个 DataSourceTransactionManager，所以我们只需在方法（或者类）加上 @Transactional 注解，就自动纳入 Spring 的事务管理了

```Java
@Transactional
public void update(){
    User user = new User();
    user.setId(1);
    user.setPassword("test2");
    user.setAccount("test2");
    userMapper.update(user);
}
```





## 五、SpringBoot3 项目打包和运行

### 5.1 添加打包插件

  > 在Spring Boot项目中添加`spring-boot-maven-plugin`插件是为了支持将项目打包成可执行的可运行jar包。如果不添加`spring-boot-maven-plugin`插件配置，使用常规的`java -jar`命令来运行打包后的Spring Boot项目是无法找到应用程序的入口点，因此导致无法运行。

```XML
<!--    SpringBoot 应用 打包插件 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```



### 5.2 通过 Maven 执行打包

1. 在 Intellij IDEA 的 Maven 插件中，点击 package 进行打包；

2. 可以在编译的target文件中查看jar包



### 5.3 通过 命令行 启动 Jar 文件

`java -jar`命令用于在Java环境中执行可执行的JAR文件。下面是关于`java -jar`命令的说明：

```XML
命令格式：java -jar  [选项] [参数] <jar文件名>
```

1. `-D<name>=<value>`：设置系统属性，可以通过`System.getProperty()`方法在应用程序中获取该属性值。例如：`java -jar -Dserver.port=8080 myapp.jar`。
2. `-X`：设置JVM参数，例如内存大小、垃圾回收策略等。常用的选项包括：
    - `-Xmx<size>`：设置JVM的最大堆内存大小，例如 `-Xmx512m` 表示设置最大堆内存为512MB。
    - `-Xms<size>`：设置JVM的初始堆内存大小，例如 `-Xms256m` 表示设置初始堆内存为256MB。
3. `-Dspring.profiles.active=<profile>`：指定Spring Boot的激活配置文件，可以通过`application-<profile>.properties`或`application-<profile>.yml`文件来加载相应的配置。例如：`java -jar -Dspring.profiles.active=dev myapp.jar`。

> [!CAUTION]
>
> 注意： -D 参数必须要在 jar 之前！否则不生效！



