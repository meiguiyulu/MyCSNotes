# SpringBoot

## 0、SpringBoot与Spring的区别

### 0.1、介绍

**`Spring`**

`Spring`是一个开源的应用框架，是针对bean的生命周期进行管理的轻量级容器。

`Spring`框架为开发Java应用程序提供了全面的基础架构支持，它包含一些很好的功能，如依赖注入和开箱即用的模块，如：

- Spring JDBC
- Spring MVC
- Spring Security
- Spring AOP
- Spring ORM
- Spring Test

这些模块可以大大缩短应用程序的开发时间。例如，在Java Web开发的早期阶段，我们需要编写大量的重复代码来将记录插入到数据源中。但是通过使用Spring JDBC模块的JDBCTemplate，我们可以将它简化为只需几个简单配置或者几行代码。

**`SpringBoot`**

`Spring Boot` 基本上是 `Spring` 框架的扩展，它消除了设置 `Spring` 应用程序所需的复杂例行配置。它的目标和`Spring` 的目标是一致的，为更快，更高效的开发生态系统铺平了道路。

### 0.2、比较

#### 1、Maven依赖的方式不同

- `spring`

  - ```java
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.1.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.0.RELEASE</version>
    </dependency>
    ```

- `springboot`

  - ```java
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>2.0.5.RELEASE</version>
    </dependency>
    ```

- `Spring Boot` 为不同的` Spring` 模块提供了许多入门依赖项。有很多的 `starter`

  - ```markdown
    1. spring-boot-starter-data-jpa
    2. spring-boot-starter-security
    3. spring-boot-starter-test
    4. spring-boot-starter-web
    5. spring-boot-starter-thymelea
    ```

#### 2、MVC配置的方式不同

- `spring`

  - ```java
    public class MyWebAppInitializer implements WebApplicationInitializer {
    
        @Override
        public void onStartup(ServletContext container) {
            AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
            context.setConfigLocation("com.test.package");
    
            container.addListener(new ContextLoaderListener(context));
    
            ServletRegistration.Dynamic dispatcher = container.addServlet("dispatcher", new DispatcherServlet(context));
    
            dispatcher.setLoadOnStartup(1);
            dispatcher.addMapping("/");
        }
    }
    
    ```

  - 我们还需要将@EnableWebMvc注解添加到@Configuration注解类，并定义一个视图解析器来解析从控制器返回的视图：

  - ```java
    @EnableWebMvc
    @Configuration
    public class ClientWebConfig implements WebMvcConfigurer {
       @Bean
       public ViewResolver viewResolver() {
          InternalResourceViewResolver bean = new InternalResourceViewResolver();
              bean.setViewClass(JstlView.class);
              bean.setPrefix("/WEB-INF/view/");
              bean.setSuffix(".jsp");
          return bean;
       }
    }
    
    ```

- `springboot`

  - ```java
    spring.mvc.view.prefix=/WEB-INF/jsp/
    spring.mvc.view.suffix=.jsp
    
    ```

- ```markdown
  **
  上面的所有Spring配置都是通过一个名为auto-configuration的进程添加Boot web starter来自动包含的。
  这意味着Spring Boot将自动扫描应用程序中存在的依赖项，属性和bean，并根据这些内容启用相应的配置。
  **
  ```

#### 3、模板引擎配置不同

- `spring`

  - 在Spring中，我们需要为视图解析器添加 thymeleaf-spring5依赖项和一些配置

  - ```java
    @Configuration
    @EnableWebMvc
    public class MvcWebConfig implements WebMvcConfigurer {
    
        @Autowired
        private ApplicationContext applicationContext;
    
        @Bean
        public SpringResourceTemplateResolver templateResolver() {
            SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
                templateResolver.setApplicationContext(applicationContext);
                templateResolver.setPrefix("/WEB-INF/views/");
                templateResolver.setSuffix(".html");
            return templateResolver;
        }
    
        @Bean
        public SpringTemplateEngine templateEngine() {
            SpringTemplateEngine templateEngine = new SpringTemplateEngine();
                templateEngine.setTemplateResolver(templateResolver());
                templateEngine.setEnableSpringELCompiler(true);
            return templateEngine;
        }
    
        @Override
        public void configureViewResolvers(ViewResolverRegistry registry) {
            ThymeleafViewResolver resolver = new ThymeleafViewResolver();
                resolver.setTemplateEngine(templateEngine());
            registry.viewResolver(resolver);
        }
    }
    
    ```

