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



## 三、《任务列表案例》前后端分离项目 SSM框架

### 3.1 需求分析｜体系结构设计

##### 前端预览图

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-%E4%BB%BB%E5%8A%A1%E5%88%97%E8%A1%A8%E6%A1%88%E4%BE%8B-%E5%89%8D%E7%AB%AF%E9%A2%84%E8%A7%88%E5%9B%BE.png" alt="截屏2024-06-14 23.35.12" style="width:50%;" />

##### 后端体系结构设计

1. **Controller 表述层**：只负责 1. 简化**接收请求** 2. 正常调用Service层逻辑 3. 简化**发送响应**；
2. **Service 业务逻辑层**：1. 分页装配返回的完整list 2. 把 mapper 返回结果封装到 R 结果类对象中 3. 正常调用Mapper 层逻辑；
3. **Mapper 持久层**：Mapper 接口类 --- Mapper.xml 支持自定义 sql 语句，直接与数据库交互；



前端接口文档 ｜需求分析 - 功能点

###### 1. 学习计划分页查询

- 接口文档要求

```Java
/* 
需求说明
    查询全部数据页数据
请求uri
    schedule/{pageSize}/{currentPage}
请求方式 
    get   
响应的json
    {
        "code":200,
        "flag":true,
        "data":{
            //本页数据
            data:
            [
            {id:1,title:'学习java',completed:true},
            {id:2,title:'学习html',completed:true},
            {id:3,title:'学习css',completed:true},
            {id:4,title:'学习js',completed:true},
            {id:5,title:'学习vue',completed:true}
            ], 
            //分页参数
            pageSize:5, // 每页数据条数 页大小
            total:0 ,   // 总记录数
            currentPage:1 // 当前页码
        }
    }
*/
```
###### 2. 学习计划删除

```Java
/* 
需求说明
    根据id删除日程
请求uri
    schedule/{id}
请求方式 
    delete
响应的json
    {
        "code":200,
        "flag":true,
        "data":null
    }
*/
```
###### 3. 学习计划保存

```Java
/* 
需求说明
    增加日程
请求uri
    schedule
请求方式 
    post
请求体中的JSON
    {
        title: '',
        completed: false
    }
响应的json
    {
        "code":200,
        "flag":true,
        "data":null
    }
*/
```
###### 4. 学习计划修改

```Java
/* 
需求说明
    根据id修改数据
请求uri
    schedule
请求方式 
    put
请求体中的JSON
    {
        id: 1,
        title: '',
        completed: false
    }
响应的json
    {
        "code":200,
        "flag":true,
        "data":null
    }
*/
```

##### @Controller 层实现

```java
package com.boyan.controller;

import com.boyan.pojo.Schedule;
import com.boyan.service.ScheduleService;
import com.boyan.pojo.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

/**
 * 控制层只负责两件事：接收参数 ｜ 响应结果
 */
@CrossOrigin
@RestController // == @Controller + @ResponseBody
@RequestMapping("schedule")
@Slf4j
public class ScheduleController {   // 在后端 @Controller层 设置允许非同源访问 restful服务

    @Autowired
    private ScheduleService scheduleService;

    @GetMapping("/{pageSize}/{currentPage}")
    public R page(@PathVariable(name = "pageSize") Integer pageSize, @PathVariable(name = "currentPage") Integer currentPage)
    {
        R r = scheduleService.queryPage(pageSize, currentPage);
        // sl4fj
        // log.info("响应结果：{}",r);
        return r;
    }

    @GetMapping
    public R listAll()
    {
        R r = scheduleService.list();
        // sl4fj
        // log.info("响应结果：{}",r);
        return r;
    }

    @PostMapping
    public R insert(@Validated @RequestBody Schedule schedule, BindingResult result)
    {
        if (result.hasErrors())
        {
            return R.fail("参数为空，不能保存");
        }
        R r = scheduleService.insert(schedule);
        // sl4fj
        // log.info("响应结果：{}",r);
        return r;
    }

    @DeleteMapping("{id}")
    public R deleteById(@PathVariable Integer id)
    {
        R r = scheduleService.deleteById(id);
        // sl4fj
        // log.info("响应结果：{}",r);
        return r;
    }

    @PutMapping
    public R update(@Validated @RequestBody Schedule schedule, BindingResult result)
    {
        if (result.hasErrors())
        {
            return R.fail("参数为空，不能更新");
        }
        R r = scheduleService.update(schedule);
        // sl4fj
        // log.info("响应结果：{}",r);
        return r;
    }
}

```

