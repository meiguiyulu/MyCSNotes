Spring

#### 什么是 Spring 框架?

Spring是一种轻量级开发框架，旨在提高开发人员的开发效率以及系统的可维护性。Spring官网：https://spring.io/。

我们一般说Spring框架指的都是Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。

这些模块是：核心容器、数据访问/集成，、Web、AOP（面向切面编程）、工具、消息和测试模块。

比如：Core Container中的Core组件是Spring所有组件的核心，Beans组件和Context组件是实现IOC和依赖注入的基础，AOP组件用来实现面向切面编程。

**Spring是一个轻量级的控制反转(IOC)和面向切片编程(AOP)的框架!**



Spring 官网列出的Spring的6个特征：

- **核心技术：**依赖注入（Dl），AOP，事件（events），资源，i18n(国际化)，验证，数据绑定，类型转换，SpEL。
- **测试：**模拟对象，TestContext框架，Spring MVC测试，Web TestClient。
- **数据访问：**事务，DAO支持，JDBC，ORM，编组XML。
- **Web支持：**Spring MVC和Spring WebFlux Web框架。
- **集成：**远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存。
- **语言：**Kotlin，Groovy，动态语言。



```markdown
**
Spring的历史
1. 2004 年 03 月，1.0 版发布。
2. 2006 年 10 月，2.0 版发布。
3. 2007 年 11 月，更名为 SpringSource，同时发布了 Spring 2.5。
4. 2009 年 12 月，Spring 3.0 发布。
5. 2013 年 12 月，Pivotal 宣布发布 Spring 框架 4.0。
6. 2017 年 09 月，Spring 5.0 发布。

SpringMVC、SpringBoot、Spring Cloud、Spring Cloud Dataflow等。有人亲切的称之为：Spring 全家桶。
```





#### 列举⼀些重要的Spring模块？

下图对应的是Spring4.x版本。目前最新的5.x版本中Web模块的Portlet组件已经被废弃掉，同时增加了用于异步响应式处理的WebFlux组件。

![image-20210421103336321](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421103336321.png)

- **Spring Core：**基础，可以说Spring 其他所有的功能都需要依赖于该类库。主要提供loC、依赖注入功能。
- **Spring Aspects：**该模块为与AspectJ的集成提供支持。
- **Spring AOP：**提供了面向切面的编程实现。
- **Spring JDBC:** Java数据库连接。
- **Spring JMS:** Java消息服务。
- **Spring ORM：**用于支持Hibernate等现有的ORM工具。
- **Spring Web：**为创建Web应用程序提供支持。
- **Spring Test：**提供了对JUnit和TestNG测试的支持。



#### Spring容器的启动流程

1. `Spring` 的源码实现的是⾮常优秀的，比如说设计模式的应⽤、并发安全的实现、⾯向接⼝的设计等。
2. 在创建 `Spring` 容器，也就是启动 `Spring` 时：
   - ⾸先会进⾏扫描，扫描得到所有的 `BeanDefinition` 对象，并存在于⼀个 `Map` 中
   - 然后筛选出**⾮懒加载**的单例 `BeanDefinition` 进⾏创建 `Bean` ，对于**多例Bean不需要在启动过程中去进⾏创建**，对于多例 `Bean` 会在每次获取 `Bean` 时利⽤ `BeanDefinition` 去创建
   - 利⽤ `BeanDefinition` 创建 `Bean` 就是 `Bean` 的创建⽣命周期，这期间包括了合并 `BeanDefinition` 、推断构造⽅法、实例化、属性填充、初始化前、初始化、初始化后等步骤，其中 **`AOP` 就是发⽣在初始化后这⼀步骤中**。
3. 单例 `Bean` 创建完了之后，`Spring` 会发布⼀个容器启动事件。
4. `Spring` 启动结束。
5. 在源码中会更复杂，⽐如源码中会提供⼀些模板⽅法，让⼦类来实现，⽐如源码中还涉及到⼀些`BeanFactoryPostProcessor` 和 `BeanPostProcessor` 的注册，`Spring` 的扫描就是通过 `BenaFactoryPostProcessor` 来实现的，依赖注⼊就是通过 `BeanPostProcessor` 来实现的。
6. 在 `Spring` 启动过程中还会去处理 `@Import` 等注解。 

#### Spring IOC 

##### IOC 控制反转

​		**`IoC`**（`Inverse of Control`：控制反转）是一种**设计思想**，就是将**原本在程序中手动创建对象的控制权，交由Spring框架来控制，在Spring工厂和配置文件中完成**。loC在其他语言中也有应用，并非Spring特有。**loC容器是 Spring 用来实现loC的载体，loC 容器实际上就是个Map（key，value），Map中存放的是各种对象**。

​		**好处：解耦合(解决对象之间耦合度过高的问题)**。

```markdown
1. 软件系统在没有引入IOC容器之前，对象A依赖于对象B，那么对象A在初始化或者运行到某一点的时候，自己必须主动去创建对象B或者使用已经创建的对象B。无论是创建还是使用对象B，控制权都在自己手上。

 2. 软件系统在引入IOC容器之后，这种情形就完全改变了，由于IOC容器的加入，对象A与对象B之间失去了直接联系，所以，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。

 - 通过前后的对比，我们不难看出来：对象A获得依赖对象B的过程,由主动行为变为了被动行为，控制权颠倒过来了，这就是“控制反转”这个名称的由来。
```

> 既然 `IOC` 是控制反转，那么到底是“哪些方面的控制被反转了呢？”，经过详细地分析和论证后，他得出了答案：“**获得依赖对象的过程被反转了**”

**实现 `IOC` 的方法**：注入。所谓**依赖注入( `Dependency Injection` : `DI`)**，就是由 `IOC` 容器在运行期间，动态地将某种依赖关系注入到对象之中。

```markdown
# 注入： 通过Spring的工厂及配置文件，为对象(bean，组件)的成员变量赋值

# 依赖注入：当一个类需要另一个类的时候，就意味着存在依赖，一旦出现依赖就可以把另一个类作为本类的成员变量，最终通过Spring的配置文件进行注入(赋值)
	# 比如说 Service 层需要DAO层，所以我们在定义 xxxxService的时候，都会定义一个xxxDAO的成员变量，我们给这个变量赋值的时候，就是依赖注入。
```

##### IOC底层原理
xml解析、**工厂设计模式**、**反射**。

##### IOC过程

- 创建xml配置文件，配置要创建对象类

  - ```xml
    <bean id="userService" class="....."/>
    ```

- 创建工厂类，使用 `dom4j` 解析配置文件+反射
  - 使用 `dom4j` 解析 `xml` 文件
  
  - 使用反射创建类对象
  
  - ```java
    public class UserFactory{
    
        public static UserService getUserService(){
    
            //使用dom4j解析配置文件
    
            //根据id值获得class的属性值
    
            String classValue="class属性值"；
    
            //使用反射来创建class对象
            Class class=Class.forName(classValue);
    
            UserService service=class.newInstatnce();
    
        	return service;
    
        }
    } 
    ```



##### 两种类型的 IOC 容器(`ApplicationContext`、`BeanFactory`)

- **`BeanFactory`**: `IOC` 容器的基本实现。
- **`ApplicationContext`**: 提供了更多的高级特性. 是 `BeanFactory` 的子接口。



<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/4476195-19f343df32c7cf74">

从图上可以看到：`ApplicationContext`是`BeanFactory`的子接口，所以，`ApplicationContext`可以看做更强大的`BeanFactory`，比如说：国际化；统一的资源文件访问方式；提供在监听器中注册 `bean` 的事件；可以同时加载多个配置文件。他们两个之间的区别如下：

1. 
   -  `BeanFactory` 采用的是 **懒加载** 机制来注入 `Bean` 的，即只有在使用到某个 `Bean` 时(调用`getBean()`)，才对该 `Bean` 进行实例化。所以，相对来说，容器启动初期速度较快，所需要的资源有限。这样就不能发现一些存在的配置问题，如果 `Bean `的某一个属性没有注入，只能等到调用`getBean()`方法后才会抛出异常。
   - `ApplicationContext` 是在容器启动时，一次性创建所有的 `Bean`。相对于`BeanFactory`来说`ApplicationContext`要求更多的系统资源，同时，因为在启动时就完成所有初始化，容器启动时间较之`BeanFactory`也会长一些。
2. `BeanFactory`通常是以编程的方式被创建，`ApplicationContext` 可以以声明的方式创建，如使用 `ContextLoader`。
3. `BeanFactory`和`ApplicationContext` 都支持 `BeanPostProcessor` 、`BeanFactoryPostProcessor`的使用，但两者之间的区别是：`BeanFactory`需要手动注册，而 `ApplicationContext` 则是自动注册。

###### ApplicationContext

​	`ApplicationContext` 的主要实现类是 `ClassPathXmlApplicationContext` 和 `FileSystemXmlApplicationContext` ，前者默认从类路径加载配置文件，后者默认从文件系统中装载配置文件。

- 如果配置文件放置在类路径下，用户可以优先使用 `ClassPathXmlApplicationContext` 实现类：

  - ```java
    ApplicationContext ctx = new ClassPathXmlApplicationContext(“com/baobaotao/context/beans.xml”)
    ```

- 如果配置文件放置在文件系统的路径下，则可以优先考虑使用 `FileSystemXmlApplicationContext` 实现类：

  - ```java
    ApplicationContext ctx = new FileSystemXmlApplicationContext(“com/baobaotao/context/beans.xml”);  
    ```

​    在获取 `ApplicationContext` 实例后，就可以像 `BeanFactory` 一样调用 `getBean(beanName)` 返回 `Bean` 了。`ApplicationContext` 的初始化和 `BeanFactory` 有一个重大的区别：`BeanFactory` 在初始化容器时，并未实例化`Bean` ，直到第一次访问某个 `Bean` 时才实例目标`Bean`；而 `ApplicationContext` 则在初始化应用上下文时就实例化所有单实例的` Bean` 。因此 `ApplicationContext` 的初始化时间会比 `BeanFactory` 稍长一些，不过稍后的调用则没有”第一次惩罚”的问题。



#### ==Spring AOP==

​		`AOP`（Aspect-Oriented Programming：面向切面编程）能够**将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来**，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

​		**`Spring AOP` 就是基于动态代理**的，如果要代理的对象，实现了某个接口，那么`Spring AOP`会使用`JDKProxy`，去创建代理对象，而对于没有实现接口的对象，就无法使用`JDKProxy` 去进行代理了，这时候`Spring AOP`会使用`Cglib`，这时候`Spring AOP`会使用`Cglib`生成一个被代理对象的子类来作为代理，如下图所示：

![image-20210421104811050](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421104811050.png)

**提到AOP就必须要了解的知识点：**

```markdown
**
1. 切面：拦截器类，其中会定义切点以及通知
2. 切点：具体拦截的某个业务点。
3. 通知：切面当中的方法，声明通知方法在目标业务层的执行位置，通知类型如下：
    前置通知：@Before 在目标业务方法执行之前执行
    后置通知：@After 在目标业务方法执行之后执行
    返回通知：@AfterReturning 在目标业务方法返回结果之后执行
    异常通知：@AfterThrowing 在目标业务方法抛出异常之后
    环绕通知：@Around 功能强大，可代替以上四种通知，还可以控制目标业务方法是否执行以及何时执行
```

**动态代理类没有对应的 `.class`文件，所以 JVM 不会为其分配 `ClassLoader`。**

##### [AOP的底层实现原理](https://www.cnblogs.com/shoshana-kong/p/9042102.html)

​		`Spring`的`AOP`实现原理其实很简单，就是通过**动态代理**实现的。如果我们为`Spring`的某个`bean`配置了切面，那么`Spring`在创建这个`bean`的时候，实际上创建的是这个`bean`的一个代理对象，我们后续对`bean`中方法的调用，实际上调用的是代理类重写的代理方法。而`Spring`的`AOP`使用了两种动态代理，分别是**JDK动态代理**，以及**CGLib动态代理**。

###### JDK动态代理

**底层原理**

​		`JDK`的动态代理是基于**反射** 实现的。`JDK`通过反射，生成一个代理类，这个代理类实现了原来那个类的全部接口，并对接口中定义的所有方法进行了代理。当我们通过代理对象执行原来那个类的方法时，代理类底层会通过反射机制，回调我们实现的`InvocationHandler`接口的`invoke`方法。**并且这个代理类是Proxy类的子类**（记住这个结论，后面测试要用）。这就是`JDK`动态代理大致的实现方式。

```markdown
**
1. 优点
 > JDK动态代理是JDK原生的，不需要任何依赖即可使用；
 > 通过反射机制生成代理类的速度要比CGLib操作字节码生成代理类的速度更快；
2. 缺点
 > 如果要使用JDK动态代理，被代理的类必须实现了接口，否则无法代理；
 > JDK动态代理无法为没有在接口中定义的方法实现代理，假设我们有一个实现了接口的类，我们为它的一个不属于接口中的方法配置了切面，Spring仍然会使用JDK的动态代理，但是由于配置了切面的方法不属于接口，为这个方法配置的切面将不会被织入。
 > JDK动态代理执行代理方法时，需要通过反射机制进行回调，此时方法执行的效率比较低；
```

**实现步骤**

- 被代理类需要实现统一接口

- 代理类要实现 `InvocationHandler` 接口，重写 `invoke`方法。

  - ```java
    public class MyHandler implements InvocationHandler {
        private Object target;
    
        MyHandler(Object target) {
            this.target = target;
        }
        
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    /*
    		函数作用：用于在原先对象方法实现的功能基础之上，书写额外的方法
    		Object：原始方法的返回值
    		参数：
    			Proxy：代表的是代理对象
    			Method：原始方法
    			args：原始方法的参数
    */
            System.out.println("before ...");
            Object result = method.invoke(target, args); // 原始方法的运行：对象+方法名+参数
            System.out.println("after ...");
            return result;
        }
    }
    ```

