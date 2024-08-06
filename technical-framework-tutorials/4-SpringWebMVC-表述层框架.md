# Part04 - SpringMVC - 表述层框架 - 实战指南 - 精华总结版



## 一、SpringMVC 简介和体验

### 1.1 介绍和主要作用

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E6%A1%86%E6%9E%B6%E4%BB%8B%E7%BB%8D%E5%9B%BE.png" alt="image-20240604135259216" style="width:70%;" />

**SpringWebMVC 框架作用：1）简化接收前端参数（形参列表），2）简化后端数据响应（返回值）3）正常调用业务逻辑。**

对比一下⬇️

1. 原生Servlet API开发代码片段

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) 
                                                        throws ServletException, IOException {  
    String userName = request.getParameter("userName");
    
    System.out.println("userName="+userName);
}
```

2. 基于SpringMVC开发代码片段（很明显更优雅）		

```java
@RequestMapping("/user/login")
public String login(@RequestParam("userName") String userName,Sting password){
    
    log.debug("userName="+userName);
    //调用业务即可
    
    return "result";
}
```



### 1.2 核心组件和调用流程理解

Spring MVC 和许多其他 Web 框架一样，是围绕前端控制器模式设计的，其中**中央 Servlet** ， 为 **DispatcherServlet** 请求处理提供共享算法，而实际工作则由可配置的委托组件执行。该模型非常灵活，支持多种工作流程。

**SpringMVC 请求处理流程：**

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E8%AF%B7%E6%B1%82%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B.png" alt="image-20240604152958527" style="zoom:70%;" />

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E8%AF%B7%E6%B1%82%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B2.png" alt="image-20240604153311423" style="width:99%;" />

**SpringMVC 涉及组件理解：**

  1. **DispatcherServlet [ CEO ]** : 

      SpringMVC 提供，我们需要使用 **web.xml 配置**使其生效，它是整个流程处理的核心，所有请求都经过它的处理和分发！

  2. **HandlerMapping [ 秘书 ]** : 

      SpringMVC 提供，我们需要进行 **IoC 配置**使其加入 IoC 容器方可生效，它内部缓存 handler (controller 方法)和 handler 访问路径数据，被 DispatcherServlet 调用，用于查找路径对应的 handler！

  3. **HandlerAdapter[ 经理 ]** : 

     SpringMVC 提供，我们需要进行 **IoC 配置**使其加入 IoC 容器方可生效，它可以处理请求参数和处理响应数据数据，每次DispatcherServlet 都是通过 handlerAdapter 间接调用 handler，他是 handler 和 DispatcherServlet 之间的适配器！

  4. **Handler[ 打工人 ]** : handler 又称处理器，他是 **Controller 类内部的方法**简称，是由我们自己定义，用来接收参数，向后调用业务，最终返回响应结果！

  5. **ViewResovler[ 财务 ]** : SpringMVC 提供，我们需要进行 IoC 配置使其加入 IoC 容器方可生效！视图解析器主要作用简化模版视图页面查找的，但是需要注意，**前后端分离项目，后端只返回 JSON 数据，不返回页面，那就不需要视图解析器！**所以，视图解析器，相对其他的组件 **不是必须的**！



### 1.3 快速体验 (maven-web项目)

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-quickstart-%E9%9C%80%E6%B1%82%E5%9B%BE.png" alt="image-20240604154301471" style="width:70%;" />

##### 1）配置分析

1. DispatcherServlet，设置处理所有请求！
2. HandlerMapping,HandlerAdapter,Handler需要加入到IoC容器，供DS调用！
3. Handler自己声明（Controller）需要配置到HandlerMapping中供DS查找！

##### 2）准备项目

1. 创建项目

    模块：ssm-springmvc-part / **springmvc-base-quick-01** (注意：需要转成maven/web程序！！!)

2. 导入依赖

```XML
<properties>
    <spring.version>6.0.6</spring.version>
    <servlet.api>9.1.0</servlet.api>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>

<dependencies>
    <!-- springioc相关依赖  -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>

    <!-- web相关依赖  -->
    <!-- 在 pom.xml 中引入 Jakarta EE Web API 的依赖 -->
    <!--
        在 Spring Web MVC 6 中，Servlet API 迁移到了 Jakarta EE API，因此在配置 DispatcherServlet 时需要使用
         Jakarta EE 提供的相应类库和命名空间。错误信息 “‘org.springframework.web.servlet.DispatcherServlet’
         is not assignable to ‘javax.servlet.Servlet,jakarta.servlet.Servlet’” 表明你使用了旧版本的
         Servlet API，没有更新到 Jakarta EE 规范。
    -->
    <dependency>
        <groupId>jakarta.platform</groupId>
        <artifactId>jakarta.jakartaee-web-api</artifactId>
        <version>${servlet.api}</version>
        <scope>provided</scope>
    </dependency>

    <!-- springwebmvc相关依赖  -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>

</dependencies>
```

3. Controller声明

```Java
@Controller
public class HelloController {

    //handlers

    /**
     * handler就是controller内部的具体方法
     * @RequestMapping("/springmvc/hello") 就是用来向handlerMapping中注册的方法注解!
     * @ResponseBody 代表向浏览器直接返回数据!
     */
    @RequestMapping("/")
    @ResponseBody
    public String hello(){
        System.out.println("HelloController.hello");
        return "hello springmvc!!";
    }
}

```
4. Spring MVC核心组件配置类

> 声明springmvc涉及组件信息的配置类

```Java
//TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]

//TODO: 导入handlerMapping和handlerAdapter的三种方式
 //1.自动导入handlerMapping和handlerAdapter [推荐]
 //2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载
 //3.使用@Bean方式配置handlerMapper和handlerAdapter
@EnableWebMvc     
@Configuration
@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫
//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    @Bean
    public HandlerMapping handlerMapping(){
        return new RequestMappingHandlerMapping();
    }

    @Bean
    public HandlerAdapter handlerAdapter(){
        return new RequestMappingHandlerAdapter();
    }
    
}

