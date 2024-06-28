# Part01 - Maven(版本3.6.3) - 重点版

> [!TIP]
>
> - **Maven 是一个工具、软件（项目构建和依赖工具），学会如何使用即可；**
> - Maven 核心点掌握总结：
>   1. 安装：maven安装、环境变量、maven配置文件修改；
>   2. 工程创建：gavp属性理解、JavaSE/EE工程创建、项目结构；
>   3. *依赖管理：依赖添加、依赖传递、版本提取、导入依赖错误解决；
>   4. 构建管理：构建过程、构建场景、构建周期等；
>   5. 继承和聚合：理解继承和聚合作用、继承语法和实践、聚合语法和实践；
>
> 相关文章链接：
>
> - maven： [一、高效构建 Java 应用：Maven 入门和进阶](https://www.wolai.com/fbnhGx8eE9JfZugFpbCWmC)



# 一、Maven 简介和快速入门

## 1.1 Maven 介绍和作用

1. **是什么？**：软件，重点是使用，而不是掌握其核心原理；

2. **为什么服务？**：只支持 Java 项目；

3. **主要功能**：1）**项目构建**管理，2）**依赖管理**；

   1. 项目构建：

      - **什么是**项目构建？：项目构建是指将源代码、配置文件、资源文件等转化为能够运行或部署的应用程序或库的过程！

      - **什么时候进行**项目构建？：项目开发完成后，想要将项目打成.war文件，并部署到服务器中运行；

      - **如何进行**项目构建？：

        ```cmd
        mvn package	// 一行构建命令，快速进行 项目构建、打包；
        ```

      - **使用maven进行项目构建的好处**？（相比用IDE构建）

        1. 触发方便，一行命令可视化触发；
        2. 统一构建格式，否则不同IDE构建格式不同；

   2. 依赖管理：自动下载依赖、自动下载依赖所需要的依赖且保证避免版本冲突，只需要在 pom.xml 文件中编写配置；

   

## 1.2 Maven 安装和配置

（选*）homebrew 快速安装 / 直接使用 IDEA 内置 maven: [Homebrew 安装 Maven 及其 IDEA 配置](https://www.cnblogs.com/RioTian/p/17293008.html)

- 如果使用在电脑本地下载安装 Maven，应该需要自定义修改 settings.xml 文件；
- 如果是 macOS， 想通过 Terminal 操作 mvn 命令，应该还需要更新 .zshrc / .zshrc.pre-oh-my-zsh (如果安装了oh-my-zsh) 文件；



## 1.3 Maven 工程的 GAVP 属性

**GAVP** 组合起来，可以在 maven 仓库唯一标识一个项目。其中 GAV 在项目创建时指定（**GA 一旦声明不会改变**，V有默认值，随着项目构建过程部署修改，P有默认值，后期通过配置文件修改）。

一般规则，在不同公司中根据情况具体定制：

1. GroupID：com.{公司/BU }.业务线.[子业务线]，**最多 4 级**。

​			    正确举例：com.taobao.tddl

2. ArtifactID：产品线名-模块名。语义不重复不遗漏，先到仓库中心去查证一下。

   ​                     正确举例：tc-client / uic-api / tair-tool / bookstore

3. Version 版本号推荐写法：主版本号.次版本号.修订号

​		1） 主版本号：当做了不兼容的 API 修改，或者增加了能改变产品方向的新功能

​		2） 次版本号：当做了向下兼容的功能性新增（新增类、接口等）

​		3） 修订号：修复 bug，没有修改方法签名的功能加强，保持 API 兼容性

​	：例如： 初始→1.0.0  修改bug → 1.0.1  功能调整 → 1.1.1等  新增/删除模块 → 2.0.0

​	：默认值<version>1.0-SNAPSHOT</version>，后期可以在项目中随意修改。

4. Packaging 打包方式（指示将项目打包成什么类型的文件）

   1）jar（默认值）：代表普通的Java工程，打包以后是.jar结尾的文件。

   2）war，代表Java的web工程，打包以后.war结尾的文件。

   3）pom，代表不会打包，用来做继承的父工程。





# 二、基于 Idea 的 Maven 工程创建

## 2.1 Idea 构建 Maven Java SE 工程

通过 **New Module / New Project** 填写 **GA** 属性（**一旦声明 GA 不会改变**），创建Maven工程：

- Name：输入，maven 工程名称；
- GroupID：输入；
- ArtifactID：输入，一般情况下同 Project Name ；
- Version：无需输入，使用默认值 1.0-SNAPSHOT ；



## 2.2 Idea 构建 Maven Java EE 工程

两个方法：手动 / 通过 JBLJavaToWeb 插件快速补全 web 项目

### 方法1. 手动创建 EE 工程

1. 创建一个 SE 工程。
2. **手动**添加 web 项目结构文件：**在 /main 位置中添加 webapp/WEB-INF/web.xml 文件**，⚠️注意：结构和命名固定。
3. **修改** pom.xml 文件 中关于 **packaging** 打包方式的设定。

```xml
<!-- 一旦声明 GA 不会改变 -->
<groupId></groupId>
<artifactId></artifactId>
<!-- 构建过程 - 部署时 - 修改 -->
<version></version>
<!-- maven工程 packaging 打包方式 设定 - 默认值 jar(SE工程) / war(web工程） / pom(不打包)-->
<packaging>war</packaging>
```

4. 点击刷新 maven 工程，项目的 webapp 文件夹出现小蓝点，代表成功！

### 方法2. 通过 JBLJavaToWeb 插件快速补全 web 项目

a. 在 Idea 的 Plugin Marketplace 插件市场中，搜索安装 JBLJavaToWeb 插件。

b. 创建一个 SE 工程。

c.  “项目工程” 右键，选择单击 “JBLJavaToWeb” 使用插件快速创建 web 项目，插件会快速补全上述内容。



## 2.3 Maven Web 工程项目的文件结构 及 每个文件的作用

Maven 是一个强大的构建工具，它提供一种标准化的项目结构，可以帮助开发者更容易地管理项目的依赖、构建、测试和发布等任务。以下是 Maven Web 程序的文件结构及每个文件的作用：

```xml
|-- pom.xml                               # Maven 项目管理文件 
|-- src
    |-- main                              # 项目主要代码
    |   |-- java                          # Java 源代码目录
    |   |   `-- com/example/myapp         # 开发者代码主目录
    |   |       |-- controller            # 存放 Controller 层代码的目录
    |   |       |-- service               # 存放 Service 层代码的目录
    |   |       |-- dao                   # 存放 DAO 层代码的目录
    |   |       `-- model                 # 存放数据模型的目录
    |   |-- resources                     # 资源目录，存放配置文件、静态资源等
    |   |   |-- log4j.properties          # 日志配置文件
    |   |   |-- spring-mybatis.xml        # Spring Mybatis 配置文件
    |   |   `-- static                    # 存放静态资源的目录
    |   |       |-- css                   # 存放 CSS 文件的目录
    |   |       |-- js                    # 存放 JavaScript 文件的目录
    |   |       `-- images                # 存放图片资源的目录
    |   `-- webapp                        # 存放 WEB 相关配置和资源
    |       |-- WEB-INF                   # 存放 WEB 应用配置文件
    |       |   |-- web.xml               # Web 应用的部署描述文件
    |       |   `-- classes               # 存放编译后的 class 文件
    |       `-- index.html                # Web 应用入口页面
    `-- test                              # 项目测试代码
        |-- java                          # 单元测试目录
        `-- resources                     # 测试资源目录
```

- pom.xml：Maven 项目管理文件，用于描述项目的依赖和构建配置等信息。
- src/main/java：存放项目的 Java 源代码。
- src/main/resources：存放项目的资源文件，如配置文件、静态资源等。
- src/main/webapp/WEB-INF：存放 Web 应用的配置文件。
- src/main/webapp/index.html：Web 应用的入口页面。
- src/test/java：存放项目的测试代码。
- src/test/resources：存放测试相关的资源文件，如测试配置文件等。





# 三、Maven 核心功能介绍

1）依赖管理  2）项目构建

## 3.1 依赖管理

### 1）配置：pom.xml 文件的 xml 结构及各部分的作用

1. maven项目信息属性配置：<model> and <GAVP>

```xml
<!-- 模型版本：表示该项目遵循 Maven 4.0.0 版本的 XML 模型规范。 -->
<modelVersion>4.0.0</modelVersion>

<!-- GAVP - 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->
<groupId>com.companyname.project-group</groupId>
<!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
<artifactId>project</artifactId>
<!-- 版本号 -->
<version>1.0.0</version>
<!-- 打包方式
     默认：jar
     jar指的是普通的java项目打包方式！ 项目打成jar包！
     war指的是web项目打包方式！项目打成war包！
     pom不会讲项目打包！这个项目作为父工程，被其他工程聚合或者继承！后面会讲解两个概念
-->
<packaging>jar/pom/war</packaging>
```

2. 依赖管理和添加：
   - <dependencies> 中包含每一个依赖项
     - <dependency> 中是具体的依赖项:
       - 配置该依赖的 GAV，从而唯一标识一个依赖项（引入别的 maven 项目）；
       - 该依赖项的生效范围 == compile(默认) / provided / runtime / test；
       - **how to get?**：**从 maven repository 直接复制** / 使用 maven-search 插件;

```xml
<!-- 
   通过编写依赖jar包的gav必要属性，引入第三方依赖！
   scope属性是可选的，可以指定依赖生效范围！
   依赖信息查询方式：
      1. maven仓库信息官网 https://mvnrepository.com/
      2. mavensearch插件搜索
 -->
<dependencies>
    <!-- 引入具体的依赖包 -->
    <dependency>
        <!-- 其他 maven 工程的定位信息-->
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
        <!--
            生效范围
            - compile ：main目录✅ test目录✅  打包运行✅ [默认值]
            - provided：main目录✅ test目录✅	打包运行❌	eg.servlet HttpServlet, tomcat 提供了servlet；
            - runtime： main目录❌ test目录❌  打包运行✅ eg.com.mysql.cj.jdbc.Driver, 只在打包运行时有效；
            - test：    main目录❌ test目录✅  打包运行❌ eg.junit @Test, 只在测试范围内生效；
				    总结：配置 scope 是一种锦上添花的手段，如果掌握不好，使用默认值一定不会错。
         -->
        <scope>runtime</scope>
    </dependency>

</dependencies>
```

3. （扩展1）依赖版本提取和维护：提取版本号，统一管理 <properties>

```xml
<!-- 声明 - 版本号 version -->
<properties>
  <!-- 命名随便但建议 <xx.xx> 两层以上, 内部制定版本号即可！声明之后就可以在其他位置引用：${junit.version} -->
  <junit.version>4.11</junit.version>
  <!-- 也可以通过 maven 规定的固定的key，配置maven的参数！如下配置编码格式！-->
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>

<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <!-- 使用 - ⬇️ -->
    <version>${junit.version}</version>
  </dependency>
</dependencies>
```

优点：比起在一个个 <dependency> 依赖项中查看、修改依赖项，把所有依赖的版本号提取出来统一管理，更方便！

4. （扩展2）可选属性 scope 生效范围：见⬆️ “2. xml 部分” 注释中的解释；

5. （扩展3）在 pom.xml 中配置 导入插件

   <build><plugins></plugins></build>

```xml
<build>
   <!-- 问题：jdk17 和 war包版本插件不匹配 - 解决：导入自己的插件代替原本插件 -->
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.2</version>
        </plugin>
    </plugins>
</build>
```



### 2）Maven 自动管理：依赖传递与冲突

重点是 “自动” 。

#### a. Maven “依赖传递” 特性

引入一个库或框架时，构建工具（如 Maven、Gradle）会自动解析和加载其所有的直接和间接依赖，确保这些依赖都可用。

Maven自动管理依赖传递的好处：1）减少重复依赖 2）自动管理依赖，简化依赖项的管理 3）确保依赖传递之间的版本正确性，排除其中的版本不兼容问题，只要通过 Maven 自动管理的依赖项，版本一定是兼容的。

#### b. Maven 自动解决 “依赖冲突” 原则

- 第一原则：“短路优先”

  - A—>B—>C—>D—>E—>X(version 0.0.1)

    A—>F—>X(version 0.0.2)

    则A依赖于X(version 0.0.2)。

- 第二原则：依赖路径长度相同的情况下，则 “先声明优先”

  - A—>E—>X(version 0.0.1)

    A—>F—>X(version 0.0.2)

    在<depencies></depencies>中，路径相同，先声明的，会优先选择！



### 3）Maven “依赖导入失败” 的场景和解决方案

场景1. 下载失败；

场景2. “不一致”：依赖项的版本号或配置文件中的版本号错误 / 依赖项没有正确定义，导致下载的依赖项与实际需要的不一致；

场景3. “损坏”：本地 Maven 仓库或缓存被污染或损坏，导致 Maven 无法正确地使用现有的依赖项，并且也无法重新下载！

**解决方案**

1. 检查网络连接和 Maven 仓库服务器状态。
2. 确保依赖项的版本号与项目对应的版本号匹配，并检查 POM 文件中的依赖项是否正确。
3. 清除本地 Maven 仓库缓存（lastUpdated 文件），因为只要存在lastupdated缓存文件，刷新也不会重新下载。本地仓库中，根据依赖的gav属性依次向下查找文件夹，最终删除内部的文件，刷新重新下载即可！



## 3.2 构建管理

#### 1 - 构建概念

- 项目构建：将 **“源代码、依赖库和资源文件等“** - 转换成 - **”可执行或可部署的应用程序“** 的过程。

​	这个过程包含，编译源代码、链接依赖库、打包和部署等多个步骤。

#### 2 - 主动触发 “项目构建” 的 3个场景

1. （重新编译） ：项目文件编译不充分, 部分文件没有被编译!
2. （打包部署）：需要独立部署到外部服务器软件，打包部署。
3. （本地部署 / 私服仓库）：需要将 maven 工程加入到本地或者私服仓库，以供其他工程使用。

#### 3 - Maven 构建方式

##### a. 细说 mvn 命令行语法（命令行方式构建）

| 命令        | 描述                                         |
| ----------- | -------------------------------------------- |
| mvn clean   | 清理编译或打包后的项目结构，删除target文件夹 |
| mvn compile | 编译项目，生成target文件                     |
| mvn test    | 执行测试源码 (测试)                          |
| mvn site    | 生成一个项目依赖信息的展示页面               |
| mvn package | 打包项目，生成war / jar 文件                 |
| mvn install | 打包后上传到maven本地仓库(本地部署)          |
| mvn deploy  | 只打包，上传到maven私服仓库(私服部署)        |

##### b. Idea 右侧 Maven 可视化插件（可视化方式构建）

- 顶栏中值得注意的，包含：1）左一：刷新 pom.xml 配置  2）右四：跳过测试；
- 选中程序，即可触发构建命令。

#### 4 - Maven 构建命令 / 生命周期（自动化）

##### a. 原理

- 是一组固定构建命令的有序集合，触发周期后的命令，会自动触发周期前的命令！也达到了简化构建的目的！

- 包含 **3 个周期**（从上到下按顺序进行）：

  1. **清理**周期：主要是对项目之前编译生成的文件进行清理

     包含命令：clean

  2. **默认**周期：定义了真正构件时所需要执行的所有步骤，它是生命周期中最核心的部分

     包含命令：compile - test - package - install / deploy

  3. **报告**周期

      包含命令：site


##### b. *最佳使用方案

1. **打包: mvn clean package**
2. **重新编译: mvn clean compile**
3. **本地部署: mvn clean install** 

#### 5 - "周期，命令和插件" 三者之间的关系

- 关系：**周期→包含若干命令→包含若干插件!**   使用周期命令构建，简化构建过程。最终进行构建的是插件！

```xml
<!-- 在 pom.xml 文件中 - 插件配置 部分 - 标签：build - plugins -->
<build>
   <!-- jdk17 和 war包版本插件不匹配 -->
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.2</version>
        </plugin>
    </plugins>
</build>
```





# 四、Maven 工程：继承和聚合特性

Maven 中的工程继承和聚合是两个重要的相关的概念，它们允许开发者更好地 **组织和管理项目** 。

## 4.1 继承（对应 依赖管理）

### 1 - 继承 - 概念

Maven 继承是指在 Maven 的项目中，让一个项目从另一个项目中继承配置信息的机制。继承可以让我们在多个项目中共享同一配置信息，简化项目的管理和维护工作。

Eg. 父工程，不打包，也不写代码，只做 pom.xml 配置的传递。父工程中可以删除 src 文件夹，只留 pom.xml 文件；

### 2 - 继承 - 作用

**作用**：在父工程中统一管理项目中的依赖信息,进行统一版本管理!

它的**背景**是：

- 对一个比较大型的项目进行了模块拆分。
- 一个 project 下面，创建了很多个 module。
- 每一个 module 都需要配置自己的依赖信息。

它**背后的需求**是：

- 多个模块要使用同一个框架，它们应该是同一个版本，所以整个项目中使用的框架版本需要统一管理。
- 使用框架时所需要的 jar 包组合（或者说依赖信息组合）需要经过长期摸索和反复调试，最终确定一个可用组合。这个耗费很大精力总结出来的方案不应该在新的项目中重新摸索。

通过在父工程中为整个项目维护依赖信息的组合既保证了整个项目使用规范、准确的 jar 包；又能够将以往的经验沉淀下来，节约时间和精力。

### 3 - pom.xml 配置 - 工程之间的继承关系

- 父工程 pom.xml 

```xml
<groupId>com.boyan.maven</groupId>
<artifactId>pro03-maven-parent</artifactId>
<version>1.0-SNAPSHOT</version>
<!-- 当前工程作为父工程，它要去管理子工程，所以打包方式必须是 pom -->
<packaging>pom</packaging>
```

- 子工程 pom.xml

```xml
<!-- 使用parent标签指定当前工程的父工程 -->
<parent>
  <!-- 父工程的坐标 -->
  <groupId>com.boyan.maven</groupId>
  <artifactId>pro03-maven-parent</artifactId>
  <version>1.0-SNAPSHOT</version>
</parent>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 - 如果不一致，可以自行配置 -->
<!-- <groupId>com.boyan.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->
```

### 4 - pom.xml 配置 - 依赖统一管理

在Maven的`pom.xml`文件中，父工程通过`dependencyManagement`元素配置的作用是指定了一个依赖管理部分，用于集中管理子工程的依赖版本信息。这意味着在父工程的`dependencyManagement`部分中声明的依赖项版本号将会被子模块继承，但不会自动引入依赖。相反，子模块需要 **显式地声明** 它们需要的依赖项，并且不需要指定版本号，因为它们会从父工程的`dependencyManagement`部分继承。

- 父工程 pom.xml 

  通过统一配置管理 依赖 的 **GAVP**（Packaging自动取默认值）

```xml
<!-- 使用dependencyManagement标签配置对依赖的管理 -->
<!-- 被管理的依赖并没有真正被引入到工程 -->
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```

- 子工程 pom.xml

```xml
<!-- 子工程引用父工程中的依赖信息时，可以把版本号去掉。  -->
<!-- 把版本号去掉就表示子工程中这个依赖的版本由父工程决定。 -->
<!-- 具体来说是由父工程的dependencyManagement来决定。 -->
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
  </dependency>
</dependencies>
```



## 4.2 聚合（对应 构建管理）

#### 1 - 聚合概念

Maven 聚合是指将多个项目组织到一个父级项目中，**通过触发父工程的构建，统一按顺序触发子工程构建**的过程!!

#### 2 - 聚合作用

统一管理子项目的构建，可以控制多个子项目的构建顺序，方便管理和维护。

#### 3 - pom.xml 配置 -聚合 构建管理

- 在 父项目 中配置 聚合（构建管理）的子项目列表

```xml
<project>
  <groupId>com.example</groupId>
  <artifactId>parent-project</artifactId>
  <packaging>pom</packaging>
  <version>1.0.0</version>
  <modules>
    <module>child-project1</module>
    <module>child-project2</module>
  </modules>
</project>
```

- 通过触发父工程构建命令，可以引发所有子模块构建！产生反应堆！



# 五、Maven 实战：搭建微服务 Maven 工程架构

### 5.1 项目需求和结构分析

![IMG_6484](https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-maven-%E9%A1%B9%E7%9B%AE%E9%9C%80%E6%B1%82%E7%BB%93%E6%9E%84%E5%88%86%E6%9E%90%E5%9B%BE.JPG)

其中：

- spring-context会依赖传递 -> core/beans
- jackson-databind会依赖传递 -> core/annotations

### 5.2 项目搭建和统一搭建

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-maven-%E6%90%AD%E5%BB%BA%E5%BE%AE%E6%9C%8D%E5%8A%A1Maven%E5%B7%A5%E7%A8%8B-%E7%9B%AE%E5%BD%95%E6%9E%B6%E6%9E%84%E6%88%AA%E5%9B%BE.png" style="width:50%;" />

- 其实外圈还有 ssm - project

#### parent - pom.xml(maven-micro-shop)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 配置 继承关系 - 父工程 GAVP 坐标 -->
    <parent>
        <groupId>com.boyan</groupId>
        <artifactId>ssm-project</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <!-- 配置 当前工程 GAVP 坐标 -->
    <artifactId>maven-micro-shop</artifactId>
    <packaging>pom</packaging>
    <!-- 配置 聚合关系 -->
    <modules>
        <module>common-service</module>
        <module>user-service</module>
        <module>order-service</module>
    </modules>

    <!-- 依赖管理 - 统一管理版本号 -->
    <properties>
        <spring.version>6.0.6</spring.version>
        <jackson.version>2.15.0</jackson.version>
        <shiro.version>1.10.1</shiro.version>
        <commons.version>2.11.0</commons.version>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- 依赖管理 - 统一配置子工程依赖项 - 不会自动继承 需要子工程显式声明继承 -->
    <dependencyManagement>
        <dependencies>
            <!-- spring-context 会依赖传递core/beans -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!-- jackson-databind 会依赖传递core/annotations -->
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>${jackson.version}</version>
            </dependency>

            <!-- shiro-core -->
            <dependency>
                <groupId>org.apache.shiro</groupId>
                <artifactId>shiro-core</artifactId>
                <version>${shiro.version}</version>
            </dependency>

            <!-- commons-io -->
            <dependency>
                <groupId>commons-io</groupId>
                <artifactId>commons-io</artifactId>
                <version>${commons.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- 统一更新子工程打包插件-->
    <build>
        <!-- jdk17 和 war包版本插件不匹配 -->
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.2.2</version>
            </plugin>
        </plugins>
    </build>
</project>
        <!-- A Parent (pom) Maven Project -->

```



#### child - pom.xml(common-service)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 配置 继承关系 - 父工程 GAVP 坐标 -->
    <parent>
        <groupId>com.boyan</groupId>
        <artifactId>maven-micro-shop</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <!-- 配置 当前工程 GAVP 坐标 -->
    <artifactId>common-service</artifactId>
    <packaging>jar</packaging><!-- 打包方式 默认值：jar 其实可以不写-->

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- 显式声明 - 依赖继承父工程版本-->
    <dependencies>
        <!-- commons-io -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
        </dependency>
    </dependencies>
</project>
        <!-- A Java SE (jar) Maven Project -->
```



#### child - pom.xml(user-service)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>

  <!-- 配置 继承关系 - 父工程 GAVP 坐标 -->  
  <parent> 
    <groupId>com.boyan</groupId>  
    <artifactId>maven-micro-shop</artifactId>  
    <version>1.0-SNAPSHOT</version> 
  </parent>  
  <!-- 配置 当前工程 GAVP 坐标 -->  
  <artifactId>user-service</artifactId>  
  <packaging>war</packaging> <!-- 通过插件 JBLJavaToWeb 转换模块 -->

  <properties> 
    <maven.compiler.source>17</maven.compiler.source>  
    <maven.compiler.target>17</maven.compiler.target>  
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
  </properties>

  <!-- 显式声明 - 依赖继承父工程版本-->
  <dependencies>
    <!-- spring-context 会依赖传递core/beans -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
    </dependency>

    <!-- jackson-databind 会依赖传递core/annotations -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
    </dependency>
  </dependencies>
</project>
        <!-- A Java EE (war) Maven Project -->

```



####  child - pom.xml(order-service)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>

  <!-- 配置 继承关系 - 父工程 GAVP 坐标 -->
  <parent> 
    <groupId>com.boyan</groupId>  
    <artifactId>maven-micro-shop</artifactId>  
    <version>1.0-SNAPSHOT</version> 
  </parent>
  <!-- 配置 当前工程 GAVP 坐标 -->
  <artifactId>order-service</artifactId>  
  <packaging>war</packaging> <!-- 通过插件 JBLJavaToWeb 转换模块 -->

  <properties> 
    <maven.compiler.source>17</maven.compiler.source>  
    <maven.compiler.target>17</maven.compiler.target>  
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
  </properties>

  <!-- 显式声明 - 依赖继承父工程版本-->
  <dependencies>
    <!-- spring-context 会依赖传递core/beans -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
    </dependency>

    <!-- shiro-core -->
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-core</artifactId>
    </dependency>
  </dependencies>
</project>
        <!-- A Java EE (war) Maven Project -->

```



























