# Part02 - Spring6 - SpringFramework - 实战指南 - 精华总结版

> [!TIP]
>
> **Spring 核心点&掌握总结**
>
> 1. spring框架理解：spring家族和spring framework框架
> 2. spring核心功能：ioc/di , aop , tx
> 3. **spring ioc / di：组件管理、ioc容器、ioc/di , 三种配置方式**
> 4. spring aop：aop和aop框架和代理技术、基于注解的aop配置
> 5. spring tx：声明式和编程式事务、动态事务管理器、事务注解、属性
>
> 相关文章链接：
>
> - spring： [二、SpringFramework实战指南](https://www.wolai.com/oacbJpH1wPzGNoMAVnoELR)



# 一、项目总体技术体系 和 框架

### 1.1 - 单一架构

1. 特点：一个项目，一个工程，导出成一个 war 包，运行在一个 tomcat 上；
2. 项目主要应用技术框架：SSM（Spring，SpringMVC，Mybatis）

​	<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-1-1-%E5%8D%95%E4%B8%80%E6%9E%B6%E6%9E%84ssm%E5%9B%BE.png" alt="image-20240510214558919" style="width:70%;" />

### 1.2 - 分布式架构

1. 特点：eg. Idea 中，一个 project 中包含很多 modules（微服务），每个 module 内部都是一个单一架构的应用，module 之间可以相互调用。
2. 项目主要应用技术框架：SpringBoot (内部: SSM), SpringCloud , 中间件等。
   - 中间件 作用：在微服务间，提供数据缓存、传递的服务；

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-1-1-%E5%88%86%E5%B8%83%E5%BC%8F%E6%9E%B6%E6%9E%84%E5%9B%BE.png" alt="image-20240510220355212" style="width:70%;" />

### 2 - 框架（Framework） = Jar 包 + 配置文件

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-1-2-framework%E5%9B%BE.png" alt="image-20240510221125026" style="width:50%;" />

**作用**

1. 对于一些常见问题提供标准化的解决方案；

2. 降低开发人员之间的沟通成本、项目维护成本等；

3. 提高工作效率，使开发人员的重点更多的放在 **业务逻辑** 中；

   ...



# 二、Spring & Spring Framework 介绍

### 2.1 概念

> 如果 **广义Spring == 腾讯** 的话，包含：SpringFramework、SpringMVC、SpringBoot、SpringCloud、SpringData、SpringSecurity......，就像：QQ、微信、腾讯文档...... 
>
> 其中，SpringFramework 是 Spring全家桶 的基础框架，以上框架都是以 Spring Framework 为基础的。

**狭义的 Spring 即 Spring Framework。**

- Spring Framework 最初是为了解决企业级开发中各种常见问题而创建的；
- 功能：依赖注入（Dependency Injection）、面向切面编程（AOP）、声明式事务管理（TX）等；
- 主要目标是使 Java 企业级应用程序的开发变得更加简单和快速；

### 2.2 SpringFramework 主要功能模块

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-2-2-springframework%E5%8A%9F%E8%83%BD%E6%A8%A1%E5%9D%97%E5%9B%BE.png" alt="image-20240510223953883" style="width:60%;" />

| 功能模块       | 功能介绍                                                     |
| -------------- | ------------------------------------------------------------ |
| Core Container | 核心容器，在 Spring 环境下使用任何功能都必须基于 IOC 容器【IOC & DI 两大特性】 |
| AOP & Aspects  | 面向切面编程                                                 |
| TX             | 声明式事务管理                                               |
| Spring MVC     | 提供了面向Web应用程序的集成功能                              |



# 三、Spring IoC & DI 两大特性介绍

## 3.1 引入

### 1 -  什么是组件？

项目分各个层，即由各种组件搭建而成的。

- 组件是映射到应用程序中所有可重用组件的Java对象，应该是可复用的功能对象！
  - 组件一定是对象，对象不一定是组件。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-3-%E4%BB%80%E4%B9%88%E6%98%AF%E7%BB%84%E4%BB%B6.png" alt="image-20240510225241687" style="width:70%;" />



### 2 - 什么是容器？

普通容器：只用于存储，没有更多功能。eg. 数组、List、Set ......

复杂容器：

- 生活中的复杂容器：政府，政府管理我们的一生，生老病死都和政府有关；

- 程序中的复杂容器：Servlet

  ​	Servlet 容器能够管理 Servlet(init,service,destroy)、Filter、Listener 这样的组件的一生。

  | 名称       | 时机                                                         | 次数 |
  | ---------- | ------------------------------------------------------------ | ---- |
  | 创建对象   | 默认情况：接收到第一次请求  修改启动顺序后：Web应用启动过程中 | 一次 |
  | 初始化操作 | 创建对象之后                                                 | 一次 |
  | 处理请求   | 接收到请求                                                   | 多次 |
  | 销毁操作   | Web应用卸载之前                                              | 一次 |



## 3.2 Spring IoC / DI 两大特性

### 1 - **IoC（Inversion of Control）控制反转**

IoC 特性 是针对 **对象的创建和调用控制** 而言的。

不是由应用程序直接创建该对象，而是由 IoC 容器来创建和管理，即控制权由应用程序转移到 IoC 容器中，也就是“反转”了控制权。

这种方式基本上是通过依赖查找的方式来实现的，即 IoC 容器维护着构成应用程序的对象，并负责创建这些对象。

### 2 - **DI (Dependency Injection) 依赖注入**

DI 是指在 **组件之间传递依赖关系** 的过程中，将依赖关系 **在容器内部进行处理**。

- 这样就不必在应用程序代码中硬编码对象之间的依赖关系，实现了对象之间的解耦合。

- 在 Spring 中，DI 是通过 XML 配置文件或注解的方式实现的；

- 提供**三种形式**的依赖注入：构造函数注入、Setter 方法注入和接口注入。



## 3.3 Spring IoC 容器、实现

### 1 - Spring IoC 容器 - 介绍 & 工作方式

Spring IoC 容器，是一种复杂容器，不仅负责存储，更负责实例化、配置和组装 bean（组件）。

容器通过读取配置元数据来获取有关要实例化、配置和组装组件的指令。配置元数据以 XML、Java 注解或 Java 代码形式表现。允许表达组成应用程序的组件以及这些组件之间丰富的相互依赖关系。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-container%E5%9B%BE.png" alt="image-20240511185535779" style="width:50%;" />



### 2 - Spring IoC 容器 - 两大接口 & 具体实现类

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-iocontainer-interface.PNG" alt="IMG_6540" style="width:80%;" />

- **接口**

**BeanFactory** 提供了配置框架和基本功能，而 **ApplicationContext** 添加了更多特定于企业的功能。 ApplicationContext 是 BeanFactory 的完整超集！

1.  `BeanFactory` ：提供了一种高级配置机制，能够管理任何类型的对象，是SpringIoC容器标准化超接口；

2.  `ApplicationContext`

   是 `BeanFactory`的子接口，基于  `BeanFactory` 做了以下扩展：

   1. 更容易与 Spring 的 AOP 功能集成
   2. 消息资源处理（用于国际化）
   3. 特定于应用程序给予此接口实现，例如Web 应用程序的 `WebApplicationContext`；

- `ApplicationContext` 接口的 **实现类**

| 类型名                                 | 简介                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| **ClassPathXml**ApplicationContext     | 通过读取 **类路径** 下的 XML 格式的配置文件创建 IOC 容器对象 |
| **FileSystemXml**ApplicationContext    | 通过 **文件系统路径** 读取 XML 格式的配置文件创建 IOC 容器对象 |
| **AnnotationConfig**ApplicationContext | 通过读取 **Java配置类** 创建 IOC 容器对象                    |
| **Web**ApplicationContext              | 专门为 Web 应用准备，基于 **Web** 环境创建 IOC 容器对象，并将对象引入存入 ServletContext 域中。 |



### 3 -Spring IoC 容器 - 三种管理配置方式

1️⃣ XML配置、2️⃣ **注解配置**、3️⃣ **Java 配置类**（现在的开发环境中 主要使用2️⃣3️⃣：配置类 + 注解 方式）

进一步解释：

1. XML配置：XML 是 Spring **最早**的配置方式之一。在XML文件中定义Bean及其依赖关系、Bean的作用域等信息。

2. **注解方式**：@ 通过在Bean类上使用注解来代替 XML 配置文件中的配置信息。通过在 Bean 类上加上相应的注解（如@Component, @Service, @Autowired等），将 Bean 注册到 Spring IoC 容器中，这样 Spring IoC 容器就可以管理这些 Bean 之间的依赖关系。
3. **Java 配置类**：通过 Java 类来定义 Bean 、 Bean 之间的依赖关系和配置信息，从而代替 XML 配置文件的方式。Java 配置类是一种使用 Java 编写配置信息的方式，通过@Configuration、@Bean等注解来实现 Bean 和依赖关系的配置。

接下来通过一些小实验，实现这三个方式的配置。



### 4 - Spring IoC 容器 - 管理、获取、使用 Bean 组件的 3个基本步骤

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-di-%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A4.png" alt="image-20240511204216692" style="width:80%;" />

#### Step 1：配置元数据

- 基于 xml 的配置元数据的基本结构（在 xml 配置文件中）

```xml
<bean id="..." [1] class="..." [2]>  
    <!-- collaborators and configuration for this bean go here -->
</bean>
```

​	其中：**<bean> 标签 == 组件信息声明**

  		1. `id` 属性是标识单个 Bean 定义的字符串。
  		2. `class` 属性定义 Bean 的类型并使用完全限定的类名。

#### Step 2：实例化 IoC 容器

提供给 ApplicationContext 构造函数的位置路径是资源字符串地址，允许容器从各种外部资源（如本地文件系统、Java CLASSPATH 等）加载配置元数据。

应该选择一个合适的容器实现类，进行IoC容器的实例化工作：

```java
// 实例化 ioc 容器，读取外部 xml 配置文件，最终会在容器内进行 ioc 和 di 动作
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

#### Step 3：获取组件 Bean

`ApplicationContext` 是一个高级工厂的接口，能够维护不同 bean 及其依赖项的注册表。

通过使用方法 `T getBean(String name, Class<T> requiredType)` ，您可以检索 bean 的实例。

- 允许读取 Bean 定义并访问它们，如以下示例所示：

```Java
// 创建ioc容器对象，指定配置文件，ioc也开始实例组件对象
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// **通过指定的 xml 配置文件，获取ioc容器中的组件对象：这里是 id == petStore, class == PetStoreService.class
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// 正常使用组件对象
List<String> userList = service.getUsernameList();
```



## 3.4 Sping IoC 实战

接下来通过实战 三种配置方式 的一系列小实验，更深层的感受一下。

这里只会在小标题下补充相应的理论，具体操作细节请看：https://www.wolai.com/oacbJpH1wPzGNoMAVnoELR，我实现的代码仓库请看：https://github.com/boyan-uni/ssm-project

写在前面：会为每个案例维护一个程序，依赖都差不多，所以 新建一个父工程，统一维护依赖管理。

### 3.4.1- XML 配置方式管理 Bean

1. Bean 组件 - IoC - 信息声明配置
2. Bean 组件 - DI - 依赖注入配置
3. IoC 容器创建和使用
4. 高级特性 - Bean 作用域 & 周期方法 配置
5. 高级特性 - FactoryBean 特性和使用
6. 基于 XML 方式整合三层架构的组件

### 3.4.2 - 注解方式管理 Bean

1. Bean 注解标记和扫描（IoC）
2. Bean 组件作用域和周期方法注解
3. Bean 属性赋值：引用类型自动装配（DI）
4. Bean 属性赋值：基本类型属性赋值（DI）
5. 基于注解 + XML 方式整合三层架构组件

### 3.4.3 - 配置类方式管理 Bean

*. 完全 注解开发 理解

1. 配置类和扫描注解
2. @Bean 定义组件
3. 高级特性：@Bean 注解细节
4. 高级特性：@Import 扩展
5. 基于注解 + 配置类方式整合三层架构组件



### 3.4.4 - 三种配置方式总结

#### 配置方式1 - XML

1. 所有内容写到 xml 格式配置文件中
2. 声明 bean 通过 <bean 标签
3. <bean 标签包含基本信息（id,class）和属性信息 <property name value / ref
4. 引入外部的 properties 文件可以通过 <context:property-placeholder
5. IoC 具体容器实现选择 ClassPathXmlApplicationContext 对象

#### 配置方式2 - XML + 注解

1. 注解负责标记 IoC 的类和进行属性装配
2. xml 文件依然需要，需要通过 <context:component-scan 标签指定注解范围
3. 标记 IoC 注解：@Component, @Service, @Controller, @Repository 
4. 标记 DI 注解：@Autowired @Qualifier @Resource @Value
5. IoC 具体容器实现选择 ClassPathXmlApplicationContext 对象

#### 配置方式3 - 完全注解

1. 完全注解方式指的是去掉 xml 文件，使用配置类 + 注解实现
2. xml 文件替换成使用 @Configuration 注解标记的类
3. 标记 IoC 注解：@Component, @Service, @Controller, @Repository 
4. 标记 DI 注解：@Autowired @Qualifier @Resource @Value
5. <context:component-scan 标签指定注解范围使用 @ComponentScan(basePackages = {"com.atguigu.components"}) 替代
6. <context:property-placeholder 引入外部配置文件使用@PropertySource({"classpath:application.properties","classpath:jdbc.properties"})替代
7. <bean 标签使用 @Bean 注解和方法实现
8. IoC 具体容器实现选择 AnnotationConfigApplicationContext 对象



### 3.4.5 - 整合 Spring-Test & JUnit5 搭建测试环境

**a. 好处**

​	1. 不需要自己创建 IoC 对象，

​	2. 任何需要的 bean 都可以在测试类中直接享受装配；

**b. 测试集成**

​	Spring 5的测试模块（通常称为Spring Test）可以与JUnit一起使用，以及Spring Test与JUnit集成得非常好。

**c. 使用**

通常，会使用Spring的 `@RunWith `注解将 JUnit 运行器与 Spring 的 `SpringJUnit4ClassRunner` 或 `SpringRunner`（Spring 5引入的）一起使用，以便在测试期间启动 Spring 容器，并从容器中获取需要的 bean。此外，你还可以使用 Spring 的各种注解（如`@Autowired`、`@ContextConfiguration`等）来配置测试环境和依赖项注入。

**d. 相关依赖导入**

 ```xml
 <!--junit5测试-->
 <dependency>
     <groupId>org.junit.jupiter</groupId>
     <artifactId>junit-jupiter-api</artifactId>
     <version>5.3.1</version>
 </dependency>
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-test</artifactId>
     <version>6.0.6</version>
     <scope>test</scope>
 </dependency>
 ```

**e. 整合测试注解使用**

```java
//@SpringJUnitConfig(locations = {"classpath:spring-context.xml"})  //指定配置文件xml
@SpringJUnitConfig(value = {BeanConfig.class})  //指定配置类
public class Junit5IntegrationTest {
    
    @Autowired
    private User user;
    
    @Test
    public void testJunit5() {
        System.out.println(user);
    }
}
```











































































































