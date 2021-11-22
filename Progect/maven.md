# MAVEN

##	maven逆向工程

在idea 中使用 mybatis的 mybatis-generator-maven-plugin 可以根据数据库 生成 dao层，pojo类，Mapper文件。

 

一: 在 pom.xml 中添加相关插件依赖。

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
 1         <!-- MySQL 连接驱动依赖 -->
 2         <dependency>
 3             <groupId>mysql</groupId>
 4             <artifactId>mysql-connector-java</artifactId>
 5         </dependency>
 6 
 7         <!-- Junit -->
 8         <dependency>
 9             <groupId>junit</groupId>
10             <artifactId>junit</artifactId>
11             <version>4.12</version>
12         </dependency>
13         <dependency>
14             <groupId>org.mybatis.generator</groupId>
15             <artifactId>mybatis-generator-core</artifactId>
16             <version>1.3.2</version>
17         </dependency>
18         <dependency>
19             <groupId>log4j</groupId>
20             <artifactId>log4j</artifactId>
21             <version>1.2.17</version>
22         </dependency>
23         <dependency>
24             <groupId>org.mybatis</groupId>
25             <artifactId>mybatis</artifactId>
26             <version>3.2.6</version>
27         </dependency>
28     </dependencies>
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
 1 <plugin>
 2                 <groupId>org.mybatis.generator</groupId>
 3                 <artifactId>mybatis-generator-maven-plugin</artifactId>
 4                 <version>1.3.5</version>
 5                 <configuration>
 6                     <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
 7                     <verbose>true</verbose>
 8                     <overwrite>true</overwrite>
 9                 </configuration>
10 
11             </plugin>
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

 

二：创建 generatorConfig.xml 配置文件

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<!--生成器-->
<generatorConfiguration>
    <!--一.导入属性配置-->
    <properties resource="generator.properties"></properties>
    <!--二.在MBG工作的时候，需要额外加载的依赖包
        location属性指明加载jar/zip包的全路径-->
    <classPathEntry location="${jdbc.driverLocation}"/>
    <!--
           三：context:生成一组对象的环境
           id:必选，上下文id，用于在生成错误时提示
           defaultModelType:指定生成对象的样式
               1，conditional：类似hierarchical；
               2，flat：所有内容（主键，blob）等全部生成在一个对象中；
               3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象中(record class)
           targetRuntime:
               1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
               2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
           introspectedColumnImpl：类全限定名，用于扩展MBG
       -->

    <context id="tables" targetRuntime="MyBatis3">

        <plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin"/>

        <!-- 不生成注释 -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--jdbc的数据库连接 -->
        <jdbcConnection driverClass="${jdbc.driverClass}" connectionURL="${jdbc.connectionURL}"
                        userId="${jdbc.userName}"
                        password="${jdbc.password}">
            <!-- 这里面也可以设置property属性，每一个property属性都设置到配置的Driver上 -->
        </jdbcConnection>

        <!-- java类型处理器
            用于处理DB中的类型到Java中的类型，默认使用JavaTypeResolverDefaultImpl；
            注意一点，默认会先尝试使用Integer，Long，Short等来对应DECIMAL和 NUMERIC数据类型；
        -->
        <javaTypeResolver type="org.mybatis.generator.internal.types.JavaTypeResolverDefaultImpl">
            <!--
               true：使用BigDecimal对应DECIMAL和 NUMERIC数据类型
               false：默认,
                   scale>0;length>18：使用BigDecimal;
                   scale=0;length[10,18]：使用Long；
                   scale=0;length[5,9]：使用Integer；
                   scale=0;length<5：使用Short；
            -->
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>


        <!-- java模型创建器，是必须要的元素
            负责：1，key类（见context的defaultModelType）；2，java类；3，查询类
            targetPackage：生成的类要放的包，真实的包受enableSubPackages属性控制；
            targetProject：目标项目，指定一个存在的目录下，生成的内容会放到指定目录中，如果目录不存在，MBG不会自动建目录
         -->

        <javaModelGenerator targetPackage="com.springweekend.week.pojo" targetProject="src/main/java">
            <!-- 是否对model添加 构造函数 -->
            <property name="constructorBased" value="true"/>
            <!--是否允许子包，即targetPackage.schemaName.tableName-->
            <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package，最终生成的类放在这个package下，默认为false -->
            <property name="enableSubPackages" value="false"/>
            <!-- 建立的Model对象是否 不可改变  即生成的Model对象不会有 setter方法，只有构造方法 -->
            <property name="immutable" value="true"/>
            <!-- 给Model添加一个父类 ,设置一个根对象，
                如果设置了这个根对象，那么生成的keyClass或者recordClass会继承这个类；在Table的rootClass属性中可以覆盖该选项
                注意：如果在key class或者record class中有root class相同的属性，MBG就不会重新生成这些属性了，包括：
                    1，属性名相同，类型相同，有相同的getter/setter方法；