- `SpringBoot`

  - `Spring Boot` 只需要 `spring-boot-starter-thymeleaf` 的依赖项 来启用Web应用程序中的Thymeleaf支持。一旦依赖关系添加成功后，我们就可以将模板添加到 `src/main/resources/templates` 文件夹中，`Spring Boot`将自动显示它们。

#### 4、安全配置的方式不同

- `Spring`

  - Spring需要标准的 spring-security-web和spring-security-config 依赖项来在应用程序中设置Security。

  - 接下来， 我们需要添加一个扩展WebSecurityConfigurerAdapter的类，并使用@EnableWebSecurity注解：

  - ```java
    @Configuration
    @EnableWebSecurity
    public class CustomWebSecurityConfigurerAdapter extends WebSecurityConfigurerAdapter {
    
        @Autowired
        public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
            auth.inMemoryAuthentication()
                .withUser("user1")
                .password(passwordEncoder().encode("user1Pass"))
                .authorities("ROLE_USER");
        }
    
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.authorizeRequests().anyRequest().authenticated().and().httpBasic();
        }
    
    
        @Bean
        public PasswordEncoder passwordEncoder() {
            return new BCryptPasswordEncoder();
        }
    }
    
    ```

- `Spring Boot`

  - 也需要这些依赖项才能使其工作。但是我们只需要定义`spring-boot-starter-security`的依赖关系，它会自动将所有相关的依赖项添加到类路径中。

#### 5、应用引导Application Bootstrap方式不同

Spring和Spring Boot中应用程序引导的基本区别在于servlet。

Spring使用web.xml 或SpringServletContainerInitializer 作为其引导入口点。
spring boot仅仅使用Servlet 3来引导程序。

**Spring引导**

- 方法一：web.xml引导方法

  - ```markdown
    1. Servlet容器（服务器）读取web.xml
    2. web.xml中定义的DispatcherServlet由容器实例化
    3. DispatcherServlet通过读取WEB-INF / {servletName} -servlet.xml来创建WebApplicationContext
    4. DispatcherServlet注册在应用程序上下文中定义的bean
    ```

- 方法二：servlet 3+引导方法

  - ```markdown
    1. 容器搜索实现ServletContainerInitializer的 类并执行
    2. SpringServletContainerInitializer找到实现类WebApplicationInitializer的子类
    3. WebApplicationInitializer创建会话使用XML或上下文@Configuration类
    4. WebApplicationInitializer创建DispatcherServlet，使用先前创建的上下文。
    ```



**Spring Boot引导**

Spring Boot应用程序的入口点是使用`@SpringBootApplication`注释的类：

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

