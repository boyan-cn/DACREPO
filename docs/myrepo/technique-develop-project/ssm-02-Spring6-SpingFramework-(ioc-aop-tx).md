

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
| **FileSystemXml**ApplicationContext    | 通过 **文件系统路径** 读取 XML 格式的配置文件创建 IOC 容器对象，eg.C/D盘中的 xml 配置文件 |
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

> [!NOTE]
>
> 接下来通过实战 三种配置方式 的一系列小实验，更深层的感受一下。
>
> 这里只会在小标题下补充相应的理论。
>
> - 具体操作细节请看：https://www.wolai.com/oacbJpH1wPzGNoMAVnoELR
>
> - 我实现的代码仓库请看：https://github.com/boyan-uni/ssm-project
>
> 写在前面：会为每个案例维护一个程序，依赖都差不多，所以 新建一个父工程 ssm-spring-part，将依赖统一配置在父工程的pom.xml 文件中，对依赖进行统一维护和管理。



####  * 补充：不同的实例化方式的对象和组件 IoC/DI 配置方式不同

Q：如果不使用 Spring - DI 特性，开发人员一般如何实例化对象？不考虑反射

A：构造函数 + 工厂模式，进一步细分为：

	- 构造函数 - 无参 - IoC 配置
	- 构造函数 - 有参 - IoC / DI 配置
	- 工厂模式 - 静态工厂 - IoC 配置
	- 工厂模式 - 动态工厂 - IoC 配置

⚠️：不同的实例化方式 的 对象和组件，IoC / DI 的配置方式也有区别。



### 3.4.1- XML 配置方式管理 Bean

#### 3.4.1.1 Bean 组件 - IoC - 信息声明配置

1. 快速创建一个 spring xml 配置文件，而不是手动：

   在导入 spring(spring-context) 依赖后，右键 resource，New，XMLConfigurationFile，SpringConfig；

2. Java 组件类（详见源码）

3. 创建 Bean：

```xml
<!-- 实验一 [重要]创建bean - 不同的实例化方式 的 对象和组件，IoC 的配置方式 -->

    <!-- 1. 基于无参数构造函数 - IoC 配置-->
    <bean id="happyComponent1" class="com.boyan.ioc_01.HappyComponent"/>
    <bean id="happyComponent2" class="com.boyan.ioc_01.HappyComponent"/>
    <!--
        - bean标签：通过配置bean标签告诉IOC容器需要创建对象的组件信息
        - id属性：bean的唯一标识,方便后期获取Bean！
        - class属性：组件类的全限定符！
        - 注意：要求当前组件类必须包含 无参数构造函数！
    -->


    <!-- 2. 基于静态工厂方法实例化 - IoC 配置-->
    <bean id="clientService" class="com.boyan.ioc_01.ClientService" factory-method="createInstance"/>
    <!--- factory-method: 指定静态工厂方法，注意，该方法必须是static方法。 -->


    <!-- 3. 基于实例工厂方法实例化 - IoC 配置-->
    <!-- 1️⃣将工厂类进行ioc配置 -->
    <bean id="serviceLocator" class="com.boyan.ioc_01.DefaultServiceLocator">
    </bean>
    <!-- 2️⃣根据指定的 工厂对象 的实例工厂方法 进行实例化组件对象 -->
    <bean id="clientService2" factory-bean="serviceLocator" factory-method="createClientServiceInstance"/>
```

3. 图解IoC配置流程

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-IoC%E9%85%8D%E7%BD%AE%E6%B5%81%E7%A8%8B.png" alt="image-20240514124405724" style="width:99%;" />

  - **单例**：是一种设计模式，其核心思想是确保一个类只有一个实例，并提供一个全局访问点来访问该实例。

    单例模式通常用于以下情况：

    1. **资源共享**: 当系统中的多个部分需要共享一个对象时，单例模式可以确保只创建一个对象，并且所有部分都引用同一个实例，从而节省资源和确保一致性。
    2. **全局访问**: 单例模式提供了一个全局访问点，使得任何地方都可以方便地访问单例实例，而不需要通过参数传递或者显式创建实例。
    3. **惰性初始化**: 单例模式可以延迟对象的创建，直到第一次使用时才进行初始化，从而提高性能和节省资源。

    实现单例模式的方法有多种，其中最常见的是使用一个私有的静态成员变量来存储实例，并提供一个公有的静态方法来获取该实例。通常情况下，构造函数被设置为私有的，以防止外部直接实例化对象

    

#### 3.4.1.2 Bean 组件 - DI - 依赖注入配置

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-di%E9%85%8D%E7%BD%AE%E6%80%9D%E8%B7%AF.png" alt="image-20240514163428268" style="width:50%;" />

```xml
<!-- 实验2: Bean 组件 - DI - 依赖注入配置 -->

    <!-- 1. 基于构造函数的依赖注入（单个构造参数）- DI 配置-->
    <bean name="userDao" class="com.boyan.ioc_02.UserDao"/>
    <bean name="userService1" class="com.boyan.ioc_02.UserService">
        <constructor-arg ref="userDao"/>
    </bean>
    <!-- - 1）引用和被引用的组件都要在 spring 配置文件中声明
           2）引用和被引用的组件，声明顺序不重要，没有先后。因为 spring ioc container 是一个高级容器，内部会有缓存动作
           3）不管什么类型，值都是字符串形式，直接类型： value = ""，引用类型：ref = “被引用对象 id ，即 bean 的名称 
            -->

    <!-- 2. 基于构造函数的依赖注入（多个构造参数）- DI 配置 - 3种配置方法-->
    <!-- 2-1. 按顺序直接赋值 -->
    <bean name="userService21" class="com.boyan.ioc_02.UserService">
        <constructor-arg ref="userDao"/>
        <constructor-arg value="boyan"/>
        <constructor-arg value="18"/>
    </bean>
    <!-- 2-2. 按参数 name 直接赋值，不用考虑顺序 * 最推荐 -->
    <bean name="userService22" class="com.boyan.ioc_02.UserService">
        <constructor-arg name="userDao" ref="userDao"/>
        <constructor-arg name="name" value="boyan"/>
        <constructor-arg name="age" value="18"/>
    </bean>
    <!-- 2-3. 按 UserDao userDao, String name, int age 参数列表，index 从左到右 从 0 开始 -->
    <bean name="userService23" class="com.boyan.ioc_02.UserService">
        <constructor-arg index="0" ref="userDao"/>
        <constructor-arg index="1" value="boyan"/>
        <constructor-arg index="2" value="18"/>
    </bean>

    <!-- 3. 基于 Setter 方法 依赖注入（一个类是另一个的属性，使用 setter 方法注入）- DI 配置-->
    <bean name="movieFinder" class="com.boyan.ioc_02.MovieFinder"/>
    <bean name="simpleMovieLister" class="com.boyan.ioc_02.SimpleMovieLister">
        <property name="movieFinder" ref="movieFinder"/>
        <property name="movieName" value="消失的她（泰国版）"/>
        <!-- property 的 name 字段实际配置的是去掉set，首字母小写后的 方法名：setMovieFinder -> name = movieFinder -->
    </bean>
```



#### 3.4.1.3 IoC 容器创建和使用

Spring IoC 容器如何 获取配置文件，并加载容器中的对象：1）容器实例化，2种； 2）Bean对象读取，3种方法；

```java
public class SpringIoCTest {

    /**
     * 1. 讲解 如何创建 ioc 容器并且读取配置文件
     * -  创建容器，选择合适的容器创建即可：ApplicationContext -> ClassPathXMLApplicationContext
     */
    @Test
    public void createIoCContainer() {
        // 1-1 方法1 * 最推荐
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-03.xml");
        // 传入一个或多个配置文件，位置：resources/spring-bean-03.xml

        // 1-2 方法2 先实例化，再指定配置文件，最后刷新容器触发Bean实例化动作 [springmvc源码和contextLoadListener源码方式]
        ClassPathXmlApplicationContext context2 = new ClassPathXmlApplicationContext();
        context2.setConfigLocation("spring-bean-03.xml");   // 外部配置文件的设置
        context2.refresh();                                 // 调用 ioc 和 di 的流程
    }

    /**
     * 2. 讲解 如何在 ioc 容器中获取组件 Bean 对象
     */
    @Test
    public void getBeanFromIoC() {
        // 1. 创建 IoC Container 对象
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-03.xml");

        // 2. 读取 IoC Container 的 Bean 组件
        // - 2-1 方法1. 通过 Bean 的 id 获取（不推荐，需要强制类型转换）
        HappyComponent happyComponent1 = (HappyComponent) context.getBean("happyComponent");
        // - 2-2 方法2. 通过 Bean 的 id 和 类型 获取（仍然需要写name，有点麻烦）
        HappyComponent happyComponent2 = context.getBean("happyComponent", HappyComponent.class);
        // - 2-3 方法3. 通过 Bean 的 类型 获取（最简化）
        HappyComponent happyComponent3 = context.getBean(HappyComponent.class);
        // - ⚠️注意：第三种可能出现问题：根据 Bean 的类型获取，如果容器中存在多个相同类型的 Bean，则获取失败；
        //          会抛出异常：org.springframework.beans.factory.NoUniqueBeanDefinitionException
        // - ⚠️注意：IoC 的 bean 配置一定是实现类，但是可以在使用时根据接口类型获取值
        //          只要 "getBean(类型); instanceOf ioc容器的类型 == true“ 即可；
        //          eg. 接口类型 happyComponent3 = context.getBean(接口类型.class); 是可以的！

        // 测试
        happyComponent3.doWork();
        System.out.println(happyComponent1 == happyComponent2);
        System.out.println(happyComponent1 == happyComponent3);
    }
}

```



#### 3.4.1.4 高级特性 - Bean 周期方法 & 作用域 配置

##### 1 - 周期方法：init-method & destory-method

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-bean-%E5%91%A8%E6%9C%9F%E6%96%B9%E6%B3%95.png" alt="image-20240515162705736" style="width:80%;" />

1 - 准备组件类

```java
public class JavaBean {

    /**
     * 必须是 public，必须是 void 返回值，必须是无参数！
     * 命名随意！
     * 初始化方法 -> 在函数中编写初始化业务逻辑即可
     */
    public void init()
    {
        // 初始化逻辑
        System.out.println("JavaBean.init");
    }

    /**
     * 销毁方法
     */
    public void destroy()
    {
        // 释放资源逻辑
        System.out.println("JavaBean.destroy");
    }
}

```

2 - 编写 spring-bean-04.xml 配置文件

