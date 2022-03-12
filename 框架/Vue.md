# Vue



## computed与methods的区别



## Vue的生命周期



## v-if与v-show的区别



## `this.$route` 与 `this.$router`

- `this.$router`相当于一个全局的路由器对象(`Vue-Router`)，包含了很多属性和对象（比如 `history` 对象），任何页面都可以调用其 `push()`, `replace()`, `go()` 等方法。
- `this.$route`为当前路由的对象，每一个路由都会有一个 `route` 对象，是一个局部的对象，可以获取对应的 `name`, `path`, `params`, `query` 等属性。

> `push()` 方法

- 想要导航到不同的 `URL`，则使用 `router.push()` 方法。这个方法会向 `history` 栈添加一个新的记录，所以，**当用户点击浏览器后退按钮时，则回到之前的 URL**。

  - 若使用 `router.replace`方法则没有后退到之前 URL 的功能。

- 当你点击 `<router-link>` 时，这个方法会在内部调用，所以说，点击` <router-link :to="..."> `等同于调用 `router.push(...)`

- 使用情况：

  - 没有参数的情况

  - ```js
        //字符串
        this.$router.push('home')
     
        //对象
        this.$router.push({path:'home'})
     
        //命名的路由
        this.$router.push({name:'user', params:{userId: '123'}})
     
        //带查询参数，变成 /register?plan=private
        this.$router.push({path:'register', query:{plan:private}})
    ```

  - 有参数的情况

  - ```js
    //1、手写完整的 path:
        this.$router.push({path: `/user/${userId}`});
     //   获取参数：this.$route.params.userId
     
    //2、用 params 传递：
        this.$router.push({name:'user', params:{userId: '123'}});
     //   获取参数：this.$route.params.userId
    //    url 形式：url 不带参数，http:localhost:8080/#/user
     
    //3、用 query 传递：
        this.$router.push({path:'/user', query:{userId: '123'}});
    //    获取参数：this.$route.query.userId
    //    url 形式：url 带参数，http:localhost:8080/#/user?userId=123
    ```