##### @Service 层实现

```java
package com.boyan.service;

import com.boyan.mapper.ScheduleMapper;
import com.boyan.pojo.Schedule;
import com.boyan.pojo.PageBean;
import com.boyan.pojo.R;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import jakarta.annotation.Resource;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

import java.util.List;

@Slf4j
@Service
public class ScheduleServiceImpl implements ScheduleService{

    @Resource // == @Autowired + @Qualifier
    private ScheduleMapper scheduleMapper;

    /**
     * 分页查询，具体步骤：分页、查询、分页数据装配
     *
     * @param pageSize int 每页显示条数
     * @param currentPage int 当前页数
     * @return
     */
    @Override
    public R queryPage(int pageSize, int currentPage) {
        //1.设置分页参数
        PageHelper.startPage(currentPage,pageSize); // sql 语句后自动追加 limit
        //2.数据库查询
        List<Schedule> scheduleList = scheduleMapper.list();
        //3.结果获取
        PageInfo<Schedule> pageInfo = new PageInfo<>(scheduleList);
        //4.pageBean封装
        PageBean<Schedule> pageBean = new PageBean<>(currentPage,pageSize,pageInfo.getTotal(),pageInfo.getList());
        log.info("分页查询结果:{}",pageBean);

        // 返回结果
        R r = R.ok(pageBean);
        return r;
    }

    @Override
    public R list() {
        R r = R.ok(scheduleMapper.list());
        return r;
    }

    /**
     * @param schedule
     */
    @Override
    public R insert(Schedule schedule) {
        int response = scheduleMapper.insert(schedule);
        if (response == 1) {
            return R.ok(null);
        } else {
            return R.fail(null);
        }

    }

    /**
     * @param id
     */
    @Override
    public R deleteById(Integer id) {
        int response = scheduleMapper.deleteById(id);
        if (response == 1) {
            return R.ok(null);
        } else {
            return R.fail(null);
        }
    }

    /**
     * @param schedule
     */
    @Override
    public R update(Schedule schedule) {
        int response = scheduleMapper.update(schedule);
        if (response == 1) {
            return R.ok(null);
        } else {
            return R.fail(null);
        }
    }
}
```

##### @Mapper 接口

```java
package com.boyan.mapper;

import com.boyan.pojo.Schedule;

import java.util.List;

/**
 * Mapper 接口
 * @author boyan
 */

public interface ScheduleMapper {

    /**
     * Retrieve a list of all schedules.
     *
     * @return List of Schedule objects.
     */
    List<Schedule> list();

    /**
     * Insert a new schedule.
     *
     * @param schedule Schedule object to be inserted.
     * @return int 1 if insert was successful, 0 otherwise.
     */
    int insert(Schedule schedule);

    /**
     * Delete a schedule by its ID.
     *
     * @param id ID of the schedule to be deleted.
     * @return int 1 if delete was successful, 0 otherwise.
     */
    int deleteById(Integer id);

    /**
     * Update an existing schedule.
     *
     * @param schedule Schedule object with updated details.
     * @return int 1 if update was successful, 0 otherwise.
     */
    int update(Schedule schedule);
}
```



### 3.2 前端程序部署

下载前端代码，通过 VScode 打开:

```cmd
sudo npm install  // 自动下载安装依赖
sudo npm run dev  // 运行测试，得到 url 地址
```

Local：http://localhost:5173/ , 暂时保持运行状态，然后部署后端项目，通过这个地址访问页面，前端中已经连接后端url；

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-integration-todolist-frontend.png" alt="image-20240617153812145" style="width:50%;" />



### 3.3 前后端联调｜跨域问题解决 @CrossOrigin

Html 调试，打开 F12 开发者模式；

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-integration-crossorigin.png" alt="image-20240618144627731" style="width:50%;" />

发现问题：浏览器同源策略安全机制 导致；

解决设置：在后台设置可以跨域访问（允许非同源访问）：

​		    在后端，在@Controller上：添加 **@CrossOrigin** 在 XxxController上（允许位置：接口&方法上）允许跨域访问；



### 3.4 前后端分离项目 架构图

##### 前后端各自部署连通逻辑

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-integration-%E5%89%8D%E5%90%8E%E7%AB%AF%E8%81%94%E8%B0%83%E7%BB%93%E6%9E%84.png" alt="image-20240618162700753" style="width:80%;" />



##### 后端架构详解：模块 ssm-integration-todolist-02

![image-20240618144400332](https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-integration-todolist-src.png)



## 六、SSM 依赖总结

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

