# Vue.js
```
var vm = new Vue({
    el : "#theelement",
    data : {
        items : []
    },
    methods : {

    },
    watch : {

    },
    computed : {

    }
})
```

## 指令
    v-bind:title 元素的属性不能直接引用data中的数据，要使用v-bind绑定。
    v-html 直接渲染html元素
    v-on: 事件，可以简写为@
      - click
      - keyup
      - dblclick
      - mousemove

    v-text
    v-model.lazy
    v-for=
    v-model=

## 事件
v-on 可以简写为 @

事件修改器：
v-on:click.once
v-on:click.prevent
v-on:keyup.enter
v-on:keyup.alt.enter

## Conditionals
v-if= 会从DOM中移除元素
v-else
v-if-else
v-show 不会移除，而是display:none

## Looping with v-for

<template v-for = "(ninja, index) in ninjas">
</template>

(val, key) in ninja

不会输出template


## 钩子函数
beforeCreate
created - 发送请求获取数据
beforeMount
mounted
beforeUpdate
updated

## 组件

Vue 最核心的部分之一。

全局注册：data跟Vue实例中不一样，只能是函数。
```
    Vue.component('my-component', {
        template: `
            <div>

            </div>
        `,
        data: function() {
            return {
                item : [
                    "hello",
                    "world"
                ]
            }
        }
    })
```
template多行的话需要用反引号（template literals）括起来。html中使用组件的时候需要使用Vue实例初始化。

```
<div id = "componet1">
    <my-component></my-componet>
</div>

new Vue({
    el: "#component1"
})
```

## Refs
<div ref="refname">

this.$refs.refname.value

Angular
React
Vue

data中的指令不需要通过this.data.content来引用。可以直接this.content。

通过ajax来填充data：created函数

## vue-cli
* use es6 features
* compile & minify our js into 1 file
* use single file templates
* compile everything on our machine, not in a browser
* live reload dev server

vue init <template> <project>
```
npm install -g vue-cli
vue init webpack my-project
cd my-project
npm install
npm run dev
```

component: global, local



template <div></div>
script export defualt {data(){}}
style scoped


## props

向子模块传递数据

v-bind:ninjas

props {
    type:Array,
    required:true
}

## events : to parent
child component: this.$emit('changeTitle', 'Vue Wizards')
parent component: v-on:changeTitle="updateTitle"

event-bus:
export const bus = new Vue();

import {bus} from "../main";


## slot
传递数据的方式之一。
<h2 slot="title"></h2>

<slot name="title">

单页web应用
    ExtJS

## dynamic component
<keep-alive>
<component v-bind:is=""></component>
</keep-alive>

## 插件
* vue-router
* vue-resource
* vee-validate

* traversymedia.com

## router
this.$route.params.id

  this.$http.get(URL).then(function(data){
      this.blog = data.body;
  })


## vee-validate
v-validate="required|email"
data-vv-delay="1000"


jsonplacehlod