```

#### 6、打包和部署的方式不同

最后，让我们看看如何打包和部署应用程序。这两个框架都支持Maven和Gradle等常见的包管理技术。但是在部署方面，这些框架差异很大。

   例如，Spring Boot Maven插件在Maven中提供Spring Boot支持。它还允许打包可执行jar或war档案并“就地”运行应用程序。

   与spring相比，在部署环境中Spring Boot的一些优点包括

- 提供嵌入式容器支持
- 使用命令java -jar独立运行jar
- 在外部容器中部署时，可以选择排除依赖关系以避免潜在的jar冲突
- 部署时灵活指定配置文件的选项
- 用于集成测试的随机端口生成

## 1、自动配置原理

- 首先看一下主启动类

  - ```java
    @SpringBootApplication()
    public class CommunityApplication {
        public static void main(String[] args) {
            SpringApplication.run(CommunityApplication.class, args);
        }
    }
    ```
    
  - 有一个注解叫做 `@SpringBootApplication()`

- 点开 `@SpringBootApplication()`这个注解

  - ```java
    @Target(ElementType.TYPE) // 表示注解作用范围，超过这个作用范围，编译的时候就会报错
    @Retention(RetentionPolicy.RUNTIME) // 描述注解的生命周期：source、class、runtime
    @Documented //表明这个注释是由 javadoc记录的
    @Inherited // 子类要继承父类的注解需要该注解被 @Inherited 标识。
    @SpringBootConfiguration // 标记当前类为配置类
    @EnableAutoConfiguration // 开启自动配置
    @ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class), // 扫描主类所在的同级包以及下级包里的Bean
    		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
    public @interface SpringBootApplication {
    }
    ```
    
  - 有一个注解叫做 `@EnableAutoConfiguration()`

- 点开 `@EnableAutoConfiguration()`这个注解

  - ```java
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @AutoConfigurationPackage
    @Import(AutoConfigurationImportSelector.class)
    public @interface EnableAutoConfiguration {
    
    	/**
    	 * Environment property that can be used to override when auto-configuration is
    	 * enabled.
    	 */
    	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
    
    	/**
    	 * Exclude specific auto-configuration classes such that they will never be applied.
    	 * @return the classes to exclude
    	 */
    	Class<?>[] exclude() default {};
    
    	/**
    	 * Exclude specific auto-configuration class names such that they will never be
    	 * applied.
    	 * @return the class names to exclude
    	 * @since 1.3.0
    	 */
    	String[] excludeName() default {};
    }
    ```

  - 首先有一个注解 `@AutoConfigurationPackage`, 点开有一个`@Import(AutoConfigurationPackages.Registrar.class)`，作用是把扫描到的路径注册到全局变量中，提供查询。

  - 其次有一个 `@Import(EnableAutoConfigurationImportSelector.class)`，借助`EnableAutoConfigurationImportSelector`，`@EnableAutoConfiguration` 可以帮助 SpringBoot 应用将所有符合条件的 `@Configuration` 配置都加载到当前 SpringBoot 创建并使用的IoC容器：通过`@Import(AutoConfigurationImportSelector.class)` 导入的配置功能。`AutoConfigurationImportSelector` 中的方法 `getCandidateConfigurations`，得到待配置的 class 的类名集合, 这个集合就是所有需要进行自动配置的类，而是是否配置的关键在于 `META-INF/spring.factories` 文件中是否存在该配置信息。
  
  - ```java
    	protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
      		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
      				getBeanClassLoader());
      		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
      				+ "are using a custom packaging, make sure that file is correct.");
      		return configurations;
      	}
    ```



## 2、Springboot怎么实现异常处理

SpringBoot中有一个`ControllerAdvice`的注解，使用该注解**表示开启了全局异常的捕获**，我们只需再自定义一个方法使用`ExceptionHandler`注解，然后定义捕获异常的类型即可对这些捕获的异常进行统一的处理。

比如：

```java
@ControllerAdvice
public class MyExceptionHandler {

