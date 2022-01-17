### 特点

1. 约定优于配置
2. 控制反转 IoC
   1. 管理各类Java资源，降低资源的耦合
   2. 极低的侵入性
3. 依赖注入
4. 面向切面的变成 AOP
   1. 动态代理技术，按照约定进行配置变成，增强Bean
   2. 擦除重复代码，如数据库编程所需大量的try...catch...finally
5. 整合持久层HibernateTemplate模板、ibatis的SQLMapClientTemplate模板

### 优点

1. 创建独立的Spring应用程序
2. 嵌入的Tomcat、Jetty、Undertow，不须部署war文件
3. 允许通过Maven/Gradle来根据需要获取starter
4. 自动配置Spring
5. 提供生产就绪型功能，如指标、健康检查和外部配置
6. 绝对没有代码生成，对xml没有要求配置

### 配置

#### 自动配置

DispatcherServletAutoConfiguration 读取默认配置

#### 自定义配置

优先级

1. 命令行
2. java:comp/env的JNDI属性
3. Java系统属性（System.getProperties()）
4. 操作系统环境变量
5. RandomValuePropertySource配置的random.*属性值
6. jar 包外部的application-{profile}.properties或application.yml（带spring.profile）配置文件
7. jar 包内部的application-{profile}.properties或application.yml（带spring.profile）配置文件
8. jar 包外部的application.properties或application.yml（不带spring.profile）配置文件
9. jar 包内部的application.properties或application.yml（不带spring.profile）配置文件
10. @Configuration注解类上的@PropertySource
11. 通过SpringApplication.setDefaultProperties制定的默认属性

### 程序入口

注解`@SpringBootApplication`

``` java
@SpringBootApplication
public class MyApplication {
  public static void main(String[] args) {
    SpringApplication.run(MyApplication.class, args);
  }
}
```



