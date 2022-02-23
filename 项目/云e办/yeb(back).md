# 云E办(后端)

## 项目介绍

本项目目的是实现中小型企业的在线办公系统，云E办在线办公系统是一个用来管理日常的办公事务的一个系统，他能够管的内容有：日常的各种流程审批，新闻，通知，公告，文件信息，财务，人事，费用，资产，行政，项目，移动办公等等。它的作用就是通过软件的方式，方便管理，更加简单，更加扁平。更加高效，更加规范，能够提高整体的管理运营水平。

本项目在技术方面采用最主流的前后端分离开发模式，使用业界最流行、社区非常活跃的开源框架Spring Boot来构建后端，旨在实现云E办在线办公系统。包括职位管理、职称管理、部门管理、员工管理、工资管理、在线聊天等模块。项目中还会使用业界主流的第三方组件扩展大家的知识面和技能池。

本项目主要模块及技术点如图

![](yeb(back).images/Snipaste_2020-04-23_16-52-59.png)

## 搭建项目

### 创建父项目

![image-20210714213414685](yeb(back).assets/image-20210714213414685.png)



**添加依赖**

yeb的pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>


	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.0.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<groupId>com.yjxxt</groupId>
	<artifactId>yeb</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>pom</packaging>
	<name>yeb</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>
</project>

```

### 创建yeb-server子项目

![image-20210714215310972](yeb(back).assets/image-20210714215310972.png)



### **添加依赖**

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>com.yjxxt</groupId>
		<artifactId>yeb</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>

	<groupId>com.yjxxt</groupId>
	<artifactId>yeb-server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>yeb-server</name>

	<dependencies>
		<!--web 依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!--lombok 依赖-->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<!--mysql 依赖-->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<!--mybatis-plus 依赖-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.3.1.tmp</version>
		</dependency>
        <!-- swagger2 依赖 -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.7.0</version>
		</dependency>
		<!-- Swagger第三方ui依赖 -->
		<dependency>
			<groupId>com.github.xiaoymin</groupId>
			<artifactId>swagger-bootstrap-ui</artifactId>
			<version>1.9.6</version>
		</dependency>
	</dependencies>
</project>
```

### **修改配置文件**

application.yml

```yaml
server:
  # 端口
  port: 8081

spring:
  # 数据源配置
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/yeb?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
    username: root
    password: root
    hikari:
      # 连接池名
      pool-name: DateHikariCP
      # 最小空闲连接数
      minimum-idle: 5
      # 空闲连接存活最大时间，默认600000（10分钟）
      idle-timeout: 180000
      # 最大连接数，默认10
      maximum-pool-size: 10
      # 从连接池返回的连接的自动提交
      auto-commit: true
      # 连接最大存活时间，0表示永久存活，默认1800000（30分钟）
      max-lifetime: 1800000
      # 连接超时时间，默认30000（30秒）
      connection-timeout: 30000
      # 测试连接是否可用的查询语句
      connection-test-query: SELECT 1

# Mybatis-plus配置
mybatis-plus:
  #配置Mapper映射文件
  mapper-locations: classpath*:/mapper/*Mapper.xml
  # 配置MyBatis数据返回类型别名（默认别名是类名）
  type-aliases-package: com.yjxxt.server.pojo
  configuration:
    # 自动驼峰命名
    map-underscore-to-camel-case: false

## Mybatis SQL 打印(方法接口所在的包，不是Mapper.xml所在的包)
logging:
  level:
    com.yjxxt.server.mapper: debug
```

### **启动类**

```java
package com.yjxxt.server;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 启动类

 * @since 1.0.0
 */
@SpringBootApplication
@MapperScan("com.yjxxt.server.mapper")
public class VoaApplication {

	public static void main(String[] args) {
		SpringApplication.run(VoaApplication.class,args);
	}

}
```

### 完整目录

![image-20210714215546966](yeb(back).assets/image-20210714215546966.png)

## AutoGenerator的使用

### AutoGenerator是什么？

   AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Pojo、Mapper、Mapper XML、Service、Controller 等各个模块的代码

### AutoGenerator能干什么？

​     对于单表而言，几乎是一个全能的工具，极大的提升了开发效率。更多的关注业务逻辑的实现。

### 怎么使用？

#### 创建一个AutoGenerator项目

　AutoGenerator本身和我们项目没有关联，所以可以单独新建为一个Project，这边也做成Maven聚合项目里的一个子项目

![image-20210714215715711](yeb(back).assets/image-20210714215715711.png)



#### 添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>com.yjxxt</groupId>
		<artifactId>yeb</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>

	<groupId>com.yjxxt</groupId>
	<artifactId>yeb-generator</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>
		<!--web 依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!--mybatis-plus 依赖-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.3.1.tmp</version>
		</dependency>
		<!--mybatis-plus 代码生成器依赖-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-generator</artifactId>
			<version>3.3.1.tmp</version>
		</dependency>
		<!--freemarker 依赖-->
		<dependency>
			<groupId>org.freemarker</groupId>
			<artifactId>freemarker</artifactId>
		</dependency>
		<!--mysql 依赖-->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
	</dependencies>

</project>
```

#### CodeGenerator工具类

```java
package com.yjxxt.generator;

import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.FileOutConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.TemplateConfig;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

/**
 * 执行 main 方法控制台输入模块表名回车自动生成对应项目目录中
 * @since 1.0.0
 */

public class CodeGenerator {

	/**
	 * <p>
	 * 读取控制台内容
	 * </p>
	 */
	public static String scanner(String tip) {
		Scanner scanner = new Scanner(System.in);
		StringBuilder help = new StringBuilder();
		help.append("请输入" + tip + "：");
		System.out.println(help.toString());
		if (scanner.hasNext()) {
			String ipt = scanner.next();
			if (StringUtils.isNotEmpty(ipt)) {
				return ipt;
			}
		}
		throw new MybatisPlusException("请输入正确的" + tip + "！");
	}

	public static void main(String[] args) {
		// 代码生成器
		AutoGenerator mpg = new AutoGenerator();

		// 全局配置
		GlobalConfig gc = new GlobalConfig();
		String projectPath = System.getProperty("user.dir");
		gc.setOutputDir(projectPath + "/yeb-server/src/main/java");
		//作者
		gc.setAuthor("zhoubin");
		//打开输出目录
		gc.setOpen(false);
		//xml开启 BaseResultMap
		gc.setBaseResultMap(true);
		//xml 开启BaseColumnList
		gc.setBaseColumnList(true);
		// 实体属性 Swagger2 注解
		gc.setSwagger2(true);
		mpg.setGlobalConfig(gc);

		// 数据源配置
		DataSourceConfig dsc = new DataSourceConfig();
		dsc.setUrl("jdbc:mysql://localhost:3306/yeb?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia" +
				"/Shanghai");
		dsc.setDriverName("com.mysql.cj.jdbc.Driver");
		dsc.setUsername("root");
		dsc.setPassword("root");
		mpg.setDataSource(dsc);

		// 包配置
		PackageConfig pc = new PackageConfig();
		pc.setParent("com.yjxxt.server")
				.setEntity("pojo")
				.setMapper("mapper")
				.setService("service")
				.setServiceImpl("service.impl")
				.setController("controller");
		mpg.setPackageInfo(pc);

		// 自定义配置
		InjectionConfig cfg = new InjectionConfig() {
			@Override
			public void initMap() {
				// to do nothing
			}
		};

		// 如果模板引擎是 freemarker
		String templatePath = "/templates/mapper.xml.ftl";
		// 如果模板引擎是 velocity
		// String templatePath = "/templates/mapper.xml.vm";

		// 自定义输出配置
		List<FileOutConfig> focList = new ArrayList<>();
		// 自定义配置会被优先输出
		focList.add(new FileOutConfig(templatePath) {
			@Override
			public String outputFile(TableInfo tableInfo) {
				// 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
				return projectPath + "/yeb-server/src/main/resources/mapper/" + tableInfo.getEntityName() + "Mapper"
						+ StringPool.DOT_XML;
			}
		});
		cfg.setFileOutConfigList(focList);
		mpg.setCfg(cfg);

		// 配置模板
		TemplateConfig templateConfig = new TemplateConfig();

		templateConfig.setXml(null);
		mpg.setTemplate(templateConfig);

		// 策略配置
		StrategyConfig strategy = new StrategyConfig();
		//数据库表映射到实体的命名策略
		strategy.setNaming(NamingStrategy.underline_to_camel);
		//数据库表字段映射到实体的命名策略
		strategy.setColumnNaming(NamingStrategy.no_change);
		//lombok模型
		strategy.setEntityLombokModel(true);
		//生成 @RestController 控制器
		strategy.setRestControllerStyle(true);
		strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
		strategy.setControllerMappingHyphenStyle(true);
		//表前缀
		strategy.setTablePrefix("t_");
		mpg.setStrategy(strategy);
		mpg.setTemplateEngine(new FreemarkerTemplateEngine());
		mpg.execute();
	}

}
```

#### 执行

执行main方法，在控制台直接输出表名，多个表名用`,`隔开

![](yeb(back).images/image40.png)



## 登录功能

我们这边使用`Spring Security`框架实现登录功能,关于`Spring Security`知识点请参考之前文档

### 添加依赖

pom.xml

```xml
<!--security 依赖-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<!--JWT 依赖-->
<dependency>
   <groupId>io.jsonwebtoken</groupId>
   <artifactId>jjwt</artifactId>
   <version>0.9.0</version>
</dependency>
```

### 修改配置

application.yml

```yaml
jwt:
  # JWT存储的请求头
  tokenHeader: Authorization
  # JWT 加解密使用的密钥
  secret: yeb-secret
  # JWT的超期限时间（60*60*24）
  expiration: 604800
  # JWT 负载中拿到开头
  tokenHead: Bearer
```

### 添加Jwt Token的工具类

JwtTokenUtil.java

```java
package com.yjxxt.yeb.security;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

/**
 * Jwt Token工具类
 *

 * @since 1.0.0
 */
@Component
public class JwtTokenUtil {

   private static final String CLAIM_KEY_USERNAME = "sub";
   private static final String CLAIM_KEY_CREATED = "created";
   @Value("${jwt.secret}")
   private String secret;
   @Value("${jwt.expiration}")
   private Long expiration;

   /**
    * 根据负载生成JWT Token
    *
    * @param claims
    * @return
    */
   private String generateToken(Map<String, Object> claims) {
      return Jwts.builder()
            .setClaims(claims)
            .setExpiration(generateExpirationDate())
            .signWith(SignatureAlgorithm.ES256, secret)
            .compact();
   }


   /**
    * 从token中获取JWT中的负载
    *
    * @param token
    * @return
    */
   private Claims getClaimsFromToken(String token) {
      Claims claims = null;
      try {
         claims = Jwts.parser()
               .setSigningKey(secret)
               .parseClaimsJws(token)
               .getBody();
      } catch (Exception e) {
         e.printStackTrace();
      }
      return claims;
   }

   /**
    * 生成token过期时间
    *
    * @return
    */
   private Date generateExpirationDate() {
      return new Date(System.currentTimeMillis() + expiration * 1000);
   }

   /**
    * 从token中获取过期时间
    *
    * @param token
    * @return
    */
   private Date getExpiredDateFromToken(String token) {
      Claims claims = getClaimsFromToken(token);
      return claims.getExpiration();
   }

   /**
    * 判断token是否失效
    *
    * @param token
    * @return
    */
   private boolean isTokenExpired(String token) {
      Date expiredDate = getExpiredDateFromToken(token);
      return expiredDate.before(new Date());
   }

   /**
    * 从token中获取登录用户名
    *
    * @param token
    * @return
    */
   public String getUserNameFormToken(String token) {
      String username;
      try {
         Claims claims = getClaimsFromToken(token);
         username = claims.getSubject();
      } catch (Exception e) {
         username = null;
      }
      return username;
   }

   /**
    * 验证token是否有效
    *
    * @param token
    * @param userDetails
    * @return
    */
   public boolean validateToken(String token, UserDetails userDetails) {
      String username = getUserNameFormToken(token);
      return username.equals(userDetails.getUsername()) && !isTokenExpired(token);
   }

   /**
    * 根据用户信息生成token
    * @param userDetails
    * @return
    */
   public String generateToken(UserDetails userDetails){
      Map<String,Object> claims = new HashMap<>();
      claims.put(CLAIM_KEY_USERNAME,userDetails.getUsername());
      claims.put(CLAIM_KEY_CREATED,new Date());
      return generateToken(claims);
   }

   /**
    * 判断token是否可以被刷新
    * @param token
    * @return
    */
   public boolean canRefresh(String token){
      return !isTokenExpired(token);
   }

   /**
    * 刷新token
    * @param token
    * @return
    */
   public String refreshToken(String token){
      Claims claims = getClaimsFromToken(token);
      claims.put(CLAIM_KEY_CREATED,new Date());
      return generateToken(claims);
   }


}
```

### Admin实现UserDetails类

```java
package com.yjxxt.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

/**
 * <p>
 *
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_admin")
@ApiModel(value = "Admin对象", description = "")
public class Admin implements Serializable, UserDetails {

	@ApiModelProperty(value = "id")
	@TableId(value = "id", type = IdType.AUTO)
	private Integer id;

	@ApiModelProperty(value = "姓名")
	private String name;

	@ApiModelProperty(value = "手机号码")
	private String phone;

	@ApiModelProperty(value = "住宅电话")
	private String telephone;

	@ApiModelProperty(value = "联系地址")
	private String address;

	@ApiModelProperty(value = "是否启用")
	private Boolean enabled;

	@ApiModelProperty(value = "用户名")
	private String username;

	@ApiModelProperty(value = "密码")
	private String password;

	@ApiModelProperty(value = "用户头像")
	private String userFace;

	@ApiModelProperty(value = "备注")
	private String remark;

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		return null;
	}

	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	@Override
	public boolean isEnabled() {
		return enabled;
	}
}
```

### 添加公共返回对象

**RespBean.java**

```java
package com.yjxxt.server.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * 通用返回结果对象
 *

 * @since 1.0.0
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class RespBean {

	private long code;
	private String message;
	private Object obj;

	/**
	 * 成功返回结果
	 *
	 * @param message
	 */
	public static RespBean success(String message) {
		return new RespBean(200, message, null);
	}

	/**
	 * 成功返回结果
	 *
	 * @param obj
	 * @param message
	 */
	public static RespBean success(String message, Object obj) {
		return new RespBean(200, message, obj);
	}

	/**
	 * 失败返回结果
	 *
	 * @param message
	 */
	public static RespBean error(String message) {
		return new RespBean(500, message, null);
	}

	/**
	 * 失败返回结果
	 * @param message
	 * @param obj
	 * @return
	 */
	public static RespBean error(String message,Object obj) {
		return new RespBean(500, message, obj);
	}
}
```

### 添加登录相应接口

#### AdminLoginParam

```java
package com.yjxxt.server.pojo;

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@ApiModel(value = "AdminLogin对象", description = "")
public class AdminLoginParam {
    @ApiModelProperty(value = "用户名", required = true)
    private String username;
    @ApiModelProperty(value = "密码", required = true)
    private String password;
}
```

#### LoginController

```java
package com.yjxxt.server.controller;

import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.AdminLoginParam;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IAdminService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import java.security.Principal;

/**
 * 登录控制器
 *

 * @since 1.0.0
 */
@Api(tags = "LoginController")
@RestController
public class LoginController {

	@Autowired
	private IAdminService adminService;


	@ApiOperation(value = "登录之后返回token")
	@PostMapping("/login")
	public RespBean login(@RequestBody AdminLoginParam adminLoginParam) {
		return adminService.login(adminLoginParam.getUsername(), adminLoginParam.getPassword());
	}


	@ApiOperation(value = "获取当前用户信息")
	@GetMapping("/admin/info")
	public Admin getAdminInfo(Principal principal) {
		if (null == principal) {
			return null;
		}
		String username = principal.getName();
		Admin admin = adminService.getAdminByUserName(username);
		admin.setPassword(null);
		return admin;
	}


	@ApiOperation(value = "退出登录")
	@PostMapping("/logout")
	public RespBean logout() {
		return RespBean.success("注销成功！");
	}

}
```

#### IAdminService

```java
package com.yjxxt.server.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.Role;

import java.util.List;

/**
 * <p>
 *  服务类
 * </p>
 *

 */
public interface IAdminService extends IService<Admin> {

    /**
    * 登录返回token
    * @param username
    * @param password
    * @return
    */
   RespBean login(String username,String password);

   /**
    * 根据用户名获取用户
    * @param username
    */
   Admin getAdminByUserName(String username);
}
```

#### AdminServiceImpl

```java
package com.yjxxt.server.service.impl;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.yjxxt.server.config.security.JwtTokenUtil;
import com.yjxxt.server.mapper.AdminMapper;
import com.yjxxt.server.mapper.RoleMapper;
import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.pojo.Role;
import com.yjxxt.server.service.IAdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import javax.servlet.http.HttpServletRequest;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * <p>
 * 服务实现类
 * </p>
 *

 */
@Service
public class AdminServiceImpl extends ServiceImpl<AdminMapper, Admin> implements IAdminService {
	@Autowired
	private AdminMapper adminMapper;
	@Autowired
	private UserDetailsService userDetailsService;
	@Autowired
	private PasswordEncoder passwordEncoder;
	@Autowired
	private JwtTokenUtil jwtTokenUtil;
	@Value("${jwt.tokenHead}")
	private String tokenHead;

	/**
	 * 登录返回token
	 *
	 * @param username
	 * @param password
	 * @return
	 */
	@Override
	public RespBean login(String username, String password) {
		UserDetails userDetails = userDetailsService.loadUserByUsername(username);
		if (null == userDetails || !passwordEncoder.matches(password, userDetails.getPassword())) {
			return RespBean.error("用户名或密码不正确!");
		}
		if (!userDetails.isEnabled()){
			return RespBean.error("账号被禁用，请联系管理员!");
		}
		UsernamePasswordAuthenticationToken authentication =
				new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
		SecurityContextHolder.getContext().setAuthentication(authentication);
		String token = jwtTokenUtil.generateToken(userDetails);
		Map<String, String> tokenMap = new HashMap<>();
		tokenMap.put("token", token);
		tokenMap.put("tokenHead", tokenHead);
		return RespBean.success("登录成功", tokenMap);
	}

	/**
	 * 根据用户名获取用户
	 *
	 * @param username
	 * @return
	 */
	@Override
	public Admin getAdminByUserName(String username) {
		return adminMapper.selectOne(new QueryWrapper<Admin>().eq("username", username));
	}
}
```



### 配置SpringSecurity

**SecurityConfig.java**

```java
package com.yjxxt.server.config.security;

import com.yjxxt.server.config.security.component.JwtAuthenticationTokenFilter;
import com.yjxxt.server.config.security.component.RestAuthenticationEntryPoint;
import com.yjxxt.server.config.security.component.RestfulAccessDeniedHandler;
import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.service.IAdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

/**
 * Security配置类
 *

 * @since 1.0.0
 */
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	private IAdminService adminService;
	@Autowired
	private RestAuthenticationEntryPoint restAuthenticationEntryPoint;
	@Autowired
	private RestfulAccessDeniedHandler restfulAccessDeniedHandler;

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.userDetailsService(userDetailsService()).passwordEncoder(passwordEncoder());
	}

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		//使用JWT，不需要csrf
		http.csrf()
				.disable()
				//基于token，不需要session
				.sessionManagement()
				.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
				.and()
				.authorizeRequests()
				//允许登录访问
				.antMatchers("/login","/logout")
				.permitAll()
				//除上面外，所有请求都要求认证
				.anyRequest()
				.authenticated()
				.and()
				//禁用缓存
				.headers()
				.cacheControl();
		//添加jwt 登录授权过滤器
		http.addFilterBefore(jwtAuthenticationTokenFilter(), UsernamePasswordAuthenticationFilter.class);
		//添加自定义未授权和未登录结果返回
		http.exceptionHandling()
				.accessDeniedHandler(restfulAccessDeniedHandler)
				.authenticationEntryPoint(restAuthenticationEntryPoint);
	}

	@Bean
	public PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}

	@Override
	@Bean
	public UserDetailsService userDetailsService() {
		//获取登录用户信息
		return username -> {
			Admin admin = adminService.getAdminByUserName(username);
			if (null != admin) {
				return admin;
			}
			return null;
		};
	}

	@Bean
	public JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter() {
		return new JwtAuthenticationTokenFilter();
	}
}
```

### 添加自定义未授权及未登录的结果返回

**RestAuthorizationEntryPoint.java**

```java
package com.yjxxt.server.config.security.component;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.yjxxt.server.pojo.RespBean;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * 当未登录或者token失效时访问接口时，自定义的返回结果
 *

 * @since 1.0.0
 */
@Component
public class RestAuthenticationEntryPoint implements AuthenticationEntryPoint {

   @Override
   public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
      response.setCharacterEncoding("UTF-8");
      response.setContentType("application/json");
      PrintWriter out = response.getWriter();
      RespBean bean = RespBean.error("权限不足，请联系管理员！");
	  bean.setCode(401);
	  out.write(new ObjectMapper().writeValueAsString(bean));
      out.flush();
      out.close();
   }
}
```

**RestfulAccessDeniedHandler.java**

```java
package com.yjxxt.server.config.security.component;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.yjxxt.server.pojo.RespBean;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.stereotype.Component;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * 当访问接口没有权限时，自定义返回结果类
 *

 * @since 1.0.0
 */
@Component
public class RestfulAccessDeniedHandler implements AccessDeniedHandler {