```
5. SpringMVC环境搭建

> 对于使用基于 Java 的 Spring 配置的应用程序，建议这样做，如以下示例所示：

```Java
//TODO: SpringMVC提供的接口,是替代web.xml的方案,更方便实现完全注解方式ssm处理!
//TODO: Springmvc框架会自动检查当前类的实现类,会自动加载 getRootConfigClasses / getServletConfigClasses 提供的配置类
//TODO: getServletMappings 返回的地址 设置DispatherServlet对应处理的地址
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

  /**
   * 指定service / mapper层的配置类
   */
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return null;
  }

  /**
   * 指定springmvc的配置类
   * @return
   */
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] { SpringMvcConfig.class };
  }

  /**
   * 设置dispatcherServlet的处理路径!
   * 一般情况下为 / 代表处理所有请求!
   */
  @Override
  protected String[] getServletMappings() {
    return new String[] { "/" };
  }
}
```

##### 3）启动测试

配置 tomcat，运行。⚠️注意： tomcat应该是10+版本！方可支持 Jakarta EE API!

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-quickstart-run.png" alt="image-20240605154246790" style="width:80%;" />



## 二、SpringMVC 接收数据

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E6%8E%A5%E6%94%B6%E5%8F%82%E6%95%B0%E5%B0%8F%E7%BB%93.png" alt="image-20240606014327350" style="width:80%;" />



### 2.1 访问路径设置-注解 @RequestMapping @ResponseBody

##### @RequestMapping

@WebServlet("必须使用/开头")

@RequestMapping("不必须使用/开头")  user/login /user/login 都可以

路径设置注解：
1. 精确地址【一个｜多个】 /user/login  {"address1","address2"}

2. 支持模糊： * 任意一层字符串｜** 任意层任意字符串

   * /user/*   ----> /user/a 可以，/user/任意字符串都可以访问

   * /user/**  ----> /user  /user/a /user/a/b 都可以，/user/任意层任意字符串都可以访问，只要是以 /user 开头就行

3. 在类｜方法上 添加 @RequestMapping 的区别

   * 类上：提取通用的访问地址

   * 方法上：具体的 handler 地址

   * 访问：类地址 / 方法地址 即可 

4. 请求方式指定

   - 请求方式处理路径：客户端 -- http(get|post|put|delete) -- dispatcherServlet -- 具体 handler (XxxController 中自定义具体方法)

   * 默认情况：@RequestMapping("login") 只要地址正确，任何类型的请求方法都可以访问

   * 指定请求方式：@RequestMapping(value = "login", method = RequestMethod.POST)

   * 如果发送的请求 不符合请求方式：会出现 405 异常！

5. 注解进阶（只能用在方法上，类上是定义提取的通用 address，不管具体的方式指定）

   * get：@GetMapping("login") == @RequestMapping(value = "login", method = RequestMethod.GET)

   * post：@PostMapping("login") == @RequestMapping(value = "login", method = RequestMethod.POST)

   * put：@PutMapping("login")  == @RequestMapping(value = "login", method = RequestMethod.PUT)

   * delete：@DeleteMapping("login")  == @RequestMapping(value = "login", method = RequestMethod.DELETE)

##### @ResponseBody 简介

`@ResponseBody` 是 Spring MVC 中的一个注解，用于将控制器方法的返回值直接写入到 HTTP 响应体中，而不是通过视图解析器解析成一个视图。它常用于 RESTful Web 服务中，通常结合 `@RequestMapping` 一起使用，以便处理 HTTP 请求并返回 JSON 或 XML 格式的响应数据。

作用

- **直接返回数据**: 将方法返回值直接写入 HTTP 响应体。
- **不经过视图解析**: 返回的数据不会被解析为视图名，而是直接作为响应数据。
- **与 `@RestController` 配合使用**: `@RestController` 本身包含了 `@Controller` 和 `@ResponseBody`，所以在 `@RestController` 下的方法默认返回 JSON 数据。

##### 常见配置问题

出现原因：多个 handler 方法映射了同一个地址，导致 SpringMVC 在接收到这个地址的请求时该找哪个 handler 方法处理。

> There is already 'demo03MappingMethodHandler' bean method com.boyan.mvc.handler.Demo03MappingMethodHandler#empGet() **mapped**.



### 2.2 简化参数接收 - 3种方式：Param|路径｜JSON

#### 2.2.1 Param 🆚 JSON

> Param 参数看起来：key=value&key=value          JSON 参数看起来：{key:value, key:value}

1. 参数编码：  

    - param 类型的参数会被编码为 ASCII 码。例如，假设 `name=john doe`，则会被编码为 `name=john%20doe`。
    
    - JSON 类型的参数会被编码为 UTF-8。
2. 参数顺序：  

    - param 类型的参数没有顺序限制。
    - JSON 类型的参数是有序的。JSON 采用键值对的形式进行传递，其中键值对是有序排列的。
3. 数据类型：  

    - param 类型的参数仅支持字符串类型、数值类型和布尔类型等简单数据类型。
    -  JSON 类型的参数则支持更复杂的数据类型，如数组、对象等。
4. 嵌套性：  

    - param 类型的参数不支持嵌套。
    - JSON 类型的参数支持嵌套，可以传递更为复杂的数据结构。
5. 可读性：  

    - param 类型的参数格式比 JSON 类型的参数更加简单、易读。
    - JSON 格式在传递嵌套数据结构时更加清晰易懂。

总的来说，**Param 类型的参数适用于单一的数据传递**，而 **JSON 类型的参数则更适用于更复杂的数据结构传递**。根据具体的业务需求，需要选择合适的参数类型。

**在实际开发中，常见的做法是：在 GET 请求中采用 param 类型的参数，而在 POST 请求中采用 JSON 类型的参数传递**。（相对于对 服务器 来说的，GET：请求从服务器获取数据，



#### 2.2.2 接收 param 参数

##### 1. 直接接收

客户端请求传参：

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-param-result1.png" alt="image-20240605175158523" style="width:50%;" />

##### 2. 注解指定 @RequireParam

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-param-result2.png" alt="image-20240605214605506" style="width:50%;" />

##### 3. 特殊场景

###### a. 一个变量名传入多值

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-param-result3.png" alt="image-20240605215431372" style="width:50%;" />

###### b. 实体接收

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-param-result4.png" alt="image-20240605220528832" style="width:50%;" />

```java
package com.boyan.param;

import com.boyan.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.List;

/**
 * 讲解：如何接收前端 Param 类型的参数
 */
@Controller
@RequestMapping("param")
public class ParamController {

    /**
     * 直接接收参数   /param/data1?name=root&age=18
     *
     * 要求在形参列表填写对应参数的名称即可：请求参数名 = 形参参数名！
     * 1. 名称相同
     * 2. 可以不传递，不会报错
     */
    @RequestMapping("data1")
    @ResponseBody
    public String data1(String name, int age)
    {
        String result = "name = " + name + " age = " + age;
        System.out.println(result);
        return result;
    }

    /**
     * 注解指定参数   /param/data2?account=root&page=1    (其中 account 必须传递，page 可以不传递)
     *
     * @RequestParam(value = "name", required = false, defaultValue = "root") -> 只能在形参列表，作用：1）指定请求参数名 2）是否必须传递 3）非必需传递，并设置默认值
     * - 注解指定名称可以和参数名称不一样，以 @RequestParam(value="") 指定名称为准。
     */
    @RequestMapping("data2")
    @ResponseBody
    public String data2(@RequestParam("account") String username,
                        @RequestParam(required = false, defaultValue = "1") int page){
        String result = "username = " + username + " page = " + page;
        System.out.println(result);
        return result;
    }


    /**
     * 特殊值
     *
     * 1. 一变量名，多值：key=1&key=2    /param/data3?hbs=玩&hbs=学习
     *    - 直接用同名的集合接收值，加 @RequestParam 注解即可（可指定其他名称）
     *
     * 2. 使用实体类型对象接值            /param/data4?name=Anna&age=18
     *    - 直接用实体类型对象接收值
     *    1. 属性名 == 参数名！！！
     *    2. 没有办法使用@RequestParam注解
     *    3. 默认值：可以直接在User类里写死，传入的就不会生效
     */
    @RequestMapping("data3")
    @ResponseBody
    public String data3(@RequestParam List<String> hbs){
        System.out.println(hbs);
        return "ok";
    }
    @RequestMapping("data4")
    @ResponseBody
    public String data4(User user){
        System.out.println(user);
        return user.toString();
    }
}

```



#### 2.2.3 接收 动态路径 参数 @PathVariable

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-path-result.png" alt="image-20240605221923290" style="width:50%;" />

路径传递参数是一种在 URL 路径中传递参数的方式。在 RESTful 的 Web 应用程序中，经常使用路径传递参数来表示资源的唯一标识符或更复杂的表示方式。Spring MVC 框架提供了 @PathVariable 注解来处理路径传递参数。

@PathVariable 注解允许将 URL 中的占位符映射到控制器方法中的参数。

```java
package com.boyan.path;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * 动态路径参数 接收
 */
@Controller
@RequestMapping("path")
@ResponseBody
public class PathController {

    /**
     * 动态路径设计: /user/{动态部分}/{动态部分}   动态部分使用{}包含即可! {}内部动态标识!
     * 形参列表取值: @PathVariable Long id  如果形参名 = {动态标识} 自动赋值!
     *             @PathVariable("动态标识") Long id  如果形参名 != {动态标识} 可以通过指定动态标识赋值!
     *
     * 访问测试:  /path/user/1/root  -> id = 1  uname = root
     */
    @RequestMapping("user/{id}/{name}")
    // @ResponseBody
    public String user(@PathVariable Long id, @PathVariable("name") String uname){
        System.out.println("id = " + id + ", uname = " + uname);
        return "user_detail";
    }
}