-->
            <!--<property name="rootClass" value="com.foo.louis.Hello"/>-->
            <!-- 是否对类CHAR类型的列的数据进行trim操作 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>


        <!--Mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件org.louis.hometutor.domain -->
        <sqlMapGenerator targetPackage="com.springweekend.week.sql" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>

        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
                        type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
                        type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
                        type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
                -->
        <javaClientGenerator targetPackage="com.springweekend.week.dao"
                             targetProject="src/main/java"
                             type="XMLMAPPER">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 指定数据库表 此处还有很多自定义配置，根据个人需求进行设置即可 -->
        <!--<table tableName="user"></table>-->
        <!--<table tableName="order"></table>-->
        <!--<table tableName="detail"></table>-->
        <!--<table tableName="item"></table>-->

        <!-- 选择一个table来生成相关文件，可以有一个或多个table，必须要有table元素
                   选择的table会生成一下文件：
                   1，SQL map文件
                   2，生成一个主键类；
                   3，除了BLOB和主键的其他字段的类；
                   4，包含BLOB的类；
                   5，一个用户生成动态查询的条件类（selectByExample, deleteByExample），可选；
                   6，Mapper接口（可选）

                   tableName（必要）：要生成对象的表名；
                   注意：大小写敏感问题。正常情况下，MBG会自动的去识别数据库标识符的大小写敏感度，在一般情况下，MBG会
                       根据设置的schema，catalog或tablename去查询数据表，按照下面的流程：
                       1，如果schema，catalog或tablename中有空格，那么设置的是什么格式，就精确的使用指定的大小写格式去查询；
                       2，否则，如果数据库的标识符使用大写的，那么MBG自动把表名变成大写再查找；
                       3，否则，如果数据库的标识符使用小写的，那么MBG自动把表名变成小写再查找；
                       4，否则，使用指定的大小写格式查询；
                   另外的，如果在创建表的时候，使用的""把数据库对象规定大小写，就算数据库标识符是使用的大写，在这种情况下也会使用给定的大小写来创建表名；
                   这个时候，请设置delimitIdentifiers="true"即可保留大小写格式；

                   可选：
                   1，schema：数据库的schema；
                   2，catalog：数据库的catalog；
                   3，alias：为数据表设置的别名，如果设置了alias，那么生成的所有的SELECT SQL语句中，列名会变成：alias_actualColumnName
                   4，domainObjectName：生成的domain类的名字，如果不设置，直接使用表名作为domain类的名字；可以设置为somepck.domainName，那么会自动把domainName类再放到somepck包里面；
                   5，enableInsert（默认true）：指定是否生成insert语句；
                   6，enableSelectByPrimaryKey（默认true）：指定是否生成按照主键查询对象的语句（就是getById或get）；
                   7，enableSelectByExample（默认true）：MyBatis3Simple为false，指定是否生成动态查询语句；
                   8，enableUpdateByPrimaryKey（默认true）：指定是否生成按照主键修改对象的语句（即update)；
                   9，enableDeleteByPrimaryKey（默认true）：指定是否生成按照主键删除对象的语句（即delete）；
                   10，enableDeleteByExample（默认true）：MyBatis3Simple为false，指定是否生成动态删除语句；
                   11，enableCountByExample（默认true）：MyBatis3Simple为false，指定是否生成动态查询总条数语句（用于分页的总条数查询）；
                   12，enableUpdateByExample（默认true）：MyBatis3Simple为false，指定是否生成动态修改语句（只修改对象中不为空的属性）；
                   13，modelType：参考context元素的defaultModelType，相当于覆盖；
                   14，delimitIdentifiers：参考tableName的解释，注意，默认的delimitIdentifiers是双引号，如果类似MYSQL这样的数据库，使用的是`（反引号，那么还需要设置context的beginningDelimiter和endingDelimiter属性）
                   15，delimitAllColumns：设置是否所有生成的SQL中的列名都使用标识符引起来。默认为false，delimitIdentifiers参考context的属性

                   注意，table里面很多参数都是对javaModelGenerator，context等元素的默认属性的一个复写；
                -->

        <table tableName="student" domainObjectName="Student"

               enableCountByExample="false"

               enableUpdateByExample="false"

               enableDeleteByExample="false"

               enableSelectByExample="false"

               selectByExampleQueryId="false">
            <!-- 如果设置为true，生成的model类会直接使用column本身的名字，而不会再使用驼峰命名方法，比如BORN_DATE，生成的属性名字就是BORN_DATE,而不会是bornDate -->
            <property name="useActualColumnNames" value="flase"/>
        </table>
    </context>
</generatorConfiguration>
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

 

 

数据库配置文件 application.properties

```
jdbc.driverLocation=E:\\software\\maven3.5\\repository\\mysql\\mysql-connector-java\\8.0.15\\mysql-connector-java-8.0.15.jar
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT%2B8&nullCatalogMeansCurrent=true
jdbc.userName=root
jdbc.password=123789
```

 

这是添加后控制器输出日志：

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.springweekend:week >-----------------------
[INFO] Building week 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- mybatis-generator-maven-plugin:1.3.5:generate (default-cli) @ week ---
[INFO] Connecting to the Database
[INFO] Introspecting table student
[INFO] Generating Record class for table student
[INFO] Generating Mapper Interface for table student
[INFO] Generating SQL Map for table student
[INFO] Saving file StudentMapper.xml
[INFO] Saving file Student.java
[INFO] Saving file StudentMapper.java
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\pojo\Student.java was overwritten
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\dao\StudentMapper.java was overwritten
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.918 s
[INFO] Finished at: 2019-04-12T18:06:46+08:00
[INFO] ------------------------------------------------------------------------
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

 

如果不添加的话，就会出现警告：　

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.springweekend:week >-----------------------
[INFO] Building week 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- mybatis-generator-maven-plugin:1.3.5:generate (default-cli) @ week ---
[INFO] Connecting to the Database
[INFO] Introspecting table student
[INFO] Generating Record class for table student
[INFO] Generating Mapper Interface for table student
[INFO] Generating SQL Map for table student
[INFO] Generating Record class for table student
[INFO] Generating Mapper Interface for table student
[INFO] Generating SQL Map for table student
[INFO] Saving file StudentMapper.xml
[INFO] Saving file StudentMapper.xml
[INFO] Saving file Student.java
[INFO] Saving file StudentMapper.java
[INFO] Saving file Student.java
[INFO] Saving file StudentMapper.java
[WARNING] Table Configuration student matched more than one table (test..student,nba..student)
[WARNING] Cannot obtain primary key information from the database, generated objects may be incomplete
[WARNING] Cannot obtain primary key information from the database, generated objects may be incomplete
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\pojo\Student.java was overwritten
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\dao\StudentMapper.java was overwritten
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\pojo\Student.java was overwritten
[WARNING] Existing file E:\software\week\src\main\java\com\springweekend\week\dao\StudentMapper.java was overwritten
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.197 s
[INFO] Finished at: 2019-04-12T18:03:51+08:00
[INFO] ------------------------------------------------------------------------

Process finished with exit code 0
```

![复制代码](https://common.cnblogs.com/images/copycode.gif)

 

另外，添加命令行：-Dmybatis.generator.overwrite=true(如果数据库中表改动了，就可以对创建的dao ,pojo,Mapper文件重载)

mybatis-generator:generate（显示运行的详细信息）![img](https://img2018.cnblogs.com/blog/1512366/201904/1512366-20190412181120707-1316597610.png)