# 解决方案 - Springboot + MyBatis 框架 “Cause: org.springframework.jdbc.CannotGetJdbcConnectionException” 问题



## 出错类型1: 连接MySQL失败

### a. 报错信息

```bash
Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection; nested exception is com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Access denied for user 'root'@'127.0.0.1' to database 'mybatis-demo'
```

### b. 解决过程

这个问题真的坑了我 2 天半，检查了 XXXMapper.xml 的 sql 语句以及电脑主机的 mysql 密码，确认无误，在 Google 上搜索多个方案尝试无果！重装 MySQL，也没有解决。

我的 MySQL 的安装方式是通过 homebrew 快速安装的，所以尝试从 homebrew 开始卸载重装，然后问题解决。

这个问题解决后下一个类型的报错又出现了... ...



## 出错类型2: 连接 MySQL 使用的密码错误

### a. 报错信息

```bash
Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection; nested exception is java.sql.SQLException: Access denied for user 'root'@'localhost' (using password: YES)
```

### b. 解决过程

1. 重装 mysql，重新设置 mysql_secure_installation，过程中意外发现输错密码后报错和项目中一样（但项目中 root 的密码是正确的、以及检查 mysql 连接正常），于是尝试在 terminal 通过命令行修改 root 密码；
2. 在重新设置新密码后，再次运行正常，问题解决。