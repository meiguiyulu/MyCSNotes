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

## 5. 一级缓存二级缓存

### 5.1 一级缓存

​		`Mybatis` 对缓存提供支持，但是在没有配置的默认情况下，它只开启一级缓存，一级缓存只是相对于同一个`SqlSession` 而言。所以在参数和 `SQL` 完全一样的情况下，我们使用同一个 `SqlSession` 对象调用一个 `Mapper` 方法，往往只执行一次 `SQL` ，因为使用 `SelSession` 第一次查询后，`MyBatis` 会将其放在缓存中，以后再查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，`SqlSession` 都会取出当前缓存的数据，而不会再次发送 `SQL` 到数据库。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1254583-20171026214546023-1354746770.png)

#### 5.1.1 一级缓存的生命周期有多长？

1. `MyBatis` 在开启一个数据库会话时，会创建一个新的 `SqlSession` 对象，`SqlSession` 对象中会有一个新的 `Executor` 对象。`Executor` 对象中持有一个新的 `PerpetualCache` 对象；当会话结束时，`SqlSession` 对象及其内部的 `Executor` 对象还有 `PerpetualCache` 对象也一并释放掉。
2. 如果 `SqlSession` 调用了 `close()` 方法，会释放掉一级缓存 `PerpetualCache` 对象，一级缓存将不可用。
3. 如果 `SqlSession` 调用了 `clearCache()` ，会清空 `PerpetualCache` 对象中的数据，但是该对象仍可使用。
4. `SqlSession`中执行了任何一个写操作(`update()、delete()、insert()`) ，都会清空 `PerpetualCache` 对象的数据，但是该对象可以继续使用。

#### 5.1.2 怎么判断某两次查询是完全相同的查询？

　　`Mybatis` 认为，对于两次查询，如果以下条件都完全一样，那么就认为它们是完全相同的两次查询。

-  传入的 `statementId` 
- 查询时要求的结果集中的结果范围
- 这次查询所产生的最终要传递给 `JDBC java.sql.Preparedstatement` 的 `Sql` 语句字符串（boundSql.getSql() ）
- 传递给 `java.sql.Statement` 要设置的参数值



### 5.2 二级缓存

`MyBatis` 的二级缓存是 `Application` 级别的缓存，它可以提高对数据库查询的效率，以提高应用的性能。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1254583-20171029185910164-1823278112.png)

`SqlSessionFactory` 层面上的二级缓存默认是不开启的，二级缓存的开启需要进行配置，实现二级缓存的时候，`MyBatis` 要求返回的 `POJO` 必须是可序列化的。 也就是要求实现 `Serializable` 接口，配置方法很简单，只需要在映射 `XML` 文件配置就可以开启缓存了 `<cache/>`，如果我们配置了二级缓存就意味着：

- 映射语句文件中的所有 `select` 语句将会被缓存。
- 映射语句文件中的所欲 `insert`、`update`和`delete`语句会刷新缓存。
- 缓存会使用默认的 `Least Recently Used(LRU, 最近最少使用的)`算法来收回。
- 根据时间表，比如 `No Flush Interval`,（CNFI没有刷新间隔），缓存不会以任何时间顺序来刷新。
- 缓存会存储列表集合或对象(无论查询方法返回什么)的1024个引用
- 缓存会被视为是 `read/write` (可读/可写)的缓存，意味着对象检索不是共享的，而且可以安全的被调用者修改，不干扰其他调用者或线程所做的潜在修改。