    @ExceptionHandler(value =Exception.class)
	public String exceptionHandler(Exception e){
		System.out.println("未知异常！原因是:"+e);
       	return e.getMessage();
    }
}
```

上述的示例中，我们对捕获的异常进行简单的二次处理，返回异常的信息。



## 3、Spring Security与Shiro

**相同点：**

1、认证功能; 2、授权功能; 3、加密功能; 4、会话管理; 5、缓存支持; 6、rememberMe功能

**不同点：**

1、Spring Security 基于Spring 开发，项目若使用 Spring 作为基础，配合 Spring Security 做权限更加方便，而 Shiro 需要和 Spring 进行整合开发；

2、Spring Security 功能比 Shiro 更加丰富些，例如安全维护方面；

3、Spring Security 社区资源相对比 Shiro 更加丰富；

Spring Security对Oauth、OpenID也有支持,Shiro则需要自己手动实现。而且Spring Security的权限细粒度更高

spring security 接口 RequestMatcher 用于匹配路径,对路径做特殊的请求，类似于shiro的

抽象类 PathMatchingFilter，但是 RequestMatcher 作用粒度更细

4、Shiro 的配置和使用比较简单，Spring Security 上手复杂些；

5、Shiro 依赖性低，不需要任何框架和容器，可以独立运行.Spring Security 依赖Spring容器；

6、shiro 不仅仅可以使用在web中，还支持非web项目它可以工作在任何应用环境中。在集群会话时Shiro最重要的一个好处或许就是它的会话是独立于容器的。

apache shiro的话，简单，易用，功能也强大，spring官网就是用的shiro，可见shiro的强大

## 4、如何理解Spring Boot中的Starter

​	如果使用Spring + SpringMVC的话，要使用 MyBatis 等框架，就需要在xml文件中定义MyBatis需要的Bean。但是如果使用Spring Boot的话，开发人员只需要将相应的 starter 包依赖进应用，进行相应的熟悉配置，就可以直接进行开发了，比如 `mybatis-spring-boot-starter, spring-boot-starter-redis`。

​	比如说MyBatis的starter，就是定义一个 starter 的 jar 包，在里面写一个 `@Configuration` 配置类，将需要的bean定义在这个配置类里面，然后在starter包的 `META-IBF/spring.factories` 中写入该配置类，spring boot就会按照约定来加载这些配置类。

## 5、SpringBoot的常用注解

- **`@SpringBootApplication` 注解**：**包含了`@SpringBootConfiguration`，`@EnableAutoConfiguration`，`@ComponentScan`这三个注解。**
  - `@SpringBootConfiguration`: 标注当前类是配置类，这个注解继承自 `@Configuration`。并会将当前类内声明的一个或多个以 `@Bean` 注解标记的方法的实例纳入到spring容器中，并且实例名就是方法名。
  - `@EnableAutoConfiguration` : 是自动配置的注解，这个注解会根据我们添加的组件jar来完成一些默认配置，我们会添加spring-boot-starter-web这个组件jar的pom依赖，这样配置会默认配置springmvc 和tomcat。
  - `@ComponentScan` : 标识扫描路径，因为默认是没有配置实际扫描路径，所以SpringBoot扫描的路径是启动类所在的当前⽬录。
  
- **`@Controller`**：表明这个类是一个**控制器类**，和**`@RequestMapping`**来配合使用拦截请求，如果不在method中注明请求的方式，默认是拦截get和post请求。这样请求会完成后转向一个视图解析器。但是在大多微服务搭建的时候，前后端会做分离。所以请求后端只关注数据处理，后端返回 `json` 数据的话，需要配合 `@ResponseBody` 注解来完成。
  - `@RestController` 是 `@Controller` 和 `@ResponseBody` 的结合，一个类被加上 `@RestController` 注解，数据接口中就不再需要添加 `@ResponseBody`。
  - **`@Autowired`**: 这是个最熟悉的注解，是spring的自动装配，这个注解可以用到构造器，变量域，方法，注解类型上。当我们需要从 bean 工厂中获取一个bean时，Spring会自动为我们装配该bean中标记为 `@Autowired` 的元素。
  - `@PathVariable`：路径变量注解。
  - `@RequestMapping`: 中用`{}`来定义url部分的变量名
  
- **`@Service`：**这个注解用来标记**业务层**的组件，我们会将**业务逻辑处理的类**都会加上这个注解交给spring容器。**事务的切面**也会配置在这一层。
  - `@Resource`：``@Resource` 和 `@Autowired` 一样都可以用来装配bean，都可以标注字段上，或者方法上。 `@Resource` 注解不是spring提供的，是属于J2EE规范的注解。
  - `@Resource` 默认按照名称方式进行bean匹配
  - `@Autowired` 默认按照类型方式进行bean匹配。
  