```xml
<!--
    init-method = "初始化方法名“
    destroy-method = “销毁方法名”
    ：IoC容器会在对应的时间节点调用对应的方法，我们在其中写对应的业务就可以啦！
    ：eg. 正常结束：IoC容器名.close(); IoC 容器会在关闭前通知所有容器 destory;
-->
<bean id="javaBean" class="com.boyan.ioc_04.JavaBean" init-method="init" destroy-method="destroy"/>
```

3 - 编写测试代码

```java
/**
 * 实验4.1 测试 Bean 周期方法 配置
 */
@Test
public void testBeanLifeCycle() {
    // 1.创建 IoC 容器：容器创建时，会自动调用 Bean 的初始化方法
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-04.xml");

    // 2.正常结束 IoC 容器：容器关闭时，会自动调用 Bean 的销毁方法
    context.close();
}
```

​     运行结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-bean-%E5%91%A8%E6%9C%9F%E6%96%B9%E6%B3%95%E6%B5%8B%E8%AF%95.png" alt="image-20240515164826310" style="width:90%;" />

##### 2 - 作用域

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-bean%E4%BD%9C%E7%94%A8%E5%9F%9F%E5%9B%BE.png" alt="image-20240515161711300" style="width:80%;" />

1 - 准备组件类

```java
public class JavaBean2 { }
```

2 - 编写 spring-bean-04.xml 配置文件

```xml
<!-- 声明一个组件信息，默认就是单例模式 -->
<bean id="javaBean2" class="com.boyan.ioc_04.JavaBean2" scope="singleton"/>
<bean id="javaBean3" class="com.boyan.ioc_04.JavaBean2" scope="prototype"/>
```

3 - 编写测试代码

```java
/**
 * 实验4.2 测试 Bean 作用域 配置
 */
@Test
public void testBeanScope() {
    // 创建 IoC 容器：容器创建时，会自动调用 Bean 的初始化方法
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-04.xml");

    // scope = “singleten” 单例模式：IoC容器中始终是这一个对象
    JavaBean2 bean1 = context.getBean("javaBean2",JavaBean2.class);
    JavaBean2 bean2 = context.getBean("javaBean2",JavaBean2.class);
    System.out.println(bean1 == bean2);

    // scope = “prototype” 多例模式：外部每获取一次创建一个对象
    JavaBean2 bean3 = context.getBean("javaBean3",JavaBean2.class);
    JavaBean2 bean4 = context.getBean("javaBean3",JavaBean2.class);
    System.out.println(bean3 != bean4);

    // 正常结束 IoC 容器：容器关闭时，会自动调用 Bean 的销毁方法
    context.close();
}
```

​	运行结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-bean%E4%BD%9C%E7%94%A8%E5%9F%9F-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240515175957768" style="width:90%;" />

##### * - 总结 Bean 配置的主要属性

1. id = “唯一标识”，通常将组件类名第一个字母小写；

2. class = “类url”，eg. com.boyan.ioc_04.JavaBean;

3. init-method = "初始化方法名"，destory-method = "销毁方法名"

   必须是 public，必须是 void 返回值，必须是无参数！命名随意！编写初始化逻辑 和 资源释放逻辑。

4. scope = “singleten / prototype”：推荐默认值 singleten，推荐多线程在资源池管理，而不是使用多例模式；



#### 3.4.1.5 高级特性 - FactoryBean 特性和使用

一起复盘一下实例化对象的几种方式：

​	1️⃣ 通过构造函数：无参数/有参数

​	2️⃣ 通过工厂模式：静态工厂/实例工厂（非静态）

​	3️⃣ FactoryBean 标准化工厂（新增）：简化两种工厂模式

##### 1 - 什么是 FactoryBean

`FactoryBean` 是 Spring 框架中的一个特殊接口，允许开发者自定义实例的创建逻辑。通过实现 `FactoryBean` 接口，你可以创建复杂的对象，甚至可以决定在 Spring 容器中返回什么样的对象。与普通的 Spring bean 不同，`FactoryBean` 本身不是直接注册到容器中的 bean，而是它创建的对象会被注册。

> [!NOTE]
>
> #####  FactoryBean 和 BeanFactory 的区别
>
> **FactoryBean **是 Spring 中一种特殊的 bean，可以在 getObject() 工厂方法自定义的逻辑创建Bean！是一种能够生产其他 Bean 的 Bean。FactoryBean 在容器启动时被创建，而在实际使用时则是通过调用 getObject() 方法来得到其所生产的 Bean。因此，FactoryBean 可以自定义任何所需的初始化逻辑，生产出一些定制化的 bean。
>
> 一般情况下，整合第三方框架，都是通过定义FactoryBean实现！！！
>
> **BeanFactory** 是 Spring 框架的基础，其作为一个顶级接口定义了容器的基本行为，例如管理 bean 的生命周期、配置文件的加载和解析、bean 的装配和依赖注入等。BeanFactory 接口提供了访问 bean 的方式，例如 getBean() 方法获取指定的 bean 实例。它可以从不同的来源（例如 Mysql 数据库、XML 文件、Java 配置类等）获取 bean 定义，并将其转换为 bean 实例。同时，BeanFactory 还包含很多子类（例如，ApplicationContext 接口）提供了额外的强大功能。
>
> 总的来说，FactoryBean 和 BeanFactory 的区别主要在于前者是用于创建 bean 的接口，它提供了更加灵活的初始化定制功能，而后者是用于管理 bean 的框架基础接口，提供了基本的容器功能和 bean 生命周期管理。

##### 2 - 使用场景

1. **复杂对象创建**：当对象的创建过程非常复杂，可能涉及到多步骤的初始化、复杂的配置或者依赖注入时
2. **代理对象**：当你需要返回一个代理对象（例如，使用 AOP 动态代理）而不是实际的对象时
3. **延迟实例化**：你可以控制对象的创建时机，避免在容器启动时立即创建所有对象。

##### 3 - 实现方法

1. **`getObject()`**：返回由这个工厂创建的对象实例。需要 @Override 重写，在这个函数中编写创建对象的逻辑。
2. **`getObjectType()`**：返回这个工厂创建的对象类型。需要 @Override 重写。
3. **`isSingleton()`**：指示由这个工厂创建的对象是否是单例。一般使用默认值单例，则不需要 @Override 重写。

##### 4 - 实验代码

 **a. 准备 JavaBean 和 JavaBeanFactory 实现类**

- JavaBean.java

```java
public class JavaBean {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

- JavaBeanFactory.java

```java
import org.springframework.beans.factory.FactoryBean;

public class JavaBeanFactory implements FactoryBean {

    // 通过配置文件 给 javaBean 的 name 属性赋值 的中间桥接
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    // 重写 FactoryBean 中的方法（一共 3 个）
    @Override
    public Object getObject() throws Exception {
        // getObject(): 使用自己的方式实例化对象就可以啦
        JavaBean javaBean = new JavaBean();
        javaBean.setName(name);
        return javaBean;
    }

    @Override
    public Class<?> getObjectType() {
        // getObjectType: 返回对象的类型
        return JavaBean.class;
    }
        // isSingleton(): 是否单例：默认单例，不用重写。如果是多例，需要重写。
}
```

**b. spring-bean-05.xml 配置文件**

```xml
<!--
    id -> getObject 方法返回的对象的标识
    class -> 实现 factoryBean 接口的标准化工厂类
  -->
<bean id="javaBean" class="com.boyan.ioc_05.JavaBeanFactory">
    <!-- 此处配置的赋值，是给 JavaBeanFactory类 的 value 属性赋值，做一个中间桥接，外界对 javaBeanFactory 赋值，javaBeanFactory 内部给 javaBean 赋值-->
    <property name="name" value="hello world"/>
</bean>
<!-- JavaBean 和 JavaBeanFactory 都在 IoC容器 中，JavaBeanFactory 的 id：&id，这里是 &javaBean-->
```

**c. @Test 测试**

```java
/**
 * 实验5. 读取使用 factoryBean 工厂配置的 Bean 组件对象
 */
@Test
public void testFactoryBean() {
    // 创建 IoC 容器：容器创建时，会自动调用 Bean 的初始化方法
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-05.xml");

    // 读取组件
    // 1 - 读取 javaBean 对象
    JavaBean javaBean = context.getBean("javaBean",JavaBean.class);
    System.out.println(javaBean);
    System.out.println("javaBean.getName() = " + javaBean.getName());

    // 2 - 读取 javaBeanFactory 对象
    Object javaBeanFactory = context.getBean("&javaBean");
    System.out.println(javaBeanFactory);

    // 正常结束 IoC 容器：容器关闭时，会自动调用 Bean 的销毁方法
    context.close();
}
```

- 运行结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-FactoryBean-%E6%B5%8B%E8%AF%95.png" alt="image-20240516160144355" style="width:90%;" />



#### 3.4.1.6 XML整合实战：基于 XML 方式整合三层架构的组件

##### 1 - 持久层框架：JdbcTemplate + DruidDataSource 使用讲解

- maven 依赖配置

```xml
<!-- 数据库驱动和连接池-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>${druid.version}</version>
</dependency>

<!-- spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring-jdbc.version}</version>
</dependency>
```

- 最基本配置方式

```java
@Test
public void testForJava() {
    /**
     * JdbcTemplate ： 简化数据库的 crud 操作，不提供连接池
     * DuridDataSource ： 负责连接的创建和数据库驱动的注册等等
     */

    // 0. 创建一个连接池对象
    DruidDataSource dataSource = new DruidDataSource();
    dataSource.setUrl("jdbc:mysql://localhost:3306/studb");
    dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
    dataSource.setUsername("root");
    dataSource.setPassword("12345678");

    // 1. 实例化对象
    JdbcTemplate jdbcTemplate = new JdbcTemplate();
    jdbcTemplate.setDataSource(dataSource);

    // 2. 调用方法
    // jdbcTemplate.update()           DDL DML DCL ... 非查询语句
    // jdbcTemplate.queryForObject()   DQL 查询单个对象
    // jdbcTemplate.query()            DQL 查询集合

}
```

Java配置使用思路 -> IoC配置思路（对应的 spring-01.xml Bean配置）

```xml
<!-- 将 Java 实例化方式 -> IoC 实例化方式 -->
<bean id="datasource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="url" value="jdbc:mysql://localhost:3306/studb"/>
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="username" value="root"/>
    <property name="password" value="12345678"/>
</bean>

