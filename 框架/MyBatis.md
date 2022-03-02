# MyBatis

## 1、什么是MyBatis

`MyBatis` 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。`MyBatis` 免除了几乎所有的 `JDBC` 代码以及设置参数和获取结果集的工作。`MyBatis` 可以通过简单的 `XML` 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



## 2、#{}与${}的区别

- **#{}** **是** **占位符** **：动态解析 -> 预编译 -> 执行**；**${}是拼接符：动态解析->编译->执行**；
  - \#{}动态获取id时，sql语句的打印显示的是一个 “？” ，即占位符。
  - \${}动态获取id时，sql语句的打印显示的是一个 “1” ，没有占位符，直接显示。
- **#{} 对应的变量会自动加上单引号**；**${}对应的变量不会加上单引号** 
- **#{}能防止sql 注入**；**${}不能防止sql 注入**
  - 传参：**富贵 or name = 狗蛋**。
    - `select * from role where name = #{name}`$====>$`select * from role where name = '富贵 or name = 狗蛋'`
    - `select * from role where name = ${name}`$====>$​`select * from role where name = '富贵' or name = '狗蛋'`
- **#{} 默认值arg0、arg1、arg2 或 0、 1**；**${} 默认值param1、param2、param3**

## 3、Statement和PreparedStatement之间的区别

- **关系：**

  - `PreparedStatement` 继承自 `Statement` ，都是接口

- **区别：**

  - `PreparedStatement` 表示预编译的 SQL 语句的对象。

    - SQL 语句被预编译并存储在 `PreparedStatement` 对象中。然后可以使用此对象多次高效地执行该语句。

    - ```java
      PreparedStatement pstmt = con.prepareStatement("UPDATE EMPLOYEES SALARY = ? WHERE ID = ?");
      pstmt.setBigDecimal(1, 1533.00)
      pstmt.setInt(2, 1102)
      pstmt.execute()//注意提交时这里不能再有sql语句,不同于Statment
      ```

  - `Statement` 用于执行静态 SQL 语句并返回它所生成结果的对象。

    - 在默认情况下，同一时间每个 `Statement` 对象只能打开一个 `ResultSet` 对象。因此，如果读取一个 `ResultSet` 对象与另一个交叉，则这两个对象必须是由不同的 `Statement` 对象生成的。如果存在某个语句的打开的当前 `ResultSet` 对象，则 `Statement` 接口中的所有执行方法都会隐式关闭它。

    - ```java
      Statement stat=conn.createStatement();
      String sql="insert into lover values(6,'suxingxing',to_date('21-9-2016','dd-mm-yyyy'))";
      stat.execute(sql);//这里提交时应该有sql语句，不同于PreparedStatmen
      ```

## 4、动态SQL

### 4.1、IF

实现的功能：**如果没有传参的话，就查询表中所有的结果；如果有参数传递过来，就查询指定条件的结果**。

```xml
<select id="queryBlog" parameterType="map" resultType="Blog">
    select * from mybatis.blog
    <where>
        <if test="title != null">
            and `title` = #{title}
        </if>
        <if test="author != null">
            and `author` = #{author}
        </if>
    </where>
</select
```

### 4.2、choose、when、otherwise

**实现功能：不想用到所有的条件语句，只想用到其中一个。**比如说传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若没有传参数，则返回全部。

```xml
    <select id="queryBlogChoose" parameterType="map" resultType="Blog">
        select * from mybatis.blog
        <where>
            <choose>
                <when test="title != null">
                    `title` = #{title}
                </when>
                <when test="author != null">
                    and `author` = #{author}
                </when>
<!--                <otherwise>-->
<!--                    -->
<!--                </otherwise>-->
            </choose>
        </where>
    </select>
```

### 4.3、trim、where、set

**set** 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）

```xml
<update id="updateBlog" parameterType="map">
    update blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id}
</update>
```

![image-20210625212144347](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210625212144347.png)