```



#### 2.2.4 接收 JSON 参数 @RequestBody(不是ResponseBody!!!)｜@EnableWebMvc｜导入 JSON 依赖

测试：下载Postman.dmg 安装（目录：Collection测试项目/folder测试专题/request具体的测试条目）

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-postman-example.png" alt="image-20240605233133583" style="width:100%;" />

编写 JSON 测试

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-json-test-result200.png" alt="image-20240605235310078" style="width:100%;" />

**源代码配置过程**

前端传递 JSON 数据时，Spring MVC 框架可以使用 `@RequestBody` 注解来将 JSON 数据转换为 Java 对象。`@RequestBody` 注解表示当前方法参数的值应该从请求体中获取，并且需要指定 value 属性来指示请求体应该映射到哪个参数上。其使用方式和示例代码如下：

1. 前端发送 JSON 数据的示例：（使用postman测试）

```JSON
{
  "name": "张三",
  "age": 18,
  "gender": "男"
}
```

2. 定义一个用于接收 JSON 数据的 Java 类，例如：

```Java
package com.boyan.pojo;

import lombok.Data;

@Data
public class Person {
    private String name;
    private int age;
    private String gender;
}
```

##### @RequestBody 接收 JSON 数据

3. 

在Controller中，使用 `@RequestBody` 注解来接收 JSON 数据，并将其转换为 Java 对象，例如：

```Java
package com.boyan.json;

import com.boyan.pojo.Person;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * JSON 参数 接收
 */
@Controller
@RequestMapping("json")
@ResponseBody
public class JsonController {

    @RequestMapping("person")
    public String addPerson(@RequestBody Person person) {	// @RequestBody
        return person.toString();
    }
}
```

在上述代码中，`@RequestBody` 注解将请求体中的 JSON 数据映射到 `Person` 类型的 `person` 参数上，并将其作为一个对象来传递给 `addPerson()` 方法进行处理。

4. **完善 JSON 配置** 

   **解决方案：1） @EnableWebMvc  2）SpringMvcConfig implements WebMvcConfigurer  3）pom.xml 加入jackson依赖**

   过程：

   - 测试发现问题：org.springframework.web.HttpMediaTypeNotSupportedException: Content-Type 'application/json;charset=UTF-8' is not supported]

   - 原因：

     - 不支持 json 数据类型处理

     - 没有 json 类型处理的工具（jackson）

   - 解决：

     - springmvc handlerAdpater 配置 json 转化器,配置类需要明确：

     ```Java
     //TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]
     
     //TODO: 导入handlerMapping和handlerAdapter的三种方式
      //1.自动导入handlerMapping和handlerAdapter [推荐]
      //2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载
      //3.使用@Bean方式配置handlerMapper和handlerAdapter
     
     @EnableWebMvc   // 1）JSON 数据处理，必须使用此注解，因为它会加入 JSON 处理器
     @Configuration
     @ComponentScan({"com.boyan.param","com.boyan.path","com.boyan.json"})
     public class SpringMvcConfig implements WebMvcConfigurer {
     		// 2）WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
       
         /*
         @EnableWebMvc 自动配置以下内容，不需要再自定义了！！！可以删去
          // handlerMapping
         @Bean
         public RequestMappingHandlerMapping handlerMapping(){
             return new RequestMappingHandlerMapping();
         }
     
         // handlerAdapter
         @Bean
         public RequestMappingHandlerAdapter handlerAdapter(){
             return new RequestMappingHandlerAdapter();
         }*/
     }
     ```

     - pom.xml 加入 jackson 依赖

     ```XML
     <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-databind</artifactId>
         <version>2.15.0</version>
     </dependency>
     ```

##### @EnableWebMvc 注解作用

`@EnableWebMvc` 是 Spring MVC 中的一个注解，用于启用 Spring MVC 的默认配置和支持。它通常用于 Java 配置类中，以取代 XML 配置。这一注解通过导入 `DelegatingWebMvcConfiguration` 类，提供了基本的 MVC 配置，如视图解析器、消息转换器、静态资源处理等。

主要作用

1. **启用 MVC 配置**：`@EnableWebMvc` 启用一组默认的 Spring MVC 配置，如视图解析器、静态资源处理、消息转换器等。
2. **配置 Web 应用程序上下文**：它将一些常用的 Spring MVC 组件自动配置到 Web 应用程序上下文中。
3. **简化配置**：减少了繁琐的 XML 配置，只需在 Java 配置类上添加该注解，即可启用 Spring MVC 的所有基本功能。



### 2.3 接收 Cookie 参数数据 @CookieValue

可以使用 `@CookieValue` 注释将 HTTP Cookie 的值绑定到控制器中的方法参数。

下面的示例演示如何获取 cookie 值：

```Java
package com.boyan.cookie;

import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("cookie")
@ResponseBody
public class CookieController {

    // 取 cookie
    @RequestMapping("data")
    public String data(@CookieValue(value = "cookieName") String value) {
        System.out.println("value = " + value);
        return value;
    }
    // 存 cookie
    @RequestMapping("save")
    public String save(HttpServletResponse response) {
        Cookie cookie = new Cookie("cookieName", "root");
        response.addCookie(cookie);
        return "ok";
    }
}

```

先存再取

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-cookie-save-result.png" alt="image-20240606002259794" style="width:50%;" />

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-cookie-data-result.png" alt="image-20240606002339940" style="width:50%;" />



### 2.4 接收 请求头 参数数据 @RequestHeader

可以使用 `@RequestHeader` 批注将请求标头绑定到控制器中的方法参数。

**请考虑以下带有标头的请求**：

```Java
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300
```

下面的示例获取 `Accept-Encoding` 和 `Keep-Alive` 标头的值：

```Java
@GetMapping("/demo")
public void handle(
    @RequestHeader("Accept-Encoding") String encoding, 
    @RequestHeader("Keep-Alive") long keepAlive) { 
  //...
}
```

获取 请求头 参数示例：

```java
package com.boyan.header;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("header")
@ResponseBody
public class HeaderController {