<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="datasource"/>
</bean>
```

- 改进：jdbc.properties 把数据库连接配置转移到专门的配置文件，通过读取外部配置文件配置

  spring-01.xml Bean配置

  ```xml
  <!-- 读取外部配置文件, 只限 [.properties] 文件 => value = "${key}" -->
  <context:property-placeholder location="classpath:jdbc.properties"/>
  
  <!-- 将 Java 实例化方式 -> IoC 实例化方式 -->
  <bean id="datasource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="url" value="${boyan.url}"/>
      <property name="driverClassName" value="${boyan.driver}"/>
      <property name="username" value="${boyan.username}"/>
      <property name="password" value="${boyan.password}"/>
  </bean>
  
  <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
      <property name="dataSource" ref="datasource"/>
  </bean>
  ```

  jdbc.properties 文件

  ```properties
  boyan.url=jdbc:mysql://localhost:3306/studb
  boyan.driver=com.mysql.jdbc.Driver
  boyan.username=root
  boyan.password=12345678
  ```

​	测试 @Test

```java
// 通过 IoC 容器 读取配置的 JdbcTemplate 组件

/**
 * 使用jdbcTemplate进行DML动作
 */
@Test
public void testDML(){

    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-01.xml");

    JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);

    // 执行插入一条学员数据
    String sql = "insert into students (id,name,gender,age,class) values (?,?,?,?,?);";
    /*
        参数1: sql语句
        参数2: 可变参数,占位符的值
     */
    int rows = jdbcTemplate.update(sql, 9,"十一", "男", 18, "二年三班");

    System.out.println("rows = " + rows);

}


/**
 * 查询单条实体对象
 *   public class Student {
 *     private Integer id;
 *     private String name;
 *     private String gender;
 *     private Integer age;
 *     private String classes;
 */
@Test
public void testDQLForPojo(){

    String sql = "select id , name , age , gender , class as classes from students where id = ? ;";

    ApplicationContext applicationContext =
            new ClassPathXmlApplicationContext("spring-01.xml");

    JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);

    //根据id查询
    Student student = jdbcTemplate.queryForObject(sql,  (rs, rowNum) -> {
        //自己处理结果映射
        Student stu = new Student();
        stu.setId(rs.getInt("id"));
        stu.setName(rs.getString("name"));
        stu.setAge(rs.getInt("age"));
        stu.setGender(rs.getString("gender"));
        stu.setClasses(rs.getString("classes"));
        return stu;
    }, 2);

    System.out.println("student = " + student);
}

/**
 * 查询实体类集合
 */
@Test
public void testDQLForListPojo(){

    String sql = "select id , name , age , gender , class as classes from students  ;";

    ApplicationContext applicationContext =
            new ClassPathXmlApplicationContext("spring-01.xml");

    JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);
    /*
        query可以返回集合!
        BeanPropertyRowMapper就是封装好RowMapper的实现,要求属性名和列名相同即可
     */
    List<Student> studentList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(Student.class));

    System.out.println("studentList = " + studentList);
}
```

​	运行测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-xml-practice-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240516232405021" style="width:99%;" />

------

##### 2 - 实战整合部分

- 需求分析

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-xml-%E6%95%B4%E5%90%88%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84%E5%AE%9E%E6%88%98-%E6%9E%B6%E6%9E%84%E5%9B%BE.png" alt="image-20240516165238069" style="width:80%;" />

- 具体代码详见仓库内源码；
- 运行测试整合结果：如图，成功打印全部 studentlist<Student>⬇️

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-xml%E6%95%B4%E5%90%88%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84%E5%AE%9E%E6%88%98-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240517004750555" style="width:99%;" />

##### 3 - XML IoC 方式 问题总结

1. 注入的属性必须添加setter方法、代码结构乱！
2. 配置文件和Java代码分离、编写不是很方便！
3. XML配置文件解析效率低

------

### 3.4.2 - 注解方式管理 Bean



#### 3.4.2.1 Bean 注解标记和扫描（IoC）

##### 1 - 注解 理解

和 XML 配置文件一样，注解本身并不能执行，注解本身仅仅只是做一个标记，具体的功能是框架检测到注解标记的位置，然后针对这个位置按照注解标记的功能来执行具体操作。

- **本质上**：所有一切的操作都是 Java 代码来完成的，XML 和注解只是告诉框架中的 Java 代码如何执行。

- **扫描**：Spring 为了知道程序员在哪些地方标记了什么注解，就需要通过扫描的方式，来进行检测。然后根据注解进行后续操作。

##### 2 - 组件标记注解和区别

| 注解        | 说明                                                         | 等价                                                         |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| @Component  | 该注解用于描述 Spring 中的 Bean，它是一个泛化的概念，仅仅表示容器中的一个组件（Bean），并且可以作用在应用的任何层次，例如 Service 层、Dao 层等。 使用时只需将该注解标注在相应类上即可。 | <bean id="被注解类名第一个字母小写" class="被注解的类的路径"/> |
| @Repository | 该注解用于将**数据访问层**（**Dao 层**）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |                                                              |
| @Service    | 该注解通常作用在**业务层**（**Service 层**），用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |                                                              |
| @Controller | 该注解通常作用在**控制层**（**如SpringMVC 的 Controller**），用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |                                                              |

Spring 提供了以下多个注解，这些注解可以直接标注在 Java 类上，将它们定义成 Spring Bean。

通过查看源码得知，@Controller、@Service、@Repository 这三个注解只是在 @Component 注解的基础上起了三个新的名字。

对于 Spring 使用 IOC 容器管理这些组件来说没有区别，也就是语法层面没有区别。所以 @Controller、@Service、@Repository 这三个注解只是给开发人员看的，让我们能够便于分辨组件的作用。

注意：虽然它们本质上一样，但是为了代码的可读性、程序结构严谨！我们肯定不能随便胡乱标记。

##### 3 - 使用逻辑

1. 在类上添加 @注解；
2. 在 XML 配置文件 中配置 **注解生效包** 的信息：告诉 Spring IoC Container（容器），开发人员在哪些包下添加了注解，IoC 容器会自动扫描该 package 下的注解，从而识别 Bean 组件类信息；

##### 4 - *组件 BeanName 的问题

1. 默认情况：@Controller

   类名首字母小写就是 bean 的 id。例如：SoldierController 类对应的 bean 的 id 就是 soldierController。

2. 使用value属性指定：@Controller(value = "smallDog")

​	此时被注解的组件类的IoC识别的 BeanName 是 "smallDog"。

##### 5 - 注解@ + XML配置文件

2 中的注解可以直接标注在 Java 类上，将它们定义成 Spring Bean；

**XML 文件 配置语法**

```xml
<!-- 在spring配置文件中，使用context命名空间，并开启组件扫描功能，配置注解生效包信息 -->

    <!-- 情况1. 普通配置包扫描 * （最基本的） -->
    <!-- base-package 指定 IoC Container 去哪些包下查找注解类
         1.包要精准,提高性能! 会扫描指定的包和子包内容，指定包，相当于指定包中所有类；
         2.多个包 用逗号 , 分割 例如: com.boyan.controller,com.boyan.service等；
    -->
    <context:component-scan base-package="com.boyan.ioc_01"/>

    <!-- 情况2. 不扫描 指定的组件 -->
    <context:component-scan base-package="com.boyan.ioc_01">
        <!-- 扫描 指定 package，排除 Controller 组件 -->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
             <!-- context:exclude-filter标签：指定排除规则 -->
             <!-- type属性：指定根据什么来进行排除，annotation取值表示根据注解来排除 -->
             <!-- expression属性：指定排除规则的表达式，对于注解来说指定全类名即可 -->
    </context:component-scan>

    <!-- 情况3. 仅扫描 指定的组件 -->
    <context:component-scan base-package="com.boyan.ioc_01" use-default-filters="false">
        <!-- 仅扫描 = 关闭默认规则 + 追加规则 -->
            <!-- base-package 属性：该包下所有注解都生效 -->
            <!-- use-default-filters 属性：取值false表示关闭默认扫描规则 -->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 只扫描这个类型⬆️ -->
    </context:component-scan>

```

测试

```java
/**
 * XML + @Annotation 配置
  */

/**
 * IoC_01：测试 Bean IoC 配置
 * - 组件配置：@Component @Controller @Service @Repository
 */
@Test
public void testIoc_01() {
    // 1. 创建 IoC 容器
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-01.xml");

    // 2. 获取 Bean
    System.out.println(applicationContext.getBean(XxxController.class)); // @Controller
    System.out.println(applicationContext.getBean("xxxController"));
    System.out.println(applicationContext.getBean(XxxService.class));		 // @Service
    System.out.println(applicationContext.getBean("xxxService"));
    System.out.println(applicationContext.getBean(XxxDao.class));				 // @Dao
    System.out.println(applicationContext.getBean("xxxDao"));

    // 3. close IoC 容器
    applicationContext.close();
}
```

运行测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-annotation-ioc01-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240517150229568" style="width:99%;" />



#### 3.4.2.2 Bean 组件 作用域和周期方法注解

0. 注解
   - 周期方法 @PostConstruct @PreDestroy
   - 作用域 @Scope(scopeName = ConfigrableBeanFactory.SCOPE_PROTOTYPE 多例 / SINGLETON 单例)

1. 组件类准备

```java
import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;


// @Scope(scopeName = ConfigurableBeanFactory.SCOPE_SINGLETON) 单例，默认值 - 作用域 只有单例管理 destroy
// @Scope(scopeName = ConfigurableBeanFactory.SCOPE_PROTOTYPE) 多例，二选一 - 作用域 多例组件太多不管理 destroy
@Component
public class JavaBean {

    // 周期方法：对应 XML 配置的 init-method / destroy-method
    @PostConstruct
    public void init() {
        // 初始化逻辑
        System.out.println("JavaBean init");
    }
    @PreDestroy
    public void destroy() {
        // 销毁逻辑，eg.释放资源
        System.out.println("JavaBean destroy");
    }
}
```

2. XML + @Annotation 配置文件

```xml
<!-- IoC_02. 配置bean 周期 & 作用域 -->
<context:component-scan base-package="com.boyan.ioc_02"/>
<!-- 周期：@PostConstruct、@PreDestroy 作用域：@Scope(scopeName = ConfigrableBeanFactory.SCOPE_PROTOTYPE 多例 / SINGLETON 单例)-->
```

3. 测试

```java
/**
 * IoC_02：测试 Bean 配置 周期方法 & 作用域
 * - 周期方法 @PostConstruct @PreDestroy
 * - 作用域 @Scope(scopeName = ConfigrableBeanFactory.SCOPE_PROTOTYPE 多例 / SINGLETON 单例)
 */