- `@Repository`：``@Repository`注解类作为DAO对象，管理操作数据库的对象。
  - `@Component`, `@Service`, `@Controller`, `@Repository`是spring注解，注解后可以被spring框架所扫描并注入到spring容器来进行管理。
  - `@Component`是通用注解，其他三个注解是这个注解的拓展，并且具有了特定的功能。
  
- **`@Transactional`： 通过这个注解可以声明事务，可以添加在类上或者方法上。**

- `@RequestMapping`

  - `@RequestMapping` 用来映射请求，也就是通过它来指定控制器可以处理哪些URL请求，相当于Servlet中在web.xml中配置的映射作用一致。

  - ```xml
    <servlet>
        <servlet-name>servletName</servlet-name>
        <servlet-class>ServletClass</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>servletName</servlet-name>
        <url-pattern>url</url-pattern>
    </servlet-mapping>
    ```

  - `RequestMapping`注解类的源码：

    - ```java
      @Target({ElementType.METHOD, ElementType.TYPE})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      @Mapping
      public @interface RequestMapping {
      	String name() default "";
      	String[] value() default {};
      	String[] path() default {};
      	RequestMethod[] method() default {};
      	String[] params() default {};
      	String[] headers() default {};
      	String[] consumes() default {};
      	String[] produces() default {};
      }
      ```

    - 在 `@Target` 中有两个属性，分别为 `ElementType.METHOD` 和 `ElementType.TYPE`, 也就是说 `@RequestMapping` 可以在**方法和类**的声明中使用。

    - 可以看到注解中的属性除了 `name()` 返回的字符串，其它的方法均返回数组，也就是可以定义多个属性值，例如 `value()` 和 `path()` 都可以同时定义多个字符串值来接收多个URL请求。

    - `@RequestMapping` 中的 `method` 主要用来定义接收浏览器发来的何种请求。如：`GET`, `POST`等。

    - `@RequestMapping` 的 `params` 属性，该属性表示请求参数，也就是追加在URL上的键值对，多个请求参数以&隔开, 例如：

      - ```java
        http://localhost/SpringMVC/user/login?username=kolbe&password=123456
        
        @Controller
        @RequestMapping(path = "/user")
        public class UserController {
                // 该方法将接收 /user/login 发来的请求，且请求参数必须为 username=kolbe&password=123456
        	@RequestMapping(path = "/login", params={"username=kolbe","password=123456"})
        	public String login() {
        		return "success";
        	}
        
        ```

  - 如果有两个 `RequestMapping` 的url是一样的, **后端编译无法通过，前端会报500错误。**

## 6、SpringBoot Bean

### 6.1、Spring Boot中 Bean的加载过程

https://blog.csdn.net/caoyuanyenang/article/details/110505166

- 根据 `package` 扫描出需要被管理的类
- 将这些类封装成 `BeanDefinition `并注册到 `BeanFactory `容器
- 实例化所有扫描到的 `BeanDefinition`，其中包括解决循环依赖、延迟加载问题

<img src="https://img-blog.csdnimg.cn/20210304135252666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nhb3l1YW55ZW5hbmc=,size_16,color_FFFFFF,t_70">

## 7、WebMvcConfigurer

### 7.1、简介

`WebMvcConfigurer` 配置类其实是`Spring`内部的一种配置方式，采用`JavaBean`的形式来代替传统的`xml`配置文件形式进行针对框架个性化定制，可以自定义一些Handler，Interceptor，ViewResolver，MessageConverter。基于java-based方式的spring mvc配置，需要创建一个**配置类并实现`WebMvcConfigurer`接口**；

### 7.2、常用方法

```java
public interface WebMvcConfigurer {
    /* 拦截器配置 */
    void addInterceptors(InterceptorRegistry var1);
    /* 视图跳转控制器 */
    void addViewControllers(ViewControllerRegistry registry);
    /* 静态资源处理 */
    void addResourceHandlers(ResourceHandlerRegistry registry);
    /* 默认静态资源处理器 */
    void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer);
    /* 这里配置视图解析器 */
    void configureViewResolvers(ViewResolverRegistry registry);
    /* 配置内容裁决的一些选项*/
    void configureContentNegotiation(ContentNegotiationConfigurer configurer);
    /* 解决跨域问题 */
    public void addCorsMappings(CorsRegistry registry);
}
```

#### 7.2.1 addInterceptors：拦截器

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    super.addInterceptors(registry);
    registry.addInterceptor(new TestInterceptor())
            .addPathPatterns("/**")   		.excludePathPatterns("/emp/toLogin","/emp/login","/js/**","/css/**","/images/**");
}
```

- `addInterceptor`：需要一个实现 `HandlerInterceptor` 接口的拦截器实例
- `addPathPatterns`：用于设置拦截器的过滤路径规则；`addPathPatterns("/**")`对所有请求都拦截
- `excludePathPatterns`：用于设置不需要拦截的过滤规则
- 拦截器主要用途：进行用户登录状态的拦截，日志的拦截等。

#### 7.2.2、addViewControllers：页面跳转

以前写SpringMVC的时候，如果需要访问一个页面，必须要写Controller类，然后再写一个方法跳转到页面，感觉好麻烦，其实重写WebMvcConfigurer中的addViewControllers方法即可达到效果了。

