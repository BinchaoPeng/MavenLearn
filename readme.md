

[TOC]

# Maven

> https://www.liaoxuefeng.com/wiki/1252599548343744/1309301146648610
>
> Maven是一个**Java项目管理和构建工具**，
>
> 它可以定义项目结构、项目依赖，并使用**统一**的方式进行**自动化构建**。
>
> 也就是提供了一个标准的流程，方便从业者提升工作效率



Maven就是是专门为Java项目打造的管理和构建工具，它的主要功能有：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。



### Maven项目结构

一个使用Maven管理的普通的Java项目，它的目录结构默认如下：

```
a-maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```