@Test
public void testIoc_021_CycleMethod() {
    // 1. 创建 IoC 容器: IoC 容器调用所有 Bean 的初始化方法（如果有的话）
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-02.xml");

    // 2. 获取 Bean
    System.out.println(applicationContext.getBean(JavaBean.class));
  
    // 3. close IoC 容器：IoC 容器关闭之前调用所有 Bean 的销毁方法（如果有的话）
    applicationContext.close();
}
@Test
public void testIoc_022_Scope() {
    // 1. 创建 IoC 容器
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-02.xml");

    // 2. 获取 Bean，测试逻辑：在组件类 @配置中修改 scopeName值，并重新运行
    JavaBean javaBean1 = applicationContext.getBean(JavaBean.class);
    JavaBean javaBean2 = applicationContext.getBean(JavaBean.class);
    System.out.println(javaBean1 == javaBean2);

    // 3. close IoC 容器
    applicationContext.close();
}
```



#### 3.4.2.3 Bean 属性赋值：引用类型自动装配（DI）

##### 1 - 自动装配

1. **前提**：参与自动装配的组件（需要装配、被装配）全部都必须在IoC容器中。

   注意：不区分IoC的方式！XML和注解都可以！

2. **实现**：@AutoWire + @Qualifier = @Resource（引用类型属性的自动装配） &&  @Value（引入外部文件的基本类型属性赋值）

3. **@Resource @Autowire 区别**：
   - @Resource注解：用在属性上、setter方法上。
   - @Autowired注解：用在属性上、setter方法上、构造方法上、构造方法参数上。
   - @Resource 注解：默认根据Bean名称装配，未指定name时，使用属性名作为name。通过name找不到的话会自动启动通过类型装配。
   - @Autowired 注解：默认根据类型装配，如果想根据名称装配，需要配合@Qualifier注解一起用。

##### 2 - @Autowired + @Qualifier

（一）注解标记位置

1. 成员变量：这是最主要的使用方式！（**与xml进行bean ref引用不同，这里不需要有set方法！**）

```Java
@Service("smallDog")
public class SoldierService {
    
    @Autowired
    private SoldierDao soldierDao;
    
    public void getMessage() {
        soldierDao.getMessage();
    }
}
```
2. 构造器

```Java
@Controller(value = "smallDog")
public class SoldierController {
    
    private SoldierService soldierService;
    
    @Autowired
    public SoldierController(SoldierService soldierService) {
        this.soldierService = soldierService;
    }
    ……
```
3. setXxx()方法

```Java
@Controller(value = "smallDog")
public class SoldierController {

    private SoldierService soldierService;

    @Autowired
    public void setSoldierService(SoldierService soldierService) {
        this.soldierService = soldierService;
    }
    ……
```

（二）@Autowire 佛系装配

给 @Autowired 注解设置 required = false 属性表示：能装就装，装不上就不装。但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性：

```java
@Autowired(required = false)
private ISoldierService soldierService;
```

如果一个组件佛系装配，使用它的组件会报错空指针，建议项目中的返回结果是准确可预期的，而不是“都行”。



##### 3 - @Resource

@Resource 注解使用需要额外引入以下依赖：

```XML
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>2.1.1</version>
</dependency>
```

@Resource (name="被引用组件名") **等价于** @Autowired + @Qualifier("被引用组件名")

准备组件类

```java
@Controller(value = "userController")
public class UserController {

    // @Autowired
    // @Qualifier(value = "userService") 自动装配 引用类型为 userService 的 bean
    @Resource(name = "userService")
    private UserService userService;

    public void show() {
        userService.show();
    }
}
// ---------------------------------------------------------------------------
@Service(value = "userService")
public class UserService {
    public void show() {
        System.out.println("ioc_04_show()");
    }
}
```

测试

```java
/**
 *
 * IoC_DI_03：测试 Bean 配置 DI 依赖注入
 */
@Test
public void testIoc_03_Di() {
    // 1. 创建 IoC 容器
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-03.xml");

    // 2. 获取 Bean
    UserController userController = applicationContext.getBean(UserController.class);
    userController.show();

    // 3. close IoC 容器
    applicationContext.close();
}
```

运行测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-annotation-di-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240519140919898" style="width:99%;" />



#### 3.4.2.4 Bean 属性赋值：基本类型属性赋值（DI）

##### @Value：通常用于引入外部化属性

###### 1 - 基本类型直接赋值（一般不用，推荐直接 = 赋值）

```java
@Value("19")
int age;		// 推荐直接 int age = 19;
```

###### 2 - 引用外部文件，为基本类型赋值（* 常用）

1. 声明外部配置：jdbc.properties

```Java
boyan.password=12345678
```

2. xml引入外部配置

```Java
<!-- 引入外部配置文件-->
<context:property-placeholder location="jdbc.properties" />
```

3. @Value注解读取配置

```Java
@Component
public class JavaBean {

    @Value("boyan")
    private String name;

    @Value("${boyan.password:root}")
    private String password;
  	/**
     * 情况1: ${key} 取外部配置key对应的值!
     * 情况2: ${key:defaultValue} 没有key,可以给与默认值
     */

    public String getName() {
        return name;
    }

    public String getPassword() {
        return password;
    }
}
```

4. 测试

```java
/**
 *
 * IoC_DI_04：测试 Bean 配置 DI 依赖注入
 */
@Test
public void testIoc_04_DiValue() {
    // 1. 创建 IoC 容器
    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-04.xml");

    // 2. 获取 Bean
    com.boyan.ioc_04.JavaBean javaBean = applicationContext.getBean(com.boyan.ioc_04.JavaBean.class);
    System.out.println(javaBean.getName());
    System.out.println(javaBean.getPassword());

    // 3. close IoC 容器
    applicationContext.close();
}
```

- 运行测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-annotation-di-value-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240519144200659" style="width:99%;" />



#### 3.4.2.5 注解 + XML 整合实战：基于注解 + XML 方式整合三层架构组件

**需求分析**

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-annotation-xml-%E4%B8%89%E5%B1%82%E6%95%B4%E5%90%88%E5%AE%9E%E6%88%98%E6%9E%B6%E6%9E%84.png" alt="image-20240519151543758" style="width:80%;" />

搭建一个三层架构案例，模拟查询全部学生（学生表）信息，持久层使用 JdbcTemplate 和 Druid 技术，使用XML+注解方式进行组件管理。

**Spring-bean-01.xml 配置文件**

```xml
<!-- 配置组件扫描 IoC / DI 注解-->
<context:component-scan base-package="com.boyan.dao,com.boyan.service,com.boyan.controller"/>
<!-- 导入外部属性文件 -->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!--配置引入数据库依赖：jdbcTemplate druidDataSource-->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="url" value="${boyan.url}"/>
    <property name="driverClassName" value="${boyan.driverClassName}"/>
    <property name="username" value="${boyan.username}"/>
    <property name="password" value="${boyan.password}"/>
</bean>
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="druidDataSource"/>
</bean>
```

**测试及测试结果**

```java
public class ControllerTest {
    @Test
    public void testRun() {
        // 1. 创建IOC容器
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring-bean-01.xml");
        // 2. 获取IOC容器中的bean
        StudentController studentController = context.getBean(StudentController.class);
        studentController.findAll();
        // 3. 关闭IOC容器
        context.close();
    }
}
```

运行测试结果：

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-annotation-practice-%E6%95%B4%E5%90%883%E5%B1%82%E6%9E%B6%E6%9E%84-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" style="width:99%;" />

- 成功从数据库打印全部值，具体源码请访问相应github仓库的 spring-annotation-practice-04 模块；

------

### 3.4.3 - 配置类方式管理 Bean

1. 完全注解开发 理解

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-java-annotaion-%E5%AE%8C%E5%85%A8%E9%85%8D%E7%BD%AE%E7%B1%BB%E7%90%86%E8%A7%A3%E5%9B%BE.png" alt="image-20240519231318740" style="width:70%;" />

​				- 使用 Java 配置类，完全代替 xml 配置文件

2. 配置类和扫描注解

3. @Bean 定义组件

4. 高级特性：@Bean 注解细节

5. 高级特性：@Import 扩展

**本部分重点，在实验代码和注释中梳理：**

1- 准备组件类：StudentController.java & StudentService.java

```java
@Controller
public class StudentController {

    @Autowired
    private StudentService studentService;

    public void show() {
        studentService.show();
    }
}
@Service
public class StudentService {
    public void show()
    {
        System.out.println("spring-ioc-annotation-practice-05");
    }
}
```

2 - Java配置类：JavaConfiguration.java

```java
import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.*;
import org.springframework.jdbc.core.JdbcTemplate;

/**
 * Java配置类 完全替代 xml 配置文件
 *      1. 包扫描注解配置
 *      2. 引用外部的配置文件
 *      3. 声明第三方依赖的 bean 组件
 *
 *  步骤1. 添加 @Configuration 注解，代表是配置类
 *  步骤2. 实现上面 3 个功能的注解
 *          1. 单包扫描：@ComponentScan(value = "com.boyan")
 *             多包扫描：@ComponentScan(value = {"com.boyan.service", "com.boyan.dao"})
 *          2. 引用外部的配置文件：@PropertySource(value = "classpath:jdbc.properties")
 *          3. 声明第三方依赖的 bean 组件：@Bean
 *
 *  问题：如果项目中有多个配置类，如何使用多个配置类？
 *    1 - 在创建 IoC 容器时，new AnnotationConfigApplicationContext(JavaConfiguration1.class, JavaConfiguration2.class, ...)
 *    2 - 配置类汇总机制：使用 @Import(被汇总配置类1.class) 汇总所有配置类 到 一个配置类文件中，在创建配置类时，只需导入一个汇总好的即可。
 */
@ComponentScan(value = "com.boyan")
@PropertySource(value = "classpath:jdbc.properties")
@Import(JavaConfigurationPlus.class)
@Configuration
public class JavaConfiguration {

    @Value("${boyan.driverClassName}")	// 给基本类型引入外部文件的值
    private String driverClassName;
    @Value("${boyan.url}")
    private String url;
    @Value("${boyan.username}")
    private String username;
    @Value("${boyan.password}")
    private String password;

    /**
     * XML 中的 <bean></bean> 对应的 @Bean 注解 -> 一个方法函数
     *
     * - 方法 - 返回值类型 == bean 组件的类型 / 其接口 或 父类
     * - 方法 - 方法名 == bean 组件的 id
     * - 在方法上加上 @Bean 注解 会真正让配置类的方法创建的组件 存储到 IoC 容器中
     *
     * 问题1: Bean Name 的问题
     * 1 - 默认：方法名
     * 2 - 指定：@Bean(value = "beanName") / @Bean(name = "beanName")：通过 value/name 属性起名字，覆盖默认方法
     *
     * 问题2: Bean 周期方法
     * 1 - 依然用注解：@PostConstruct / @PreDestroy
     * 2 - @Bean 的属性 initMethod/destroyMethod，eg. @Bean(initMethod = "init", destroyMethod = "destroy")
     *
     * 问题3: Bean 的作用域问题
     *   - 依然用 @Scope(scopeName = ConfigurableBeanFactory.SCOPE_PROTOTYPE)，默认单例 SCOPE_SINGLETON
     *
     * 问题4: 如何在一个 @Bean 方法中，引用 IoC 中的其他组件？
     *          1 - 直接调用被引用的组件的 @Bean 方法，返回值就是引用的组件（不推荐），要求被引用组件也被 @Bean 注解
     *          2 - 使用形参列表声明想要的组件类型，可以是 1/多个，从 IoC 容器中获取组件
     *              要求必须有对应类型的组件，如果没有 -> 抛出异常
     *                                    如果有多个 -> 可以用形参名称 = bean id 标识获取
     */
    @Bean(name = "dataSourceA")
    public DruidDataSource druidDataSourceA() {
        // 使用 Java 代码 实例化
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driverClassName);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DruidDataSource dataSourceA) { 
        // 多个，两个数据源：使用 bean id 唯一标识声明使用
        // 使用 Java 代码 实例化
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSourceA);
        return jdbcTemplate;
    }

