# SpringBoot

## 一、 SpringBoot 配置

### 1.1 yaml数据格式

- 对象（map）：键值对的集合

```yaml
person:
	name: xxx
person: {name: xxx}
```

- 数组：一组按次序排类的值

```yaml
address:
	- beijing
	- shanghai
address: [beijing,shanghai]
```

- 纯量：单个的、不可再分的值

```yaml
msg1: 'hello \n world' #单引号忽略转义字符
msg2: "hello \n world" #双引号识别转义字符
```

- 参数引用

```yaml
name: zhangsan
person:
	name: ${name}
```

### 1.2 读取配置内容

- <font color="green">@Value</font>

```java
@Value("${name}")
String name01;

@Value("${person01.name}")
String name02;
```

- <font color="green">Environmen</font>:一键获取

```java
import org.springframework.core.env.Environment;

		@Autowired
    private Environment ev;

    @GetMapping("/ev")
    public Map getAll(){
        Map<String ,Object> map = new HashMap<>();
        map.put("name",ev.getProperty("name"));
        map.put("person01.name",ev.getProperty("person01.name"));
        map.put("person02.name",ev.getProperty("person02.name"));
        return map;
    }
```

- <font color="green">@ConfigurationProperties</font>

```java
@Data
@ToString
@Component
@ConfigurationProperties(prefix = "person01")
public class Person {
    private String name;
    private int id;
    private int age;
}
```

### 1.3 profile

- 功能：**进行动态配置切换**
- 顺序：配置-》激活

![截屏2021-10-05 下午5.12.25](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-05%20%E4%B8%8B%E5%8D%885.12.25.png)

![截屏2021-10-05 下午5.14.00](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-05%20%E4%B8%8B%E5%8D%885.14.00.png)

java -jar 带参启动

![image-20211005171647696](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211005171647696.png)

虚拟机参数

-Dspring.profile.active=xxx

### 1.4 内部配置加载顺序

![image-20211005171920181](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211005171920181.png)

### 1.5 外部配置加载顺序

https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config

![截屏2021-10-05 下午5.23.33](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-05%20%E4%B8%8B%E5%8D%885.23.33.png)

- 控制台参数
  - --server.port=8080
  - --spring.config.location=文件位置全路径
  - 目录相同直接加载
- 多文件互补优势

## 二、SpringBoot整合其他框架

### 2.1 整合Junit

- 搭建SpringBoot工程
- 引入stater-test依赖
- 编写测试类
- 添加相关注解
  - @RunWith(SpringRunner.class)
  - @SpringBootTest(classes=启动类.class)
- 编写测试方法

#### 2.1.1 依赖

IDEA自动构建会自动添加依赖以及测试类

- 引入stater-test依赖

```xml
				<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

#### 2.1.2 编写测试类

- eg：

```java
//需要测试的方法
@Service
public class UserService {
    public void add(){
        System.out.println("add....");
    }
}
```

```java
//测试类 在test文件夹下创建树形文件
/**
 * UerService的测试类
 */
@RunWith(SpringRunner.class)
@SpringBootTest(classes = WebApplication.class) //引导类
public class UserServiceTest {
    @Autowired
    private UserService userService;

    @Test
    public void testAdd(){
        userService.add();
    }
}
```

- tips:<font color="green">若test为主目录的子包，则 不需要指定引导类</font>

### 2.2 整合Redis

 https://www.bilibili.com/video/BV1Lq4y1J77x?p=16&spm_id_from=pageDriver 

- 引入redis起步依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

- 测试

```java
@Autowired
private RedisTemplate redisTemplate;

@Test
void testSet() {
    redisTemplate.boundValueOps("name").set("zhangsan");
}

@Test
void testGet(){
     Object object = redisTemplate.boundValueOps("name").get();
    System.out.println(object);
}
```

- 配置redis相关属性

```yaml
spring:
  redis:
    host: 127.0.0.1 #ip
    port: 6379