    @RequestMapping("data")
    public String data(@RequestHeader("Host") String host)
    {
        System.out.println("host = " + host);
        return "header = " + host;
    }
}
```

测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-header-result.png" alt="image-20240606010434103" style="width:50%;" />



### 2.5 原生API对象 操作

下表描述了**支持的控制器方法参数**（⚠️直接在形参列表中声明传入，没有先后顺序，愿意声明几个就几个!!!）

| Controller method argument 控制器方法参数                    | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `jakarta.servlet.ServletRequest`, `jakarta.servlet.ServletResponse` | 请求/响应对象                                                |
| `jakarta.servlet.http.HttpSession`                           | 强制存在会话。因此，这样的参数永远不会为 `null` 。           |
| `java.io.InputStream`, `java.io.Reader`                      | 用于访问由 Servlet API 公开的原始请求正文。                  |
| `java.io.OutputStream`, `java.io.Writer`                     | 用于访问由 Servlet API 公开的原始响应正文。                  |
| `@PathVariable`                                              | 接收路径参数注解                                             |
| `@RequestParam`                                              | 用于访问 Servlet 请求参数，包括多部分文件。参数值将转换为声明的方法参数类型。 |
| `@RequestHeader`                                             | 用于访问请求标头。标头值将转换为声明的方法参数类型。         |
| `@CookieValue`                                               | 用于访问Cookie。Cookie 值将转换为声明的方法参数类型。        |
| `@RequestBody`                                               | 用于访问 HTTP 请求正文。正文内容通过使用 `HttpMessageConverter` 实现转换为声明的方法参数类型。 |
| `java.util.Map`, `org.springframework.ui.Model`, `org.springframework.ui.ModelMap` | 共享域对象，并在视图呈现过程中向模板公开。                   |
| `Errors`, `BindingResult`                                    | 验证和数据绑定中的错误信息获取对象！                         |

获取原生对象示例：

```Java
package com.boyan.api;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("api")
public class ApiControllor {
    /**
     * TODO. 如果想要获取请求或者响应对象, 或者会话等, 可以直接在形参列表传入, 并且不分先后顺序!
     * TODO. 注意: 接收原生对象, 并不影响参数接收!
     */
    @GetMapping("data")
    @ResponseBody
    public String api(HttpSession session, HttpServletRequest request, HttpServletResponse response){
        String method = request.getMethod();
        System.out.println("method = " + method);
        return "method = " + method;
    }
}
```

测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-api-result.png" alt="image-20240606011751359" style="width:50%;" />

### 2.6 共享域对象 操作

#### 1. 共享域？在 Servlet 中存储数据

在 JavaWeb 中，**共享域 指的是在 Servlet 中存储数据**，以便在同一 Web 应用程序的多个组件中进行共享和访问。

**常见的共享域有四种**：`ServletContext`、`HttpSession`、`HttpServletRequest`、`PageContext`。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-share-%E5%85%B1%E4%BA%AB%E5%9F%9F%E5%9B%BE%E8%A7%A3.png" alt="image-20240606013056374" style="width:50%;" />

1. `ServletContext` 共享域：`ServletContext` 对象可以在整个 Web 应用程序中共享数据，是最大的共享域。一般可以用于保存整个 Web 应用程序的全局配置信息，以及所有用户都共享的数据。在 `ServletContext` 中保存的数据是线程安全的。
2. `HttpSession` 共享域：`HttpSession` 对象可以在同一用户发出的多个请求之间共享数据，但只能在同一个会话中使用。比如，可以将用户登录状态保存在 `HttpSession` 中，让用户在多个页面间保持登录状态。
3. `HttpServletRequest` 共享域：`HttpServletRequest` 对象可以在同一个请求的多个处理器方法之间共享数据。比如，可以将请求的参数和属性存储在 `HttpServletRequest` 中，让处理器方法之间可以访问这些数据。
4. `PageContext` 共享域：`PageContext` 对象是在 JSP 页面Servlet 创建时自动创建的。它可以在 JSP 的各个作用域中共享数据，包括`pageScope`、`requestScope`、`sessionScope`、`applicationScope` 等作用域。

共享域的作用是提供了方便实用的方式在同一 Web 应用程序的多个组件之间传递数据，并且可以将数据保存在不同的共享域中，根据需要进行选择和使用。

#### 2. 原生 API 方式使用即可

```java
@Controller
@RequestMapping("share")
@ResponseBody
public class ShareController {
  
  @Autowired
  private ServletContext servletContext; // Spring IoC 容器自动装配
  
  // 原生 API 方式 获取
  @RequestMapping("data") 
  public void data(HttpServletRequest request, HttpSession session){
    // ...
    String method = request.getMethod();
    System.out.println("method = " + method);
  }
}
```

#### 3. springmvc 提供的方法（了解即可）

https://www.wolai.com/mY4orG21749UeVBHsefaAb 2.6 共享域对象操作；





## 三、SpringMVC 响应数据

### 3.1 前置信息回顾

#### 3.1.1 handler 方法结构复习

**什么是 handler？**：每个处理业务的方法叫做一个：handler，位于某个 controller 中（表述层）；

**SpringMVC 简化了参数接收和响应**：

- **Request** 请求数据接收：通过 handler 的**形参列表**

- **Response** 前端数据响应：通过 handler 的 **return 关键字**快速处理！

```java
/**
 * TODO: 一个controller的方法是控制层的一个处理器,我们称为handler
 * TODO: handler需要使用@RequestMapping/@GetMapping系列,声明路径,在HandlerMapping中注册,供DS查找!
 * TODO: handler作用总结:
 *       1.接收请求参数(param,json,pathVariable,共享域等) 
 *       2.调用业务逻辑 
 *       3.响应前端数据(页面（不讲解模版页面跳转）,json,转发和重定向等)
 * TODO: handler如何处理呢
 *       1.接收参数: handler(形参列表: 主要的作用就是用来接收参数)
 *       2.调用业务: { 方法体  可以向后调用业务方法 service.xx() }
 *       3.响应数据: return 返回结果,可以快速响应前端数据
 */
@GetMapping
public Object handler(简化请求参数接收){
    调用业务方法
    返回的结果 （页面跳转，返回数据（json））
    return 简化响应前端数据;
}
```

#### 3.1.2 开发模式：前后端分离｜混合开发

在 Web 开发中，有两种主要的开发模式：前后端分离和混合开发。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E5%BC%80%E5%8F%91%E6%A8%A1%E5%BC%8F%E5%9B%9E%E9%A1%BE.png" alt="image-20240606015621568" style="width:70%;" />

1. **混合开发模式**

  指将前端和后端的代码集成在同一个项目中，共享相同的技术栈和框架。这种模式在小型项目中比较常见，可以减少学习成本和部署难度。但是，在大型项目中，这种模式会导致代码耦合性很高，维护和升级难度较大。**对于混合开发，我们就需要使用动态页面技术（JSP）动态展示Java的共享域数据！！**

2. **前后端分离模式：[重点]**

  指将前端的界面和后端的业务逻辑通过接口分离开发的一种方式。开发人员使用不同的技术栈和框架，前端开发人员主要负责页面的呈现和用户交互，后端开发人员主要负责业务逻辑和数据存储。**前后端通信通过 API 接口完成，数据格式一般使用 JSON 或 XML**。前后端分离模式可以提高开发效率，同时也有助于代码重用和维护。

#### 3.1.3 JSP (JavaServer Pages)

> JSP（JavaServer Pages）是一种动态网页开发技术，它是由 Sun 公司提出的一种基于 Java 技术的 Web 页面制作技术，可以在 HTML 文件中嵌入 Java 代码，使得生成动态内容的编写更加简单。
>
> JSP 最主要的作用是生成动态页面。它允许将 Java 代码嵌入到 HTML 页面中，以便使用 Java 进行数据库查询、处理表单数据和生成 HTML 等动态内容。另外，JSP 还可以与 Servlet 结合使用，实现更加复杂的 Web 应用程序开发。
>
> JSP 的主要特点包括：
>
> 1. 简单：JSP 通过将 Java 代码嵌入到 HTML 页面中，使得生成动态内容的编写更加简单。
> 2. 高效：JSP 首次运行时会被转换为 Servlet，然后编译为字节码，从而可以启用 Just-in-Time（JIT）编译器，实现更高效的运行。
> 3. 多样化：JSP 支持多种标准标签库，包括 JSTL（JavaServer Pages 标准标签库）、EL（表达式语言）等，可以帮助开发人员更加方便的处理常见的 Web 开发需求。
>
> 总之，JSP 是一种简单高效、多样化的动态网页开发技术，它可以方便地生成动态页面和与 Servlet 结合使用，是 Java Web 开发中常用的技术之一。

##### JSP 依赖引入 pom.xml

```xml
<!-- jsp需要依赖! jstl-->
<dependency>
    <groupId>jakarta.servlet.jsp.jstl</groupId>
    <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

#### 3.1.4 转发和重定向

##### 转发（Forward）

是服务器内部的一种操作，客户端并不知道服务器进行了页面跳转。转发是在服务器端完成的，不改变URL。

特点：

- **服务器内部操作**：请求从一个Servlet转发到另一个Servlet或JSP页面，由服务器在内部完成。
- **URL不变**：客户端的浏览器地址栏URL保持不变。
- **共享请求和响应对象**：转发前后的Servlet或JSP页面共享同一个请求和响应对象。
- **一次请求**：客户端只发出一次请求，服务器处理后直接返回响应。