    @Bean(name = "dataSourceB")
    public DruidDataSource druidDataSourceB() {
        // eg. 当 IoC 中根据类型有多个 Bean，使用 Bean ID 做唯一标识
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driverClassName);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }
}
```

3 - 测试（通过 Java 配置类，创建 IoC 容器）

```java
@Test
    public void test() {
        // step1. 创建 IOC 容器 Java 配置类方法创建
        //      * 方式1：
        //      *   1. 创建 IOC 容器：new AnnotationConfigApplicationContext();
        //      *   2. 添加配置类：addConfigClass(JavaConfiguration.class);
        //      *   3. 刷新容器：refresh(); 一定要刷新才会生效
        //      * 方式2：new AnnotationConfigApplicationContext(JavaConfiguration.class);
        //
        //      * 情况1. 单一配置类：new AnnotationConfigApplicationContext(JavaConfiguration.class);
        //      * 情况2. 多个配置类：new AnnotationConfigApplicationContext(JavaConfiguration.class, JavaConfiguration2.class, ...);
        //      * 结构更清晰的是将所有的配置类通过 @Import 放在一个配置类中，然后只导入这一个汇总的配置类；
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(JavaConfiguration.class);
        // step2. 获取 IOC 容器中的 bean
        StudentController studentController = annotationConfigApplicationContext.getBean(StudentController.class);
        studentController.show();
        // step3. close IOC 容器
        annotationConfigApplicationContext.close();
    }
}
```



#### 3.4.3.6 注解 + 配置类 整合实战：基于注解 + 配置类方式整合三层架构组件

**需求分析**

搭建一个三层架构案例，模拟查询全部学生（学生表）信息，持久层使用JdbcTemplate和Druid技术，使用注解+配置类方式进行组件管理！

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/image-20240520104100198.png" alt="image-20240520104100198" style="width:80%;" />

**测试结果**

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-ioc-java-annotation-practice-%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84%E6%95%B4%E5%90%88%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240520105618843" style="width:99%;" />

- 成功从数据库打印全部值，具体源码请访问相应github仓库的 spring-java-practice-06 模块；

------

### 3.4.4 - 三种配置方式总结

#### 配置方式1 - 完全XML

1. 所有内容写到 xml 格式配置文件中
2. 声明 bean 通过 <bean 标签
3. <bean 标签包含基本信息（id,class）和属性信息 <property name value / ref
4. 引入外部的 properties 文件可以通过 <context:property-placeholder
5. IoC 具体容器实现选择 **ClassPathXmlApplicationContext** 对象

#### 配置方式2 - XML + 注解

1. 注解负责标记 IoC 的类和进行属性装配
2. xml 文件依然需要，需要通过 <context:component-scan 标签指定注解范围
3. 标记 IoC 注解：@Component, @Service, @Controller, @Repository
4. 标记 DI 注解：@Autowired @Qualifier @Resource @Value
5. IoC 具体容器实现选择 **ClassPathXmlApplicationContext** 对象

#### 配置方式3 - 完全注解（现在的主流配置方式）

1. 完全注解方式指的是去掉 xml 文件，使用配置类 + 注解实现
2. xml 文件替换成使用 @Configuration 注解标记的类
3. 标记 IoC 注解：@Component, @Service, @Controller, @Repository
4. 标记 DI 注解：@Autowired @Qualifier @Resource @Value
5. <context:component-scan 标签指定注解范围使用 @ComponentScan(basePackages = {"com.atguigu.components"}) 替代
6. <context:property-placeholder 引入外部配置文件使用@PropertySource({"classpath:application.properties","classpath:jdbc.properties"})替代
7. <bean 标签使用 @Bean 注解和方法实现
8. IoC 具体容器实现选择 **AnnotationConfigApplicationContext** 对象

------

### 3.4.5 - 整合 JUnit5 & Spring-Test 搭建测试环境（通过注解，让 spring 帮我们创建 IoC 对象，替代手动创建）

**1 - 所需依赖**

 ```xml
<!-- junit5测试 -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.3.1</version>
</dependency>
<!-- spring-test 模块引入 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>6.0.6</version>
    <scope>test</scope>
</dependency>
 ```

**2 - 好处**

1. 不需要自己创建 IoC 对象，任何需要的 bean 都可以在测试类中直接享受装配；

 	2. 集成两大测试框架：Spring 5的测试模块（通常称为Spring Test）可以与JUnit一起使用，以及Spring Test与JUnit集成得非常好。

**3 - 实验代码**

- 组件类

```java
@Component
public class A {
    public void show()
    {
        System.out.println("com.boyan.A show");
    }
}
```

- Java 配置类

```java
@Configuration
@ComponentScan(basePackages = {"com.boyan"})
public class JavaConfig {

}
```

- 测试类

```java
package com.boyan.test;

import com.boyan.A;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

// @SpringJUnitConfig(locations = {"classpath:spring-context.xml"})  // 指定配置文件xml
// @SpringJUnitConfig(value = {com.boyan.config.JavaConfig.class})     // 指定配置类
public class SpringIoCTest {

    @Autowired
    private A a;	

