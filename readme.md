

[TOC]

# Maven:学自廖雪峰官网

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



## Maven项目结构

一个使用Maven管理的普通的Java项目，它的目录结构默认如下：

```
a-maven-project
├── pom.xml	项目描述文件
├── src
│   ├── main
│   │   ├── java	存放Java源码
│   │   └── resources	存放资源文件
│   └── test
│       ├── java	存放测试源码
│       └── resources	存放测试资源
└── target	编译、打包生成的文件
```



## 小结

Maven是一个Java项目的管理和构建工具：

- Maven使用`pom.xml`定义项目内容，并使用预设的目录结构；
- 在Maven中声明一个依赖项可以自动下载并导入classpath；
- Maven使用`groupId`，`artifactId`和`version`唯一定位一个依赖。



## 具体内容

### pom.xml

Maven的第一个作用就是解决依赖管理。

我们声明了自己的项目需要`abc`，Maven会自动导入`abc`的jar包，再判断出`abc`需要`xyz`，又会自动导入`xyz`的jar包

```
# 添加依赖
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.3.2</version>
    <scope>test</scope>
</dependency>
```

Maven定义了几种**依赖关系**，分别是`compile`、`test`、`runtime`和`provided`：

| scope    | 说明                                          | 示例            |
| :------- | :-------------------------------------------- | :-------------- |
| compile  | 编译时需要用到该jar包（**默认**）             | commons-logging |
| test     | 编译Test时需要用到该jar包                     | junit           |
| runtime  | 编译时不需要，但运行时需要用到                | mysql           |
| provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |

其中，默认的`compile`是最常用的，Maven会把这种类型的依赖直接放入classpath。

`provided`依赖表示编译时需要，但运行时不需要。最典型的`provided`依赖是Servlet API，编译的时候需要，但是运行时，Servlet服务器内置了相关的jar，所以运行期不需要

### 依赖下载与缓存

Maven从中央库下载并缓存，以供下次使用。

注：只有以`-SNAPSHOT`结尾的版本号会被Maven视为开发版本，开发版本每次都会重复下载，这种SNAPSHOT版本只能用于内部私有的Maven repo，公开发布的版本不允许出现SNAPSHOT。

****

对于某个依赖，Maven只需要3个变量即可**唯一确定某个jar包**：

- groupId：属于组织的名称，类似Java的包名；
- artifactId：该jar包自身的名称，类似Java的类名；
- version：该jar包的版本。

****

**缓存目录**在：用户主目录的`.m2`目录。

如果使用**Idea**，可以在【`setting`->`Build`->`Build Tools `】下的`Maven`中修改：

一个是`User settings file`:

```
C:\Users\pbc\.m2\settings.xml
```

另一个就是本地仓库路径：

```
C:\Users\pbc\.m2\repository
```

关于Maven的其他设置都可以在这里找到



### 使用镜像仓库

> settings.xml，在这个文件里设置

可以设置Maven镜像仓库，Maven镜像仓库定期从中央仓库同步，也就是换源。

一般来说，从中央仓库下载jar包很慢。

```
<settings>
    <mirrors>
        <mirror>
            <id>aliyun</id>
            <name>aliyun</name>
            <mirrorOf>central</mirrorOf>
            <!-- 国内推荐阿里云的Maven镜像 -->
            <url>https://maven.aliyun.com/repository/central</url>
        </mirror>
    </mirrors>
</settings>
```



### Maven生命周期（lifecycle）

> https://www.liaoxuefeng.com/wiki/1252599548343744/1309301196980257
>
> lifecycle|phase|goal
>
> lifecycle有很多种，比如default和clean。它由多个phase组成
>
> phase代表生命周期中不同的阶段，他绑定了一个或多个goal
>
> goal可以认为是Java中的方法，执行真的运行逻辑，比如编译、测试、打包什么的

**lifecycle**

Maven的生命周期（lifecycle）由一系列阶段（phase）构成

