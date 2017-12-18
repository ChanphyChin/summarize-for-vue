#### 导语：
> 用了vue这么久一直没有好好整理一下这当中学到的东西，感觉时间稍微长一点，脱离vue稍微长一点就会忘得很快，好记性不如烂笔头，想一点写一点，记录一下关于vue的些许。
## vue项目的搭建
相当于配置乱七八糟的一大堆配置表，相信很多人会选择vue官方的脚手架来搭建项目，而不是基于官方脚手架的项目搭建网上也是一大堆，没有一个统一标准，那么还是来看看vue官方手脚架怎么搭的。  
在你希望构建项目的地方新建一个文件夹来放置即将构建的项目文件，调出命令行工具（在新建好的文件夹按住shiift键+鼠标右击，选择：在此处打开命令窗口）
```
    # 全局安装 vue-cli
    $ npm install --global vue-cli
    # 创建一个基于 webpack 模板的新项目
    $ vue init webpack my-project
    # 安装依赖，走你
    $ cd my-project
    $ npm install
    $ npm run dev
```
其中在第二步的时候有有一些非必选项，可以根据你的需求，选择是否添加到你的项目中。  
到此，项目的构建就已经完成，项目基本的壳也出来了，但也只是各空壳，路由组件什么的还是得自己学着配一下

## 路由的搭配
1. 接下来看的是路由的配置。  
vue路由是基于官方插件vue-router进行配置的，配置路径在src目录下router目录下的index.js中，默认的代码为：
```
        import Vue from 'vue'
        import Router from 'vue-router'
        import Hello from '@/components/Hello'
        Vue.use(Router)
        export default new Router({
        routes: [
            {
                path: '/',  //path为路由
                name: 'Hello', //name为路由名称，用路由跳转的时候回用到
                component: Hello // component为路由指向的组件
                }
            ]
        })
```
vue-router有一个懒加载的配置方式，只需要把
```
    import Hello from '@/components/Hello'
    替换成
    const Foo = resolve => {
    // require.ensure 是 Webpack 的特殊语法，用来设置 code-split point
    // （代码分块）
    require.ensure(['./Foo.vue'], () => {
        resolve(require('./Foo.vue'))
    })
    }
    或者AMD风格代码
    const Hello = resolve => require(['@/components/Hello'], resolve)
```
更改后的代码如下：
```
    import Vue from 'vue'
    import Router from 'vue-router'
    // import Hello from '@/components/Hello'
    const Hello = resolve => require(['@/components/Hello'], resolve)
    Vue.use(Router)

    export default new Router({
    routes: [
        {
        path: '/',
        name: 'Hello',
        component: Hello
        }
    ]
    })
```
如果需要在路由增加参数那么比如配置了路径为/bar，需要传递的参数为id，那么代码如下：
```
    import Vue from 'vue'
    import Router from 'vue-router'
    const Hello = resolve => require(['@/components/Hello'], resolve)
    Vue.use(Router)

    export default new Router({
    routes: [
        {
        path: '/',
        name: 'Hello',
        component: Hello
        },
        {
        path: '/bar/:id',
        name: 'Hello',
        component: Hello
        }
    ]
    })
```
注意：当配置了参数之后，路由跳转的时候如果不带参数，那么就会报错。
2. 路由的跳转
路由的跳转有两种方式，分别为：
```
    声明式 : <router-link :to="...">
    编程式 : router.push(...) 
```
比如跳转到上面配置的'/'用声明式以标签的形式插入到视图中，那么就是：
```
    <router-link :to="{name:'hello'}"></router-link>
```
带参数的跳转
```
    <router-link :to="{name:'hello',params:{id:5}}"></router-link>
```
编程式跳转：
```
    // 字符串
    router.push('home')

    // 对象
    router.push({ path: 'home' })

    // 命名的路由
    router.push({ name: 'user', params: { userId: 123 }})

    // 带查询参数，变成 /register?plan=private
    router.push({ path: 'register', query: { plan: 'private' }})
```
组件内部使用this.$route进行访问router对象  
至于路由下面的children那些，路由的映射可以官方文档都有说明，在这里配置的路由跳转映射到app.vue标签
```
    <router-view></router-view>
```  
更多详细介绍请看vue-router的[官方文档](https://router.vuejs.org/zh-cn/)
## 组件的引用
接下来说说定义好了一个组件之后怎么在其他的.vue文件中引用  
项目目录src下面的components就是用来给我们放组件的，公共组件比如header和footer这些是需要放在app.vue里面的，这样就不用每次都在子组件中去引用。  
组件引入分为三步：  
* 在组建中通过import方式引入
* 在组件的components中声明这个组件
* 在view的部分通过声明的组件，以标签的形式插入到视图中
公共组件代码范例：
```
    <template>
        <div id="app">
            <headerPublic></headerPublic> 
            <img src="./assets/logo.png">   
            <router-view></router-view>
            <footerPublic></footerPublic>
        </div>
    </template>

    <script>
        import headerPublic from './components/common/header.vue'
        import footerPublic from './components/common/footer.vue'
        export default {
        name: 'app',
        components:{headerPublic,footerPublic}
        }
    </script>
```
子组件中引用范例：
```
    <template>
        <div id="hello">
            <testComponent></testComponent> 
        </div>
    </template>

    <script>
        import testComponent from './components/test.vue'
        export default {
        components:{testComponent}
        }
    </script>
```
可以带参数到引入的组件中，也就是组件中打开的时候就会带有这些参数:
```
    <template>
        <div id="hello">
            <testComponent :data-id="id"></testComponent> 
        </div>
    </template>

    <script>
        import testComponent from './components/test.vue'
        export default {
            data(){
                id:1
            },
            components:{testComponent}
        }
    </script>
```
子组件中要接收父组件的参数，需要在props中定义这个传递过来的变量名,取得这个值只需要this.dataId就可以获取到:
```
    <template>
        <div id="test">
        </div>
    </template>
    <script>
        import testComponent from './components/test.vue'
        export default {
            data(){
            },
            props:['dataId']
        }
    </script>
```

## 插件的引用
官方有具体说明，这里不重复 => [官方插件引用说明](https://cn.vuejs.org/v2/guide/plugins.html)  

jquery的引入比较特殊 => [vue-cli webpack 引入jquery](http://www.cnblogs.com/pandabunny/p/5417938.html)  

## 全局方法的引入
全局方法一般是跟某些插件一样，在main.js引入之后，添加到vue的原型中。  
```
    import common from './js/common';
    Vue.prototype.common = common;
```
这样的方式引入之后那么调用的时候只需要this.common就可以调用到了。
## proxytable解决接口联调跨域问题
``` 
    config文件夹 -> index.js文件
    dev下配置
        env: require('./dev.env'),
        port: 8081,
        autoOpenBrowser: true,
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        proxyTable: {
          '/api': {
            target: 'http://***/api',
            changeOrigin: true,
            pathRewrite: {
            '^/api': '/'
            }
          }
      接口填写：
      axios.post('api/vr-oauth-center/oauth/token', qs.stringify(data[0]))
