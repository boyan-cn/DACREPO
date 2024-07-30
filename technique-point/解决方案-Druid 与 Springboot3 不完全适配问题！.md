# 解决方案 - Druid 与 Springboot3 不完全适配问题！

> [!CAUTION]
>
> 最新版本的druid-spring-boot-3-starter已经兼容！使用1.2.20 以上的版本就不需要配置这个文件了！



##### 方案1：使用 SpringBoot2 + Druid 的版本，兼容性还好。

##### 方案2：使用 SpringBoot3 + Druid，补充：自动装配的配置文件。

- druid-spring-boot-3-starter 目前最新版本是1.2.18，虽然适配了 SpringBoot3，但缺少自动装配的配置文件

```yaml
位置: resources.META-INF.spring.文件名 
	文件名: org.springframework.boot.autoconfigure.AutoConfiguration.imports
	内容: com.alibaba.druid.spring.boot3.autoconfigure.DruidDataSourceAutoConfigure
```

##### 方案3：使用 SpringBoot3 + Druid，使用 druid-spring-boot-3-starter 1.2.20 以上版本

- 最新版本的druid-spring-boot-3-starter已经兼容，使用1.2.20 以上的版本就不需要配置这个文件了！