   @Override
   public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
      response.setCharacterEncoding("UTF-8");
      response.setContentType("application/json");
      PrintWriter out = response.getWriter();
      RespBean bean = RespBean.error("权限不足，请联系管理员！");
	  bean.setCode(403);
	  out.write(new ObjectMapper().writeValueAsString(bean));
      out.flush();
      out.close();
   }
}
```

### 添加Jwt登录授权过滤器

**JwtAuthenticationTokenFilter.java**

```java
package com.yjxxt.server.config.security.component;

import com.yjxxt.server.config.security.JwtTokenUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * Jwt登录授权过滤器
 *
 * 获取头-->根据头获取token-->根据token 获取username-->判断用户是否登录-->执行登录-->设置登录信息-->过滤器放行
 * @since 1.0.0
 */
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {
	@Autowired
	private UserDetailsService userDetailsService;
	@Autowired
	private JwtTokenUtil jwtTokenUtil;
	@Value("${jwt.tokenHeader}")
	private String tokenHeader;
	@Value("${jwt.tokenHead}")
	private String tokenHead;

	@Override
	protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws ServletException, IOException {
		String authHeader = request.getHeader(this.tokenHeader);
		//存在token
		if (null != authHeader && authHeader.startsWith(this.tokenHead)) {
			String authToken = authHeader.substring(this.tokenHead.length());
			String username = jwtTokenUtil.getUserNameFormToken(authToken);
			//token中存在用户名但未登录
			if (null!=username&& null==SecurityContextHolder.getContext().getAuthentication()){
				//登录
				UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);
				//验证token是否有效，重新设置用户对象
				if (jwtTokenUtil.validateToken(authToken,userDetails)){
					UsernamePasswordAuthenticationToken authentication =
							new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
					authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
					SecurityContextHolder.getContext().setAuthentication(authentication);
				}
			}
		}
		chain.doFilter(request,response);
	}
}



```

## 接口文档

接口文档以及测试使用`swagger`实现，关于`swagger`知识点请参考之前文档

### 添加依赖

```xml
<!-- swagger2 依赖 -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger2</artifactId>
   <version>2.7.0</version>
</dependency>
<!-- Swagger第三方ui依赖 -->
<dependency>
   <groupId>com.github.xiaoymin</groupId>
   <artifactId>swagger-bootstrap-ui</artifactId>
   <version>1.9.6</version>
</dependency>
```

### 配置Swagger

**Swagger2Config.java**

```java
package com.yjxxt.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.ApiKey;
import springfox.documentation.service.AuthorizationScope;
import springfox.documentation.service.Contact;
import springfox.documentation.service.SecurityReference;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.service.contexts.SecurityContext;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;
import java.util.List;

/**
 * Swagger2配置
 *

 * @since 1.0.0
 */
@Configuration
@EnableSwagger2
public class Swagger2Config {
	@Bean
	public Docket createRestApi() {
		return new Docket(DocumentationType.SWAGGER_2)
				.apiInfo(apiInfo())
				.select()
				//为当前包下的controller生成api文档
				.apis(RequestHandlerSelectors.basePackage("com.yjxxt.server.controller"))
				.paths(PathSelectors.any())
				.build();
	}

	private ApiInfo apiInfo() {
		//设置文档信息
		return new ApiInfoBuilder()
				.title("云E办接口文档")
				.description("云E办接口文档")
				.contact(new Contact("yjxxt", "http:localhost:8081/doc.html", "yjxxt@xxxx.com"))
				.version("1.0")
				.build();
	}
}
```

### 准备测试接口

**HelloController.java**

```java
package com.yjxxt.server.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * 测试Controller
 *

 * @since 1.0.0
 */
@RestController
public class HelloController {

   @GetMapping("/hello")
   public String hello(){
      return "hello";
   }

}
```

### 测试

访问http:localhost:8081/doc.html

![](yeb(back).images/Snipaste_2020-04-17_08-51-17.png)

发现无法访问，这是因为Swagger的地址被SpringSecurity拦截。我们修改下SpringSecurity的配置，放行Swagger

**SecurityConfig.java**

```java
@Override
public void configure(WebSecurity web) throws Exception {
   //放行静态资源
   web.ignoring().antMatchers(
         "/login",
         "/logout",
         "/css/**",
         "/js/**",
         "/index.html",
         "/favicon.ico",
         "/doc.html",
         "/webjars/**",
         "/swagger-resources/**",
         "/v2/api-docs/**");
}
```

重启项目再次访问`/hello`接口

![](yeb(back).images/Snipaste_2020-04-17_10-24-33.png)

发现接口访问失败，提示**暂未登录或token已经过期**。这是因为`/hello`接口需要登录成功之后才能访问

### 添加Authorize

**Swagger2Config.java**

```java
package com.yjxxt.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.ApiKey;
import springfox.documentation.service.AuthorizationScope;
import springfox.documentation.service.Contact;
import springfox.documentation.service.SecurityReference;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.service.contexts.SecurityContext;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;
import java.util.List;

/**
 * Swagger2配置
 *

 * @since 1.0.0
 */
@Configuration
@EnableSwagger2
public class Swagger2Config {
   @Bean
   public Docket createRestApi() {
      return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .select()
            //为当前包下的controller生成api文档
            .apis(RequestHandlerSelectors.basePackage("com.yjxxt.server.controller"))
            .paths(PathSelectors.any())
            .build()
            //添加登录认证
            .securitySchemes(securitySchemes())
            .securityContexts(securityContexts());
   }

   private List<SecurityContext> securityContexts() {
      //设置需要登录认证的路径
      List<SecurityContext> result = new ArrayList<>();
      result.add(getContextByPath("/hello/.*"));
      return result;
   }

   private SecurityContext getContextByPath(String pathRegex) {
      return SecurityContext.builder()
            .securityReferences(defaultAuth())
            .forPaths(PathSelectors.regex(pathRegex))
            .build();
   }

   private List<SecurityReference> defaultAuth() {
      List<SecurityReference> result = new ArrayList<>();
      AuthorizationScope authorizationScope = new AuthorizationScope("global", "accessEverything");
      AuthorizationScope[] authorizationScopes = new AuthorizationScope[1];
      authorizationScopes[0] = authorizationScope;
      result.add(new SecurityReference("Authorization",authorizationScopes));
      return result;
   }

   private ApiInfo apiInfo() {
      //设置文档信息
      return new ApiInfoBuilder()
            .title("云E办接口文档")
            .description("云E办接口文档")
            .contact(new Contact("zhoubin", "http:localhost:8081/doc.html", "yjxxt@xxxx.com"))
            .version("1.0")
            .build();
   }

   private List<ApiKey> securitySchemes(){
      //设置请求头信息
      List<ApiKey> result = new ArrayList<>();
      ApiKey apiKey = new ApiKey("Authorization","Authorization","header");
      result.add(apiKey);
      return result;
   }


}
```

访问http:localhost:8081/doc.html

首先登录获取token

![](yeb(back).images/Snipaste_2020-04-17_10-34-35.png)

点击`Authorize`按钮，在参数值输入框中输入获取的token，并保存

![](yeb(back).images/Snipaste_2020-04-17_10-36-51.png)

**注意：**`Bearer`必须输入，并且和token中间有一个空格。

可以通过`/admin/info`接口获取当前登录用户信息

![](yeb(back).images/Snipaste_2020-04-17_10-39-36.png)

再次访问`/hello`接口，也可以正常访问

![](yeb(back).images/Snipaste_2020-04-17_10-39-47.png)

## 登录验证码

### 生成验证码

图像验证码显示功能使用`google Kaptcha` 验证码产品 实现前台验证码显示功能

![](yeb(back).images\image36.png)

#### 添加依赖

```xml
<!-- google kaptcha依赖 -->
<dependency>
   <groupId>com.github.axet</groupId>
   <artifactId>kaptcha</artifactId>
   <version>0.0.9</version>
</dependency>
```

#### 验证码配置类

**CaptchaConfig.java**

```java
package com.yjxxt.yebserver.config;

import com.google.code.kaptcha.impl.DefaultKaptcha;
import com.google.code.kaptcha.util.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Properties;

/**
 * 验证码配置类
 *

 * @since 1.0.0
 */
@Configuration
public class CaptchaConfig {

   @Bean
   public DefaultKaptcha getDefaultKaptcha(){
      //验证码生成器
      DefaultKaptcha defaultKaptcha=new DefaultKaptcha();
      //配置
      Properties properties = new Properties();
      //是否有边框
      properties.setProperty("kaptcha.border", "yes");
      //设置边框颜色
      properties.setProperty("kaptcha.border.color", "105,179,90");
      //边框粗细度，默认为1
      // properties.setProperty("kaptcha.border.thickness","1");
      //验证码
      properties.setProperty("kaptcha.session.key","code");
      //验证码文本字符颜色 默认为黑色
      properties.setProperty("kaptcha.textproducer.font.color", "blue");
      //设置字体样式
      properties.setProperty("kaptcha.textproducer.font.names", "宋体,楷体,微软雅黑");
      //字体大小，默认40
      properties.setProperty("kaptcha.textproducer.font.size", "30");
      //验证码文本字符内容范围 默认为abced2345678gfynmnpwx
      // properties.setProperty("kaptcha.textproducer.char.string", "");
      //字符长度，默认为5
      properties.setProperty("kaptcha.textproducer.char.length", "4");
      //字符间距 默认为2
      properties.setProperty("kaptcha.textproducer.char.space", "4");
      //验证码图片宽度 默认为200
      properties.setProperty("kaptcha.image.width", "100");
      //验证码图片高度 默认为40
      properties.setProperty("kaptcha.image.height", "40");
      Config config = new Config(properties);
      defaultKaptcha.setConfig(config);
      return defaultKaptcha;
   }
}
```

#### 提供验证码生成接口

**CaptchaController.java**

```java
package com.yjxxt.server.controller;

import com.google.code.kaptcha.impl.DefaultKaptcha;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.imageio.ImageIO;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.awt.image.BufferedImage;
import java.io.IOException;

/**
 * 验证码Controller
 *

 * @since 1.0.0
 */
@RestController
public class CaptchaController {

	@Autowired
	private DefaultKaptcha defaultKaptcha;