```

- 注入RedisTemplate模版
- 编写测试方法

### 2.3 整合Mybatis

- 引入mybatis起步依赖，添加mysql驱动
- 编写DataSource和MyBatis相关配置
- 定义表和实体类
- 编写dao和mapper文件/纯注解开发
- 测试

#### 2.3.1 依赖

```xml
				<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.0</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

- 包名以spring开头的是spring官方提供的依赖
- 包名以其他开头的组建自定义的依赖

#### 2.3.2 DataSource及Mybatis配置

```yaml
#DataSource
spring:
  datasource:
    url: jdbc:mysql:///SpringBootTest01?serverTimezone=UTC
    username: root
    password: Kx123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

- 纯注解开发

```java
/**
 * @author li
 * Mybatis纯注解开发
 */
@Mapper
public interface UserMapper {
    /**
     * 查找person表中所有内容
     * @return
     */
    @Select("select * from person")
    public List<Person> findAll();
}
```

- 使用xml开发

```java
/**
 * @author li
 * Mybatis使用xml开发
 */
@Mapper
public interface UserMapper {
    /**
     * 查找person表中所有内容
     * @return
     */
    public List<Person> findAll();
}
```

```xml
<!--标准文件头-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org//dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
    
</mapper>
```

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org//dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.web.mapper.PersonXMLMapper">
    <select id="findAll" resultType="person">
        select * from person
    </select>
</mapper>
```

springboot配置文件中加入

```yam
#mybatis
mybatis:
  mapper-locations: classpath:mapper/*Mapper.xml #mapper映射文件路径
  type-aliases-package: com/example/web/User 
#check-config-location: #mybatis核心配置文件
```



#### 2.3.4 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = WebApplication.class)
public class MyBatisTest {

    @Autowired
    private PersonMapper mapper;

    @Test
    public void testFindAll(){
        List<Person> list = mapper.findAll();
        System.out.println(list);
    }
  
}
```

## 三、SpringBoot原理分析

### 3.1 SpringBoot自动配置

#### 3.1.1 Condition

- 选择性创建Bean
- 重写org.springframework.context.annotation.Condition.class方法判断是否要创建Bean
- @Conditional注解

- 自定义条件：
  - 定义条件类，重写Condition接口，重写matches方法，在matches方法中进行逻辑判断，返回boolean值。matches有两个参数：
    - context：上下文对象，可以获取属性值，获取类加载器，获取BeanFactory等
    - metadata：元数据对象，用于获取注解属性
  - 判断条件：在初始化Bean时，使用@Condital（条件类.class）注解

- SpringBoot提供常用条件注解：
  - @ConditionalOnProperties：判断配置文件中有对应的属性和值才初始化Bean
  - @ConditionalOnClass：判断环境中是否有对应的字节码文件才初始化Bean
  - @ConditionalOnMissingBean：判断环境中有没有对应Bean才初始化Bean

#### 3.1.2 切换内置web服务器

- SpringBoot默认使用tomcat作为内置服务器，其实提供了4种内置服务器
  - Jetty
  - Netty
  - Tomcat
  - Undertow
- 切换：移除tomcat包，添加所要使用的包

```xml
				<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion> 此处移除tomcat
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>	再添加jetty（例）
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>
```

#### 3.1.3 @Enable*注解

- 用于动态启用某些功能
- 底层原理是使用@Import注解导入一些配置类，实现Bean的动态加载

![image-20211006115233271](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211006115233271.png)

![image-20211006115238882](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211006115238882.png)

```java
@SpringBootApplication
@Import(UserConfig.class)
public class EnableApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(EnableApplication.class, args);

        Object user = context.getBean("user");
        System.out.println(user);
    }

}
```



- 即可直接使用EnableUser注解

#### 3.1.4 @Import注解

- 使用@Import导入的类会被Spring加载到IOC容器中
- @Import提供4种用法
  - 导入Bean
  - 导入配置类
  - 导入ImportSelector实现类。一般用于加载配置文件中的类
  - 导入ImportBeanDefinitionRegistater实现类

##### 3.1.4.1 导入Bean

- 文件结构

![截屏2021-10-06 下午12.13.19](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-06%20%E4%B8%8B%E5%8D%8812.13.19.png)

- 导入外包的依赖

```xml
<dependency>
            <groupId>com.example</groupId>
            <artifactId>enable-other</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```

- 编写dao及config

```java
public class User {
}