- 通过 JDK 代理 提供的静态方法`newProxyInstance(xxx)`来**动态**创建代理类

  - ```java
    public static void main(String[] args) {
            //创建被代理类
            IService service = new UserService();
            //新建代理类, 将被代理类注册到里面
            MyHandler aop = new MyHandler(service);
        // newProxyInstance()需要传递3个参数
                    //第一个参数:获取被代理类的classloader
                    //第二个参数:获取被代理类实现的接口, 由此可见被代理类需要实现统一的接口
                    //第三个参数:InvocationHandler的实例, 也就是我们做切面逻辑的类
            IService proxy = (IService) Proxy
                    .newProxyInstance(service.getClass().getClassLoader(),
                            service.getClass().getInterfaces(),
                            aop);
                    //调用代理方法
            proxy.save();
        }
    ```

`Proxy.newPorxyInstance` 方法参数详解

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20200530231452777.png">

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/202005302319274.png">

```java
public class TestJDKProxy {
    /**
     1. 借⽤类加载器  TestJDKProxy 或 UserServiceImpl 都可以
     2. JDK8.x 前必须加 final
     final UserService userService = new UserServiceImpl();
     */
    public static void main(String[] args) {
        // 1. 创建原始对象
        UserService userService = new UserServiceImpl();

        // 2. JDK 动态代理
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("---- proxy log ----");
                // 原始方法运行
                Object ret = method.invoke(userService, args);
                return ret;
            }
        };
        UserService userServiceProxy = (UserService) Proxy.
                newProxyInstance(TestJDKProxy.class.getClassLoader(),
                                userService.getClass().getInterfaces(),
                                handler);
        userServiceProxy.login("zhenyu", "123456");

        userServiceProxy.register(new User());
    }
}
```

**JDK动态代理产生的对象不再是原对象**

######  CGLIB动态代理

```markdown
# CGlib 创建动态代理的原理：通过父子继承关系创建代理对象，原始类作为父类，代理类作为子类，这样既可以保证二者方法⼀致，同时在代理类中可以提供新的实现（额外功能+原始方法）。
```



![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20200530234244268.png)



**实现原理**

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/jnbNov2005-1.png)

​		`CGLib`实现动态代理的原理是，底层采用了`ASM`字节码生成框架，直接对需要代理的类的字节码进行操作，生成这个类的一个子类，并**重写了类的所有可以重写的方法(`private`跟`final`方法不能重写)**，在重写的过程中，将我们定义的额外的逻辑（简单理解为`Spring`中的切面）织入到方法中，对方法进行了增强。而通过字节码操作生成的代理类，和我们自己编写并编译后的类没有太大区别。

```markdown
**
1. 优点
 > 使用CGLib代理的类，不需要实现接口，因为CGLib生成的代理类是直接继承自需要被代理的类;
 > CGLib生成的代理类是原来那个类的子类，这就意味着这个代理类可以为原来那个类中，所有能够被子类重写的方法进行代理;
 > CGLib生成的代理类，和我们自己编写并编译的类没有太大区别，对方法的调用和直接调用普通类的方式一致，所以CGLib执行代理方法的效率要高于JDK的动态代理;
2. 缺点
 > 由于CGLib的代理类使用的是继承，这也就意味着如果需要被代理的类是一个final类，则无法使用CGLib代理;
 > 由于CGLib实现代理方法的方式是重写父类的方法，所以无法对final方法，或者private方法进行代理，因为子类无法重写这些方法;
 > CGLib生成代理类的方式是通过操作字节码，这种方式生成代理类的速度要比JDK通过反射生成代理类的速度更慢;
```



**实现步骤：**

- 定义被代理类

  - ```java
    public class UserService {
        public void login(String name, String password) {
            System.out.println("UserService.login");
        }
        public void register(User user) {
            System.out.println("UserService.register");
        }
    }
    ```

- 实现`MethodInterceptor`接口(切面逻辑)

  - ```java
    public class UserProxy implements MethodInterceptor {
        @Override
        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
            //方法执行前的逻辑
            System.out.println("before ...");
            //参数o是被代理对象, invokeSuper方法调用父类方法, 在这前后我们可以实现自定义切面逻辑
            Object result = methodProxy.invokeSuper(o, objects);
            //方法执行后的逻辑
            System.out.println("after ...");
            return result;
        }
    }
    ```

- 获取被代理类`Factory`工厂, 该工厂专门生产加入了切面逻辑的被代理类

  - ```java
    public class UserServiceFactory {
        public static UserService getInstance(UserProxy proxy) {
            Enhancer enhancer = new Enhancer(); //Enhancer是一个字节码增强器
            enhancer.setSuperclass(UserService.class);
            //设置回调类,强化类调用回调类中的intercept方法来执行我们定义好的切面逻辑
            enhancer.setCallback(proxy);
            //返回一个添加了切面逻辑的增强类
            return (UserService) enhancer.create();
        }
    }
    ```

- 客户端调用

  - ```java
        public static void main(String[] args) {
            UserProxy proxy=new UserProxy();
            //通过工厂方法模式获取增强类
            UserService service = UserServiceFactory.getInstance(proxy);
            service.save();
        }
    ```

###### 总结

```markdown
**
> JDK动态代理   Proxy.newProxyInstance()   通过接口创建代理的实现类
> Cglib动态代理 Enhancer				   通过继承父类创建的代理类
3. Spring在运行期间生成动态代理对象，不需要特殊的编译器；
4. Spring AOP 优先对接口进行代理 (使用JDK动态代理)如果目标对象没有实现任何接口，才会对类进行代理 （使用CGLIB动态代理）
**
```

###### 需要注意的

1、**对接口创建代理优于对类创建代理**，因为会产生更加松耦合的系统，所以 `Spring `默认是使用  `JDK代理`。对类代理是让遗留系统或无法实现接口的第三方类库同样可以得到通知，这种方式应该是备用方案。

2、标记为 `final` 的方法不能够被通知。`Spring` 是为目标类产生子类。任何需要被通知的方法都被复写，将通知织入。`final` 方法是不允许重写的

3、`Spring` 只支持方法连接点：不提供属性接入点，spring的观点是属性拦截破坏了封装。面向对象的概念是对象自己处理工作，其他对象只能通过方法调用的得到的结果

##### AOP创建代理的源码分析

`createProxy` 方法：

```java
protected Object createProxy(
   		Class<?> beanClass, String beanName, Object[] specificInterceptors, TargetSource targetSource) {
   		
   	if (this.beanFactory instanceof ConfigurableListableBeanFactory) {
   		AutoProxyUtils.exposeTargetClass((ConfigurableListableBeanFactory) this.beanFactory, beanName, beanClass);
   	}

   // 1.创建proxyFactory，proxy的生产主要就是在proxyFactory做的
   	ProxyFactory proxyFactory = new ProxyFactory();
   	proxyFactory.copyFrom(this);

   	if (!proxyFactory.isProxyTargetClass()) {
   		if (shouldProxyTargetClass(beanClass, beanName)) {
   			proxyFactory.setProxyTargetClass(true);
   		}
   		else {
   			evaluateProxyInterfaces(beanClass, proxyFactory);
   		}
   	}

   // 2.将当前bean适合的advice，重新封装下，封装为Advisor类，然后添加到ProxyFactory中
   	Advisor[] advisors = buildAdvisors(beanName, specificInterceptors);
   	for (Advisor advisor : advisors) {
   		proxyFactory.addAdvisor(advisor);
   	}

   	proxyFactory.setTargetSource(targetSource);
   	customizeProxyFactory(proxyFactory);

   	proxyFactory.setFrozen(this.freezeProxy);
   	if (advisorsPreFiltered()) {
   		proxyFactory.setPreFiltered(true);
   	}

   // 3.调用getProxy获取bean对应的proxy
   	return proxyFactory.getProxy(getProxyClassLoader());
   }

```

`getProxy()` 方法：

`createAopProxy()`方法就是决定究竟创建何种类型的`proxy`

```java
	public Object getProxy(ClassLoader classLoader) {
		return createAopProxy().getProxy(classLoader);
	}
    // createAopProxy()方法就是决定究竟创建何种类型的proxy
	protected final synchronized AopProxy createAopProxy() {
		if (!this.active) {
			activate();
		}
        // 关键方法createAopProxy()
		return getAopProxyFactory().createAopProxy(this);
	}
	
    // createAopProxy()
	public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
        // 1.config.isOptimize()是否使用优化的代理策略，目前使用与CGLIB
        // config.isProxyTargetClass() 是否目标类本身被代理而不是目标类的接口
        // hasNoUserSuppliedProxyInterfaces()是否存在代理接口
		if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
			Class<?> targetClass = config.getTargetClass();
			if (targetClass == null) {
				throw new AopConfigException("TargetSource cannot determine target class: " +
						"Either an interface or a target is required for proxy creation.");
			}
            
            // 2.如果目标类是接口类（目标对象实现了接口），则直接使用JDKproxy
			if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
				return new JdkDynamicAopProxy(config);
			}
            
            // 3.其他情况则使用CGLIBproxy
			return new ObjenesisCglibAopProxy(config);
		}
		else {
			return new JdkDynamicAopProxy(config);
		}
	}

```



##### AOP实现步骤

**AOP(切面)=通知方法(5种)+切入点表达式(4种)**

**通知方法：**

> - **before通知** 在执行目标方法之前执行
> - **afterReturning通知** 在目标方法执行之后执行
> - **afterThrowing通知** 在目标方法执行之后报错时执行
> - **after通知** 无论什么时候程序执行完成都要执行的通知
>   上述的4大通知类型,不能控制目标方法是否执行.一般用来记录程序的执行的状态。一般应用与监控的操作.
> - **around通知(功能最为强大的)** 在目标方法执行前后执行。因为环绕通知可以控制目标方法是否执行.控制程序的执行的轨迹。

**切入点表达式：**

> - **bean(“bean的ID”) 粒度:** 粗粒度 按bean匹配 当前bean中的方法都会执行通知.
> - **within(“包名.类名”) 粒度**: 粗粒度 可以匹配多个类
> - **execution(“返回值类型 包名.类名.方法名(参数列表)”) 粒度:** 细粒度 方法参数级别
> - **@annotation(“包名.类名”) 粒度:** 细粒度 按照注解匹配



##### Spring AOP 和 AspectJ AOP 有什么区别？

​		**Spring AOP属于运行时增强，而AspectJ是编译时增强。**Spring AOP基于代理（Proxying），而AspectJ基于字节码操作（Bytecode Manipulation）。

​		Spring AOP已经集成了AspectJ，AspectJ应该算的上是Java生态系统中最完整的AOP框架了。AspectJ相比于Spring AOP功能更加强大，但是Spring AOP相对来说更简单，如果我们的切面比较少，那么两者性能差异不大。

但是，当切面太多的话，最好选择AspectJ，它比Spring AOP快很多。

#### Spring bean

##### Spring 中的 bean 的作⽤域有哪些?

- **单例 (singleton)：**默认，每个容器中只有一个 bean 的实例，单例的模式由 `BeanFactory` 自身来维护。该对象的生命周期是与 Spring IoC 容器一致的(第一次被注入时才会创建)。
- **原型（prototype）：**每次bean请求都会创建一个新的bean实例。每次注入时都会创建一个新的对象。
- **请求（request）：**每一次HTTP请求都会产生一个单例对象，该bean仅在当前HTTP request内有效。
- **会话（session）：**每一次HTTP请求都会产生一个单例对象，该bean仅在当前HTTP session内有效。
- **application：**bean被定义为在 ServletContext 的生命周期中复用一个单例对象。
- **websocket：** bean被定义为在 websocket 的生命周期中复用一个单例对象。
- **全局会话（global session）：**全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已经没有了。Portlet是能够生成语义代码（例如：HTML）片段的小型Java Web插件。它们基于portlet容器，可以像servlet一样处理HTTP请求。但是，与servlet不同，每个portlet都有不同的会话.

##### Spring 中的 bean 的线程安全问题

Spring容器本身并没有提供`Bean`的线程安全策略，因此可以说Spring容器中的Bean本身不具备线程安全的特性，但是具体情况还是要结合`Bean`的作用域来讨论。

1. 对于 `prototype` 作用域的Bean，每次都创建一个新对象，也就是线程之间不存在Bean共享，因此不会有线程安全问题。

2. 对于 `singleton` 作用域的 `Bean`，所有的线程都共享一个**单例实例的 `Bean`，因此是存在线程安全问题的**。如果单例 `Bean`, 是一个**无状态 `Bean`**，也就是线程中的操作不会对`Bean`的成员执行**查询**以外的操作，那么这个**单例`Bean`是线程安全**的。比如Spring mvc 的 Controller、Service、Dao等，这些`Bean`大多是无状态的，只关注于方法本身。


> 有状态Bean(Stateful Bean) ：就是**有实例变量**的对象，**可以保存数据**，是非线程安全的。
>
> 无状态Bean(Stateless Bean)：就是**没有实例变量**的对象，**不能保存数据**，是不变类，是线程安全的。

###### 单例bean线程安全解决问题

常见的有两种解决办法：

- 在Bean对象中尽量避免定义可变的成员变量（不太现实）。