	@ApiOperation(value = "验证码")
	@GetMapping(value = "/captcha", produces = "image/jpeg")
	public void captcha(HttpServletRequest request, HttpServletResponse response) {
		// 定义response输出类型为image/jpeg类型
		response.setDateHeader("Expires", 0);
		// Set standard HTTP/1.1 no-cache headers.
		response.setHeader("Cache-Control", "no-store, no-cache, must-revalidate");
		// Set IE extended HTTP/1.1 no-cache headers (use addHeader).
		response.addHeader("Cache-Control", "post-check=0, pre-check=0");
		// Set standard HTTP/1.0 no-cache header.
		response.setHeader("Pragma", "no-cache");
		// return a jpeg
		response.setContentType("image/jpeg");

		//-------------------生成验证码 begin --------------------------
		//获取验证码文本内容
		String text = defaultKaptcha.createText();
		System.out.println("验证码内容：" + text);
		//将验证码放入session中
		request.getSession().setAttribute("captcha", text);
		//根据文本内容创建图形验证码
		BufferedImage image = defaultKaptcha.createImage(text);
		ServletOutputStream outputStream = null;
		try {
			outputStream = response.getOutputStream();
			//输出流输出图片，格式jpg
			ImageIO.write(image, "jpg", outputStream);
			outputStream.flush();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if (null != outputStream) {
				try {
					outputStream.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
		//-------------------生成验证码 end ----------------------------
	}

}
```

* `produces`：设置返回数据类型及编码

#### 放行验证码接口

**SecurityConfig.java**

```java
@Override
public void configure(WebSecurity web) throws Exception {
   //放行静态资源
   web.ignoring().antMatchers(
         "/login",
         "/logout",
         "/css/**",
         "/js/**",
         "/index.html",
         "/img/**",
         "/fonts/**",
         "/favicon.ico",
         "/doc.html",
         "/webjars/**",
         "/swagger-resources/**",
         "/v2/api-docs/**",
         "/captcha");
}
```

#### 测试

![](yeb(back).images\Snipaste_2020-04-13_12-01-49.png)

### 校验验证码

#### 登录参数对象添加验证码属性

**AdminLoginParam.java**

```java
package com.yjxxt.server.pojo;

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@ApiModel(value = "AdminLogin对象", description = "")
public class AdminLoginParam {
    @ApiModelProperty(value = "用户名", required = true)
    private String username;
    @ApiModelProperty(value = "密码", required = true)
    private String password;
    @ApiModelProperty(value = "验证码", required = true)
    private String Code;
}
```

#### 登录方法添加验证码判断

**LoginController.java**

```java
@ApiOperation(value = "登录之后返回token")
	@PostMapping("/login")
	public RespBean login(@RequestBody AdminLoginParam adminLoginParam, HttpServletRequest request) {
		return adminService.login(adminLoginParam.getUsername(), adminLoginParam.getPassword(),
				adminLoginParam.getCode(), request);
	}
```

**IAdminService.java**

```java
/**
 * 登录返回token
 * @param username
 * @param password
 * @param code
 * @param request
 * @return
 */
RespBean login(String username, String password, String code, HttpServletRequest request);
```

**AdminServiceImpl.java**

```java
/**
 * 登录返回token
 *
 * @param username
 * @param password
 * @param code
 * @param request
 * @return
 */
@Override
public RespBean login(String username, String password, String code, HttpServletRequest request) {
   String captcha = (String) request.getSession().getAttribute("captcha");
   if (StringUtils.isBlank(code) || !captcha.equals(code)) {
      return RespBean.error("验证码填写错误！");
   }
   UserDetails userDetails = userDetailsService.loadUserByUsername(username);
   if (null == userDetails || !passwordEncoder.matches(password, userDetails.getPassword())) {
       return RespBean.error("用户名或密码不正确!");
   }
   if (!userDetails.isEnabled()){
       return RespBean.error("账号被禁用，请联系管理员!");
   }
   UsernamePasswordAuthenticationToken authentication =
         new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
   SecurityContextHolder.getContext().setAuthentication(authentication);
   String token = jwtTokenUtil.generateToken(userDetails);
   Map<String, String> tokenMap = new HashMap<>();
   tokenMap.put("token", token);
   tokenMap.put("tokenHead", tokenHead);
   return RespBean.success("登录成功", tokenMap);
}
```

#### 测试

首先获取验证码

![](yeb(back).images\Snipaste_2020-04-13_12-01-49.png)

输入错误的验证码，提示验证码填写错误

![](yeb(back).images\Snipaste_2020-04-13_15-05-33.png)

输入正确验证码，登录成功

![](yeb(back).images\Snipaste_2020-04-13_15-05-55.png)

## 菜单功能

### 修改菜单类

菜单分两级，一级菜单下面有子菜单，我们修改下Menu对象

**Menu.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.List;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_menu")
@ApiModel(value="Menu对象", description="")
public class Menu implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "url")
    private String url;

    @ApiModelProperty(value = "path")
    private String path;

    @ApiModelProperty(value = "组件")
    private String component;

    @ApiModelProperty(value = "菜单名")
    private String name;

    @ApiModelProperty(value = "图标")
    private String iconCls;

    @ApiModelProperty(value = "是否保持激活")
    private Boolean keepAlive;

    @ApiModelProperty(value = "是否要求权限")
    private Boolean requireAuth;

    @ApiModelProperty(value = "父id")
    private Integer parentId;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;

    @ApiModelProperty(value = "子菜单")
    TableField(exist = false)
    private List<Menu> children;
}
```

### MenuMapper

**MenuMapper.java**

```java
package com.yjxxt.server.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.yjxxt.server.pojo.Menu;

import java.util.List;

/**
 * <p>
 *  Mapper 接口
 * </p>
 *

 */
public interface MenuMapper extends BaseMapper<Menu> {

	/**
	 * 通过用户id获取菜单列表
	 * @param id
	 * @return
	 */
	List<Menu> getMenusByAdminId(Integer id);
}
```

**MenuMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yjxxt.server.mapper.MenuMapper">
    <resultMap id="Menus" type="com.yjxxt.server.pojo.Menu" extends="BaseResultMap">
        <collection property="children" ofType="com.yjxxt.server.pojo.Menu">
            <id column="id2" property="id" />
            <result column="url2" property="url" />
            <result column="path2" property="path" />
            <result column="component2" property="component" />
            <result column="name2" property="name" />
            <result column="iconCls2" property="iconCls" />
            <result column="keepAlive2" property="keepAlive" />
            <result column="requireAuth2" property="requireAuth" />
            <result column="parentId2" property="parentId" />
            <result column="enabled2" property="enabled" />
        </collection>
    </resultMap>

    <!--通过用户id获取菜单列表-->
	<select id="getMenusByAdminId" resultMap="Menus">
        SELECT
        DISTINCT m1.*,
            m2.id as id2,
            m2.component as component2,
            m2.enabled as enabled2,
            m2.iconCls as iconCls2,
            m2.keepAlive as keepAlive2,
            m2.name as name2,
            m2.parentId as parentId2,
            m2.requireAuth as requireAuth2,
            m2.path as path2
        FROM
            t_menu m1,
            t_menu m2,
            t_admin_role ar,
            t_menu_role mr
        WHERE
            m1.id = m2.parentId
            AND ar.adminId = #{id}
            AND ar.rid = mr.rid
            AND mr.mid = m2.id
            AND m2.enabled = true
        ORDER BY
            m1.id,
            m2.id
    </select>
</mapper>
```

* `collection`：关联关系，是实现一对多的关键
  * `property`：javabean中容器对应字段名
  * `ofType`：指定集合中元素的对象类型
  * `select`：使用另一个查询封装的结果
  * `column`：数据库中的列名，与`select`配合使用

### MenuService

**IMenuService.java**

```java
package com.yjxxt.server.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.yjxxt.server.pojo.Menu;

import java.util.List;

/**
 * <p>
 *  服务类
 * </p>
 *

 */
public interface IMenuService extends IService<Menu> {

	/**
	 * 通过用户id获取菜单列表
	 * @return
	 */
	List<Menu> getMenusByAdminId();
}
```

**MenuServiceImpl.java**

```java
package com.yjxxt.server.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.yjxxt.server.mapper.MenuMapper;
import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.Menu;
import com.yjxxt.server.service.IMenuService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * <p>
 *  服务实现类
 * </p>
 *

 */
@Service
public class MenuServiceImpl extends ServiceImpl<MenuMapper, Menu> implements IMenuService {

	@Autowired
	private MenuMapper menuMapper;

	/**
	 * 通过用户id获取菜单列表
	 * @return
	 */
	@Override
	public List<Menu> getMenusByAdminId() {
		return menuMapper.getMenusByAdminId(((Admin) SecurityContextHolder.getContext().getAuthentication().getPrincipal()).getId());
	}
}
```

### 修改接口请求路径

接口请求路径必须符合`menu`表里的定义。

**MenuController.java**

```java
package com.yjxxt.yebserver.controller;

import com.yjxxt.yebserver.pojo.Menu;
import com.yjxxt.yebserver.service.MenuService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * 系统配置Controller
 *

 * @since 1.0.0
 */
@RestController
@RequestMapping("/system")
public class SystemConfigController {

	@Autowired
	private MenuService menuService;

	@ApiOperation(value = "通过用户id获取菜单列表")
	@GetMapping("/menu")
	public List<Menu> getMenusByHrId(){
		return menuService.getMenusByHrId();
	}

}
```

### 测试

![](yeb(back).images\Snipaste_2020-03-28_23-11-08.png)

### Redis优化菜单

菜单大部分情况下不会出现变化，我们可以将其放入`Redis`加快加载速度，,关于`Redis`知识点请参考之前文档

#### 添加依赖

```xml
<!-- spring data redis 依赖 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- commons-pool2 对象池依赖 -->
<dependency>
   <groupId>org.apache.commons</groupId>
   <artifactId>commons-pool2</artifactId>
</dependency>
```

### 修改配置

```yaml
# Redis配置
redis:
  timeout: 10000ms                        # 连接超时时间
  host: 192.168.10.100                   # Redis服务器地址
  port: 6379                              # Redis服务器端口
  database: 0                             # 选择哪个库，默认0库
  lettuce:
    pool:
      max-active: 1024                    # 最大连接数，默认 8
      max-wait: 10000ms                   # 最大连接阻塞等待时间，单位毫秒，默认 -1
      max-idle: 200                       # 最大空闲连接，默认 8
      min-idle: 5                          # 最小空闲连接，默认 0
```

### 配置Redis

**RedisConfig.java**

```java
package com.yjxxt.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * Redis配置类
 *

 * @since 1.0.0
 */
@Configuration
public class RedisConfig {
   @Bean
   public RedisTemplate<String,Object> redisTemplate(LettuceConnectionFactory redisConnectionFactory){
      RedisTemplate<String,Object> redisTemplate = new RedisTemplate<>();
      //为string类型key设置序列器
      redisTemplate.setKeySerializer(new StringRedisSerializer());
      //为string类型value设置序列器
      redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
      //为hash类型key设置序列器
      redisTemplate.setHashKeySerializer(new StringRedisSerializer());
      //为hash类型value设置序列器
      redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
      redisTemplate.setConnectionFactory(redisConnectionFactory);
      return redisTemplate;
   }
}
```

### 修改菜单方法

**MenuServiceImpl.java**

```java
/**
 * 通过用户id获取菜单列表
 *
 * @return
 */
@Override
public List<Menu> getMenusByAdminId() {
   Integer adminId = ((Admin) SecurityContextHolder.getContext().getAuthentication().getPrincipal()).getId();
   ValueOperations<String, Object> valueOperations = redisTemplate.opsForValue();
   //查询缓存中是否有数据
   List<Menu> menus = (List<Menu>) valueOperations.get("menu_" + adminId);
   if (CollectionUtils.isEmpty(menus)){
      //如果没数据，数据库中查询，并设置到缓存中
      menus = menuMapper.getMenusByAdminId(adminId);
      valueOperations.set("menu_"+adminId,menus);
   }
   return menus;
}
```

### 测试

第一次查询时，Redis并没有菜单数据

![](yeb(back).images/Snipaste_2020-04-20_11-28-13.png)

会从数据库中查询菜单数据并设置到Redis中，此时再次查看发现Redis中已经有数据。再次查询会直接查询Redis中数据。

![](yeb(back).images/Snipaste_2020-04-20_11-30-07.png)

## 权限管理

### 权限管理RBAC基本概念

RBAC是基于角色的访问控制（`Role-Based Access Control` ）在RBAC中，权限与角色相关联，用户通过扮演适当的角色从而得到这些角色的权限。这样管理都是层级相互依赖的，权限赋予给角色，角色又赋予用户，这样的权限设计很清楚，管理起来很方便。

RBAC授权实际上是`Who` 、`What` 、`How` 三元组之间的关系，也就是`Who` 对`What` 进行`How` 的操作，简单说明就是谁对什么资源做了怎样的操作。

### RBAC表结构设计

#### 实体对应关系

用户-角色-资源实体间对应关系图分析如下

![](yeb(back).images\image-20200224111123875.png)



![](yeb(back).images\image-20200224111506037.png)



这里用户与角色实体对应关系为多对多,角色与资源对应关系同样为多对多关系，所以在实体设计上用户与角色间增加用户角色实体,将多对多的对应关系拆分为一对多，同理，角色与资源多对多对应关系拆分出中间实体对象权限实体。

#### 表结构设计

​		从上面实体对应关系分析,权限表设计分为以下基本的五张表结构:用户表(admin),角色表(role),用户角色表(admin_role),菜单表(menu),菜单权限表(menu_role)，表结构关系如下:

![](yeb(back).images\Snipaste_2020-03-30_15-58-15.png)

### 根据请求的url判断角色

#### 修改菜单类

在菜单类里添加角色列表属性

**Menu.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.List;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_menu")
@ApiModel(value="Menu对象", description="")
public class Menu implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "url")
    private String url;

    @ApiModelProperty(value = "path")
    private String path;

    @ApiModelProperty(value = "组件")
    private String component;

    @ApiModelProperty(value = "菜单名")
    private String name;

    @ApiModelProperty(value = "图标")
    private String iconCls;

    @ApiModelProperty(value = "是否保持激活")
    private Boolean keepAlive;

    @ApiModelProperty(value = "是否要求权限")
    private Boolean requireAuth;

    @ApiModelProperty(value = "父id")
    private Integer parentId;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;

    @ApiModelProperty(value = "子菜单")
    TableField(exist = false)
    private List<Menu> children;

    @ApiModelProperty(value = "角色列表")
    TableField(exist = false)
    private List<Role> roles;
}
```

#### MenuMapper

**MenuMapper.java**

```java
/**
 * 通过角色获取菜单列表
 *
 * @return
 */
List<Menu> getAllMenusWithRole();
```

**MenuMapper.xml**

```xml
 <resultMap id="MenusWithRole" type="com.yjxxt.server.pojo.Menu" extends="BaseResultMap">
     <collection property="roles" ofType="com.yjxxt.server.pojo.Role">
         <id column="rid" property="id" />
         <result column="rname" property="name"/>
         <result column="rnameZh" property="nameZh"/>
     </collection>
</resultMap>
  
   <!--通过角色获取菜单列表-->
   <select id="getAllMenusWithRole" resultMap="MenuWithRole">
       SELECT
               m.*,
               r.id AS rid,
               r.`name` AS rname,
               r.nameZh AS rnameZh
           FROM
               t_menu m,
               t_menu_role mr,
               t_role r
           WHERE
               m.id = mr.mid
               AND mr.rid = r.id
           ORDER BY
               m.id
   </select>
```

#### MenuService

**IMenuService.java**

```java
/**
 * 通过角色获取菜单列表
 * @return
 */
List<Menu> getAllMenusWithRole();
```

**MenuServiceImpl.java**

```java
/**
 * 通过角色获取菜单列表
 *
 * @return
 */
@Override
public List<Menu> getAllMenusWithRole() {
   return menuMapper.getAllMenusWithRole();
}
```

#### 添加过滤器根据url获取所需角色

**CustomFilter.java**

```java
package com.yjxxt.server.config.security.component;

import com.yjxxt.server.pojo.Menu;
import com.yjxxt.server.pojo.Role;
import com.yjxxt.server.service.IMenuService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.access.ConfigAttribute;
import org.springframework.security.access.SecurityConfig;
import org.springframework.security.web.FilterInvocation;
import org.springframework.security.web.access.intercept.FilterInvocationSecurityMetadataSource;
import org.springframework.stereotype.Component;
import org.springframework.util.AntPathMatcher;

import java.util.Collection;
import java.util.List;

/**
 * 权限控制
 * 根据请求url分析出请求所需角色
 *

 * @since 1.0.0
 */
@Component
public class CustomFilter implements FilterInvocationSecurityMetadataSource {

	@Autowired
	private IMenuService menuService;

	AntPathMatcher antPathMatcher = new AntPathMatcher();

	@Override
	public Collection<ConfigAttribute> getAttributes(Object object) throws IllegalArgumentException {
		//获取请求的url
		String requestUrl = ((FilterInvocation) object).getRequestUrl();
		//获取菜单
		List<Menu> menus = menuService.getAllMenusWithRole();
		for (Menu menu : menus) {
			//判断请求url与菜单角色是否匹配
			if (antPathMatcher.match(menu.getUrl(),requestUrl)){
				String[] str = menu.getRoles().stream().map(Role::getName).toArray(String[]::new);
				return SecurityConfig.createList(str);
			}
		}
		//没匹配的url默认为登录即可访问
		return SecurityConfig.createList("ROLE_LOGIN");
	}

	@Override
	public Collection<ConfigAttribute> getAllConfigAttributes() {
		return null;
	}

	@Override
	public boolean supports(Class<?> clazz) {
		return true;
	}
}
```

### 判断用户的角色

#### 修改管理员类

在管理员类里添加角色列表属性，并且可以获取到当前用户的角色

**Admin.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

/**
 * <p>
 *
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_admin")
@ApiModel(value = "Admin对象", description = "")
public class Admin implements Serializable, UserDetails {

	@ApiModelProperty(value = "id")
	@TableId(value = "id", type = IdType.AUTO)
	private Integer id;

	@ApiModelProperty(value = "姓名")
	private String name;

	@ApiModelProperty(value = "手机号码")
	private String phone;

	@ApiModelProperty(value = "住宅电话")
	private String telephone;

	@ApiModelProperty(value = "联系地址")
	private String address;

	@ApiModelProperty(value = "是否启用")
	private Boolean enabled;

	@ApiModelProperty(value = "用户名")
	private String username;

	@ApiModelProperty(value = "密码")
	private String password;

	@ApiModelProperty(value = "用户头像")
	private String userFace;

	@ApiModelProperty(value = "备注")
	private String remark;

	@ApiModelProperty(value = "权限")
	@TableField(exist = false)
	private List<Role> roles;

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		List<SimpleGrantedAuthority> authorities =
				roles.stream()
						.map(role -> new SimpleGrantedAuthority(role.getName()))
						.collect(Collectors.toList());
		return authorities;
	}

	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	@Override
	public boolean isEnabled() {
		return enabled;
	}
}
```

#### RoleMapper

**RoleMapper.java**

```java
/**
 * 根据用户id获取权限列表
 * @param adminId
 * @return
 */
List<Role> getRoles(Integer adminId);
```

**RoleMapper.xml**

```xml
<!--根据用户id获取权限列表-->
<select id="getRoles" resultType="com.yjxxt.server.pojo.Role">
       SELECT
           r.id,
           r.`name`,
           r.nameZh
       FROM
           t_role AS r
           LEFT JOIN t_admin_role AS ar ON ar.rid = r.id
       WHERE
           ar.adminId = #{adminId}
   </select>
```

#### AdminService

**IAdminService.java**

```java
/**
 * 根据用户id或者权限列表
 *
 * @param adminId
 * @return
 */
List<Role> getRoles(Integer adminId);
```

**AdminServiceImpl.java**

```java
/**
 * 根据用户id获取权限列表
 *
 * @param adminId
 * @return
 */
@Override
public List<Role> getRoles(Integer adminId) {
   return roleMapper.getRoles(adminId);
}
```

#### 在获取用户信息和登录方法中添加该方法，获取用户信息时能得到角色列表

**LoginController.java**

```java
@ApiOperation(value = "获取当前用户信息")
@GetMapping("/info")
public Admin getAdminInfo(Principal principal) {
   if (null == principal) {
      return null;
   }
   String username = principal.getName();
   Admin admin = adminService.getAdminByUserName(username);
   admin.setPassword(null);
   admin.setRoles(adminService.getRoles(admin.getId()));
   return admin;
}
```

**SecurityConfig.java**

```java
@Bean
public UserDetailsService userDetailsService() {
   //获取登录用户信息
   return username -> {
      Admin admin = adminService.getAdminByUserName(username);
      if (null != admin) {
         admin.setRoles(adminService.getRoles(admin.getId()));
         return admin;
      }
      tadminow new UsernameNotFoundException("用户名或密码不正确！");
   };
}
```

#### 添加过滤器判断用户的角色

**CustomUrlDecisionManager.java**

```java
package com.yjxxt.server.config.security.component;

import org.springframework.security.access.AccessDecisionManager;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.access.ConfigAttribute;
import org.springframework.security.authentication.AnonymousAuthenticationToken;
import org.springframework.security.authentication.InsufficientAuthenticationException;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.stereotype.Component;

import java.util.Collection;

/**
 * 权限控制
 * 判断用户角色
 *

 * @since 1.0.0
 */
@Component
public class CustomUrlDecisionManager implements AccessDecisionManager {

	@Override
	public void decide(Authentication authentication, Object object, Collection<ConfigAttribute> configAttributes) throws AccessDeniedException, InsufficientAuthenticationException {
		for (ConfigAttribute configAttribute : configAttributes) {
			//当前url所需角色
			String needRole = configAttribute.getAttribute();
			//判断角色是否为登录即可访问的角色，此角色在CustomFilter中设置
			if ("ROLE_LOGIN".equals(needRole)) {
				//判断是否登录
				if (authentication instanceof AnonymousAuthenticationToken) {
					tadminow new AccessDeniedException("尚未登录，请登录！");
				} else {
					return;
				}
			}
			//判断用户角色是否为url所需角色
			Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
			for (GrantedAuthority authority : authorities) {
				if (authority.getAuthority().equals(needRole)) {
					return;
				}
			}
		}
		tadminow new AccessDeniedException("权限不足，请联系管理员！");
	}

	@Override
	public boolean supports(ConfigAttribute attribute) {
		return true;
	}

	@Override
	public boolean supports(Class<?> clazz) {
		return true;
	}
}
```

### 配置Security

**SecurityConfig.java**

```java
package com.yjxxt.server.config.security;

import com.yjxxt.server.config.security.component.CustomFilter;
import com.yjxxt.server.config.security.component.CustomUrlDecisionManager;
import com.yjxxt.server.config.security.component.JwtAuthenticationTokenFilter;
import com.yjxxt.server.config.security.component.RestAuthenticationEntryPoint;
import com.yjxxt.server.config.security.component.RestfulAccessDeniedHandler;
import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.service.IAdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.ObjectPostProcessor;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.access.intercept.FilterSecurityInterceptor;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

/**
 * Security配置类
 *

 * @since 1.0.0
 */
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	private IAdminService adminService;
	@Autowired
	private RestAuthenticationEntryPoint restAuthenticationEntryPoint;
	@Autowired
	private RestfulAccessDeniedHandler restfulAccessDeniedHandler;
	@Autowired
	private CustomFilter customFilter;
	@Autowired
	private CustomUrlDecisionManager customUrlDecisionManager;

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.userDetailsService(userDetailsService()).passwordEncoder(passwordEncoder());
	}

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		//使用JWT，不需要csrf
		http.csrf()
				.disable()
				//基于token，不需要session
				.sessionManagement()
				.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
				.and()
				.authorizeRequests()
				//所有请求都要求认证
				.anyRequest()
				.authenticated()
            	//动态权限配置
				.withObjectPostProcessor(new ObjectPostProcessor<FilterSecurityInterceptor>() {
					@Override
					public <O extends FilterSecurityInterceptor> O postProcess(O object) {
						object.setAccessDecisionManager(customUrlDecisionManager);
						object.setSecurityMetadataSource(customFilter);
						return object;
					}
				})
				.and()
				//禁用缓存
				.headers()
				.cacheControl();
		//添加jwt 登录授权过滤器
		http.addFilterBefore(jwtAuthenticationTokenFilter(), UsernamePasswordAuthenticationFilter.class);
		//添加自定义未授权和未登录结果返回
		http.exceptionHandling()
				.accessDeniedHandler(restfulAccessDeniedHandler)
				.authenticationEntryPoint(restAuthenticationEntryPoint);
	}

	@Override
	public void configure(WebSecurity web) throws Exception {
		//放行静态资源
		web.ignoring().antMatchers(
				"/login",
            	"/logout",
				"/css/**",
				"/js/**",
				"/index.html",
				"/favicon.ico",
				"/doc.html",
				"/webjars/**",
				"/swagger-ui.html",
				"/swagger-resources/**",
				"/v2/api-docs/**",
				"/captcha");
	}

	@Bean
	public PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}


	@Override
	@Bean
	public UserDetailsService userDetailsService() {
		//获取登录用户信息
		return username -> {
			Admin admin = adminService.getAdminByUserName(username);
			if (null != admin) {
				admin.setRoles(adminService.getRoles(admin.getId()));
				return admin;
			}
			return null;
		};
	}

	@Bean
	public JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter() {
		return new JwtAuthenticationTokenFilter();
	}
}
```

### 测试

HelloController.java

```java
@GetMapping("/employee/basic/hello")
public String hello2(){
 return "/employee/basic/hello";
}


@GetMapping("/employee/advanced/hello")
public String hello3(){
 return "/employee/advanced/hello";
}
```

使用admin登录并进行访问

![](yeb(back).images\Snipaste_2020-03-30_19-22-46.png)

`employee/basic/hello`可以访问

![taomeng](yeb(back).images\Snipaste_2020-03-30_19-34-00.png)

`employee/advanced/hello`由于没有权限，无法访问

![](yeb(back).images\Snipaste_2020-03-30_19-34-09.png)

使用taomeng登录并进行访问

![](yeb(back).images\Snipaste_2020-03-30_19-36-33.png)

`employee/basic/hello`可以访问

![](yeb(back).images\Snipaste_2020-03-30_19-34-00.png)

`employee/advanced/hello`可以访问

![](yeb(back).images/Snipaste_2020-03-30_19-36-15.png)

## 职位管理

该模块提供职位的常用操作，例如查询职位，添加职位，更新职位，删除职位等方法

### 自定义日期格式

**Position.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_position")
@ApiModel(value="Position对象", description="")
public class Position implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职位")
    private String name;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

### 功能实现

**PositionController.java**

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.Position;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IPositionService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;

/**
 * <p>
 * 前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/system/basic/pos")
public class PositionController {

	@Autowired
	private IPositionService positionService;


	@ApiOperation(value = "获取所有职位信息")
	@GetMapping("/")
	public List<Position> getAllPositions() {
		return positionService.list();
	}


	@ApiOperation(value = "添加职位信息")
	@PostMapping("/")
	public RespBean addPosition(@RequestBody Position position) {
		position.setCreateDate(LocalDateTime.now());
		if (positionService.save(position)) {
			return RespBean.success("添加成功!");
		}
		return RespBean.error("添加失败!");
	}

	@ApiOperation(value = "更新职位信息")
	@PutMapping("/")
	public RespBean updatePosition(@RequestBody Position position) {
		if (positionService.updateById(position)) {
			return RespBean.success("更新成功!");
		}
		return RespBean.error("更新失败！");
	}


	@ApiOperation(value = "删除职位信息")
	@DeleteMapping("/{id}")
	public RespBean deletePosition(@PathVariable Integer id) {
		if (positionService.removeById(id)) {
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}


	@ApiOperation(value = "批量删除职位信息")
	@DeleteMapping("/")
	public RespBean deletePositionByIds(Integer[] ids) {
		if (positionService.removeByIds(Arrays.asList(ids))) {
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}

}
```

### 测试

测试前需要先进行登录

添加职位信息

![](yeb(back).images\Snipaste_2020-03-30_22-47-01.png)

查询职位信息

![](yeb(back).images\Snipaste_2020-03-30_22-45-26.png)

更新职位信息

![](yeb(back).images\Snipaste_2020-03-30_22-47-39.png)

删除职位信息

![](yeb(back).images\Snipaste_2020-03-30_22-47-56.png)

批量删除职位信息

![](yeb(back).images/Snipaste_2020-04-20_14-57-21.png)

## 全局异常处理

我们知道，系统中异常包括：编译时异常和运行时异常`RuntimeException`，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试通过手段减少运行时异常的发生。在开发中，不管是dao层、service层还是controller层，都有可能抛出异常，在Springmvc中，能将所有类型的异常处理从各处理过程解耦出来，既保证了相关处理过程的功能较单一，也实现了异常信息的统一处理和维护。
SpringBoot全局异常处理方式主要两种：

* 使用`@ControllerAdvice`和`@ExceptionHandler`注解。 
* 使用`ErrorController类`来实现

区别：

1. `@ControllerAdvice`方式只能处理控制器抛出的异常。此时请求已经进入控制器中。

2. `ErrorController类`方式可以处理所有的异常，包括未进入控制器的错误，比如404,401等错误

3. 如果应用中两者共同存在，则`@ControllerAdvice`方式处理控制器抛出的异常，`ErrorController类`方式处理未进入控制器的异常。

4. `@ControllerAdvice`方式可以定义多个拦截方法，拦截不同的异常类，并且可以获取抛出的异常信息，自由度更大。

GlobalException.java

```java
package com.yjxxt.yebserver.exception;

import com.yjxxt.yebserver.pojo.RespBean;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.sql.SQLException;
import java.sql.SQLIntegrityConstraintViolationException;

/**
 * 全局异常
 *

 * @since 1.0.0
 */
@RestControllerAdvice
public class GlobalExceptionHandler {


	@ExceptionHandler(SQLException.class)
	public RespBean mySQLException(SQLException e) {
		if (e instanceof SQLIntegrityConstraintViolationException) {
			return RespBean.error("该数据有关数据，操作失败！");
		}
		return RespBean.error("数据库异常，操作失败！");

	}
}
```

* `@ControllerAdvice`：表示这是一个控制器增强类，当控制器发生异常且符合类中定义的拦截异常类，将会被拦截
* `@ExceptionHandler`：定义拦截的异常类

**测试**

![](yeb(back).images\Snipaste_2020-03-31_10-53-09.png)

## 职称管理

该模块提供职称的常用操作，例如查询职称，添加职称，更新职称，删除职称等方法

### 自定义日期格式

**JobLevel.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_joblevel")
@ApiModel(value="Joblevel对象", description="")
public class Joblevel implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职称名称")
    private String name;

    @ApiModelProperty(value = "职称等级")
    private String titleLevel;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

### 功能实现

**JobLevelController.java**

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.Joblevel;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IJoblevelService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;
import java.util.Arrays;
import java.util.List;

/**
 * <p>
 *  前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/system/basic/joblevel")
public class JoblevelController {

	@Autowired
	private IJoblevelService joblevelService;


	@ApiOperation(value = "获取所有职称")
	@GetMapping("/")
	public List<Joblevel> getAllJobLevels(){
		return joblevelService.list();
	}


	@ApiOperation(value = "添加职称")
	@PostMapping("/")
	public RespBean addJobLevel(@RequestBody Joblevel jobLevel){
		jobLevel.setCreateDate(LocalDateTime.now());
		if (joblevelService.save(jobLevel)){
			return RespBean.success("添加成功!");
		}
		return RespBean.error("添加失败!");
	}


	@ApiOperation(value = "更新职称")
	@PutMapping("/")
	public RespBean updateJobLevel(@RequestBody Joblevel jobLevel){
		if (joblevelService.updateById(jobLevel)){
			return RespBean.success("更新成功!");
		}
		return RespBean.error("更新失败!");
	}


	@ApiOperation(value = "删除职称")
	@DeleteMapping("/{id}")
	public RespBean deleteJobLevel(@PathVariable Integer id){
		if (joblevelService.removeById(id)){
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}


	@ApiOperation(value = "批量删除职称")
	@DeleteMapping("/")
	public RespBean deleteJobLevelByIds(Integer[] ids){
		if (joblevelService.removeByIds(Arrays.asList(ids))){
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}

}
```

### 测试

测试前需要先进行登录

添加职称信息

![](yeb(back).images\Snipaste_2020-03-31_14-49-02.png)

编辑职称信息

![](yeb(back).images\Snipaste_2020-03-31_14-49-37.png)

查询职称信息

![](yeb(back).images\Snipaste_2020-03-31_14-49-55.png)

删除职称信息

![](yeb(back).images\Snipaste_2020-03-31_14-50-10.png)

批量删除职称信息

![](yeb(back).images/Snipaste_2020-04-20_16-34-02.png)

## 权限组

该模块提供角色的相关操作。在之前权限管理可以看到角色可以通过用户\_角色表与用户表关联进而分配用户不同的角色，也可以通过菜单\_角色与菜单表关联进而分配不同角色可以拥有不同菜单的权限。

在权限组模块，我们只关注通过菜单\_角色与菜单表关联进而分配不同角色可以拥有不同菜单的权限。因此本模块除了角色的相关操作：查询角色、添加角色、删除角色，还需要对菜单进行一些操作：查询所有菜单、根据角色查询菜单、更新角色的菜单

### 角色功能

**PermissController.java**

```java
package com.yjxxt.server.controller;

import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.pojo.Role;
import com.yjxxt.server.service.IRoleService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * 权限组
 *

 * @since 1.0.0
 */
