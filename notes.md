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

上面例子，js 访问 URL 的全称为：http://localhost:8443/api/login

另外参考 [axios.defaults.baseURL 的三种配置方法](https://blog.csdn.net/weixin_41023528/article/details/89886735)

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