```

```java
@Configuration  //此处的注释可以不加
public class UserConfig {
    @Bean
    public User user(){
        return new User();
    }
}
```

- 主程序

```java
@SpringBootApplication
@Import(UserConfig.class)
public class EnableApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(EnableApplication.class, args);

        User user = context.getBean(User.class);
        System.out.println(user);
      	Map<String ,User> map = context.getBeansOfType(User.class);
        System.out.println(map);
    }

}
```

```java
//输出
com.example.dao.User@736caf7a
{com.example.dao.User=com.example.dao.User@736caf7a}
```

##### 3.1.4.2 导入ImportSelector实现类

- 重写ImportSelector,返回全类名

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com/example/dao/Role","com/example/dao/User"};
    }
}
```

- @Import（）引用该类即可

##### 3.1.4.3 导入ImportBeanDefinitionRegistrar实现类

- 重写ImportBeanDefinitionRegistrar，直接注册Bean对象

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry, BeanNameGenerator importBeanNameGenerator) {
        AbstractBeanDefinition builder = BeanDefinitionBuilder.rootBeanDefinition(User.class).getBeanDefinition();
        registry.registerBeanDefinition("user",builder);
    }
}
```

- @Import（）引用该类即可

### 3.2 自动配置原理

- 由上述实验可得：
  - springboot的自动配置原理，核心注解@SpringBootApplication中使用了@EnableAutoConfiguration
  - @EnableAutoConfiguration使用了@Import(AutoConfigurationImportSelector.class)
  - 最终使用的是第三种Import方式完成自动配置

#### 3.3 @EnableAutoConfiguration

![截屏2021-10-07 下午7.13.13](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-07%20%E4%B8%8B%E5%8D%887.13.13.png)

```
@Import(AutoConfigurationImportSelector.class)
```

![截屏2021-10-07 下午7.15.51](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-07%20%E4%B8%8B%E5%8D%887.15.51.png)

- 核心代码为getAutoConfigurationEntry方法

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
   if (!isEnabled(annotationMetadata)) {
      return EMPTY_ENTRY;
   }
   AnnotationAttributes attributes = getAttributes(annotationMetadata);
   List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
   configurations = removeDuplicates(configurations);
   Set<String> exclusions = getExclusions(annotationMetadata, attributes);
   checkExcludedClasses(configurations, exclusions);
   configurations.removeAll(exclusions);
   configurations = getConfigurationClassFilter().filter(configurations);
   fireAutoConfigurationImportEvents(configurations, exclusions);
   return new AutoConfigurationEntry(configurations, exclusions);
}
```

- 核心为getCandidateConfigurations方法

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
   List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
         getBeanClassLoader());
   Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
         + "are using a custom packaging, make sure that file is correct.");
   return configurations;
}
```

- 在META-INF/spring.factories文件中的类，将自动装配

### 3.3 SpringBoot自定义starter

- 例：自定义redis-starter
  - 要求导入redis坐标时，SpringBoot自动创建Jedis的Bean
- 如MyBatis的自动配置，starter依赖是整合pom，方便导入，核心是autoconfigure依赖

- 步骤
  - 创建autoconfigure模块
  - 创建starter模块，依赖autoconfigure模块
  - 在autoconfigure模块中初始化Jedis的Bean，并定义META-INF/spring.factories文件

- starter 删除其他无关文件，主要写入pom文件即可

```xml
<!--starter引入自定义依赖-->
<dependency>
    <groupId>com.example</groupId>
    <artifactId>redis-autoConfigure</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0-RC1</version>
</dependency>
```

- autoconfigure 

```xml
<!--引入jedis依赖-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0-RC1</version>
</dependency>
```

![截屏2021-10-07 下午7.52.23](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-07%20%E4%B8%8B%E5%8D%887.52.23.png)

```java
/**
 * @author li
 * redis自动配置类
 */

