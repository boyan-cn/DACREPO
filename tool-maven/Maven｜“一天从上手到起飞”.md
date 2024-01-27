# Maven｜“一天从上手到起飞”

## B. 上手直接用带来的问题

### Q - 在Intellij IDEA中创建maven项目时，通过New Project选择maven build system，和通过Generators-Maven Archrtype，两种创建maven项目的方式，有什么区别？

A - 在IntelliJ IDEA中创建Maven项目时，可以选择通过“New Project”直接创建，选择Maven作为构建系统，或者使用“Maven Archetype”来生成项目。这两种方法的主要区别在于项目的初始结构和预设内容：

1. **通过New Project选择Maven构建系统创建项目**：
   - 这种方法创建的是一个基本的Maven项目，通常只包含最基础的目录结构（如`src/main/java`，`src/test/java`）和一个初始的`pom.xml`文件。
   - 你需要手动添加任何所需的依赖和插件。
   - 这是一个比较空白的模板，适合已经熟悉Maven并且想要从头开始构建项目结构的开发者。

2. **通过Generators-Maven Archetype创建项目**：
   - Maven Archetype是一种项目模板工具，可以生成预定义的项目结构和配置文件。
   - 当你选择使用Archetype时，你可以从一系列现有的模板中选择，这些模板可能是由Maven中心提供的，也可能是第三方提供的，比如Spring Boot, Jersey或者其他框架的官方模板。
   - 使用Archetype生成的项目会包含特定框架或者模块所需的目录结构、依赖配置和样板代码。
   - 这种方法适合那些想要快速开始一个项目并希望遵循特定框架或者最佳实践的开发者。

​	简单来说，直接创建一个Maven项目会得到一个干净的基础结构，而使用Maven Archetype能够根据特定的模板快速构建出一个更复杂的项目结构，包括必要的依赖和可能的样板代码。选择哪一种方式取决于你的具体需求和个人喜好。如果你确切知道你的项目需要什么，或者你想要完全控制项目的结构，你可能会选择手动创建。如果你想要快速开始并利用已经存在的模板来减少初始设置的工作量，Archetype会是一个很好的选择。