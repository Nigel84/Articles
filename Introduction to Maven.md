# Maven入门

## 什么是Maven?
Maven，鼎鼎大名，在今天之前，我对于它一直是处于一种"只闻其名不见其人"的状态。之所以说"只闻其名"，是因为Maven太有名了，它是Apache基金会的顶级项目，一般情况下，被Apache看中的都是精品。之所以说"不见其人"，是因为之前的工作中不需要使用到Maven，因此对Maven并不熟。

正巧，最近换了工作，新工作中项目是使用Maven构建的，这让我必须要会使用Maven。因此，本篇文章就零开始对Maven进行一个学习，在我学习的过程中，必然遇到很多问题，在思考、解决这些问题的同时，输出一些文字，相信会对各位网友朋友们有帮助。

OK，那么让我们开始吧。

## Maven的作用是什么

这是我遇到的第一个问题，甚至在看了一些Maven的资料之后，我还是会有疑问，到底Maven是干什么用的？在继续学习以及自己写了例子之后，我有了一定的感悟，下面尝试回答一下这个问题。首先，假如使用传统的项目构建方式，可能会有这样的困惑：

开发中，为了保证编译通过，我们会到处去寻找jar包，当编译通过了，运行的时候，却发现"ClassNotFoundException"，难道还差jar包？再去找找吧

每个Java项目的目录结构都没有一个统一的标准，配置文件到处都是，单元测试代码到底应该放在那里也没有一个权威的规范

因此，我们就要用到Maven（使用Ant也可以，不过编写Ant的xml脚本比较麻烦）----一个项目管理工具。Maven主要做了两件事：

统一开发规范与工具

统一管理jar包

下面我们来对比一下，首先建立一个普通的Java工程，是这样的：
(http://images2015.cnblogs.com/blog/801753/201603/801753-20160323142502401-108348091.png)

这个我们都很熟悉，src下建包写代码，那么配置文件放在哪里？单元测试代码放在哪里？没有一个统一标准，更多时候都是开发者的自由发挥，每个人有自己的风格，这并不十分适合团队协作。接下来，看一下使用maven构建一个普通Java项目之后的目录结构：


看到使用Maven构建的普通Java项目，对源代码、单元测试代码、资源乃至后续需要的文件都有专门的目录规划。

上面的最后有一个pom.xml，这是Maven的核心配置文件，pom称为Project Object Model（项目对象模型），它用于描述整个Maven项目，所以也称为Maven描述文件。

当然事情不会这么简单，接下来，继续进入Maven的世界吧。

pom.xml
打开pom.xml，最基础的是这样的：
```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">     
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.xrq.withmaven</groupId>
  <artifactId>withmaven</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <build/>
</project>
```
因为这个配置文件是Maven的核心，因此有必要详细解读一下pom.xml，来先看一下上面的几个

- modelVersion:
  指定了当前Maven模型的版本号，对于Maven2和Maven3来说，它只能是4.0.0
  
- groupId: 
  顾名思义，这个应该是公司名或是组织名。一般来说groupId是由三个部分组成，每个部分之间以"."分隔，第一部分是项目用途，比如用于商业的就是"com"，用于非营利性组织的就　　是"org"；第二部分是公司名，比如"tengxun"、"baidu"、"alibaba"；第三部分是你的项目名
  
- artifactId: 
  可以认为是Maven构建的项目名，比如你的项目中有子项目，就可以使用"项目名-子项目名"的命名方式
  
- version: 
  版本号，SNAPSHOT意为快照，说明该项目还在开发中，是不稳定的版本。在Maven中很重要的一点是，**groupId、artifactId、version三个元素生成了一个Maven项目的基本坐标**，这非常重要，我在使用和研究Maven的时候多次感受到了这点。

在上面的这些元素之外，还有一些元素，同样罗列一下

- packing: 
  项目打包的类型，可以使jar、war、rar、ear、pom，默认是jar
  
- dependencies 和dependency: 
  前者包含后者。前面说了，Maven的一个重要作用就是统一管理jar包，为了一个项目可以build或运行，项目中不可避免的，会依赖很多其他的jar包，在Maven中，这些依赖就被称为dependency。

　说到这里，就有一个本地仓库和远程仓库的概念了。官方下载的本地仓库的配置在"%MAVEN_HOME%\conf\settings.xml"里面，找一下"localRepository"就可以了；MyEclipse默认的本地仓库的地址在"{user.home}/.m2/repository"路径下，同样找一下"localRepository"就可以找到MyEclipse默认的本地仓库了。

　本地仓库和远程仓库是这样的，**Maven工程首先会从本地仓库中获取jar包，当无法获取指定jar包时，本地仓库会从远程仓库（中央仓库）中下载jar包，并放入本地仓库以备将来使用。**

　举个例子，比方说我的项目中用到了MyBatis，那么可以这么配置：
  ```
  <dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.2.5</version>
    </dependency>
</dependencies>
```
之前有说过groupId、artifactId、version唯一标识一个Maven项目，有了这三个元素，我们就可以去远程仓库下载MyBatis3.2.5.jar到本地仓库了。回想我们之前的做法，如果要MyBatis的jar包，发现没有，然后去网上下载一个，需要另外的jar包，然后去网上下载一个，但是有了Maven，就方便多了，只需要配置jar包对应的dependency依赖，Maven会自动帮助我们去远程仓库中下载jar包到本地仓库中。

- properties:
  properties是用来定义一些配置属性的，例如project.build.sourceEncoding（项目构建源码编码方式），可以设置为UTF-8，防止中文乱码，也可定义相关构建版本号，便于日后统一升级。
  
- build:
  build表示与构建相关的配置，比如build下有finalName，表示的就是最终构建之后的名称。
  
## Maven工程目录结构
接着解释一下Maven的目录结构：

main目录下是项目的主要代码，test目录下存放测试相关的代码

编译输出后的代码会放在target目录下

src/main/java下存放Java代码，src/main/resources下存放配置文件

这里没有webapp，Web项目会有webapp目录，webapp下存放Web应用相关代码

pom.xml是Maven项目的配置文件


[Thanks to reference](http://www.lai18.com/content/10982773.html)
[Apache Official docs of Settings Reference] (http://maven.apache.org/settings.html)
[Apache Official docs of Settings Reference] (http://maven.apache.org/pom.html)
