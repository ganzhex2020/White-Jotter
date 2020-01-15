[Vue + Spring Boot 项目实战](https://blog.csdn.net/Neuf_Soleil/article/details/88925013)


- 如何从 0 开始搭建 Web 项目？
- 什么是前后端分离？如何实现前后端分离？
- 单页面应用有哪些特点？
- 如何在 Web 项目中使用数据库并利用网页实现增删改查？
- 在开发中如何利用各种辅助手段？
- Vue.js 的基本概念与用法
- 简单的前端页面设计
- 如何部署 Web 应用？

项目的第二部分是后台管理模块的开发，主要包括以下内容：

- 后台管理模块的常见功能与布局（内容管理、用户/权限管理、运维监控）
- 用户身份验证、授权、会话管理与信息加密存储
- Shiro 框架的使用
- 实现不同粒度的访问控制（动态菜单、功能控制、数据控制）

# 搭建项目

## 搭建 Vue 前台项目

### 使用 CLI

### 使用 IDEA

在 package.json 文件上点击右键，选择 show npm scripts

### Vue 项目结构分析

![](https://img-blog.csdnimg.cn/2019040120135655.png)

- index.html: 

  首页入口，整个项目只有这一个 html 文件，所以这是一个 单页面应用。表面上可以有很多的页面，但这些页面都将被动态注入展示在 index.html 这个页面的 <div id="app"></div> 中。
  
- App.vue:

  “根组件”，因为其它的组件（一般实现在 components 文件夹中，也是前端主要的开发量体现之所在）又都包含在这个组件中。
  
  `export default` 是 ES6 的语法，意思是将这个组件整体导出，之后就可以使用 import 导入组件了。
  
  这个文件最关键的一点其实是第四行， <router-view/>，是一个容器，名字叫“路由视图”，意思是当前路由（ URL）指向的内容将显示在这个容器中。也就是说，其它的组件即使拥有自己的路由（URL，需要在 router 文件夹的 index.js 文件里定义），也只不过表面上是一个单独的页面，实际上只是在根组件 App.vue 中。这段话我还不是很明白！！！
  
- main.js
  
  我现在的理解，`main.js` 中的 `el: '#app'` 中定义的 `app` 对应的就是 index.html 中的 `<div id="app"></div>` 中的 “app”。
  
  `main.js` 中的 `el: '#app'` 的 Vue 对象类似一个 js 的汇聚点，包含了路由信息、组件信息等，编译后 Vue 会以该对象为基础，把生成的 js 代码渲染的内容注入（inject）到 index.html 的 id 为 app 的 div 所指定的区域。
  
  具体的我还不能完全说清楚，TBD

## 搭建后端

### IDE

### 命令行方式（TBD）

# 什么叫 “前后端分离”

在开发的时候，前端用前端的服务器（Ngix），后端用后端的服务器（Tomcat），使用自己独立的服务部署。

而所谓的前后端不分离，就是将前端的代码（html，css，js）放在后端的服务器路径下。

## 反向代理

这个概念随着 “前后端分离” 的指导思想引出。

正向代理就是，你要访问一个网站 A，比如 “192.168.1.100:8081”，然后发现访问不到，于是你访问了一个能访问到 “A” 的代理服务器 “B”（常用的方法是在浏览器中配置 “B” 的地址，譬如 “192.168.1.1:80”），当我们在浏览器中输入 A 的地址的时候，浏览器实际访问 B，并通过 B 获取 A 上的页面，对于用户来说，他们实际是知道 A 的地址的。

反向代理就是，用户不需要知道 A 的地址，只有 B 知道，当你访问 B 的时候，“B” 发现有些网页它没有，但 A 上有，于是 B 就取回“A”的内容给你看。作为用户的你，是不知道有这个过程的，这么做是为了保护服务器 A，不暴露服务器A的真实地址。

# 前后端结合开发的总结

## 前端要做的事情：

### 解决跨域问题

前端部署在 localhost:8080；后端部署在 localhost:8443， 自然存在所谓的跨域问题

为了解决跨域问题，需要在前端和后端都做相应的调整，这里先说前端的

#### 在生产环境中

通过 nginx 的配置实现跨域部署

参考 

- [前后端分离跨域问题解决方案](https://blog.csdn.net/zhang6622056/article/details/75221492)
- [什么是跨域？如何解决？](https://www.jianshu.com/p/8fa2acd103ea)
这篇文章介绍了采用代理的方式实现跨域访问
- [前后端分离+微信授权+跨域](https://blog.csdn.net/llllllloooooo/article/details/89216735)
- [前后端分离跨域问题解决方案](https://www.cnblogs.com/abcwt112/p/8005182.html)
- [使用 Nginx 部署前后端分离项目，解决跨域问题](https://www.cnblogs.com/lenve/p/11576581.html)

更具体的行为还需要了解 nginx 的一些知识。

#### 在开发环境中

可以通过在修改 `config\index.js` 中的 proxyTable 来

```
    proxyTable: {
      '/api': {
        target: 'http://localhost:8443',
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    }
```

参考：
- [webpack之proxyTable设置跨域](https://www.cnblogs.com/wancheng7/p/8987694.html)
由于我们是在开发环境下使用，自然而然是要配置在 config/index.js 的dev里面：
- [Vue-cli proxyTable 解决开发环境的跨域问题](https://www.jianshu.com/p/95b2caf7e0da)

### 引入 axios

在前端 js 代码中会发送请求访问后端的地址链接 url。可以采用多种机制，axios是其中一种。

具体为了使用 axios，采取如下步骤：

1. 安装 axios 模块

```
npm install --save axios
```
也有说使用
```
npm install --save-dev axios
```

区别暂时不清楚，可以看这里：https://segmentfault.com/q/1010000000403629

2、在 `src/main.js` 中添加如下内容：

```
import axios from 'axios'

// 设置前端请求默认发送到以 http://localhost:8443/api 为前缀的 URL
axios.defaults.baseURL = 'http://localhost:8443/api'
// 全局注册，之后可在其他组件中通过 this.$axios 发送数据
Vue.prototype.$axios=axios;
```

通过以上设置后，前端基于axios 发送请求可以写成以下：

```
 methods: {
      login () {
        this.$axios
          .post('/login', {
            username: this.loginForm.username,
            password: this.loginForm.password
          })
```

上面例子，js 访问 URL 的全称为：`http://localhost:8443/api/login`

另外参考 
- [Vue.js Ajax(axios) 教程](https://www.runoob.com/vue2/vuejs-ajax-axios.html)
- [axios.defaults.baseURL 的三种配置方法](https://blog.csdn.net/weixin_41023528/article/details/89886735)

### 编写页面对应的 vue 组件文件

### 配置页面路由

将页面 vue 组件文件添加到 `src\router\index.js`

路由配置时给出的是每一项的

```
{
      path: '/login',
      name: 'Login',
      component: Login
},
```

## 后端要做的事情

### 后端中涉及跨域的问题的处理

相对前端，后端要做的事情比较简单

在前端 js 代码中会请求访问后端的地址链接 url。我们在前端的服务器 nginx 中要请求后端 Tomcat 中的这个 url，这就是跨域请求，为了支持前端的跨域请求则后台代码也要允许跨域，这里我们最简单的方法是使用 springMVC 的注解 @CrossOrigin(origins = "*", maxAge = 3600)。  ，这里要强调的是springMVC的版本要在4.2或以上版本才支持@CrossOrigin，我这里的设置是允许所有跨域访问，也可以单独指定允许的服务器跨域（设置origin的值便可）。

这个参考例子中

```
@Controller
public class LoginController {

    @CrossOrigin
    @PostMapping(value = "api/login")
    @ResponseBody
    public Result login(@RequestBody User requestUser) {
    ......
```

### 什么叫 POJO 类

[什么是POJO，JavaBean？](https://www.jianshu.com/p/6f3e2bd50cb1)

POJO：一个简单的Java类，这个类没有实现/继承任何特殊的java接口或者类，不遵循任何主要java模型，约定或者框架的java对象。在理想情况下，POJO不应该有注解。

### @requestBody

@requestBody 注解常用来处理 content-type 不是默认的 application/x-www-form-urlcoded 编码的内容，比如说：application/json 或者是 application/xml 等。一般情况下来说常用其来处理 application/json 类型。

# 数据库的引入


- @Entity ：表示这是一个实体类
- @Table(name = "user")：表示对应的表名是 user
- @JsonIgnoreProperties({"handler","hibernateLazyInitializer"})：因为是做前后端分离，而前后端数据交互用的是 json 格式。 那么 User 对象就会被转换为 json 数据。 而本项目使用 jpa 来做实体类的持久化，jpa 默认会使用 hibernate, 在 jpa 工作过程中，就会创造代理类来继承 User ，并添加 handler 和 hibernateLazyInitializer 这两个无须 json 化的属性，所以这里需要用 JsonIgnoreProperties 把这两个属性忽略掉。
- @Id：标注用于声明一个实体类的属性映射为数据库的主键列。该属性通常置于属性声明语句之前，可与声明语句同行，也可写在单独行上。 @Id标注也可置于属性的getter方法之前。
- @GeneratedValue：用于标注主键的生成策略，通过 strategy 属性指定。默认情况下，JPA 动选择一个最适合底层数据库的主键生成策略：SqlServer对应identity，MySQL 对应 auto increment。 在 javax.persistence.GenerationType 中定义了以下几种可供选择的策略： 
	- IDENTITY 采用数据库ID自增长的方式来自增主键字段，Oracle 不支持这种方式； 
	- AUTO： JPA自动选择合适的策略，是默认选项； 
	- SEQUENCE：通过序列产生主键，通过@SequenceGenerator 注解指定序列名，MySql不支持这种方式 
	- TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略可以使应用更易于数据库移植。
- @Column注解来标识实体类中属性与数据表中字段的对应关系。

所以代码中的以下三行注解都是针对 `int id` 这个属性的说明

```
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    int id;
```

JPA
```
public interface UserDAO extends JpaRepository<User,Integer> {
    User findByUsername(String username);

    User getByUsernameAndPassword(String username,String password);
}
```

JPA 提供了一套语法让我们不用写SQL函数，只需要按照一定的规则定义函数名字就可以实现SQL

这里 find 还是 get 这些动词都不重要，建议用 find。关键是By后面的

也可以自己定义

```
@Query(value = "select new User(u.id,u.username,u.name,u.phone,u.email,u.enabled) from User u")
    List<User> list();
```

UserService

这里实际上是对 UserDAO 进行了二次封装，一般来讲，我们在 DAO 中只定义基础的增删改查操作，而具体的操作，需要由 Service 来完成。当然，由于我们做的操作原本就比较简单，所以这里看起来只是简单地重命名了一下，比如把 “通过用户名及密码查询并获得对象” 这种方法命名为 get。


# Vue 中路由的 Hash 模式和 Histroy 模式

如果采用 Hash 模式，访问页面的时候需要使用形如 `localhost:8080/#/login` 的写法，不太好看

可以换成 History 模式，方法：修改前端vue 的 `router\index.js`，加入 `mode: 'history'` 这句话，这样就可以使用 `http://localhost:8080/login` 这样访问了。

参考的 https://learner.blog.csdn.net/article/details/89422585

# 页面拦截

限制未登录状态下对核心功能页面的访问，我的理解是这里的拦截就是防止用户在未登录的情况下随意输入二级路径，从而访问到一些重要的页面。

一种是在后端拦截，但这种拦截器只有在将前后端项目整合在一起时才能生效，而前后端分离的项目实际上不推荐这么做（我的理解是如果要在后端实现拦截，则需要为每个登录用户维护一个session，是不是有点太消耗资源了），接下来我们尝试用前端实现相似的功能。

实现前端登录器，需要在前端判断用户的登录状态。我们可以像之前那样在组件的 data 中设置一个状态标志，但登录状态应该被视为一个全局属性，而不应该只写在某一组件中。所以我们需要引入一个新的工具——Vuex，它是专门为 Vue 开发的状态管理方案，我们可以把需要在各个组件中传递使用的变量、方法定义在这里。

https://learner.blog.csdn.net/article/details/89422585

针对前端的登录拦截主要实现以下逻辑：

```
if (登录不是 login 页面，即是需要拦截的页面) {
	// A：拦截的上半部
	if (用户已经登录) {
		显示请求的页面，goto 其他逻辑
	} else {
		记下需要拦截的页面
	｝
}
显示 Login 页面
// B：拦截的下半部
用户输入用户名和密码后向后端发起 post 请求
后端返回应答
if (应答 ok) {
	如果存在拦截的页面则进入拦截的页面，否则进入缺省 Login 后需要跳转的页面
}
```

具体的修改体现在：
- `src/store/index.js`: 利用 vuex 定义全局状态变量保存用户的状态并提供一个方法 login 用来封装对状态的保存
- `src/router/index.js`: 设置需要拦截的路由，例子里只有一个 index
- `src/main.js`: 对路由注册一个钩子函数，在实际路由之前对用户的登录状态进行判断和拦截的上半部，即上面伪代码中的 A
- `Login.vue`: 拦截的下半部，主要是确保拦截后一旦用户登录等自动恢复用户请求的网页，即上面伪代码中的 B

更复杂的实现，参考，还没有来得及看
- https://blog.csdn.net/saucxs/article/details/93669641
- https://blog.csdn.net/sky786905664/article/details/73920725

# Vuex

[Vue.js——十分钟入门Vuex](https://www.jianshu.com/p/a804606ad8e9)