- 在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中（推荐的一种方式）。

  > ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。同步机制采用了“时间换空间”的方式，仅提供一份变量，不同的线程在访问前需要获取锁，没获得锁的线程则需要排队。而ThreadLocal采用了“空间换时间”的方式。ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。

##### @Component 和 @Bean 的区别是什么？

1. 作用对象不同：`@Component `注解作用于类，而`@Bean` 注解作用于方法。
2. `@Component `通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用`@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到Spring的bean容器中）。`@Bean`注解通常是我们在标有该注解的方法中定义产生这个bean，`@Bean` 告诉了Spring这是某个类的示例，当我需要用它的时候还给我。
3. `@Bean `注解比`@Component` 注解的自定义性更强，而且很多地方我们只能通过`@Bean`注解来注册`bean`。比如当我们引用第三方库中的类需要装配到Spring容器时，则只能通过`@Bean `来实现。

![image-20210421110350068](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421110350068.png)

![image-20210421110403025](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421110403025.png)

下面这个例子是无法通过`@Component` 实现的。

![image-20210421110446305](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421110446305.png)

##### 将⼀个类声明为Spring的 bean 的注解有哪些?

我们一般使用`@Autowired `注解自动装配bean，要想把类标识成可用于`@Autowired` 注解自动装配的bean的类，采用以下注解可实现：

- `@Component`：通用的注解，可标注任意类为Spring组件。如果一个Bean不知道属于哪个层，可以使用@Component 注解标注。
- `@Repository`：对应持久层即Dao层，主要用于数据库相关操作。
- `@Service`：对应服务层，主要涉及一些复杂的逻辑，需要用到Dao层。
- `@Controller`：对应 Spring MVC控制层，主要用户接受用户请求并调用Service层返回数据给前端页面。

##### Spring 中的 bean 生命周期

Bean 的生命周期概括起来就是 **4 个阶段**：

1. 实例化（Instantiation）
2. 属性赋值（Populate）
3. 初始化（Initialization）
4. 销毁（Destruction）

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1704860a4de235aa~tplv-t2oaga2asx-watermark.awebp)

- 实例化：第 1 步，实例化一个 bean 对象；
- 属性赋值：第 2 步，为 bean 设置相关属性和依赖；
- 初始化：第 3~7 步，步骤较多，其中第 5、6 步为初始化操作，第 3、4 步为在初始化前执行，第 7 步在初始化后执行，该阶段结束，才能被用户使用；
- 销毁：第 8~10步，第8步不是真正意义上的销毁（还没使用呢），而是先在使用前注册了销毁的相关调用接口，为了后面第9、10步真正销毁 bean 时再执行相应的方法。

> 下面是比较详细的过程。
>
> 好吧 其实我也不知道哪个是对的，上面这个答案讲解一下，下面这个比较冗余些。

1. Spring启动，查找并加载需要被Spring管理的bean，进行Bean的实例化
2. Bean实例化后对将Bean的引入，值注入到Bean的属性中
3. 
   - 如果 Bean 实现了 `BeanNameAware` 接口的话，Spring将Bean的Id传递给 `setBeanName()` 方法
   - 如果 Bean实现了 `BeanFactoryAware` 接口的话，Spring将调用 `setBeanFactory()` 方法，将BeanFactory容器实例传入
   - 如果 Bean 实现了 `ApplicationContextAware` 接口的话，Spring将调用Bean的 `setApplicationContext()` 方法，将bean所在应用上下文引用传入进来。
   - 如果实现了其他 `*.Aware`接口，就调用相应的方法。
4. 如果 Bean实现了 `BeanPostProcessor` 接口，Spring就将调用他们的 `postProcessBeforeInitialization()` 方法。
5. 如果 Bean 实现了 `InitializingBean` 接口，Spring将调用他们的 `afterPropertiesSet()` 方法。类似的，如果bean使用 `init-method` 声明了初始化方法，该方法也会被调用
6. 如果 Bean 实现了 `BeanPostProcessor` 接口，Spring就将调用他们的 `postProcessAfterInitialization()` 方法。
7. 此时 Bean 已经准备就绪，可以被应用程序使用了。他们将一直驻留在应用上下文中，直到应用上下文被销毁。
8. 如果 bean实现了 `DisposableBean` 接口，Spring将调用它的 `destory()` 接口方法，同样，如果 bean 使用了 `destory-method` 声明销毁方法，该方法也会被调用。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/java0-1558500658.jpg)

> **上面是Spring 中Bean的核心接口和生命周期，面试回答上述过程已经足够了。但是翻阅JavaDoc文档发现除了以上接口外，还有另外的初始化过程涉及的接口：**
>
> **摘自org.springframework.beans.factory.BeanFactory， 全部相关接口如下，上述已有的就不用着重标注，把额外的相关接口着重标注**

![深究Spring中Bean的生命周期](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/java10-1558500659.jpg)

文字解释如下：

————————————初始化————————————

- BeanNameAware.setBeanName() 在创建此bean的bean工厂中设置bean的名称，在普通属性设置之后调用，在InitializinngBean.afterPropertiesSet()方法之前调用
- `BeanClassLoaderAware.setBeanClassLoader()`: 在普通属性设置之后，InitializingBean.afterPropertiesSet()之前调用
- BeanFactoryAware.setBeanFactory() : 回调提供了自己的bean实例工厂，在普通属性设置之后，在InitializingBean.afterPropertiesSet()或者自定义初始化方法之前调用
- `EnvironmentAware.setEnvironment()`: 设置environment在组件使用时调用
- `EmbeddedValueResolverAware.setEmbeddedValueResolver()`: 设置StringValueResolver 用来解决嵌入式的值域问题
- `ResourceLoaderAware.setResourceLoader()`: 在普通bean对象之后调用，在afterPropertiesSet 或者自定义的init-method 之前调用，在 ApplicationContextAware 之前调用。
- `ApplicationEventPublisherAware.setApplicationEventPublisher()`: 在普通bean属性之后调用，在初始化调用afterPropertiesSet 或者自定义初始化方法之前调用。在 ApplicationContextAware 之前调用。
- `MessageSourceAware.setMessageSource()`: 在普通bean属性之后调用，在初始化调用afterPropertiesSet 或者自定义初始化方法之前调用，在 ApplicationContextAware 之前调用。
- ApplicationContextAware.setApplicationContext(): 在普通Bean对象生成之后调用，在InitializingBean.afterPropertiesSet之前调用或者用户自定义初始化方法之前。在ResourceLoaderAware.setResourceLoader，ApplicationEventPublisherAware.setApplicationEventPublisher，MessageSourceAware之后调用。
- `ServletContextAware.setServletContext()`: 运行时设置ServletContext，在普通bean初始化后调用，在InitializingBean.afterPropertiesSet之前调用，在 ApplicationContextAware 之后调用**注：是在WebApplicationContext 运行时**
- BeanPostProcessor.postProcessBeforeInitialization() : 将此BeanPostProcessor 应用于给定的新bean实例 在任何bean初始化回调方法(像是InitializingBean.afterPropertiesSet或者自定义的初始化方法）之前调用。这个bean将要准备填充属性的值。返回的bean示例可能被普通对象包装，默认实现返回是一个bean。
- BeanPostProcessor.postProcessAfterInitialization() : 将此BeanPostProcessor 应用于给定的新bean实例 在任何bean初始化回调方法(像是InitializingBean.afterPropertiesSet或者自定义的初始化方法)之后调用。这个bean将要准备填充属性的值。返回的bean示例可能被普通对象包装
- InitializingBean.afterPropertiesSet(): 被BeanFactory在设置所有bean属性之后调用(并且满足BeanFactory 和 ApplicationContextAware)。

————————————销毁————————————

在BeanFactory 关闭的时候，Bean的生命周期会调用如下方法:

- `DestructionAwareBeanPostProcessor.postProcessBeforeDestruction()`: 在销毁之前将此BeanPostProcessor 应用于给定的bean实例。能够调用自定义回调，像是DisposableBean 的销毁和自定义销毁方法，这个回调仅仅适用于工厂中的单例bean(包括内部bean)
- 实现了自定义的destory()方法

###### 接口

**影响多个bean的接口**

> **InstantiationAwareBeanPostProcessor
> BeanPostProcessor**
>
> 实现这两个接口的bean，会自动切入到相应的生命周期中，其中`InstantiationAwareBeanPostProcessor`是作用于实例化阶段的前后，`BeanPostProcessor`是作用于初始化阶段的前后

**与aware有关的接口**

> BeanNameAware
> BeanFactoryAware
> ApplicationContextAware
>
> 需要实体类实现这些接口，分别调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值，调用 setBeanFactory() 方法传入当前工厂实例的引用，调用 setApplicationContext() 方法传入当前 ApplicationContext 实例的引用。

**两个生命周期接口**

> 实例化和属性赋值都是Spring帮助我们做的，能够自己实现的有初始化和销毁两个生命周期阶段。
> 1.InitializingBean 对应生命周期的初始化阶段，在源码的invokeInitMethods(beanName, wrappedBean, mbd);方法中调用。
> 2.DisposableBean 类似于InitializingBean，对应生命周期的销毁阶段，以ConfigurableApplicationContext#close()方法作为入口，实现是通过循环取所有实现了DisposableBean接口的Bean然后调用其destroy()方法 。

##### [Bean的加载流程](https://segmentfault.com/a/1190000012887776)

整个 `Bean` 的装载过程中，重点围绕 `AbstractBeanFactory`。代码下面有解释。

```java
public abstract class AbstractBeanFactory {

    @Override
    public Object getBean(String name) throws BeansException {
        return doGetBean(name, null, null, false);
    }

    protected <T> T doGetBean(final String name, @Nullable final Class<T> requiredType,
            @Nullable final Object[] args, boolean typeCheckOnly) throws BeansException {
        // 别名转换
        final String beanName = transformedBeanName(name);
        Object bean;

        // 检查缓存中是否存在 该 Bean 的单例（Bean默认的Scope = singleton）
        // 比如容器初始化的时候或者其他地方调用过getBean，已经完成了初始化
        Object sharedInstance = getSingleton(beanName);
        if (sharedInstance != null && args == null) {
            if (logger.isDebugEnabled()) {
                if (isSingletonCurrentlyInCreation(beanName)) {
                    logger.debug("Returning eagerly cached instance of singleton bean '" + beanName +
                            "' that is not fully initialized yet - a consequence of a circular reference");
                }
                else {
                    logger.debug("Returning cached instance of singleton bean '" + beanName + "'");
                }
            }
            // 返回实例，FactoryBean 的情况下，并不是直接返回实例本身而是返回指定方法返回的实例
            bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
        }

        else {
            // 存在循环依赖则报错
            if (isPrototypeCurrentlyInCreation(beanName)) {
                throw new BeanCurrentlyInCreationException(beanName);
            }

            // 判断工厂中是否含有当前 Bean 的定义
            BeanFactory parentBeanFactory = getParentBeanFactory();
            if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
                // 如果没有，查询父工厂
                String nameToLookup = originalBeanName(name);
                if (parentBeanFactory instanceof AbstractBeanFactory) {
                    return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
                            nameToLookup, requiredType, args, typeCheckOnly);
                }
                else if (args != null) {
                    // 执行带有 args 参数的 getBean 方法
                    return (T) parentBeanFactory.getBean(nameToLookup, args);
                }
                else {
                    // 如果没有参数，执行标准的 getBean 方法 
                    return parentBeanFactory.getBean(nameToLookup, requiredType);
                }
            }

            if (!typeCheckOnly) { // 如果不是做类型检查，则需要标记此 Bean 正在创建之中
                markBeanAsCreated(beanName);
            }

            try {
                // 将存储XML配置文件的GernericBeanDefinition转换成RootBeanDefinition，如果BeanName是子Bean的话会合并父类的相关属性
                final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
                checkMergedBeanDefinition(mbd, beanName, args);

                // 获取依赖的 Bean
                String[] dependsOn = mbd.getDependsOn();
                if (dependsOn != null) {
                    for (String dep : dependsOn) {
                        if (isDependent(beanName, dep)) {
                            throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                    "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
                        }
                        registerDependentBean(dep, beanName);
                        getBean(dep);
                    }
                }

                // 终于开始创建 Bean 实例了，如果是单例的，那么会创建一个单例的匿名工厂，
                // 如果是原型模式的，则不需要创建单例的工厂的，其他的如 request、session 作用域的，则根据自身的需要
                if (mbd.isSingleton()) {
                    sharedInstance = getSingleton(beanName, () -> {
                        try {
                            return createBean(beanName, mbd, args);// 调用默认构造函数，有兴趣自己跟进下代码
                        }
                        catch (BeansException ex) {
                            // Explicitly remove instance from singleton cache: It might have been put there
                            // eagerly by the creation process, to allow for circular reference resolution.
                            // Also remove any beans that received a temporary reference to the bean.
                            destroySingleton(beanName);
                            throw ex;
                        }
                    });
                    bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
                }

                else if (mbd.isPrototype()) {
                    // It's a prototype -> create a new instance.
                    Object prototypeInstance = null;
                    try {
                        beforePrototypeCreation(beanName);
                        prototypeInstance = createBean(beanName, mbd, args);
                    }
                    finally {
                        afterPrototypeCreation(beanName);
                    }
                    bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
                }

                else {
                    String scopeName = mbd.getScope();
                    final Scope scope = this.scopes.get(scopeName);
                    if (scope == null) {
                        throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
                    }
                    try {
                        Object scopedInstance = scope.get(beanName, () -> {
                            beforePrototypeCreation(beanName);
                            try {
                                return createBean(beanName, mbd, args);
                            }
                            finally {
                                afterPrototypeCreation(beanName);
                            }
                        });
                        bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
                    }
                    catch (IllegalStateException ex) {
                        throw new BeanCreationException(beanName,
                                "Scope '" + scopeName + "' is not active for the current thread; consider " +
                                "defining a scoped proxy for this bean if you intend to refer to it from a singleton",
                                ex);
                    }
                }
            }
            catch (BeansException ex) {
                cleanupAfterBeanCreationFailure(beanName);
                throw ex;
            }
        }

        // 类型检查，如果不能进行类型转换，则抛出异常
        if (requiredType != null && !requiredType.isInstance(bean)) {
            try {
                T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
                if (convertedBean == null) {
                    throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
                }
                return convertedBean;
            }
            catch (TypeMismatchException ex) {
                if (logger.isDebugEnabled()) {
                    logger.debug("Failed to convert bean '" + name + "' to required type '" +
                            ClassUtils.getQualifiedName(requiredType) + "'", ex);
                }
                throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
            }
        }
        return (T) bean;
    }
}
```

**转换 `BeanName`**

```java
final String beanName = transformedBeanName(name);

