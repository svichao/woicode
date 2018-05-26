---
title: vue中computed与watch的异同
date: 2018-05-26 20:14:56
tags: vue

---
### 一、computed 和 watch

都可以观察页面的数据变化；
当处理页面的数据变化时，更好的办法是使用computed属性，而不是命令是的watch回调。

<!-- more -->
```

/*html:

我们要实现 第三个表单的值 是第一个和第二个的拼接，并且在前俩表单数值变化时，第三个表单数值也在变化
*/

<div id="myDiv">
    <input type="text" v-model="firstName">
    <input type="text" v-model="lastName">
    <input type="text" v-model="fullName">
</div>

<!--js: 用watch方法来实现-->
//将需要watch的属性定义在watch中，当属性变化氏就会动态的执行watch中的操作，并动态的可以更新到dom中  

new Vue({
  el: '#myDiv',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})

<!--js: 利用computed 来写-->
//计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。
//这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。  
new Vue({
    el:"#myDiv",
    data:{
        firstName:"Den",
        lastName:"wang",
    },
    computed:{
        fullName:function(){
            return  this.firstName  + " " +this.lastName;
        }
    }
})

```

### 一、computed 和 watch  

在vue的 模板内是可以写一些简单的js表达式的 ，很便利。但是如果在页面中使用大量或是复杂的表达式去处理数据，对页面的维护会有很大的影响。这个时候就需要用到computed 计算属性来处理复杂的逻辑运算。

1.优点： 在数据未发生变化时，优先读取缓存。computed 计算属性只有在相关的数据发生变化时才会改变要计算的属性，当相关数据没有变化是，它会读取缓存。而不必想 motheds方法 和 watch 方法是的每次都去执行函数。
2.setter 和 getter方法：（注意在vue中书写时用set 和 get） setter 方法在设置值是触发。 getter 方法在获取值时触发。

```
computed:{
    fullName:{
        //这里用了es6书写方法 
        set(){ alert("set"); }, 
        get(){ alert("get"); 
        return this.firstName + " " +this.lastName; },
    }
}
```

### 三 、watch 方法

虽然计算属性在大多数情况下是非常适合的，但是在有些情况下我们需要自定义一个watcher，当需要在数据变化时执行异步或开销较大的操作时，这时watch是非常有用的。
例如：

```
<div id="watch-example">
    <p>
        Ask a yes/no question:
        <input v-model="question">
    </p>
    <p>{{ answer }}</p>
</div>
<!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
<!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
    var watchExampleVM = new Vue({
        el: '#watch-example',
        data: {
            question: '',
            answer: 'I cannot give you an answer until you ask a question!'
        },
        watch: {
            // 如果 `question` 发生改变，这个函数就会运行
            question: function(newQuestion) {
                this.answer = 'Waiting for you to stop typing...'
                this.getAnswer()
            }
        },
        methods: {
            // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
            // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
            // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
            // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
            // 请参考：https://lodash.com/docs#debounce
            getAnswer: _.debounce(
                function() {
                    if(this.question.indexOf('?') === -1) {
                        this.answer = 'Questions usually contain a question mark. ;-)'
                        return
                    }
                    this.answer = 'Thinking...'
                    var vm = this
                    axios.get('https://yesno.wtf/api')
                        .then(function(response) {
                            vm.answer = _.capitalize(response.data.answer)
                        })
                        .catch(function(error) {
                            vm.answer = 'Error! Could not reach the API. ' + error
                        })
                },
                // 这是我们为判定用户停止输入等待的毫秒数
                500
            )
        }
    })
</script>
```

在这个示例中，使用 watch 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。
除了 watch 选项之外，您还可以使用命令式的 vm.$watch API。

 [原文链接][https://www.cnblogs.com/Sarah119/p/7843312.html]