一个是默认的生命周期`default`，另一个常用的生命周期是`clean`

****

**phase**

[具体内容看这里](https://www.liaoxuefeng.com/wiki/1252599548343744/1309301196980257)，通过`mvn phase1 phase2 ...`这种命令，可以指定先执行某个周期从最初到某个phase，即执行到指定的phase为止。一般都是用IDE了，用不上命令行。

例如，运行`mvn clean package`，Maven先执行`clean`生命周期并运行到`clean`这个phase，然后执行`default`生命周期并运行到`package`这个phase，实际执行的phase如下：

- pre-clean
- clean （注意这个clean是phase）
- validate
- ...
- package

在实际开发过程中，经常使用的命令有：

`mvn clean`：清理所有生成的class和jar；

`mvn clean compile`：先清理，再执行到`compile`；

`mvn clean test`：先清理，再执行到`test`，因为执行`test`前必须执行`compile`，所以这里不必指定`compile`；

`mvn clean package`：先清理，再执行到`package`。

经常用到的phase其实只有几个：

- clean：清理
- compile：编译
- test：运行测试
- package：打包

****

**goal**

执行一个phase又会触发一个或多个goal，goal的命名总是`abc:xyz`这种形式。

****

**小结**

其实我们类比一下就明白了：

- lifecycle相当于Java的package，它包含一个或多个phase；
- phase相当于Java的class，它包含一个或多个goal；
- goal相当于class的method，它其实才是真正干活的。

大多数情况，我们只要指定phase，就默认执行这些phase默认绑定的goal，只有少数情况，我们可以直接指定运行一个goal，例如，启动Tomcat服务器：`mvn tomcat:run`

### Maven插件

> https://www.liaoxuefeng.com/wiki/1252599548343744/1309301217951777

在Idea中，最右侧有`Maven`窗口，里面可以看到：`Lifecycle`和`Plugins`。

执行每个phase，都是通过某个插件（plugin）来执行的，Maven本身其实并不知道如何执行`compile`，它只是负责找到对应的`compiler`插件，然后执行默认的`compiler:compile`这个goal来完成编译。

Maven已经内置了一些常用的标准插件：

| 插件名称 | 对应执行的phase |
| :------- | :-------------- |
| clean    | clean           |
| compiler | compile         |
| surefire | test            |
| jar      | package         |

****

**自定义插件**

使用自定义插件，需要在`pom.xml`中声明（自定义插件往往需要一些配置）：

```xml
<project>
    ...
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
                            ...
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>
```

****

**常用插件**

下面列举了一些**常用的插件**：

- maven-shade-plugin：打包所有依赖包并生成可执行jar；
- cobertura-maven-plugin：生成单元测试覆盖率报告；
- findbugs-maven-plugin：对Java源码进行静态分析以找出潜在问题。

### 模块管理

> https://www.liaoxuefeng.com/wiki/1252599548343744/1309301243117601

就是把一个大项目分拆成多个模块（moudle），每个模块视为单独的项目，符合maven项目标准，然后通过每个模块的`pom.xml`建立联接。

**整个工程结构**如下：

```ascii
multiple-project
├── pom.xml
├── parent
│   └── pom.xml
├── module-a
│   ├── pom.xml
│   └── src
├── module-b
│   ├── pom.xml
│   └── src
└── module-c
    ├── pom.xml
    └── src
```

****

此外，可以通过提取不同模块间的pom.xml中的公共部分，减少冗余。

所以，一般会有以下的`pom.xml`文件：

- 根目录一个，用于统一编译

  ```xml
  <project xmlns="http://maven.apache.org/POM/4.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  
      <modelVersion>4.0.0</modelVersion>
      <groupId>com.itranswarp.learnjava</groupId>
      <artifactId>build</artifactId>
      <version>1.0</version>
      <packaging>pom</packaging>
      <name>build</name>
  
      <modules>
          <module>parent</module>
          <module>module-a</module>
          <module>module-b</module>
          <module>module-c</module>
      </modules>
  </project>
  ```

- parent目录有一个，只存放一个`pom.xml`文件，用于提取公共的依赖：

  ```xml
  ...
  	<groupId>com.itranswarp.learnjava</groupId>
      <artifactId>parent</artifactId>
      <version>1.0</version>
      <packaging>pom</packaging>
  
      <name>parent</name>
  ...
  ```

- 各模块的`pom.xml`文件，他们相互之间也可能存在相互依赖的关系

  如果模块A依赖模块B，则模块A需要模块B的jar包才能正常编译，我们需要在模块A中引入模块B：

  ```xml
      ...
      <dependencies>
          <dependency>
              <groupId>com.itranswarp.learnjava</groupId>
              <artifactId>module-b</artifactId>
              <version>1.0</version>
          </dependency>
      </dependencies>
  ```

**pom的继承关系**：

```xml
<parent>
    <groupId>com.itranswarp.learnjava</groupId>
    <artifactId>parent</artifactId>
    <version>1.0</version>
    <relativePath>../parent/pom.xml</relativePath>
</parent>
```

****

**一些关键的xml标签**：

`<relativePath>../parent/pom.xml</relativePath>`：用于继承

`<packaging>pom</packaging>`用于标记是jar包还是pom

`<modules>
<module>parent</module>
<module>module-a</module>
<module>module-b</module>
<module>module-c</module>
</modules>`：用于标记该项目包含多少个模块，实际上是以pom来定的（parent中只有pom没有src）。

****

在根目录执行`mvn clean package`时，Maven根据根目录的`pom.xml`找到包括`parent`在内的共4个`<module>`，一次性全部编译。

****

**评论的总结**

maven的多模块只是批量管理或操作用的，聚合工程或传递依赖用的，不是用来为一个项目打一个jar包的，一个工程下包括多个模块，实际上一个模块才对应一个jar包，有几个module就打出几个jar。

根目录的pom和parent的根目录，是否多余应该合并：

- 因为那个根pom.xml，实际上叫build pom，你可以选择引入parent, A, B, C，也可以选择引入parent, A, B（假设C还在开发中）。**parent和根pom各负责各自的一件事情。**

### Maven Wrapper

> https://www.liaoxuefeng.com/wiki/1252599548343744/1305148057976866
>
> 使用Maven Wrapper，可以为一个项目指定特定的Maven版本。

`mvnw`是Maven Wrapper的缩写。因为我们安装Maven时，默认情况下，系统所有项目都会使用全局安装的这个Maven版本。但是，对于某些项目来说，它可能必须使用某个特定的Maven版本，这个时候，就可以使用Maven Wrapper，它可以**负责给这个特定的项目安装指定版本的Maven**，而其他项目不受影响。

多了`mvnw`、`mvnw.cmd`和`.mvn`目录，我们只需要把`mvn`命令改成`mvnw`就可以使用跟项目关联的Maven。

Maven Wrapper的另一个作用是把项目的`mvnw`、`mvnw.cmd`和`.mvn`提交到版本库中，可以使所有开发人员使用统一的Maven版本。

****

**评论总结**

使用IDEA创建spring的project时会自动生成`mvnw`、`mvnw.cmd`和`.mvn `这3个目录。不知道有啥用都给删除了。

- idea自动给你当前的项目安装指定版本的Maven，而其他项目不受影响



### 发布Artifact

> https://www.liaoxuefeng.com/wiki/1252599548343744/1347981037010977
>
> 就是开源库给别人用
>
> 等牛逼了再学

使用Maven发布一个Artifact时：

- 可以发布到本地，然后推送到远程Git库，由静态服务器提供基于网页的repo服务，使用方必须声明repo地址；
- 可以发布到[central.sonatype.org](https://central.sonatype.org/)，并自动同步到Maven中央仓库，需要前期申请账号以及本地配置；
- 可以发布到GitHub Packages作为私有仓库使用，必须提供Token以及正确的权限才能发布和使用。