protected String transformedBeanName(String name) {
    return canonicalName(BeanFactoryUtils.transformedBeanName(name));
}
```

去除 `FactoryBean` 的修饰符，最终取指定 `alias` 所表示的 `beanName`。因为有可能获取到以 **&** 开头的 `FactoryBean`，所以要进行转化（关于 *`BeanFactory`* 和 *`FactoryBean`* 后面会进行区分）。

**加载单例**

检查缓存中是否存在该 `Bean` 的单例**`(Bean默认的Scope = singleton)`**，如容器初始化的时候或者其他地方已经调用过 `getBean()` 完成了初始化。





##### Bean的自动装配

  Spring IOC容器可以自动装配Bean，需要做的仅仅实在==<bean>的autowire属性里指定自动装配的模式。==

```xml
<bean id="customer" class="com.xxx.xxx.Customer" autowire=""/>
```

`autowire` 有五种装配的方式：![image-20210804222349207](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210804222349207.png)

- 缺省状态下，自动配置是通过 `ref` 属性手动设置的。

- ==`byName`==-根据Bean的属性名称进行自动装配

  - ```xml
    Customer的属性名称是person，spring会将bean id为person的bean通过setter方法进行自动装配
    <bean id="customer" class="com.xxx.xxx.Customer" autowire="byName"/>
    <bean id="person" class="com.xxx.xxx.Person"/>
    ```

- ==`bytype`==-根据Bean的类型进行自动装配

  - ```xml
    Customer的属性person的类型是Person，spring会将Person类型通过setter方法进行自动装配
    <bean id="customer" class="com.xxx.xxx.Customer" autowire="byType"/>
    <bean id="person" class="com.xxx.xxx.Person"/>
    ```

- constructor(通过构造器自动装配): 当Bean中存在多个构造器时，此种自动装配方式会很复杂，不推荐使用。

  - 类似于byType，不过是应用于构造器的函数。如果一个bean与构造器参数的类型相同，则进行自动装配，否则导致异常。

  - ```java
    Customer构造函数的参数person的类型是Person Spring会将Person类型通过构造函数进行自动装配
    <bean id="customer" class="com.xxx.xxx.Customer" autowire="constructor"/>
    <bean id="person" class="com.xxx.xxx.Person"/>
    ```

- autodetect- 如果有默认的构造器，则通过constructor进行自动装配，否则使用byType方式进行自动装配。

  - ```java
    如果有默认的构造器，则通过 constructor 进行自动装配，否则使用 byType 进行自动装配
    ```



##### Spring   bean的循环依赖以及解决方式

https://cloud.tencent.com/developer/article/1769948

###### 什么是循环依赖?

```markdown
**
> Bean A 依赖 B，Bean B 依赖 A 这种情况下出现循环依赖。
	Bean A → Bean B → Bean A
	更复杂的间接依赖造成的循环依赖如下:
	Bean A → Bean B → Bean C → Bean D → Bean E → Bean A
这里不是函数的循环调用，是对象的相互依赖关系。循环调用其实就是一个死循环，除非有终结条件。
```

###### 循环依赖会产生什么结果？

```markdown
**
1. 当Spring正在加载所有Bean时，Spring尝试以能正常创建Bean的顺序去创建Bean。
	1.1 例如，有如下依赖: Bean A → Bean B → Bean C
	1.2 Spring先创建beanC，接着创建bean B（将C注入B中)，最后创建bean A(将B注入A中)。
2. 但当存在循环依赖时，Spring将无法决定先创建哪个bean。这种情况下，Spring将产生异常`BeanCurrentlyInCreationException`。
```

###### Spring中循环依赖的场景

![image-20210909231257417](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210909231257417.png)

- ### 单例的setter注入

  - 这种注入方式应该是 `spring` 用的最多的，举例：

  - ```java
    @Service
    public class TestService1 {
    
        @Autowired
        private TestService2 testService2;
    
        public void test1() {
        }
    }
    
    @Service
    public class TestService2 {
    
        @Autowired
        private TestService1 testService1;
    
        public void test2() {
        }
    }
    ```

  - 这是一个经典的循环依赖，但是它能正常运行，得益于 `spring` 的内部机制，让我们根本无法感知它有问题，因为`spring` 默默帮我们解决了。

  - ```markdown
    **
    > spring内部有三级缓存：
    	singletonObjects 一级缓存，用于保存实例化、注入、初始化完成的bean实例
    	earlySingletonObjects 二级缓存，用于保存实例化完成的bean实例
    	singletonFactories 三级缓存，用于保存bean创建工厂，以便于后面扩展有机会创建代理对象
    ```

  - ![image-20210910214122558](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210910214122558.png)

  - 

- ### 多例的setter注入

  - 

- 



###### 怎么检测是否存在循环依赖

检测循环依赖相对比较容易，Bean在创建的时候可以给该Bean打个标记，如果递归调用回来发现正在创建中的话，即说明了循环依赖了。

###### Spring怎么解决循环依赖

>  Spring的循环依赖的理论依据其实是基于Java的引用传递，当我们获取到对象的引用时，对象的field或zh属性是可以延后设置的(但是构造器必须是在获取引用之前)。

==非单例时，无法解决==

Spring的单例对象的初始化主要分为三步：

![image-20210809092813384](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210809092813384.png)

（1）createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象

（2）populateBean：填充属性，这一步主要是多bean的依赖属性进行填充

（3）initializeBean：调用spring xml中的 `init` 方法。

循环依赖主要发生在第一、第二步。也就是构造器循环依赖和field循环依赖。



对于单例来说，在Spring容器整个生命周期内，有且只有一个对象，所以很容易想到这个对象应该存在Cache中，Spring为了解决单例的循环依赖问题，使用了**三级缓存**。

调整配置文件，将**构造函数注入**方式改为 **属性注入方式** 即可

> singletonFactories ： 单例对象工厂的cache
> earlySingletonObjects ：提前暴光的单例对象的Cache
> singletonObjects：单例对象的cache
>
> 我们在创建bean的时候，首先想到的是从cache中获取这个单例的bean，这个缓存就是singletonObjects。
>
> Spring首先从一级缓存singletonObjects中获取。如果获取不到，并且对象正在创建中，就再从二级缓存earlySingletonObjects中获取。如果还是获取不到且允许singletonFactories通过getObject()获取，就从三级缓存singletonFactory.getObject()(三级缓存)获取。如果获取到了从singletonFactories中移除，并放入earlySingletonObjects中。其实也就是从三级缓存移动到了二级缓存。



#### Spring框架中用到了哪些设计模式？

1. 简单工厂模式：由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类。

   ```markdown
   Spring 中的 BeanFactory就是根据简单工厂模式的体现，根据传入一个唯一的标识来获得 Bean 对象，但是否是在传入参数后创建还是传入参数前创建，这个要根据具体情况来定。
   ```

2. 工厂模式：

   ```markdown
   实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是：spring会在使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是bean.getObject()方法的返回值。
   ```

3. 单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

   ```markdown
   spring对单例的实现：spring中的单例模式提供了全局的访问点BeanFactory。但没有从构造器级别去控制单例，这是因为spring管理的是任意的Java对象。
   ```

4. 适配器模式

   ```markdown
   spring定义了一个适配器接口，使得每一种controller有一种对应的适配器实现类，让适配器代替controller指向相应的方法，这样在扩展Controller时，只需要增加一个适配器类就完成了SringMVC的扩展了。
   ```

5. 装饰器模式：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更加灵活。

   ```markdown
   Spring中用到的装饰器模式在类名上有两种表现：一种是类名中含有Wrapper，另一种是类名中含有Decorator。
   ```

6. 动态代理：

   ```markdown
   切面在应用运行的时候被织入。一般情况下，在织入切面时，AOP容器会为目标动态地创建一个代理对象。Spring AOP就是以这种方式织入切面的。
   ```

7. 观察者模式：

   ```markdown
   spring的事件驱动模型使用的是 观察者模式，Spring中的观察者模式常用的地方是listener的实现。
   ```

8. 策略模式

   ```markdown
   Spring框架的资源访问Resource接口。该接口提供了更强的资源访问能力，Spring框架本身大量使用了 Resource接口来访问底层资源。
   ```




#### Spring MVC

##### 说说⾃⼰对于 Spring MVC 了解?

谈到这个问题，我们不得不提提之前Model1和Model2这两个没有Spring MVC的时代。

- Model1时代：很多学Java 后端比较晚的朋友可能并没有接触过Model1模式下的Java Web应用开发。在Model1模式下，整个Web应用几乎全部用JSP页面组成，只用少量的Java Bean 来处理数据库连接、访问等操作。这个模式下JSP即是控制层又是表现层。显而易见，这种模式存在很多问题。比如①将控制逻辑和表现逻辑混杂在一起，导致代码重用率极低；②前端和后端相互依赖，难以进行测试并且开发效率极低；
- Model2时代：学过Servlet 并做过相关Demo的朋友应该了解“Java Bean（Model）+ JSP（View）+ Servlet（Controller）”这种开发模式，这就是早期的JavaWeb MVC开发模式。Model：系统涉及的数据，也就是dao和bean。View：展示模型中的数据，只是用来展示。Controller：处理用户请求都发送给，返回数据给JSP并展示给用户。

Model2模式下还存在很多问题，Model2的抽象和封装程度还远远不够，使用Model2进行开发时不可避免地会重复造轮子，这就大大降低了程序的可维护性和复用性。于是很多JavaWeb开发相关的MVC框架应运而生比如Struts2，但是Struts2比较笨重。随着Spring 轻量级开发框架的流行，Spring 生态圈出现了Spring MVC框架，Spring MVC是当前最优秀的MVC框架。相比于Struts2，Spring MVC使用更加简单和方便，开发效率更高，并且Spring MVC运行速度更快。

>  MVC是一种设计模式，Spring MVC是一款很优秀的MVC框架。Spring MVC可以帮助我们进行更简洁的Web层的开发，并且它天生与Spring框架集成。Spring MVC下我们一般把后端项目分为Service层（处理业务）、Dao层（数据库操作）、Entity层（实体类）、Controller层（控制层，返回数据给前台页面）。

Spring MVC 的简单原理图如下：

![image-20210421111537740](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421111537740.png)

##### SpringMVC ⼯作原理了解

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/5220087-3c0f59d3c39a12dd.png)

###### **流程说明（重要）：**

1. 
   用户发送请求至前端控制器 `DispatcherServlet`
2. `DispatcherServlet` 收到请求调用 处理器映射器 `HandlerMapping`。
3. 处理器映射器根据请求 `url` 找到具体的处理器，生成处理器执行链 `HandlerExecutionChain` (包括处理器对象和处理器拦截器)一并返回给 `DispatcherServlet`。
4. `DispatcherServlet` 根据处理器 `Handler` 获取处理器适配器 `HandlerAdapter` , 执行 `HandlerAdapter` 处理一系列的操作，如：参数封装，数据格式转换，数据验证等操作。
5. 调用处理器 `Handler(Controller，也叫页面控制器)`。
6. `Handler` 执行完成返回 `ModelAndView`
7. `HandlerAdapter` 将 `Handler` 执行结果 `ModelAndView` 返回到 `DispatcherServlet`
8. `DispatcherServlet` 将 `ModelAndView` 传给 `ViewReslover` 视图解析器
9. `ViewReslover` 解析后返回具体 `View`
10. `DispatcherServlet` 对 `View` 进行渲染视图（即将模型数据model填充至视图中）。
11. `DispatcherServlet` 响应用户。

##### SpringMVC的主要组件

1. `DispatcherServlet`：前端控制器，主要负责捕获来自客户端的请求和调度各个组件。
2. **`HandlerMapping`：**处理器映射器，负责根据用户请求找到Handler(处理器)，springmvc提供了不同的映射器实现方式。
3. `Handler`：处理器，对具体的用户请求进行处理。
4. **`HandlerAdapter`：**处理器适配器，通过HandlerAdapter对处理器进行执行。
5. `View Resolver`：视图解析器，负责将处理结果生成view视图。View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。

##### [如何使用SpringMVC对用户写入的参数进行修改](https://www.cnblogs.com/wyhluckdog/p/10181134.html)

- 基本数据类型接收表单数据

  - springmvc可以直接接收基本数据类型，包括string。springmvc可以帮你在传参时自动进行类型转换。
  - ==转换的前提：controller方法接收的参数名称必须等于页面上input框的name属性名。==

- 在spring的业务层的`ItemService`接口中添加通过id进行更新数据库的方法。`public void update(Items items);`

- 在`ItemServiceImpl`实现类中定义`public void update(Items items)`方法

  - ```java
    @Override
        public void update(Items items) {
            //
            //按照主键id进行修改
            //使用WithBLOBs作用是最整个文本进行修改
            itemsMapper.updateByPrimaryKeyWithBLOBs(items);
            
        }
    ```

- 在控制层的`ItemsController`中定义更新方法

  - 在控制层的`ItemsController`中定义根据接收的参数调用业务层的更新方法，实现将数据更新到数据库中。

  - ```java
    /*
         * springmvc可以直接接收基本数据类型，包括string，springmvc可以帮你在传参时自动进行
         * 类型转换
         * 转换的前提：controller方法接受的参数名称必须等于
         * 页面上input框的name属性名
         */
        @RequestMapping("/updateitem")
        public String update(Integer id,String name,float price,Date createtime,String detail){
            
            System.out.println(id);
            System.out.println(createtime);
            Items items=new Items();
            items.setId(id);
            items.setName(name);
            items.setPrice(price);
            items.setDetail(detail);
            //items.setCreatetime(new Date());
            items.setCreatetime(createtime);
            //调用业务层对相关
            itemService.update(items);
            return "success";   
        }
    ```

##### 拦截器和过滤器的区别

###### **区别**

```markdown
**
1. 拦截器是基于java的反射机制的，而过滤器是基于函数回调。
2. 拦截器不依赖servlet容器，过滤器依赖servlet容器。
3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
4. 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
5. 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
6. 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。
**
```

###### **拦截器(Interceptor)**

> 拦截器的概念 

Spring 里的拦截器是 **动态拦截Action调用的对象**，它提供了一种机制可以使开发者在一个Action执行的前后执行一段代码，也可以在一个Action执行前阻止其执行，同时也提供了一种可以提取Action中可重用部分代码的方式。在AOP中，拦截器用于在某个方法或者字段被访问之前进行拦截，然后在之前或者之后加入某些操作。

> 拦截器的原理

**拦截器是基于Java的反射机制实现的，拦截的对象只能是实现了接口的类**。所以拦截器只能拦截部分web请求。

> 自定义拦截器的步骤

- 自定义一个实现了 `Interceptor` 接口的类，或者继承抽象类 `AbstractInterceptor`。

  - ```java
    public interface HandlerInterceptor {
            boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
     
            void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView var4) throws Exception;
     
            void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception var4) throws Exception;
    }
    ```

  - ```markdown
    > preHandle 方法：在进入具体的Controller方法之前执行。在这个方法体实现里可以做权限校验，以及其他公共检测（比如 安全校验等）。校验通过返回true，进入下一个拦截器或者直接进入Controller方法。第三个参数handler，表示待执行的Controller方法。
    
    > postHandle方法：在具体Controller方法执行完成后，视图渲染之前执行。主要用途是在视图渲染前做一些通用的准备工作，可以把一些通用的数据放到第四个参数ModelAndView对象里，供视图渲染时使用。
    
    > afterCompletion方法：在视图渲染完成之后执行，主要用于在返回页面之前做一些清理工作。比如最常见的使用场景：在preHandle方法中可以把用户的基本信息放到 ThreadLocal中，以便在同一次请求内的任意方法里都可以取到用户信息，由于spring mvc处理用户请求是用的线程池技术，ThreadLocal是与线程绑定的，如果不及时清理就会导致内存泄漏，这时我们必须在afterCompletion方法中调用ThreadLocal的清理方法，清除掉本次请求中的信息。
    ```

- `Spring` 需要在配置文件中注册这个定义的拦截器。`SpringBoot` 不需要注册，可以定义一个实现了 `WebMvcConfigurer`接口的类来表明自定义拦截器的作用范围。

  - ```java
    @Configuration
    public class WebConfig implements WebMvcConfigurer {
    
        @Autowired
        private SessionInterceptor sessionInterceptor;
    
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry.addInterceptor(sessionInterceptor)
                    .addPathPatterns("/**");
        }
    }
    ```

- `Spring`还需要在使用Action的地方引用上述定义的拦截器，为了方便也可以将拦截器定义为默认的拦截器，这样在不加特殊说明的情况下，所有的Action都被这个拦截器拦截。

> 多个拦截器的执行顺序

先注册的拦截器先输出。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20200812220635390.png)

###### **过滤器(Filter)**

> Filter的概念

Filter也称之为过滤器，它是Servlet技术中最激动人心的技术之一，WEB开发人员通过Filter技术，**对web服务器管理的所有web资源：例如Jsp, Servlet, 静态图片文件或静态html文件等进行拦截，从而实现一些特殊的功能。**例如实现URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能。

> Filter的工作原理

```java
public abstract interface Filter{
    public abstract void init(FilterConfig paramFilterConfig) throws ServletException;
    public abstract void doFilter(ServletRequest paramServletRequest, ServletResponse paramServletResponse, FilterChain 
        paramFilterChain) throws IOException, ServletException;
    public abstract void destroy();
}
```

Filter接口中有一个 `doFilter` 方法，当我们编写好 `Filter`, 并配置对哪个web资源进行拦截后，WEB服务器每次在调用web资源的service方法之前, 都会先调用一下filter的 `doFilter` 方法，因此，在该方法内编写代码可达到如下目的:

1. 调用目标资源之前，让一段代码执行。
2. 是否调用目标资源(即是否让用户访问web资源)。
3. 调用目标资源之后，让一段代码执行。

web服务器在调用 `doFilter` 方法时，会传递一个`filterChain`对象进来，`filterChain`对象是 `filter` 接口中最重要的一个对象，它也提供了一个`doFilter`方法，开发人员可以根据需求决定是否调用此方法，调用该方法，则web服务器就会调用web资源的service方法，即web资源就会被访问, 否则web资源不会被访问。

> Filter的生命周期

- `Filter`的创建

  ```markdown
  Filter的创建和销毁由web服务器负责。 web应用程序启动时，web服务器将创建Filter的实例对象，并调用其init方法，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作，filter对象只会创建一次，init方法也只会执行一次。通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。
  ```

- `Filter`的销毁

  ```markdown
  web容器调用destroy方法销毁Filter。destroy方法在Filter的生命周期中仅执行一次。在destroy方法中，可以释放过滤器使用的资源。
  ```

- `FilterConfig`接口
  　　用户在配置filter时，可以使用`<init-param>`为filter配置一些初始化参数，当web容器实例化Filter对象，调用其init方法时，会把封装了filter初始化参数的filterConfig对象传递进来。因此开发人员在编写filter时，通过filterConfig对象的方法，就可获得：
    　　String getFilterName()：得到filter的名称。
    　　String getInitParameter(String name)： 返回在部署描述中指定名称的初始化参数的值。如果不存在返回null.
    　　Enumeration getInitParameterNames()：返回过滤器的所有初始化参数的名字的枚举集合。
    　　public ServletContext getServletContext()：返回Servlet上下文对象的引用。

###### 监听器(Listener)

现在来说说 `Servlet` 的监听器 `Listener`，它是实现了 `javax.servlet.ServletContextListener` 接口的服务器端程序，它也是**随web应用的启动而启动，只初始化一次**，随 `web` 应用的停止而销毁。主要作用是：做一些初始化的内容添加工作、设置一些基本的内容、比如一些参数或者是一些固定的对象等等。首先来看一下 `ServletContextListener` 接口的源代码:

```java
public abstract interface ServletContextListener extends EventListener{
    public abstract void contextInitialized(ServletContextEvent paramServletContextEvent);
    public abstract void contextDestroyed(ServletContextEvent paramServletContextEvent);
}
```

**示例代码:**

```java
import javax.servlet.ServletContext;   
import javax.servlet.ServletContextEvent;   
import javax.servlet.ServletContextListener;   
import org.apache.commons.dbcp.BasicDataSource;       
/**
 * 现在来说说Servlet的监听器Listener，它是实现了javax.servlet.ServletContextListener 接口的
 * 服务器端程序，它也是随web应用的启动而启动，只初始化一次，随web应用的停止而销毁。主要作用是：做一些初始化
 * 的内容添加工作、设置一些基本的内容、比如一些参数或者是一些固定的对象等等。
 * 
 * 示例代码：使用监听器对数据库连接池DataSource进行初始化
 */ 