    @Test
    public void test()
    {
        a.show();
    }
}
```

​	运行测试结果：无需手动创建 spring IoC 容器，通过注解由 spring 自动创建成功⬇️

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-test-junit-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C%E6%88%AA%E5%9B%BE.png" alt="image-20240520122632636" style="width:99%;" />

通常，会使用Spring的 `@RunWith` 注解将 JUnit 运行器与 Spring 的 `SpringJUnit4ClassRunner`  或 `SpringRunner`（Spring 5引入的）一起使用，以便在测试期间启动 Spring 容器，并从容器中获取需要的 bean。

此外，你还可以使用 Spring 的各种注解（如`@Autowired`、`@ContextConfiguration`等）来配置测试环境和依赖项注入。







> [!NOTE]
>
> #### “建立 Spring - AOP & TX 概念”
>
> ##### Spring AOP (Aspect-Oriented Programming)
>
> **AOP（面向切面编程）** 是一种编程范式，旨在提高代码的模块化程度，尤其是关注点分离（Separation of Concerns）。它允许你将那些与业务逻辑无关的功能（如日志记录、安全性、事务管理等）从核心业务逻辑中分离出来。这些功能通常被称为“横切关注点”（Cross-Cutting Concerns）。
>
> **Spring AOP** 主要用于以下场景：
>
> 1. **日志记录**：在方法调用前后记录日志。
> 2. **安全性**：检查用户是否有权限执行某个方法。
> 3. **事务管理**：在方法执行时管理事务。
> 4. **性能监控**：监控方法的执行时间。
>
> ##### 基本概念
>
> - **切面（Aspect）**：横切关注点的模块化表示。例如，一个日志记录切面。
> - **连接点（Join Point）**：程序执行过程中的一个点，例如方法调用或异常抛出。
> - **通知（Advice）**：在特定的连接点上执行的动作，如方法调用之前、之后或抛出异常时。
> - **切入点（Pointcut）**：匹配连接点的集合，定义在哪些连接点上应用通知。
> - **织入（Weaving）**：将切面应用到目标对象并创建代理对象的过程。
>
> ##### Spring TX（Transaction Management）
>
> **事务管理（TX）** 是指对事务进行管理和控制的过程。事务是一组要么全部成功要么全部失败的操作，确保数据的一致性和完整性。
>
> ##### Spring中的事务管理
>
> Spring提供了两种管理事务的方式：
>
> 1. **编程式事务管理**：你需要在代码中显式地管理事务边界（开始、提交、回滚）。
> 2. **声明式事务管理**：通过注解或XML配置管理事务，这种方式更常用且更简洁。
>
> ##### 基本注解
>
> - **@Transactional**：这是声明式事务管理中最常用的注解。你可以在类或者方法上使用它来定义事务的边界。
>
> ##### 事务属性
>
> - 传播行为（Propagation）
>
>   ：定义事务如何传播。例如，一个方法调用另一个方法时，事务是否应该传播。
>
>   - **REQUIRED**：当前方法必须在事务中执行。如果当前没有事务，则开始一个新的事务。
>   - **REQUIRES_NEW**：当前方法必须在自己的事务中执行。无论是否有现存的事务，都要启动一个新的事务。
>
> - 隔离级别（Isolation）
>
>   ：定义一个事务如何与其他事务进行隔离。常用的隔离级别有：
>
>   - **READ_COMMITTED**：一个事务只能读取另一个事务已经提交的数据。
>   - **REPEATABLE_READ**：保证一个事务在执行过程中读取到的数据是一致的，即使其他事务修改了数据。
>
> - **回滚规则（Rollback Rules）**：定义在遇到什么样的异常时事务应该回滚。
>
> ##### 总结
>
> - **AOP** 用于将日志、安全性、事务等横切关注点与核心业务逻辑分离。
> - **事务管理** 确保一组操作要么全部成功，要么全部失败，以保证数据一致性和完整性。
>
>    通过使用Spring的AOP和事务管理特性，你可以编写更模块化、可维护性更高的代码。



# 四、Spring AOP 面向切面编程

## 4.1 AOP 编程思想 & 代理模式

#### 1 - AOP 🆚 OOP

面向切面编程，是对面向对象编程的完善。

OOP(object oriented programming)：继承关系，只能完全使用 / 完全重写 父类的方法;

AOP(Aspect oriented programming)：可以进行方法的局部调整，把非核心业务代码解耦合提取出来，再通过代理技术使用。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop&oop%E7%BC%96%E7%A8%8B%E6%80%9D%E6%83%B3%E5%9B%BE.png" alt="image-20240520152716213" style="width:50%;" />

#### 2 - AOP 面向切面编程

1 - 把非核心代码解耦合

2 - 动态插入（如何实现？）-> 代理模式

- 代理模式

  - 静态代理：代码写死且量大，每一个需要代理的目标类都需要由开发人员自己写一个固定的代理类（知道有就行了）
  - 动态代理：
    - jdk 动态代理（有接口）-- java 原生技术
    - cglib 动态代理（无接口）-- 第三方，但已集成到 spring-aop 中

- **代理方式可以解决附加功能代码干扰核心代码和不方便统一维护的问题！**

  主要是将附加功能代码提取到代理中执行，不干扰目标核心代码！

  但是，我们也发现，无论使用静态代理和动态代理(jdk,cglib)，程序员的工作都比较繁琐！需要自己编写代理工厂等！

  但是，提前剧透，我们在实际开发中，不需要编写代理代码，我们可以使用[Spring AOP]框架，会简化动态代理的实现！！！

#### 3 - AOP 底层技术组成

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E5%BA%95%E5%B1%82%E6%8A%80%E6%9C%AF%E7%BB%84%E6%88%90%E5%9B%BE.png" alt="image-20240521135748546" style="width:50%;" />

- **动态代理（InvocationHandler）**：JDK原生的实现方式，需要被代理的目标类必须实现接口。因为这个技术要求代理对象和目标对象实现同样的接口（兄弟两个拜把子模式）。
- **cglib**：通过继承被代理的目标类（认干爹模式）实现代理，所以不需要目标类实现接口。
- **AspectJ**：早期的AOP实现的框架，SpringAOP借用了AspectJ中的AOP注解。

#### 4 - AOP 主要应用场景

AOP（面向切面编程）是一种编程范式，它通过将通用的横切关注点（如日志、事务、权限控制等）与业务逻辑分离，使得代码更加清晰、简洁、易于维护。AOP可以应用于各种场景，以下是一些常见的AOP应用场景：

1. 日志记录：在系统中记录日志是非常重要的，可以使用AOP来实现日志记录的功能，可以在方法执行前、执行后或异常抛出时记录日志。
2. 事务处理：在数据库操作中使用事务可以保证数据的一致性，可以使用AOP来实现事务处理的功能，可以在方法开始前开启事务，在方法执行完毕后提交或回滚事务。
3. 安全控制：在系统中包含某些需要安全控制的操作，如登录、修改密码、授权等，可以使用AOP来实现安全控制的功能。可以在方法执行前进行权限判断，如果用户没有权限，则抛出异常或转向到错误页面，以防止未经授权的访问。
4. 性能监控：在系统运行过程中，有时需要对某些方法的性能进行监控，以找到系统的瓶颈并进行优化。可以使用AOP来实现性能监控的功能，可以在方法执行前记录时间戳，在方法执行完毕后计算方法执行时间并输出到日志中。
5. 异常处理：系统中可能出现各种异常情况，如空指针异常、数据库连接异常等，可以使用AOP来实现异常处理的功能，在方法执行过程中，如果出现异常，则进行异常处理（如记录日志、发送邮件等）。
6. 缓存控制：在系统中有些数据可以缓存起来以提高访问速度，可以使用AOP来实现缓存控制的功能，可以在方法执行前查询缓存中是否有数据，如果有则返回，否则执行方法并将方法返回值存入缓存中。
7. 动态代理：AOP的实现方式之一是通过动态代理，可以代理某个类的所有方法，用于实现各种功能。

综上所述，AOP可以应用于各种场景，它的作用是将通用的横切关注点与业务逻辑分离，使得代码更加清晰、简洁、易于维护。

#### 5 - AOP 8个名词解释

1. **横切关注点**

   ​	从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横切关注点。

   ​	AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如权限认证、日志、事务、异常等。AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。

2. **通知（增强）**

   每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。

   - 前置通知：在被代理的目标方法前执行
   - 返回通知：在被代理的目标方法成功结束后执行（**寿终正寝**）
   - 异常通知：在被代理的目标方法异常结束后执行（**死于非命**）
   - 后置通知：在被代理的目标方法最终结束后执行（**盖棺定论**）
   - 环绕通知：使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置

3. **连接点 joinpoint**

​	这也是一个纯逻辑概念，不是语法定义的。指那些被拦截到的点。在 Spring 中，可以被动态代理拦截目标类的方法。

4. **切入点 pointcut**

   定位连接点的方式，或者可以理解成被选中的连接点！是一个表达式，比如execution(* com.spring.service.impl.*.*(..))。符合条件的每个方法都是一个具体的连接点。

5. **切面 aspect**：切入点和通知的结合。是一个类。

6. **目标 target**：被代理的目标对象。
7. **代理 proxy**：向目标对象应用通知之后创建的代理对象。
8. **织入 weave**：指把通知应用到目标上，生成代理对象的过程。可以在编译期织入，也可以在运行期织入，Spring采用后者。



## 4.2 Spring AOP 基于注解方式实现和细节

### 4.2.1 - 项目准备

项目模块：spring-aop-annotation-10	

- 目标：横向插入增强代码
- 需求：给计算的业务类，添加日志（log）

- **步骤**：

  - （一）IoC/DI + Test

    1. 导入依赖：IoC/DI - spring-context，AOP - spring-aspects

       ```xml
       <!-- spring-context 包含对 spring-aop 模块的 依赖传递 -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-aop</artifactId>
           <version>6.0.6</version>
       </dependency>
       <!-- spring-aspects会帮我们传递过来aspectjweaver -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-aspects</artifactId>
           <version>6.0.6</version>
       </dependency>
       ```

    2. 正常编写核心业务，加入 IoC 容器

    3. 编写 IoC 的配置类和文件

    4. 测试环境

  - （二）AOP

    5. 增强类，定义3个增强方法（存储横切关注点的代码）
    6. 增强类的配置（插入切点的位置，切点指定，切面配置等）
    7. 开启 AOP 的配置

- ⚠️ AOP 功能只针对 IoC 容器的对象。AOP 创建代理对象，并把代理对象存储到 IoC 容器。并且因为代理的原因，在代码通过类取值部分，（如果有接口）建议用接口取值，防止用类取不到对象。

### 4.2.2 - 初步实现

#### 1 - 准备接口

```Java
public interface Calculator {
    
    int add(int i, int j);
    
    int sub(int i, int j);
    
    int mul(int i, int j);
    
    int div(int i, int j);
    
}
```
#### 2 - 纯净实现类

```Java
package com.atguigu.proxy;


/**
 * 实现计算接口,单纯添加 + - * / 实现! 掺杂其他功能!
 */
@Component
public class CalculatorPureImpl implements Calculator {
    
    @Override
    public int add(int i, int j) {
    
        int result = i + j;
    
        return result;
    }
    
    @Override
    public int sub(int i, int j) {
    
        int result = i - j;
    
        return result;
    }
    
    @Override
    public int mul(int i, int j) {
    
        int result = i * j;
    
        return result;
    }
    
    @Override
    public int div(int i, int j) {
    
        int result = i / j;
    
        return result;
    }
}
```
#### 3 - 声明切面类

```Java
package com.atguigu.advice;

import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

// @Aspect表示这个类是一个切面类
@Aspect
// @Component注解保证这个切面类能够放入IOC容器
@Component
public class LogAspect {
        
