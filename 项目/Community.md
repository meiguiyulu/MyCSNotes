# Community

## 项目流程

```markdown
# 项目流程及功能实现
**
1. http://localhost:8080/ ---> index.html
	1.1 登录方式: Github、Gitee第三方登录。使用OAuth 2.0协议。详细见下面。
	1.2 登录的时候用到了策略模式。
	1.3 登陆的时候，首先使用第三方登录来获得用户的信息。根据类型(Github或者Gitee)、AccountId来获得是否是新的用户。
	   1.3.1 每次通过UUID.randomUUID().toString()来生成一个唯一的字符串; 通过response.addCookie(new Cookie("token", token)) 存到cookie中。
	   1.3.2 拦截器: 通过session来拦截。首先从cookie中判断token是否存在, 根据token的值从数据库中查找user, 如果能查到, 将user的信息存到session中: request.getSession().setAttribute("user", user)。
	   1.3.3 保持登录状态: 判断session.user是否是空。
     1.4 进阶功能: 将登陆信息存储到Redis缓存中
2. http://localhost:8080/callback/{type} ----> AuthorizeController
    2.1 根据type的值来判断登录的方式: Github; Gitee。
    2.2 根据不同的登陆方式调用OAuth协议来获得登录用户的信息：name, id, bio, avatar_url等。
    2.3 id对应User数据库表中的accountId。
       2.3.1 若User表中存在该id则表明是旧的用户;
       2.3.2 若不存在则表明是新用户, 向数据库表中插入用户信息。
3. 热门标签功能
	3.1 使用@Scheduled(fixedRate = 1000 * 60 * 60 * 3)设置3小时运行一次。
	3.2 热门标签的判断方法: 热度判断方式很简单, 线性判断方式----> 热度值+5+评论数
	3.3 热门标签排序使用的是优先队列(Redis用的是zset), 容量为5.
4. 分页功能：
	4.1 MySQL select xxx limit offset, size
	4.2 offset = size * (page - 1);
5. 相关问题
	5.1 根据标签来查询  两个问题的标签相同, 可视为相关问题
	5.2 select * from question where id != #{id} and tag regexp #{tag}用到了正则表达式
	5.3 对查询功能进行了改进: 使用elasticSearch
6. 准备实现功能:
	6.1 日志功能: AOP实现
	   6.1.1 定义一个切面, 也就是要实现的额外功能, 可以使用@Aspect注解
	   6.1.2 在声明为Aspect的的类中, 定义切点: @Pointcut(....)
	   6.1.3 定义对应的额外功能的方法, 并在方法上使用@Before、@After、@Around等注解, 并在其中指定pointcut, 这些方法传入的参数为JointPoint, 在方法中实现原有方法的调用、要加入的额外功能等等信息。
```



## 登录方式

### 1. Github登录

#### 1.1 登录流程

- 点击登录按钮后，会向 `Github` 发送 `get` 请求。这时会跳转到`GitHub`登入页面，授权后`GitHub`会返回一个携带 `code`的 `redirect-uri`。 `https://redirect_url?code=XXXXXX`
- 系统处理`https://redirect_url?code=XXXXXX`请求，获取`code`值，向`https://github.com/login/oauth/access_token`发起 `post `请求，请求参数为`client_id`,`client_secret`和`code`。
- `Github` 会返回一个 `access_token`。大概的样子：`access_token=d0686dc49a22d64e77402db072b719f510f22421&scope=user&token_type=bearer`。
- 只需要向`https://api.github.com/user?access_token=xxx`发送`GET`请求，即可获取到登录用户的基本信息。

#### 1.2 OAuth协议

`OAuth`协议旨在为用户资源的授权访问提供一个安全，开放的标准。平台商通过`OAuth`协议，提示用户对第三方软件厂商(ISV)进行授权，使得第三方软件厂商能够使用平台商的部分数据，对外提供服务。

它的最终目的是为第三方应用颁发一个有时效性的令牌 token。使得第三方应用能够通过该令牌获取相关的资源。

##### 角色

首先需要介绍的是 OAuth 2.0 协议中的一些角色，整个授权协议的流程都将围绕着这些角色：

- `resource owner`，资源所有者，能够允许访问受保护资源的实体。如果是个人，被称为 `end-user`。
- `resource server`，资源服务器，托管受保护资源的服务器。
- `client`，客户端，使用资源所有者的授权代表资源所有者发起对受保护资源的请求的应用程序。如：web网站，移动应用等。
- `authorization server`，授权服务器，能够向客户端颁发令牌。
- `user-agent`，用户代理，帮助资源所有者与客户端沟通的工具，一般为 web 浏览器，移动 APP 等。