public class ListenerTest implements ServletContextListener{     
   // 应用监听器的销毁方法   
   public void contextDestroyed(ServletContextEvent servletContextEvent) {   
        ServletContext servletContext = servletContextEvent.getServletContext();
        // 在整个web应用销毁之前调用，将所有应用空间所设置的内容清空
        servletContext.removeAttribute("dataSource");
        System.out.println("销毁工作完成...");  
   }   
    // 应用监听器的初始化方法   
    public void contextInitialized(ServletContextEvent servletContextEvent) {   
        // 通过这个事件可以获取整个应用的空间   
        // 在整个web应用下面启动的时候做一些初始化的内容添加工作   
        ServletContext servletContext = servletContextEvent.getServletContext();  
        // 设置一些基本的内容；比如一些参数或者是一些固定的对象   
        // 创建DataSource对象，连接池技术 dbcp   
        BasicDataSource basicDataSource = new BasicDataSource(); 
        basicDataSource.setDriverClassName("com.jdbc.Driver"); 
        basicDataSource.setUrl("jdbc:mysqlocalhost:3306/"); 
        basicDataSource.setUsername("root");   
        basicDataSource.setPassword("root");   
        basicDataSource.setMaxActive(10);//最大连接数   
        basicDataSource.setMaxIdle(5);//最大管理数   
        //bds.setMaxWait(maxWait); 最大等待时间   
        // 把 DataSource 放入ServletContext空间中，   
        // 供整个web应用的使用(获取数据库连接)
        servletContext.setAttribute("dataSource", basicDataSource);   
        System.out.println("应用监听器初始化工作完成...");   
        System.out.println("已经创建DataSource...");  
    }   
}
```

##### RESTful API

https://www.cnblogs.com/bigsai/p/14099154.html

**************

#### Spring 事务

##### 1、说一下 Spring 的事务机制

- `Spring` 事务底层是基于数据库事务和 `AOP` 机制的。
- 首先对于使用了 `@Transactional` 注解的 `Bean`，`Spring` 会创建一个代理对象作为 `Bean`。
- 当调⽤代理对象的⽅法时，会先判断该⽅法上是否加了 `@Transactional` 注解
- 如果加了，那么则利⽤事务管理器创建⼀个数据库连接。
- 并且修改数据库连接的 `autocommit` 属性为 `false`，禁⽌此连接的⾃动提交，这是实现 `Spring` 事务⾮常重要的⼀步。
- 然后执⾏当前⽅法，⽅法中会执⾏ `sql`
  - 执⾏完当前⽅法后，如果没有出现异常就直接提交事务
  - 如果出现了异常，并且这个异常是需要回滚的就会回滚事务，否则仍然提交事务
- `Spring` 事务的隔离级别默认对应的就是数据库的隔离级别
- `Spring` 事务的传播机制是 `Spring` 事务⾃⼰实现的，也是 `Spring` 事务中最复杂的
- `Spring` 事务的传播机制是基于数据库连接来做的，⼀个数据库连接⼀个事务，如果传播机制配置为需要新开⼀个事务，那么实际上就是先建⽴⼀个数据库连接，在此新数据库连接上执⾏ `sql`

##### 2、Spring 管理事务的⽅式有⼏种？

- **编程式事务**，在代码中硬编码。**在代码中调用 commit()、rollback()等事务管理相关的方法**。（不推荐使用）
- **声明式事务**，在配置文件中配置（推荐使用）

**声明式事务又分为两种：**

- 基于 `XML` 的声明式事务
- 基于注解的声明式事务，比如 `@Transactional`注解就可以开启事务，这个方法中所有的 `sql` 都会在同一个事务中执行，统一成功或失败。

针对哪些异常回滚事务是可以配置的，可以利用  `@Transactional` 注解中的 `rollbackFor` 属性进行配置，默认情况下会对 `RuntimeException` 和 `Error` 进行回滚。

> spring事务只对运行时异常奏效，编译时异常Exception是不会回滚的。

##### 3、Spring 事务中的隔离级别有哪⼏种?

**TransactionDefinition 接⼝中定义了五个表示隔离级别的常量：**

- **TransactionDefinition.ISOLATION_`DEFAULT`：**使用后端数据库默认的隔离级别，Mysql默认采用的REPEATABLE_READ隔离级别；Oracle 默认采用的 READ_COMMITTED隔离级别.
- **TransactionDefinition.ISOLATION_`READ_UNCOMMITTED`：**最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
- **TransactionDefinition.ISOLATION_`READ_COMMITTED`：**允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
- **TransactionDefinition.ISOLATION_`REPEATABLE_READ`：**对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **TransactionDefinition.ISOLATION_`SERIALIZABLE`：**最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

##### 4、Spring 事务中哪⼏种事务传播⾏为?

```java
/**
方法 A 是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务的执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。
*/
```

一共有七种。

**支持当前事务的情况：**

- **TransactionDefinition.PROPAGATION_`REQUIRED`：**如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。**默认选择**。
- **TransactionDefinition.PROPAGATION_`SUPPORTS`：**如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **TransactionDefinition.PROPAGATION_`MANDATORY`：**如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（`mandatory`：强制性）

**不支持当前事务的情况：**

- **TransactionDefinition.PROPAGATION_`REQUIRES_NEW`：**不管是否存在事务，都创建一个新的事务：如果当前存在事务，则把当前事务挂起，新的执行完毕，继续执行老的事务。
- **TransactionDefinition.PROPAGATION_`NOT_SUPPORTED`：**以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **TransactionDefinition.PROPAGATION_`NEVER`：**以非事务方式运行，如果当前存在事务，则抛出异常。

**其他情况：**

- **TransactionDefinition.PROPAGATION_`NESTED`：**如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_`REQUIRED`。

> 以下是例子+具体说明

- `@Transactional(propagation = Propagation.REQUIRED)`

  - ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.REQUIRED)
    public void testB(){
        B(b1);  //调用B入参b1
        throw Exception;     //发生异常抛出
        B(b2);  //调用B入参b2
    }
    ```

  - **数据库没有插入新的数据，数据库还是保持着执行 `testMain` 方法之前的状态，没有发生改变。**`testMain`上声明了事务，在执行 `testB` 方法时就加入了 `testMain` 的事务（**当前存在事务，则加入这个事务**），在执行 `testB` 方法抛出异常后事务会发生回滚，又 `testMain` 和 `testB` 使用的同一个事务，所以事务回滚后 `testMain` 和 `testB` 中的操作都会回滚，也就使得数据库仍然保持初始状态。

  - ```java
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.REQUIRED)
    public void testB(){
        B(b1);  //调用B入参b1
        throw Exception;     //发生异常抛出
        B(b2);  //调用B入参b2
    }
    ```

  - **数据 `a1` 存储成功，数据 `b1` 和 `b2` 没有存储**。由于testMain没有声明事务，testB有声明事务且传播行为是REQUIRED，所以在执行testB时会自己新建一个事务（**如果当前没有事务，则自己新建一个事务**），testB抛出异常则只有testB中的操作发生了回滚，也就是b1的存储会发生回滚，但a1数据不会回滚，所以最终a1数据存储成功，b1和b2数据没有存储。