```java
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/toLogin").setViewName("login");
    }
```

指出的是，在这里重写addViewControllers方法，并不会覆盖**WebMvcAutoConfiguration**（Springboot自动配置）中的addViewControllers（在此方法中，Spring Boot将“/”映射至index.html），这也就意味着自己的配置和Spring Boot的自动配置同时有效，这也是我们推荐添加自己的MVC配置的方式。

#### 7.2.3、addResourceHandlers：静态资源

我们想自定义静态资源映射目录的话，只需重写 `addResourceHandlers` 方法即可。

```java
@Configuration
public class MyWebMvcConfigurerAdapter implements WebMvcConfigurer {
    /**
     * 配置静态访问资源
     * @param registry
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/my/**").addResourceLocations("classpath:/my/");
    }
}
```

- addResoureHandler：指的是对外暴露的访问路径
- addResourceLocations：指的是内部文件放置的目录

#### 7.2.4、configureDefaultServletHandling：默认静态资源处理器

```java
@Override
public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
        configurer.enable("defaultServletName");
}
```

此时会注册一个默认的 `Handler：DefaultServletHttpRequestHandler`，这个Handler也是用来处理静态文件的，它会尝试映射`/`。当 `DispatcherServelt` 映射/时（/ 和/ 是有区别的），并且没有找到合适的Handler来处理请求时，就会交给DefaultServletHttpRequestHandler 来处理。注意：这里的静态资源是放置在web根目录下，而非WEB-INF 下。
　　可能这里的描述有点不好懂（我自己也这么觉得），所以简单举个例子，例如：在web root目录下有一个图片：1.png 我们知道Servelt规范中web根目录（web root）下的文件可以直接访问的，但是由于DispatcherServlet配置了映射路径是：/ ，它几乎把所有的请求都拦截了，从而导致1.png 访问不到，这时注册一个DefaultServletHttpRequestHandler 就可以解决这个问题。其实可以理解为DispatcherServlet破坏了Servlet的一个特性（根目录下的文件可以直接访问），DefaultServletHttpRequestHandler是帮助回归这个特性的

#### 7.2.5、configureViewResolvers：视图解析器

这个方法是用来配置视图解析器的，该方法的参数 `ViewResolverRegistry` 是一个注册器，用来注册你想自定义的视图解析器等。

```java
/**
 * 配置请求视图映射
 * @return
 */
@Bean
public InternalResourceViewResolver resourceViewResolver()
{
	InternalResourceViewResolver internalResourceViewResolver = new InternalResourceViewResolver();
	//请求视图文件的前缀地址
	internalResourceViewResolver.setPrefix("/WEB-INF/jsp/");
	//请求视图文件的后缀
	internalResourceViewResolver.setSuffix(".jsp");
	return internalResourceViewResolver;
}
 
/**
 * 视图配置
 * @param registry
 */
@Override
public void configureViewResolvers(ViewResolverRegistry registry) {
	super.configureViewResolvers(registry);
	registry.viewResolver(resourceViewResolver());
	/*registry.jsp("/WEB-INF/jsp/",".jsp");*/
}
```

#### 7.2.6、addCorsMappings：跨域

```java
@Override
public void addCorsMappings(CorsRegistry registry) {
    super.addCorsMappings(registry);
    registry.addMapping("/cors/**")
            .allowedHeaders("*")
            .allowedMethods("POST","GET")
            .allowedOrigins("*");
}
```

#### 7.2.7、configureMessageConverters：信息转换器

```java
 
/**
* 消息内容转换配置
 * 配置fastJson返回json转换
 * @param converters
 */
@Override
public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    //调用父类的配置
    super.configureMessageConverters(converters);
    //创建fastJson消息转换器
    FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
    //创建配置类
    FastJsonConfig fastJsonConfig = new FastJsonConfig();
    //修改配置返回内容的过滤
    fastJsonConfig.setSerializerFeatures(
            SerializerFeature.DisableCircularReferenceDetect,
            SerializerFeature.WriteMapNullValue,
            SerializerFeature.WriteNullStringAsEmpty
    );
    fastConverter.setFastJsonConfig(fastJsonConfig);
    //将fastjson添加到视图消息转换器列表内
    converters.add(fastConverter);

}
```