    // @Before注解：声明当前方法是前置通知方法
    // value属性：指定切入点表达式，由切入点表达式控制当前通知方法要作用在哪一个目标方法上
    @Before(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogBeforeCore() {
        System.out.println("[AOP前置通知] 方法开始了");
    }
    
    @AfterReturning(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogAfterSuccess() {
        System.out.println("[AOP返回通知] 方法成功返回了");
    }
    
    @AfterThrowing(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogAfterException() {
        System.out.println("[AOP异常通知] 方法抛异常了");
    }
    
    @After(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogFinallyEnd() {
        System.out.println("[AOP后置通知] 方法最终结束了");
    }
    
}
```
#### 4 - 开启aspectj注解支持

1. xml方式

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 进行包扫描-->
    <context:component-scan base-package="com.atguigu" />
    <!-- 开启aspectj框架注解支持-->
    <aop:aspectj-autoproxy />
</beans>
```
2. 配置类方式

```Java
@Configuration
@ComponentScan(basePackages = "com.atguigu")
//作用等于 <aop:aspectj-autoproxy /> 配置类上开启 Aspectj注解支持!
@EnableAspectJAutoProxy
public class MyConfig {
}

```
#### 5 - 测试

```Java
//@SpringJUnitConfig(locations = "classpath:spring-aop.xml")
@SpringJUnitConfig(value = {MyConfig.class})
public class AopTest {

    @Autowired
    private Calculator calculator;

    @Test
    public void testCalculator(){
        calculator.add(1,1);
    }
}

```

- 运行测试结果：成功配置 **AOP**

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E5%88%9D%E6%AD%A5%E5%AE%9E%E7%8E%B0-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240521145151538" style="zoom:99%;" />



### 4.2.3 - 获取切点细节信息语法

#### 1 - **JointPoint接口**

需要获取方法签名、传入的实参等信息时，可以在通知方法声明JoinPoint类型的形参。

- 要点1：JoinPoint 接口通过 getSignature() 方法获取目标方法的签名（方法声明时的完整信息）
- 要点2：通过目标方法签名对象获取方法名
- 要点3：通过 JoinPoint 对象获取外界调用目标方法时传入的实参列表组成的数组

```Java
// @Before注解标记前置通知方法
// value属性：切入点表达式，告诉Spring当前通知方法要套用到哪个目标方法上
// 在前置通知方法形参位置声明一个JoinPoint类型的参数，Spring就会将这个对象传入
// 根据JoinPoint对象就可以获取目标方法名称、实际参数列表
@Before(value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))")
public void printLogBeforeCore(JoinPoint joinPoint) {
    
    // 1.通过JoinPoint对象获取目标方法签名对象
    // 方法的签名：一个方法的全部声明信息
    Signature signature = joinPoint.getSignature();
    
    // 2.通过方法的签名对象获取目标方法的详细信息
    String methodName = signature.getName();
    System.out.println("methodName = " + methodName);
    
    int modifiers = signature.getModifiers();
    System.out.println("modifiers = " + modifiers);
    
    String declaringTypeName = signature.getDeclaringTypeName();
    System.out.println("declaringTypeName = " + declaringTypeName);
    
    // 3.通过JoinPoint对象获取外界调用目标方法时传入的实参列表
    Object[] args = joinPoint.getArgs();
    
    // 4.由于数组直接打印看不到具体数据，所以转换为List集合
    List<Object> argList = Arrays.asList(args);
    
    System.out.println("[AOP前置通知] " + methodName + "方法开始了，参数列表：" + argList);
}
```
#### 2 - **方法返回值**

在返回通知中，通过**@AfterReturning**注解的returning属性获取目标方法的返回值！

```Java
// @AfterReturning注解标记返回通知方法
// 在返回通知中获取目标方法返回值分两步：
// 第一步：在@AfterReturning注解中通过returning属性设置一个名称
// 第二步：使用returning属性设置的名称在通知方法中声明一个对应的形参
@AfterReturning(
        value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))",
        returning = "targetMethodReturnValue"
)
public void printLogAfterCoreSuccess(JoinPoint joinPoint, Object targetMethodReturnValue) {
    
    String methodName = joinPoint.getSignature().getName();
    
    System.out.println("[AOP返回通知] "+methodName+"方法成功结束了，返回值是：" + targetMethodReturnValue);
}
```
#### 3 - **异常对象捕捉**

在异常通知中，通过@AfterThrowing注解的throwing属性获取目标方法抛出的异常对象

```Java
// @AfterThrowing注解标记异常通知方法
// 在异常通知中获取目标方法抛出的异常分两步：
// 第一步：在@AfterThrowing注解中声明一个throwing属性设定形参名称
// 第二步：使用throwing属性指定的名称在通知方法声明形参，Spring会将目标方法抛出的异常对象从这里传给我们
@AfterThrowing(
        value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))",
        throwing = "targetMethodException"
)
public void printLogAfterCoreException(JoinPoint joinPoint, Throwable targetMethodException) {
    
    String methodName = joinPoint.getSignature().getName();
    
    System.out.println("[AOP异常通知] "+methodName+"方法抛异常了，异常类型是：" + targetMethodException.getClass().getName());
}
```

#### 4 - **实验代码演示**

```java
/**
 * description: 定义四个增强方法，获取目标方法的信息，返回值，异常对象
 *
 * 1. 定义方法 - 增强代码
 * 2. 使用注解指定对应的位置
 * 3. 配置切点表达式选中方法
 * 4. 切面和IoC 的配置
 * 5. 开启 aspectj 注解的支持
 *
 * TODO：增强方法中获取目标方法信息
 *  1. 全部增强方法中，获取目标方法的信息（方法名，参数，访问）
 *     - (JoinPoint jointPoint) jointPoint 包含目标方法的信息 import org.aspectj.lang.JoinPoint;
 *  2. 返回的结果 - @AfterReturning
 *     1 - (JoinPoint jointPoint, Object result) result接收返回的结果
 *     2 - @AfterReturning(value = "execution(* com..impl.*.*(..))",returning = "result") 注解中指定形参名称
 *  3. 异常的信息 - @AfterThrowing
 *    1 - (JoinPoint jointPoint, Throwable e) e接收异常对象
 *    2 - @AfterThrowing(value = "execution(* com..impl.*.*(..))",throwing = "e") 注解中指定形参名称
 */
@Component
@Aspect
public class MyAdvice {
    @Before("execution(public int com.boyan.Calculator.add(int,int))")
    public void before(JoinPoint jointPoint) {
        // 1. 获取方法所属类的信息（固定方法，记就行）
        String simpleName = jointPoint.getTarget().getClass().getSimpleName();
        // 2. 获取方法名称
        int modifiers = jointPoint.getSignature().getModifiers();
        Modifier.toString(modifiers);
        String name = jointPoint.getSignature().getName();
        // 3. 获取参数列表
        Object[] args = jointPoint.getArgs();
    }
    @AfterReturning(value = "execution(public int com.boyan.Calculator.add(int,int))",returning = "result")
    public void afterReturning(JoinPoint jointPoint, Object result) {  // 获取返回结果


    }

    @After("execution(public int com.boyan.Calculator.add(int,int))")
    public void after(JoinPoint jointPoint) {

    }
    @AfterThrowing(value = "execution(public int com.boyan.Calculator.add(int,int))",throwing = "e")
    public void afterThrowing(JoinPoint jointPoint, Throwable e) {  // 获取异常信息

    }
}
}
```



### 4.2.4 - 切点表达式语法

AOP切点表达式（Pointcut Expression）是一种用于指定切点的语言，它可以通过定义匹配规则，来选择需要被切入的目标对象。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E5%88%87%E7%82%B9%E8%A1%A8%E8%BE%BE%E5%BC%8F%E8%AF%AD%E6%B3%95%E5%9B%BE.png" alt="image-20240521170532297" style="width:90%;" />

**语法细节**

```java
/**
 * TODO: 切点表达式
 *  固定语法 execution(1 2 3.4.5(6))
 *  1. 访问修饰符 public / private
 *  2. 返回值类型 int / void / String
 *     如果不考虑访问修饰符和返回值！这两位整合成一起写 *
 *     如果不考虑，两个必须一起不考虑！不能出现 * String
 *  3. 包的位置
 *      具体包：com.boyan.service.impl
 *      单层模糊：com.boyan.service.*
 *      多层模糊：com..impl
 *              .. 代表任意层的模糊，但细节：.. 不能开头
 *              找所有 impl包：com..impl / *..impl✅
 *  4. 类的名称
 *       具体类：CalculatorPureImpl
 *       模糊：*
 *       部分模糊：*Impl
 *  5. 方法名 语法和类名一致
 *  6. (6) 形参数列表
 *         没有参数 ()
 *         有具体参数 (String)
 *         模糊参数 (..) 有没有参数都可以，有多个也可以！
 *         部分模糊 (String..) String 后面有没有无所谓
 *                 (..int)    最后一个参数是 int
 *                 (String..int)
 */
```

**案例**

```
1. 查询某包某类下，访问修饰符是公有，返回值是int的全部方法
2. 查询某包下类中第一个参数是String的方法
3. 查询全部包下，无参数的方法！
4. 查询com包下，以int参数类型结尾的方法
5. 查询指定包下，Service开头类的私有返回值int的无参数方法

1. execution(public int com.example.package..*.*(..))
2. execution(* com.example.package..*.*(String,..))
3. execution(* *..*.*())
4. execution(* com..*.*(.., int))
5. execution(private int com.example.package..Service*.*())
```



### 4.2.5 - 统一切点管理：切点表达式的提取和复用

这是我们之前编写切点表达式的方式，发现 所有增强方法的切点表达式相同！出现了冗余，如果需要切换也不方便统一维护！

所以可以将切点提取，在增强上进行引用即可！

```java
// @Before注解：声明当前方法是前置通知方法
// value属性：指定切入点表达式，由切入点表达式控制当前通知方法要作用在哪一个目标方法上
@Before(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogBeforeCore() {
    System.out.println("[AOP前置通知] 方法开始了");
}

@AfterReturning(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogAfterSuccess() {
    System.out.println("[AOP返回通知] 方法成功返回了");
}

@AfterThrowing(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogAfterException() {
    System.out.println("[AOP异常通知] 方法抛异常了");
}

@After(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogFinallyEnd() {
    System.out.println("[AOP后置通知] 方法最终结束了");
}
```



#### 4.2.5.1 同一类中提取内部引用

提取

```Java
// 切入点表达式重用
@Pointcut("execution(public int com.atguigu.aop.api.Calculator.add(int,int)))")
public void declarPointCut() {}
```

​		注意：提取切点注解使用@Pointcut(切点表达式) ， 需要添加到一个无参数无返回值方法上即可！

引用：在注解值赋予这个表达式

```Java
@Before(value = "declarPointCut()") 
public void printLogBeforeCoreOperation(JoinPoint joinPoint) {
```

案例整合

```java
//  4.2.5.1 同一类中提取后内部引用
@Pointcut("execution(public int com.atguigu.aop.api.Calculator.add(int,int)))")
public void declarPointCut() {}

@Before(value = "declarPointCut()") 
public void printLogBeforeCore() {
    System.out.println("[AOP前置通知] 方法开始了");
}

@AfterReturning(value = "declarPointCut()") 
public void printLogAfterSuccess() {
    System.out.println("[AOP返回通知] 方法成功返回了");
}

@AfterThrowing(value = "declarPointCut()") 
public void printLogAfterException() {
    System.out.println("[AOP异常通知] 方法抛异常了");
}

@After(value = "declarPointCut()") 
public void printLogFinallyEnd() {
    System.out.println("[AOP后置通知] 方法最终结束了");
}
```

#### 4.2.5.2 不同类中引用

不同类在引用切点，只需要添加类的全限定符+方法名即可！

```Java
@Before(value = "com.atguigu.spring.aop.aspect.LogAspect.declarPointCut()")
public Object roundAdvice(ProceedingJoinPoint joinPoint) {
```

#### 4.2.5.3 切点统一管理

**建议：将切点表达式统一存储到一个类中进行集中管理和维护！**

```Java
@Component
public class AtguiguPointCut {
    
    @Pointcut(value = "execution(public int *..Calculator.sub(int,int))")
    public void atguiguGlobalPointCut(){}
    
    @Pointcut(value = "execution(public int *..Calculator.add(int,int))")
    public void atguiguSecondPointCut(){}
    
    @Pointcut(value = "execution(* *..*Service.*(..))")
    public void transactionPointCut(){}
}
```



### 4.2.6 - 环绕通知 @Around

**简介**

一个顶上面四个：@Before前置 @AfterReturning后置 @AfterThrowing异常 @After最后

```java
try{
    前置
    目标方法执行
    后置
}catch(){
    异常
}finally{
    最后
}
```

环绕通知对应整个 try...catch...finally 结构，包括前面四种通知的所有功能。

**实现两个案例对照着看**

```java
@Component
@Aspect
public class TxAdvice {

    @Before(value = "com.boyan.pointcut.MyPointCut.boyanSecondPointCut()")
    public void begin() {
        System.out.println("事务开启");
    }
    @AfterReturning(value = "com.boyan.pointcut.MyPointCut.boyanSecondPointCut()")
    public void commit() {
        System.out.println("事务提交");
    }
    @AfterThrowing(value = "com.boyan.pointcut.MyPointCut.boyanSecondPointCut()")
    public void rollback() {
        System.out.println("事务回滚");
    }

}
```

@Around：形参列表中一定要有：(ProceedingJoinPoint joinPoint)，而不是(JoinPoint joinPoint)⬇️

```java
@Component
@Aspect
public class TxAroundAdvice {
    @Around(value = "com.boyan.pointcut.MyPointCut.transactionPointCut()")
    public Object transaction(ProceedingJoinPoint joinPoint) {
        // 保证目标方法被执行即可
        Object[] args = joinPoint.getArgs();
        Object result = null;

        try {
            // 开启事务
            System.out.println("开启事务");
            // 执行目标方法
            result = joinPoint.proceed(args);
            // 提交事务
            System.out.println("提交事务");
        } catch (Throwable throwable) {
            // 回滚事务
            System.out.println("回滚事务");
            throw new RuntimeException(throwable);
        } finally {

        }
        return result;
    }
}

```



一个较为完整的案例⬇️

```Java
// 使用@Around注解标明环绕通知方法
@Around(value = "com.boyan.MyPointCut.transactionPointCut()")
public Object manageTransaction(ProceedingJoinPoint joinPoint) {
        // 通过在通知方法形参位置声明ProceedingJoinPoint类型的形参，
        // Spring会将这个类型的对象传给我们
        
    
    // 通过ProceedingJoinPoint对象获取外界调用目标方法时传入的实参数组
    Object[] args = joinPoint.getArgs();
    
    // 通过ProceedingJoinPoint对象获取目标方法的签名对象
    Signature signature = joinPoint.getSignature();
    
    // 通过签名对象获取目标方法的方法名
    String methodName = signature.getName();
    
    // 声明变量用来存储目标方法的返回值
    Object targetMethodReturnValue = null;
    
    try {
    
        // 在目标方法执行前：开启事务（模拟）
        log.debug("[AOP 环绕通知] 开启事务，方法名：" + methodName + "，参数列表：" + Arrays.asList(args));
    
        // 过ProceedingJoinPoint对象调用目标方法
        // 目标方法的返回值一定要返回给外界调用者
        targetMethodReturnValue = joinPoint.proceed(args);
    
        // 在目标方法成功返回后：提交事务（模拟）
        log.debug("[AOP 环绕通知] 提交事务，方法名：" + methodName + "，方法返回值：" + targetMethodReturnValue);
    
    }catch (Throwable e){
    
        // 在目标方法抛异常后：回滚事务（模拟）
        log.debug("[AOP 环绕通知] 回滚事务，方法名：" + methodName + "，异常：" + e.getClass().getName());
    
    }finally {
    
        // 在目标方法最终结束后：释放数据库连接
        log.debug("[AOP 环绕通知] 释放数据库连接，方法名：" + methodName);
    
    }
    
    return targetMethodReturnValue;
}
```



### 4.2.7 - 切面优先级设置

**1 - 相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序**。

- 优先级高的切面：外面
- 优先级低的切面：里面

使用 @Order 注解可以控制切面的优先级：

- @Order(较**小**的数)：优先级**高**
- @Order(较大的数)：优先级低

实际意义

实际开发时，如果有多个切面嵌套的情况，要慎重考虑。例如：如果事务切面优先级高，那么在缓存中命中数据的情况下，事务切面的操作都浪费了。此时应该将缓存切面的优先级提高，在事务操作之前先检查缓存中是否存在目标数据。

**2 - 案例：TxAdvice & LogAdvice 两者的优先级（谁包裹谁）**

还是之前的案例，测试代码如下

```java
@SpringJUnitConfig(classes = {com.boyan.config.MyConfig.class})
public class AopTest {

    @Autowired
    private Calculator calculator;

    @Test
    public void testCalculator(){
        calculator.add(1,1);
    }
}
```

直接运行，测试结果：Log 包裹 Tx

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E5%88%87%E9%9D%A2%E4%BC%98%E5%85%88%E7%BA%A7%E8%AE%BE%E7%BD%AE%E5%89%8D%E6%B5%8B%E8%AF%95.png" alt="image-20240521182928824" style="width:99%;" />

想让 Tx 包裹 Log，进行 @Order(value) 优先级设置：**给指定 Advice 类进行设定，指定一个优先级的值，值越小，优先级越高！越高的前置先执行，后置后执行！（想象大圆套小圆，竖着穿插执行）**

```java
@Component
@Aspect
@Order(1) // 值越小越先执行
public class TxAdvice {...}	

@Aspect
@Component
@Order(10)
public class LogAspect {...}
```

再次运行，测试结果：Tx 包裹 Log

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E5%88%87%E9%9D%A2%E4%BC%98%E5%85%88%E7%BA%A7%E8%AE%BE%E5%AE%9A%E5%90%8E%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png" alt="image-20240521183707608" style="width:99%;" />



### 4.2.8 - cglib 生效场景

**在目标类没有实现任何接口的情况下，Spring会自动使用cglib技术实现代理。**

使用总结：

  a.  如果目标类有接口,选择使用jdk动态代理

  b.  如果目标类没有接口,选择cglib动态代理

  c.  如果有接口,接口接值

  d.  如果没有接口,类进行接值



## 4.3 XML 配置 AOP（了解）

- spring-aop.xml

```xml
<!-- TODO: ⬇️ 使用 XML 配置方式 进行 aop 配置：切面配置、声明切点、位置指定 -->

<!-- 配置目标类的bean -->
<bean id="calculatorPure" class="com.boyan.CalculatorPureImpl"/>

<!-- 配置切面类的bean -->
<bean id="logAspect" class="com.boyan.advice.LogAspect"/>

<!-- 配置AOP -->
<aop:config>

    <!-- 配置切入点表达式 -->
    <aop:pointcut id="logPointCut" expression="execution(* *..*.*(..))"/>

    <!-- aop:aspect标签：配置切面 -->
    <!-- ref属性：关联切面类的bean -->
    <aop:aspect ref="logAspect">
        <!-- aop:before标签：配置前置通知 -->
        <!-- method属性：指定前置通知的方法名 -->
        <!-- pointcut-ref属性：引用切入点表达式 -->
        <aop:before method="printLogBeforeCore" pointcut-ref="logPointCut"/>

        <!-- aop:after-returning标签：配置返回通知 -->
        <!-- returning属性：指定通知方法中用来接收目标方法返回值的参数名 -->
        <aop:after-returning
                method="printLogAfterSuccess"
                pointcut-ref="logPointCut"
                returning=" "/>

        <!-- aop:after-throwing标签：配置异常通知 -->
        <!-- throwing属性：指定通知方法中用来接收目标方法抛出异常的异常对象的参数名 -->
        <aop:after-throwing
                method="printLogAfterException"
                pointcut-ref="logPointCut"
                throwing=""/>

        <!-- aop:after标签：配置后置通知 -->
        <aop:after method="printLogFinallyEnd" pointcut-ref="logPointCut"/>

        <!-- aop:around标签：配置环绕通知 -->
        <!--<aop:around method="……" pointcut-ref="logPointCut"/>-->
    </aop:aspect>

</aop:config>
```



## 4.4 Spring AOP 对组件管理影响和总结

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-%E6%B3%A8%E8%A7%A3%E5%AE%9E%E7%8E%B0%E5%B0%8F%E7%BB%93%E5%9B%BE%E8%A7%A3.png" alt="image-20240521212251688" style="width:90%;" />

- 对于 “有接口” 的 类应用切面：

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-aspect%E7%B1%BB%E5%BA%94%E7%94%A8%E5%88%87%E9%9D%A2.png" alt="image-20240521212422583" style="zoom:50%;" />

​	- **这种情况下只通过接口可取，但通过实现类取不到了，因为是将代理类放入 IoC 容器，和实现类无关了。**

- 对于 “无接口” 的 类应用切面：

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-spring-aop-cglib%E7%B1%BB%E5%BA%94%E7%94%A8%E5%88%87%E9%9D%A2.png" style="width:30%;" />

​	- 所以：**在 Spring-AOP 技术中，只要目标类有接口，必须使用其接口类型接 IoC 容器中代理组件！**





# 五、Spring TX 声明式事务管理





# 总结、Spring 常用注解 @Annotation

### 1. 配置相关注解

- **`@Configuration`**：标记一个类为 Spring 的配置类，相当于传统的 XML 配置文件。
- **`@ComponentScan`**：自动扫描和注册使用 `@Component`、`@Service`、`@Repository` 和 `@Controller` 注解的 bean。
- **`@Bean`**：在配置类中定义一个方法，创建并返回一个 Spring 容器管理的 bean。

### 2. 组件相关注解

- **`@Component`**：通用的 Spring 组件注解，标识一个类为 Spring 管理的组件。
- **`@Service`**：标识一个服务层组件，通常用于业务逻辑层。
- **`@Repository`**：标识数据访问层组件，通常用于数据访问逻辑层，并支持自动翻译数据库异常。
- **`@Controller`**：标识一个控制器组件，通常用于 Web 层，处理请求和返回视图。

### 3. 依赖注入相关注解

- **`@Autowired`**：自动注入依赖的 bean，按类型注入。
- **`@Qualifier`**：与 `@Autowired` 结合使用，指定注入的具体 bean，按名称注入。
- **`@Primary`**：在有多个 bean 候选时，指定首选的 bean。
- **`@Resource`**（`javax.annotation`）：按名称注入 bean。
- **`@Inject`**（`javax.inject`）：按类型注入 bean，与 `@Autowired` 类似。

### 4. 生命周期相关注解

- **`@PostConstruct`**（`javax.annotation`）：标记一个方法为初始化方法，在 bean 构造完成并依赖注入之后执行。
- **`@PreDestroy`**（`javax.annotation`）：标记一个方法为销毁方法，在 Spring 容器销毁 bean 之前执行。

### 5. 事务管理相关注解

- **`@Transactional`**：声明事务管理，应用于类或方法上，标识方法需要事务支持。

### 6. AOP 相关注解

- **`@Aspect`**：声明一个类为切面类。
- **`@Before`**：声明前置通知，在目标方法执行之前执行。
- **`@After`**：声明后置通知，在目标方法执行之后执行。
- **`@AfterReturning`**：声明返回通知，在目标方法成功返回之后执行。
- **`@AfterThrowing`**：声明异常通知，在目标方法抛出异常时执行。
- **`@Around`**：声明环绕通知，可以自定义目标方法的执行。

### 7. Web 相关注解

- **`@RequestMapping`**：映射 HTTP 请求到处理方法上，可以用于类和方法上。
- **`@GetMapping`**：映射 GET 请求到处理方法上。
- **`@PostMapping`**：映射 POST 请求到处理方法上。
- **`@PutMapping`**：映射 PUT 请求到处理方法上。
- **`@DeleteMapping`**：映射 DELETE 请求到处理方法上。
- **`@PatchMapping`**：映射 PATCH 请求到处理方法上。
- **`@RequestParam`**：绑定请求参数到方法参数。
- **`@PathVariable`**：绑定 URL 路径变量到方法参数。
- **`@RequestBody`**：将 HTTP 请求体绑定到方法参数。
- **`@ResponseBody`**：将方法的返回值绑定到 HTTP 响应体。
- **`@RestController`**：组合注解，标识一个类为控制器，并且每个方法都返回对象而不是视图，相当于 `@Controller` 和 `@ResponseBody` 的组合。

### 8. 配置属性相关注解

- **`@Value`**：注入外部配置属性值。
- **`@PropertySource`**：指定属性文件的位置。

























































