- `@Transactional(propagation = Propagation.SUPPORTS)`

  - ```java
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.SUPPORTS)
    public void testB(){
        B(b1);  //调用B入参b1
        throw Exception;     //发生异常抛出
        B(b2);  //调用B入参b2
    
    ```

  - 这种情况下，执行 `testMain` 的最终结果就是，`a1，b1` 存入数据库，`b2` 没有存入数据库。由于`testMain` 没有声明事务，且 `testB` 的事务传播行为是 `SUPPORTS`，所以执行 `testB` 时就是没有事务的（**如果当前没有事务，就以非事务方法执行**），则在 `testB` 抛出异常时也不会发生回滚，所以最终结果就是 `a1` 和 `b1` 存储成功，`b2` 没有存储。

  - 那么当我们在 `testMain` 上声明事务且使用 `REQUIRED` 传播方式的时候，这个时候执行 `testB` 就满足**当前存在事务，则加入当前事务**，在 `testB` 抛出异常时事务就会回滚，最终结果就是 `a1，b1` 和 `b2` 都不会存储到数据库。

- `@Transactional(propagation = Propagation.MANDATORY)`

  - ```java
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.MANDATORY)
    public void testB(){
        B(b1);  //调用B入参b1
        throw Exception;     //发生异常抛出
        B(b2);  //调用B入参b2
    }
    ```

  - 这种情形的执行结果就是 `a1` 存储成功，而 `b1` 和 `b2` 没有存储。`b1` 和 `b2` 没有存储，并不是事务回滚的原因，而是因为 `testMain` 方法没有声明事务，在去执行 `testB` 方法时就直接抛出事务要求的异常（**如果当前事务不存在，则抛出异常**），所以 `testB` 方法里的内容就没有执行。

  - 那么如果在 `testMain` 方法进行事务声明，并且设置为 `REQUIRED`，则执行 `testB` 时就会使用 `testMain`已经开启的事务，遇到异常就正常的回滚了。

- `@Transactional(propagation = Propagation.REQUIRED)`

  - ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
        throw Exception;     //发生异常抛出
    }
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void testB(){
        B(b1);  //调用B入参b1
        B(b2);  //调用B入参b2
    }
    ```

  - 这种情形的执行结果就是 `a1` 没有存储，而 `b1` 和 `b2` 存储成功，因为 `testB` 的事务传播设置为`REQUIRES_NEW` ,所以在执行 `testB` 时会开启一个新的事务，`testMain` 中发生的异常时在 `testMain` 所开启的事务中，所以这个异常不会影响 `testB` 的事务提交，`testMain` 中的事务会发生回滚，所以最终 `a1` 就没有存储，而 `b1` 和 `b2` 就存储成功了。

  - 如果 `testMain` 和 `testB` 都设置为 `REQUIRED`，那么上面的代码执行结果就是所有数据都不会存储，因为 `testMain` 和 `testB` 是在同一个事务下的，所以事务发生回滚时，所有的数据都会回滚

- `@Transactional(propagation = Propagation.REQUIRED)`

  - 可以理解为设置事务传播类型为 `NOT_SUPPORTED` 的方法，在执行时，不论当前是否存在事务，都会以非事务的方式运行。

  - ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.NOT_SUPPORTED)
    public void testB(){
        B(b1);  //调用B入参b1
        throw Exception;     //发生异常抛出
        B(b2);  //调用B入参b2
    }
    ```

  - 该场景的执行结果就是 `a1` 和 `b2` 没有存储，而 `b1` 存储成功。`testMain` 有事务，而 `testB` 不使用事务，所以执行中 `testB` 的存储 `b1` 成功，然后抛出异常，此时 `testMain` 检测到异常事务发生回滚，但是由于 `testB` 不在事务中，所以只有 `testMain` 的存储 `a1` 发生了回滚，最终只有 `b1` 存储成功，而 `a1` 和 `b1` 都没有存储。

- `@Transactional(propagation = Propagation.NEVER)`

  - ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
    }
    @Transactional(propagation = Propagation.NEVER)
    public void testB(){
        B(b1);  //调用B入参b1
        B(b2);  //调用B入参b2
    }
    ```

  - 该场景执行，直接抛出事务异常，且不会有数据存储到数据库。由于 `testMain` 事务传播类型为`REQUIRED`，所以 `testMain` 是运行在事务中，而 `testB` 事务传播类型为 `NEVER`，所以 `testB` 不会执行而是直接抛出事务异常，此时 `testMain` 检测到异常就发生了回滚，所以最终数据库不会有数据存入。

- `@Transactional(propagation = Propagation.NESTED)`

  - 和 `REQUIRES_NEW` 的区别

    - > `REQUIRES_NEW` 是新建一个事务并且新开启的这个事务与原有事务无关，而NESTED则是当前存在事务时（我们把当前事务称之为父事务）会开启一个嵌套事务（称之为一个子事务）。
      > 在 `NESTED` 情况下父事务回滚时，子事务也会回滚，而在 `REQUIRES_NEW` 情况下，原有事务回滚，不会影响新开启的事务。

  - 和 `REQUIRED` 的区别

    - > `REQUIRED` 情况下，调用方存在事务时，则被调用方和调用方使用同一事务，那么被调用方出现异常时，由于共用一个事务，所以无论调用方是否 `catch` 其异常，事务都会回滚，而在 `NESTED` 情况下，被调用方发生异常时，调用方可以 `catch` 其异常，这样只有子事务回滚，父事务不受影响。

  - ```java
    @Transactional(propagation = Propagation.REQUIRED)
    public void testMain(){
        A(a1);  //调用A入参a1
        testB();    //调用testB
        throw Exception;     //发生异常抛出
    }
    @Transactional(propagation = Propagation.NESTED)
    public void testB(){
        B(b1);  //调用B入参b1
        B(b2);  //调用B入参b2
    }
    ```

  - 该场景下，所有数据都不会存入数据库，因为在 `testMain` 发生异常时，父事务回滚则子事务也跟着回滚。

##### 5、@Transactional实现的原理

https://mp.weixin.qq.com/s/gObt3SjeB6T4r-6gX37W0Q

```markdown
**
> Spring事务的本质其实就是数据库对事务的支持，没有数据库的事务支持, spring是无法提供事务功能的。真正的数据库层的事务提交和回滚是通过 `bin log` 或者 `redo log` 实现的.
**
```

Spring事务注解是个典型的Spring AOP的注解。方法上面加上 `@Transactional`，方法就有了事务的能力。底层实现还是==动态代理==，动态代理又分为[JDK](######JDK动态代理)自身和[CGLIB](######Cglib动态代理)。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20181207003257884.png)



在配置 `@Transactional`  注解以后，`spring` 会在 `IOC` 容器创建一个**BeanFactoryTransactionAttributeSourceAdvisor实例，这个实例可以看作是一个切点, 在判断一个bean在初始化过程中是否需要创建代理对象，都需要验证一次BeanFactoryTransactionAttributeSourceAdvisor是否是适用这个bean的切点。如果是，就需要创建代理对象，并且把BeanFactoryTransactionAttributeSourceAdvisor实例注入到代理对象中。**

有一个 `parseTransactionAnnotation` 方法，判断是否需要根据 `@Transactional` 进行代理对象创建的判断过程。`@Transactional` 的作用一个就是标识方法需要被代理，一个就是携带事务管理需要的一些属性信息。

```java
@Override
public TransactionAttribute parseTransactionAnnotation(AnnotatedElement ae) {
    //这里就是分析Method是否被@Transactional注解标注，有的话，不用说BeanFactoryTransactionAttributeSourceAdvisor适配当前bean，进行代理，并且注入切点
    //BeanFactoryTransactionAttributeSourceAdvisor
   AnnotationAttributes attributes = AnnotatedElementUtils.getMergedAnnotationAttributes(ae, Transactional.class);
   if (attributes != null) {
      return parseTransactionAnnotation(attributes);
   }
   else {
      return null;
   }
}
```

事务逻辑的织入是通过`TransactionInterceptor`进行的，而且 `@Transactional` 是一个 `AOP` 注解，我们查看

`TransactionInterceptor` 的 `invoke()` 方法

```java
@Override
public Object invoke(final MethodInvocation invocation) throws Throwable {
	// Work out the target class: may be {@code null}.
	// The TransactionAttributeSource should be passed the target class
	// as well as the method, which may be from an interface.
	Class<?> targetClass = (invocation.getThis() != null ? AopUtils.getTargetClass(invocation.getThis()) : null);

	// Adapt to TransactionAspectSupport's invokeWithinTransaction...
	return invokeWithinTransaction(invocation.getMethod(), targetClass, new InvocationCallback() {
		@Override
		public Object proceedWithInvocation() throws Throwable {
			return invocation.proceed();
		}
	});
}

```

里面有一个 `invokeWithinTransaction` 方法

```java
protected Object invokeWithinTransaction(Method method, Class<?> targetClass, final InvocationCallback invocation)
      throws Throwable {

   // If the transaction attribute is null, the method is non-transactional.
   final TransactionAttribute txAttr = getTransactionAttributeSource().getTransactionAttribute(method, targetClass);
   final PlatformTransactionManager tm = determineTransactionManager(txAttr);
   final String joinpointIdentification = methodIdentification(method, targetClass);

   if (txAttr == null || !(tm instanceof CallbackPreferringPlatformTransactionManager)) {
      // Standard transaction demarcation with getTransaction and commit/rollback calls.
       //开启事务
      TransactionInfo txInfo = createTransactionIfNecessary(tm, txAttr, joinpointIdentification);
      Object retVal = null;
      try {
         // This is an around advice: Invoke the next interceptor in the chain.
         // This will normally result in a target object being invoked.
          //方法调用
         retVal = invocation.proceedWithInvocation();
      }
      catch (Throwable ex) {
         // target invocation exception
   		//回滚事务
         completeTransactionAfterThrowing(txInfo, ex);
         throw ex;
      }
      finally {
         cleanupTransactionInfo(txInfo);
      }
       //提交事务
      commitTransactionAfterReturning(txInfo);
      return retVal;
   }

   else {
      // It's a CallbackPreferringPlatformTransactionManager: pass a TransactionCallback in.
      try {
         Object result = ((CallbackPreferringPlatformTransactionManager) tm).execute(txAttr,
               new TransactionCallback<Object>() {
                  @Override
                  public Object doInTransaction(TransactionStatus status) {
                     TransactionInfo txInfo = prepareTransactionInfo(tm, txAttr, joinpointIdentification, status);
                     try {
                        return invocation.proceedWithInvocation();
                     }
                     catch (Throwable ex) {
                        if (txAttr.rollbackOn(ex)) {
                           // A RuntimeException: will lead to a rollback.
                           if (ex instanceof RuntimeException) {
                              throw (RuntimeException) ex;
                           }
                           else {
                              throw new ThrowableHolderException(ex);
                           }
                        }
                        else {
                           // A normal return value: will lead to a commit.
                           return new ThrowableHolder(ex);
                        }
                     }
                     finally {
                        cleanupTransactionInfo(txInfo);
                     }
                  }
               });

         // Check result: It might indicate a Throwable to rethrow.
         if (result instanceof ThrowableHolder) {
            throw ((ThrowableHolder) result).getThrowable();
         }
         else {
            return result;
         }
      }
      catch (ThrowableHolderException ex) {
         throw ex.getCause();
      }
   }
}