##### 重定向（Redirect）

是服务器告诉客户端浏览器要访问的新URL，浏览器会重新发送请求到这个新URL。重定向时，URL会改变。

特点：

- **客户端操作**：服务器响应客户端并告诉它要访问的新URL，浏览器会自动发起新的请求。
- **URL改变**：客户端的浏览器地址栏URL会改变为重定向后的URL。
- **不同请求和响应对象**：重定向前后的请求和响应对象不共享。
- **两次请求**：客户端首先请求服务器，服务器响应一个重定向指令，然后客户端再次请求新的URL。

##### 转发与重定向的区别

| 特点        | 转发（Forward）                | 重定向（Redirect）                           |
| ----------- | ------------------------------ | -------------------------------------------- |
| 操作类型    | 服务器内部操作                 | 客户端操作                                   |
| URL是否改变 | 不变                           | 改变                                         |
| 请求次数    | 一次                           | 两次                                         |
| 共享数据    | 共享同一个请求和响应对象       | 不共享数据，新的请求和响应对象               |
| 用途        | 适用于在服务器内部资源间的跳转 | 适用于跳转到外部资源或告诉客户端新的资源位置 |

##### 使用场景

- **转发**：常用于在服务器内部进行页面跳转，特别是需要在不同的Servlet或JSP页面之间传递数据时。例如，表单验证失败后转发回表单页面显示错误信息。
- **重定向**：常用于需要告知客户端一个新的URL，特别是在处理完成后需要告诉客户端去获取新的资源时。例如，用户登录成功后重定向到用户主页。



### 3.3 动态页面跳转控制 JSP

“handler 如何快速返回一个模板页面”？这里模板页面选用 JSP

1. 引入 JSP 依赖 pom.xml
2. 创建 JSP 页面：建议位置：/WEB-INF/下，避免外部直接访问！位置：/WEB-INF/views/index.jsp

```jsp
<%--
  Created by IntelliJ IDEA.
  User: boyan
  Date: 2024/6/6
  Time: 上午10:04
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <!-- 可以获取共享域的数据,动态展示! jsp== 后台vue -->
    <!-- request.setAttribute("msg","hello jsp!!!") -->
     ${msg}
</body>
</html>

```

3. **配置视图解析器**

   在 SpringMvcConfig.class 中配置 JSP(或其他模版语言) 对应的 视图解析器

```java
// 在类中加入以下方法⬇️
/**
 * 配置jsp对应的视图解析器
 */
@Override
public void configureViewResolvers(ViewResolverRegistry registry) {
    //快速配置 jsp 模板语言对应的路径
    registry.jsp("/WEB-INF/views/",".jsp");
    // handler -> jsp
}
```



#### 1. 快速跳转逻辑视图

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-jsp-index.png" alt="image-20240606104754973" style="width:50%;" />

#### 2. 转发和重定向实现 /forward|/redirect

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-jsp-forward.png" alt="image-20240606111332294" style="width:50%;" />

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-jsp-index.png" alt="image-20240606104754973" style="width:50%;" />

4. **handler 源代码实现｜⚠️路径细节** 

```java
package com.boyan.controller.jsp;

import jakarta.servlet.http.HttpServletRequest;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * 快速返回一个 JSP 页面
 */
@Controller
@RequestMapping("jsp")
// @ResponseBody
public class JspController {

    /**
     * 跳转到提交文件页面  /index
     *
     * TODO. 快速查找视图
     * 1. 方法的返回值是 String 字符串类型
     * 2. 不能添加 @ResponseBody：因为添加后代表直接返回字符串给浏览器，不找 view ，也不走 view 视图解析器
     * 3. 返回值，对应 JSP 页面的名称即可
     *
     * <property name="prefix" value="/WEB-INF/views/"/>
     *  + 逻辑视图名 +
     * <property name="suffix" value=".jsp"/>
     */
    @GetMapping("index")
    public String jumpJsp(HttpServletRequest request){
        request.setAttribute("msg","request data!!");
        return "index";
    }

    /**
     * TODO. 转发到   /index 路径
     *
     * 1. 方法的返回值是 String 字符串类型
     * 2. 不能添加 @ResponseBody，对应 JSP 页面的名称即可
     * 3. 返回字符串前面：forword:/转发到的目标地址
     *
     * ⚠️ 转发：只能转发到项目下的资源跳转
     * @return
     */
    @RequestMapping("/forward")
    public String forward() {
        return "forward:/jsp/index";
    }

    /**
     * TODO. 重定向到 项目内界面：/index 路径
     * 1. 方法的返回值是 String 字符串类型
     * 2. 不能添加 @ResponseBody，对应 JSP 页面的名称即可
     * 3. 返回字符串前面：redirect:/重定向到的目标地址
     *
     * ⚠️ 重定向：项目内外的地址都可以！
     * @return
     */
    @RequestMapping("/redirect")
    public String redirect() {
        return "redirect:/jsp/index";
    }

    /**
     * TODO. 重定向到 项目外页面：eg. 百度网页路径
     * http://localhost:8080/jsp/redirect-baidu
     * @return
     */
    @RequestMapping("/redirect-baidu")
    public String redirectBaidu() {
        return "redirect:http://www.baidu.com";
    }

    /**
     * 路径细节⚠️注意：【使用 springmvc 路径语法时】转发和重定向到【项目下资源】 路径都是相同，都不需要添加项目根路径！填写项目下路径即可！但要写的相对完整！类上的路径都要带着。
     */
}
```



### 3.4 返回 JSON 数据[重点但不难]：通过返回 Json 对应实体类对象实现

#### 3.4.1 源代码操作

##### 前置准备(之前已经配置过了)

1. 导入 JSON 依赖 [pom.xml]

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

