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

## 概念

### 单一状态树

意思是vuex的设计理念就是永远只用应该$store来管理，方便维护，而不会有多个$store各自存一些数据这样

### vuex管理状态，什么是状态

对于单个页面的数据来说，如下图，

- data上的变量表示状态state，
- 反映在页面view上，
- 通过一些如点击事件来修改data上变量来改变状态。

![](images\flow.png)



## 使用约定

### 👑安装、Vuex组成部分

> 在生产环境安装vuex，`cnpm i --save vuex`
>
> new一个vuex.store对象（俗称仓库），挂载在vue实例上
>
> 
>
> 在src下新建一个store文件夹的index.js文件，下面省略了一些配置、import、export
>
> Vuex由五个部分组成：`state、mutations、getters、actions、modules`
>
> ```js
> Vue.use(Vuex)
> const store = new Vuex.store({
>     //------------放共享变量的
>     //state创建的，都会加入响应式系统（会监听变化）。后加的不会响应式，除非响应式方法
>     //假设state一个对象要加字段，要有响应式。用【Vue.set、Vue.delete】
>     //或者一些数组的响应式方法，如push、pop、splice
>     state: {
>         count: 100    
>     },		
>     //------------只放同步方法 = 【事件类型type + 回调函数】
>     //风格一：【组件用是this.$store.commit('increment', 对象Payload or 变量) 】
>     //风格二：【this.$store.commit({type: 'increment', 一系列Payload的属性 })】
>     mutations: {
>       // Payload：负载。就是能接受一个对象 or 变量，更灵活
>       // 自动传state参数、对象Payload or 变量
>       increment(state, 对象Payload or 变量) {
>           state.count += Payload.xxx  or 变量;
>       }
>     },
>     //------------放的异步方法：如setTimeout、网络请求
>     //还是commit  mutations的事件类型，异步转同步，让devtool能检测
>     //组件用就不再commit了，而是dispatch
>     //然后这里面的方法再commit，相当于加了actions这个中间层
>     //【this.$store.dispatch('actions的方法名', 对象Payload or 变量)】
>     //【actions 的方法可以返回一个Promise，让调用方继续.then执行resolve给的东西】
>     actions: {
>         //context 就是$store，区别modules下的context，不太一样
>         incrementInfo(context, 对象Payload or 变量) {
>             return new Promise((resolve, reject) => {
>                 setTimeout(() => {
> 					context.commit('mutations的事件类型')
>                     resolve('可用来通知【执行回调了】')
>                 }, 1000)
>             })
>         } 
>     },
>     //---------就是放一些【需要处理后】才显示的数据，如平方、filter、map、reduce
>     // 类似computed属性，只不过getters作用vuex，computed是作用某个vue组件
>     getters: {
>         // 最多两个参数，获取vuex的state、getter
>         // 推荐这样写，【第一层不要用箭头函数】可读性
>         pow(state, getter) { 
>             //既然只能两个参数，万一用的时候要传参数，返回方法
>             //return 一个function (参数){}
>         }
> 
>     },
>     //------------分模块，由于单一状态树，所以在store里面分模块
>     //一般就两层，再多显得复杂
>     modules: {
>         //上面那个根不是可以通过$store来访问state、getters吗
>         //那对于第二层的m1呢？答案：会把m1放在根的states上
>         //即这样访问【$store.state.m1.name】
>         m1: {
>             state: {
>                 name: 'lhw'
>             },
>             //建议不要和根的mutations方法名重复，先匹配根的、再匹配模块的
>             //同样还是用$store.commit('根的/模块的事件类型')
>             mutations: {
>                 //与根的不同，多了一些访问根的属性，如rootGetter、rootState
>                 xxx(context) {
>                     //commit的只能是当前层的其他mutations方法！！
>                     context.commit('xxx')
>                 }
>             },
>             actions: {},
>             //一样这样访问【$store.getters.根/模块的方法名】
>             //区别state，还要带个模块名m1，这里没有
>             getters: {
>                 //能获取当前层的state、getters、以及根的也行
>                 xxx(state, getters, rootState) {}
>             },
>         },
>         m2:.....
>     }
> })
> ```
>
> 在main.js上
>
> ```js
> new Vue({
>     el: 'app',
>     store
> })
> ```
>
> 然后就能`$store.state.xxx`访问放在vuex的变量



### store状态更新的唯一方式：提交Mutation

!> 👑<u>强烈推荐</u>遵循下图使用流程。 vue组件不要直接`$store.state.xxx`访问，而是通过Actions——》Mutations的流程



注：浏览器插件Vue的Devtools能记录到Mutations的修改，方便调试，因为是共享数据，多人访问，很有必要记录修改的信息。

Mutations这里是`同步的`。如果只是同步操作，可以不经过Actions，可以从vue组件直接去Mutations。但一旦是那些访问后端数据的，`异步的，等回调的，就一定要用上Actions这一步。`

![](images/vuex.png)



### 使用Mutation常量——减少出错

我们不是在store下的mutations属性上写方法吗、然后对应组件用的时候就commit这个这个方法名（也叫事件类型）。涉及大量的copy，容易出错

因此在store文件下新建一个`mutations-type.js`，名字任意，内容大概如下

```js
export const INCREMENT = 'increment'
```

然后在store文件夹的index.js文件改成` [常量]`、`commit(常量)`的形式

```js
mutations: {
    [INCREMENT](state, 对象Payload or 变量) {
        state.count += Payload.xxx  or 变量;
    }
}
```

### 抽取文件，让vuex-store目录更加清晰

```js
store文件夹
|——	index.js		//state放着，其他抽离出去
|——	actions.js
|——	mutations.js
|——	modules文件夹
	|—— m1.js
	|—— m2.js
```



## 资料

[B站教程](https://www.bilibili.com/video/BV15741177Eh?p=129)