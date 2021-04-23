---
title: 前端面试Vue篇：Vue组件通信的几种方式
date: 2019-12-27 22:22:22
categories: 
- [前端框架, VUE]
tags: 
- [vue, Vue基础, 面试, 组件通信]
---
> 作者：李旭光
> 引用请标明出处


# 前言

公司这两年兴起了前后端分离的热潮，在选用三大技术框架时，vue凭借其学习曲线低深受公司程序员的喜爱，因此我们也就走上了vue学习的不归路，在招聘招揽小伙伴时有那么一些问题经常被问起，vue组件间通信就是其中之一，下面我们来聊一聊vue组件通信有哪些方式。
<!-- more -->

## `props`和`$emit`
啥也不了解的小伙伴应该也知道这种方式吧，这是最最基础的通信方式了，父子组件通信基本都用它。父组件向子组件传递数据的时候通过prop传参，子组件中通过$emit传递给父组件，父组件在触发子组件$emit方法时得到子组件数据。实例如下：

```
// 父组件 parent.vue

Vue.compinent('parent', {
    template: `
    <div>
    <p>父组件</p>
    <child :message="message" @getChildrenData="getChildrenData"></child>
    </div>`,
    data() {
        return {
            message:'Hello lixuguang'
        }
    },
    methods: {
        /**
        * 执行子组件触发的事件方法 
        */
        getChildrenData(data){
            console.log(data)
        }
    }
})

// 子组件 child.vue

Vue.component('child', {
    props:['message'], // 得到父组件传过来的数据
    data() {
        return {
            childMessage: this.message
        }
    },
    template:`
    <div>
    <input type="text" v-model="childMessage" @input="emitParentData(childMessage)">
    </div>
    `,
    methods: {
        emitParentData(data) {
            this.$emit('getChildrenData', data) // 父组件触发时给父组件传值
        }
    }
})

// App.vue
var app = new Vue({
    el: "#app",
    template: `
    <div>
    <parent></parent>
    </div>
    `
})
```
**解析代码：**
1. 父组件通过`message`属性将数据传递给子组件，并且通过`getChildrenData`事件来监听子组件出发的事件；
2. 子组件通过`props`获得父组件传过来的数据，并且通过`this.$emit`触发了`getChildrenData`事件;

## `$attrs`和`$listeners`
前一种方法我们完成了父子组件的数据通信，那你有没有想过如果有多层嵌套的数据最上层要往最下层传值怎么办，前一种方法只能一层一层的往下传，可是这样太麻烦了，那么有没有什么方法能够一次传到你想要传到的位置呢，当然是有的，那就是接下来要说到的`$attrs`和`$listeners`。
假设我们现在有三层包含关系的组件，分别是level1/level2/level3，level1 > level2 > level3, `>` 表示包含关系。
```
// level3.vue

Vue.component('level3', {
    template:`
    <div>
    <input type="text" v-model="$attrs.level3Message" @input="emitLevel3Data($attrs.level3Message)">
    </div>
    `,
    methods: {
        emitLevel3Data(data) {
            this.$emit('getLevel3Data', data)
        }
    }
})

// level2.vue

Vue.component('level2', {
    props:['level2Message'],
    data(){
        return {
            level2Message:this.level2Message
        }
    },
    template:`
    <div>
    <input type="text" v-model="level2Message" @input="emitLevel2Data(level2Message)">
    <level3 v-bind='$attrs' v-on='$listenrs'></level3>
    </div>
    `,
    methods: {
        emitLevel2Data(data) {
            this.$emit('getLevel2Data', data) 
        }
    }
})

// level1.vue

Vue.component('level1', {
    data(){
        return {
            level3Message:"I am Level3",
            level2Message:"I am Level2"
        }
    },
    template:`
    <div>
    <h1>这是level1中的内容</h1>
    <level2 :level2Message="level2Message" 
            :level3Message="level3Message"
            @getLevel2Data="getLevel2Data()"
            @getLevel3Data="getLevel3Data()"
            ></level2>
    </div>
    `,
    methods: {
        getLevel2Data(data) {
            console.log('这是来自level2的数据', data)
        },
        getLevel3Data(data) {
            console.log('这是来自level3的数据', data)
        }
    }
})

// App.vue

var app = new Vue({
    el: "#app",
    template: `
    <div>
    <level1></level1>
    </div>
    `
})
```
**解析代码：**
1. level3组件能直接触发`getLevel3Data`是因为level2组件在调用level3组件时使用v-on绑定了$listeners属性；
2. 通过v-bind绑定了$attrs属性，level3组件可以直接获取到从level1传下来的props;

## v-model
父组件通过v-model传递值给子组件时，会自动传递一个value的props户型，在子组件中可以通过this.$emit('input',val)自动修改v-model绑定的值。
```

未完待续~