# Part05 - SSM - 整合原理与实战

> [!TIP]
>
> 温故而知新 · 实践出真知 ～经过本部分的整合实战，会全面的巩固前面学过的知识，把整体串联到一起就能灵活运用，游刃有余！

## 一、SSM 整合配置理解

**宏观**：Spring接管一切（将框架核心组件交给Spring进行IoC管理），代码更加简洁。

- SpringMVC管理表述层、SpringMVC相关组件
- Spring管理业务层、持久层、以及数据库相关（DataSource,MyBatis）的组件
- 使用IoC的方式管理一切所需组件

**实施**：通过编写配置文件，实现SpringIoC容器接管一切组件。

### Q123. 需要几个 IoC 容器？每个 IoC 容器对应哪些类型组件？容器之间的关系？

1. 两个
2. 容器对应组件

| 容器名   | 盛放组件                                                     |
| -------- | ------------------------------------------------------------ |
| web容器  | web相关组件（controller,springmvc核心组件）                  |
| root容器 | 业务和持久层相关组件（service,aop,tx,dataSource,mybatis,mapper等） |

3. 两个容器之间关系：web容器是root容器的子容器，父子容器关系。子IoC容器可以单向的注入父IoC容器的组件！(调用流程⬇️)

   <img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-%E6%95%B4%E5%90%88-web%E8%B0%83%E7%94%A8root%E6%B5%81%E7%A8%8B.png" alt="image-20240613140225898" style="width:33%;" />

### Q4. 具体配置类个数，以及对应容器关系？

配置类的数量不是固定的，但是至少要两个，建议配置文件：

| 配置名               | 对应内容                   | 对应容器 |
| -------------------- | -------------------------- | -------- |
| WebJavaConfig        | controller, springmvc 相关 | web容器  |
| ServiceJavaConfig    | service, aop, tx 相关      | root容器 |
| MapperJavaConfig     | mapper, mybatis 相关       | root容器 |
| DataSourceJavaConfig | datasource 相关            | root容器 |

### Q5. Spring IoC 初始化方式和配置位置？