2. 添加 JSON 数据转化器 [SpringMvcConfig.class]
   - **@EnableWebMvc**
   - SpringMvcConfig **implements WebMvcConfigurer** {//...}

##### 实战操作

返回值：1）单个实体类对象的 JSON 	2） 集合<实体类对象>的 JSON 

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-output-json-result.png" alt="image-20240606151737321" style="width:50%;" />

![image-20240606152541661](https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/image-20240606152541661.png)

1. 确认完成在上述 3.4.1 前置准备；
2. 定义 JSON 对应 实体类型

```java
package com.boyan.pojo;

import lombok.Data;

@Data
public class User {
    private String name;
    private int age;
}
```

3. 定义 Handler

```java
package com.boyan.controller.json;

import com.boyan.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

/**
 * TODO. Handler：返回 JSON 类型的数据
 */
//@Controller
@RequestMapping("json")
//@ResponseBody
@RestController
public class JsonConfig {

    // 实体类对象 User
    @RequestMapping("data-user")
    public User dataUser(){
        User user = new User();
        user.setName("Anna");
        user.setAge(18);
        return user;
    }   // user -> handlerAdapter -> json -> @ResponseBody -> json直接返回【前后端分离模式】

  
    // 集合 List<Object>
    @RequestMapping("data-userlist")
    public List<User> dataUserList(){

        User user = new User();
        user.setName("Anna");
        user.setAge(18);

        ArrayList<User> users = new ArrayList<>();
        users.add(user);
        return users;
    }
}
```

#### 3.4.2 @ResponseBody

##### 1）方法上使用

用于将方法返回的对象序列化为 JSON 或 XML 格式的数据，并发送给客户端。在前后端分离的项目中使用！

具体来说，@ResponseBody 注解可以用来标识方法或者方法返回值，表示方法的返回值是要直接返回给客户端的数据，而不是由视图解析器来解析并渲染生成响应体（viewResolver没用）。

##### 2）类上使用

如果类中每个方法上都标记了 @ResponseBody 注解，那么这些注解就可以提取到类上。



#### 3.4.3 @RestController【类上注解】 = @ResponseBody + @Controller

类上的 @ResponseBody 注解可以和 @Controller 注解合并为 @RestController 注解。所以使用了 @RestController 注解就相当于给类中的每个方法都加了 @ResponseBody 注解。



### 3.5 静态资源处理

资源本身已经是可以直接拿到浏览器上使用的程度了，**不需要在服务器端做任何运算、处理**。典型的静态资源包括：

- 纯HTML文件、图片、CSS文件、JavaScript文件 ……

1. **在web应用中加入静态资源**：webapp/images/xx.jpg

2. **手动构建确保编译**：Build -> Build Artifacts -> 选择响应的工件 -> Action: Rebuild

   检查一下 target 目录下，确保资源在编译后存在。

3. **在 SpringMvcConfig.class 配置配置类**：

   ```java
   /**
   * 开启静态资源处理 <mvc:default-servlet-handler/>
   */
   @Override
   public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
       configurer.enable();
   }
   ```

4. 访问：http://localhost:8080/images/screenshot.png

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90%E8%AE%BF%E9%97%AE-result.png" alt="image-20240606161254091" style="width:80%;" />



## 四、RESTful 风格设计和实战

### 4.1 RESTful 介绍

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-restful.png" alt="image-20240606163056291" style="width:80%;" />

💡**Restful -> 是最标准的Http使用方案，教如何使用 Http 协议，在每个请求中如何：1）设计路径 2）设计参数传递 3）选择请求方式。**

1）url

2）请求方式：Post：上传｜Get：获取｜Put：更新｜Delete：删除

3）参数传递：param ｜ json｜路径 path



### 4.2 RESTful 特点和规范

#### 4.2.1 特点

1. 每一个URI【统一资源标识符】代表1种资源（URI 是名词）：**名词地址路径传参**，在路径中尽量不要加动作，用请求方式代替动作；
2. 客户端使用GET、POST、PUT、DELETE 4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源；
3. 资源的表现形式是XML或者 **JSON**；
4. 客户端与服务端之间的交互在请求之间是 **无状态的**【不保存上下文状态，没有二次通信】，从客户端到服务端的每个请求都必须包含理解请求所必需的信息。

#### 4.2.2 规范

1. **HTTP协议请求方式要求**

    REST 风格主张在项目设计、开发过程中，具体的操作符合**HTTP协议定义的请求方式的语义**。

| 操作     | 请求方式 |
| -------- | -------- |
| 查询操作 | GET      |
| 保存操作 | POST     |
| 删除操作 | DELETE   |
| 更新操作 | PUT      |

2. **URL路径风格要求**

    ​	REST风格下每个资源都应该有一个唯一的标识符，例如一个 URI（统一资源标识符）或者一个 URL（统一资源定位符）。资源的标识符应该能明确地说明该资源的信息，同时也应该是可被理解和解释的！

    ​	使用 **URL+请求方式** 确定具体的动作，他也是一种标准的HTTP协议请求！

| 操作 | 传统风格                | REST 风格【标准推荐】                   |
| ---- | ----------------------- | --------------------------------------- |
| 保存 | /CRUD/saveEmp           | URL 地址：/CRUD/emp  请求方式：POST     |
| 删除 | /CRUD/removeEmp?empId=2 | URL 地址：/CRUD/emp/2  请求方式：DELETE |
| 更新 | /CRUD/updateEmp         | URL 地址：/CRUD/emp  请求方式：PUT      |
| 查询 | /CRUD/editEmp?empId=2   | URL 地址：/CRUD/emp/2  请求方式：GET    |

- **总结**

    根据接口的具体动作，选择具体的HTTP协议请求方式

    路径设计从原来携带动标识，改成名词，对应资源的唯一标识即可！



### 4.3 Restful 风格实战

上面的是理论，理解之后着重看一下总结 ⬆️，然后根据这个做一遍就会了！实践出真知 ⬇️

##### 场景 and 需求分析

- 数据结构： User {id 唯一标识,name 用户名，age 用户年龄}
- 功能分析
    - 用户数据分页展示功能（条件：page 页数 默认1，size 每页数量 默认 10）
    - 保存用户功能
    - 根据用户id查询用户详情功能
    - 根据用户id更新用户数据功能
    - 根据用户id删除用户数据功能
    - 多条件模糊查询用户功能（条件：keyword 模糊关键字，page 页数 默认1，size 每页数量 默认 10）

##### 🌟 一般设计思路【有/没有请求体、参数是/不是id】

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-restful%E8%AE%BE%E8%AE%A1.png" alt="image-20240606170652737" style="width:60%;" />

- 没有请求体【GET、DELETE】：路径参数 / param参数，进一步判断：参数是不是 id：
  - 是id：**路径**参数
  - 不是id：范围 **param** 参数
- 有请求体【POST、PUT】：**JSON** 参数

##### Restful 接口设计

| 功能     | 接口和请求方式   | 请求参数                                | 返回值       | 功能分析                                                     |
| -------- | ---------------- | --------------------------------------- | ------------ | ------------------------------------------------------------ |
| 分页查询 | GET  /user       | page=1&size=10 【Param】                | { 响应数据 } | 用户数据分页展示功能（条件：page 页数 默认1，size 每页数量 默认 10） |
| 用户添加 | POST /user       | {user 数据 } 【JSON】                   | {响应数据}   | 保存用户功能                                                 |
| 用户详情 | GET /user/1      | 路径参数 【Path】                       | {响应数据}   | 根据用户id查询用户详情功能                                   |
| 用户更新 | PUT /user        | {user 更新数据}【JSON】                 | {响应数据}   | 根据用户id更新用户数据功能                                   |
| 用户删除 | DELETE /user/1   | 路径参数【Path】                        | {响应数据}   | 根据用户id删除用户数据功能                                   |
| 条件模糊 | GET /user/search | page=1&size=10&keywork=关键字 【Param】 | {响应数据}   | 多条件模糊查询用户功能（条件：keyword 模糊关键字，page 页数 默认1，size 每页数量 默认 10） |

##### 源码实现

新建模块：springmvc-base-restful-04

UserController.java

```java
package com.boyan.controller;

import com.boyan.pojo.User;
import org.springframework.web.bind.annotation.*;

/**
 * TODO. Restful 测试，用 Postman 测试通过指定每个请求的：【请求方式】+ url 来调用，会加深理解！！！
 * ⚠️ 同一个url 不同的 请求方式，具体访问的 handler 不同！
 */

@RestController
@RequestMapping("user")
public class UserController {

    /**
     * 分页查询
     * 用户数据分页展示功能（条件：page 页数 默认1，size 每页数量 默认 10）
     * 【Param】
     * url：http://localhost:8080/user | http://localhost:8080/user?page=1&size=10
     */
    @GetMapping
    public Object page(@RequestParam(required = false,defaultValue = "1") int page,
                                @RequestParam(required = false,defaultValue = "10") int size)
    {
        System.out.println("分页查询业务：" + "page = " + page+ "size = " + size);
        return "分页查询业务：" + "page = " + page+ "size = " + size;
    }

    /**
     * 用户添加
     * 保存用户功能
     * 【JSON】通过 Postman 测试
     * url：http://localhost:8080/user 【POST】Json结构体：填写User信息
     */
    @PostMapping
    public Object user(@RequestBody User user)
    {
        System.out.println("保存用户业务：user = " + user);
        return "{'status':'ok'}";
    }

    /**
     * 用户详情
     * 根据用户id查询用户详情功能
     * 【Path】id 动态路径
     * url：http://localhost:8080/user/{id}
     */
    @GetMapping("/{id}")
    public Object user(@PathVariable int id)
    {
        System.out.println("查询用户详情业务：id = " + id);
        return "查询用户详情业务：id = " + id;
    }

    /**
     * 用户更新
     * 根据用户id更新用户数据功能(只传入User，id包含在User里)
     * 【JSON】通过 Postman 测试
     * url：http://localhost:8080/user 【PUT】Json结构体：填写User信息
     */
    @PutMapping
    public Object update(@RequestBody User user)
    {
        System.out.println("更新用户业务：user = " + user);
        return "更新用户业务：user = " + user;
    }

    /**
     * 用户删除
     * 根据用户id删除用户数据功能
     * 【Path】id 动态路径
     * url：http://localhost:8080/user/{id} 【DELETE】
     */
    @DeleteMapping("{id}")
    public Object delete(@PathVariable int id)
    {
        System.out.println("删除用户业务：id = " + id);
        return "删除用户业务：id = " + id;
    }

    /**
     * 条件模糊
     * 多条件模糊查询用户功能（条件：keyword 模糊关键字(String任意字符串），page 页数 默认1，size 每页数量 默认 10）
     * 【Param】
     * url：http://localhost:8080/user/search?keyword=haha&page=2&size=11 【GET】
     */
    @GetMapping("search")
    public Object search(@RequestParam(required = false) String keyword,
                         @RequestParam(required = false,defaultValue = "1") int page,
                         @RequestParam(required = false,defaultValue = "10") int size)
    {
        System.out.println("条件模糊查询业务：keyword = " + keyword + "page = " + page+ "size = " + size);
        return "条件模糊查询业务：keyword = " + keyword + "page = " + page+ "size= " + size;
    }

}

```

补全其他配置 ⬇️

User.java

```java
package com.boyan.pojo;

import lombok.Data;

@Data
public class User {
    private Integer id;
    private String name;
    private Integer age;
}
```

✅ 以及 SpringMvcConfig.class | SpringMvcInit.class 部分通用配置内容。



 

## 五、SpringMVC 其他扩展

### 5.1 全局异常处理：声明式统一管理

#### 5.1.1 两种异常处理方式：编程式｜声明式

1. 编程式
   - 在代码中显式地编写处理异常的逻辑。通常涉及到对异常类型的检测及其处理，例如使用 try-catch 块来捕获异常，然后在 catch 块中编写特定的处理代码，或者在 finally 块中执行一些清理操作。
   - 缺点：异常处理代码混杂在业务代码中，导致代码可读性较差。
2. 声明式（全局式异常处理）
   - 整个项目从架构这个层面设计的异常处理的统一机制和规范。
   - 将异常处理的逻辑从具体的业务逻辑中分离出来，通过配置等方式进行统一的管理和处理。
   - 在声明式异常处理中，开发人员只需要为方法或类标注相应的注解（如 @Throws 或 @ExceptionHandler），就可以处理特定类型的异常。
   - 优点：相较于编程式异常处理，声明式异常处理可以使代码更加简洁、易于维护和扩展。

#### 5.1.2 基于注解异常声明异常处理 @RestControllerAdvice +@ExceptionHandler(指定异常类)

##### 1. 声明异常处理控制器类

- 异常处理控制类，统一定义异常处理handler方法！

##### 2. 声明异常处理hander方法

- 异常处理handler方法和普通的handler方法参数接收和响应都一致！

- 只不过异常处理handler方法要映射异常，发生对应的异常会调用！

- 普通的handler方法要使用@RequestMapping注解映射路径，发生对应的路径调用！

```Java
package com.boyan.error;

import org.springframework.http.converter.HttpMessageNotReadableException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * 全局异常处理
 *
 *  @RestControllerAdvice = @ControllerAdvice + @ResponseBody
 *                          @ControllerAdvice 代表当前类的异常处理controller!
 *
 */

@RestControllerAdvice
public class GlobalExceptionHandler {

    /**
     * 异常处理handler
     * @ExceptionHandler(HttpMessageNotReadableException.class)
     * 该注解标记异常处理Handler,并且指定发生异常调用该方法!
     *
     *
     * @param e 获取异常对象!
     * @return 返回handler处理结果!
     */
    @ExceptionHandler(HttpMessageNotReadableException.class)
    public Object handlerJsonDateException(HttpMessageNotReadableException e){

        return null;
    }

    /**
     * 当发生空指针异常会触发此方法!
     * @param e
     * @return
     */
    @ExceptionHandler(NullPointerException.class)
    public Object handlerNullException(NullPointerException e){

        return null;
    }

    /**
     * 所有异常都会触发此方法!但是如果有具体的异常处理Handler!
     * 具体异常处理Handler优先级更高!
     * 例如: 发生NullPointerException异常!
     *       会触发handlerNullException方法,不会触发handlerException方法!
     * @param e
     * @return
     */
    @ExceptionHandler(Exception.class)
    public Object handlerException(Exception e){

        return null;
    }
}

```
##### 3. 配置文件扫描控制器类配置

- 确保异常处理控制类被扫描：SpringMvcConfig.class

```Java
 <!-- 扫描controller对应的包,将handler加入到ioc-->
 @ComponentScan(basePackages = {"com.boyan.controller","com.boyan.error"})
```



### 5.2 SpringMVC 拦截器

#### 5.2.1 拦截器 SpringMVC 🆚 过滤器 JavaWeb

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-filter-vs-inceptor.png" alt="image-20240606225347082" style="width:70%;" />

- 相似点
    - 拦截：必须先把请求拦住，才能执行后续操作
    - 过滤：拦截器或过滤器存在的意义就是对请求进行统一处理
    - 放行：对请求执行了必要操作后，放请求过去，让它访问原本想要访问的资源
- 不同点
    - 工作平台不同
        - 过滤器工作在 Servlet 容器中
        - 拦截器工作在 SpringMVC 的基础上
    - 拦截的范围
        - 过滤器：能够拦截到的最大范围是整个 Web 应用
        - 拦截器：能够拦截到的最大范围是整个 SpringMVC 负责的请求
    - IOC 容器支持
        - 过滤器：想得到 IOC 容器需要调用专门的工具方法，是间接的
        - 拦截器：它自己就在 IOC 容器中，所以可以直接从 IOC 容器中装配组件，也就是可以直接得到 IOC 容器的支持

**选择：功能需要如果用 SpringMVC 的拦截器能够实现，就不使用过滤器。**



#### 5.2.2 拦截器配置细节和源码分析：HandlerInterceptor 拦截器接口

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-springmvc-interceptor-interface.png" alt="image-20240606225458652" style="width:99%;" />

##### 1. 创建拦截器类

```java
public class Process01Interceptor implements HandlerInterceptor {

    // if( ! preHandler()){return;}
    // 在处理请求的目标 handler 方法前执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler);
        System.out.println("Process01Interceptor.preHandle");
         
        // 返回true：放行
        // 返回false：不放行
        return true;
    }
 
    // 在目标 handler 方法之后，handler报错不执行!
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", modelAndView = " + modelAndView);
        System.out.println("Process01Interceptor.postHandle");
    }
 
    // 渲染视图之后执行(最后),一定执行!
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", ex = " + ex);
        System.out.println("Process01Interceptor.afterCompletion");
    }
}
```

##### 2. 修改 SpringMvcConfig 配置类添加拦截器

```java
//添加拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) { 
        //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
        registry.addInterceptor(new Process01Interceptor());
    }
```

添加后完整配置类

```java
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = {"com.boyan.controller","com.boyan.exceptionhandler"}) 

//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    //配置jsp对应的视图解析器
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        //快速配置jsp模板语言对应的
        registry.jsp("/WEB-INF/views/",".jsp");
    }

    //开启静态资源处理 <mvc:default-servlet-handler/>
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //添加拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) { 
        //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
        registry.addInterceptor(new Process01Interceptor());
    }
}

```

##### 3. 配置详解

###### a. 默认拦截全部

```Java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
}

```
###### b. 精准配置

```Java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
    
    //精准匹配,设置拦截器处理指定请求 路径可以设置一个或者多个,为项目下路径即可
    //addPathPatterns("/common/request/one") 添加拦截路径
    //也支持 /* 和 /** 模糊路径。 * 任意一层字符串 ** 任意层 任意字符串
    registry.addInterceptor(new Process01Interceptor()).addPathPatterns("/common/request/one","/common/request/tow");
}

```
###### c. 排除配置

```Java
//添加拦截器
@Override
public void addInterceptors(InterceptorRegistry registry) {
    
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
    
    //精准匹配,设置拦截器处理指定请求 路径可以设置一个或者多个,为项目下路径即可
    //addPathPatterns("/common/request/one") 添加拦截路径
    registry.addInterceptor(new Process01Interceptor()).addPathPatterns("/common/request/one","/common/request/tow");
    
    
    //排除匹配,排除应该在匹配的范围内排除
    //addPathPatterns("/common/request/one") 添加拦截路径
    //excludePathPatterns("/common/request/tow"); 排除路径,排除应该在拦截的范围内
    registry.addInterceptor(new Process01Interceptor())
            .addPathPatterns("/common/request/one","/common/request/tow")
            .excludePathPatterns("/common/request/tow");
}
```

##### 4. 多个拦截器执行顺序

1. preHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置顺序调用各个 preHandle() 方法。
2. postHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 postHandle() 方法。
3. afterCompletion() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 afterCompletion() 方法。



### 5.3 参数校验 Jsr303

#### 介绍

- Jsr303 系列校验注解：直接在实体类相应属性上方，配置@校验注解，对相应属性配置限制，从而完成校验。

- JSP-303 是由 Java 提供的，由 Hibernate 框架实现的，但 **SpringMVC 支持这套注解实现！**
- 在SpringMVC 中，可直接通过注解驱动 @EnableWebMvc 的方式进行数据校验。配置 @EnableWebMvc后，SpringMVC 会默认装配好一个 LocalValidatorFactoryBean，通过在处理方法的入参上标注 @Validated 注解即可让 SpringMVC 在完成数据绑定后执行数据校验的工作。

#### 标准校验注解 + @Validate 形参

| 注解                       | 规则                                           |
| -------------------------- | ---------------------------------------------- |
| @Null                      | 标注值必须为 null                              |
| @NotNull                   | 标注值不可为 null                              |
| @AssertTrue                | 标注值必须为 true                              |
| @AssertFalse               | 标注值必须为 false                             |
| @Min(value)                | 标注值必须大于或等于 value                     |
| @Max(value)                | 标注值必须小于或等于 value                     |
| @DecimalMin(value)         | 标注值必须大于或等于 value                     |
| @DecimalMax(value)         | 标注值必须小于或等于 value                     |
| @Size(max,min)             | 标注值大小必须在 max 和 min 限定的范围内       |
| @Digits(integer,fratction) | 标注值值必须是一个数字，且必须在可接受的范围内 |
| @Past                      | 标注值只能用于日期型，且必须是过去的日期       |
| @Future                    | 标注值只能用于日期型，且必须是将来的日期       |
| @Pattern(value)            | 标注值必须符合指定的正则表达式                 |

#### 扩展校验注解

| 注解      | 规则                               |
| --------- | ---------------------------------- |
| @Email    | 标注值必须是格式正确的 Email 地址  |
| @Length   | 标注值字符串大小必须在指定的范围内 |
| @NotEmpty | 标注值字符串不能是空字符串         |
| @Range    | 标注值必须在指定的范围内           |

#### 易混点总结  @NotNull  @NotEmpty  @NotBlan

@NotNull、@NotEmpty、@NotBlank 都是用于在数据校验中检查字段值是否为空的注解，但是它们的用法和校验规则有所不同。

1. @NotNull  (包装类型不为null)

    @NotNull 注解是 JSR 303 规范中定义的注解，当被标注的字段值为 null 时，会认为校验失败而抛出异常。该注解不能用于字符串类型的校验，若要对字符串进行校验，应该使用 @NotBlank 或 @NotEmpty 注解。
2. @NotEmpty (集合类型长度大于0)

    @NotEmpty 注解同样是 JSR 303 规范中定义的注解，对于 CharSequence、Collection、Map 或者数组对象类型的属性进行校验，校验时会检查该属性是否为 Null 或者 size()==0，如果是的话就会校验失败。但是对于其他类型的属性，该注解无效。需要注意的是只校验空格前后的字符串，如果该字符串中间只有空格，不会被认为是空字符串，校验不会失败。
3. @NotBlank （字符串，不为null，切不为"  "字符串）

    @NotBlank 注解是 Hibernate Validator 附加的注解，对于字符串类型的属性进行校验，校验时会检查该属性是否为 Null 或 “” 或者只包含空格，如果是的话就会校验失败。需要注意的是，@NotBlank 注解只能用于字符串类型的校验。

#### 💡基本步骤

步骤1：实体类属性添加校验注解 @

步骤2：handler方法(@Validated 被校验实体类对象)

**细节**

- 校验注解 对【Param】【Json】参数注解均有效果
- 如果是 【Json】参数，记得加上 **@RequestBody**

**校验结果**：如果不符合校验规则，会直接向前端抛出异常；但这里我们最好自定义异常处理⬇️

**解决方案**：接收错误绑定信息！自定义返回结果，约定：参数错误 -> {code:400} -> 前端

**如何实现**：捕捉错误绑定错误信息

	1. handler(@Validated 被校验实体类对象, **BindingResult result**) 

​	要求：**BindingResult result** 的位置必须紧挨着被校验对象！

2. 通过 **bindingResult result** 获取绑定错误！

#### 操作演示

##### handler

```java
@RestController
@RequestMapping("user")
public class UserController {

    /**
     * @Validated 代表应用校验注解! 必须添加!
     */
    @PostMapping("save")
    public Object save(@Validated @RequestBody User user, BindingResult result){ 
       //在实体类参数和 BindingResult 之间不能有任何其他参数, ⬆️ BindingResult可以接受错误信息,避免信息抛出!
      
       //判断是否有信息绑定错误! 有可以自行处理!
        if (result.hasErrors()){
            System.out.println("错误");
            String errorMsg = result.getFieldError().toString();
            return errorMsg;
        }
        //没有,正常处理业务即可
        System.out.println("正常");
        return user;
    }
}
```

##### pojo 被校验实体类

```java
@Data
public class User {
    //age   1 <=  age < = 150
    @Min(10)
    private int age;

    //name 3 <= name.length <= 6
    @Length(min = 3,max = 10)
    private String name;

    //email 邮箱格式
    @Email
    private String email;
}
```

##### 导入 校验注解 依赖

```xml
<!-- 校验注解 -->
<dependency>
    <groupId>jakarta.platform</groupId>
    <artifactId>jakarta.jakartaee-web-api</artifactId>
    <version>9.1.0</version>
    <scope>provided</scope>
</dependency>
        
<!-- 校验注解实现-->        
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.0.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator-annotation-processor -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator-annotation-processor</artifactId>
    <version>8.0.0.Final</version>
</dependency>
```





## 六、SpringMVC 总结

| 核心点               | 掌握目标                                   |
| -------------------- | ------------------------------------------ |
| springmvc框架        | 主要作用、核心组件、调用流程               |
| 简化参数接收【重点】 | 路径设计、参数接收、请求头接收、cookie接收 |
| 简化数据响应【重点】 | 模板页面、转发和重定向、JSON数据、静态资源 |
| restful风格设计      | 主要作用、具体规范、请求方式和请求参数选择 |
| 功能扩展             | 全局异常处理、拦截器、参数校验注解         |











