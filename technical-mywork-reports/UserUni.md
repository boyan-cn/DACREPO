# 用户中心（上）主题：项目初始化

## 前端初始化

------

### 1. Ant Design Pro 开箱即用

🔗 Ant Design Pro 官方文档：地址链接：https://pro.ant.design/zh-CN/docs/getting-started/

```shell
# 使用 npm
(sudo) npm i @ant-design/pro-cli -g
	# 下载 myapp 项目
pro create myapp
? 🚀 要全量的还是一个简单的脚手架? (Use arrow keys)
❯ simple
  complete
```

1. 在目标目录下单击右键打开 iTerm，再执行命令，这样下载的 myapp 会放在这个目录下，eg. ~/DevDir/WebStormProject;

2. 如果执行失败，给前面加`sudo`再试一下;

3. 这里先做简单使用，所以先选择simple；

4. 在WebStorm内打开myapp项目之后，打开terminal输入`yarn`，自动安装项目所需依赖；

5. README.md中会有关于 `start` , `build`, ... 的 `shell`命令，阅读执行即可；

   ------

###  2. 开启Umi UI

#### （1）Umi JS 插件：MAX和UMI的区别

🔗 UmiJS 官方文档，地址链接：https://umijs.org/docs/guides/use-plugins

#### （2）开启方式

```shell
(sudo) npm install --save-dev @umijs/preset-ui
```

然后在`config.ts`中的`defineConfig`中增加：

```javascript
  // 添加 Umi UI 配置，开启页面小组件入口
  ui: {
    enable: true, // 确保 Umi UI 启用
  },
```

即可开启Umi UI小组件！

------

### 3. 前端框架介绍 & 代码瘦身

：删去项目中冗杂的功能代码：“一定每删一个都运行一下，不要全删完之后项目跑不起来了”

#### （1）去除国际化

```shell
# 在 package.json 中找到原始命令："i18n-remove": "pro i18n-remove --locale=zh-CN --write"
# 在 terminal 中执行命令：
sudo npm run i18n-remove
```

#### （2）initial project 目录

1. /config：配置（配置、代理、路由...；
2. /mock：模拟数据；
3. /node_modules：library root；
4. /public：存放静态资源：eg.网站logo、视频...；
5. /src：写代码时常用的目录；
   1. /components：组件；
   2. /pages：页面，页面和组件的区别就是页面是唯一的，组件可复用；
   3. /services：服务，
   4. ~~/locales：国际化文件；~~(deleted)
6. /test：测试目录；
8. 其中，`/services/app.tsx` 是 **整个项目的入口**；

#### （3）删除

1. /src/locales：国际化文件，删掉；
2. /src/swagger：接口文档，先删掉；
3. /config/oneapi.json：不用了解是干嘛的，没用，删掉；

#### （4）注意事项

1. 删一个文件跑一遍；
2. 发现被删除的文档引用没删干净：全局搜索被删除的文档名，在确定没用之后，可以把使用的地方也删掉，再次运行；
   - 步骤如下：
     1. 删去`oneapi.json`后运行报错，`ERROR: File ~/oneapi.json not found. `
     2. 使用全局搜索：`oneapi.json`，发现一处引用；
     3. 删去此处openAPI的引用代码，再次运行；
     4. 运行成功；

------



## 后端初始化

：现在Spring官方的版本：Java 17，代替之前的Java 8；

------

### 1. 环境准备：MySQL数据库安装

- 常用mysql命令： https://www.jianshu.com/p/8f8d8161631f ；

使用homebrew快速下载安装mysql

```shell
brew -v								   	  # 检查 homebrew 安装状态
brew install mysql          	# 快速安装mysql
mysql_secure_installation		  # mysql安全配置（包括设置root密码等）
mysql -u root -p						 # 登录（用户名：root，密码：刚才设置的）
```

：不知道上次为什么安装那么久，可能冲突了，这次安装大约耗时5min～

------

### 2. Java后端项目初始化的三种方式

1. github上找别人提交的现成的模板（个人来说不推荐）：提交者做的微小改动可能会导致未知bug；
2. SpringBoot官方的模板生成器：https://start.spring.io/ （推荐），在`EXPLORE`探索部分可以直接预览生成的代码里有什么;
3. Intellij IDEA（最方便也是最推荐的）

------

### 3. 项目框架配置

