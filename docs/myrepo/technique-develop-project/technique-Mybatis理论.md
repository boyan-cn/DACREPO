# Mybatis

> [!TIP]
>
> -  会使用，理解MyBatis原理，知道二级缓存：
>   1. [Mybatis教程（内容很全，重在上手实操）](https://www.cnblogs.com/diffx/p/10611082.html)
>   2. [MyBatis面试题八股文](https://tobebetterjavaer.com/sidebar/sanfene/mybatis.html)

## 一. Mybatis介绍

### 1）Mybatis整体架构图

-

### 2）Mybatis介绍



### 3）MyBatis使用步骤（每次）

1. 配置mybatis-config.xml 全局的配置文件 (1.数据源，2.外部的mapper)
2. 创建SqlSessionFactory
3. 通过SqlSessionFactory创建SqlSession对象
4. 通过SqlSession操作数据库 CRUD
5. 调用session.commit()提交事务
6. 调用session.close()关闭会话



实现动态代理要求：

```java
// 1. 映射文件的命名空间（namespace）必须是mapper接口的全路径
// 2. 映射文件的statement的id必须和mapper接口的方法名保持一致
// 3. Statement的resultType必须和mapper接口方法的返回类型一致
// 4. statement的parameterType必须和mapper接口方法的参数类型一致（不一定）
```