✅ SpringIoCInitiator.class | web.xml

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

  //指定root容器对应的配置类
  //root容器的配置类
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] { ServiceJavaConfig.class,MapperJavaConfig.class };
  }
  
  //指定web容器对应的配置类 webioc容器的配置类
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] { WebJavaConfig.class };
  }
  
  //指定dispatcherServlet处理路径，通常为 / 
  @Override
  protected String[] getServletMappings() {
    return new String[] { "/" };
  }
}
```



## 二、SSM 整合配置实战

- Spring IoC 统一管理：Web 容器配置类｜Root 容器配置类 ⬇️ SSM 整合项目框架如下

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-%E6%95%B4%E5%90%88%E9%85%8D%E7%BD%AE%E6%A1%86%E6%9E%B6%E5%9B%BE.png" alt="image-20240613133726880" style="width:100%;" />

- 项目搭建完成后，SSM 项目目录截图 ⬇️

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-%E6%95%B4%E5%90%88-%E6%A8%A1%E5%9D%97%E7%9B%AE%E5%BD%95%E8%B7%AF%E5%BE%84%E6%88%AA%E5%9B%BE.png" alt="image-20240613134028335" style="width:33%;" />

​	其中，pojo 和 XxxMapper 的生成，可以使用 MyBatis-X Generator 插件的逆向工程，自动生成基本 CRUD；

- 模块位于：ssm-project / ssm-integration-part【父工程 maven-pom项目】 / **ssm-integration-01**【转成web项目】

- 测试需求：查询所有员工信息，返回对应 Json 数据！✅

  <img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-%E6%95%B4%E5%90%88%E6%B5%8B%E8%AF%95-restful.png" alt="image-20240613135253871" style="width:33%;" />



## 三、《任务列表案例》前端程序搭建和运行





## 四、《任务列表案例》后端程序实现和测试







## 五、SSM 依赖总结

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.boyan</groupId>
    <artifactId>ssm-project</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <artifactId>ssm-integration-part</artifactId>
  <packaging>pom</packaging>
  <modules>
    <module>ssm-integration-01</module>
  </modules>

  <!-- SSM 整合所有所需要的依赖 -->
  <properties>
    <spring.version>6.0.6</spring.version>
    <jakarta.annotation-api.version>2.1.1</jakarta.annotation-api.version>
    <jakarta.jakartaee-web-api.version>9.1.0</jakarta.jakartaee-web-api.version>
    <jackson-databind.version>2.15.0</jackson-databind.version>
    <hibernate-validator.version>8.0.0.Final</hibernate-validator.version>
    <mybatis.version>3.5.11</mybatis.version>
    <mysql.version>8.0.25</mysql.version>
    <pagehelper.version>5.1.11</pagehelper.version>
    <druid.version>1.2.8</druid.version>
    <mybatis-spring.version>3.0.2</mybatis-spring.version>
    <jakarta.servlet.jsp.jstl-api.version>3.0.0</jakarta.servlet.jsp.jstl-api.version>
    <logback.version>1.2.3</logback.version>
    <lombok.version>1.18.26</lombok.version>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <!--
   需要依赖清单分析:
      spring
        ioc/di
          spring-context / 6.0.6
          jakarta.annotation-api / 2.1.1  jsr250
        aop
          spring-aspects / 6.0.6
        tx
          spring-tx  / 6.0.6
          spring-jdbc / 6.0.6

      springmvc
         spring-webmvc 6.0.6
         jakarta.jakartaee-web-api 9.1.0
         jackson-databind 2.15.0
         hibernate-validator / hibernate-validator-annotation-processor 8.0.0.Final

      mybatis
         mybatis  / 3.5.11
         mysql    / 8.0.25
         pagehelper / 5.1.11

      整合需要
         加载spring容器 spring-web / 6.0.6
         整合mybatis   mybatis-spring x x
         数据库连接池    druid / x
         lombok        lombok / 1.18.26
         logback       logback/ 1.2.3
-->

  <dependencies>
    <!--spring pom.xml依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>jakarta.annotation</groupId>
      <artifactId>jakarta.annotation-api</artifactId>
      <version>${jakarta.annotation-api.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>


    <!--
       springmvc
           spring-webmvc 6.0.6
           jakarta.jakartaee-web-api 9.1.0
           jackson-databind 2.15.0
           hibernate-validator / hibernate-validator-annotation-processor 8.0.0.Final
    -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>jakarta.platform</groupId>
      <artifactId>jakarta.jakartaee-web-api</artifactId>
      <version>${jakarta.jakartaee-web-api.version}</version>
      <scope>provided</scope>
    </dependency>

    <!-- jsp需要依赖! jstl-->
    <dependency>
      <groupId>jakarta.servlet.jsp.jstl</groupId>
      <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
      <version>${jakarta.servlet.jsp.jstl-api.version}</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>${jackson-databind.version}</version>
    </dependency>


    <!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
    <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>${hibernate-validator.version}</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator-annotation-processor -->
    <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator-annotation-processor</artifactId>
      <version>${hibernate-validator.version}</version>
    </dependency>


    <!--
      mybatis
           mybatis  / 3.5.11
           mysql    / 8.0.25
           pagehelper / 5.1.11
    -->
    <!-- mybatis依赖 -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>${mybatis.version}</version>
    </dependency>

    <!-- MySQL驱动 mybatis底层依赖jdbc驱动实现,本次不需要导入连接池,mybatis自带! -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>

    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>${pagehelper.version}</version>
    </dependency>

    <!-- 整合第三方特殊依赖 -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>${mybatis-spring.version}</version>
    </dependency>

    <!-- 日志 ， 会自动传递slf4j门面-->
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>${logback.version}</version>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>${druid.version}</version>
    </dependency>
  </dependencies>

</project>
```

### logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置，ConsoleAppender表示输出到控制台 -->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger] [%msg]%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!-- 设置全局日志级别。日志级别按顺序分别是：TRACE、DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT"/>
    </root>

    <!-- 根据特殊需求指定局部日志级别，可也是包名或全类名。 -->
    <logger name="com.boyan" level="DEBUG"/>

</configuration>
```