假如我想要在 我的论坛上 用 `github.com` 的账号登录。那么 我的论坛相对于 `Github `就是一个客户端。而我们用什么操作的呢？浏览器，这就是一个用户代理。当从 `Github `的授权服务器获得 token 后，论坛系统 是需要请求 `Github `账号信息的，从哪请求？从 `Github `的资源服务器。

#### 1.3 协议流程

![oauth2-roles](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/oauth2-roles.jpg)

- $A$ `Client `请求 `Resource Owner` 的授权。授权请求可以直接向 `Resource Owner` 请求，也可以通过 `Authorization Server` 间接的进行。
- $B$ `Client `获得授权许可。
- $D$ `Client `向 `Authorization Server` 请求访问令牌。
- $C$ `Authorization Server` 验证授权许可，如果有效则颁发访问令牌。
- $E$ `Client `通过访问令牌从 `Resource Server` 请求受保护资源。
- $F$ `Resource Server` 验证访问令牌，有效则响应请求。

```markdown
     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
```

##### 授权

一个客户端想要获得授权，就需要先到服务商那注册你的应用。一般需要你提供下面这些信息：

- 应用名称
- 应用网站
- 重定向 `URI `或回调 `URL`（`redirect_uri`）

重定向 URI 是服务商在用户授权（或拒绝）应用程序之后重定向用户的地址，因此也是用于处理授权代码或访问令牌的应用程序的一部分。在你注册成功之后，你会从服务商那获取到你的应用相关的信息：

- 客户端标识 `client_id`
- 客户端密钥 `client_secret`

`client_id` 用来表识客户端（公开），`client_secret` 用来验证客户端身份（保密）。

##### 授权类型

OAuth 2.0 列举了四种授权类型，分别用于不同的场景：

- `Authorization Code`（授权码 code）：服务器与客户端配合使用。
- `Implicit`（隐式 token）：用于移动应用程序或 Web 应用程序（在用户设备上运行的应用程序）。
- `Resource Owner Password Credentials`（资源所有者密码凭证 password）：资源所有者和客户端之间具有高度信任时（例如，客户端是设备的操作系统的一部分，或者是一个高度特权应用程序），以及当其他授权许可类型（例如授权码）不可用时被使用。
- `Client Credentials`（客户端证书 client_credentials）：当客户端代表自己表演（客户端也是资源所有者）或者基于与授权服务器事先商定的授权请求对受保护资源的访问权限时，客户端凭据被用作为授权许可。

下面来具体说说这四种授权。**注意重定向一定要用 `302`**。