@Configuration
@EnableConfigurationProperties(RedisProperties.class)  //启用配置
public class RedisAutoConfiguration {

    @Bean
    public Jedis getJedis(RedisProperties redisProperties){
        return new Jedis(redisProperties.getHost(),redisProperties.getPort());
    }
}
```

```java
/**
 * @author li
 */
@ConfigurationProperties(prefix = "redis")
public class RedisProperties {
    private String host = "localhost";
    private int port = 6379;

    public String getHost() {
        return host;
    }

    public void setHost(String host) {
        this.host = host;
    }

    public int getPort() {
        return port;
    }

    public void setPort(int port) {
        this.port = port;
    }
}
```

spring.factories

```xml
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com/example/redisautoconfigure/RedisAutoConfiguration
```

测试方法

```java
Jedis jedis = context.getBean(Jedis.class);
System.out.println(jedis);

jedis.set("name","itcast");
System.out.println(jedis.get("name"));
```

配置文件内容也将生效

优化

```java
@Configuration
@EnableConfigurationProperties(RedisProperties.class)  //启用配置
@ConditionalOnClass(Jedis.class)  //找到类才会构建Bean
public class RedisAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean(name = "jedis")
    public Jedis getJedis(RedisProperties redisProperties){
        return new Jedis(redisProperties.getHost(),redisProperties.getPort());
    }
}
```

### 3.4 SpringBoot监听机制

- 封装了Java监听机制
  - 事件：Event，继承java.util.EventObject类的对象
  - 事件源：Source，任意对象Object
  - 监听器：Listener，实现java.util.EventListener接口
- SpringBoot项目启动时，会对几个监听器进行回调
- 我们可以实现这些监听器接口，在项目启动时完成一些操作
  - ApplicationContextInitializer
  - SpringApplicationRunListener
  - CommandLineRunner
  - ApplicationRunner

#### 3.4.1 ApplicationContextInitializer

```java
@Component
public class MyListener01 implements ApplicationContextInitializer {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        System.out.println("01初始化。。。。");
    }
}
```

默认状态不执行

在spring.factories中

```factories
org.springframework.context.ApplicationContextInitializer=\
  com.example.listener.listner.MyListener01
```

#### 3.4.2 SpringApplicationRunListener

```java
public class MyListener02 implements SpringApplicationRunListener {
    public MyListener02(SpringApplication application,String[] args) {
    }

    @Override
    public void starting(ConfigurableBootstrapContext bootstrapContext) {
        System.out.println("02初始化。。。");
    }

    @Override
    public void starting() {
        System.out.println("02初始化。。。");
    }

    @Override
    public void environmentPrepared(ConfigurableBootstrapContext bootstrapContext, ConfigurableEnvironment environment) {
        SpringApplicationRunListener.super.environmentPrepared(bootstrapContext, environment);
    }

    @Override
    public void environmentPrepared(ConfigurableEnvironment environment) {
        SpringApplicationRunListener.super.environmentPrepared(environment);
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
        SpringApplicationRunListener.super.contextPrepared(context);
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
        SpringApplicationRunListener.super.contextLoaded(context);
    }

    @Override
    public void started(ConfigurableApplicationContext context) {
        SpringApplicationRunListener.super.started(context);
    }

    @Override
    public void running(ConfigurableApplicationContext context) {
        SpringApplicationRunListener.super.running(context);
    }

    @Override
    public void failed(ConfigurableApplicationContext context, Throwable exception) {
        SpringApplicationRunListener.super.failed(context, exception);
    }
}
```

默认状态不执行

#### 3.4.3 CommandLineRunner

```java
@Component
public class MyListener03 implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("03初始化");
    }
}
```

默认状态执行

在spring.factories中

```factories
org.springframework.boot.SpringApplicationRunListener=\
  com.example.listener.listner.MyListener02
```

#### 3.4.4 ApplicationRunner

```java
@Component
public class MyListener04 implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("04启动中");
    }
}
```

默认状态执行

3，4传入对象类型不同，效果相同

 

### 3.5 SpringBoot启动流程分析

- 首先判断主类
- 判断是否为web环境
- 初始化类



