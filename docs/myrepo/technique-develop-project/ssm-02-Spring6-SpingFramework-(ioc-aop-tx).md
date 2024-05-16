

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



------

### 3.4.4 - 三种配置方式总结

#### 配置方式1 - 完全XML

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

------

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





# 四、Spring AOP 面向切面编程





# 五、Spring TX 声明式事务管理

































































































