
> 名字：封捷

<br/>

![img.png](images/img231.png)

<br/>

![img.png](images/img232.png)

<br/>

# 一、SpringBoot要解决的问题
SpringFramework 配置文件太过冗长，大型项目开发仍然不够简洁高效。所以SpringBoot相当于Spring Framework的进一步封装。

# 二、SpringBoot介绍
## 1、优势
- Create stand-alone Spring applications
- Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
- Provide opinionated 'starter' dependencies to simplify your build configuration
- Automatically configure Spring and 3rd party libraries whenever possible
- Provide production-ready features such as metrics, health checks, and externalized configuration
- Absolutely no code generation and no requirement for XML configuration

<br/>

翻译如下：
- 创建独立的Spring应用程序
- 直接内置Tomcat、Jetty、Undertow，所以不必为了部署应用而导出war文件
- 通过场景启动器封装了Spring Boot团队基于最佳实践的设计决策，从而简化构建配置
- 尽可能自动配置Spring和第三方库
- 提供可用于生产的功能，如度量、运行状况检查和外部化配置
- 告别了生成代码和XML配置

# 三、SpringBoot HelloWorld
## 1、一个SpringBoot应用的基本构成
![img.png](images/img222.png)

## 2、HelloWorld操作
### ①目标
浏览器访问Controller方法，在页面上看到Controller方法返回的响应。

### ②配置父工程POM
```xml
<!-- 继承 SpringBoot 父工程 -->
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>2.3.6.RELEASE</version>  
</parent>
```

### ③模块工程引入Web场景启动器
```xml
<dependencies>  
	<!-- 封装 Web 开发功能的场景启动器 -->
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>  
</dependencies>
```

### ④模块工程创建主启动类
```java
// 把当前类标记为 SpringBoot 的主启动类  
@SpringBootApplication  
public class HelloWorldMainType {  
  
    // 当前 SpringBoot 应用程序入口  
    public static void main(String[] args) {  
        // 调用 run() 方法进入 SpringBoot 启动流程  
        // 传入参数1：当前类的 Class 对象  
        // 传入参数2：main() 方法的参数  
        SpringApplication.run(HelloWorldMainType.class, args);  
    }  
  
}
```

### ⑤模块工程创建Controller类
```java
@RestController  
public class SampleController {
  
    @GetMapping("/sample")  
    public String sample() {  
        return "sample data";  
    }  
  
}
```

## 3、HelloWorld解析
### ①为什么没有contextPath？

### ②为什么没有配置自动扫描的包？

### ③如何自定义扫描包的范围？

### ④为什么Web starter不需要写版本号？

### ⑤为什么只需要导入Web starter一个依赖？