1. Name：user-center

2. Language：Java

3. Type：Maven

4. Group：com.boyan，一般是公司名/组织名

5. Artifact、Package name：默认根据上述填写自动生成

6. Project SDK：直接在IDEA中选择相应的版本号下载就好，不需要去官网上下载，一般选择第一个下载就好；

7. Java：17

8. Packaging：Jar

9. 配置依赖 `Dependencies` 

   1. /Developer Tools
      1. Lombok：注解工具，帮助自动生成get/set方法等；
      2. Spring Boot DevTools：热更新，改代码后自动重启项目；
      3. Spring Configuration Processor：读取Spring注解需要；
   2. MySQL Driver
   3. /Web
      - Spring Web：RESTful API，提供访问能力；
   4. /SQL
      - MyBatis Framework

   剩下的可能需要后面手动配置，先不加了，eg. JUnit、Redis...；

10. 点击`Finish`，生成代码；

------

### 4. Mybatis-plus 整合

- Mybatis-plus 官方文档地址： https://baomidou.com/ 

- 快速开始一个demo的基本步骤：1. 打开官方文档  2. 按照官方文档“快速开始”的步骤创建并跑通原始demo；

- **这里官方（快速开始）中使用的是h2数据库，要把相应的地方改成mysql数据库**；

#### （1）建立数据库连接、创建数据库（boyan-uni）

```sql
# 跑通官方demo的sql语句

DROP TABLE IF EXISTS `user`;

CREATE TABLE `user`
(
    id BIGINT NOT NULL COMMENT '主键ID',
    name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
    age INT NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
);

DELETE FROM `user`;

INSERT INTO `user` (id, name, age, email) VALUES
                                              (1, 'Jone', 18, 'test1@baomidou.com'),
                                              (2, 'Jack', 20, 'test2@baomidou.com'),
                                              (3, 'Tom', 28, 'test3@baomidou.com'),
                                              (4, 'Sandy', 21, 'test4@baomidou.com'),
                                              (5, 'Billie', 24, 'test5@baomidou.com');
```

#### （2）添加依赖：pom.xml

```xml
<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.1</version>
</dependency>
<!-- mysql已经在生成初始化项目的时候自动添加依赖 -->
```

- 每增加一个配置，点击maven的刷新键，刷新一下；

#### （3）配置：application.yml 

1. 修改文件类型：把application.properties改名成application.yml，因为.yml文件支持嵌套，会很方便；

2. 在 `application.yml` 配置文件中添加 MySQL 数据库的相关配置：

```yml
# DataSource Config
# 从官方文档处复制，将 h2 数据库配置相应的更改为 mysql 数据库配置
spring:
  application:
    name: user-center
  # 数据源配置
  datasource:
    # 改成 mysql 驱动
    driver-class-name: com.mysql.jdbc.Driver
    # 从 mysql 属性中获取数据库的 url ，复制（手动修改数据库名和密码）
    url: jdbc:mysql://localhost:3306/数据库名
    username: root
    password: ****	
# 手动定义端口号的目的：1）更直观 2）如果出现冲突方便修改
server:
  port: 8080
```

#### （4）扫描目录 @MapperScan 

- eg. `/src/main/java/com.boyan.usercenter/mapper/UserMapper.java`
- 在 Spring Boot 启动类 `ProjectNameApplication.java` 中添加 `@MapperScan` 注解，扫描 Mapper 文件夹，添加后类内容如下：

```java
package com.boyan.usercenter;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.boyan.usercenter.mapper") // 新增：@MapperScan 里的扫描路径修改成自己的mapper路径，右键～/mapper复制路径可得

public class UserCenterApplication {
    public static void main(String[] args) {
        // 启动 springboot 项目
        SpringApplication.run(UserCenterApplication.class, args);
    }
}
```

- Mapper 接口的作用及内容？User & UserMapper

​		- mybatis 是一个对数据库进行操作的框架，可以在mapper中定义操作数据库的方法，比如这里：`addUser()`、`deleteUser()`；

​		- 因为我们引入了 `mybatis-plus`，所以可以直接继承 `BaseMapper<Object>` 基类，查看源码可知已经对所有 `crud` 操作进行了相应的封装，所以在使用的时候，直接继承基类后使用即可，如果源码没有，再进行`@Override`或增加新方法；