```

底层实现还是数据库的事务。

##### 6、Spring事务什么时候会失效？

`spring` 事务的原理是 `AOP`，进行了切面增强，那么失效的根本原因是**这个AOP不起作用了**！常见情况有如下几种：

1. 发生自调用，类里面使用 `this` 调用本类的方法，此时这个 `this` 对象不是代理类，而是UserService本身。

2. 方法不是 `public` 的

   ```java
   @Transactional 只能用于 public 方法上，否则事务不会生效，如果用在非 public 方法上，可以开启AspectJ代理模式。
   ```

3. 数据库不支持事务

4. 类没有被 `spring` 管理

5. 异常被吃掉(`catch`)，事务不会回滚（或者抛出的异常没有被定义，默认为 `RuntimeException`）

***********

#### Spring中后置处理器的作⽤

​		Spring中的后置处理器分为 `BeanFactory` 后置处理器和 `Bean` 后置处理器，它们是 Spring 底层源码架构设计中⾮常重要的⼀种机制，同时开发者也可以利⽤这两种后置处理器来进⾏扩展。``BeanFactory` 后置处理器表示针对 `BeanFactory` 的处理器, Spring 启动过程中，会先创建出 `BeanFactory` 实例，然后利⽤ `BeanFactory` 处理器来加⼯ `BeanFactory`，⽐如 Spring 的扫描就是基于 `BeanFactory` 后置处理器来实现的，⽽ `Bean` 后置处理器也类似，Spring在创建⼀个 `Bean`的过程中，⾸先会实例化得到⼀个对象，然后再利⽤ `Bean` 后置处理器来对该实例对象进⾏加⼯，⽐如我们常说的依赖注⼊就是基于⼀个 `Bean` 后置处理器来实现的，通过该 `Bean` 后置处理器来给实例对象中加了 `@Autowired` 注解的属性⾃动赋值，还⽐如我们常说的 `AOP` ，也是利⽤⼀个 `Bean` 后置处理器来实现的，基于原实例对象，判断是否需要进⾏ `AOP`，如果需要，那么就基于原实例对象进⾏动态代理，⽣成⼀个代理对象。

********************

#### JPA

> JPA顾名思义就是Java Persistence API的意思，中文名**Java持久层API**，是JDK 5.0注解或XML描述对象-关系表的映射关系，并将运行期的实体对象持久化。

##### 如何使⽤JPA在数据库中⾮持久化⼀个字段？

![image-20210421141607616](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421141607616.png)

![image-20210421141622135](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210421141622135.png)



#### 注解

##### @RestController 与 @Controller

###### `@Controller` 返回⼀个⻚⾯

单独使用`@Controller`不加`@ResponseBody`的话一般使用在要返回一个视图的情况，这种情况属于比较传统的Spring MVC的应用，对应于前后端不分离的情况。

###### `@RestController` 返回JSON 或XML 形式数据

但`@RestController` 只返回对象，对象数据直接以JSON或XML形式写入HTTP响应（Response）中，这种情况属于RESTful Web服务，这也是目前日常开发所接触的最常用的情况（前后端分离）。

`@Controller`+`@ResponseBody `返回JSON或XML形式数据

如果你需要在Spring4之前开发RESTful Web服务的话，你需要使用`@Controller `并结合`@ResponseBody `注解，也就是说`@Controller`+`@ResponseBody`=`@RestController`（Spring4之后新加的注解）。

> `@ResponseBody`注解的作用是将`@Controller`的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到HTTP响应（Response）对象的body中，通常用来返回JSON或者XML数据，返回JSON数据的情况比较多。



##### @Bean和@Component

- 二者目的是相同的，即都是将 bean 注册到 Spring容器中。
- 不同点：
  - 作用对象不同：`@Component` 注解作用于类，而 `@Bean` 注解作用于方法。
  - `@Component` 通常是通过路径扫描来自动侦测以及自动装配到 `Spring` 容器中(我们可以使用 `@ComponentScan` 注解定义要扫描的路径，从中找出标识了需要装配的类并且自动装配到 `Spring` 的 `bean` 容器中)。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 `bean`，`@Bean` 告诉了 `Spring` 这是某个类的实例，当我们需要用它的时候还给我。

##### @Component、@Repository、@Service、@Controller区别

- `@Repository` 注解：用于标注持久层数据访问组件，即DAO组件
- `@Service` 注解：用于标注业务层组件
- `@Controller` 注解：用于标注控制层组件
- `@Component` 注解：泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。



##### @Conditional

`@Conditional` 根据满足某一个特定条件创建一个特定的 Bean。就是根据特定条件来控制 Bean 的创建行为，这样我们可以利用这个特性进行一些自动的配置。

**使用方法**

实现 `Condition` 接口，并重写其 `matches()` 方法来构造判断条件。

**例子：**

若在 `Windows` 系统下运行程序，则输出列表命令为 `dir` ，若在 `Linux` 系统下运行程序，则输出列表命令为 `ls`。

- 判断 Window 条件

  ```java
  public class WindowsCondition implements Condition {
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
          return context.getEnvironment().getProperty("os.name").contains("Windows");
      }
  }
  ```

- 判断 Linux 条件

  ```java
  public class LinuxCondition implements Condition {
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
          return context.getEnvironment().getProperty("os.name").contains("Linux ");
      }
  }
  ```

- 不同系统下的 Bean 类

  - 接口

    ```java
    public interface ListService {
        String showListCmd();
    }
    ```

  - Window 下所要创建的 Bean 类

    ```java
    public class WindowsListService implements ListService{
        @Override
        public String showListCmd() {
            return "dir";
        }
    }
    ```

  - Linux 下所要创建的 Bean 类

    ```java
    public class LinuxListService implements ListService {
        @Override
        public String showListCmd() {
            return "ls";
        }
    }
    ```

- 配置类

  ```java
  @Configuration
  public class ConditionConfig {
  
      @Bean
      @Conditional(WindowsCondition.class)
      public ListService windowsListService(){
          return new WindowsListService();
      }
  
      @Bean
      @Conditional(LinuxCondition.class)
      public ListService linuxListService(){
          return new LinuxListService();
      }
  
  }
  ```

