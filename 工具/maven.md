# Maven

## 一、仓库

-   本地
-   中央
-   远程

### 1.1 本地仓库

-   Maven本地仓库，在安装maven后不会创建，在第一次执行maven的时候才会创建
-   运行Maven的时候，Maven所需要的所有构建都是从本地仓库获取的，如果本地仓库没有，它首先会从本地仓库获取，如果本地仓库没有，它会首先尝试从远程仓库下载到本地，再使用本地的构件
-   默认位置为.m2/repository
-   可以手动设置

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository>本地仓库位置</localRepository>
</settings>
```

### 1.2 中央仓库

-   由Maven社区提供的仓库，其中包含了大量的常用库
-   中央社区包含了绝大多数的开源java构件，以及源码、作者信息、SCM、信息、许可证信息等。
-   一般来说，简单的Java项目依赖构建都可以在这里下载到
-   关键概念：
    -   这个仓库由Maven社区管理
    -   不需要配置
    -   需要通过网络访问

### 1.3 远程仓库

-   如果在Maven本地仓库和中央仓库都找不到依赖文件，会停止构建并输出错误信息到控制台，为了避免这种情况，Maven提供了远程仓库的概念，开发人员可以自己定制仓库，如下配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>com.companyname.projectgroup</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>
   <dependencies>
      <dependency>
         <groupId>com.companyname.common-lib</groupId>
         <artifactId>common-lib</artifactId>
         <version>1.0.0</version>
      </dependency>
   <dependencies>
   <repositories>
      <repository>
         <id>companyname.lib1</id>
         <url>http://download.companyname.org/maven2/lib1</url>
      </repository>
      <repository>
         <id>companyname.lib2</id>
         <url>http://download.companyname.org/maven2/lib2</url>
      </repository>
   </repositories>
</project>
```

### 1.4 依赖搜索顺序

-   本地仓库 -> 中央仓库 -> 远程仓库

### 1.5 国内仓库 Aliyun

```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

```xml
<repository>
  <id>spring</id>
  <url>https://maven.aliyun.com/repository/spring</url>
  <releases>
    <enabled>true</enabled>
  </releases>
  <snapshots>
    <enabled>true</enabled>
  </snapshots>
</repository>
```

-   拉取命令

    -   `mvn install`

-   grade

    -   ```gradle
        allprojects {
          repositories {
            maven {
              url 'https://maven.aliyun.com/repository/public/'
            }
            mavenLocal()
            mavenCentral()
          }
        }
        ```

    -   ```gradle
        allProjects {
          repositories {
            maven {
              url 'https://maven.aliyun.com/repository/public/'
            }
            maven {
              url 'https://maven.aliyun.com/repository/spring/'
            }
            mavenLocal()
            mavenCentral()
          }
        }
        ```

    -   安装依赖

        -   `gradle dependencies `

## 二、Maven插件

-   生命周期

>   clean：项目清理
>
>   default/build：项目部署处理
>
>   site：项目站点文档创建的处理

-   Maven实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成的。
-   Maven的插件通常被用来：

>   创建jar文件
>
>   创建war文件
>
>   编译代码文件
>
>   代码单元测试
>
>   创建工程文档
>
>   创建工程报告

-   Maven提供了一个目标的集合
    -   `mvn [plugin-name]:[goal-name]`
    -   eg：`mvn compiler:compile`

### 2.1 插件类型

| 类型              | 描述                                          |
| ----------------- | --------------------------------------------- |
| Build plugins     | 在构建时执行，并在pom.xml的元素中配置         |
| Reporting plugins | 在网站生成过程中执行，并在pom.xml的元素中配置 |

-   常用插件的列表：

| 插件     | 描述                                                |
| :------- | :-------------------------------------------------- |
| clean    | 构建之后清理目标文件。删除目标目录。                |
| compiler | 编译 Java 源文件。                                  |
| surefile | 运行 JUnit 单元测试。创建测试报告。                 |
| jar      | 从当前工程中构建 JAR 文件。                         |
| war      | 从当前工程中构建 WAR 文件。                         |
| javadoc  | 为工程生成 Javadoc。                                |
| antrun   | 从构建过程的任意一个阶段中运行一个 ant 任务的集合。 |

## 三、构建项目

-   创建基于Maven的java项目

```
mvn archetype:generate "-DgroupId=com.companyname.bank" "-DartifactId=consumerBanking" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"
```

>   -DgroupId：组织名
>
>   -Dartifactld：项目名-模块名
>
>   -DarchetypeArtifactId：指定ArtifactId，创建一个简单的java项目
>
>   -DinteractiveMode：是否使用交互模式

## 四、分模块开发与设计

-   分模块设计，增加模块可用性
-   将各个模块拆分
-   eg：SSM项目
    -   拆分entity包
        -   将entity变为一个新的模块，将entity包中的文件复制，此时因为模块中的实体类仅为java原生api，因此pom文件不会追加依赖
    -   拆分dao包
        -   不同模块导入需要先install到本地仓库中，依次install即可
        -   分页插件需保留

## 五、聚合

-   打包类型为pom

## 六、继承

-   定义父工程

```xml
<parent>
  xxx
  xxx
  xxx
  <!--填写父工程的pom文件-->
  <relativePath>../xx/pom.xml</relativePath>
</parent>
```

-   父工程定义依赖管理

```xml
<dependencyManagement>
  <dependencies>
  	<dependency>
    	<!--具体依赖-->
    </dependency>
  </dependencies>
</dependencyManagement>
```

-   子工程此时无需配置版本

![901637562798_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/901637562798_.pic_hd.jpg)

## 七、属性

### 7.1 定义

```xml
<properties>
	<变量名>值</变量名>
</properties>
```

### 7.2 使用

```xml
${变量名}
```

## 八、工程版本

-   SNAPSHOT 快照版本
-   RELEASE 发布版本

## 九、资源配置

```xml
<properties>
	<变量名>值</变量名>
</properties>

<!--build中定义-->
<build>
	<resource>
  	<directory>properties目录</directory>
    <!--开启文件过滤-->
    <filtering>true</filtering>
  </resource>
</build>
```

## 十、多环境

![911637563359_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/911637563359_.pic_hd.jpg)

-   定义多环境

```xml
<profiles>
	<profile>
  	<id>pro_env</id>
    <properties>
    	xxxxxxx
    </properties>
  </profile>
  <profile>
  	<id>dep_env</id>
  </profile>
</profiles>
```

-   IDEA设置Maven使用环境

![截屏2021-11-22 14.47.28](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-22%2014.47.28.png)

## 十一、私服

![921637563725_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/921637563725_.pic_hd.jpg)

-   Nexus

-   mvn deploy 发布私服jar包