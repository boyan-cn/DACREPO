# Part07 - MyBatisPlus3 - 最优化持久层开发框架 - 高级用法｜基于 SpringBoot3 整合

> [!NOTE]
>
> 1. 自动生成单表的CRUD功能
>
> 2. 提供丰富的条件拼接方式
>
> 3. 全自动ORM类型持久层框架
>
> 官方文档链接：https://baomidou.com/ 【真的很优化！！！！！】



## 一、MyBatis-Plus 简介

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](https://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

特性：

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 SQL 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作



## 二、MyBatis-Plus 快速入门

### 🌟 完整 Maven 配置文件 pom.xml

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
    <artifactId>mybatisPlus-base-quickstart-01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- TODO. 依赖导入 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <!-- 测试环境 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!-- mybatis-plus  -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.3.1</version>
        </dependency>

        <!-- 数据库相关配置启动器 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <!-- druid启动器的依赖  -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-3-starter</artifactId>
            <version>1.2.18</version>
        </dependency>

        <!-- 驱动类-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.28</version>
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

### 🌟 完善 Springboot3 + druid 连接池配置：

文件夹：META-INF.spring

文件名：org.springframework.boot.autoconfigure.AutoConfiguration.imports

内容：com.alibaba.druid.spring.boot3.autoconfigure.DruidDataSourceAutoConfigure

### 🌟 Springboot3 配置文件｜启动类

application.yaml

```yaml
# web 配置
web:
  server:
    port: 80         # 端口号
  servlet:
    context-path: /  # 项目根路径

spring:
  datasource:
    # druid 连接池配置
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      url: jdbc:mysql://localhost:3306/mybatis-example
      username: root
      password: 12345678
      driver-class-name: com.mysql.cj.jdbc.Driver
```

启动类

```Java
@MapperScan("com.boyan.mapper")
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
      	// springboot 启动入口
        SpringApplication.run(MainApplication.class,args);
    }
}
```

### 🌟 UserMapper 使用示例｜测试

UserMapper 接口

```java
public interface UserMapper extends BaseMapper<User> {

}
```

本地单元测试类 - test / java / com.boyan.test.MyBatisPlus.class

```java
package com.boyan.test;

import com.boyan.mapper.UserMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class MyBatisPlusTest {

    @Autowired
    private UserMapper userMapper;

    /**
     * 测试 MyBatis-Plus quickstart 运行
     */
    @Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        userMapper.selectList(null).forEach(System.out::println);
    }
}
```

控制台运行结果 [SpringBoot + MyBatisPlus]

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatisplus-quickstart01-terminal.png" alt="image-20240621205923129" style="width:100%;" />



> [!NOTE]
>
> 源代码模块：ssm-project / mybatisPlus-part [Intellij IDEA 空项目] / **mybatisPlus-base-quickstart-01 **[Springboot3+MP框架]





## 三、MyBatis-Plus 核心功能

> [!TIP]
>
> 引入 MyBatis-Plus 是在 SpringBoot + MyBatis 集成的情况下使用的，MyBatis-Plus 的使用不会影响 MyBatis 的使用，就算有可以直接集成的现成的 CRUD 接口，依然支持自定义 sql 语句。
>
> eg. UserMapper 接口【MyBatis + MP 同时写法】
>
> ```java
> @Mapper
> public interface UserMapper extends BaseMapper<User> {
> 
>     //正常自定义方法！
>     //可以使用注解@Select或者mapper.xml实现
>     @Select("select * from user")
>     List<User> queryAll();
> }
> 
> ```
>
> 基于对应的  UserMapper.xml 实现
>
> ```xml
> <?xml version="1.0" encoding="UTF-8" ?>
> <!DOCTYPE mapper
>         PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
>         "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
> <!-- namespace = 接口的全限定符 -->
> <mapper namespace="com.boyan.mapper.UserMapper">
> 
>    <select id="queryAll" resultType="user" >
>        select * from user
>    </select>
> </mapper>
> ```
>
> 💯结论：引入 MP 完全不影响 MyBatis 的使用～！



> [!NOTE]
>
> 虽然但是 -> 这部分细节强烈建议去看官方文档，都写的很清楚！点击跳转：[MyBatis-Plus 官方文档](https://baomidou.com/guides/wrapper/)



> [!TIP]
>
> 1）IService  2）BaseMapper
>
> - 这两个一个是持久层的 Mapper 增强，一个是业务逻辑层的 Service 增强，想用哪个用哪个，不用也不耽误学一下！
> - 分别都可以达到 CRUD 的作用



#### 🌟BaseMapper 接口 - 源码｜参数说明

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.baomidou.mybatisplus.core.mapper;

import com.baomidou.mybatisplus.core.conditions.Wrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.exceptions.TooManyResultsException;

/**
 * BaseMapper 接口定义了 MyBatis-Plus 中常用的 CRUD 方法
 * @param <T> 实体类类型
 */
public interface BaseMapper<T> extends Mapper<T> {

    /**
     * 插入一条记录
     * @param entity 实体对象
     * @return 受影响的行数
     */
    int insert(T entity);

    /**
     * 根据 ID 删除记录
     * @param id 主键 ID
     * @return 受影响的行数
     */
    int deleteById(Serializable id);

    /**
     * 根据实体条件删除记录
     * @param entity 实体对象
     * @return 受影响的行数
     */
    int deleteById(T entity);

    /**
     * 根据 columnMap 条件删除记录
     * @param columnMap 删除条件
     * @return 受影响的行数
     */
    int deleteByMap(@Param("cm") Map<String, Object> columnMap);

    /**
     * 根据条件包装器删除记录
     * @param queryWrapper 条件包装器
     * @return 受影响的行数
     */
    int delete(@Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据 ID 集合批量删除记录
     * @param idList ID 集合
     * @return 受影响的行数
     */
    int deleteBatchIds(@Param("coll") Collection<?> idList);

    /**
     * 根据 ID 更新记录
     * @param entity 实体对象
     * @return 受影响的行数
     */
    int updateById(@Param("et") T entity);

    /**
     * 根据条件包装器更新记录
     * @param entity 实体对象
     * @param updateWrapper 条件包装器
     * @return 受影响的行数
     */
    int update(@Param("et") T entity, @Param("ew") Wrapper<T> updateWrapper);

    /**
     * 根据 ID 查询记录
     * @param id 主键 ID
     * @return 实体对象
     */
    T selectById(Serializable id);

    /**
     * 根据 ID 集合批量查询记录
     * @param idList ID 集合
     * @return 实体对象集合
     */
    List<T> selectBatchIds(@Param("coll") Collection<? extends Serializable> idList);

    /**
     * 根据 columnMap 条件查询记录
     * @param columnMap 查询条件
     * @return 实体对象集合
     */
    List<T> selectByMap(@Param("cm") Map<String, Object> columnMap);

    /**
     * 根据条件包装器查询单条记录
     * @param queryWrapper 条件包装器
     * @return 实体对象
     * @throws TooManyResultsException 如果查询结果超过一条
     */
    default T selectOne(@Param("ew") Wrapper<T> queryWrapper) {
        List<T> list = this.selectList(queryWrapper);
        if (list.size() == 1) {
            return list.get(0);
        } else if (list.size() > 1) {
            throw new TooManyResultsException("Expected one result (or null) to be returned by selectOne(), but found: " + list.size());
        } else {
            return null;
        }
    }

    /**
     * 判断记录是否存在
     * @param queryWrapper 条件包装器
     * @return 记录是否存在
     */
    default boolean exists(Wrapper<T> queryWrapper) {
        Long count = this.selectCount(queryWrapper);
        return null != count && count > 0L;
    }

    /**
     * 根据条件包装器查询记录数
     * @param queryWrapper 条件包装器
     * @return 记录数
     */
    Long selectCount(@Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器查询记录集合
     * @param queryWrapper 条件包装器
     * @return 实体对象集合
     */
    List<T> selectList(@Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器查询 Map 记录集合
     * @param queryWrapper 条件包装器
     * @return Map 对象集合
     */
    List<Map<String, Object>> selectMaps(@Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器查询对象记录集合
     * @param queryWrapper 条件包装器
     * @return 对象集合
     */
    List<Object> selectObjs(@Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器分页查询记录
     * @param page 分页对象
     * @param queryWrapper 条件包装器
     * @param <P> 分页对象类型
     * @return 分页结果
     */
    <P extends IPage<T>> P selectPage(P page, @Param("ew") Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器分页查询 Map 记录
     * @param page 分页对象
     * @param queryWrapper 条件包装器
     * @param <P> 分页对象类型
     * @return 分页结果
     */
    <P extends IPage<Map<String, Object>>> P selectMapsPage(P page, @Param("ew") Wrapper<T> queryWrapper);
}

```

#### 🌟IService 接口 - 源码

```java
package com.baomidou.mybatisplus.extension.service;

import com.baomidou.mybatisplus.core.conditions.Wrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.core.toolkit.support.SFunction;
import org.apache.ibatis.annotations.Param;

import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import java.util.function.Function;

/**
 * IService 是 MyBatis-Plus 提供的通用服务接口，封装了常见的 CRUD 操作。
 * @param <T> 实体类类型
 */
public interface IService<T> {

    /**
     * 插入一条记录
     * @param entity 实体对象
     * @return 是否成功
     */
    boolean save(T entity);

    /**
     * 批量插入记录
     * @param entityList 实体对象列表
     * @return 是否成功
     */
    boolean saveBatch(Collection<T> entityList);

    /**
     * 批量插入记录
     * @param entityList 实体对象列表
     * @param batchSize 批次大小
     * @return 是否成功
     */
    boolean saveBatch(Collection<T> entityList, int batchSize);

    /**
     * 根据 ID 删除记录
     * @param id 主键 ID
     * @return 是否成功
     */
    boolean removeById(Serializable id);

    /**
     * 根据实体对象删除记录
     * @param entity 实体对象
     * @return 是否成功
     */
    boolean removeById(T entity);

    /**
     * 根据条件删除记录
     * @param queryWrapper 条件包装器
     * @return 是否成功
     */
    boolean remove(Wrapper<T> queryWrapper);

    /**
     * 根据 ID 集合批量删除记录
     * @param idList 主键 ID 集合
     * @return 是否成功
     */
    boolean removeByIds(Collection<? extends Serializable> idList);

    /**
     * 根据 ID 更新记录
     * @param entity 实体对象
     * @return 是否成功
     */
    boolean updateById(T entity);

    /**
     * 根据条件更新记录
     * @param updateWrapper 更新条件包装器
     * @return 是否成功
     */
    boolean update(Wrapper<T> updateWrapper);

    /**
     * 根据 ID 批量更新记录
     * @param entityList 实体对象列表
     * @return 是否成功
     */
    boolean updateBatchById(Collection<T> entityList);

    /**
     * 根据 ID 批量更新记录
     * @param entityList 实体对象列表
     * @param batchSize 批次大小
     * @return 是否成功
     */
    boolean updateBatchById(Collection<T> entityList, int batchSize);

    /**
     * 根据 ID 查询记录
     * @param id 主键 ID
     * @return 实体对象
     */
    T getById(Serializable id);

    /**
     * 查询记录总数
     * @return 记录总数
     */
    long count();

    /**
     * 根据条件查询记录数
     * @param queryWrapper 条件包装器
     * @return 记录数
     */
    long count(Wrapper<T> queryWrapper);

    /**
     * 查询所有记录
     * @return 实体对象列表
     */
    List<T> list();

    /**
     * 根据条件查询记录
     * @param queryWrapper 条件包装器
     * @return 实体对象列表
     */
    List<T> list(Wrapper<T> queryWrapper);

    /**
     * 查询所有记录，并转换为 Map 列表
     * @return Map 对象列表
     */
    List<Map<String, Object>> listMaps();

    /**
     * 根据条件查询记录，并转换为 Map 列表
     * @param queryWrapper 条件包装器
     * @return Map 对象列表
     */
    List<Map<String, Object>> listMaps(Wrapper<T> queryWrapper);

    /**
     * 查询所有记录，并转换为对象列表
     * @return 对象列表
     */
    List<Object> listObjs();

    /**
     * 根据条件查询记录，并转换为对象列表
     * @param queryWrapper 条件包装器
     * @return 对象列表
     */
    List<Object> listObjs(Wrapper<T> queryWrapper);

    /**
     * 根据条件查询记录，并转换为对象列表
     * @param queryWrapper 条件包装器
     * @param mapper 转换函数
     * @param <R> 返回对象类型
     * @return 转换后的对象列表
     */
    <R> List<R> listObjs(Wrapper<T> queryWrapper, Function<? super Object, R> mapper);

    /**
     * 根据条件分页查询记录
     * @param page 分页对象
     * @param queryWrapper 条件包装器
     * @param <E> 分页对象类型
     * @return 分页结果
     */
    <E extends IPage<T>> E page(E page, Wrapper<T> queryWrapper);

    /**
     * 分页查询所有记录
     * @param page 分页对象
     * @param <E> 分页对象类型
     * @return 分页结果
     */
    <E extends IPage<T>> E page(E page);

    /**
     * 根据条件分页查询记录，并转换为 Map 列表
     * @param page 分页对象
     * @param queryWrapper 条件包装器
     * @param <E> 分页对象类型
     * @return 分页结果
     */
    <E extends IPage<Map<String, Object>>> E pageMaps(E page, Wrapper<T> queryWrapper);

    /**
     * 分页查询所有记录，并转换为 Map 列表
     * @param page 分页对象
     * @param <E> 分页对象类型
     * @return 分页结果
     */
    <E extends IPage<Map<String, Object>>> E pageMaps(E page);

    /**
     * 根据条件包装器查询单条记录
     * @param queryWrapper 条件包装器
     * @return 实体对象
     */
    T getOne(Wrapper<T> queryWrapper);

    /**
     * 根据条件包装器查询单条记录
     * @param queryWrapper 条件包装器
     * @param throwEx 查询结果超过一条时是否抛出异常
     * @return 实体对象
     */
    T getOne(Wrapper<T> queryWrapper, boolean throwEx);

    /**
     * 根据条件查询记录
     * @param queryWrapper 条件包装器
     * @return Map 对象列表
     */
    Map<String, Object> getMap(Wrapper<T> queryWrapper);

    /**
     * 根据 ID 集合查询记录
     * @param idList 主键 ID 集合
     * @return 实体对象列表
     */
    Collection<T> listByIds(Collection<? extends Serializable> idList);

    /**
     * 根据 columnMap 查询记录
     * @param columnMap 查询条件
     * @return 实体对象列表
     */
    Collection<T> listByMap(Map<String, Object> columnMap);

    /**
     * 获取实体对象的单列值列表
     * @param column 列名
     * @param wrapper 条件包装器
     * @return 单列值列表
     */
    List<Object> getObjList(SFunction<T, ?> column, Wrapper<T> wrapper);
}

```



### 3.1 BaseMapper\<T>｜M-P 持久层接口｜CRUD [自定义]

M-P 默认的 XxxMapper.xml 位置

```YAML
mybatis-plus: # mybatis-plus的配置
  # 默认位置 private String[] mapperLocations = new String[]{"classpath*:/mapper/**/*.xml"};    
  mapper-locations: classpath:/mapper/*.xml
```

自定义mapper方法：

```Java
public interface UserMapper extends BaseMapper<User> {

    //正常自定义方法！
    //可以使用注解@Select或者mapper.xml实现
    List<User> queryAll();
}

```

基于mapper.xml实现：

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace = 接口的全限定符 -->
<mapper namespace="com.boyan.mapper.UserMapper">

   <select id="queryAll" resultType="user" >
       select * from user
   </select>
</mapper>
```

### 3.2 IService\<T> |M-P 业务逻辑层接口｜CRUD

#### 对比Mapper接口CRUD区别：

- service添加了批量方法
- service层的方法自动添加事务 @Transactional [自动省略]

#### 使用Iservice接口方式

接口继承IService接口

```Java
public interface UserService extends IService<User> {
}

```

类继承ServiceImpl实现类

```Java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService{

}

```



### 3.3 IService 🆚 BaseMapper｜区别、联系

1. **BaseMapper**
   - 角色：数据访问层（DAO）
   - 职责：直接与数据库交互，提供基础的 CRUD 操作。
   - 主要方法：`insert`、`deleteById`、`updateById`、`selectById`、`selectList` 等。
2. **IService**
   - 角色：服务层（Service）
   - 职责：封装业务逻辑，调用 `BaseMapper` 提供的方法来进行数据库操作。
   - 主要方法：`save`、`removeById`、`updateById`、`getById`、`list`、`saveBatch` 等。

##### 🌟1. 方法对照

| IService 方法                                                | BaseMapper 方法                                              | 说明                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------- |
| save(T entity)                                               | insert(T entity)                                             | 保存实体，如果实体没有主键则插入 |
| removeById(Serializable id)                                  | deleteById(Serializable id)                                  | 根据ID删除实体                   |
| removeByMap(Map<String, Object> columnMap)                   | deleteByMap(@Param("cm") Map<String, Object> columnMap)      | 根据条件删除实体                 |
| remove(Wrapper<T> queryWrapper)                              | delete(@Param("ew") Wrapper<T> queryWrapper)                 | 根据条件构造器删除实体           |
| removeBatchIds(Collection<? extends Serializable> idList)    | deleteBatchIds(@Param("coll") Collection<?> idList)          | 批量删除实体                     |
| updateById(T entity)                                         | updateById(@Param("et") T entity)                            | 根据ID更新实体                   |
| update(T entity, Wrapper<T> updateWrapper)                   | update(@Param("et") T entity, @Param("ew") Wrapper<T> updateWrapper) | 根据条件更新实体                 |
| getById(Serializable id)                                     | selectById(Serializable id)                                  | 根据ID查询实体                   |
| listByIds(Collection<? extends Serializable> idList)         | selectBatchIds(@Param("coll") Collection<? extends Serializable> idList) | 根据ID集合批量查询实体           |
| listByMap(Map<String, Object> columnMap)                     | selectByMap(@Param("cm") Map<String, Object> columnMap)      | 根据条件查询实体                 |
| getOne(Wrapper<T> queryWrapper)                              | selectOne(@Param("ew") Wrapper<T> queryWrapper)              | 根据条件查询单个实体             |
| count(Wrapper<T> queryWrapper)                               | selectCount(@Param("ew") Wrapper<T> queryWrapper)            | 根据条件查询总记录数             |
| list(Wrapper<T> queryWrapper)                                | selectList(@Param("ew") Wrapper<T> queryWrapper)             | 根据条件查询实体列表             |
| list()                                                       | **N/A**                                                      | 查询所有实体                     |
| page(IPage<T> page, Wrapper<T> queryWrapper)                 | selectPage(P page, @Param("ew") Wrapper<T> queryWrapper)     | 分页查询实体列表                 |
| page(IPage<T> page)                                          | **N/A**                                                      | 分页查询所有实体                 |
| listMaps(Wrapper<T> queryWrapper)                            | selectMaps(@Param("ew") Wrapper<T> queryWrapper)             | 根据条件查询实体的Map列表        |
| listObjs(Wrapper<T> queryWrapper)                            | selectObjs(@Param("ew") Wrapper<T> queryWrapper)             | 根据条件查询实体的某些字段       |
| pageMaps(IPage<Map<String, Object>> page, Wrapper<T> queryWrapper) | selectMapsPage(P page, @Param("ew") Wrapper<T> queryWrapper) | 分页查询实体的Map列表            |

##### 🌟2. 参数类型

| 类型                               | 参数名       | 描述                                     |
| ---------------------------------- | ------------ | ---------------------------------------- |
| Serializable                       | id           | 主键 ID                                  |
| Wrapper<T>                         | queryWrapper | 实体对象封装操作类（可以为 null）        |
| Collection<? extends Serializable> | idList       | 主键 ID 列表(不能为 null 以及 empty)     |
| Map<String, Object>                | columnMap    | 表字段 map 对象                          |
| IPage<T>                           | page         | 分页查询条件（可以为 RowBounds.DEFAULT） |

##### 🌟3. BaseMapper 方法中的 @Param 参数解释

| 参数名称 | 方法                                                         | 意义                                        |
| -------- | ------------------------------------------------------------ | ------------------------------------------- |
| et       | updateById(@Param("et") T entity)<br>update(@Param("et") T entity, @Param("ew") Wrapper<T> updateWrapper) | 实体类型参数，表示需要操作的实体对象        |
| ew       | delete(@Param("ew") Wrapper<T> queryWrapper)<br>update(@Param("et") T entity, @Param("ew") Wrapper<T> updateWrapper)<br>selectOne(@Param("ew") Wrapper<T> queryWrapper)<br>selectCount(@Param("ew") Wrapper<T> queryWrapper)<br>selectList(@Param("ew") Wrapper<T> queryWrapper)<br>selectMaps(@Param("ew") Wrapper<T> queryWrapper)<br>selectObjs(@Param("ew") Wrapper<T> queryWrapper)<br>selectPage(P page, @Param("ew") Wrapper<T> queryWrapper)<br>selectMapsPage(P page, @Param("ew") Wrapper<T> queryWrapper) | 条件构造器，构建 SQL 的 WHERE 条件          |
| cm       | deleteByMap(@Param("cm") Map<String, Object> columnMap)<br>selectByMap(@Param("cm") Map<String, Object> columnMap) | 列的 Map 参数，通过列名和值的键值对指定条件 |
| coll     | deleteBatchIds(@Param("coll") Collection<?> idList)<br>selectBatchIds(@Param("coll") Collection<? extends Serializable> idList) | 集合参数，传递一组 ID 值                    |



### 3.5 🌟分页查询｜IPage\<Object> Page\<Object>

#### 1）全局导入分页插件｜在SpringBoot入口类

```java
package com.boyan;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@MapperScan(basePackages = "com.boyan.mapper")
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    /**
     * MyBatis-Plus 的 插件配置专区
     * @return
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        // MP 的插件集合【分页插件、乐观锁插件... 都在这里直接添加就可以了】
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 添加 - 分页插件
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL)); // 指定 数据库类型
        // 返回插件集合
        return interceptor;
    }
}
```

#### 2）[分页] 直接使用 MyBatis-Plus 内置方法 

直接在使用中演示，别处无需更改：eg. 测试

```java
/**
 * 测试 - 01 - 分页插件的使用
 */
@Test
public void testPage() {
  
    // IPage -> Page(页码,页容量)
    Page<User> page = new Page<>(1, 5);
  
    // 调用 Mapper -> BaseMapper 中的方法
    userMapper.selectPage(page,null);

    // 结果 page 最后也会被封装结果，更细节的信息
    long current = page.getCurrent();                    // 页码
    System.out.println("current = " + current);
    long size = page.getSize();                          // 页容量
    System.out.println("size = " + size);
    List<User> userRecords = page.getRecords();          // 当前页的数据
    System.out.println("userRecords = " + userRecords);
    long total = page.getTotal();                        // 总条数
    System.out.println("total = " + total);
}
```

#### 3）[分页] 自定义 MyBatis 方法 + MyBatis-Plus框架

UserMapper 接口方法

```java
package com.boyan.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.boyan.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

@Mapper
public interface UserMapper extends BaseMapper<User> {

    /**
     * 自定义分页
     * 定义一个根据 id 参数查询，并且分页的方法
     */
    IPage<User> queryById(IPage<User> page, @Param("id") Integer id);

}
```

UserMapper.xml [返回类型依然是 POJO：User，Mapper层 无需分页设定]

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace == mapper接口类的全限定名 -->
<mapper namespace="com.boyan.mapper.UserMapper">

    <select id="queryById" resultType="user">
        select * from user where id = #{id}
    </select>

</mapper>
```

测试

```java
/**
 * 测试 - 02 - 自定义分页的使用
 */
@Test
public void testMyPage(){
    // IPage -> Page(页码,页容量)
    Page<User> page = new Page<>(1,5);
    // 调用 Mapper -> 自定义的方法
    userMapper.queryById(page,2);

    // 结果 page最后也会被封装结果
    long current = page.getCurrent();                    // 页码
    System.out.println("current = " + current);
    long size = page.getSize();                          // 页容量
    System.out.println("size = " + size);
    List<User> userRecords = page.getRecords();          // 当前页的数据
    System.out.println("userRecords = " + userRecords);
    long total = page.getTotal();                        // 总条数
    System.out.println("total = " + total);
}
```



### 3.6 🌟条件构造器 Wrapper｜推荐使用 LambdaWrapper

##### 1. Wrapper 各层继承关系图

Wrapper -- AbstractWrapper -- AbstractLamdaWrapper [LamdaUpdateWrapper|LamdaQueryWrapper]

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatisplus-wrapper%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB%E5%9B%BE.png" alt="image-20240625141018852" style="width:67%;" />

> Wrapper ： 条件构造抽象类，最顶端父类
>
> - AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件
>     - QueryWrapper ： 查询/删除条件封装
>     - UpdateWrapper ： 修改条件封装
>     - AbstractLambdaWrapper ： 使用Lambda 语法
>         - LambdaQueryWrapper ：用于Lambda语法使用的查询Wrapper
>         - LambdaUpdateWrapper ： Lambda 更新封装Wrapper



##### 2. 条件组装

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/ssm-mybatis3-%E6%9D%A1%E4%BB%B6%E6%9E%84%E9%80%A0%E5%99%A8%E8%AF%AD%E6%B3%95.png" alt="image-20240621233440308" style="width:100%;" />

- **基本用法**

```java
package com.boyan.test;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.boyan.mapper.UserMapper;
import com.boyan.pojo.UserOthers;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
public class MyBatisPlusWrapperTest {

    @Autowired
    private UserMapper userMapper;

    /**
     * Wrapper 基本用法测试
     */
    @Test
    public void test01(){
        // 创建 Wrapper 并组装条件 
        QueryWrapper<UserOthers> queryWrapper = new QueryWrapper<>();
        // [拼接写法]
        queryWrapper.eq("username", "root");   // 添加等于条件
        queryWrapper.ne("password", "222222"); // 添加不等于条件

        // 另一种写法 [链式写法] 链式写法更简洁一些～
        queryWrapper.eq("username","root").ne("password", "222222");

        // 正常使用 - 按条件查询user 并返回List<User>
        List<UserOthers> users = userMapper.selectList(queryWrapper);
        System.out.println("条件查询结果：" + users);
    }
  	/** 等价于：
  	*   SELECT * FROM user WHERE username = 'root' AND password != '222222';
  	*/
}
```



#### 3.6.1 QueryWrapper

##### 组装查询条件：

```Java
@Test
public void test01(){
    //查询用户名包含a，年龄在20到30之间，并且邮箱不为null的用户信息
    //SELECT id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 AND (username LIKE ? AND age BETWEEN ? AND ? AND email IS NOT NULL)
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("username", "a")
            .between("age", 20, 30)
            .isNotNull("email");
    List<User> list = userMapper.selectList(queryWrapper);
    list.forEach(System.out::println);

```

##### 组装排序条件:

```Java
@Test
public void test02(){
    //按年龄降序查询用户，如果年龄相同则按id升序排列
    //SELECT id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 ORDER BY age DESC,id ASC
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper
            .orderByDesc("age")
            .orderByAsc("id");
    List<User> users = userMapper.selectList(queryWrapper);
    users.forEach(System.out::println);
}
```

##### 组装删除条件:

```Java
@Test
public void test03(){
    //删除email为空的用户
    //DELETE FROM t_user WHERE (email IS NULL)
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.isNull("email");
    //条件构造器也可以构建删除语句的条件
    int result = userMapper.delete(queryWrapper);
    System.out.println("受影响的行数：" + result);
}
```

##### and和or关键字使用(修改)：

```Java
@Test
public void test04() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //将年龄大于20并且用户名中包含有a或邮箱为null的用户信息修改
    //UPDATE t_user SET age=?, email=? WHERE username LIKE ? AND age > ? OR email IS NULL)
    queryWrapper
            .like("username", "a")
            .gt("age", 20)
            .or()
            .isNull("email");
    User user = new User();
    user.setAge(18);
    user.setEmail("user@atguigu.com");
    int result = userMapper.update(user, queryWrapper);
    System.out.println("受影响的行数：" + result);
}
```

##### 指定列映射查询：

```Java
@Test
public void test05() {
    //查询用户信息的username和age字段
    //SELECT username,age FROM t_user
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.select("username", "age");
    //selectMaps()返回Map集合列表，通常配合select()使用，避免User对象中没有被查询到的列值为null
    List<Map<String, Object>> maps = userMapper.selectMaps(queryWrapper);
    maps.forEach(System.out::println);
}
```

##### condition 判断组织条件【动态sql语句 -> Java实现】

```Java
 @Test
public void testQuick3(){
    
    String name = "root";
    int age = 18;

    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //判断条件拼接
    //当name不为null拼接等于, age > 1 拼接等于判断
    //方案1: 手动判断
    if (!StringUtils.isEmpty(name)){
        queryWrapper.eq("name",name);
    }
    if (age > 1){
        queryWrapper.eq("age",age);
    }
    
    //方案2: 拼接condition判断
    //每个条件拼接方法都condition参数,这是一个比较运算,为true追加当前条件!
    //eq(condition,列名,值)
    queryWrapper.eq(!StringUtils.isEmpty(name),"name",name)
            .eq(age>1,"age",age);   
}
```



#### 3.6.3 UpdateWrapper

注意：使用queryWrapper + 实体类形式可以实现修改，但是无法将列值修改为null值！

使用updateWrapper:

```Java
@Test
public void testQuick2(){

    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    //将id = 3 的email设置为null, age = 18
    updateWrapper.eq("id",3)
            .set("email",null)  // set 指定列和结果
            .set("age",18);
    //如果使用updateWrapper 实体对象写null即可!
    int result = userMapper.update(null, updateWrapper);
    System.out.println("result = " + result);

}
```

使用updateWrapper可以随意设置列的值！！



#### 3.6.4 LambdaQueryWrapper｜LambdaUpdateWrapper【推荐 ! eg. User::getName】

> [!NOTE]
>
> 相比于 QueryWrapper，LambdaQueryWrapper 使用了实体类的属性引用（例如 User::getName、User::getAge），而不是字符串来表示字段名，这提高了代码的可读性和可维护性。

#####   1. 方法引用

 是 Java 8 中引入的一种语法特性，它提供了一种简洁的方式来直接引用已有的方法或构造函数，可以替代 Lambda 表达式，使代码更简洁、更易读。

  Java 8 支持以下几种方法引用的形式：

  1. **静态方法引用：** 引用静态方法，语法为 `类名::静态方法名`。
  2. **实例方法引用：** 引用实例方法，语法为 `实例对象::实例方法名`。
  3. **对象方法引用：** 引用特定对象的实例方法，语法为 `类名::实例方法名`。
  4. **构造函数引用：** 引用构造函数，语法为 `类名::new`。

  演示代码:

```Java
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

public class MethodReferenceExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Tom", "Alice");
        // 使用 Lambda 表达式
        names.forEach(name -> System.out.println(name));
        // 使用方法引用
        names.forEach(System.out::println);
    }
}
```

##### 2. LambdaQueryWrapper 使用案例

```Java
@Test
public void testQuick4(){

    String name = "root";
    int age = 18;

    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //每个条件拼接方法都condition参数,这是一个比较运算,为true追加当前条件!
    //eq(condition,列名,值)
    queryWrapper.eq(!StringUtils.isEmpty(name),"name",name)
            .eq(age>1,"age",age);

    //TODO: 使用lambdaQueryWrapper
    LambdaQueryWrapper<User> lambdaQueryWrapper = new LambdaQueryWrapper<>();
    //注意: 需要使用方法引用
    //技巧: 类名::方法名
    lambdaQueryWrapper.eq(!StringUtils.isEmpty(name), User::getName,name);
    List<User> users= userMapper.selectList(lambdaQueryWrapper);
    System.out.println(users);
}
```

##### 3. 基于 LambdaUpdateWrapper 组装条件 使用案例

```Java
@Test
public void testQuick2(){

    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    //将id = 3 的email设置为null, age = 18
    updateWrapper.eq("id",3)
            .set("email",null)  // set 指定列和结果
            .set("age",18);

    //使用lambdaUpdateWrapper
    LambdaUpdateWrapper<User> updateWrapper1 = new LambdaUpdateWrapper<>();
    updateWrapper1.eq(User::getId,3)
            .set(User::getEmail,null)
            .set(User::getAge,18);
    
    //如果使用updateWrapper 实体对象写null即可!
    int result = userMapper.update(null, updateWrapper);
    System.out.println("result = " + result);
}
```



### 3.7 自定义映射关系 - 核心注解@TableName @TableId @TableField｜全局配置修改

> 数据库的信息和实体类不完全映射时，需要进一步自定义映射关系【 POJO类 - - 数据库表 】
>
> ps. **MyBatis-Plus会自动开启驼峰命名风格映射!!!**
>
> 自定义一共有两种方式：1）使用核心注解 2）通过在application.yaml 中全局配置修改

#### 1. 核心注解

1. @TableName(value="")

   - value：指定表名，当 POJO类 -> 数据库表明不对应时，自定义映射；

2. @TableId(value="", IdType.AUTO|IdType.ASSIGN_ID(默认))

   - value：指定主键列名

   - IdType：主键策略

     1. AUTO (自增：MySQL 配置主键自增长)

     2. ASSIGN_ID (**默认**：雪花算法自动生成 Long 类型 Id)

        > [!NOTE]
        >
        > **雪花算法**
        >
        > ​	- **解决分布式系统中生成全局唯一ID的需求**。
        >
        > ​	- 随机生成一个 Long 类型的不重复的数字，需要用 **Long/String** 接值「适合：分布式/大量数据场景使用」 否则大数据场景下，分表自增长会有瓶颈，到达一定限度会不同表之间存在 id 重复；
        >
        > 🆚 **UUID**：随机生成一个不重复的**字符串**；

3. @TableField(value="",exist=true/false)
   - value：数据库字段名
   - IdType：是否为数据库表字段，默认值为 true，如果改成 false，在数据库操作的时候会自动忽略这个列；

#### 2. 全局配置 application.yaml｜table-prefix id-type

```yaml
mybatis-plus:
  configuration:
    # 配置MyBatis日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      table-prefix: t_			# 统一配置MyBatis-Plus操作表的默认前缀
      id-type: auto 				# 统一配置MyBatis-Plus的主键策略【默认是雪花算法，这里改成自增】
```





## 四、MyBatis-Plus 高级扩展



### 4.1 🌟【逻辑删除】篇

##### 1.数据库表中添加列

```sql
ALTER TABLE USER ADD deleted INT DEFAULT 0 ;  # int 类型 1 逻辑删除 0 未逻辑删除
```

##### 2.POJO类添加属性

```java
		// @TableLogic
    // 逻辑删除字段 int mybatis-plus下,默认 逻辑删除值为1 未逻辑删除 1 
    private Integer deleted;
```

##### 3.配置｜**@TableLogic**  

 1. 单一指定：在每个类 deleted 上加 **@TableLogic**  

 2. 全局指定：在 application.yaml 中添加

    ```yaml
    mybatis-plus:
      configuration:
        # 配置MyBatis日志
        log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
      global-config:
        db-config:
          table-prefix: t_			         # 统一配置 MyBatis-Plus 操作表的默认前缀
          id-type: auto 				         # 统一配置 MyBatis-Plus 的主键策略【默认是雪花算法，这里改成自增】
          # 逻辑删除 配置
          logic-delete-field: deleted    # 统一配置所有 POJO类 中的 逻辑删除属性名
          logic-not-delete-value: 0			 # 未删除【默认值0，可以自定义】
          logic-delete-value: 1					 # 删除  【默认值1，可以自定义】
    ```

##### 4. 作用

- 配置【逻辑删除】以后，
  - 没有真正的删除语句，所有删除会自动改为修改语句!
  - 查询等只是对于 非逻辑删除 语句的：eg. select ...... from user where deleted=0

- 【逻辑删除】是为了保护数据，永远不知道什么时候这条数据会有用。





### 4.2 🌟【悲观锁｜乐观锁】篇｜解决并发问题的思路

##### 思想

【悲观锁｜乐观锁】是两个解决并发问题的思路，举一个很好理解的例子【厕所只有一间坑，同一时刻只有一个人用】

- 悲观锁：你进来上厕所，你是一个悲观的人，你认为一会会有人来，所以 ->【把厕所门锁上】->【并发锁机制】
- 乐观锁：你进来上厕所，你是一个乐观的人，你认为一会不会有人来，所以 ->【把厕所门开着，如果有人来了，Ta推开门看见有人在用，就走了】->【反复尝试】

##### 具体技术和方案

1. 乐观锁实现方案和技术：
    - **版本号/时间戳**：为数据添加一个版本号或时间戳字段，每次更新数据时，比较当前版本号或时间戳与期望值是否一致，若一致则更新成功，否则表示数据已被修改，需要进行冲突处理。
    - CAS（Compare-and-Swap）：使用原子操作比较当前值与旧值是否一致，若一致则进行更新操作，否则重新尝试。
    - 无锁数据结构：采用无锁数据结构，如无锁队列、无锁哈希表等，通过使用原子操作实现并发安全。
2. 悲观锁实现方案和技术：
    - 锁机制：使用传统的锁机制，如互斥锁（Mutex Lock）或读写锁（Read-Write Lock）来保证对共享资源的独占访问。
    - 数据库锁：在数据库层面使用行级锁或表级锁来控制并发访问。
    - 信号量（Semaphore）：使用信号量来限制对资源的并发访问。

##### 介绍版本号乐观锁技术的实现流程

- 每条数据添加一个版本号字段version
- 取出记录时，获取当前 version
- 更新时，检查获取版本号是不是数据库当前最新版本号
- 如果是[证明没有人修改数据], 执行更新, set 数据更新 , version = version+ 1 
- 如果 version 不对[证明有人已经修改了]，我们现在的其他记录就是失效数据!就更新失败

#### 🌟使用 mybatis-plus 数据使用乐观锁｜版本号 @Version 注解

##### 1. 添加版本号更新插件

```Java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}
```

##### 2. 乐观锁字段添加@Version注解

- 支持的数据类型只有: int, Integer, long, Long, Date, Timestamp, LocalDateTime
- 仅支持 `updateById(id)` 与 `update(entity, wrapper)` 方法

```Java
@Version
private Integer version;
```

##### 3. 数据库也需要添加version字段

```sql
ALTER TABLE USER ADD VERSION INT DEFAULT 1 ;   # int 类型 乐观锁字段
```

##### 4. 正常更新使用即可，MyBatis-Plus 自动管理更新版本号

```Java
//演示乐观锁生效场景
@Test
public void testQuick7(){
    //步骤1: 先查询, 再更新 获取 version 数据
    //同时查询两条, 但是version唯一, 最后更新的失败
    User user  = userMapper.selectById(5);
    User user1  = userMapper.selectById(5);

    user.setAge(20);
    user1.setAge(30);

    userMapper.updateById(user);
    //乐观锁生效,失败!
    userMapper.updateById(user1);
}
```





### 4.3 🌟【防全表更新｜删除实现】篇｜插件

针对 update 和 delete 语句，作用：**阻止恶意的全表更新删除**。

【插件】添加防止全表更新和删除拦截器

```Java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
  MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
  interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
  return interceptor;
}
}
```

测试：全部更新或者删除

```Java
@Test
public void testQuick8(){
    User user = new User();
    user.setName("custom_name");
    user.setEmail("xxx@mail.com");
    //Caused by: com.baomidou.mybatisplus.core.exceptions.MybatisPlusException: Prohibition of table update operation
    //全局更新,报错
    userService.saveOrUpdate(user,null);
}
```





## 五、MyBatisX 插件｜快速代码生成器｜逆向工程

> 点击跳转官方文档描述：[MyBatisX 插件](https://baomidou.com/guides/mybatis-x/)
>
> 具体详情操作请参考：MyBatis 部分的 MyBatisX 插件部分操作；

【template】选择：MyBatis3