具体见[10 分钟理解什么是OAuth 2.0 协议](https://deepzz.com/post/what-is-oauth2-protocol.html)。



### 2. Shiro登陆注册

#### 2.1 登录逻辑

关于登录模块，我们先来梳理一下逻辑，首先是把登录注册的页面复制进来，然后改成模板形式（头和尾，侧边栏等），再然后集成 shiro 框架，写登录注册接口，login -> realm(认证）-> 写登录注册逻辑 -> 页面的 shiro 标签 -> 分布式 session 的相关配置.

`LoginController`

```java
    @GetMapping("/login")
    public String login() {
        return "/auth/login";
    }

    @ResponseBody
    @PostMapping("/login")
    public Result doLogin(String email, String password) {
        if (StrUtil.isEmpty(email) || StrUtil.isBlank(password)) {
            return Result.fail("邮箱或密码不能为空");
        }

        UsernamePasswordToken token = new UsernamePasswordToken(email, SecureUtil.md5(password));
        try {
            SecurityUtils.getSubject().login(token);
        } catch (AuthenticationException e) {
            if (e instanceof UnknownAccountException) {
                return Result.fail("用户不存在");
            } else if (e instanceof LockedAccountException) {
                return Result.fail("用户被禁用");
            } else if (e instanceof IncorrectCredentialsException) {
                return Result.fail("密码错误");
            } else {
                return Result.fail("用户认证失败");
            }
        }

        return Result.success().action("/");
    }
```

上面的代码，首先分别写了一下 login 的 get 和 post 的方式，一个是跳转到 login，然后我们通过异步的 post 方式来提交 form 表单数据，login 的主要逻辑很简单，主要就一行代码：

```java
SecurityUtils.getSubject().login(token);

根据我们对shiro的理解，login之后会最终委托给realm完成登录逻辑的认证，那么我们先来看看realm的内容（doGetAuthenticationInfo）
    
@Slf4j
@Component
public class AccountRealm extends AuthorizingRealm {
    @Autowired
    UserService userService;
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        UsernamePasswordToken token = (UsernamePasswordToken)authenticationToken;
        //注意token.getUsername()是指email！！
        AccountProfile profile = userService.login(token.getUsername(), String.valueOf(token.getPassword()));
        log.info("---------------->进入认证步骤");
        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(profile, token.getCredentials(), getName());
        return info;
    }
}
```

`doGetAuthenticationInfo` 就是我们认证的方法，`authenticationToken` 就是我们的传过来的 `UsernamePasswordToken` ，包含着邮箱和密码。然后 `userService.login` 的内容就是校验一下账户的合法性，不合法就抛出对应的异常，合法最终就返回封装对象 `AccountProfile`。

```java
@Override
public AccountProfile login(String username, String password) {
    log.info("------------>进入用户登录判断，获取用户信息步骤");
    User user = this.getOne(new QueryWrapper<User>().eq("email", username));
    if(user == null) {
        throw new UnknownAccountException("账户不存在");
    }
    if(!user.getPassword().equals(password)) {
        throw new IncorrectCredentialsException("密码错误");
    }
    //更新最后登录时间
    user.setLasted(new Date());
    this.updateById(user);
    AccountProfile profile = new AccountProfile();
    BeanUtil.copyProperties(user, profile);
    return profile;
}
```








#### 2.2 注册

注册过程设计到一个验证码校验的插件，这里我们使用 google 的验证码生成器 kaptcha。

1. 引入依赖

   ```xml
           <!--图像验证码-->
           <!-- https://mvnrepository.com/artifact/com.github.axet/kaptcha -->
           <dependency>
               <groupId>com.github.axet</groupId>
               <artifactId>kaptcha</artifactId>
               <version>0.0.9</version>
           </dependency>
   ```

2. 然后配置一下验证码的图片生成规则：（边框、颜色、字体大小、长、高等）

   ```java
   /**
    * 图形验证码配置类
    * */
   @Configuration
   public class KaptchaConfig {
       @Bean
       public DefaultKaptcha producer () {
           Properties propertis = new Properties();
           propertis.put("kaptcha.border", "no");
           propertis.put("kaptcha.image.height", "38");
           propertis.put("kaptcha.image.width", "150");
           propertis.put("kaptcha.textproducer.font.color", "black");
           propertis.put("kaptcha.textproducer.font.size", "32");
           Config config = new Config(propertis);
           DefaultKaptcha defaultKaptcha = new DefaultKaptcha();
           defaultKaptcha.setConfig(config);
   
           return defaultKaptcha;
       }
   }
   ```

3. 提供一个访问的接口用于生成验证码图片

   ```java
       private static final String KAPTCHA_SESSION_KEY = "KAPTCHA_SESSION_KEY";
   
       @Autowired
       Producer producer;
   
       @GetMapping("/kapthca.jpg")
       public void kaptcha(HttpServletResponse response) throws IOException {
           /*验证码*/
           String text = producer.createText();
           BufferedImage image = producer.createImage(text);
   
           /*将验证码信息存储到Session中*/
           request.getSession().setAttribute(KAPTCHA_SESSION_KEY, text);
   
           response.setHeader("Cache-Control",
                   "no-store, no-cache");
           response.setContentType("image/ipeg");
           ServletOutputStream outputStream = response.getOutputStream();
           ImageIO.write(image, "jpg", outputStream);
       }
   ```

4. 所以访问这个接口就能得到验证码图片流，页面中：

   ```html
   <div class="">
       <image id="kapthca" src="/kapthca.jpg"></image>
   </div>
   
   ```

5. 那么流是接通前端后端的，到后端还需要验证验证码的正确性，所以生成验证码的时候我们需要把验证码先存到 session 中，然后注册接口中再从 session 中获取出来然后比较是否正确。

   ```java
       @ResponseBody
       @PostMapping("/register")
       public Result doRegister(User user, String repass, String vercode) {
   
           /*检验用户名、密码、邮箱*/
           ValidationUtil.ValidResult validResult = ValidationUtil.validateBean(user);
           if (validResult.hasErrors()) {
               return Result.fail(validResult.getErrors());
           }
   
           /**
            * 这里如果把密码加密应该更好一些
            * */
           if (!user.getPassword().equals(repass)) {
               return Result.fail("两次输入密码不正确");
           }
   
           /*图片验证码*/
           String attribute = (String) request.getSession().getAttribute(KAPTCHA_SESSION_KEY);
   
           if (attribute == null || !attribute.equalsIgnoreCase(vercode)) {
               return Result.fail("验证码输入不正确");
           }
   
           /*注册功能*/
           Result result = userService.register(user);
           return result.action("/login");
       }
   ```

6. 注册的逻辑

   ```java
       /*用户注册*/
       @Override
       public Result register(User user) {
           long count = this.count(new QueryWrapper<User>()
                   .eq("email", user.getEmail())
                   .or()
                   .eq("username", user.getUsername()));
           if (count > 0) {
               return Result.fail("邮箱或用户名已被占用");
           }
   
   /*新建一个User对象而不直接传过来的user原因在于代码只判断了email、username、password三项;
   * 不能确保F12修改代码传输其他的属性
   * */
           User temp = new User();
           temp.setUsername(user.getUsername());
           temp.setPassword(SecureUtil.md5(user.getPassword()));
           temp.setEmail(user.getEmail());
           temp.setAvatar("/res/images/avatar/default.png");
   
           temp.setCreated(new Date());
           temp.setPoint(0);
           temp.setVipLevel(0);
           temp.setCommentCount(0);
           temp.setPostCount(0);
           temp.setGender("0");
           this.save(temp);
   
           return Result.success();
       }
   ```







## 数据库设计 

### 数据库表

- **`user`**

  - ![image-20210828202217727](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210828202217727.png)

  - | 主键 | 账户的id     | 名称        | UUID生成的token值 | 创建时间 | 更新时间 | 个性签名     | 头像的url    | 用户登录的类型 |
    | ---- | ------------ | ----------- | ----------------- | -------- | -------- | ------------ | ------------ | -------------- |
    | int  | varchar(100) | varchar(50) | char(36)          | bigint   | bigint   | varchar(255) | varchar(100) | varchar(10)    |

- **`comment`**
  
  - ![image-20210828203844671](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210828203844671.png)
  
    - | 主键 | 父类id | 评论的类型                        | 评论人的id | 创建时间 | 修改时间 | 点赞数           | 内容          | 评论数        |
      | ---- | ------ | --------------------------------- | ---------- | -------- | -------- | ---------------- | ------------- | ------------- |
      | int  | int    | int(1代表回复问题, 2代表回复评论) | int        | bigint   | bigint   | bigint defalut 0 | varchar(1024) | int default 0 |
  
- **`question`**
  
  - ![image-20210828203916988](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210828203916988.png)
  
  - | 主键 | 标题        | 内容 | 创建时间 | 修改时间 | 创建人的id | 评论数        | 阅读数        | 点赞数        | 标签         |
    | ---- | ----------- | ---- | -------- | -------- | ---------- | ------------- | ------------- | ------------- | ------------ |
    | int  | varchar(50) | text | bigint   | bigint   | int        | int default 0 | int default 0 | int default 0 | varchar(255) |
  
    
  
- **`notification`**
  
  - ![image-20210828203957938](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210828203957938.png)
  
  - | 主键 | 发出通知的那个人 | 收到统治的那个人 |      | 统治的类型 点赞还是评论 | 创建时间 | 状态 0未读;1已读 | 发出通知的那个人的名字 | 标题      |
    | ---- | ---------------- | ---------------- | ---- | ----------------------- | -------- | ---------------- | ---------------------- | --------- |
    | int  | int              | int              | int  | int                     | bigint   | int default 0    | varchar(100)           | varchar() |
  
    

## 秒杀问题以及解决方案

### 1、秒杀的流程

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20237768-f2b7f6ad0c356bfc.png">

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20237768-a459693aa7ab502b.png">

### 2、秒杀的问题

1. **高并发:** 时间极短、 瞬间用户量大，而且用户会在开始前不断刷新页面，还会积累一大堆重复请求的问题，请求过多把数据库打宕机了，系统响应失败，导致与这个系统耦合的系统也GG，一挂挂一片;
2. **链接暴露:** 有人知道了你秒杀的url，然后在秒杀开始前通过这个url传要传递的参数来进行购买操作;
3. **超卖:** 你只有一百件商品，由于是高并发的问题，一起拿到了最后一件商品的信息，都认为还有，全卖出去了，最终卖了一百十件，仓库里根本没这么多货;
4. **恶意请求:** 因为秒杀的价格比较低，有人会用脚本来秒杀，全给一个人买走了，他再转卖，或者同时以脚本操作多个账号一起秒杀。(就是我们常见的黄牛党)
5. **数据库:** 一瞬间高QPS把数据库打宕机了，谁都抢不到,活动失败GG，这可能与高并发有重叠的点，不过着眼于数据库的具体方面。

### 3、解决方案

#### 3.1、高并发的解决方案

既然是超高并发，就想着**降低和分散流量**，需要考虑一些加进来的系统会不会被挂掉。

1. **nginx做负载均衡**(一个tomcat可能只能抗住几百的的并发，nginx还可以对一些恶意ip做限制)
2. **资源静态化，把前端的模板页面放到CDN服务器中**(放到别的服务器中，减轻自己服务器的压力)
3. **页面的按钮，按一次后置灰 $x$ 秒**(防止一直用户一直点,同一个用户重复点击，虽然不会再卖给他，但是请求还是会到后端给系统压力，需要在前端按钮上做限制，比如点一下限制五秒内不能点击)
4. **同一个uid，限制访问频度，做页面缓存，$x$ 秒内到达站点层的请求，均返回同一页面**(用来防止其他程序员跳过按钮，直接用for循环不断发起http请求，具体的话可以请求每次进来都去redis查有没有对应的id的key值，没有就在redis中设置X秒过期的key)
5. **对于写请求，用消息队列**(比如商品有一万件，就放一万个请求进来，当然要做好每秒几个的限制，不能一秒内全放进来，都成功了就继续放下一批，没成功就剩下的请求全部返回失败)
6. **读请求用redis集群顶住**(一个redis也只能顶住几万的并发，叫上兄弟)
7. **一定一定要先把数据库里的东西提前加载到redis来，别等用户查了再加**



#### 3.2、链接暴露的解决方案

​	其实在秒杀没开始前，秒杀的接口就是已经存在的，如果这个接口的url被人知道了，他直接可以在秒杀开始前就通过请求传输必要的参数来进行秒杀。我们要做的就是在秒杀开始前，谁都不知道秒杀(也就是付款、减少库存的接口)这个接口的url是什么。

**如何防止url的暴露?**

我们要做的是，**在秒杀时间到的时候，才能获得url**。而且秒杀场景中，肯定会有一个倒计时的模块，来告诉你还有几秒开始秒杀。我们逻辑如下：

> 1. 页面中有一个计时模块，是访问秒杀页面的时候去从服务器里拿的，计时结束，显示秒杀的按钮。
>    - **为什么不直接取用户的时间？**
>      - 用户的本机时间是可以修改的。
> 2. 点击秒杀按钮后，再次请求服务器时间，与秒杀的时间对比，如果秒杀进行中，返回一个由加密过的秒杀url
>    - **为什么还要再次请求服务器时间？**
>      - 虽然第一次请求到了服务器时间，但是时间的倒计时操作是页面来完成的，比如在几天前就打开这个秒杀页面等待倒计时，等秒杀开始时可能与服务器时间存在几秒的误差。
>    - **怎么加密url?**
>      - 通过一个无序的String字符串，也就是我们常说的盐值字符串，进行MD5加密得到哈希值。当点击秒杀按钮返回url的时候，返回这个MD5值进行url的拼接。
>        真正秒杀接口的mapping格式为
>      - <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20237768-8e231696bc44deff.png">
> 3. 通过这个加密过的url来进行支付、减库存操作



#### 3.3、超卖问题的解决方案

先设想这么一个场景：

> 我们假设现在商品只剩下一件了，此时数据库中 $num = 1$;
> 但有100个线程同时读取到了这个 $num = 1$，所以100个线程都开始减库存了。

这里有三个解决思路,分别是**悲观锁**、**乐观锁**和**redis**：

- **悲观锁**

  - 将减少库存的操作加入到事务中，（注意此时数据库的引擎需要是Innodb的只有innodb才支持事务和行级锁，而事务和行级锁这两个概念，一般也是同时出现），此时一个线程开始修改这一行的时候，会先获得排他锁，获得锁后开始修改，没有获得锁的线程会阻塞。
  - **思考**:在我实现的秒杀系统中用的正是事务来实现的，将订单信息的插入（insert）和减少库存（update）放到一个事务中。**可以思考下先做insert还是update**？
  - 答案应该是**先insert而后update**，因为insert的锁的行不会有人竞争，而update的排它锁的行会出现大量竞争，而**事务锁释放的时机是整个事务完成，而不是这个方法执行完毕的时候**，所以需要后update，尽量减少库存行锁的获取时间，来提高并发效率。

- **乐观锁**

  - **需要在数据库表中加入版本号字段**，sql语句如下，先查询出版本号，在下次更新的时候通过判断版本号是否更改和库存是否不为0来决定是否update

  - ```mysql
    select version from goods WHERE id = 1001;
    update goods set num = num - 1, version = version + 1 WHERE id= 1001 AND num > 0 AND version = @version(上面查到的version);
    ```

  - 这种方式采用了版本号的方式，其实也就是**CAS**的原理。

  - >假设此时version = 100， num = 1; 
    >
    >100个线程进入到了这里，同时他们select出来版本号都是version = 100。
    >
    >然后直接update的时候，只有其中一个先update了，同时更新了版本号。
    >
    >那么其他99个在更新的时候，会发觉version并不等于上次select的version，就说明version被其他线程修改过了。那么我就放弃这次update。

- **Redis**

  - >首先要了解，redis是**单线程执行**的
    >
    >利用redis的单线程预减库存。比如商品有100件。那么我在redis存储一个<k,v>。例如 <gs1001, 100>
    >
    >每一个用户线程进来，key值就减1，等减到0的时候，全部拒绝剩下的请求。
    >
    >那么也就是只有100个线程会进入到后续操作。所以一定不会出现超卖的现象

#### 3.4、恶意请求的解决方案

- **怎么限制让一个人只能秒杀一件商品？**

  - 这个其实比较容易想到，数据库肯定有一张订单表（包含用户id字段、商品id字段），只要每次去查一下**用户的id和这件商品的id**有没有在这张表里就行了，存在说明该用户买过这个商品了，就不给买了。

  - 问题在于，是不是每次都需要去查询然后插入呢?有没有更快的方法？

    - > 一般支付成功扣款和把订单信息加入到订单表中这两个操作设为一个事务。
      >
      > 把**用户id**和**商品id**作为**联合主键索引**存储到数据库中，下次如果再想插入一样用户id和商品id的行是会报错的。（主键索引的唯一性）,事务中出现错误，支付操作自然也失败了

- **如果一个人用脚本掌握了多个账号去执行秒杀，怎么办？**

  - 可以让用户付款的时候回答问题，防止脚本的操作。比如12306买火车票的时候，是不是会有按顺序点击图中相同的文字？这就是为了防止脚本。

#### 3.5、数据库层面的解决方案

1. **消息队列消峰**

   - >比如一秒钟进来1W个写请求，我们数据库只能顶住一秒5000个，那我们就每秒放出来4000个。

2. **用缓存来顶住大量的查询请求**

   - > 引入redis，如果redis中有要查询的数据，就直接返回，如果没有，从数据库查询的时候，把查询的结果放到redis中，以后查询都会落到redis层。

   - > 1. **问题一：** 一开始没有这个key，需要第一次查询才会缓存到redis，此时秒杀开始，又是一堆并发进来把数据库打挂了，活动失败，GG。
     >     **解决方案：**在活动没开始前，就把可能会访问到的数据都加载到redis中，虽然解决方法很简单，但是一开始没想到这个请况，会是很严重的问题。
     > 2. **问题二**：如果这个数据特别热，突然这个key过期了，然后一堆并发请求过来查询这个数据，还是把数据库打挂了。
     >     **解决方案：** 这也是我们常说的**缓存击穿**的问题，先看下这个问题的解决方案
     >     1.设置这个key不过期（比如淘宝首页，只需要在有修改的时候去更新这个key就行）
     >     2.数据库的查询时使用互斥锁，这个时候只有一个线程来查询，不会有一瞬间大量的查询sql，查询之后也放在了redis中，后面的查询就不会打到sql。（查询的互斥锁会把查询的结果集加入互斥锁，其他的不受影响，也就是说大大降低了相同查询的并发量）
     >
     > 

3. **数据库读写分离**

   - >设置主从数据库，主数据库负责写，从数据库负责读，可以极大程度的缓解X锁和S锁争用。

