# SpringBoot

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

## 5、Spring Boot的常用注解

- **`@SpringBootApplication` 注解**：**包含了`@SpringBootConfiguration`，`@EnableAutoConfiguration`，`@ComponentScan`这三个注解。**
  - `@SpringBootConfiguration`: 标注当前类是配置类，这个注解继承自 `@Configuration`。并会将当前类内声明的一个或多个以 `@Bean` 注解标记的方法的实例纳入到spring容器中，并且实例名就是方法名。
  - `@EnableAutoConfiguration` : 是自动配置的注解，这个注解会根据我们添加的组件jar来完成一些默认配置，我们会添加spring-boot-starter-web这个组件jar的pom依赖，这样配置会默认配置springmvc 和tomcat。
  - `@ComponentScan` : 标识扫描路径，因为默认是没有配置实际扫描路径，所以SpringBoot扫描的路径是启动类所在的当前⽬录。
- **`@Controller`**：表明这个类是一个**控制器类**，和**`@RequestMapping`**来配合使用拦截请求，如果不在method中注明请求的方式，默认是拦截get和post请求。这样请求会完成后转向一个视图解析器。但是在大多微服务搭建的时候，前后端会做分离。所以请求后端只关注数据处理，后端返回 `json` 数据的话，需要配合 `@ResponseBody` 注解来完成。
  - `@RestController` 是 `@Controller` 和 `@ResponseBody` 的结合，一个类被加上 `@RestController` 注解，数据接口中就不再需要添加 `@ResponseBody`。
  - **`@Autowired`**: 这是个最熟悉的注解，是spring的自动装配，这个注解可以用到构造器，变量域，方法，注解类型上。当我们需要从 bean 工厂中获取一个bean时，Spring会自动为我们装配该bean中标记为 `@Autowired` 的元素。
  - `@PathVariable`：路径变量注解。
  - `@RequestMapping` : 中用`{}`来定义url部分的变量名
- **`@Service`：**这个注解用来标记**业务层**的组件，我们会将**业务逻辑处理的类**都会加上这个注解交给spring容器。**事务的切面**也会配置在这一层。
  - `@Resource`：``@Resource` 和 `@Autowired` 一样都可以用来装配bean，都可以标注字段上，或者方法上。 `@Resource` 注解不是spring提供的，是属于J2EE规范的注解。
  - `@Resource` 默认按照名称方式进行bean匹配
  - `@Autowired` 默认按照类型方式进行bean匹配。
- `@Repository`：``@Repository`注解类作为DAO对象，管理操作数据库的对象。
  - `@Component`, `@Service`, `@Controller`, `@Repository`是spring注解，注解后可以被spring框架所扫描并注入到spring容器来进行管理。
  - `@Component`是通用注解，其他三个注解是这个注解的拓展，并且具有了特定的功能。
- **`@Transactional`： 通过这个注解可以声明事务，可以添加在类上或者方法上。**

## 6、SpringBoot Bean

### 6.1、Spring Boot中 Bean的加载过程

https://blog.csdn.net/caoyuanyenang/article/details/110505166

- 根据 `package` 扫描出需要被管理的类
- 将这些类封装成 `BeanDefinition `并注册到 `BeanFactory `容器
- 实例化所有扫描到的 `BeanDefinition`，其中包括解决循环依赖、延迟加载问题

<img src="https://img-blog.csdnimg.cn/20210304135252666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nhb3l1YW55ZW5hbmc=,size_16,color_FFFFFF,t_70">