- 测试类

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest(classes = Ch522Application.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
  public class TaskTest {
  
      @Autowired
      private ListService listService;
  
      @Test
      public void conditionalTest() {
          System.out.println(listService.showListCmd());
      }
  }
  ```

- 测试结果

  ```java
  dir
  ```

##### @Autowired和@Resource的区别

**相同点：**

两者都可以写在字段和 `setter` 方法上。两者如果都写在字段上，那么就不需要再写 `setter`方法。

**不同点：**

- `@Autowired` 为Spring提供的注解，需要导入包 `org.springframework.beans.factory.annotation.Autowired`；`@Autowired` 注解是按照类型（`byType`）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许 `null` 值，可以设置它的`required` 属性为 `false`。如果我们想使用按照名称（byName）来装配，可以结合 `@Qualifier` 注解一起使用。

- `@Resource` 默认按照 `ByName` 自动注入，由J2EE提供，需要导入包 `javax.annotation.Resource` 。`@Resource` 有两个重要的属性：`name` 和 `type`，而Spring将 `@Resource` 注解的 name 属性解析为bean的名字，而type属性则解析为bean的类型。所以，如果使用name属性，则使用 `byName` 的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不制定name也不制定type属性，这时将通过反射机制使用 `byName` 自动注入策略。**注：最好是将`@Resource`放在setter方法上，因为这样更符合面向对象的思想，通过set、get去操作属性，而不是直接去操作属性**

  - `@Resource `装配顺序：

    ①如果同时指定了 `name` 和 `type` ，则从 `Spring` 上下文中找到唯一匹配的 `bean` 进行装配，找不到则抛出异常。

    ②如果指定了 `name` ，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。

    ③如果指定了 `type`，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。

    ④如果既没有指定 `name` ，又没有指定 `type` ，则自动按照 `byName` 方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。

##### @ControllerAdvice

###### 介绍

```	java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface ControllerAdvice {

	@AliasFor("basePackages")
	String[] value() default {};

	@AliasFor("value")
	String[] basePackages() default {};

	Class<?>[] basePackageClasses() default {};

	Class<?>[] assignableTypes() default {};

	Class<? extends Annotation>[] annotations() default {};
}

```

> 首先，`ControllerAdvice`本质上是一个`Component`，因此也会被当成组建扫描，一视同仁。
>
> 然后，我们来看一下此类的注释：
>
> 这个类是为那些声明了（`@ExceptionHandle`、`@InitBinder` 或 `@ModelAttribute` 注解修饰的）方法的类而提供的专业化的 `@Component` , 以供多个 `Controller` 类所共享。
>
> 说白了，就是 AOP 思想的一种实现，你告诉我需要拦截规则，我帮你把他们拦下来，具体你想做更细致的拦截筛选和拦截之后的处理，你自己通过 `@ExceptionHandler` 、`@InitBinder` 或 `@ModelAttribute` 这三个注解以及被其注解的方法来自定义。
>
> 初定义拦截规则：
>
> `ControllerAdvice` 提供了多种指定 `Advice` 规则的定义方式，默认什么都不写，则是 `Advice` 所有`Controller` ，当然你也可以通过下列的方式指定规则：
>
> - 比如对于 `String[] value() default {}` , 写成 `@ControllerAdvice("org.my.pkg")` 或者 `@ControllerAdvice(basePackages="org.my.pkg")`, 则匹配 `org.my.pkg` 包及其子包下的所有`Controller`
> - 用数组的形式指定，如：`@ControllerAdvice(basePackages={"org.my.pkg", "org.my.other.pkg"})` 
> - 通过指定注解来匹配，比如我自定了一个 `@CustomAnnotation` 注解，我想匹配所有被这个注解修饰的 `Controller`, 可以这么写：`@ControllerAdvice（annotations={CustomAnnotation.class})`
> - ......

###### 用法

- 与`@ExceptionHandler` 实现全局异常处理

  - 对于使用了`@ExceptionHandler`注的方法，捕获 `Controller` 中抛出的不同类型的异常，从而达到异常全局处理的目的；

  - 接收 `Throwable` 类作为参数，我们知道 `Throwable` 是所有异常的父类，所以说，可以自行指定所有异常。

    - 比如在 **方法**上加`@ExceptionHandler(IllegalArgumentException.class)`，则表明此方法处理`IllegalArgumentException` 类型的异常，如果参数为空，将默认为方法参数列表中列出的任何异常（方法抛出什么异常都接得住）。

    - 处理所有`IllegalArgumentException`异常，域中加入错误信息`errorMessage` 并返回错误页面`error`

    - ```java
      @ControllerAdvice
      public class GlobalExceptionHandler {
          @ExceptionHandler(IllegalArgumentException.class)
          public ModelAndView handleException(IllegalArgumentException e){
              ModelAndView modelAndView = new ModelAndView("error");
              modelAndView.addObject("errorMessage", "参数不符合规范!");
              return modelAndView;
          }
      }
      ```

- 与 `@ModelAttribute` 预设全局数据

  - 先看一下 注解`ModelAttribute`的源码

    - ```java
      /**
       * Annotation that binds a method parameter or method return value
       * to a named model attribute, exposed to a web view. Supported
       * for controller classes with {@link RequestMapping @RequestMapping}
       * methods.
       * 此注解用于绑定一个方法参数或者返回值到一个被命名的model属性中，暴露给web视图。支持在
       * 在Controller类中注有@RequestMapping的方法使用（这里有点拗口，不过结合下面的使用介绍
       * 你就会明白的)
       */
      @Target({ElementType.PARAMETER, ElementType.METHOD})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      public @interface ModelAttribute {
      
      	@AliasFor("name")
      	String value() default "";
      
      	@AliasFor("value")
      	String name() default "";
      
      	boolean binding() default true;
      }
      
      ```

  - 实际上这个注解的作用就是，允许你往 `Model` 中注入全局属性（可以供所有Controller中注有@Request Mapping的方法使用），`value` 和 `name` 用于指定 属性的 `key` ，`binding` 表示是否绑定，默认为 `true`。

  - 全局参数绑定

    - 方式一：

      - ```java
        @ControllerAdvice
        public class MyGlobalHandler {
            @ModelAttribute
            public void presetParam(Model model){
                model.addAttribute("globalAttr","this is a global attribute");
            }
        }
        ```

    - 方式二：

      - ```java
        @ControllerAdvice
        public class MyGlobalHandler {
        
            @ModelAttribute()
            public Map<String, String> presetParam(){
                Map<String, String> map = new HashMap<String, String>();
                map.put("key1", "value1");
                map.put("key2", "value2");
                map.put("key3", "value3");
                return map;
            }
        }
        ```

  - 全局参数使用

    - ```java
      @RestController
      public class AdviceController {
      
          @GetMapping("methodOne")
          public String methodOne(Model model){ 
              Map<String, Object> modelMap = model.asMap();
              return (String)modelMap.get("globalAttr");
          }
      
          @GetMapping("methodTwo")
          public String methodTwo(@ModelAttribute("globalAttr") String globalAttr){
              return globalAttr;
          }
      
          @GetMapping("methodThree")
          public String methodThree(ModelMap modelMap) {
              return (String) modelMap.get("globalAttr");
          }
      }
      ```

      

- 与 `@InitBinder` 预处理请求参数

  - `@InitBinder` 的源码

    - ```java
      /**
       * Annotation that identifies methods which initialize the
       * {@link org.springframework.web.bind.WebDataBinder} which
       * will be used for populating command and form object arguments
       * of annotated handler methods.
       * 粗略翻译：此注解用于标记那些 (初始化[用于组装命令和表单对象参数的]WebDataBinder)的方法。
       * 原谅我的英语水平，翻译起来太拗口了，从句太多就用‘()、[]’分割一下便于阅读
       *
       * Init-binder methods must not have a return value; they are usually
       * declared as {@code void}.
       * 粗略翻译：初始化绑定的方法禁止有返回值，他们通常声明为 'void'
       *
       * <p>Typical arguments are {@link org.springframework.web.bind.WebDataBinder}
       * in combination with {@link org.springframework.web.context.request.WebRequest}
       * or {@link java.util.Locale}, allowing to register context-specific editors.
       * 粗略翻译：典型的参数是`WebDataBinder`，结合`WebRequest`或`Locale`使用，允许注册特定于上下文的编辑 
       * 器。
       * 
       * 总结如下：
       *  1. @InitBinder 标识的方法的参数通常是 WebDataBinder。
       *  2. @InitBinder 标识的方法,可以对 WebDataBinder 进行初始化。WebDataBinder 是 DataBinder 的一
       * 		           个子类,用于完成由表单字段到 JavaBean 属性的绑定。
       *  3. @InitBinder 标识的方法不能有返回值,必须声明为void。
       */
      @Target({ElementType.METHOD})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      public @interface InitBinder {
      	/**
      	 * The names of command/form attributes and/or request parameters
      	 * that this init-binder method is supposed to apply to.
      	 * <p>Default is to apply to all command/form attributes and all request parameters
      	 * processed by the annotated handler class. Specifying model attribute names or
      	 * request parameter names here restricts the init-binder method to those specific
      	 * attributes/parameters, with different init-binder methods typically applying to
      	 * different groups of attributes or parameters.
      	 * 粗略翻译：此init-binder方法应该应用于的命令/表单属性和/或请求参数的名称。默认是应用于所有命	   		* 令/表单属性和所有由带注释的处理类处理的请求参数。这里指定模型属性名或请求参数名将init-binder		 * 方法限制为那些特定的属性/参数，不同的init-binder方法通常应用于不同的属性或参数组。
      	 * 我至己都理解不太理解这说的是啥呀，我们还是看例子吧
      	 */
      	String[] value() default {};
      }
      
      ```

  - 用途：

    - 参数处理

      - ```java
        /**
        *可以实现全局的实现对 Controller 中RequestMapping标识的方法中的所有 String 和Date类型的参数都会被作相应的处理。
        */
        
        @ControllerAdvice
        public class MyGlobalHandler {
        
            @InitBinder
            public void processParam(WebDataBinder dataBinder){
        
                /*
                 * 创建一个字符串微调编辑器
                 * 参数{boolean emptyAsNull}: 是否把空字符串("")视为 null
                 */
                StringTrimmerEditor trimmerEditor = new StringTrimmerEditor(true);
        
                /*
                 * 注册自定义编辑器
                 * 接受两个参数{Class<?> requiredType, PropertyEditor propertyEditor}
                 * requiredType：所需处理的类型
                 * propertyEditor：属性编辑器，StringTrimmerEditor就是 propertyEditor的一个子类
                 */
                dataBinder.registerCustomEditor(String.class, trimmerEditor);
                
                //同上，这里就不再一步一步讲解了
                binder.registerCustomEditor(Date.class,
                        new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"), false));
            }
        }
        
        ```

      - 测试：

      - ```java
        @RestController
        public class BinderTestController {
        
            @GetMapping("processParam")
            public Map<String, Object> test(String str, Date date) throws Exception {
                Map<String, Object> map = new HashMap<String, Object>();
                map.put("str", str);
                map.put("data", date);
                return  map;
            }
        }
        
        ```

      - ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20190923173648663.png)

      - 可以看出，`str` 和 `date` 这两个参数在进入 `Controller` 的test的方法之前已经被处理了，`str` 被去掉了两边的空格(`%20` 在Http url 中是空格的意思)，`String`类型的 `1997-1-10`被转换成了`Date`类型。

    - 参数绑定

      - ```java
        class Person {
        
            private String name;
            private Integer age;
            // omitted getters and setters.
        }
        
        class Book {
        
            private String name;
            private Double price;
            // omitted getters and setters.
        }
        
        @RestController
        public class BinderTestController {
        
            @PostMapping("bindParam")
            public void test(Person person, Book book) throws Exception {
                System.out.println(person);
                System.out.println(book);
            }
        }
        ```

      - `Person`类和 `Book` 类都有 `name`属性，那么这个时候就会出先问题，它可没有那么只能区分哪个`name`是哪个类的。因此 `@InitBinder`就派上用场了：

      - ```java'
        @ControllerAdvice
        public class MyGlobalHandler {
        
        	/*
             * @InitBinder("person") 对应找到@RequstMapping标识的方法参数中
             * 找参数名为person的参数。
             * 在进行参数绑定的时候，以‘p.’开头的都绑定到名为person的参数中。
             */
            @InitBinder("person")
            public void BindPerson(WebDataBinder dataBinder){
                dataBinder.setFieldDefaultPrefix("p.");
            }
        
            @InitBinder("book")
            public void BindBook(WebDataBinder dataBinder){
                dataBinder.setFieldDefaultPrefix("b.");
            }
        }
        
        ```

      - 因此，传入的同名信息就能对应绑定到相应的实体类中：`p.name -> Person.name b.name -> Book.name`。

      - 如果 `@InitBinder("value")` 中的 `value` 值和 `Controller` 中 `@RequestMapping()` 标识的方法的参数名不匹配，则就会产生绑定失败的后果：如：`@InitBinder(“p”)、@InitBinder(“b”)     public void test(Person person, Book book)`。

      - 解决方法：

        - > 上述情况就会出现绑定失败，有两种解决办法
          >
          > 第一中：统一名称，要么全叫 `p`，要么全叫 `person` ，只要相同就行。
          >
          > 第二种：方法参数加 `@ModelAttribute`，有点类似 `@RequestParam`
          >
          > `@InitBinder(“p”)`、`@InitBinder(“b”)`
          >
          > `public void test(@ModelAttribute(“p”) Person person, @ModelAttribute(“b”) Book book)`

##### @EnableAsync与@Async

- `@Async`是 spring 为了方便开发人员进行异步调用的出现的，在方法上加入这个注解，`spring` 会从线程池中获取一个新的线程来执行方法，实现异步调用。
- `@EnableAsync`表示开启对异步任务的支持，可以放在 `springboot` 的启动类上，也可以放在自定义线程池的配置类上。

###### 使用方法

1. 在 springboot 项目中，直接在启动类上加上 `@EnableAsync` ，然后在 service 层的方法上对于需要异步调用的方法加上 `@Async` 。

   > #### 没有自定义线程池 `@Async` 默认的线程池是 `SimpleAsyncTaskExecutor`

2. 自定义线程池使用

   1. 新建一个线程池配置类，`@EnableAsync` 在配置类上加，不用在启动类上加也行，可以配置不同的线程池，用 `bean` 的 ` name` 做区分。

      ```java
      @Configuration
      @EnableAsync
      public class ThreadPoolTaskConfig {
      	
      	private static final int corePoolSize = 10;       		// 核心线程数（默认线程数）
      	private static final int maxPoolSize = 100;			    // 最大线程数
      	private static final int keepAliveTime = 10;			// 允许线程空闲时间（单位：默认为秒）
      	private static final int queueCapacity = 200;			// 缓冲队列数
      	private static final String threadNamePrefix = "Async-Service-"; // 线程池名前缀
      	
      	@Bean("taskExecutor") // bean的名称，默认为首字母小写的方法名
      	public ThreadPoolTaskExecutor getAsyncExecutor(){
      		ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
      		executor.setCorePoolSize(corePoolSize);   
      		executor.setMaxPoolSize(maxPoolSize);
      		executor.setQueueCapacity(queueCapacity);
      		executor.setKeepAliveSeconds(keepAliveTime);
      		executor.setThreadNamePrefix(threadNamePrefix);
      		
      		// 线程池对拒绝任务的处理策略
      		executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
      		// 初始化
      		executor.initialize();
      		return executor;
      	}
      }
      ```
   
2. `@Async` 的使用一样是在 service 层的方法上加，如果配置了多个线程池，可以用 `@Async("name")`，那么表示线程池的 `@Bean` 的 `name`，来指定用哪个线程池处理。
   
   1. 假如只配置了一个线程池，直接用 `@Async` 就会用自定义的线程池执行
   
   2. 假如配置了多个线程池，用 `@Async` 没指定用哪个线程池，会用默认的 `SimpleAsyncTaskExecutor`来处理。
   
   3. ```java
         @Service
         public class testAsyncService {
         	Logger log = LoggerFactory.getLogger(testAsyncService.class);
          
         	// 发送提醒短信 1
         	@Async("taskExecutor")
         	public void service1() throws InterruptedException {
         		log.info("--------start-service1------------");
         		Thread.sleep(5000); // 模拟耗时
         	    log.info("--------end-service1------------");
         	}
         	
         	// 发送提醒短信 2
         	@Async("taskExecutor")
         	public void service2() throws InterruptedException {
         		
         		log.info("--------start-service2------------");
         		Thread.sleep(2000); // 模拟耗时
         	    log.info("--------end-service2------------");
          
         	}
         
         ```

###### 失效原因

`@Async` 失效原因如下：
一、异步方法使用static修饰
二、异步类没有使用@Component注解（或其他注解）导致spring无法扫描到异步类
三、异步方法不能与异步方法在同一个类中
四、类中需要使用@Autowired或@Resource等注解自动注入，不能自己手动new对象
六、在Async 方法上标注@Transactional是没用的。 在Async 方法调用的方法上标注@Transactional 有效。
七、调用被@Async标记的方法的调用者不能和被调用的方法在同一类中不然不会起作用！！！！！！！
八、使用@Async时要求是不能有返回值的不然会报错的 因为异步要求是不关心结果的

> 被 `@Async` 注解修饰的方法，返回值只能是 void 或者 Future。

###### 源码分析

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Async {
	String value() default "";
}
```

- 被 `@Async` 注解修饰的方法，返回值只能是 void 或者 Future。
- 核心线程数配置是 8 ，队列长度应该是 `Integer.MAX_VALUE`。

##### `@PathVariable`和 `@RequestParam`

- `@PathVariable` 的作用是在参数里用来提取 `url` 中的请求参数

  - ```java
    请求url例如:127.0.0.1:8080/user/2 
     
    @GetMapping("/user/{id}")
    BrandDTO findBrandById(@PathVariable("id") Long id);
    ```

- `@RequestParam` 的作用也是提取请求中的参数

  - ```java
    请求url例如:127.0.0.1:8080/user?id=2
    
    @GetMapping("/user")
    SpuDetailDTO findSpuDetailById(@RequestParam("id") Long id);
    ```

##### `@RequestBody`

- 作用：接收**前端传递给后端**的 **json字符串中的数据**(请求体中的数据的)；

- 要求：

  - `GET` 方式无请求体，所以使用 `@RequestBody` 接收数据时，前端不能使用 `GET` 方式提交数据，而是用 `POST` 方式进行提交。
  - 在后端的同一个接收方法里，`@RequestBody` 与 `@RequestParam()` 可以同时使用，`@RequestBody` 最多只能有一个，而 `@RequestParam()` 可以有多个
    - 一个请求只有一个 `@RequestBody`；
    - 一个请求可以有多个 `@RequestParam`。

- 源码分析：

  - ```java
    /**
     * Annotation indicating a method parameter should be bound to the body of the web request.
     * The body of the request is passed through an {@link HttpMessageConverter} to resolve the
     * method argument depending on the content type of the request. Optionally, automatic
     * validation can be applied by annotating the argument with {@code @Valid}.
     */
    @Target(ElementType.PARAMETER)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface RequestBody {
        /**
         * Whether body content is required.
         * <p>Default is {@code true}, leading to an exception thrown in case
         * there is no body content. Switch this to {@code false} if you prefer
         * {@code null} to be passed when the body content is {@code null}.
         * @since 3.2
         */
        boolean required() default true;
    }
    ```

  - 

**************


#### Spring、SpringMVC、Spring Boot的区别

- Spring 是一个 IOC 容器，用来管理Bean，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供AOP机制弥补面向对象的代码重复问题，更方便将不同类不同方法中的共同处理成切面、自动注入给方法执行，比如日志、异常等。
- SpringMVC 是 Spring 对 web 框架的一个解决方法，提供了一个总的前端控制器 `Servlet` ，用来接收请求，然后定义了一套路由策略（url到handle的映射）及适配执行handle，将handle的结构使用视图解析技术生成视图展示给前端
- Spring Boot是Spring提供的一个快速开发工具包，让程序员可以更方便、更快速地开发Spring+SpringMVC的应用，简化了配置（约定大于配置），整合了一系列的解决方案（starter机制）、redis、mongodb、es，可以开箱即用。

#### Maven的生命周期

Maven有三套相互独立的生命周期，分别是clean、default和site。每个生命周期包含一些阶段(phase)，阶段是有顺序的，后面的阶段依赖于前面的阶段。

1. **clean生命周期**：清理项目，包含三个phase。
   1. pre-clean：执行清理前需要完成的工作
   2. clean：清理上一次构建生成的文件
   3. post-clean：执行清理后需要完成的工作
2. **default生命周期**：构建项目，重要的phase如下。
   1. validate：验证工程是否正确，所有需要的资源是否可用。
   2. compile：编译项目的源代码。  
   3. test：使用合适的单元测试框架来测试已编译的源代码。这些测试不需要已打包和布署。
   4. Package：把已编译的代码打包成可发布的格式，比如jar。
   5. integration-test：如有需要，将包处理和发布到一个能够进行集成测试的环境。
   6. verify：运行所有检查，验证包是否有效且达到质量标准。
   7. install：把包安装到maven本地仓库，可以被其他工程作为依赖来使用。
   8. Deploy：在集成或者发布环境下执行，将最终版本的包拷贝到远程的repository，使得其他的开发者或者工程可以共享。
3. **site生命周期**：建立和发布项目站点，phase如下
   1. pre-site：生成项目站点之前需要完成的工作
   2. site：生成项目站点文档
   3. post-site：生成项目站点之后需要完成的工作
   4. site-deploy：将项目站点发布到服务器



