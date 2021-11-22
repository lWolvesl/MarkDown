# 需求

## 定位

从单一架构阶段到分布式架构阶段的过渡

帮助创业者发布创业项目，向大众募集启动资金的融资平台

## 需求分析

- 众筹项目展示页面
- 众筹项目细节展示
- 支持页面（掏钱）
- 个人中心

  - 我的众筹
    - 发起众筹-》协议。。。
- 管理员界面

  - 管理员数据维护

    - 角色数据维护

    - 菜单数据维护
- 权限控制 - SpringSecurity

## 技术选型

- Spring
- SpringMVC
- MyBatis
- Maven

![image-20211012102143914](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211012102143914.png)

- SSM整合清单

  - 在子工程中加入搭建环境所需要的具体依赖
  - 准备jdbc.properties
  - 创建Spring配置文件转吗配置Spring和Mybatis整合相关
  - 在Spring的配置 文件中加载jdbc.properties属性文件
  - 配置数据源
  - 测速数据库连接
  - 配置SqlSessionFactoryBean
    - 装配数据源
    - 指定xxxMapper.xml配置文件的位置
    - 指定MyBatis全局配置文件的位置（可选）

  - 配置MapperScannerConfigurer
  - 测试是否可以装配XXXMapper接口并操作数据库

```xml
<dependencies>
    <dependency>
        <groupId>it.shang</groupId>
        <artifactId>it-04-entity</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>${it.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.10</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.2</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>3.3.0</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.26</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.2.8</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.12.5</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.12.5</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl -->
    <dependency>
    <groupId>javax.servlet.jsp.jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.8</version>
    </dependency>
</dependencies>
```

spring-mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd ">

    <context:property-placeholder location="jdbc.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${jdbc.userName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="driverClassName" value="${jdbc.driver}"/>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:mabatis/mapper/*Mapper.xml"/>
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="mapper"/>
    </bean>


</beans>
```
