# MyBatis3 - 持久层框架

> [!NOTE]
>
> - [官方文档：mybatis - MyBatis 3｜简介](https://mybatis.org/mybatis-3/zh_CN/index.html)
> - [MyBatis实践：提高持久层数据处理效率](https://www.wolai.com/oLP8DUTzo2JRX1DfZNZn6c)



## 一、Mybatis 快速开始

### 1.1 简介

> [!NOTE]
>
> MyBatis 是一款优秀的持久层框架，支持自定义 SQL、存储过程以及高级映射。
>
> MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作，可以 **通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO**（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
>
> ⚠️ **MyBatis** 需要默认 **无参数构造函数** 来实例化对象 -- in POJO类

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-%E7%AE%80%E4%BB%8B-mybatis%E4%BD%9C%E7%94%A8%E6%B5%81%E7%A8%8B%E5%9B%BE.png" alt="image-20240523220228305" style="width:70%;" />

- 在学 MyBatis 之前的持久层：

  - XxxDao.java（接口 - 规定方法）--> XxxDaoImpl.java（实现类 - 规定的方法的具体实现，对数据库的动作）；

- 使用 MyBatis 之后的持久层：

  1. **XxxMapper.java**（接口 - 规定方法）--> **XxxMapper.xml**（接口中对应方法的 sql 语句）；

     - XxxMapper.xml 位置：/resource/XxxMapper.xml... 与接口文件同名

     - XxxMapper.xml 内容：模版：mybatis mapper 固定约束

     - ⚠️ Mapper 中各方法 **不可以重载**（多个同名不同参数方法）

       ​      因为是根据方法名唯一识别，无法从外部识别参数列表；

       ⚠️ Mapper.xml **sql 配置编写规则**

       - 方法名和SQL的id一致
       - 方法返回值和resultType一致
       - 方法的参数和SQL的参数一致
       - 接口的全类名和映射配置文件的名称空间一致

  2. **mybatis-config.xml**：mybatis全局配置文件，包含持久层-数据库的对应<environments>，以及都有哪些 mapper 都要配置进来<mappers>⬇️。在与 spring 框架整合之后，会把这些交给 IoC 容器管理，会更简化。

     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
     <configuration>
     
       <!-- environments表示配置Mybatis的开发环境，可以配置多个环境，在众多具体环境中，使用default属性指定实际运行时使用的环境。default属性的取值是environment标签的id属性的值。 -->
       <environments default="development">
         <!-- environment表示配置Mybatis的一个具体的环境 -->
         <environment id="development">
           <!-- Mybatis的内置的事务管理器 -->
           <transactionManager type="JDBC"/>
           <!-- 配置数据源 -->
           <dataSource type="POOLED">
             <!-- 建立数据库连接的具体信息 -->
             <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
             <property name="url" value="jdbc:mysql://localhost:3306/mybatis-example"/>
             <property name="username" value="root"/>
             <property name="password" value="root"/>
           </dataSource>
         </environment>
       </environments>
     
       <mappers>
         <!-- Mapper注册：指定Mybatis映射文件的具体位置 -->
         <!-- mapper标签：配置一个具体的Mapper映射文件 -->
         <!-- resource属性：指定Mapper映射文件的实际存储位置，这里需要使用一个以类路径根目录为基准的相对路径 -->
         <!-- 对Maven工程的目录结构来说，resources目录下的内容会直接放入类路径，所以这里我们可以以resources目录为基准 -->
         <mapper resource="mappers/XxxMapper.xml"/>
       </mappers>
     
     </configuration>
     ```



### 1.2 ibatis 原理和 mybatis 原理

**ibatis 方式**

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-ibatis%E5%8E%9F%E7%90%86%E5%9B%BE.png" alt="image-20240523164035296" style="width:80%;" />

- 配置过程：自定义 sql 语句，把 mapper 配置到 mybatis 文件，让 SqlSessionFactory 已知。

- 使用过程：通过 sqlSession，到 SqlSessionFactory ，去执行对应的 sql 语句。

- 问题：自定义 sql 过程会出现容易出错、参数因为只能传输一个所以需要整合、返回值问题 ... ...

**mybatis 方式**



<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-mybatis%E5%B0%81%E8%A3%85ibatis%E5%8E%9F%E7%90%86%E5%9B%BE.png" alt="image-20240523165847184" style="zoom:90%;" />

-  Mybatis是对ibatis的进一步优化封装。

  - 如何通过 sqlSession 执行/使用具体 Mapper 中的方法？

    ```java
    /**
     * projectName: com.boyan.test
     *
     * description: 测试类
     */
    public class MyBatisTest {
        @Test
        public void testSelectEmployee() throws IOException {
    
            // 1.创建 SqlSessionFactory 对象
            // 		1.1 - 声明 Mybatis 全局配置文件的路径
            String mybatisConfigFilePath = "mybatis-config.xml";
    
            // 		1.2 - 以输入流的形式加载 Mybatis 配置文件
            InputStream inputStream = Resources.getResourceAsStream(mybatisConfigFilePath);
    
            //	  1.3 - 基于读取 Mybatis 配置文件的输入流创建 SqlSessionFactory 对象
            SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    
            // 2.使用 SqlSessionFactory 对象开启一个会话
            SqlSession session = sessionFactory.openSession();
    
            // 3⚠️.根据 EmployeeMapper 接口的 Class 对象获取 Mapper 接口类型的对象(动态代理技术)
            XxxMapper xxxMapper = session.getMapper(XxxMapper.class);
    
            // 4⚠️. 调用代理类方法既可以触发对应的 SQL 语句
            XxxPojoEntity xxxentity = xxxMapper.queryById(1);
    
            System.out.println("xxxentity = " + xxxentity);	  // 输出查找到的实体作为测试结果
    
            // 4.关闭 SqlSession
            session.commit(); // 提交事务 [DQL不需要,其他需要]
            session.close();  // 关闭会话
        }
    }
    ```

    其中：

    - SqlSession：代表 Java 程序和 数据库 之间的会话
    - SqlSessionFactory：是 生产 SqlSession的 工厂 

    

### 1.3 区分 Http Session & SqlSession

![image-20240523171252037](https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-sqlSession%E5%92%8ChttpSession%E7%9A%84%E5%8C%BA%E5%88%AB.png)

- HttpSession：工作在Web服务器上，属于表述层，代表浏览器和Web服务器之间的会话。

- SqlSession：不依赖Web服务器，属于持久化层，代表Java程序和数据库之间的会话。



### 1.4 所需依赖

pom.xml

```xml
<dependencies>
  <!-- mybatis依赖 -->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.11</version>
  </dependency>

  <!-- MySQL驱动 mybatis底层依赖jdbc驱动实现,本次不需要导入连接池,mybatis自带! -->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.25</version>
  </dependency>

  <!--junit5测试-->
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.3.1</version>
  </dependency>
</dependencies>
```



### 1.4 快速入门 Mybatis3-quickstart-demo 基本搭建

> [!Note]
>
> 新建模块：ssm-mybatis-part 的子模块 mybatis-base-quick-01

（一）数据模型准备

```sql
CREATE DATABASE `mybatis-example`;

USE `mybatis-example`;

CREATE TABLE `t_emp`(
                        emp_id INT AUTO_INCREMENT,
                        emp_name CHAR(100),
                        emp_salary DOUBLE(10,5),
                        PRIMARY KEY(emp_id)
);

INSERT INTO `t_emp`(emp_name,emp_salary) VALUES("tom",200.33);
INSERT INTO `t_emp`(emp_name,emp_salary) VALUES("jerry",666.66);
INSERT INTO `t_emp`(emp_name,emp_salary) VALUES("andy",777.77);
```

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-quickstart-%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B%E5%87%86%E5%A4%87%E6%95%B0%E6%8D%AE%E5%BA%93%E6%88%AA%E5%9B%BEpng" alt="image-20240523230658758" style="width:50%;" />

（二）实体类准备：pojo/Employee.java

```java
package com.boyan.pojo;

public class Employee {

    private Integer empId;

    private String empName;

    private Double empSalary;
  	// getter setter toString
}
```

（三）Mapper 接口准备

1. EmployeeMapper 接口：接口只规定方法,参数和返回值!

```java
package com.boyan.mapper;

import com.boyan.pojo.Employee;

public interface EmployeeMapper {

    /**
     * 根据员工id查询员工数据方法
     * @param empId  员工id
     * @return 员工实体对象
     */
    Employee selectEmployee(Integer empId);
}
```

2. EmployeeMapper.xml：mapper.xml 中编写具体 SQL 语句！其中，t_emp 表对应数据库 SQL 语句映射接口!

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace等于mapper接口类的全限定名,这样实现对应 -->
<mapper namespace="com.boyan.mapper.EmployeeMapper">

    <!-- 查询使用 select标签
            id = 方法名
            resultType = 返回值类型
            标签内编写SQL语句
     -->
    <select id="selectEmployee" resultType="com.boyan.pojo.Employee">
        <!-- #{empId}代表动态传入的参数,并且进行赋值!后面详细讲解 -->
        select emp_id empId,emp_name empName, emp_salary empSalary from
        t_emp where emp_id = #{empId}
    </select>
</mapper>
```

（四）测试类准备：MyBatisTest.java

```java
package com.boyan.test;

import com.boyan.mapper.EmployeeMapper;
import com.boyan.pojo.Employee;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisTest {

    @Test
    public void testSelectEmployee() throws IOException {

        // 1.创建 SqlSessionFactory 对象
        // ①声明 Mybatis 全局配置文件的路径
        String mybatisConfigFilePath = "mybatis-config.xml";

        // ②以输入流的形式加载 Mybatis 配置文件
        InputStream inputStream = Resources.getResourceAsStream(mybatisConfigFilePath);

        // ③基于读取 Mybatis 配置文件的输入流创建 SqlSessionFactory 对象
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        // 2.使用 SqlSessionFactory 对象开启一个会话
        SqlSession session = sessionFactory.openSession();

        // 3.根据 EmployeeMapper 接口的 Class 对象获取 Mapper 接口类型的对象(动态代理技术)
        EmployeeMapper employeeMapper = session.getMapper(EmployeeMapper.class);

        // 4. 调用代理类方法既可以触发对应的 SQL 语句
        Employee employee = employeeMapper.selectEmployee(1);

        System.out.println("employee = " + employee);

        // 4.关闭 SqlSession
        session.commit(); //提交事务 [DQL不需要,其他需要]
        session.close();  //关闭会话
    }
}
```

- 运行测试结果

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-quickstart-%E8%BF%90%E8%A1%8C%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C%E6%88%AA%E5%9B%BE.png" alt="image-20240523233805350" style="width:99%;" />



### 1.5 mybatis 日志输出配置（包含 slf4j 配置方式）

#### 1 - mybatis 官网中 关于配置文件设计标签和顶层结构

描述如下（点击跳转⏭️）:

- configuration（配置）
  - [properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
  - [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  - [environments（环境配置）](https://mybatis.org/mybatis-3/zh/configuration.html#environments)
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)

其中：settings 中各属性描述中，关于日志配置的部分如下⬇️ 一般选择 SLF4J / LOG4J，如果根本不知道日志系统是什么？STDOUT_LOGGING 控制台输出 就很够用了。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-%E6%97%A5%E5%BF%97%E9%85%8D%E7%BD%AE%E5%AE%98%E7%BD%91%E6%8F%8F%E8%BF%B0%E6%88%AA%E5%9B%BE.png" alt="image-20240523211338206" style="width:99%;" />

#### 2 - 日志配置

```xml
<settings>
  <setting name="logImpl" value="STDOUT_LOGGING"/> <!-- 直接配置 value 即可 -->
  <setting name="logImpl" value="SLF4J"/> <!-- 配置 value、引入依赖、新建 log4j.properties 文件 -->
</settings>
```

运行 STDOUT_LOGGING 测试结果：✅

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-%E6%97%A5%E5%BF%97%E9%85%8D%E7%BD%AESTDOUT_LOGGING%E6%B5%8B%E8%AF%95%E6%88%AA%E5%9B%BE.png" alt="image-20240523234841874" style="width:80%;" />

#### 3 - SLF4J 日志系统配置方式

- mybatis-config.xml 配置 value

```xml
<setting name="logImpl" value="SLF4J"/> <!-- 配置 value、引入依赖、新建 log4j.properties 文件 -->
```

- pom.xml 引入依赖（log4j以及slf4j-api）

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.5</version>
</dependency>
```

- 新建：resource/log4j.properties 文件

```properties
log4j.rootLogger=DEBUG,A1
log4j.logger.org.apache=DEBUG
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c]-[%p] %m%n
```

运行 SLF4J 测试结果：✅

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-SLF4J%E6%97%A5%E5%BF%97%E7%B3%BB%E7%BB%9F%E9%85%8D%E7%BD%AE%E6%96%B9%E5%BC%8F-%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C%E6%88%AA%E5%9B%BE.png" alt="image-20240523235403799" style="width:90%;" />



### 1.6 #{} 和 ${}

- **#{ key } == ?** ：占位符 + 赋值（⚠️ 推荐使用 !!!）

  ​				? 只能代替值的位置

```xml
<!-- 过程 -->
1️⃣ emp_id = ? 2️⃣ ? = 赋值
```

- **${ key } == +** ：字符串拼接（通常不会采用${}的方式传值）

```xml
<!-- 过程 -->
"emp_id =" + id
```

**结论**：

1. **实际开发中，能用 #{} 实现的，肯定不用 ${}。**

2. **除非特殊情况： 动态的不是值，是列名或者关键字，需要使用 ${} 拼接。**因为：通过 Java 程序动态生成数据库表，表名部分需要 Java 程序通过参数传入；而 JDBC 对于表名部分是不能使用问号占位符的，此时只能使用 ${}

```Java
//注解方式传入参数！！
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);
```

- ⚠️ **为什么 #{} 更安全？**

  1. **参数化处理**

     ​	#{} 会将参数传递给数据库驱动程序，驱动程序负责将参数安全地嵌入SQL语句中。这意味着参数值不会被当作SQL代码执行。

  2. **避免SQL注入**

     ​	因为参数值是以参数形式传递的，数据库驱动程序不会将其解释为SQL代码，只会将其视为普通数据。





## 二、Mybatis 基本使用

### 2.1 Tip：在 Idea 创建 XxxMapper.xml 的 mybtais 配置模版

Idea 内位置：左上角 IntelliJ Idea -> Settings -> Editor -> File and Code Templates -> + 新建

- Name: mapper 

- Extension(后缀名): xml

- 模版内容：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace == mapper接口类的全限定名 -->
<mapper namespace="com.boyan.mapper.EmployeeMapper">
    
    <!-- example：查询使用 select标签 id = 方法名 resultType = 返回值类型 标签内编写SQL语句 -->
    <select id="selectEmployee" resultType="com.boyan.pojo.Employee">
        <!-- 自定义 sql 语句 -->
        select emp_id empId,emp_name empName, emp_salary empSalary from t_emp where emp_id = #{empId}
    </select>
  
</mapper>
```

- 使用：右键新建文件时可以选择 mapper，以及 mybatis-config 的可以一起配置了。

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-tip-%E5%9C%A8idea%E4%BD%BF%E7%94%A8mybatis-xml%E6%A8%A1%E7%89%88%E6%88%AA%E5%9B%BE.png" alt="image-20240524161832405" style="width:50%;" />



### 2.2 数据输入 Input

> [!NOTE]
>
> 这里数据输入具体是指上层方法（例如Service方法）调用Mapper接口时，数据传入的形式。
>
> - 简单类型：只包含一个值的数据类型
>   - 基本数据类型：int、byte、short、double、……
>   - 基本数据类型的包装类型：Integer、Character、Double、……
>   - 字符串类型：String
> - 复杂类型：包含多个值的数据类型
>   - 实体类类型：Employee、Department、……
>   - 集合类型：List、Set、Map、……
>   - 数组类型：int[]、String[]、……
>   - 复合类型：List<Employee>、实体类中包含集合……
>
> 以及接下来各个案例的类型描述，都是：**参数** 的类型

#### 2.2.1 单个简单类型参数

Mapper接口中抽象方法的声明

```Java
Employee selectEmployee(Integer empId);
```

SQL语句

```XML
<select id="selectEmployee" resultType="com.boyan.pojo.Employee">
  select emp_id empId,emp_name empName,emp_salary empSalary from t_emp where emp_id=#{empId}
</select>
```

```xml
<!--
- key: 随便写，一般情况下推荐使用参数名！
- 单个简单类型参数，在#{}中可以随意命名，但是没有必要。通常还是使用和接口方法参数同名。
-->
```

#### 2.2.2 实体类类型参数

Mapper接口中抽象方法的声明

```Java
int insertEmployee(Employee employee);
```

SQL语句

```XML
<insert id="insertEmployee">
  insert into t_emp(emp_name,emp_salary) values(#{empName},#{empSalary})
</insert>
```

```xml
<!--
- key: 写 属性名 即可！
- Mybatis会根据#{}中传入的数据，加工成getXxx()方法，通过反射在实体类对象中调用这个方法，从而获取到对应的数据。填充到#{}解析后的问号占位符这个位置。
-->
```

#### 2.2.3 零散的多个简单类型参数

Mapper接口中抽象方法的声明

```Java
int updateEmployee(@Param("empId") Integer empId,@Param("empSalary") Double empSalary);
```

SQL语句

```XML
<update id="updateEmployee">
  update t_emp set emp_salary=#{empSalary} where emp_id=#{empId}
</update>
```

```xml
<!-- 场景3: 零散的多个 简单类型 参数 - key 的写法
           方案1 - (推荐) 注解指定 @Param注解  指定多个简单参数的 key  key = @Param("value值");
           方案2 - mybatis 默认机制
                   机制1 - arg0 arg1 ... 形参从左到右依次对应  无需使用@Param注解
                          例： (name,salary) 两个参数：name -对应-> key = arg0; salary -对应-> key = arg1;
                   机制2 - param1 param2 ... 起始坐标从 1 开始，arg 是从 0 开始的；
-->
```

#### 2.2.4 Map 类型参数

Mapper接口中抽象方法的声明

```Java
int updateEmployeeByMap(Map<String, Object> paramMap);
```

SQL语句

```XML
<update id="updateEmployeeByMap">

  update t_emp set emp_salary=#{empSalaryKey} where emp_id=#{empIdKey}

</update>
```

junit测试

```Java
private SqlSession session;
//junit5会在每一个@Test方法前执行@BeforeEach方法
@BeforeEach
public void init() throws IOException {
    session = new SqlSessionFactoryBuilder()
            .build(
                    Resources.getResourceAsStream("mybatis-config.xml"))
            .openSession();
}

@Test
public void testUpdateEmpNameByMap() {
  EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
  Map<String, Object> paramMap = new HashMap<>();
  paramMap.put("empSalaryKey", 999.99);
  paramMap.put("empIdKey", 5);
  int result = mapper.updateEmployeeByMap(paramMap);
  log.info("result = " + result);
}

//junit5会在每一个@Test方法后执行@@AfterEach方法
@AfterEach
public void clear() {
    session.commit();
    session.close();
}

```

```xml
<!-- 场景4: Map 类型 参数 - HashMap<String key,Object value> 通过往哈希表中存值传参 - key = map 的 key 即可！ -->
```



### 2.3 数据输出 Output

> [!NOTE]
>
> 数据输出总体上有两种形式：
>
> 1. **增删改操作返回的受影响行数**：直接使用 int 或 long 类型接收即可，不需要定义返回类型；
> 2. **查询操作的查询结果**：resultType 的写法
>
> 
>
> 所以主要任务：
>
> - **“如何指定查询的（返回值）输出数据类型”** 即可！
>
> - 并且插入场景下，实现 **主键数据返回显示**！



#### 2.3.🌟 定义别名

**目的：减少反复的无意义的重复 Java 类型 的 全限定类名书写**

##### 2.3.*.1 直接定义别名：类型别名（typeAliases）

在 mybatis-config.xml 全局配置文件中定义

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。例如：

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

当这样配置时，`Blog` 可以用在任何使用 `domain.blog.Blog` 的地方。

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

每一个在包 `domain.blog` 中的 Java Bean，**在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名**。 比如 `domain.blog.Author` 的别名为 `author`；**若有注解，则别名为其注解值**。见下面的例子：

```java
@Alias("author")
public class Author {
    ...
}
```



##### 2.3.*.2 “包名 + @Alias 注解” 方式定义别名

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

```XML
<typeAliases> <package name="domain.blog"/> </typeAliases>
```

每一个在包 `domain.blog` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `domain.blog.Author` 的别名为 `author`；若有注解，则别名为其注解值。见下面的例子：

```Java
@Alias("author")
public class Author {
    ...
}
```

##### 2.3.*.3 Mybatis为 常见的 Java 类型 内建的类型别名

⚠️都是**不区分大小写**的。注意，为了应对原始类型的命名重复，采取了特殊的命名风格。

| 别名                      | 映射的类型 |
| ------------------------- | ---------- |
| _byte                     | byte       |
| _char (since 3.5.10)      | char       |
| _character (since 3.5.10) | char       |
| _long                     | long       |
| _short                    | short      |
| _int                      | int        |
| _integer                  | int        |
| _double                   | double     |
| _float                    | float      |
| _boolean                  | boolean    |
| string                    | String     |
| byte                      | Byte       |
| char (since 3.5.10)       | Character  |
| character (since 3.5.10)  | Character  |
| long                      | Long       |
| short                     | Short      |
| int                       | Integer    |
| integer                   | Integer    |
| double                    | Double     |
| float                     | Float      |
| boolean                   | Boolean    |
| date                      | Date       |
| decimal                   | BigDecimal |
| bigdecimal                | BigDecimal |
| biginteger                | BigInteger |
| object                    | Object     |
| object[]                  | Object[]   |
| map                       | Map        |
| hashmap                   | HashMap    |
| list                      | List       |
| arraylist                 | ArrayList  |
| collection                | Collection |

**规律小结**

```
mybatis 给我们提供了72种默认的别名！这些都是我们常用的 Java 数据类型：
- 基本数据类型：int -> _int  double -> _double （加_下划线）
- 包装数据类型 Integer Double -> int integer double （大写开头变小写）
- 集合数据类型 Map List HashMap -> map list hashmap（小写即可）
```



#### 2.3.🌟 数据库字段 与 实体类属性对应关系

##### 2.3.*.1 别名对应

将字段的别名设置成和实体类属性一致。

```XML
<!-- 编写具体的SQL语句，使用id属性唯一的标记一条SQL语句 -->
<!-- resultType属性：指定封装查询结果的Java实体类的全类名 -->
<select id="selectEmployee" resultType="com.boyan.pojoy.Employee">

  <!-- Mybatis负责把SQL语句中的#{}部分替换成“?”占位符 -->
  <!-- 给每一个字段设置一个别名，让别名和Java实体类中属性名一致 -->
  select emp_id empId,emp_name empName,emp_salary empSalary from t_emp where emp_id=#{maomi}

</select>
```

    - 关于实体类属性的约定： getXxx()方法、setXxx()方法把方法名中的get或set去掉，首字母小写。

##### 2.3.*.2 MyBatis全局配置自动识别驼峰式命名规则

- MyBatis 设置开启 数据库 _ -> Java驼峰命名 的自动映射 ⚠️. 设置之后就不需要定义别名啦！只要符合规则会自动映射。

- mybatis.org 官方描述：https://mybatis.org/mybatis-3/zh_CN/configuration.html#settings

| 设置名                   | 描述                                                         | 有效值        | 默认值 |
| ------------------------ | ------------------------------------------------------------ | ------------- | ------ |
| mapUnderscoreToCamelCase | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 | true \| false | False  |

在Mybatis全局配置文件(mybatis-config.xml)加入如下配置：

```XML
<!-- 使用settings对Mybatis全局进行设置 -->
<settings>

  <!-- 将xxx_xxx这样的列名自动映射到xxXxx这样驼峰式命名的属性名 -->
  <setting name="mapUnderscoreToCamelCase" value="true"/>

</settings>
```

SQL语句中可以不使用别名

```XML
<!-- Employee selectEmployee(Integer empId); -->
<select id="selectEmployee" resultType="com.boyan.pojo.Employee">

  select emp_id,emp_name,emp_salary from t_emp where emp_id=#{empId}

</select>
```

##### 2.3.*.3 使用resultMap 标签（补充）

使用resultMap标签：先定义对应关系，再在后面的SQL语句中引用这个对应关系

```XML
<!-- resultMap 标签初体验：先定义一个对应关系，再在后面的 SQL 语句中引用这个对应关系 -->
<resultMap id="selectEmployeeByResultMap" type="com.boyan.pojo.Employee03">
    <!-- id 标签（主键映射关系） - 设置主键列和主键属性之间的对应关系
                - column 属性 - 用于指定字段名；
                - property 属性 - 用于指定Java实体类属性名； -->
    <id column="emp_id" property="empId"/>

    <!-- result 标签（普通列映射关系） - 设置普通字段和Java实体类属性之间的关系 -->
    <result column="emp_name" property="empName"/>
    <result column="emp_salary" property="empSalary"/>
</resultMap>
<select id="selectEmployeeByRM" resultMap="selectEmployeeByResultMap">
    select emp_id,emp_name,emp_salary from t_emp where emp_id=#{empId}
</select>
```

##### 总结：列名和属性名不一致如何解决》3个方案

方案1: 别名

方案2: 在 mybatis-config.xml 全局开启驼峰式映射规则

方案3: resultMap 和 resultType 二选一

 - resultMap：自定义映射，**可以深层次可以浅层次**；

 - resultType：定义实体类路径，按照规则自动映射，⚠️但**只能映射一层结构**！

   - 多层结构（多表情况）二选一只能 resultMap 自定义

      eg. Order

     - orderId
     - orderName
     - OrderItem orderItem
       - itemId



#### 2.3.1 单个简单类型 输出

Mapper接口中的抽象方法

```Java
int selectEmpCount();
```

SQL语句

```XML
<select id="selectEmpCount" resultType="int">
  select count(*) from t_emp
</select>
<!-- 
- 细节解释：
- select标签，通过resultType指定查询返回值类型！
- resultType = "全限定符 or 别名 or 如果是返回集合类型，写范型类型即可"
-->
```

JUnit测试

```Java
  @Test
  public void testEmpCount() {
    EmployeeMapper employeeMapper = session.getMapper(EmployeeMapper.class);
    int count = employeeMapper.selectEmpCount();
    log.info("count = " + count);
  }
```



#### 2.3.2 单个实体类型输出

Mapper接口的抽象方法

```Java
Employee selectEmployee(Integer empId);
```

SQL语句

```XML
<!-- 编写具体的SQL语句，使用id属性唯一的标记一条SQL语句 -->
<!-- resultType属性：指定封装查询结果的Java实体类的全类名 -->
<select id="selectEmployee" resultType="com.boyan.pojo.Employee">

  <!-- Mybatis负责把SQL语句中的#{}部分替换成“?”占位符 -->
  <!-- 给每一个字段设置一个别名，让别名和Java实体类中属性名一致 -->
  select emp_id empId,emp_name empName,emp_salary empSalary from t_emp where emp_id=#{maomi}

</select>
```

通过给数据库表字段加别名，让查询结果的每一列都和Java实体类中属性对应起来。

增加全局配置自动识别对应关系

在 Mybatis 全局配置文件中，做了下面的配置，select语句中可以不给字段设置别名

```XML
<!-- 在全局范围内对Mybatis进行配置 -->
<settings>

  <!-- 具体配置 -->
  <!-- 从org.apache.ibatis.session.Configuration类中可以查看能使用的配置项 -->
  <!-- 将mapUnderscoreToCamelCase属性配置为true，表示开启自动映射驼峰式命名规则 -->
  <!-- 规则要求数据库表字段命名方式：单词_单词 -->
  <!-- 规则要求Java实体类属性名命名方式：首字母小写的驼峰式命名 -->
  <setting name="mapUnderscoreToCamelCase" value="true"/>

</settings>
```



#### 2.3.3 返回 Map 数据类型（返回值无法封装到实体类时）

**⚠️适用场景：SQL查询返回的各个字段综合起来并不和任何一个现有的实体类对应，没法封装到实体类对象中时。只要能够封装成实体类类型的，就不使用Map类型。**

Mapper接口的抽象方法

```Java
/**
 * 4 - 查询 - Map<String, Object> 类型 返回值
 * 方法：返回工资最高的员工的姓名和他的工资
 */
@MapKey("empName")  // 选
Map<String,Object> selectEmpNameAndMaxSalary();
```

SQL语句

```XML
<!-- 场景 4. 返回 Map<String, Object> 类型  -->
<!-- Map<String,Object> selectEmpNameAndMaxSalary(); -->
<select id="selectEmpNameAndMaxSalary" resultType="map">
  SELECT
    emp_name 员工姓名,
    emp_salary 员工工资,
    (SELECT AVG(emp_salary) FROM t_emp) 部门平均工资
  FROM t_emp WHERE emp_salary=(
    SELECT MAX(emp_salary) FROM t_emp
  )
  <!-- 返回工资最高的员工的姓名和他的工资 -->
</select>
```

JUnit测试

```Java
/**
 * 4 - 查询 - Map<String, Object> 类型 返回值
 * 方法：返回工资最高的员工的姓名和他的工资
 */
@Test
public void test_04(){
    EmployeeMapper employeeMapper = session.getMapper(EmployeeMapper.class);
    // Map<Key,Value>
    Map<String, Object> resultMap = employeeMapper.selectEmpNameAndMaxSalary();
    // Set<Key>
    Set<Map.Entry<String, Object>> entrySet = resultMap.entrySet();
    // for-each 取值
    for (Map.Entry<String, Object> entry : entrySet) {
        String key = entry.getKey();
        Object value = entry.getValue();
        System.out.println(key + ", " + value);
    }
}
```



#### 2.3.4 返回 List 数据类型（返回多个实体类对象时）

**⚠️ 适用场景：查询结果返回多个实体类对象，希望把多个实体类对象放在List集合中返回。**

**⚠️ 此时不需要任何特殊处理，在resultType属性中还是设置实体类类型即可。**

Mapper接口中抽象方法

```Java
List<Employee> selectAll();
```

SQL语句

```XML
<!-- 场景 5. 返回 List<Object> 类型  -->
<select id="selectAll" resultType="com.boyan.pojo.Employee03"> 
<!-- 此时不需要任何特殊处理，在resultType属性中还是设置实体类类型即可 -->
    select * from t_emp
</select>
```

JUnit测试

```Java
/**
 * 5 - 查询 - List<Object> 类型 返回值
 */
@Test
public void test_05(){
    EmployeeMapper employeeMapper = session.getMapper(EmployeeMapper.class);
    // List<Object>
    List<Employee03> employeeList = employeeMapper.selectAll();
    // for-each 取值
    for (Employee03 employee:employeeList) {
        System.out.println(employee);
    }
}
```



#### 2.3.5 返回数据库表 主键值(Primary_Key)

##### 2.3.5.1 自增长类型主键 AUTO_INCREMENT

Mapper接口中的抽象方法

```Java
int insertEmployee(Employee employee);	// 实现员工插入，并返回新插入的员工实体的id
```

SQL语句

```XML
<!--
- 场景：主键回显（获取插入数据的主键）
- 1 - 自增长类型主键 mysql auto_increment
- 函数：实现员工插入，并返回新插入的员工实体的id
-->
<insert id="insertEmployee" useGeneratedKeys="true" keyColumn="true" keyProperty="empId">
    insert into t_emp(emp_name,emp_salary) values(#{empName},#{empSalary})
    <!-- 1 - useGeneratedKeys 属性：“使用生成的主键” -> 自动生成一个主键插入到数据库(auto_increment) -->
    <!-- 2 - keyProperty 属性：指定主键在实体类对象中对应的属性名，Mybatis会将拿到的主键值存入这个属性 -->
  	<!-- 3 - keyColumn 属性：当数据库自动生成主键时，keyColumn 告诉 MyBatis 哪一列是生成的主键列。这在一些复杂的场景中非常有用，比如表中有多个自增列或者需要明确指定主键列名时。（这里可以不用）-->
</insert>
```

JUnit测试

```Java
@Test
public void test_06(){
    EmployeeMapper employeeMapper = session.getMapper(EmployeeMapper.class);
    Employee03 employee = new Employee03();
    // 这里没有设置 id，需要自增长
    employee.setEmpName("john");
    employee.setEmpSalary(666.66);
    employeeMapper.insertEmployee(employee);
    System.out.println(employee.getEmpId());
}
```

**注意** ⚠️Mybatis是将自增主键的值设置到**实体类对象**中，而不是以Mapper接口方法返回值的形式返回。



###### 传入的是 Employee 实体，如何对应 empName 和 empSalary 的？

> 在 MyBatis 中，当你传递一个实体类对象作为参数时，MyBatis 会自动将对象的属性值映射到 SQL 语句中的对应占位符（如 `#{empName}` 和 `#{empSalary}`）。这是通过反射机制实现的。具体来说，当你调用 `insertEmployee(Employee03 employee)` 方法时，MyBatis 会根据实体类 `Employee03` 的属性名匹配 SQL 语句中的占位符，并将属性值注入到 SQL 语句中。



###### openSession 事务提交（DML操作必须提交）

方法一：openSession 自动开启事务，不会自动提交，需要手动执行事务提交：sqlSession.commit(); 

方法二：openSession(true) 自动开启事务，自动提交事务！不需要手动提交commit；



##### 2.3.5.2 非自增长类型主键 (String类型 主键eg.id)

而对于不支持自增型主键的数据库（例如 Oracle）或者字符串类型主键，则可以使用 selectKey 子元素：selectKey 元素将会首先运行，id 会被设置，然后插入语句会被调用！即**通过 selectKey 先指定 id 补全，再执行 sql 语句将各属性完整的插入数据库中，生成一条数据项**。

使用 `selectKey` 帮助插入UUID作为字符串类型主键示例：

```XML
<insert id="insertUser" parameterType="User">
    <selectKey keyProperty="id" resultType="java.lang.String"
        order="BEFORE">
        SELECT UUID() as id
    </selectKey>
    INSERT INTO user (id, username, password) 
    VALUES (
        #{id},
        #{username},
        #{password}
    )
</insert>

```

在上例中，我们定义了一个 `insertUser` 的插入语句来将 `User` 对象插入到 `user` 表中。我们使用 `selectKey` 来查询 UUID 并设置到 `id` 字段中。

```java
// SELECT UUID() as id
// 的背后 Java 逻辑
String id = UUID.randomUUID().toString().replaceAll("-"," ");
```

通过 `keyProperty` 属性来指定查询到的 UUID 赋值给对象中的 `id` 属性，而 `resultType` 属性指定了 UUID 的类型为 `java.lang.String`。

需要注意的是，我们将 `selectKey` 放在了插入语句的前面，这是因为 MySQL 在 `insert` 语句中只支持一个 `select` 子句，而 `selectKey` 中查询 UUID 的语句就是一个 `select` 子句，因此我们需要将其放在前面。

最后，在将 `User` 对象插入到 `user` 表中时，我们直接使用对象中的 `id` 属性来插入主键值。

使用这种方式，我们可以方便地插入 UUID 作为字符串类型主键。当然，还有其他插入方式可以使用，如使用Java代码生成UUID并在类中显式设置值等。需要根据具体应用场景和需求选择合适的插入方式。



#### 2.3.6 CRUD 完整强化练习 

详见本项目 mybatis-base-crud-04 模块内完整代码实现，主要实现了对于上述内容的整合操作。

#### 2.3.7 mapperXML 标签总结

MyBatis 的真正强大在于它的语句映射，这是它的魔力所在。由于它的异常强大，映射器的 XML 文件就显得相对简单。如果拿它跟具有相同功能的 JDBC 代码进行对比，你会立即发现省掉了将近 95% 的代码。MyBatis 致力于减少使用成本，让用户能更专注于 SQL 代码。

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

- `insert` – 映射插入语句。
- `update` – 映射更新语句。
- `delete` – 映射删除语句。
- `select` – 映射查询语句。

##### select标签

  MyBatis 在查询和结果映射做了相当多的改进。一个简单查询的 select 元素是非常简单：

```XML
<select id="selectPerson" 
resultType="hashmap" resultMap="自定义结构"> SELECT * FROM PERSON WHERE ID = #{id} </select>
```

  这个语句名为 selectPerson，接受一个 int（或 Integer）类型的参数，并返回一个 HashMap 类型的对象，其中的键是列名，值便是结果行中的对应值。

  注意参数符号：#{id}  ${key}

  MyBatis 创建一个预处理语句（PreparedStatement）参数，在 JDBC 中，这样的一个参数在 SQL 中会由一个“?”来标识，并被传递到一个新的预处理语句中，就像这样：

```Java
// 近似的 JDBC 代码，非 MyBatis 代码...
String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
PreparedStatement ps = conn.prepareStatement(selectPerson);
ps.setInt(1,id);
```

  select 元素允许你配置很多属性来配置每条语句的行为细节：

| 属性            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| `id`            | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `resultType`    | 期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 resultType 和 resultMap 之间只能同时使用一个。 |
| `resultMap`     | 对外部 resultMap 的命名引用。结果映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 resultType 和 resultMap 之间只能同时使用一个。 |
| `timeout`       | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| `statementType` | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |

##### insert, update 和 delete标签

  数据变更语句 insert，update 和 delete 的实现非常接近：

```XML
<insert
  id="insertAuthor"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  statementType="PREPARED"
  timeout="20">
```

| 属性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `id`               | 在命名空间中唯一的标识符，可以被用来引用这条语句。           |
| `timeout`          | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。 |
| `statementType`    | 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `useGeneratedKeys` | （仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false。 |
| `keyProperty`      | （仅适用于 insert 和 update）指定能够唯一识别对象的属性，MyBatis 会使用 getGeneratedKeys 的返回值或 insert 语句的 selectKey 子元素设置它的值，默认值：未设置（`unset`）。如果生成列不止一个，可以用逗号分隔多个属性名称。 |
| `keyColumn`        | （仅适用于 insert 和 update）设置生成键值在表中的列名，在某些数据库（像 PostgreSQL）中，当主键列不是表中的第一列的时候，是必须设置的。如果生成列不止一个，可以用逗号分隔多个属性名称。 |





## 三、MyBatis 多表映射：ResultMap 自定义映射

> [!TIP]
>
> 这部分是重点但不是难点，**我们的学习目标是：**
>
> 1. 多表查询语句使用
>
> 2. 多表结果承接实体类设计
>
> 3. 使用ResultMap完成多表结果映射

------

**多表基本步骤**

1. 多表查询的sql语句，需要自己编写，（内连接 外连接...)
2. 自己设计存储数据的实体类，用来承接多个表查询的结果
3. 自己定义结果集映射（resultMap）

其他内容和单表的一模一样～！

### 3.1  多表查询结果实体类 设计方案（对一 / 对多 映射）

**Intro：多表关系回顾：（双向查看，多表的表关系是双向综合的结果）**

- 【一对一】夫妻关系，人和身份证号

- 【一对多| 多对一】用户和用户的订单，锁和钥匙

- 【多对多】老师和学生，部门和员工


**多表查询结果实体类设计关系：（单向查看，多表的查询是单向的，没有表关系设计那么复杂）**

- 对一 ： 夫妻一方对应另一方，订单对应用户都是对一关系


```Java
public class Customer 
{
  private Integer customerId;
  private String customerName;
}

public class Order 
{
  private Integer orderId;
  private String orderName;
  private Customer customer;// 体现的是对一的关系
}  
```
- 对多：用户对应的订单，讲师对应的学生或者学生对应的讲师都是对多关系：


```Java
public class Customer 
{
  private Integer customerId;
  private String customerName;
  private List<Order> orderList; // 体现的是对多的关系
}

public class Order
{
  private Integer orderId;
  private String orderName;
  private Customer customer; // 体现的是对一的关系
}
```

#### 多表结果实体类设计小技巧！

- 对一，属性中包含对方对象

- 对多，属性中包含对方对象集合

#### 三个小建议！

- 只有真实发生多表查询时，才需要设计和修改实体类，否则**不提前设计和修改实体类**！

- 无论多少张表联查，**实体类设计都是两两考虑**! 每次只从场景中提取一对关系考虑！

- 在查询映射的时候，**只需要关注本次查询相关的属性**！例如：查询订单和对应的客户，就不要关注客户中的订单集合！

------

#### 实战准备

- 项目模块：mybatis-high-multitable-05

- 新建 query 查询控制台，运行 sql 语句，准备数据库表（db：mybtais-example）

  ```sql
  CREATE TABLE `t_customer` (`customer_id` INT NOT NULL AUTO_INCREMENT, `customer_name` CHAR(100), PRIMARY KEY (`customer_id`) );
  
  CREATE TABLE `t_order` ( `order_id` INT NOT NULL AUTO_INCREMENT, `order_name` CHAR(100), `customer_id` INT, PRIMARY KEY (`order_id`) ); 
  
  INSERT INTO `t_customer` (`customer_name`) VALUES ('c01');
  
  INSERT INTO `t_order` (`order_name`, `customer_id`) VALUES ('o1', '1');
  INSERT INTO `t_order` (`order_name`, `customer_id`) VALUES ('o2', '1');
  INSERT INTO `t_order` (`order_name`, `customer_id`) VALUES ('o3', '1'); 
  ```

- 实体类设计

```java
@Data
public class Customer {
    private Integer customerId;
    private String customerName;
  
    private List<Order> orderList; // 体现的是对多的关系
}

@Data
public class Order {
    private Integer orderId;
    private String orderName;
    private Integer customerId;    
    
    private Customer customer;    // 体现的是对一的关系
}
```

##### Tip. 外键约束后置

> [!TIP]
>
> ⚠️ **实际开发时，一般在开发过程中，不给数据库表设置外键约束。 原因是避免调试不方便。 一般是功能开发完成，再加外键约束检查是否有bug。**
>
> ------
>
> 外键约束（Foreign Key Constraint）是一种数据库约束，用于维护表与表之间的数据一致性和参照完整性。它通过在一个表中的某列或某几列设置外键，使其引用另一个表中的主键或唯一键，来确保两个表之间的数据关系始终有效。
>
> **外键约束的作用**
>
> 1. **保持数据一致性**：外键约束确保引用的值在父表中是存在的，从而避免了孤立数据和无效引用。例如，如果有一个订单表和一个客户表，通过外键约束可以确保每个订单都对应一个有效的客户。
>
> 2. **参照完整性**：外键约束强制执行父子表之间的引用完整性，防止删除或更新父表中被子表引用的数据。例如，如果某个客户有相关订单记录，外键约束可以防止删除这个客户记录，除非相关的订单记录被处理或删除。
>
> **外键约束的示例**
>
> 假设我们有两个表：`Customers`（客户表）和`Orders`（订单表）。我们希望每个订单都必须有一个有效的客户，因此我们可以在`Orders`表中设置一个外键约束。
>
> ```sql
> -- 创建Customers表
> CREATE TABLE Customers (
>     CustomerID INT PRIMARY KEY,
>     Name VARCHAR(100)
> );
> 
> -- 创建Orders表，并设置CustomerID为外键
> CREATE TABLE Orders (
>     OrderID INT PRIMARY KEY,
>     OrderDate DATE,
>     CustomerID INT,
>     FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
> );
> ```
>
> 在这个示例中，`Orders`表中的`CustomerID`列是一个外键，它引用了`Customers`表中的`CustomerID`列。**这意味着，`Orders`表中的每个`CustomerID`值必须在`Customers`表中存在，否则数据库会拒绝插入或更新这些数据。**
>
> 💡**为什么在开发过程中暂时不设置外键约束**
>
> 在开发过程中，开发人员可能需要频繁地插入、更新或删除数据来测试功能。如果一开始就设置了外键约束，会对这些操作产生限制，导致开发和调试不便。因此，开发人员通常选择在开发初期不设置外键约束，等功能开发完成并且数据结构稳定后再添加外键约束，以确保数据一致性和完整性。

##### Tip. @Data lombok 的实体类注解

> [!TIP]
>
> ```java
> import lombok.Data;
> @Data
> ```
>
> @Data 是 Lombok 库中的一个注解，用于简化 Java 类的编写。通过使用 @Data 注解，Lombok 可以自动生成以下：
>
> - Getter 方法：为每个非静态字段生成一个公共的 getter 方法。
> - Setter 方法：为每个非静态字段生成一个公共的 setter 方法。
> - toString 方法：生成一个覆盖默认 toString 方法，包括类名和每个字段的值。
> - equals 和 hashCode 方法：生成覆盖默认的 equals 和 hashCode 方法，基于类中的所有非静态字段。
> - RequiredArgsConstructor 方法：生成一个包含所有 final 字段和带有 @NonNull 注解字段的构造函数。



### 3.2 实战操作：对一映射 (association 和 javaType)

1. 需求说明

    **根据ID查询订单，以及订单关联的用户的信息！**
2. OrderMapper接口

```Java
public interface OrderMapper 
{
  Order selectOrderWithCustomer(Integer orderId);
}
```
3. OrderMapper.xml配置文件

```XML
<!-- 创建resultMap实现“对一”关联关系映射
         1）id属性：通常设置为这个resultMap所服务的那条SQL语句的id加上“ResultMap” 
         2）type属性：要设置为这个resultMap所服务的那条SQL语句最终要返回的类型 
-->
<resultMap id="selectOrderWithCustomerResultMap" type="order">
  
  <!-- StepOne：先配置Order自身属性和字段的对应关系 -->
  		<!-- order 主键 -->
  <id column="order_id" property="orderId"/>
  		<!-- order 普通属性 -->
  <result column="order_name" property="orderName"/>
  <result column="customer_id" property="customerId"/>

  <!-- StepTwo：进一步配置 Order - Customer 的“对一”关联查询关系 - 使用 association 标签
         1) property 属性：在 Order 类中对一的一端进行引用时使用的属性名
  			 2) javaType 属性：一的一端类的全类名
  -->
  <association property="customer" javaType="customer"><!-- association and javaType -->
    <!-- 配置 Customer 类的属性和字段名之间的对应关系 -->
    		<!-- customer 主键 -->
    <id column="customer_id" property="customerId"/>
    		<!-- customer 普通属性 -->
    <result column="customer_name" property="customerName"/>
  </association>
</resultMap>


<select id="selectOrderWithCustomer" resultMap="selectOrderWithCustomerResultMap">
  SELECT order_id,order_name,c.customer_id,customer_name
  FROM t_order o
  LEFT JOIN t_customer c
  ON o.customer_id=c.customer_id
  WHERE o.order_id=#{orderId}
</select>
```

4. Mybatis全局注册Mapper文件

```XML
<mappers>
  <!-- 在mapper标签的resource属性中指定Mapper配置文件以“类路径根目录”为基准的相对路径 -->
  <mapper resource="mappers/OrderMapper.xml"/>
</mappers>
```
5. junit测试程序

```Java
@Slf4j
public class MyBatisTest {

    private SqlSession session;
   
    @BeforeEach
    public void init() throws IOException {
        session = new SqlSessionFactoryBuilder()
                .build(
                        Resources.getResourceAsStream("mybatis-config.xml"))
                .openSession();
    }
		@AfterEach
    public void clear() {
        session.commit();
        session.close();
    }
  
    @Test
    public void testRelationshipToOne() 
    {
      OrderMapper orderMapper = session.getMapper(OrderMapper.class);
      // 查询Order对象，检查是否同时查询了关联的Customer对象
      Order order = orderMapper.selectOrderWithCustomer(2);
      log.info("order = " + order);
    }
}
```
6. 关键词

    在“对一”关联关系中，我们的配置比较多，但是关键词就只有：**association**和**javaType**



### 3.3 实战操作：对多映射(collection 和 ofType)

1. 需求说明

    查询客户和客户关联的订单信息！
2. CustomerMapper接口

```Java
public interface CustomerMapper 
{
  Customer selectCustomerWithOrderList(Integer customerId);
}
```
3. CustomerMapper.xml文件

```xml
<!-- 配置 resultMap 实现从 Customer 到 OrderList 的“对多”关联关系 -->
<resultMap id="selectCustomerWithOrderListResultMap" type="customer">
  
  <!-- StepOne：先配置 Customer 自身属性和字段的对应关系 -->
  <id column="customer_id" property="customerId"/>
  <result column="customer_name" property="customerName"/>

  <!-- StepTwo：进一步配置 Customer - Order 的“对多”关联查询关系 - 使用 collection 标签
         1) property 属性：在Customer类中，关联“多”的一端的属性名
  			 2) ofType属性：集合属性中元素的类型
  -->
  <collection property="orderList" ofType="order"><!-- collection and ofType -->
    <!-- 映射集合中元素实体的属性：Order -->
    <id column="order_id" property="orderId"/>
    <result column="order_name" property="orderName"/>
  </collection>
</resultMap>


<select id="selectCustomerWithOrderList" resultMap="selectCustomerWithOrderListResultMap">
  SELECT c.customer_id,c.customer_name,o.order_id,o.order_name
  FROM t_customer c
  LEFT JOIN t_order o
  ON c.customer_id=o.customer_id
  WHERE c.customer_id=#{customerId}
</select>
```

4. Mybatis全局注册Mapper文件

```XML
<!-- 注册Mapper配置文件：告诉Mybatis我们的Mapper配置文件的位置 -->
<mappers>
  <!-- 在mapper标签的resource属性中指定Mapper配置文件以“类路径根目录”为基准的相对路径 -->
  <mapper resource="mappers/OrderMapper.xml"/>
  <mapper resource="mappers/CustomerMapper.xml"/>
</mappers>
```
5. junit测试程序

```Java
@Test
public void testRelationshipToMulti() 
{
  CustomerMapper customerMapper = session.getMapper(CustomerMapper.class);
  // 查询Customer对象同时将关联的Order集合查询出来
  Customer customer = customerMapper.selectCustomerWithOrderList(1);
  log.info("customer.getCustomerId() = " + customer.getCustomerId());
  log.info("customer.getCustomerName() = " + customer.getCustomerName());
  List<Order> orderList = customer.getOrderList();
  for (Order order : orderList) {
    log.info("order = " + order);
  }
}
```
6. 关键词

    在“对多”关联关系中，同样有很多配置，但是提炼出来最关键的就是：**collection 和 ofType**



### 3.4 多表映射优化：全局配置省略\<result>

**修改mybati-sconfig.xml:**

```XML
<!-- 开启resultMap自动映射 -->
<setting name="autoMappingBehavior" value="FULL"/>
```

| setting属性         | 属性含义                                                     | 可选值              | 默认值  |
| ------------------- | ------------------------------------------------------------ | ------------------- | ------- |
| autoMappingBehavior | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）。 | NONE, PARTIAL, FULL | PARTIAL |


我们可以将autoMappingBehavior设置为full,进行多表resultMap映射的时候，可以省略符合列和属性命名映射规则（列名=属性名，或者开启驼峰映射也可以自定映射）的result标签！

开启优化配置后，两个配置文件分别更新为⬇️（均可以 **删去 result**(普通属性) 的配置）

- OrderMapper.xml（对一）

```xml
<!-- namespace == mapper接口类的全限定名 -->
<mapper namespace="com.boyan.mapper.OrderMapper">

    <resultMap id="selectOrderWithCustomerResultMap" type="order">
        <id column="order_id" property="orderId"/><!-- Order 主键 -->
        
        <!-- Order - Customer 对一映射 查询结果实体关系：association标签 javaType属性：实体类型 -->
        <association property="customer" javaType="customer">
            <id column="customer_id" property="customerId"/><!-- Customer 主键 -->
        </association>
    </resultMap>

    <select id="selectOrderWithCustomer" resultMap="selectOrderWithCustomerResultMap">
        <!-- sql -->
        SELECT order_id,order_name,c.customer_id,customer_name
        FROM t_order o
        LEFT JOIN t_customer c
        ON o.customer_id=c.customer_id
        WHERE o.order_id=#{orderId}
    </select>
</mapper>
```

- CustomerMapper.xml（对多）

```xml
<!-- namespace == mapper接口类的全限定名 -->
<mapper namespace="com.boyan.mapper.CustomerMapper">

    <resultMap id="selectCustomerWithOrderListResultMap" type="customer">
        <id property="customerId" column="customer_id"/><!-- Customer 主键 -->
        
        <!-- Customer - Order 对多映射 查询结果实体关系：collection标签 ofType属性：集合中单个实体类型 -->
        <collection property="orderList" ofType="order">
            <!-- 集合中数据元素：Order 类型-->
            <id column="order_id" property="orderId"/><!-- Order 主键 -->
        </collection>

    </resultMap>
    <select id="selectCustomerWithOrderList" resultMap="selectCustomerWithOrderListResultMap">
        <!-- sql 语句-->
        SELECT c.customer_id,c.customer_name,o.order_id,o.order_name
        FROM t_customer c
        LEFT JOIN t_order o
        ON c.customer_id=o.customer_id
        WHERE c.customer_id=#{customerId}
    </select>
</mapper>
```

经过优化后，重点是会设计实体类就好了！配置就很简单了～



### 3.5 多表映射总结

| 关联关系 | 配置项关键词                              | 所在配置文件和具体位置            |
| -------- | ----------------------------------------- | --------------------------------- |
| 对一     | association标签/javaType属性/property属性 | Mapper配置文件中的resultMap标签内 |
| 对多     | collection标签/ofType属性/property属性    | Mapper配置文件中的resultMap标签内 |



## 四、MyBatis 动态语句

### 4.1 动态语句 sql标签

现实需求：**按照很多查询条件进行查询的情况**，比如智联招聘的职位搜索等。其中经常出现很多条件不取值的情况，如何实现？

现有问题：根据不同条件拼接 SQL 语句的痛苦，而利用MyBatis3实现的动态 SQL，可以彻底摆脱这种痛苦。

动态语句 sql标签

1.  \<where> - \<if>
2. \<set> - \<if>
3. \<trim> - \<if> 
4. \<choose> - \<when/otherwise>
5.  foreach
6. \<include>

下面依照案例详细的解释一下用法（理解）



#### 4.1.1 where - if (查询 select)

```xml
<!-- Mapper 接口 -  List<Employee> query(@Param("name") String name, @Param("salary") Double salary); -->
<select id="query" resultType="employee">
    select * from t_emp
    <where>
        <if test="name != null">
            emp_name = #{name}
        </if>
        <if test="salary != null and salary &gt; 100">
            and emp_salary = #{salary}
        </if>
    </where>
</select>
<!--
  if - 判断传入的参数，根据判断结果确定最终是否添加语句
       test = “条件判断” 属性 - true 添加 / false 删去
              条件编写 - 关键词 and or &gt;(>) &lt;(<)

  where - 作用 ： 动态管理 where and or 关键字
       1） 根据 if 的 test 结果 自动删去 if 中 多余的 and ｜ or 关键字
       2） 替换 sql 语句中的 where，当 where 内部有任何一个 if 满足时，自动添加 where 关键字，没有满足的时自动去掉 where
 -->

<!--
     第一种情况：所有条件都满足 WHERE emp_name=? or emp_salary>?
     第二种情况：部分条件满足 WHERE emp_salary>?
     第三种情况：所有条件都不满足 没有where子句
 -->
```



#### 4.1.2  set - if (更新 update)

```xml
<!-- Mapper 接口 - void updateEmployeeDynamic(Employee employee); -->
<update id="updateEmployeeDynamic">
    update t_emp
    <!-- set emp_name=#{empName}, emp_salary=#{empSalary} -->
    <!-- set - 1）使用set标签动态管理set子句，2）并且动态去掉两端多余的逗号 -->
    <set>
        <if test="empName != null">
            emp_name=#{empName},
        </if>
        <if test="empSalary &lt; 3000">
            emp_salary=#{empSalary},
        </if>
    </set>
    where emp_id=#{empId}
</update>
<!-- 没有set子句的update语句会导致SQL语法错误
     第一种情况：所有条件都满足 SET emp_name=?, emp_salary=?
     第二种情况：部分条件满足 SET emp_salary=?
     第三种情况：所有条件都不满足 update t_emp where emp_id=?
 -->
```



#### 4.1.3  trim - if (了解)

使用trim标签控制条件部分两端是否包含某些字符

- prefix属性：指定要动态添加的前缀
- suffix属性：指定要动态添加的后缀
- prefixOverrides属性：指定要动态去掉的前缀，使用“|”分隔有可能的多个值
- suffixOverrides属性：指定要动态去掉的后缀，使用“|”分隔有可能的多个值

```XML
<!-- Mapper 接口 - List<Employee> selectEmployeeByConditionByTrim(Employee employee) -->
<select id="selectEmployeeByConditionByTrim" resultType="employee">
    select emp_id,emp_name,emp_age,emp_salary,emp_gender
    from t_emp
    
    <!-- prefix属性指定要动态添加的前缀 -->
    <!-- suffix属性指定要动态添加的后缀 -->
    <!-- prefixOverrides属性指定要动态去掉的前缀，使用“|”分隔有可能的多个值 -->
    <!-- suffixOverrides属性指定要动态去掉的后缀，使用“|”分隔有可能的多个值 -->
    <!-- 当前例子用where标签实现更简洁，但是trim标签更灵活，可以用在任何有需要的地方 -->
    <trim prefix="where" suffixOverrides="and|or">
        <if test="empName != null">
            emp_name=#{empName} and
        </if>
        <if test="empSalary &gt; 3000">
            emp_salary>#{empSalary} and
        </if>
        <if test="empAge &lt;= 20">
            emp_age=#{empAge} or
        </if>
        <if test="empGender=='male'">
            emp_gender=#{empGender}
        </if>
    </trim>
</select>
```



#### 4.1.4  choose - when / otherwise (~= switch - case)

在多个分支条件中，仅执行一个。（**when/otherwise 与 if 的不同点**：只有一个满足，if：可以有多个满足）

- 从上到下依次执行条件判断
- 遇到的第一个满足条件的分支会被采纳
- 被采纳分支后面的分支都将不被考虑
- 如果所有的when分支都不满足，那么就执行otherwise分支

```XML
<!-- Mapper 接口 - List<Employee> selectEmployeeByConditionByChoose(Employee employee) -->
<select id="selectEmployeeByConditionByChoose" resultType="employee">
    select emp_id,emp_name,emp_salary from t_emp
    where
    <choose>
        <when test="empName != null">emp_name=#{empName}</when>
        <when test="empSalary &lt; 3000">emp_salary &lt; 3000</when>
        <otherwise>1=1</otherwise>
    </choose>
    
    <!--
     第一种情况：第一个when满足条件 where emp_name=?
     第二种情况：第二个when满足条件 where emp_salary < 3000
     第三种情况：两个when都不满足 where 1=1 执行了otherwise
     -->
</select>
```



#### 4.1.5 foreach 批量操作 *(重要)

##### 🌟 基本用法 CRUD

```xml
<!-- 批量操作：查询query 删除delete 插入insert 更新update
			- List<Employee> queryBatch(@Param("ids")List<Integer> ids);
			- int deleteBatch(@Param("ids")List<Integer> ids);
			- int insertBatch(@Param("employeeList")List<Employee> employeeList);
			- int updateBatch(@Param("employeeList")List<Employee> employeeList);
-->

<select id="queryBatch" resultType="employee">
    select * from t_emp where emp_id in
    <!-- 批量遍历 -->
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
    <!--
    to 遍历的数据：(1,2,3)

    collection - "ids|arg0|list"
    open - 遍历之前要追加的字符串
    close - 遍历结束要追加的字符串
    separator - 每次遍历的分隔符号，很智能，如果是最后一次遍历不会追加
    item - 获取每个遍历项

    <foreach> 标签中是每条遍历的数据项，#{遍历数据项 == item 指定的 key}
    -->
</select>

<delete id="deleteBatch">
    delete from t_emp where id in
    <!-- 批量遍历 -->
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>

<insert id="insertBatch">
    insert into t_emp(emp_name,emp_salary) values
    <!-- 批量遍历 -->
    <foreach collection="employeeList" separator="," item="employee">
        (#{employee.emp_name}, #{employee.emp_salary})
        <!-- ("xx",200) , ("xx",200) , ("xx",200) ... -->
    </foreach>
</insert>

<update id="updateBatch">
    <!-- 等于多个遍历语句 -->
    <foreach collection="employeeList" item="emp">
        update t_emp set emp_name = #{employee.empName}, emp_salary = #{employee.empSalary} where emp_id = #{employee.empId};
    </foreach>
    <!-- 如果一个标签涉及多条语句，需要在数据库url中设置允许多条语句: 数据库url?allowMultiQueries=true （在mybatis-config.xml中添加就可以）-->
</update>
```



##### 🌟 批量更新时需要注意 -- update

上面批量插入的例子本质上是一条SQL语句，而实现批量更新则需要多条SQL语句拼起来，用分号分开。也就是一次性发送多条SQL语句让数据库执行。此时需要在数据库连接信息的URL地址中设置：...///数据库名**?allowMultiQueries=true



##### 🌟 关于foreach标签的collection属性

如果没有给接口中List类型的参数使用@Param注解指定一个具体的名字，那么在collection属性中默认可以使用collection或list来引用这个list集合。这一点可以通过异常信息看出来：

```XML
Parameter 'empList' not found. Available parameters are [arg0, collection, list]
```

在实际开发中，为了避免隐晦的表达造成一定的误会，**建议使用@Param注解明确声明变量的名称**，然后在foreach标签的collection属性中按照@Param注解指定的名称来引用传入的参数。



#### 4.1.6 抽取重复的 SQL "片段" - sql include

##### 语法

```xml
<!-- 抽取 -->
<sql id="">
  抽取的重复 sql 片段
</sql>

<!-- 在任意相应的位置使用 -->
<include refid=""/> 
```

##### 抽取 + 引用 已抽取的SQL片段

```XML
<!--原语句-->
<select id="query" resultType="employee">
    select * from t_emp
    <where>
        <if test="name != null">
            emp_name = #{name}
        </if>
        <if test="salary != null and salary &gt; 100">
            and emp_salary = #{salary}
        </if>
    </where>
</select>

<!-- 抽取 + 使用sql标签抽取重复出现的SQL片段 -->
<sql id="selectSql">
  select * from t_emp
</sql>
<select id="query" resultType="employee">
    <include refid="selectSql"/>
    ...
</select>
```



### 4.2 实战操作：MyBatis 动态语句

- 完整代码请见模块：mybatis-high-dynsql-06（位于 ssm-mybatis-part 模块中的子模块）





## 五、MyBatis 高级扩展

### 5.1 Mapper 批量映射优化

  1. 需求

      Mapper 配置文件很多时，在全局配置文件中一个一个注册太麻烦，希望有一个办法能够一劳永逸。
  2. 配置方式

      Mybatis 允许在指定 Mapper 映射文件时，只指定其所在的包：

```XML
<mappers>
    <package name="com.boyan.mapper"/>
</mappers>
```

- 此时这个包下的所有 Mapper 配置文件将被自动加载、注册，比较方便。

  3. 资源创建要求
  - Mapper 接口和 Mapper 配置文件名称一致
      - Mapper 接口：EmployeeMapper.java
      - Mapper 配置文件：EmployeeMapper.xml
  
  - Mapper 配置文件放在 Mapper 接口所在的包内
      1. 可以将 mapper.xml 文件放在 mapper接口 所在的包！
      2. 可以在 sources 下创建 mapper 接口包一致的文件夹结构存放 mapperxml 文件
  
  - ⚠️🌟 注意在 resourse 下直接创建多层文件夹：
  
      - 创建时使用 / 分隔，展示时是 . 
      - 否则，创建的是一个完整的带.的包名，而不是分级结构。
  
        

### 5.2 插件和分页插件 PageHelper

#### 原理

MyBatis 对插件进行了标准化的设计，并提供了一套可扩展的插件机制。插件可以在用于语句执行过程中进行拦截，并允许通过自定义处理程序来拦截和修改 SQL 语句、映射语句的结果等。

具体来说，MyBatis 的插件机制包括以下三个组件：

1. `Interceptor`（拦截器）：定义一个拦截方法 `intercept`，该方法在执行 SQL 语句、执行查询、查询结果的映射时会被调用。
2. `Invocation`（调用）：实际上是对被拦截的方法的封装，封装了 `Object target`、`Method method` 和 `Object[] args` 这三个字段。
3. `InterceptorChain`（拦截器链）：对所有的拦截器进行管理，包括将所有的 Interceptor 链接成一条链，并在执行 SQL 语句时按顺序调用。

插件的开发非常简单，只需要实现 Interceptor 接口，并使用注解 `@Intercepts` 来标注需要拦截的对象和方法，然后在 MyBatis 的配置文件中添加插件即可。

PageHelper 是 MyBatis 中比较著名的分页插件，它提供了多种分页方式（例如 MySQL 和 Oracle 分页方式），支持多种数据库，并且使用非常简单。

#### 实战

- 新建模块 mybatis-other-extend-07 （位于ssm-mybatis-part模块之下）

##### 依赖引入

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.11</version>
</dependency>
```

##### 配置：mybatis-config.xml 配置分页插件

在 MyBatis 的配置文件中添加 PageHelper 的插件：

```XML
<!-- mybatis 内部插件，可以进行 sql语句 拦截了 -->
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 插件语法对应的数据库类型 -->
        <property name="helperDialect" value="mysql"/>
    </plugin>
</plugins>

<!-- ⚠️ 点进 http://mybatis.org/dtd/mybatis-3-config.dtd 可以看到 configuration 中 各标签定义顺序-->
```

其中，`com.github.pagehelper.PageInterceptor` 是 PageHelper 插件的名称，`dialect` 属性用于指定数据库类型（支持多种数据库）

##### sql 语句编写 XxxMapper.xml

- 正常编写语句即可：**不要使用 ; 结尾**！！！

```xml
<select id="queryList" resultType="employee07">
    select * from t_emp where emp_salary > 100
</select>
```

##### 分页使用测试（@Test）

```java
@Test
public void testTeacherRelationshipToMulti() {

    TeacherMapper teacherMapper = session.getMapper(TeacherMapper.class);

    PageHelper.startPage(1,2);
    // 查询Customer对象同时将关联的Order集合查询出来
    List<Teacher> allTeachers = teacherMapper.findAllTeachers();
//
    PageInfo<Teacher> pageInfo = new PageInfo<>(allTeachers);

    System.out.println("pageInfo = " + pageInfo);
    long total = pageInfo.getTotal(); // 获取总记录数
    System.out.println("total = " + total);
    int pages = pageInfo.getPages();  // 获取总页数
    System.out.println("pages = " + pages);
    int pageNum = pageInfo.getPageNum(); // 获取当前页码
    System.out.println("pageNum = " + pageNum);
    int pageSize = pageInfo.getPageSize(); // 获取每页显示记录数
    System.out.println("pageSize = " + pageSize);
    List<Teacher> teachers = pageInfo.getList(); //获取查询页的数据集合
    System.out.println("teachers = " + teachers);
    teachers.forEach(System.out::println);

}
```



### 5.3 逆向工程 和 插件 MyBatisX

#### 5.3.1 ORM思维介绍

  ORM（Object-Relational Mapping，对象-关系映射）是一种将数据库和面向对象编程语言中的对象之间进行转换的技术。它将对象和关系数据库的概念进行映射，最后我们就可以通过方法调用进行数据库操作!!

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis-orm.png" alt="image-20240603202833737" style="width:67%;" />

  最终: **让我们可以使用面向对象思维进行数据库操作！！！**

  **ORM 框架通常有半自动和全自动两种方式。**

  - 半自动 ORM：通常需要程序员手动编写 SQL 语句或者配置文件，将实体类和数据表进行映射，还需要手动将查询的结果集转换成实体对象。
  - 全自动 ORM：则是将实体类和数据表进行自动映射，使用 API 进行数据库操作时，ORM 框架会自动执行 SQL 语句并将查询结果转换成实体对象，程序员无需再手动编写 SQL 语句和转换代码。

  **下面是半自动和全自动 ORM 框架的区别：**

  1. 映射方式：半自动 ORM 框架需要程序员手动指定实体类和数据表之间的映射关系，通常使用 XML 文件或注解方式来指定；全自动 ORM 框架则可以自动进行实体类和数据表的映射，无需手动干预。
  2. 查询方式：半自动 ORM 框架通常需要程序员手动编写 SQL 语句并将查询结果集转换成实体对象；全自动 ORM 框架可以自动组装 SQL 语句、执行查询操作，并将查询结果转换成实体对象。
  3. 性能：由于半自动 ORM 框架需要手动编写 SQL 语句，因此程序员必须对 SQL 语句和数据库的底层知识有一定的了解，才能编写高效的 SQL 语句；而全自动 ORM 框架通过自动优化生成的 SQL 语句来提高性能，程序员无需进行优化。
  4. 学习成本：半自动 ORM 框架需要程序员手动编写 SQL 语句和映射配置，要求程序员具备较高的数据库和 SQL 知识；全自动 ORM 框架可以自动生成 SQL 语句和映射配置，程序员无需了解过多的数据库和 SQL 知识。

常见的半自动 ORM 框架：包括 MyBatis 等；

常见的全自动 ORM 框架：包括 Hibernate、Spring Data JPA、MyBatis-Plus 等。



#### 5.3.2 逆向工程

  希望半自动的 orm 框架也能实现 **单表的crud** 的自动生成；

  **注意：逆向工程只能生成单表crud的操作，多表查询依然需要我们自己编写！**



#### 5.3.3 逆向工程插件 MyBatisX 使用

MyBatisX 是一个 MyBatis 的代码生成插件，可以通过简单的配置和操作**快速生成 MyBatis Mapper、pojo 类和 Mapper.xml 文件**。

逆向工程使用步骤：自动生成 **单表的** crud方法！

- 建立数据库连接后，右键数据库表，选中MyBatis-X 插件

- 在 Generate Options 中选择

  		- module path：当前模块名；

  		- base package：com.boyan
    - relative package：pojo
      - 上面两个一起构成 pojo 生成的位置
      - 以下配置 mapper 模式
  		- annotation：None（注解位置不使用）
  		- options：Lombok、Model（实体类选用 lombok 注解，注意要勾选 model 否则不生成）
  		- template：default-all（这个是 mybatis 方式）
  		- 点击finish，生成！



## 六、MyBatis 总结

| 核心点                  | 掌握目标                                       |
| ----------------------- | ---------------------------------------------- |
| mybatis基础             | 使用流程, 参数输入,#{} ${},参数输出            |
| mybatis多表             | 实体类设计,resultMap多表结果映射               |
| mybatis动态语句（重要） | Mybatis 动态语句概念, where , if , foreach标签 |
| mybatis扩展             | Mapper批量处理,分页插件,逆向工程               |

##### 标准测试模板

```java
package com.boyan.test;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisTest {

    private SqlSession sqlSession;
    @BeforeEach
    public void before() throws IOException{
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        sqlSession = sqlSessionFactory.openSession(true);
    }
    @AfterEach
    public void clean(){
        sqlSession.close();
    }
    
    @Test
    public void test_01(){
        // ...
    }
}
```
