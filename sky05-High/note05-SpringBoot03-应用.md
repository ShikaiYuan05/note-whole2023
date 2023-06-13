# 一、SpringBoot整合SpringMVC
## 1、Ajax异步交互
下面两个注解的用法和在SpringMVC中完全一样：
- @ResponseBody
- @RequestBody

## 2、服务器端渲染页面
### ①引入依赖
```xml
<dependencies>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-thymeleaf</artifactId>  
    </dependency>  
</dependencies>
```

### ②视图前缀与后缀
通过查看org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties这个类我们能看到默认设置：<br/>

![img.png](images/img226.png)

<br/>

所以在resources目录下创建templates目录，视图模板文件就放在这里：<br/>

![img.png](images/img227.png)

<br/>

```html
<!DOCTYPE html>  
<html lang="en" xmlns:th="http://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>Title</title>  
</head>  
<body>  
  
    <h1>首页</h1>  
  
</body>  
</html>
```

<br/>

> 如果修改视图前后缀，那么就配置项就参考ThymeleafProperties这个类。但是相信我，你肯定不会修改的。

### ③Controller方法
```java
@Controller  
public class MVCController {  
  
    @RequestMapping("/index.html")  
    public String toIndexPage() {  
        System.out.println("hello");  
        return "portal";  
    }  
  
}
```

## 3、静态资源
### ①寻找存放位置
参考org.springframework.boot.autoconfigure.web.ResourceProperties这个类：<br/>

![img.png](images/img228.png)

<br/>

我们看到，以下四个目录均可用于存放静态资源：
- classpath:/META-INF/resources/
- classpath:/resources/
- classpath:/static/
- classpath:/public/

<br/>

通常我们会选择使用classpath:/static/目录。当然另一个办法是参考ResourceProperties这个类修改默认配置。

### ②创建目录
![img.png](images/img229.png)

### ③引用静态资源
```html
<img th:src="@{mi.jpg}">
```

## 4、拦截器
### ①创建拦截器
```java
@Component  
public class MyInterceptor extends HandlerInterceptorAdapter {  
  
    @Override  
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {  
  
        System.out.println("MyInterceptor ...");  
  
        return true;  
    }  
}
```

### ②注册
```java
@Configuration  
public class MyConfig implements WebMvcConfigurer {  
  
    @Autowired  
    private MyInterceptor myInterceptor;  
  
    @Override  
    public void addInterceptors(InterceptorRegistry registry) {  
        registry.addInterceptor(myInterceptor);  
    }  
}
```

### ③注意
拦截器和配置类要放在自动扫描包的范围内。

## 5、view-controller
```java
@Override  
public void addViewControllers(ViewControllerRegistry registry) {  
    registry.addViewController("/to/another/page").setViewName("target");  
}
```

# 二、SpringBoot整合junit
## 1、目的
和我们在Spring中整合junit一样，整合的目的是为了在单元测试类中装配IOC容器中的组件。

## 2、引入依赖
```xml
<dependencies>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
        <scope>test</scope>  
    </dependency>  
</dependencies>
```

## 3、编写单元测试
```java
@SpringBootTest  
public class MyTest {  
  
    @Autowired  
    private MyService myService;  
  
    @Test  
    public void testGetData() {  
        String data = myService.getData();  
        System.out.println("data = " + data);  
    }  
  
}
```

## 4、注意
- 单元测试类也要放在扫描包的范围内
- @Test注解要使用org.junit.jupiter.api.Test

# 三、SpringBoot整合Mybatis
## 1、建模
### ①物理建模
```sql
create table t_emp(  
    emp_id int auto_increment primary key ,  
    emp_name char(100),  
    emp_salary double  
);  
  
insert into t_emp(emp_name, emp_salary) VALUES ("tom", 1000.00);  
insert into t_emp(emp_name, emp_salary) VALUES ("jerry", 2000.00);  
insert into t_emp(emp_name, emp_salary) VALUES ("harry", 3000.00);
```

### ②逻辑建模
```java
public class Emp {  
  
    private Integer empId;  
    private String empName;  
    private Double empSalary;
```

## 2、引入依赖
```xml
<dependencies>  
    <dependency>  
        <groupId>org.mybatis.spring.boot</groupId>  
        <artifactId>mybatis-spring-boot-starter</artifactId>  
        <version>2.2.2</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
        <scope>test</scope>  
    </dependency>  
	<dependency>  
	    <groupId>mysql</groupId>  
	    <artifactId>mysql-connector-java</artifactId>  
	</dependency>
    <dependency>  
        <groupId>com.alibaba</groupId>  
        <artifactId>druid</artifactId>  
        <version>1.2.8</version>  
    </dependency>  
</dependencies>
```

