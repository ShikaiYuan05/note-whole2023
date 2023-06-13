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
# 四、SpringBoot整合Spring Data JPA
# 五、SpringBoot整合Redis
# 六、SpringBoot整合RabbitMQ
# 七、综合案例
# 八、SpringBoot应用部署