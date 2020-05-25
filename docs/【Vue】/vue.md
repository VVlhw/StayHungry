# Vue

## 1、父子组件传递信息：父传子用props，子传父用$emit

`父传子`

```vue
<template>
    <div id="app">
        <!-- myName得在子组件的props数组上 -->
        <sub-app :myName="name"></sub-app>
	</div>
</template>
<script>
//注册子组件
import Sub1 from "@/subapp/sub1.vue"
export default {
    data(){
        return {
            name:"xiaoyu"
        }
    },
    components:{
        subApp:Sub1
    }
}
</script>
```

子组件sub1.vue

```vue
<template>
    <div>
    	<!-- 输出父组件传的 name内容：xiaoyu -->
    	<h1>{{myName}}</h1>
	</div>
</template>
<script>
export default{
	// 要和父组件的一致
	props:['myName']
    /*
    	props可以是数组、
    	也可以对象（区别就是可以给默认值，父不传都有）
    props:{
        myName:{
            type:String,
            required:true,
            default:'xx'
        }
    }
    */
}
</script>
```

`子传父`有两种方法，一种同样是子组件一个一个props，不过它是函数，子组件调用这个props的函数，从而让父执行。参考《02vue进阶.pdf》

推荐emit这种

子组件

```js
doClick:function(){
    // 代表发射，发射到父那里，
    this.$emit('newName','xiaoliu');
}
```

父组件。在原生事件中，$event是事件对象在自定义事件中，`$event是传递过来的数据`

```vue
<!-- 父组件用@xxx来接受，这个xxx就是emit的第一个参数 -->
<!-- 父组件data有个 name变量  -->
<sub-app @newName="name=$event"></sub-app>
```



## 2、数组哪些操作是会加入响应式的

```js
// 1、push方法
arr.push('a', 'b')
// 2、pop方法：删除数组最后一个元素
arr.pop()
// 3、shift方法：删除数组第一个元素
arr.shift()
// 4、unshift方法：数组最前加元素
arr.unshift('a', 'b')
// 5、splice作用
// 删除：第二个参数——删除个数，没有则后全部
// 替换：第二个参数——替换个数，后面是用于替换前面的元素
// 插入：第二个参数为0，后面是插入的元素
// 6. sort
arr.sort()
// 7. reverse
arr.reverse()
// 索引修改不会相应，要set
arr[0] = 'bb'
arr.splice(0, 1, 'bb')
Vue.set(arr, 0, 'bb')
```