@RestController
@RequestMapping("/system/basic/permiss")
public class PermissController {

	@Autowired
	private IRoleService roleService;


	@ApiOperation(value = "获取所有角色")
	@GetMapping("/")
	public List<Role> getAllRoles() {
		return roleService.list();
	}

	@ApiOperation(value = "添加角色")
	@PostMapping("/role")
	public RespBean addRole(@RequestBody Role role) {
		if (!role.getName().startsWith("ROLE_")) {
			role.setName("ROLE_" + role.getName());
		}
		if (roleService.save(role)) {
			return RespBean.success("添加成功!");
		}
		return RespBean.error("添加失败!");
	}


	@ApiOperation(value = "删除角色")
	@DeleteMapping("/role/{rid}")
	public RespBean deleteRole(@PathVariable Integer rid) {
		if (roleService.removeById(rid)) {
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}

}
```

#### 测试

测试前需要先进行登录

获取所有角色

![](yeb(back).images\Snipaste_2020-04-02_11-37-19.png)

添加角色

![](yeb(back).images\Snipaste_2020-04-02_11-43-28.png)

删除角色

![](yeb(back).images\Snipaste_2020-04-02_11-44-02.png)



### 菜单功能

#### MenuMapper

**MenuMapper.java**

```java
/**
 * 获取所有菜单
 *
 * @return
 */
List<Menu> getAllMenus();
```

**MenuMapper.xml**

```xml
<resultMap id="MenusWithChildren" type="com.yjxxt.server.pojo.Menu" extends="BaseResultMap">
    <id column="id1" property="id"/>
    <result column="name1" property="name"/>
    <collection property="children" ofType="com.yjxxt.server.pojo.Menu">
        <id column="id2" property="id"/>
        <result column="name2" property="name"/>
        <collection property="children" ofType="com.yjxxt.server.pojo.Menu">
            <id column="id3" property="id"/>
            <result column="name3" property="name"/>
        </collection>
    </collection>
</resultMap>


<!--获取所有菜单-->
<select id="getAllMenus" resultMap="MenuWithChildren">
     SELECT
         m1.id AS id1,
         m1.`name` AS name1,
         m2.id AS id2,
         m2.`name` AS name2,
         m3.id AS id3,
         m3.`name` AS name3
     FROM
         t_menu m1,
         t_menu m2,
         t_menu m3
     WHERE
         m1.id = m2.parentId
         AND m2.id = m3.parentId
         AND m3.enabled = TRUE
     ORDER BY
         m1.id,
         m2.id,
         m3.id
   </select>
```

#### MenuRoleMapper

**MenuRoleMapper.java**

```java
/**
 * 更新角色菜单
 * @param rid
 * @param mids
 * @return
 */
Integer insertRecord(@Param("rid") Integer rid, @Param("mids") Integer[] mids);
```

**MenuRoleMapper.xml**

```xml
<!--更新角色菜单-->
<insert id="insertRecord">
    insert into t_menu_role(mid,rid) values
    <foreach collection="mids" item="mid" separator=",">
        (#{mid},#{rid})
    </foreach>
</insert>
```

#### MenuService

**IMenuService.java**

```java
/**
 * 获取所有菜单
 *
 * @return
 */
List<Menu> getAllMenus();
```

**MenuServiceImpl.java**

```java
/**
 * 获取所有菜单
 * @return
 */
@Override
public List<Menu> getAllMenus() {
   return menuMapper.getAllMenus();
}
```

#### MenuRoleService

**IMenuRoleService.java**

```java
/**
 * 更新角色菜单
 * @param rid
 * @param mids
 * @return
 */
RespBean updateMenuRole(Integer rid, Integer[] mids);
```

**MenuRoleServiceImpl.java**

```java
/**
 * 更新角色菜单
 * @param rid
 * @param mids
 * @return
 */
@Override
@Transactional
public RespBean updateMenuRole(Integer rid, Integer[] mids) {
   menuRoleMapper.delete(new QueryWrapper<MenuRole>().eq("rid",rid));
   if (null==mids||0==mids.length){
	  return RespBean.success("更新成功！");
   }
   Integer result = menuRoleMapper.insertRecord(rid,mids);
   if (mids.length==result){
      return RespBean.success("更新成功!");
   }
   return RespBean.error("更新失败!");
}
```

#### PermissController

```java
@ApiOperation(value = "获取所有菜单")
@GetMapping("/menus")
public List<Menu> getAllMenus(){
   return menuService.getAllMenus();
}


@ApiOperation(value = "根据角色id查询菜单id")
@GetMapping("/mid/{rid}")
public List<Integer> getMidByRid(@PathVariable Integer rid){
   return menuRoleService.list(new QueryWrapper<MenuRole>().eq("rid", rid)).stream().map(MenuRole::getMid).collect(Collectors.toList());
}


@ApiOperation(value = "更新角色菜单")
@PutMapping("/")
public RespBean updateMenuRole(Integer rid,Integer[] mids){
   return menuRoleService.updateMenuRole(rid,mids);
}
```

#### 测试

获取所有菜单

![](yeb(back).images\Snipaste_2020-04-02_11-38-32.png)

根据角色id查询菜单id

![](yeb(back).images\Snipaste_2020-04-02_11-39-33.png)

更新角色菜单

![](yeb(back).images\Snipaste_2020-04-02_11-41-54.png)





## 存储过程

MySQL 5.0 版本开始支持存储过程。

存储过程（Stored Procedure）是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象。

存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数(需要时)来调用执行。

存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。

通俗来讲：存储过程其实就是能完成一定操作的一组SQL语句

**优点**

- 存储过程可封装，并隐藏复杂的商业逻辑。
- 存储过程可以回传值，并可以接受参数。
- 存储过程无法使用 SELECT 指令来运行，因为它是子程序，与查看表，数据表或用户定义函数不同。
- 存储过程可以用在数据检验，强制实行商业逻辑等。

**缺点**

- 存储过程，往往定制化于特定的数据库上，因为支持的编程语言不同。当切换到其他厂商的数据库系统时，需要重写原有的存储过程。
- 存储过程的性能调校与撰写，受限于各种数据库系统。

### 存储过程的创建和调用

- 存储过程就是具有名字的一段代码，用来完成一个特定的功能。
- 创建的存储过程保存在数据库的数据字典中。

#### 创建存储过程

```mysql
CREATE
    [DEFINER = { user | CURRENT_USER }]
　PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
 
proc_parameter:
    [ IN | OUT | INOUT ] param_name type
 
characteristic:
    COMMENT 'string'
  | LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
 
routine_body:
　　Valid SQL routine statement
 
[begin_label:] BEGIN
　　[statement_list]
　　　　……
END [end_label]
```

**MYSQL 存储过程中的关键语法**

声明语句结束符，可以自定义:

```mysql
DELIMITER $$
或
DELIMITER //
```

声明存储过程:

```mysql
CREATE PROCEDURE demo_in_parameter(IN p_in int)       
```

存储过程开始和结束符号:

```mysql
BEGIN .... END    
```

变量赋值:

```mysql
SET @p_in=1  
```

变量定义:

```mysql
DECLARE l_int int unsigned default 4000000; 
```

创建mysql存储过程、存储函数:

```mysql
create procedure 存储过程名(参数)
```

存储过程体:

```mysql
create function 存储函数名(参数)
```

#### 调用存储过程

```mysql
call sp_name[(传参)];
```

### 存储过程的参数

MySQL存储过程的参数用在存储过程的定义，共有三种参数类型,IN,OUT,INOUT,形式如：

```mysql
CREATEPROCEDURE 存储过程名([[IN |OUT |INOUT ] 参数名 数据类形...])
```

- `IN输入参数`：表示调用者向过程传入值（传入值可以是字面量或变量）
- `OUT输出参数`：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量）
- `INOUT输入输出参数`：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量）

**注意：**

1、如果过程没有参数，也必须在过程名后面写上小括号例：

```mysql
CREATE PROCEDURE sp_name ([proc_parameter[,...]]) ……
```

2、确保参数的名字不等于列的名字，否则在过程体中，参数名被当做列名来处理

**建议：**

- 输入值使用`in参数`。
- 返回值使用`out参数`。
- `inout参数`就尽量的少用。

### 变量

#### 变量定义

局部变量声明一定要放在存储过程体的开始：

```
DECLARE variable_name [,variable_name...] datatype [DEFAULT value];
```

其中，`datatype `为 MySQL 的数据类型，如: int, float, date,varchar(length)

#### 变量赋值

```mysql
SET 变量名 = 表达式值 [,variable_name = expression ...]
```

#### 用户变量

**在存储过程中使用用户变量**

```powershell
mysql > CREATE PROCEDURE GreetWorld( ) SELECT CONCAT(@greeting,' World');  
mysql > SET @greeting='Hello';  
mysql > CALL GreetWorld( );  
+----------------------------+  
| CONCAT(@greeting,' World') |  
+----------------------------+  
|  Hello World               |  
+----------------------------+
```

**在存储过程间传递全局范围的用户变量**

```powershell
mysql> CREATE PROCEDURE p1() SET @last_procedure='p1';  
mysql> CREATE PROCEDURE p2() SELECT CONCAT('Last procedure was ',@last_procedure);  
mysql> CALL p1( );  
mysql> CALL p2( );  
+-----------------------------------------------+  
| CONCAT('Last procedure was ',@last_proc       |  
+-----------------------------------------------+  
| Last procedure was p1                         |  
 +-----------------------------------------------+
```

**注意:**

- 1、用户变量名一般以@开头
- 2、滥用用户变量会导致程序难以理解及管理

### 注释

MySQL 存储过程可使用两种风格的注释

`--`：一般用于单行注释。

`/*  */`： 一般用于多行注释。

## 部门管理

### 存储过程

**addDep**

```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `addDep`(in depName varchar(32),in parentId int,in enabled boolean,out result int,out result2 int)
begin
  declare did int;
  declare pDepPath varchar(64);
  insert into t_department set name=depName,parentId=parentId,enabled=enabled;
  select row_count() into result;
  select last_insert_id() into did;
  set result2=did;
  select depPath into pDepPath from t_department where id=parentId;
  update t_department set depPath=concat(pDepPath,'.',did) where id=did;
  update t_department set isParent=true where id=parentId;
end
```

**deleteDep**

```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `deleteDep`(in did int,out result int)
begin
  declare ecount int;
  declare pid int;
  declare pcount int;
  declare a int;
  select count(*) into a from t_department where id=did and isParent=false;
  if a=0 then set result=-2;
  else
  select count(*) into ecount from t_employee where departmentId=did;
  if ecount>0 then set result=-1;
  else 
  select parentId into pid from t_department where id=did;
  delete from t_department where id=did and isParent=false;
  select row_count() into result;
  select count(*) into pcount from t_department where parentId=pid;
  if pcount=0 then update t_department set isParent=false where id=pid;
  end if;
  end if;
  end if;
end
```

### 修改部门类

部门类中添加子部门列表属性以及结果属性（存储过程需要）

**Department.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.List;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_department")
@ApiModel(value="Department对象", description="")
public class Department implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "部门名称")
    private String name;

    @ApiModelProperty(value = "父id")
    private Integer parentId;

    @ApiModelProperty(value = "路径")
    private String depPath;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;

    @ApiModelProperty(value = "是否上级")
    private Boolean isParent;

    @ApiModelProperty(value = "子部门列表")
    @TableField(exist = false)
    private List<Department> children;

    @ApiModelProperty(value = "添加返回结果，存储过程使用")
    @TableField(exist = false)
    private Integer result;
}
```

### DepartmentMapper

**DepartmentMapper.java**

```java
package com.yjxxt.server.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.yjxxt.server.pojo.Department;

import java.util.List;

/**
 * <p>
 * Mapper 接口
 * </p>
 *

 */
public interface DepartmentMapper extends BaseMapper<Department> {

	/**
	 * 获取所有部门
	 *
	 * @param parentId
	 * @return
	 */
	List<Department> getAllDepartmentsByParentId(Integer parentId);

	/**
	 * 添加部门
	 *
	 * @param dep
	 */
	void addDep(Department dep);

	/**
	 * 删除部门
	 *
	 * @param dep
	 */
	void deleteDep(Department dep);
}
```

**DepartmentMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yjxxt.server.mapper.DepartmentMapper">
	
	<resultMap id="DepartmentWithChildren" type="com.yjxxt.server.pojo.Department" extends="BaseResultMap">
		<collection property="children" ofType="com.yjxxt.server.pojo.Department"
           select="com.yjxxt.server.mapper.DepartmentMapper.getAllDepartmentsByParentId" 			column="id">
		</collection>
	</resultMap>

	<!--添加部门-->
	<select id="addDep" statementType="CALLABLE">
    	call addDep(#{name,mode=IN,jdbcType=VARCHAR},#{parentId,mode=IN,jdbcType=INTEGER},#{enabled,mode=IN,jdbcType=BOOLEAN},#{result,mode=OUT,jdbcType=INTEGER},#{id,mode=OUT,jdbcType=INTEGER})
    </select>

	<!--删除部门-->
	<select id="deleteDep" statementType="CALLABLE">
        call deleteDep(#{id,mode=IN,jdbcType=INTEGER},#{result,mode=OUT,jdbcType=INTEGER})
    </select>

	<!--获取所有部门-->
	<select id="getAllDepartmentsByParentId" resultMap="DepartmentWithChildren">
		select
		<include refid="Base_Column_List"/>
		from t_department
		where parentId = #{parentId}
	</select>

</mapper>
```

* `statementType`：标记操作SQL的对象
  * `STATEMENT`：直接操作sql，不进行预编译，获取数据：`$`
  * `PREPARED`：预处理，参数，进行预编译，获取数据：`#`，默认
  * `CALLABLE`：执行存储过程

### DepartmentService

**IDepartmentService.java**

```java
package com.yjxxt.server.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.yjxxt.server.pojo.Department;
import com.yjxxt.server.pojo.RespBean;

import java.util.List;

/**
 * <p>
 *  服务类
 * </p>
 *

 */
public interface IDepartmentService extends IService<Department> {

	/**
	 * 获取所有部门
	 * @return
	 */
	List<Department> getAllDepartments();

	/**
	 * 添加部门
	 * @param dep
	 * @return
	 */
	RespBean addDep(Department dep);

	/**
	 * 删除部门
	 * @param id
	 * @return
	 */
	RespBean deleteDep(Integer id);
}
```

**DepartmentServiceImpl.java**

```java
package com.yjxxt.server.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.yjxxt.server.mapper.DepartmentMapper;
import com.yjxxt.server.pojo.Department;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IDepartmentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * <p>
 *  服务实现类
 * </p>
 *

 */
@Service
public class DepartmentServiceImpl extends ServiceImpl<DepartmentMapper, Department> implements IDepartmentService {
	@Autowired
	private DepartmentMapper departmentMapper;

	/**
	 * 获取所有部门
	 * @return
	 */
	@Override
	public List<Department> getAllDepartments() {
		return departmentMapper.getAllDepartmentsByParentId(-1);
	}

	/**
	 * 添加部门
	 * @param dep
	 * @return
	 */
	@Override
	public RespBean addDep(Department dep) {
		dep.setEnabled(true);
		departmentMapper.addDep(dep);
		if (1==dep.getResult()){
			return RespBean.success("添加成功!",dep);
		}
		return RespBean.error("添加失败!");
	}

	/**
	 * 删除部门
	 * @param id
	 * @return
	 */
	@Override
	public RespBean deleteDep(Integer id) {
		Department dep = new Department();
		dep.setId(id);
		departmentMapper.deleteDep(dep);
		if (-2==dep.getResult()){
			return RespBean.error("该部门下有子部门，删除失败！");
		}
		if (-1==dep.getResult()){
			return RespBean.error("该部门下有员工，删除失败！");
		}
		if (1==dep.getResult()){
			return RespBean.success("删除成功！");
		}
		return RespBean.error("删除失败！");
	}
}
```

### DepartmentController

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.Department;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IDepartmentService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * <p>
 *  前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/system/basic/department")
public class DepartmentController {

	@Autowired
	private IDepartmentService departmentService;


	@ApiOperation(value = "获取所有部门")
	@GetMapping("/")
	public List<Department> getAllDepartments(){
		return departmentService.getAllDepartments();
	}


	@ApiOperation(value = "添加部门")
	@PostMapping("/")
	public RespBean addDep(@RequestBody Department dep){
		return departmentService.addDep(dep);
	}


	@ApiOperation(value = "删除部门")
	@DeleteMapping("/{id}")
	public RespBean deleteDep(@PathVariable Integer id){
		return departmentService.deleteDep(id);
	}

}
```

### 测试

测试前需要先进行登录

获取所有部门

![](yeb(back).images\Snipaste_2020-04-02_15-35-06.png)

添加部门

![](yeb(back).images\Snipaste_2020-04-02_14-26-50.png)

删除部门

部门下有员工的情况

![](yeb(back).images\Snipaste_2020-04-02_15-11-23.png)

部门下有子部门的情况

![](yeb(back).images\Snipaste_2020-04-02_15-12-22.png)

部门下既没有子部门也没有员工的情况

![](yeb(back).images\Snipaste_2020-04-02_15-12-35.png)

## 操作员管理

### 操作员功能

#### AdminMapper

**AdminMapper.java**

```java
package com.yjxxt.server.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.yjxxt.server.pojo.Admin;
import org.apache.ibatis.annotations.Param;

import java.util.List;

/**
 * <p>
 *  Mapper 接口
 * </p>
 *

 */
public interface AdminMapper extends BaseMapper<Admin> {

   /**
    * 获取所有操作员
    * @param id
    * @param keywords
    * @return
    */
   List<Admin> getAllAdmins(@Param("id") Integer id, @Param("keywords") String keywords);
}
```

**AdminMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yjxxt.server.mapper.AdminMapper">

    <resultMap id="AdminWithRole" type="com.yjxxt.server.pojo.Admin" extends="BaseResultMap">
        <collection property="roles" ofType="com.yjxxt.server.pojo.Role">
            <id column="rid" property="id" />
            <result column="rname" property="name" />
            <result column="rnameZh" property="nameZh" />
        </collection>
    </resultMap>

    <!--获取所有操作员-->
   <select id="getAllAdmins" resultMap="AdminWithRole">
        SELECT
        a.id,
        a.`name`,
        a.phone,
        a.telephone,
        a.address,
        a.enabled,
        a.username,
        a.userface,
        a.remark,
        r.id AS rid,
        r.`name` AS rname,
        r.nameZh AS rnameZh
        FROM
        t_admin AS a
        LEFT JOIN t_admin_role AS ar ON ar.adminId = a.id
        LEFT JOIN t_role AS r ON ar.rid = r.id
        WHERE
        a.id != #{id}
        <if test="null!=keywords and ''!=keywords">
            AND a.name like concat ('%',#{keywords},'%')
        </if>
        ORDER BY
        a.id
    </select>

</mapper>
```

#### AdminService

**IAdminService.java**

```java
/**
 * 获取所有操作员
 * @param keywords
 * @return
 */
List<Admin> getAllAdmins(String keywords);
```

**AdminServiceImpl.java**

```java
/**
    * 获取所有操作员
    * @param keywords
    * @return
    */
   @Override
   public List<Admin> getAllAdmins(String keywords) {
      return adminMapper.getAllAdmins(AdminUtils.getCurrentAdmin().getId(),keywords);
   }
}
```

#### AdminController

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IAdminService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * <p>
 * 前端控制器
 * </p>
 *

 */
@Api(tags = "AdminController")
@RestController
@RequestMapping("/system/admin")
public class AdminController {

   @Autowired
   private IAdminService adminService;

   @ApiOperation(value = "获取所有操作员")
   @GetMapping("/")
   public List<Admin> getAllAdmins(String keywords){
      return adminService.getAllAdmins(keywords);
   }


   @ApiOperation(value = "更新操作员")
   @PutMapping("/")
   public RespBean updateAdmin(@RequestBody Admin admin){
      if (adminService.updateById(admin)){
         return RespBean.success("更新成功!");
      }
      return RespBean.error("更新失败");
   }


   @ApiOperation(value = "删除操作员")
   @DeleteMapping("/{id}")
   public RespBean deleteAdmin(@PathVariable Integer id){
      if (adminService.removeById(id)){
         return RespBean.success("删除成功!");
      }
      return RespBean.error("删除失败");
   }
}
```

#### 测试

测试前需要先进行登录

获取所有操作员

![](yeb(back).images\Snipaste_2020-04-03_15-03-40.png)

更新操作员，发现报错，报错信息如下

![](yeb(back).images/Snipaste_2020-04-21_15-10-34.png)

原因：Admin不仅实现了`SpringSecurity`中的`UserDetails`，重写了`isEnabled()`方法而且用了lombok的`@Data`注解自动生成了getter/setter方法，其中就有`enabled`属性的getter方法`getEnabled()`。Mybatis映射的时候不知道该映射哪一个方法因此报错。

解决：在`enabled`上加注解使其不生成getter方法

```java
@ApiModelProperty(value = "是否启用")
@Getter(AccessLevel.NONE)
private Boolean enabled;
```

再次测试,发现可以更新操作员成功

![](yeb(back).images/Snipaste_2020-04-21_15-06-15.png)



删除操作员

![](yeb(back).images/Snipaste_2020-04-21_15-16-13.png)









### 操作员角色功能

#### AdminRoleMapper

**AdminRoleMapper.java**

```java
package com.yjxxt.server.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.yjxxt.server.pojo.AdminRole;
import org.apache.ibatis.annotations.Param;

/**
 * <p>
 *  Mapper 接口
 * </p>
 *

 */
public interface AdminRoleMapper extends BaseMapper<AdminRole> {

   /**
    * 添加操作员角色
    * @param adminId
    * @param rids
    * @return
    */
   Integer addRole(@Param("adminId") Integer adminId, @Param("rids") Integer[] rids);
}
```

**AdminRoleMapper.xml**

```xml
<!--添加操作员角色-->
<insert id="addRole">
       insert into t_admin_role(adminId,rid) values
       <foreach collection="rids" item="rid" separator=",">
           (#{adminId},#{rid})
       </foreach>
   </insert>
```

#### AdminService

**IAdminService.java**

```java
/**
 * 更新操作员角色
 * @param adminId
 * @param rids
 * @return
 */
RespBean updateAdminRole(Integer adminId, Integer[] rids);
```

**AdminServiceImpl.java**

```java
/**
 * 更新操作员角色
 * @param adminId
 * @param rids
 * @return
 */
@Override
@Transactional
public RespBean updateAdminRole(Integer adminId, Integer[] rids) {
   adminRoleMapper.delete(new QueryWrapper<AdminRole>().eq("adminId",adminId));
   Integer result = adminRoleMapper.addRole(adminId,rids);
   if (rids.length==result){
      return RespBean.success("更新成功!");
   }
   return RespBean.error("更新失败!");
}
```

#### AdminController

```java
@ApiOperation(value = "获取所有角色")
@GetMapping("/roles")
public List<Role> getAllRoles(){
   return roleService.list();
}

@ApiOperation(value = "更新操作员角色")
@PutMapping("/role")
public RespBean updateAdminRole(Integer adminId,Integer[] rids){
   return adminService.updateAdminRole(adminId,rids);
}
```

#### 测试

测试前需要先进行登录

获取所有角色

![](yeb(back).images/Snipaste_2020-04-21_15-40-08.png)

更新操作员角色

![](yeb(back).images/Snipaste_2020-04-21_15-41-35.png)

## 员工管理

### 员工查询

#### 分页插件配置

**MyBatisPlusConfig.java**

```java
package com.yjxxt.server.config;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * MyBatis分页配置
 *

 * @since 1.0.0
 */
@Configuration
public class MyBatisPlusConfig {

   @Bean
   public PaginationInterceptor paginationInterceptor(){
      return new PaginationInterceptor();
   }
}
```

#### 添加分页公共返回对象

**RespPageBean.java**

```java
package com.yjxxt.server.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.List;

/**
 * 通用分页返回结果对象
 *

 * @since 1.0.0
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class RespPageBean {

	private Long total;
	private List<?> data;
}
```

#### 添加全局日期格式转换

**DateConverter.java**

```java
package com.yjxxt.server.converter;

import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

/**
 * 日期转换
 *

 * @since 1.0.0
 */
@Component
public class DateConverter implements Converter<String, LocalDate> {

	@Override
	public LocalDate convert(String source) {
		try {
			return LocalDate.parse(source, DateTimeFormatter.ofPattern("yyyy-MM-dd"));
		}catch (Exception e){
			e.printStackTrace();
		}
		return null;
	}
}
```

#### 修改员工类

员工类主要2个修改

1. 返回的日期类添加格式转换
2. 添加与员工关联的属性例如民族，政治面貌等

**Employee.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDate;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_employee")
@ApiModel(value="Employee对象", description="")
public class Employee implements Serializable {


    @ApiModelProperty(value = "员工编号")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "员工姓名")
    private String name;

    @ApiModelProperty(value = "性别")
    private String gender;

    @ApiModelProperty(value = "出生日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate birthday;

    @ApiModelProperty(value = "身份证号")
    private String idCard;

    @ApiModelProperty(value = "婚姻状况")
    private String wedlock;

    @ApiModelProperty(value = "民族")
    private Integer nationId;

    @ApiModelProperty(value = "籍贯")
    private String nativePlace;

    @ApiModelProperty(value = "政治面貌")
    private Integer politicId;

    @ApiModelProperty(value = "邮箱")
    private String email;

    @ApiModelProperty(value = "电话号码")
    private String phone;

    @ApiModelProperty(value = "联系地址")
    private String address;

    @ApiModelProperty(value = "所属部门")
    private Integer departmentId;

    @ApiModelProperty(value = "职称ID")
    private Integer jobLevelId;

    @ApiModelProperty(value = "职位ID")
    private Integer posId;

    @ApiModelProperty(value = "聘用形式")
    private String engageForm;

    @ApiModelProperty(value = "最高学历")
    private String tiptopDegree;

    @ApiModelProperty(value = "所属专业")
    private String specialty;

    @ApiModelProperty(value = "毕业院校")
    private String school;

    @ApiModelProperty(value = "入职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate beginDate;

    @ApiModelProperty(value = "在职状态")
    private String workState;

    @ApiModelProperty(value = "工号")
    private String workID;

    @ApiModelProperty(value = "合同期限")
    private Double contractTerm;

    @ApiModelProperty(value = "转正日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate conversionTime;

    @ApiModelProperty(value = "离职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate notWorkDate;

    @ApiModelProperty(value = "合同起始日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate beginContract;

    @ApiModelProperty(value = "合同终止日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDate endContract;

    @ApiModelProperty(value = "工龄")
    private Integer workAge;

    @ApiModelProperty(value = "工资账套ID")
    private Integer salaryId;

    @ApiModelProperty(value = "民族")
    @TableField(exist = false)
    private Nation nation;

    @ApiModelProperty(value = "政治面貌")
    @TableField(exist = false)
    private PoliticsStatus politicsStatus;

    @ApiModelProperty(value = "部门")
    @TableField(exist = false)
    private Department department;

    @ApiModelProperty(value = "职称")
    @TableField(exist = false)
    private Joblevel joblevel;

    @ApiModelProperty(value = "职位")
    @TableField(exist = false)
    private Position position;
}
```

#### EmployeeMapper

**EmployeeMapper.java**

```java
/**
 * 获取所有员工(分页)
 * @param page
 * @param employee
 * @param beginDateScope
 */
IPage<Employee> getEmployeeByPage(Page<Employee> page, @Param("employee") Employee employee,
                                  @Param("beginDateScope") LocalDate[] beginDateScope);
```

**EmployeeMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yjxxt.server.mapper.EmployeeMapper">

	<resultMap id="EmployeeInfo" type="com.yjxxt.server.pojo.Employee" extends="BaseResultMap">
		<association property="nation" javaType="com.yjxxt.server.pojo.Nation">
			<id column="nid" property="id"></id>
			<result column="nname" property="name"></result>
		</association>
		<association property="politicsStatus" javaType="com.yjxxt.server.pojo.PoliticsStatus">
			<id column="pid" property="id"></id>
			<result column="pname" property="name"></result>
		</association>
		<association property="department" javaType="com.yjxxt.server.pojo.Department">
			<id column="did" property="id"></id>
			<result column="dname" property="name"></result>
		</association>
		<association property="joblevel" javaType="com.yjxxt.server.pojo.Joblevel">
			<id column="jid" property="id"></id>
			<result column="jname" property="name"></result>
		</association>
		<association property="position" javaType="com.yjxxt.server.pojo.Position">
			<id column="posid" property="id"></id>
			<result column="posname" property="name"></result>
		</association>
	</resultMap>

	<!--获取所有员工(分页)-->
	<select id="getEmployeeByPage" resultMap="EmployeeInfo">
		SELECT
		e.*,
		n.id As nid,
		n.`name` AS nname,
		p.id AS pid,
		p.`name` AS pname,
		d.id AS did,
		d.`name` AS dname,
		j.id AS jid,
		j.`name` AS jname,
		pos.id AS posid,
		pos.`name` AS posname
		FROM
		t_employee e,
		t_nation n,
		t_politics_status p,
		t_department d,
		t_joblevel j,
		t_position pos
		WHERE
		e.nationId = n.id
		AND e.politicId = p.id
		AND e.departmentId = d.id
		AND e.jobLevelId = j.id
		AND e.posId = pos.id
		<if test="null!=employee.name and ''!=employee.name">
			AND e.name like concat('%',#{employee.name},'%')
		</if>
		<if test="null!=employee.politicId">
			AND e.politicId = #{employee.politicId}
		</if>
		<if test="null!=employee.nationId">
			AND e.nationId = #{employee.nationId}
		</if>
		<if test="null!=employee.jobLevelId">
			AND e.jobLevelId = #{employee.jobLevelId}
		</if>
		<if test="null!=employee.posId">
			AND e.posId = #{employee.posId}
		</if>
		<if test="null!=employee.departmentId">
			AND e.departmentId = #{employee.departmentId}
		</if>
		<if test="null!=employee.engageForm and ''!=employee.engageForm">
			AND e.engageForm = #{employee.engageForm}
		</if>
		<if test="null!=beginDateScope and 2==beginDateScope.length">
			AND e.beginDate between #{beginDateScope[0]} and #{beginDateScope[1]}
		</if>
        ORDER BY e.id
	</select>
</mapper>
```

#### EmployeeService

**IEmployeeService.java**

```java
/**
 * 获取所有员工(分页)
 * @param currentPage
 * @param size
 * @param employee
 * @param beginDateScope
 * @return
 */
RespPageBean getEmployeeByPage(Integer currentPage, Integer size, Employee employee, LocalDate[] beginDateScope);
```

**EmployeeServiceImpl.java**

```java
/**
 * 获取所有员工(分页)
 * @param currentPage
 * @param size
 * @param employee
 * @param beginDateScope
 * @return
 */
@Override
public RespPageBean getEmployeeByPage(Integer currentPage, Integer size, Employee employee, LocalDate[] beginDateScope) {
   //开启分页
   Page<Employee> page = new Page<>(currentPage, size);
   IPage<Employee> employeePage = employeeMapper.getEmployeeByPage(page, employee, beginDateScope);
   RespPageBean respPageBean = new RespPageBean(employeePage.getTotal(),employeePage.getRecords());
   return respPageBean;
}
```

#### EmployeeController

```java
@ApiOperation(value = "获取所有员工(分页)")
@GetMapping("/")
public RespPageBean getEmployeeByPage(@RequestParam(defaultValue = "1") Integer currentPage,
                                      @RequestParam(defaultValue = "10") Integer size, Employee employee, LocalDate[] beginDateScope) {
   return employeeService.getEmployeeByPage(currentPage, size, employee,beginDateScope);
}
```

#### 测试

测试前需要先进行登录

查询员工

![](yeb(back).images\Snipaste_2020-04-03_16-11-50.png)

### 员工添加

员工添加时民族，政治面貌，职称，职位等信息为下拉框筛选，因此需要查询所有民族，政治面貌，职称，职位等信息。工号默认为自动递增，不需要填写，因此需要查询当前最大工号

#### EmployeeService

**IEmployeeService.java**

```java
/**
 * 获取工号
 * @return
 */
RespBean maxWorkId();

/**
 * 添加员工
 * @param employee
 * @return
 */
RespBean insertEmployee(Employee employee);
```

**EmployeeServiceImpl.java**

```java
/**
 * 获取工号
 * @return
 */
@Override
public RespBean maxWorkId() {
   List<Map<String,Object>> maps = employeeMapper.selectMaps(new QueryWrapper<Employee>().select("max(workID)"));
   return RespBean.success(null,String.format("%08d",Integer.parseInt(maps.get(0).get("max(workID)").toString())+1));
}


/**
 * 添加员工
 * @param employee
 * @return
 */
@Override
public RespBean insertEmployee(Employee employee) {
  //处理合同期限，保留2位小数
  LocalDate beginContract = employee.getBeginContract();
  LocalDate endContract = employee.getEndContract();
  long days = beginContract.until(endContract, CadminonoUnit.DAYS);
  DecimalFormat decimalFormat = new DecimalFormat("##.00");
  employee.setContractTerm(Double.parseDouble(decimalFormat.format(days/365.00)));
  if (1==employeeMapper.insert(employee)) {
      return RespBean.success("添加成功!");
  }
  return RespBean.error("添加失败!");
}
```

#### EmployeeController

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.Department;
import com.yjxxt.server.pojo.Employee;
import com.yjxxt.server.pojo.Joblevel;
import com.yjxxt.server.pojo.Nation;
import com.yjxxt.server.pojo.PoliticsStatus;
import com.yjxxt.server.pojo.Position;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.pojo.RespPageBean;
import com.yjxxt.server.service.IDepartmentService;
import com.yjxxt.server.service.IEmployeeService;
import com.yjxxt.server.service.IJoblevelService;
import com.yjxxt.server.service.INationService;
import com.yjxxt.server.service.IPoliticsStatusService;
import com.yjxxt.server.service.IPositionService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDate;
import java.util.List;

/**
 * <p>
 *  前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/employee/basic")
public class EmployeeController {

	@Autowired
	private IEmployeeService employeeService;
	@Autowired
	private INationService nationService;
	@Autowired
	private IPoliticsStatusService politicsStatusService;
	@Autowired
	private IJoblevelService joblevelService;
	@Autowired
	private IPositionService positionService;
	@Autowired
	private IDepartmentService departmentService;

	@ApiOperation(value = "添加员工")
	@PostMapping("/")
	public RespBean addEmp(@RequestBody Employee employee) {
		return employeeService.insertEmployee(employee);
	}

	@ApiOperation(value = "获取所有民族")
	@GetMapping("/nations")
	public List<Nation> getAllNations() {
		return nationService.list();
	}


	@ApiOperation(value = "获取所有政治面貌")
	@GetMapping("/politicsstatus")
	public List<PoliticsStatus> getAllPoliticsStatus() {
		return politicsStatusService.list();
	}


	@ApiOperation(value = "获取所有职称")
	@GetMapping("/joblevels")
	public List<Joblevel> getAllJobLevels() {
		return joblevelService.list();
	}


	@ApiOperation(value = "获取所有职位")
	@GetMapping("/positions")
	public List<Position> getAllPositions() {
		return positionService.list();
	}


	@ApiOperation(value = "获取工号")
	@GetMapping("/maxWorkID")
	public RespBean maxWorkID() {
		return employeeService.maxWorkId();
	}


	@ApiOperation(value = "获取所有部门")
	@GetMapping("/deps")
	public List<Department> getAllDepartments(){
		return departmentService.getAllDepartments();
	}

}
```

#### 测试

测试前需要先进行登录

添加员工

![](yeb(back).images/Snipaste_2020-04-21_19-20-08.png)

### 员工更新及删除

**EmployeeController.java**

```java
@ApiOperation(value = "删除员工")
@DeleteMapping("/{id}")
public RespBean deleteEmp(@PathVariable Integer id){
   if (employeeService.removeById(id)){
      return RespBean.success("删除成功!");
   }
   return RespBean.error("删除失败!");
}


@ApiOperation(value = "更新员工")
@PutMapping("/")
public RespBean updateEmp(@RequestBody Employee employee) {
   if (employeeService.updateById(employee)){
      return RespBean.success("更新成功!");
   }
   return RespBean.error("更新失败!");
}
```

**测试**

测试前需要先进行登录

更新员工

![](yeb(back).images/Snipaste_2020-04-21_19-21-04.png)

删除员工

![](yeb(back).images/Snipaste_2020-04-21_19-21-24.png)

## Easy POI

### Apache POI简介

开发中经常会设计到excel的处理，如导出Excel，导入Excel到数据库中，操作Excel等。 

Apache POI 简介是用Java编写的免费开源的跨平台的 Java API，Apache POI提供API给Java程式对Microsoft Office（Excel、WORD、PowerPoint、Visio等）格式档案读和写的功能。POI为“Poor Obfuscation Implementation”的首字母缩写，意为“可怜的模糊实现”。

​     官方主页：  http://poi.apache.org/index.html

​	 API文档：   http://poi.apache.org/apidocs/index.html

缺点：

1. 对于复杂的Excel模板样式需要编写大量的代码实现

2. 大数据量的读取/导出效率低下，甚至可能内存溢出

### EasyPOI简介

为了解决上述poi的缺点，国内有很多开源项目对poi进行了封装，大大减少代码量，使其能够更简单的被我们使用并提高开发效率，例如EasyPoi,Excel4J,HuTools等优秀的开源项目。我们这次以EasyPoi为例

​     easypoi功能如同名字easy,主打的功能就是容易,让一个没见接触过poi的人员 就可以方便的写出Excel导出,Excel模板导出,Excel导入,Word模板导出,通过简单的注解和模板 语言(熟悉的表达式语法),完成以前复杂的写法。

​     官方主页：  https://gitee.com/lemur/easypoi?_from=gitee_search

​	  API文档：   https://opensource.afterturn.cn/doc/easypoi.html#201

​	  测试项目：  https://gitee.com/lemur/easypoi-test

   特点：

1. 设计精巧,使用简单

2. 接口丰富,扩展简单

3. 默认值多,write less do more

4. AbstractView支持,web导出可以简单明了

### 导出员工数据

#### 添加依赖

```xml
<!--easy poi依赖-->
<dependency>
   <groupId>cn.afterturn</groupId>
   <artifactId>easypoi-spring-boot-starter</artifactId>
   <version>4.1.3</version>
</dependency>
```

#### 修改员工类

员工类使用`Excel`定义了需要导出的属性，`name`为导出的列名，`width`可以定义列的宽度，`format`可以定义导入和导出的日期格式

员工类中还有其他类作为属性，例如民族，政治面貌，职称，职位等。这些使用`ExcelEntity`标记为实体类

**Employee.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import cn.afterturn.easypoi.excel.annotation.ExcelEntity;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDate;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_employee")
@ApiModel(value="Employee对象", description="")
public class Employee implements Serializable {


    @ApiModelProperty(value = "员工编号")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "员工姓名")
    @Excel(name = "员工姓名")
    private String name;

    @ApiModelProperty(value = "性别")
    @Excel(name = "性别")
    private String gender;

    @ApiModelProperty(value = "出生日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "出生日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate birthday;

    @ApiModelProperty(value = "身份证号")
    @Excel(name = "身份证号",width = 30)
    private String idCard;

    @ApiModelProperty(value = "婚姻状况")
    @Excel(name = "婚姻状况")
    private String wedlock;

    @ApiModelProperty(value = "民族")
    private Integer nationId;

    @ApiModelProperty(value = "籍贯")
    @Excel(name = "籍贯")
    private String nativePlace;

    @ApiModelProperty(value = "政治面貌")
    private Integer politicId;

    @ApiModelProperty(value = "邮箱")
    @Excel(name = "邮箱",width = 30)
    private String email;

    @ApiModelProperty(value = "电话号码")
    @Excel(name = "电话号码",width = 15)
    private String phone;

    @ApiModelProperty(value = "联系地址")
    @Excel(name = "联系地址",width = 40)
    private String address;

    @ApiModelProperty(value = "所属部门")
    @Excel(name = "所属部门")
    private Integer departmentId;

    @ApiModelProperty(value = "职称ID")
    private Integer jobLevelId;

    @ApiModelProperty(value = "职位ID")
    private Integer posId;

    @ApiModelProperty(value = "聘用形式")
    @Excel(name = "聘用形式")
    private String engageForm;

    @ApiModelProperty(value = "最高学历")
    @Excel(name = "最高学历")
    private String tiptopDegree;

    @ApiModelProperty(value = "所属专业")
    @Excel(name = "所属专业",width = 20)
    private String specialty;

    @ApiModelProperty(value = "毕业院校")
    @Excel(name = "毕业院校",width = 20)
    private String school;

    @ApiModelProperty(value = "入职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "入职日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate beginDate;

    @ApiModelProperty(value = "在职状态")
    @Excel(name = "在职状态")
    private String workState;

    @ApiModelProperty(value = "工号")
    @Excel(name = "工号")
    private String workID;

    @ApiModelProperty(value = "合同期限")
    @Excel(name = "合同期限",suffix = "年")
    private Double contractTerm;

    @ApiModelProperty(value = "转正日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "转正日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate conversionTime;

    @ApiModelProperty(value = "离职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "离职日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate notWorkDate;

    @ApiModelProperty(value = "合同起始日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "合同起始日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate beginContract;

    @ApiModelProperty(value = "合同终止日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "合同终止日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate endContract;

    @ApiModelProperty(value = "工龄")
    @Excel(name = "工龄")
    private Integer workAge;

    @ApiModelProperty(value = "工资账套ID")
    private Integer salaryId;

    @ApiModelProperty(value = "民族")
    @TableField(exist = false)
    @ExcelEntity(name = "民族")
    private Nation nation;

    @ApiModelProperty(value = "政治面貌")
    @TableField(exist = false)
    @ExcelEntity(name = "政治面貌")
    private PoliticsStatus politicsStatus;

    @ApiModelProperty(value = "部门")
    @TableField(exist = false)
    @ExcelEntity(name = "部门")
    private Department department;

    @ApiModelProperty(value = "职称")
    @TableField(exist = false)
    @ExcelEntity(name = "职称")
    private Joblevel joblevel;

    @ApiModelProperty(value = "职位")
    @TableField(exist = false)
    @ExcelEntity(name = "职位")
    private Position position;
}
```

#### 修改其他实体类

在员工类中仅仅用`ExcelEntity`标记为实体类仍然无法导出实体类中具体属性，需要到被标记的实体类添加`Excel`标记导出的属性

**Nation.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.Objects;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_nation")
@ApiModel(value="Nation对象", description="")
public class Nation implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "民族")
    @Excel(name = "民族")
    private String name;
}
```

**PoliticsStatus.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.Objects;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_politics_status")
@ApiModel(value="PoliticsStatus对象", description="")
public class PoliticsStatus implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "政治面貌")
    @Excel(name = "政治面貌",width = 15)
    private String name;
}
```

**Department.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.List;
import java.util.Objects;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_department")
@ApiModel(value="Department对象", description="")
public class Department implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "部门名称")
    @Excel(name = "部门",width = 15)
    private String name;

    @ApiModelProperty(value = "父id")
    private Integer parentId;

    @ApiModelProperty(value = "路径")
    private String depPath;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;

    @ApiModelProperty(value = "是否上级")
    private Boolean isParent;

    @ApiModelProperty(value = "子部门列表")
    @TableField(exist = false)
    private List<Department> children;

    @ApiModelProperty(value = "添加返回结果，存储过程使用")
    @TableField(exist = false)
    private Integer result;
}
```

**Joblevel.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;
import java.util.Objects;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_joblevel")
@ApiModel(value="Joblevel对象", description="")
public class Joblevel implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职称名称")
    @Excel(name = "职称",width = 15)
    private String name;

    @ApiModelProperty(value = "职称等级")
    private String titleLevel;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

**Position.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;
import java.util.Objects;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_position")
@ApiModel(value="Position对象", description="")
public class Position implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职位")
    @Excel(name = "职位",width = 15)
    private String name;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

#### 查询员工

**EmployeeMapper.java**

```java
/**
 * 查询员工
 * @param id
 * @return
 */
List<Employee> getEmployee(Integer id);
```

**EmployeeMapper.xml**

```xml
<!--查询员工-->
<select id="getEmployee" resultMap="EmployeeInfo">
   SELECT
   e.*,
   n.id As nid,
   n.`name` AS nname,
   p.id AS pid,
   p.`name` AS pname,
   d.id AS did,
   d.`name` AS dname,
   j.id AS jid,
   j.`name` AS jname,
   pos.id AS posid,
   pos.`name` AS posname
   FROM
   t_employee e,
   t_nation n,
   t_politics_status p,
   t_department d,
   t_joblevel j,
   t_position pos
   WHERE
   e.nationId = n.id
   AND e.politicId = p.id
   AND e.departmentId = d.id
   AND e.jobLevelId = j.id
   AND e.posId = pos.id
   <if test="null!=id">
      AND e.id = #{id}
   </if>
    ORDER BY e.id
</select>
```

**IEmployeeService.java**

```java
/**
 * 查询员工
 * @param id
 * @return
 */
List<Employee> getEmployee(Integer id);
```

**EmployeeServiceImpl.java**

```java
/**
 * 查询员工
 * @param id
 * @return
 */
@Override
public List<Employee> getEmployee(Integer id) {
   return employeeMapper.getEmployee(id);
}
```

#### 导出员工数据

**EmployeeController.java**

```java
@ApiOperation(value = "导出员工数据")
@GetMapping(value = "/export", produces = "application/octet-stream")
public void exportEmployee(HttpServletResponse response) {
   List<Employee> list = employeeService.getEmployee(null);
   ExportParams params = new ExportParams("员工表", "员工表", ExcelType.HSSF);
   Workbook book = ExcelExportUtil.exportExcel(params, Employee.class, list);
   ServletOutputStream out = null;
   try {
      //流形式
	  response.setHeader("content-type","application/octet-stream");
      //防止中文乱码
      response.setHeader("content-disposition", "attachment;filename=" + URLEncoder.encode("员工表.xls","UTF-8"));
      out = response.getOutputStream();
      book.write(out);
   } catch (Exception e) {
      e.printStackTrace();
   } finally {
      if (null != out) {
         try {
            out.flush();
            out.close();
         } catch (IOException e) {
            e.printStackTrace();
         }
      }
   }
}
```

#### 测试

运行接口后，点击下载文件即可

![](yeb(back).images/Snipaste_2020-04-22_15-02-23.png)

下载的文件如下图

![](yeb(back).images\Snipaste_2020-04-07_18-47-06.png)

### 导入员工数据

#### 重写equals和hashCode方法

导入时，我们需要获取到对应的民族id，政治面貌id，职称id，职位id等。有两种方法

1. 根据name属性的值去数据库查询对应的id，显然在循环里面不断去查询数据库非常消耗性能，不推荐
2. 重写equals和hashCode方法，只要name属性的值一致就表示对象一致。前提是name属性的值基本不会变动

我们选择第二种方法实现

**Nation.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@NoArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode(callSuper = false,of = "name")
@Accessors(chain = true)
@TableName("t_nation")
@ApiModel(value="Nation对象", description="")
public class Nation implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "民族")
    @NonNull
    @Excel(name = "民族")
    private String name;
}
```

**PoliticsStatus.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@NoArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode(callSuper = false,of = "name")
@Accessors(chain = true)
@TableName("t_politics_status")
@ApiModel(value="PoliticsStatus对象", description="")
public class PoliticsStatus implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "政治面貌")
    @NonNull
    @Excel(name = "政治面貌",width = 15)
    private String name;
}
```

**Department.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.util.List;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@NoArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode(callSuper = false,of = "name")
@Accessors(chain = true)
@TableName("t_department")
@ApiModel(value="Department对象", description="")
public class Department implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "部门名称")
    @NonNull
    @Excel(name = "部门",width = 15)
    private String name;

    @ApiModelProperty(value = "父id")
    private Integer parentId;

    @ApiModelProperty(value = "路径")
    private String depPath;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;

    @ApiModelProperty(value = "是否上级")
    private Boolean isParent;

    @ApiModelProperty(value = "子部门列表")
    @TableField(exist = false)
    private List<Department> children;

    @ApiModelProperty(value = "添加返回结果，存储过程使用")
    @TableField(exist = false)
    private Integer result;
}
```

**Position.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@NoArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode(callSuper = false,of = "name")
@Accessors(chain = true)
@TableName("t_position")
@ApiModel(value="Position对象", description="")
public class Position implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职位")
    @NonNull
    @Excel(name = "职位",width = 15)
    private String name;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

**JobLevel.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@NoArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode(callSuper = false,of = "name")
@Accessors(chain = true)
@TableName("t_joblevel")
@ApiModel(value="Joblevel对象", description="")
public class Joblevel implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "职称名称")
    @NonNull
    @Excel(name = "职称",width = 15)
    private String name;

    @ApiModelProperty(value = "职称等级")
    private String titleLevel;

    @ApiModelProperty(value = "创建时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "是否启用")
    private Boolean enabled;
}
```

#### 上传员工数据表

**EmployeeController.java**

```java
@ApiOperation(value = "导入员工数据")
@ApiImplicitParams({@ApiImplicitParam(name = "file", value = "上传文件", dataType = "MultipartFile")})
@PostMapping("/import")
public RespBean importEmployee(MultipartFile file){
   ImportParams params = new ImportParams();
   //去掉标题行
   params.setTitleRows(1);
   List<Nation> nationList = nationService.list();
   List<PoliticsStatus> politicsStatusList = politicsStatusService.list();
   List<Department> departmentList = departmentService.list();
   List<Joblevel> joblevelList = joblevelService.list();
   List<Position> positionList = positionService.list();
   try {
      List<Employee> list = ExcelImportUtil.importExcel(file.getInputStream(), Employee.class,params);
      list.forEach(employee -> {
         //民族id
         employee.setNationId(nationList.get(nationList.indexOf(new Nation(employee.getNation().getName()))).getId());
         //政治面貌id
         employee.setPoliticId(politicsStatusList.get(politicsStatusList.indexOf(new PoliticsStatus(employee.getPoliticsStatus().getName()))).getId());
         //部门id
         employee.setDepartmentId(departmentList.get(departmentList.indexOf(new Department(employee.getDepartment().getName()))).getId());
         //职称id
         employee.setJobLevelId(joblevelList.get(joblevelList.indexOf(new Joblevel(employee.getJoblevel().getName()))).getId());
         //职位id
         employee.setPosId(positionList.get(positionList.indexOf(new Position(employee.getPosition().getName()))).getId());
      });
      if (employeeService.saveBatch(list)){
         return RespBean.success("导入成功!");
      }
      return RespBean.error("导入失败!");
   } catch (Exception e) {
      e.printStackTrace();
   }
   return RespBean.error("导入失败!");
}
```

#### 测试

![](yeb(back).images/Snipaste_2020-04-22_14-58-39.png)

## 邮件服务

### 设置邮箱服务

这里是以163邮箱为例

![](yeb(back).images\mail-02.png)

注册 163 邮箱，登录 163 邮箱后，设置邮箱账户开通 smtp 服务

![](yeb(back).images\mail-03.png)

![](yeb(back).images\mail-04.png)



​	需要根据注册时的手机号发送的验证码来开通获取邮箱客户端授权码。开通成功后，会显示个人授权访问码，该授权码是后面通过 Java 发送邮件的认证密码，非常重要。

### 创建邮件服务项目

邮件服务使用了`RabbitMQ`,有关`RabbitMQ`的知识点请参考之前文档

#### 创建项目

![](yeb(back).images\image12.png)



![](yeb(back).images\image13.png)



![](yeb(back).images\Snipaste_2020-04-09_16-41-36.png)



![](yeb(back).images\image15.png)



![](yeb(back).images\Snipaste_2020-04-09_16-41-53.png)

#### 添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>com.yjxxt</groupId>
		<artifactId>yeb</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>

	<groupId>com.yjxxt</groupId>
	<artifactId>yeb-mail</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<dependencies>
		<!--rabbitmq 依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-amqp</artifactId>
		</dependency>
		<!--mail 依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-mail</artifactId>
		</dependency>
		<!--thymeleaf 依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<!--server 依赖-->
		<dependency>
			<groupId>com.yjxxt</groupId>
			<artifactId>yeb-server</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>
	</dependencies>
</project>
```

#### 修改配置文件

application.yml

```yaml
server:
  # 端口
  port: 8082
spring:
  # 邮件配置
  mail:
    # 邮件服务器地址
    host: smtp.163.com
    # 协议
    protocol: smtp
    # 编码格式
    default-encoding: utf-8
    # 授权码（在邮箱开通服务时获取）
    password: 你的授权码
    # 发送者邮箱地址
    username: 你的邮箱地址
    # 端口（不同邮箱端口号不同）
    port: 25

  # rabbitmq配置
  rabbitmq:
    # 用户名
    username: guest
    # 密码
    password: guest
    # 服务器地址
    host: 192.168.10.100
    # 端口
    port: 5672
```

### 准备邮件模板

**mail.html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>入职欢迎邮件</title>
</head>
<body>
欢迎 <span th:text="${name}"></span> 加入 XXXX 大家庭，您的入职信息如下：
<table border="1">
    <tr>
        <td>姓名</td>
        <td th:text="${name}"></td>
    </tr>
    <tr>
        <td>职位</td>
        <td th:text="${posName}"></td>
    </tr>
    <tr>
        <td>职称</td>
        <td th:text="${joblevelName}"></td>
    </tr>
    <tr>
        <td>部门</td>
        <td th:text="${departmentName}"></td>
    </tr>
</table>

<p>我们公司的工作忠旨是严格，创新，诚信，您的加入将为我们带来新鲜的血液，带来创新的思维，以及为我们树立良好的公司形象!希望在以后的工作中我们能够齐心协力，与时俱进，团结协作!同时也祝您在本公司，工作愉快，实现自己的人生价值!希望在未来的日子里，携手共进！</p>
</body>
</html>
```

### 发送邮件

#### yeb-server添加依赖

**pom.xml**

```xml
<!--rabbitmq 依赖-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

#### yeb-server修改配置文件

**application.yml**

```yaml
spring:
  # rabbitmq配置
  rabbitmq:
    # 用户名
    username: guest
    # 密码
    password: guest
    # 服务器地址
    host: 192.168.10.100
    # 端口
    port: 5672
```

#### 消息发送

**EmployeeServiceImpl.java**

```java
/**
 * 添加员工
 * @param employee
 * @return
 */
@Override
public RespBean insertEmployee(Employee employee) {
   //处理合同期限，保留2位小数
   LocalDate beginContract = employee.getBeginContract();
   LocalDate endContract = employee.getEndContract();
   long days = beginContract.until(endContract, CadminonoUnit.DAYS);
   DecimalFormat decimalFormat = new DecimalFormat("##.00");
   employee.setContractTerm(Double.parseDouble(decimalFormat.format(days/365.00)));
   if (1==employeeMapper.insert(employee)) {
      //发送消息
      Employee emp = employeeMapper.getEmployee(employee.getId()).get(0);
      rabbitTemplate.convertAndSend("mail.welcome",emp);
      return RespBean.success("添加成功!");
   }
   return RespBean.error("添加失败!");
}
```

#### 消息接收

**MailReceiver.java**

```java
package com.yjxxt.mail.receiver;

import com.yjxxt.server.pojo.Employee;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.mail.MailProperties;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.util.Date;

/**
 * 消息接收者
 *

 * @since 1.0.0
 */
@Component
public class MailReceiver {

	private static final Logger logger = LoggerFactory.getLogger(MailReceiver.class);

	@Autowired
	private JavaMailSender javaMailSender;
	@Autowired
	private MailProperties mailProperties;
	@Autowired
	private TemplateEngine templateEngine;

	/**
	 * 邮件发送
	 */
	@RabbitListener(queues = "mail.welcome")
	public void handler(Employee employee){
		MimeMessage msg = javaMailSender.createMimeMessage();
		MimeMessageHelper helper = new MimeMessageHelper(msg);
		try {
			//发件人
			helper.setFrom(mailProperties.getUsername());
			//收件人
			helper.setTo(employee.getEmail());
			//主题
			helper.setSubject("入职欢迎邮件");
			//发送日期
			helper.setSentDate(new Date());
			//邮件内容
			Context context= new Context();
			context.setVariable("name",employee.getName());
			context.setVariable("posName",employee.getPosition().getName());
			context.setVariable("joblevelName",employee.getJoblevel().getName());
			context.setVariable("departmentName",employee.getDepartment().getName());
			String mail = templateEngine.process("mail", context);
			helper.setText(mail,true);
			//发送邮件
			javaMailSender.send(msg);
		} catch (MessagingException e) {
			logger.error("邮件发送失败=====>{}", e.getMessage());
		}
	}

}
```

**VoaMailApplication.java**

```java
package com.yjxxt.mail;

import org.springframework.amqp.core.Queue;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.context.annotation.Bean;

@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class VoaMailApplication {

	public static void main(String[] args) {
		SpringApplication.run(VoaMailApplication.class, args);
	}

	@Bean
	public Queue queue(){
		return new Queue("mail.welcome");
	}

}
```

#### 测试

![](yeb(back).images\Snipaste_2020-04-09_18-44-59.png)

## RabbitMQ消息可靠性

### 消息发送

方案：

1. 消息落库，对消息状态进行打标

![](yeb(back).images/Snipaste_2020-05-29_21-54-54.png)

2. 消息延迟投递，做二次确认，回调检查

![](yeb(back).images/Snipaste_2020-05-29_22-02-41.png)



项目实现流程：

1. 发送消息时，将当前消息数据存入数据库，投递状态为消息投递中
2. 开启消息确认回调机制。确认成功，更新投递状态为消息投递成功
3. 开启定时任务，重新投递失败的消息。重试超过3次，更新投递状态为投递失败

#### 定义消息状态常量

**MailConstants.java**

```java
package com.yjxxt.server.pojo;

/**
 * 消息状态
 */
public class MailConstants {
   //消息投递中
   public static final Integer DELIVERING = 0;
   //消息投递成功
   public static final Integer SUCCESS = 1;
   //消息投递失败
   public static final Integer FAILURE = 2;
   //最大重试次数
   public static final Integer MAX_TRY_COUNT = 3;
   //消息超时时间
   public static final Integer MSG_TIMEOUT = 1;
   //队列
   public static final String MAIL_QUEUE_NAME = "mail.queue";
   //交换机
   public static final String MAIL_EXCHANGE_NAME = "mail.exchange";
   //路由键
   public static final String MAIL_ROUTING_KEY_NAME = "mail.routing.key";
}
```

#### 修改新增员工的方法

发送消息时，将当前消息数据存入数据库，投递状态为消息投递中

**EmployeeServiceImpl.java**

```java
/**
 * 添加员工
 * @param employee
 * @return
 */
@Override
@Transactional
public RespBean insertEmployee(Employee employee) {
   //处理合同期限，保留2位小数
   LocalDate beginContract = employee.getBeginContract();
   LocalDate endContract = employee.getEndContract();
   long days = beginContract.until(endContract, CadminonoUnit.DAYS);
   DecimalFormat decimalFormat = new DecimalFormat("##.00");
   employee.setContractTerm(Double.parseDouble(decimalFormat.format(days/365.00)));
   if (1==employeeMapper.insert(employee)) {
      Employee emp = employeeMapper.getEmployee(employee.getId()).get(0);
      //数据库记录发送的消息
      String msgId = UUID.randomUUID().toString();
      MailLog mailLog = new MailLog();
      mailLog.setMsgId(msgId);
      mailLog.setEid(emp.getId());
      mailLog.setStatus(0);
      mailLog.setRouteKey(MailConstants.MAIL_ROUTING_KEY_NAME);
      mailLog.setExchange(MailConstants.MAIL_EXCHANGE_NAME);
      mailLog.setCount(0);
      mailLog.setTryTime(LocalDateTime.now().plusMinutes(MailConstants.MSG_TIMEOUT));
      mailLog.setCreateTime(LocalDateTime.now());
      mailLog.setUpdateTime(LocalDateTime.now());
      mailLogMapper.insert(mailLog);
      //发送消息
  rabbitTemplate.convertAndSend(MailConstants.MAIL_EXCHANGE_NAME,MailConstants.MAIL_ROUTING_KEY_NAME,emp, new CorrelationData(msgId));
      return RespBean.success("添加成功!");
   }
   return RespBean.error("添加失败!");
}
```

#### 修改邮件服务

将队列名改为常量定义的队列名

**VoaMailApplication.java**

```java
package com.yjxxt.mail;

import com.yjxxt.server.pojo.MailConstants;
import org.springframework.amqp.core.Queue;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.context.annotation.Bean;

@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class VoaMailApplication {

   public static void main(String[] args) {
      SpringApplication.run(VoaMailApplication.class, args);
   }

   @Bean
   public Queue queue(){
      return new Queue(MailConstants.MAIL_QUEUE_NAME);
   }

}
```

**MailReceiver.java**

```java
package com.yjxxt.mail.receiver;

import com.yjxxt.server.pojo.Employee;
import com.yjxxt.server.pojo.MailConstants;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.mail.MailProperties;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.util.Date;

/**
 * 消息接收者
 *

 * @since 1.0.0
 */
@Component
public class MailReceiver {

   private static final Logger logger = LoggerFactory.getLogger(MailReceiver.class);

   @Autowired
   private JavaMailSender javaMailSender;
   @Autowired
   private MailProperties mailProperties;
   @Autowired
   private TemplateEngine templateEngine;

   /**
    * 邮件发送
    */
   @RabbitListener(queues = MailConstants.MAIL_QUEUE_NAME)
   public void handler(Employee employee){
      MimeMessage msg = javaMailSender.createMimeMessage();
      MimeMessageHelper helper = new MimeMessageHelper(msg);
      try {
         //发件人
         helper.setFrom(mailProperties.getUsername());
         //收件人
         helper.setTo(employee.getEmail());
         //主题
         helper.setSubject("入职欢迎邮件");
         //发送日期
         helper.setSentDate(new Date());
         //邮件内容
         Context context= new Context();
         context.setVariable("name",employee.getName());
         context.setVariable("posName",employee.getPosition().getName());
         context.setVariable("joblevelName",employee.getJoblevel().getName());
         context.setVariable("departmentName",employee.getDepartment().getName());
         String mail = templateEngine.process("mail", context);
         helper.setText(mail,true);
         //发送邮件
         javaMailSender.send(msg);
      } catch (MessagingException e) {
         logger.error("邮件发送失败=====>{}", e.getMessage());
      }
   }

}
```

#### 配置RabbitMQ

开启消息确认回调以及消息失败回调

**RabbitMQConfig.java**

```java
package com.yjxxt.server.config;

import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.yjxxt.server.pojo.MailConstants;
import com.yjxxt.server.pojo.MailLog;
import com.yjxxt.server.service.IMailLogService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.DirectExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.connection.CachingConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * RabbitMQ配置类
 *

 * @since 1.0.0
 */
@Configuration
public class RabbitMQConfig {

   public static final Logger Logger = LoggerFactory.getLogger(RabbitMQConfig.class);
   @Autowired
   private CachingConnectionFactory cachingConnectionFactory;
   @Autowired
   private IMailLogService mailLogService;

   @Bean
   public RabbitTemplate rabbitTemplate(){
      RabbitTemplate rabbitTemplate = new RabbitTemplate(cachingConnectionFactory);
      /**
       * 消息确认回调,确认消息是否到达broker
       * data:消息唯一标识
       * ack：确认结果
       * cause：失败原因
       */
      rabbitTemplate.setConfirmCallback((data,ack,cause)->{
         String msgId = data.getId();
         if (ack){
            //消息确认成功
            Logger.info("{}=====>消息发送成功",msgId);
            //更新数据库中记录
            mailLogService.update(new UpdateWrapper<MailLog>().set("status",1).eq("msgId",msgId));
         }else {
            Logger.info("{}=====>消息发送失败",msgId);
         }
      });
      /**
       * 消息失败回调，比如router不到queue时回调
       * msg:消息主题
       * repCode:响应码
       * repText:响应描述
       * exchange:交换机
       * routingKey:路由键
       */
      rabbitTemplate.setReturnCallback((msg,repCode,repText,exchange,routingKey)->{
         Logger.info("{}=====>消息发送到queue时失败",msg.getBody());
      });
      return rabbitTemplate;
   }

   @Bean
   public Queue queue(){
      return new Queue(MailConstants.MAIL_QUEUE_NAME,true);
   }

   @Bean
   public DirectExchange directExchange(){
      return new DirectExchange(MailConstants.MAIL_EXCHANGE_NAME);
   }

   @Bean
   public Binding binding(){
      return BindingBuilder.bind(queue()).to(directExchange()).with(MailConstants.MAIL_ROUTING_KEY_NAME);
   }
}
```

**application.yml**

```yaml
# rabbitmq配置
rabbitmq:
  # 用户名
  username: guest
  # 密码
  password: guest
  # 服务器地址
  host: 192.168.10.100
  # 端口
  port: 5672
  # 消息失败回调
  publisher-returns: true
  # 消息确认回调
  publisher-confirm-type: correlated
```

#### 定时任务重发失败消息

重新投递失败的消息。重试超过3次，更新投递状态为投递失败

**MailTask.java**

```java
package com.yjxxt.server.task;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.yjxxt.server.pojo.Employee;
import com.yjxxt.server.pojo.MailConstants;
import com.yjxxt.server.pojo.MailLog;
import com.yjxxt.server.service.IEmployeeService;
import com.yjxxt.server.service.IMailLogService;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.List;

/**
 * 邮件发送定时任务
 *

 * @since 1.0.0
 */
@Component
public class MailTask {

   @Autowired
   private IMailLogService mailLogService;
   @Autowired
   private RabbitTemplate rabbitTemplate;
   @Autowired
   private IEmployeeService employeeService;

   /**
    * 邮件发送定时任务
    * 10秒一次
    */
   @Scheduled(cron = "0/10 * * * * ?")
   public void mailTask() {
      //状态为0且重试时间小于当前时间的才需要重新发送
      List<MailLog> list = mailLogService.list(new QueryWrapper<MailLog>().eq("status", 0).lt("tryTime",
            LocalDateTime.now()));
      list.forEach(mailLog -> {
         //重试次数超过3次，更新为投递失败，不再重试
         if (3 <= mailLog.getCount()) {
            mailLogService.update(new UpdateWrapper<MailLog>().set("status", 2).eq("msgId", mailLog.getMsgId()));
         }
         //更新重试次数，更新时间，重试时间
        mailLogService.update(new UpdateWrapper<MailLog>().set("count",mailLog.getCount()+1).set("updateTime",LocalDateTime.now()).set("tryTime",LocalDateTime.now().plusMinutes(MailConstants.MSG_TIMEOUT)).eq("msgId",mailLog.getMsgId()));
         Employee emp = employeeService.getEmployee(mailLog.getEid()).get(0);
         //发送消息
         rabbitTemplate.convertAndSend(MailConstants.MAIL_EXCHANGE_NAME, MailConstants.MAIL_ROUTING_KEY_NAME, emp,
               new CorrelationData(mailLog.getMsgId()));
      });
   }

}
```

**VoaApplication.java**

```java
package com.yjxxt.server;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

/**
 * 启动类

 * @since 1.0.0
 */
@SpringBootApplication
@MapperScan("com.yjxxt.server.mapper")
@EnableScheduling
public class VoaApplication {

   public static void main(String[] args) {
      SpringApplication.run(VoaApplication.class,args);
   }

}
```

#### 测试

正常发送

![](yeb(back).images/Snipaste_2020-04-23_11-06-46.png)

修改发送时的交换机名称，首先出现发送失败。

![](yeb(back).images/Snipaste_2020-04-23_11-10-38.png)

经过定时任务重新发送成功（定时任务的交换机没改）

![](yeb(back).images/Snipaste_2020-04-23_11-11-54.png)

如果把定时任务的交换机也改了会出现重试3次都没发送成功，最终状态为消息发送失败

![](yeb(back).images/Snipaste_2020-04-23_11-19-55.png)

### 消息接收

消息接收主要考虑幂等性的问题，这边使用`Redis`处理

#### 修改配置

除了添加`Redis`相应配置，还要开启`RabbitMQ`的手动确认机制

```yaml
server:
  # 端口
  port: 8082
spring:
  # 邮件配置
  mail:
    # 邮件服务器地址
    host: smtp.163.com
    # 协议
    protocol: smtp
    # 编码格式
    default-encoding: utf-8
    # 授权码（在邮箱开通服务时获取）
    password: SXSOHLAURGZPRNFG
    # 发送者邮箱地址
    username: zhoubin2015@163.com
    # 端口（不同邮箱端口号不同）
    port: 25

  # rabbitmq配置
  rabbitmq:
    # 用户名
    username: guest
    # 密码
    password: guest
    # 服务器地址
    host: 192.168.10.100
    # 端口
    port: 5672
    listener:
      simple:
        # 手动确认
        acknowledge-mode: manual

  # Redis配置
  redis:
    timeout: 10000ms                        # 连接超时时间
    host: 192.168.10.100                   # Redis服务器地址
    port: 6379                              # Redis服务器端口
    database: 0                             # 选择哪个库，默认0库
    lettuce:
      pool:
        max-active: 1024                    # 最大连接数，默认 8
        max-wait: 10000ms                   # 最大连接阻塞等待时间，单位毫秒，默认 -1
        max-idle: 200                       # 最大空闲连接，默认 8
        min-idle: 5                          # 最小空闲连接，默认 0
```

#### 修改邮件发送服务

首先去`Redis`查看当前消息id是否存在，如果存在说明已经消费，直接返回。如果不存在，正常发送消息，并将消息id存入`Reids`。需要手动确认消息。

```java
package com.yjxxt.mail.receiver;

import com.rabbitmq.client.Channel;
import com.yjxxt.server.pojo.Employee;
import com.yjxxt.server.pojo.MailConstants;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.amqp.support.AmqpHeaders;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.mail.MailProperties;
import org.springframework.data.redis.core.HashOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.messaging.Message;
import org.springframework.messaging.MessageHeaders;
import org.springframework.stereotype.Component;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

import javax.mail.internet.MimeMessage;
import java.io.IOException;
import java.util.Date;

/**
 * 消息接收者
 *

 * @since 1.0.0
 */
@Component
public class MailReceiver {

   private static final Logger logger = LoggerFactory.getLogger(MailReceiver.class);

   @Autowired
   private JavaMailSender javaMailSender;
   @Autowired
   private MailProperties mailProperties;
   @Autowired
   private TemplateEngine templateEngine;
   @Autowired
   private RedisTemplate redisTemplate;

   /**
    * 邮件发送
    */
   @RabbitListener(queues = MailConstants.MAIL_QUEUE_NAME)
   public void handler(Message message, Channel channel) {
      Employee employee = (Employee) message.getPayload();
      MessageHeaders headers = message.getHeaders();
      //消息序号
      long tag = (long) headers.get(AmqpHeaders.DELIVERY_TAG);
      String msgId = (String) headers.get("spring_returned_message_correlation");
      HashOperations hashOperations = redisTemplate.opsForHash();
      try {
         if (hashOperations.entries("mail_log").containsKey(msgId)) {
            //redis中包含key，说明消息已经被消费
            logger.info("消息已经被消费=====>{}", msgId);
            /**
             * 手动确认消息
             * tag:消息序号
             * multiple:是否多条
             */
            channel.basicAck(tag, false);
            return;
         }
         MimeMessage msg = javaMailSender.createMimeMessage();
         MimeMessageHelper helper = new MimeMessageHelper(msg);
         //发件人
         helper.setFrom(mailProperties.getUsername());
         //收件人
         helper.setTo(employee.getEmail());
         //主题
         helper.setSubject("入职欢迎邮件");
         //发送日期
         helper.setSentDate(new Date());
         //邮件内容
         Context context = new Context();
         context.setVariable("name", employee.getName());
         context.setVariable("posName", employee.getPosition().getName());
         context.setVariable("joblevelName", employee.getJoblevel().getName());
         context.setVariable("departmentName", employee.getDepartment().getName());
         String mail = templateEngine.process("mail", context);
         helper.setText(mail, true);
         //发送邮件
         javaMailSender.send(msg);
         logger.info("邮件发送成功");
         //将消息id存入redis
         hashOperations.put("mail_log",msgId,"OK");
         //手动确认消息
         channel.basicAck(tag,false);
      } catch (Exception e) {
         try {
            /**
             * 手动确认消息
             * tag:消息序号
             * multiple:是否多条
             * requeue:是否回退到队列
             */
            channel.basicNack(tag,false,true);
         } catch (IOException ex) {
            logger.error("消息确认失败=====>{}", ex.getMessage());
         }
         logger.error("邮件发送失败=====>{}", e.getMessage());
      }
   }

}
```

#### 测试

可以看到第一条消息正常消费，第二条消息提示已经被消费

![](yeb(back).images/Snipaste_2020-04-23_12-17-25.png)

## 工资套账管理

### 修改实体类

添加返回日期格式

**Salary.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_salary")
@ApiModel(value="Salary对象", description="")
public class Salary implements Serializable {


    @ApiModelProperty(value = "id")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "基本工资")
    private Integer basicSalary;

    @ApiModelProperty(value = "奖金")
    private Integer bonus;

    @ApiModelProperty(value = "午餐补助")
    private Integer lunchSalary;

    @ApiModelProperty(value = "交通补助")
    private Integer trafficSalary;

    @ApiModelProperty(value = "应发工资")
    private Integer allSalary;

    @ApiModelProperty(value = "养老金基数")
    private Integer pensionBase;

    @ApiModelProperty(value = "养老金比率")
    private Float pensionPer;

    @ApiModelProperty(value = "启用时间")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    private LocalDateTime createDate;

    @ApiModelProperty(value = "医疗基数")
    private Integer medicalBase;

    @ApiModelProperty(value = "医疗保险比率")
    private Float medicalPer;

    @ApiModelProperty(value = "公积金基数")
    private Integer accumulationFundBase;

    @ApiModelProperty(value = "公积金比率")
    private Float accumulationFundPer;

    @ApiModelProperty(value = "名称")
    private String name;

}
```

### 工资账套功能

**SalaryController.java**

```java
package com.yjxxt.server.controller;


import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.pojo.Salary;
import com.yjxxt.server.service.ISalaryService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * <p>
 *  前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/salary/sob")
public class SalaryController {

	@Autowired
	private ISalaryService salaryService;


	@ApiOperation(value = "获取所有工资账套")
	@GetMapping("/")
	public List<Salary> getAllSalaries() {
		return salaryService.list();
	}



	@ApiOperation(value = "添加工资账套")
	@PostMapping("/")
	public RespBean addSalary(@RequestBody Salary salary) {
        salary.setCreateDate(LocalDateTime.now());
		if (salaryService.save(salary)){
			return RespBean.success("添加成功!");
		}
		return RespBean.error("添加失败!");
	}


	@ApiOperation(value = "删除工资账套")
	@DeleteMapping("/{id}")
	public RespBean deleteSalary(@PathVariable Integer id) {
		if (salaryService.removeById(id)){
			return RespBean.success("删除成功!");
		}
		return RespBean.error("删除失败!");
	}


	@ApiOperation(value = "更新工资账套")
	@PutMapping("/")
	public RespBean updateSalary(@RequestBody Salary salary) {
		if (salaryService.updateById(salary)){
			return RespBean.success("更新成功!");
		}
		return RespBean.error("更新失败!");
	}
}
```

### 测试

测试前需要先进行登录

获取所有工资账套

![](yeb(back).images\Snipaste_2020-04-10_10-19-20.png)

添加工资账套

![](yeb(back).images/Snipaste_2020-04-23_14-22-09.png)

更新工资账套

![](yeb(back).images/Snipaste_2020-04-23_14-23-01.png)

删除工资账套

![](yeb(back).images/Snipaste_2020-04-23_14-23-57.png)

## 员工账套管理

### 修改员工类

给员工类添加工资属性

**Employee.java**

```java
package com.yjxxt.server.pojo;

import cn.afterturn.easypoi.excel.annotation.Excel;
import cn.afterturn.easypoi.excel.annotation.ExcelEntity;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.annotation.JsonFormat;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.io.Serializable;
import java.time.LocalDate;

/**
 * <p>
 * 
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_employee")
@ApiModel(value="Employee对象", description="")
public class Employee implements Serializable {


    @ApiModelProperty(value = "员工编号")
    @TableId(value = "id", type = IdType.AUTO)
    private Integer id;

    @ApiModelProperty(value = "员工姓名")
    @Excel(name = "员工姓名")
    private String name;

    @ApiModelProperty(value = "性别")
    @Excel(name = "性别")
    private String gender;

    @ApiModelProperty(value = "出生日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "出生日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate birthday;

    @ApiModelProperty(value = "身份证号")
    @Excel(name = "身份证号",width = 30)
    private String idCard;

    @ApiModelProperty(value = "婚姻状况")
    @Excel(name = "婚姻状况")
    private String wedlock;

    @ApiModelProperty(value = "民族")
    private Integer nationId;

    @ApiModelProperty(value = "籍贯")
    @Excel(name = "籍贯")
    private String nativePlace;

    @ApiModelProperty(value = "政治面貌")
    private Integer politicId;

    @ApiModelProperty(value = "邮箱")
    @Excel(name = "邮箱",width = 30)
    private String email;

    @ApiModelProperty(value = "电话号码")
    @Excel(name = "电话号码",width = 15)
    private String phone;

    @ApiModelProperty(value = "联系地址")
    @Excel(name = "联系地址",width = 40)
    private String address;

    @ApiModelProperty(value = "所属部门")
    @Excel(name = "所属部门")
    private Integer departmentId;

    @ApiModelProperty(value = "职称ID")
    private Integer jobLevelId;

    @ApiModelProperty(value = "职位ID")
    private Integer posId;

    @ApiModelProperty(value = "聘用形式")
    @Excel(name = "聘用形式")
    private String engageForm;

    @ApiModelProperty(value = "最高学历")
    @Excel(name = "最高学历")
    private String tiptopDegree;

    @ApiModelProperty(value = "所属专业")
    @Excel(name = "所属专业",width = 20)
    private String specialty;

    @ApiModelProperty(value = "毕业院校")
    @Excel(name = "毕业院校",width = 20)
    private String school;

    @ApiModelProperty(value = "入职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "入职日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate beginDate;

    @ApiModelProperty(value = "在职状态")
    @Excel(name = "在职状态")
    private String workState;

    @ApiModelProperty(value = "工号")
    @Excel(name = "工号")
    private String workID;

    @ApiModelProperty(value = "合同期限")
    @Excel(name = "合同期限",suffix = "年")
    private Double contractTerm;

    @ApiModelProperty(value = "转正日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "转正日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate conversionTime;

    @ApiModelProperty(value = "离职日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "离职日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate notWorkDate;

    @ApiModelProperty(value = "合同起始日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "合同起始日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate beginContract;

    @ApiModelProperty(value = "合同终止日期")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone = "Asia/Shanghai")
    @Excel(name = "合同终止日期",width = 20,format = "yyyy-MM-dd")
    private LocalDate endContract;

    @ApiModelProperty(value = "工龄")
    @Excel(name = "工龄")
    private Integer workAge;

    @ApiModelProperty(value = "工资账套ID")
    private Integer salaryId;

    @ApiModelProperty(value = "民族")
    @TableField(exist = false)
    @ExcelEntity(name = "民族")
    private Nation nation;

    @ApiModelProperty(value = "政治面貌")
    @TableField(exist = false)
    @ExcelEntity(name = "政治面貌")
    private PoliticsStatus politicsStatus;

    @ApiModelProperty(value = "部门")
    @TableField(exist = false)
    @ExcelEntity(name = "部门")
    private Department department;

    @ApiModelProperty(value = "职称")
    @TableField(exist = false)
    @ExcelEntity(name = "职称")
    private Joblevel joblevel;

    @ApiModelProperty(value = "职位")
    @TableField(exist = false)
    @ExcelEntity(name = "职位")
    private Position position;

    @ApiModelProperty(value = "工资账套")
    @TableField(exist = false)
    private Salary salary;
}
```

### 员工账套功能

#### EmployeeMapper

**EmployeeMapper.java**

```java
/**
 * 获取所有员工账套
 *
 * @param page
 * @return
 */
IPage<Employee> getEmployeeWithSalary(Page<Employee> page);
```

**EmployeeMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yjxxt.server.mapper.EmployeeMapper">

	<resultMap id="EmployeeWithSalary" type="com.yjxxt.server.pojo.Employee" extends="BaseResultMap">
		<association property="salary" javaType="com.yjxxt.server.pojo.Salary">
			<id column="sid" property="id"/>
			<result column="sbasicSalary" property="basicSalary"/>
			<result column="sbonus" property="bonus"/>
			<result column="slunchSalary" property="lunchSalary"/>
			<result column="strafficSalary" property="trafficSalary"/>
			<result column="sallSalary" property="allSalary"/>
			<result column="spensionBase" property="pensionBase"/>
			<result column="spensionPer" property="pensionPer"/>
			<result column="smedicalBase" property="medicalBase"/>
			<result column="smedicalPer" property="medicalPer"/>
			<result column="saccumulationFundBase" property="accumulationFundBase"/>
			<result column="saccumulationFundPer" property="accumulationFundPer"/>
			<result column="sname" property="name"/>
		</association>
		<association property="department" javaType="com.yjxxt.server.pojo.Department">
			<result column="dname" property="name"/>
		</association>
	</resultMap>

	<!--获取所有员工账套-->
	<select id="getEmployeeWithSalary" resultMap="EmployeeWithSalary">
		SELECT
			e.*,
			d.`name` AS dname,
			s.id AS sid,
			s.`name` AS sname,
			s.basicSalary AS sbasicSalary,
			s.trafficSalary AS strafficSalary,
			s.lunchSalary AS slunchSalary,
			s.bonus AS sbonus,
			s.allSalary AS sallSalary,
			s.pensionPer AS spensionPer,
			s.pensionBase AS spensionBase,
			s.medicalPer AS smedicalPer,
			s.medicalBase AS smedicalBase,
			s.accumulationFundPer AS saccumulationFundPer,
			s.accumulationFundBase AS saccumulationFundBase
		FROM
			t_employee AS e
			LEFT JOIN t_salary AS s ON e.salaryId = s.id
			LEFT JOIN t_department AS d ON e.departmentId = d.id
		ORDER BY
			e.id
	</select>
</mapper>
```

#### EmployeeService

**IEmployeeService.java**

```java
/**
 * 获取所有员工账套
 *
 * @param currentPage
 * @param size
 * @return
 */
RespPageBean getEmployeeWithSalary(Integer currentPage, Integer size);
```

**EmployeeServiceImpl.java**

```java
/**
 * 获取所有员工账套
 *
 * @param currentPage
 * @param size
 * @return
 */
@Override
public RespPageBean getEmployeeWithSalary(Integer currentPage, Integer size) {
   //开启分页
   Page<Employee> page = new Page<>(currentPage, size);
   IPage<Employee> employeePage = employeeMapper.getEmployeeWithSalary(page);
   RespPageBean respPageBean = new RespPageBean(employeePage.getTotal(), employeePage.getRecords());
   return respPageBean;
}
```

### SalarySobCfgController

```java
package com.yjxxt.server.controller;

import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.yjxxt.server.pojo.Employee;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.pojo.RespPageBean;
import com.yjxxt.server.pojo.Salary;
import com.yjxxt.server.service.IEmployeeService;
import com.yjxxt.server.service.ISalaryService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * 员工账套
 *

 * @since 1.0.0
 */
@RestController
@RequestMapping("/salary/sobcfg")
public class SalarySobCfgController {
	@Autowired
	private ISalaryService salaryService;
	@Autowired
	private IEmployeeService employeeService;


	@ApiOperation(value = "获取所有员工账套")
	@GetMapping("/")
	public RespPageBean getEmployeeWithSalary(@RequestParam(defaultValue = "1") Integer currentPage,
	                                          @RequestParam(defaultValue = "10") Integer size) {
		return employeeService.getEmployeeWithSalary(currentPage, size);
	}


	@ApiOperation(value = "获取所有工资账套")
	@GetMapping("/salaries")
	public List<Salary> getAllSalaries() {
		return salaryService.list();
	}


	@ApiOperation(value = "更新员工账套")
	@PutMapping("/")
	public RespBean updateEmployeeSalary(Integer eid, Integer sid) {
		if (employeeService.update(new UpdateWrapper<Employee>().set("salaryId", sid).eq("id", eid))) {
			return RespBean.success("更新成功!");
		}
		return RespBean.error("更新失败!");
	}
}
```

### 测试

测试前需要先进行登录

获取所有员工账套

![](yeb(back).images\Snipaste_2020-04-10_15-28-19.png)

获取所有工资账套

![](yeb(back).images/Snipaste_2020-04-23_14-49-19.png)

更新员工账套

![](yeb(back).images/Snipaste_2020-04-23_14-49-42.png)

## WebSocket

### 什么是WebSocket？

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

![](yeb(back).images\bg2017051502.png)

其他特点包括：

* 较少的控制开销

* 更强的实时性

* 保持连接状态

* 更好的二进制支持

* 可以支持扩展

* 更好的压缩效果

### 为什么需要WebSocket？

举例来说，我们想了解今天的天气，只能是客户端向服务器发出请求，服务器返回查询结果。HTTP 协议做不到服务器主动向客户端推送信息。

现在，很多网站为了实现推送技术，所用的技术都是 `Ajax 轮询`。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。

而比较新的技术去做轮询的效果是`Comet`。这种技术虽然可以双向通信，但依然需要反复发出请求。而且在Comet中，普遍采用的长链接，也会消耗服务器资源。

HTML5 定义的 WebSocket 协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。



### WebSocket实现聊天功能

#### 添加依赖

```xml
<!--websocket 依赖-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

#### WebSocket配置类

**WebSocketConfig**

```java
package com.yjxxt.server.config;

import com.yjxxt.server.config.security.component.JwtTokenUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.Message;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.simp.config.ChannelRegistration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.messaging.simp.stomp.StompCommand;
import org.springframework.messaging.simp.stomp.StompHeaderAccessor;
import org.springframework.messaging.support.ChannelInterceptor;
import org.springframework.messaging.support.MessageHeaderAccessor;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.util.StringUtils;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer;

/**
 * WebSocket配置类
 *

 * @since 1.0.0
 */
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
	@Value("${jwt.tokenHead}")
	private String tokenHead;
	@Autowired
	private JwtTokenUtil jwtTokenUtil;
	@Autowired
	private UserDetailsService userDetailsService;


	/**
	 * 添加这个Endpoint，这样在网页可以通过websocket连接上服务
	 * 也就是我们配置websocket的服务地址，并且可以指定是否使用socketJS
	 * @param registry
	 */
	@Override
	public void registerStompEndpoints(StompEndpointRegistry registry) {
		/**
		 * 1.将ws/ep路径注册为stomp的端点，用户连接了这个端点就可以进行websocket通讯，支持socketJS
		 * 2.setAllowedOrigins("*")：允许跨域
		 * 3.withSockJS():支持socketJS访问
		 */
		registry.addEndpoint("/ws/ep").setAllowedOrigins("*").withSockJS();
	}


	/**
	 * 输入通道参数配置
	 * @param registration
	 */
	@Override
	public void configureClientInboundChannel(ChannelRegistration registration) {
		registration.interceptors(new ChannelInterceptor() {
			@Override
			public Message<?> preSend(Message<?> message, MessageChannel channel) {
				StompHeaderAccessor accessor = MessageHeaderAccessor.getAccessor(message, StompHeaderAccessor.class);
				//判断是否为连接，如果是，需要获取token，并且设置用户对象
				if (StompCommand.CONNECT.equals(accessor.getCommand())){
					String token = accessor.getFirstNativeHeader("Auth-Token");
					if (!StringUtils.isEmpty(token)){
						String authToken = token.substring(tokenHead.length());
						String username = jwtTokenUtil.getUserNameFromToken(authToken);
						//token中存在用户名
						if (!StringUtils.isEmpty(username)){
							//登录
							UserDetails userDetails = userDetailsService.loadUserByUsername(username);
							//验证token是否有效，重新设置用户对象
							if (jwtTokenUtil.validateToken(authToken,userDetails)){
								UsernamePasswordAuthenticationToken authenticationToken =
										new UsernamePasswordAuthenticationToken(userDetails, null,
												userDetails.getAuthorities());
								SecurityContextHolder.getContext().setAuthentication(authenticationToken);
								accessor.setUser(authenticationToken);
							}
						}
					}
				}
				return message;
			}
		});
	}

	/**
	 * 配置消息代理
	 * @param registry
	 */
	@Override
	public void configureMessageBroker(MessageBrokerRegistry registry) {
		//配置代理域，可以配置多个，配置代理目的地前缀为/queue,可以在配置域上向客户端推送消息
		registry.enableSimpleBroker("/queue");
	}
}
```

#### 定义消息类

ChatMsg.java

```java
package com.yjxxt.server.pojo;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.experimental.Accessors;

import java.time.LocalDateTime;

/**
 * 消息
 *

 * @since 1.0.0
 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
public class ChatMsg {
   private String from;
   private String to;
   private String content;
   private LocalDateTime date;
   private String fromNickName;
}
```

#### WsController

```java
package com.yjxxt.server.controller;

import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.ChatMsg;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.messaging.handler.annotation.MessageMapping;
import org.springframework.messaging.simp.SimpMessagingTemplate;
import org.springframework.security.core.Authentication;
import org.springframework.stereotype.Controller;

import java.time.LocalDateTime;

/**

 * @since 1.0.0
 */
@Controller
public class WsController {
   @Autowired
   private SimpMessagingTemplate simpMessagingTemplate;

   @MessageMapping("/ws/chat")
   public void handleMsg(Authentication authentication, ChatMsg chatMsg){
      Admin admin = (Admin) authentication.getPrincipal();
      chatMsg.setFrom(admin.getUsername());
      chatMsg.setFromNickName(admin.getName());
      chatMsg.setDate(LocalDateTime.now());
      /**
       * 发送消息
       * 1.消息接收者
       * 2.消息队列
       * 3.消息对象
       */
      simpMessagingTemplate.convertAndSendToUser(chatMsg.getTo(),"/queue/chat",chatMsg);
   }
}
```

#### 查询聊天人员

**ChatController**

```java
package com.yjxxt.server.controller;

import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.service.IAdminService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * <p>
 * 前端控制器
 * </p>
 *

 */
@RestController
@RequestMapping("/chat")
public class ChatController {
   @Autowired
   private IAdminService adminService;


   @ApiOperation(value = "获取所有操作员")
   @GetMapping("/admin")
   public List<Admin> getAllAdmins(String keywords) {
      return adminService.getAllAdmins(keywords);
   }
}
```

#### 放行WebSocket

**SecurityConfig.java**

```java
@Override
public void configure(WebSecurity web) throws Exception {
   //放行静态资源
   web.ignoring().antMatchers(
         "/login",
         "/logout",
         "/ws/**",
         "/css/**",
         "/js/**",
         "/index.html",
         "/favicon.ico",
         "/doc.html",
         "/webjars/**",
         "/swagger-ui.html",
         "/swagger-resources/**",
         "/v2/api-docs/**",
         "/captcha");
}
```

## 个人中心

在普通项目中需要获取当前登录用户的信息，一般做法是在登录成功后，将当前用户信息存入session中。在需要使用当前用户的时候从session里面读取。更新用户信息也是直接通过数据库进行相应的更新。

在Spring Security中要如何获取用户信息和更新用户信息呢？

在Spring Security中提供了一个`Authentication`对象，我们可以在`Controller`或者`Service`中，直接注入`Authentication`，注入成功后，就能直接使用。这样我们就能通过`Authentication`对象直接获取用户信息。

在Spring Security中更新用户信息，除了正常的去数据库进行相应的更新之外，还需要重新构建`Authentication`对象，这样才能在项目中正确的获取到更新后的用户信息。具体代码如下

```java
/**
 * 1.用户对象
 * 2.凭证（密码）
 * 3.用户角色
 */
SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(admin,
      authentication.getCredentials(), authentication.getAuthorities()));
```

### 个人中心操作

#### AdminService

**IAdminService.java**

```java
/**
 * 更新用户密码
 *
 * @param oldPass
 * @param pass
 * @param adminId
 * @return
 */
RespBean updatePassword(String oldPass, String pass, Integer adminId);
```

**AdminServiceImpl.java**

```java
/**
 * 更新用户密码
 *
 * @param oldPass
 * @param pass
 * @param adminId
 * @return
 */
@Override
public RespBean updatePassword(String oldPass, String pass, Integer adminId) {
   Admin admin = adminMapper.selectById(adminId);
   BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
   if (encoder.matches(oldPass, admin.getPassword())) {
      admin.setPassword(encoder.encode(pass));
      int result = adminMapper.updateById(admin);
      if (1 == result) {
         return RespBean.success("更新成功!");
      }
   }
   return RespBean.error("更新失败!");
}
```

#### AdminInfoController

```java
package com.yjxxt.server.controller;

import com.yjxxt.server.pojo.Admin;
import com.yjxxt.server.pojo.RespBean;
import com.yjxxt.server.service.IAdminService;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

/**
 * 个人信息
 *

 * @since 1.0.0
 */
@RestController
public class AdminInfoController {

	@Autowired
	private IAdminService adminService;


	@ApiOperation(value = "更新当前用户信息")
	@PutMapping("/admin/info")
	public RespBean updateAdmin(@RequestBody Admin admin, Authentication authentication) {
		//更新成功，重新构建Authentication对象
		if (adminService.updateById(admin)) {
			/**
			 * 1.用户对象
			 * 2.凭证（密码）
			 * 3.用户角色
			 */
			SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(admin,
					authentication.getCredentials(), authentication.getAuthorities()));
			return RespBean.success("更新成功!");
		}
		return RespBean.error("更新失败!");
	}


	@ApiOperation(value = "更新用户密码")
	@PutMapping("/admin/pass")
	public RespBean updateAdminPassword(@RequestBody Map<String,Object> info){
		String oldPass = (String) info.get("oldPass");
		String pass = (String) info.get("pass");
		Integer adminId = (Integer) info.get("adminId");
		return adminService.updatePassword(oldPass,pass,adminId);
	}

}
```

#### 测试

更新用户信息

![](yeb(back).images/Snipaste_2020-04-23_16-01-34.png)

更新密码

![](yeb(back).images/Snipaste_2020-04-23_16-02-59.png)

用之前密码登录，发现提示用户名或密码错误

![](yeb(back).images/Snipaste_2020-04-23_16-03-26.png)

用新密码测试，成功登录并返回token

![](yeb(back).images/Snipaste_2020-04-23_16-03-39.png)

#### Bug解决

当我们更新个人信息同时附带个人权限，会发现报错，如图

![](yeb(back).images/Snipaste_2020-04-28_19-29-28.png)

原因：这是我们的`Admin`实体类实现了`UserDetails`接口，重写了`getAuthorities()`方法，但是`Admin`实体类却没有对应的`Collection<? extends GrantedAuthority>`属性，也无法创建含有`Collection<? extends GrantedAuthority>`属性的构造函数。JSON无法进行反序列化，导致报错

解决：自定义反序列化类，在`getAuthorities()`方法定义使用自定义的反序列化类进行

**CustomAuthorityDeserializer.java**

```java
package com.yjxxt.server.config;

import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.databind.DeserializationContext;
import com.fasterxml.jackson.databind.JsonDeserializer;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;

import java.io.IOException;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;

/**
 * 自定义Authority解析器
 *

 */
public class CustomAuthorityDeserializer extends JsonDeserializer {

    @Override
    public Object deserialize(JsonParser jp, DeserializationContext ctxt) throws IOException {
        ObjectMapper mapper = (ObjectMapper) jp.getCodec();
        JsonNode jsonNode = mapper.readTree(jp);
        List<GrantedAuthority> grantedAuthorities = new LinkedList<>();

        Iterator<JsonNode> elements = jsonNode.elements();
        while (elements.hasNext()) {
            JsonNode next = elements.next();
            JsonNode authority = next.get("authority");
            grantedAuthorities.add(new SimpleGrantedAuthority(authority.asText()));
        }
        return grantedAuthorities;
    }
}
```

**Admin.java**

```java
package com.yjxxt.server.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import com.fasterxml.jackson.databind.annotation.JsonDeserialize;
import com.yjxxt.server.config.CustomAuthorityDeserializer;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.AccessLevel;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.Getter;
import lombok.experimental.Accessors;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.io.Serializable;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;

/**
 * <p>
 *
 * </p>
 *

 */
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("t_admin")
@ApiModel(value = "Admin对象", description = "")
public class Admin implements Serializable, UserDetails {

   @ApiModelProperty(value = "id")
   @TableId(value = "id", type = IdType.AUTO)
   private Integer id;

   @ApiModelProperty(value = "姓名")
   private String name;

   @ApiModelProperty(value = "手机号码")
   private String phone;

   @ApiModelProperty(value = "住宅电话")
   private String telephone;

   @ApiModelProperty(value = "联系地址")
   private String address;

   @ApiModelProperty(value = "是否启用")
   @Getter(AccessLevel.NONE)
   private Boolean enabled;

   @ApiModelProperty(value = "用户名")
   private String username;

   @ApiModelProperty(value = "密码")
   private String password;

   @ApiModelProperty(value = "用户头像")
   private String userFace;

   @ApiModelProperty(value = "备注")
   private String remark;

   @ApiModelProperty(value = "权限")
   @TableField(exist = false)
   private List<Role> roles;

   @Override
   @JsonDeserialize(using = CustomAuthorityDeserializer.class)
   public Collection<? extends GrantedAuthority> getAuthorities() {
      List<SimpleGrantedAuthority> authorities =
            roles.stream()
                  .map(role -> new SimpleGrantedAuthority(role.getName()))
                  .collect(Collectors.toList());
      return authorities;
   }

   @Override
   public boolean isAccountNonExpired() {
      return true;
   }

   @Override
   public boolean isAccountNonLocked() {
      return true;
   }

   @Override
   public boolean isCredentialsNonExpired() {
      return true;
   }

   @Override
   public boolean isEnabled() {
      return enabled;
   }
}
```

### 更新头像

首先确保`FASTDFS`服务已启动，关于`FASTDFS`知识点请参考之前文档

![](yeb(back).images/Snipaste_2020-04-14_11-17-33.png)

#### 添加依赖

pom.xml

```xml
<!--FastDFS依赖-->
<dependency>
   <groupId>org.csource</groupId>
   <artifactId>fastdfs-client-java</artifactId>
   <version>1.29-SNAPSHOT</version>
</dependency>
```

#### 在resources目录下添加配置文件

fdfs_client.conf

注1：tracker_server指向您自己IP地址和端口，1-n个

注2：除了tracker_server，其它配置项都是可选的

```properties
#连接超时
connect_timeout = 2
#网络超时
network_timeout = 30
#编码格式
charset = UTF-8
#tracker端口号
http.tracker_http_port = 8080
#防盗链功能
http.anti_steal_token = no
#秘钥
http.secret_key = FastDFS1234567890
#tracker ip：端口号
tracker_server = 192.168.10.100:22122
#连接池配置
connection_pool.enabled = true
connection_pool.max_count_per_entry = 500
connection_pool.max_idle_time = 3600
connection_pool.max_wait_time_in_ms = 1000
```

#### 编写FastDFS工具类

FastDFSUtils.java

```java
package com.yjxxt.yebserver.utils;

import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.FileInfo;
import org.csource.fastdfs.StorageClient;
import org.csource.fastdfs.StorageServer;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerServer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.io.ClassPathResource;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

/**
 * 文件上传工具类
 *

 * @since 1.0.0
 */
public class FastDFSUtils {

	private static Logger logger = LoggerFactory.getLogger(FastDFSUtils.class);

	//ClientGlobal.init 方法会读取配置文件，并初始化对应的属性。
	static {
		try {
			String filePath = new ClassPathResource("fdfs_client.conf").getFile().getAbsolutePath();
			ClientGlobal.init(filePath);
		} catch (Exception e) {
			logger.error("FastDFS Client Init Fail!", e);
		}
	}

	/**
	 * 上传文件
	 *
	 * @param file
	 * @return
	 */
	public static String[] upload(MultipartFile file) {
		String name = file.getOriginalFilename();
		logger.info("File Name: " + name);

		long startTime = System.currentTimeMillis();
		String[] uploadResults = null;
		StorageClient storageClient = null;
		try {
			//获取storage客户端
			storageClient = getStorageClient();
			//上传
			uploadResults = storageClient.upload_file(file.getBytes(), name.substring(name.lastIndexOf(".") + 1),
                    null);
		} catch (IOException e) {
			logger.error("IO Exception when uploadind the file:" + name, e);
		} catch (Exception e) {
			logger.error("Non IO Exception when uploadind the file:" + name, e);
		}
		logger.info("upload_file time used:" + (System.currentTimeMillis() - startTime) + " ms");

		//验证上传结果
		if (uploadResults == null && storageClient != null) {
			logger.error("upload file fail, error code:" + storageClient.getErrorCode());
		}
		//上传文件成功会返回 groupName。
		logger.info("upload file successfully!!!" + "group_name:" + uploadResults[0] + ", remoteFileName:" + " " + uploadResults[1]);
		return uploadResults;
	}


	/**
	 * 获取文件信息
	 *
	 * @param groupName
	 * @param remoteFileName
	 * @return
	 */
	public static FileInfo getFile(String groupName, String remoteFileName) {
		try {
			StorageClient storageClient = getStorageClient();
			return storageClient.get_file_info(groupName, remoteFileName);
		} catch (IOException e) {
			logger.error("IO Exception: Get File from Fast DFS failed", e);
		} catch (Exception e) {
			logger.error("Non IO Exception: Get File from Fast DFS failed", e);
		}
		return null;
	}

	/**
	 * 下载文件
	 *
	 * @param groupName
	 * @param remoteFileName
	 * @return
	 */
	public static InputStream downFile(String groupName, String remoteFileName) {
		try {
			StorageClient storageClient = getStorageClient();
			byte[] fileByte = storageClient.download_file(groupName, remoteFileName);
			InputStream ins = new ByteArrayInputStream(fileByte);
			return ins;
		} catch (IOException e) {
			logger.error("IO Exception: Get File from Fast DFS failed", e);
		} catch (Exception e) {
			logger.error("Non IO Exception: Get File from Fast DFS failed", e);
		}
		return null;
	}

	/**
	 * 删除文件
	 *
	 * @param groupName
	 * @param remoteFileName
	 * @tadminows Exception
	 */
	public static void deleteFile(String groupName, String remoteFileName)
			throws Exception {
		StorageClient storageClient = getStorageClient();
		int i = storageClient.delete_file(groupName, remoteFileName);
		logger.info("delete file successfully!!!" + i);
	}


	/**
	 * 生成Storage客户端
	 *
	 * @return
	 * @tadminows IOException
	 */
	private static StorageClient getStorageClient() throws IOException {
		TrackerServer trackerServer = getTrackerServer();
		StorageClient storageClient = new StorageClient(trackerServer, null);
		return storageClient;
	}

	/**
	 * 生成Tracker服务器端
	 *
	 * @return
	 * @tadminows IOException
	 */
	private static TrackerServer getTrackerServer() throws IOException {
		TrackerClient trackerClient = new TrackerClient();
		TrackerServer trackerServer = trackerClient.getTrackerServer();
		return trackerServer;
	}

	/**
	 * 获取文件路径
	 *
	 * @return
	 */
	public static String getTrackerUrl() {
		TrackerClient trackerClient = new TrackerClient();
		TrackerServer trackerServer = null;
		StorageServer storeStorage = null;
		try {
			trackerServer = trackerClient.getTrackerServer();
			storeStorage = trackerClient.getStoreStorage(trackerServer);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return "http://" + storeStorage.getInetSocketAddress().getHostString() + ":8888/";
	}
}
```

#### 更新头像

**IAdminService.java**

```java
/**
 * 更新用户头像
 *
 * @param url
 * @param id
 * @param authentication
 * @return
 */
RespBean updateAdminUserFace(String url, Integer id, Authentication authentication);
```

**IAdminService.java**

```java
/**
 * 更新用户头像
 *
 * @param url
 * @param id
 * @param authentication
 * @return
 */
@Override
public RespBean updateAdminUserFace(String url, Integer id, Authentication authentication) {
   Admin admin = adminMapper.selectById(id);
   admin.setUserFace(url);
   int result = adminMapper.updateById(admin);
   if (1==result){
      Admin principal = (Admin) authentication.getPrincipal();
      principal.setUserFace(url);
      //更新Authentication
      SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(admin,
            authentication.getCredentials(),authentication.getAuthorities()));
      return RespBean.success("更新成功!",url);
   }
   return RespBean.error("更新失败!");
}
```

#### AdminInfoController.java

```java
@ApiOperation(value = "更新用户头像")
@ApiImplicitParams({@ApiImplicitParam(name = "file", value = "头像", dataType = "MultipartFile")})
@PostMapping("/admin/userface")
public RespBean updateHrUserFace(MultipartFile file, Integer id, Authentication authentication) {
   //获取上传文件地址
   String[] fileAbsolutePath = FastDFSUtils.upload(file);
   String url = FastDFSUtils.getTrackerUrl() + fileAbsolutePath[0] + "/" + fileAbsolutePath[1];
   return adminService.updateAdminUserFace(url, id, authentication);
}
```

#### 测试

测试前需要先登录

![](yeb(back).images/Snipaste_2020-04-14_12-04-54.png)

更新成功，同时拿到上传之后返回的url，在浏览器直接输入这个url可以直接访问

![](yeb(back).images/Snipaste_2020-04-14_12-05-10.png)





