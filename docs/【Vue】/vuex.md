# Vuex

## 是个什么，为什么用

> vuex说白就是 放共享变量
> 其实直接挂载在Vue的prototype上也能做到<u>多个组件用这个共享变量</u>
>
> ✨注：因为多个组件用的都是同一个Vue实例
> 那为什么还需要Vuex，那是因为`这样挂载的 不是响应式的`，也就是说，某个组件改了，在另外的组件上式<u>检测不到修改</u>
> 因此用人家的vuex，它帮我们加上响应式的效果

## 什么时候用

> 不是什么数据都放到vuex上的！！
>
> 如`token`，它作为一个登录令牌，任何页面发请求都需要携带着它，这种就很适合放在vuex上。
>
> 再比如`购物车的东西`呀，因为很可能设计多个页面，每个页面都能说把商品加入购物车，那如果每个页面都另开一个数组，后面又设计合并，显然不方便，所以这些也适合放vuex上
>
> 如果只是【父子组件传数据】，这没必要用vuex。有点杀鸡用牛刀的感觉，用回平时的套路就好——<u>父传子用props、子传父用emit</u>



## 使用约定

安装

> 在生产环境安装vuex，`cnpm i --save vuex`
>
> new一个vuex.store对象（俗称仓库），挂载在vue实例上
>
> ```js
> //省略了一些配置、import、export
> //在src下新建一个store文件夹
> Vue.use(Vuex)
> const store = new Vuex.store({
>  //放共享变量的
>  state: {
>  	count: 100    
>  },		
>  //放方法，会自动传state参数
>  //【 组件用是this.$store.commit(increment) 】
>  mutations: {
>      increment(state) {
>          state.count++;
>      }
>  },
>  actions: {},
>  getters: {},
>  modules: {}
> })
> 
> //在main.js上
> new Vue({
>  el: 'app',
>  store
> })
> ```
>
> 然后就能`$store.state.xxx`访问放在vuex的变量



对于单个页面的数据来说，如下图，

- data上的变量表示状态state，
- 反映在页面view上，
- 通过一些如点击事件来修改data上变量来改变状态。

![](images\flow.png)



!> 👑<u>强烈推荐</u>遵循下图使用流程。 vue组件不要直接`$store.state.xxx`访问，而是通过Actions——》Mutations的流程



注：浏览器插件Vue的Devtools能记录到Mutations的修改，方便调试，因为是共享数据，多人访问，很有必要记录修改的信息。

Mutations这里是`同步的`。如果只是同步操作，可以不经过Actions，可以从vue组件直接去Mutations。但一旦是那些访问后端数据的，`异步的，等回调的，就一定要用上Actions这一步。`

![](images/vuex.png)



## 资料

[B站教程](https://www.bilibili.com/video/BV15741177Eh?p=129)