## 3、Mapper
### ①接口
```java
@Mapper  
public interface EmpMapper {  
  
    List<Emp> selectAll();  
  
}
```

### ②配置文件
![img.png](images/img230.png)

<br/>

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE mapper  
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">  
<mapper namespace="com.atguigu.boot.mapper.EmpMapper">  
  
    <select id="selectAll" resultType="com.atguigu.boot.entity.Emp">  
        select emp_id empId,emp_name empName,emp_salary empSalary from t_emp    </select>  
  
</mapper>
```

## 4、YAML配置
```yaml
spring:  
  datasource:  
    username: root  
    password: atguigu  
    url: jdbc:mysql://localhost:3306/db_hr?serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver  
    type: com.alibaba.druid.pool.DruidDataSource  
mybatis:  
  mapper-locations: classpath:/mapper/*.xml
logging:  
  level:  
    com.atguigu.boot.mapper: debug
```

## 5、单元测试
```java
@SpringBootTest  
public class MybatisTest {  
  
    @Autowired  
    private DataSource dataSource;  
  
    @Autowired  
    private EmpMapper empMapper;  
  
    @Test  
    public void testEmpMapperSelect() {  
        List<Emp> empList = empMapper.selectAll();  
        for (Emp emp : empList) {  
            System.out.println("emp = " + emp);  
        }  
    }  
  
    @Test  
    public void testConn() throws SQLException {  
        Connection connection = dataSource.getConnection();  
        System.out.println("connection = " + connection);  
    }  
  
}
```

## 6、Mapper扫描
- 方式一：在Mapper接口上加@Mapper注解
- 方式二：在主启动类上加@MapperScan注解
```java
@SpringBootApplication  
@MapperScan("com.atguigu.boot.mapper")  
public class MybatisMainType {  
  
    public static void main(String[] args) {  
        SpringApplication.run(MybatisMainType.class, args);  
    }  
  
}
```

## 7、其它配置
其它可用配置参考org.mybatis.spring.boot.autoconfigure.MybatisProperties类


# 四、SpringBoot整合Spring Data JPA
## 1、JPA简介

## 2、引入依赖
```xml
<dependencies>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-data-jpa</artifactId>  
    </dependency>  
    <dependency>  
        <groupId>mysql</groupId>  
        <artifactId>mysql-connector-java</artifactId>  
    </dependency>  
    <dependency>  
        <groupId>com.alibaba</groupId>  
        <artifactId>druid</artifactId>  
        <version>1.2.8</version>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
        <scope>test</scope>  
    </dependency>  
</dependencies>
```

## 3、创建实体类
```java
@Entity // 声明实体类  
@Table(name="t_emp") // 指定实体类对应的数据库表  
public class Emp implements Serializable {  
  
    @Id // 声明主键  
    @GeneratedValue(strategy = GenerationType.IDENTITY) // 声明主键值通过自增方式生成  
    @Column(name = "emp_id") // 指定对应字段名称  
    private Integer empId;  
  
    @Column(name = "emp_name")  
    private String empName;  
  
    @Column(name = "emp_salary")  
    private Double empSalary;
```

## 4、创建Dao接口
```java
public interface EmpDao extends JpaRepository<Emp, Integer> {  
}
```

## 5、主启动类
```java
@SpringBootApplication  
public class JPAMainType {  
  
    public static void main(String[] args) {  
        SpringApplication.run(JPAMainType.class, args);  
    }  
  
}
```

## 6、YAML配置
```yaml
spring:  
  datasource:  
    username: root  
    password: atguigu  
    url: jdbc:mysql://localhost:3306/db_hr?serverTimezone=Asia/Shanghai  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    type: com.alibaba.druid.pool.DruidDataSource  
  jpa:  
    database: mysql  
    show-sql: true  
    generate-ddl: true  
    hibernate:  
      ddl-auto: update  
      naming_strategy: org.hibernate.cfg.ImprovedNamingStrategy  
logging:  
  level:  
    com.atguigu.boot.dao: debug
```

## 7、测试
```java
@SpringBootTest  
public class JPATest {  
  
    @Autowired  
    private DataSource dataSource;  
  
    @Autowired  
    private EmpDao empDao;  
  
    @Test  
    public void testConn() throws SQLException {  
        Connection connection = dataSource.getConnection();  
        System.out.println("connection = " + connection);  
    }  
  
    @Test  
    public void testEmpDao() {  
        List<Emp> empList = empDao.findAll();  
        for (Emp emp : empList) {  
            System.out.println("emp = " + emp);  
        }  
    }  
  
}
```

# 五、SpringBoot整合Redis
## 1、引入依赖
```xml
<dependencies>  
    <!-- Redis -->  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-data-redis</artifactId>  
    </dependency>  
  
    <!-- Spring2.X 集成 Redis 所需 commons-pool2 -->    <dependency>  
        <groupId>org.apache.commons</groupId>  
        <artifactId>commons-pool2</artifactId>  
        <version>2.6.0</version>  
    </dependency>  
  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
        <scope>test</scope>  
    </dependency>  
</dependencies>
```

## 2、YAML配置
```yaml
spring:  
  redis:  
    host: 192.168.200.128  
    port: 6379
```

## 3、测试
```java
@SpringBootTest  
public class RedisTest {  
  
    @Autowired  
    private RedisTemplate redisTemplate;  
  
    @Autowired  
    private StringRedisTemplate stringRedisTemplate;  
  
    @Test  
    public void testRedisGet() {  
        ValueOperations operator = redisTemplate.opsForValue();  
        operator.set("hello", "tom");  
        Object hello = operator.get("hello");  
        System.out.println("hello = " + hello);  
    }  
  
    @Test  
    public void testStringRedisTemplate() {  
        ValueOperations<String, String> operator = stringRedisTemplate.opsForValue();  
        operator.set("myKey", "myValue");  
        String myKey = operator.get("myKey");  
        System.out.println("myKey = " + myKey);  
    }  
  
}
```

# 六、SpringBoot整合RabbitMQ
## 1、生产者工程
### ①引入依赖
```xml
<dependencies>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-amqp</artifactId>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
    </dependency>  
</dependencies>
```

### ②YAML配置
```yaml
spring:  
  rabbitmq:  
    host: 192.168.200.128  
    port: 5672  
    username: guest  
    password: guest  
    publisher-confirms: true  
    publisher-returns: true  
    listener:  
      simple:  
        acknowledge-mode: manual  
        prefetch: 1
```

### ③主启动类
```java
@SpringBootApplication  
public class RabbitMainType {  
  
    public static void main(String[] args) {  
        SpringApplication.run(RabbitMainType.class, args);  
    }  
  
}
```

### ④测试
```java
@SpringBootTest  public class RabbitMQTest {    
    
    public static final String EXCHANGE_DIRECT = "exchange.direct.order";    
    public static final String ROUTING_KEY = "order";  
    
    @Autowired    
private RabbitTemplate rabbitTemplate;  
    
    @Test    
public void testSendMessage() {    
        rabbitTemplate.convertAndSend(    
                EXCHANGE_DIRECT,     
                ROUTING_KEY,     
                "Hello atguigu");    
    }    
    
}
```

## 2、消费者工程
### ①引入依赖
比生产者工程多一个：
```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
</dependency>
```

### ②一样
- 主启动类和生产者工程一样
- YAML配置和生产者工程一样

### ③监听器
```java
package com.atguigu.boot.listener;  
    
import com.rabbitmq.client.Channel;    
import org.springframework.amqp.core.Message;    
import org.springframework.amqp.rabbit.annotation.Exchange;    
import org.springframework.amqp.rabbit.annotation.Queue;    
import org.springframework.amqp.rabbit.annotation.QueueBinding;    
import org.springframework.amqp.rabbit.annotation.RabbitListener;    
import org.springframework.stereotype.Component;    
    
@Component  public class MyMessageListener {    
    
    public static final String EXCHANGE_DIRECT = "exchange.direct.order";    
    public static final String ROUTING_KEY = "order";    
    public static final String QUEUE_NAME  = "queue.order";    
    
    @RabbitListener(bindings = @QueueBinding(    
            value = @Queue(value = QUEUE_NAME, durable = "true"),    
            exchange = @Exchange(value = EXCHANGE_DIRECT),    
            key = {ROUTING_KEY}    
    ))    
    public void processMessage(String dateString,    
                               Message message,    
                               Channel channel) {    
        System.out.println(dateString);    
    }    
    
}
```


# 七、综合案例

# 八、SpringBoot应用部署