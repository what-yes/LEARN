# Vue

## 创建Vue脚手架

1. 切换到要创建项目的目录，然后使用命令创建项目

   vue create xxx

   xxx是小写，当然，包含大写的时候会报错提示不能有大写。

2. 启动项目

   npm run serve

## 项目文件或文件夹作用：

![image-20211206221007938](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211206221007938.png)



### .gitignore

 git忽略文件

### babel.config.js

babel的控制文件

### package-lock.json

包版本控制文件

### package.json

包说明书

### assets

放静态资源

### components

放所有组件除了 App.vue

### main.js 

该文件是整个项目的**入口文件**。

```vue
new Vue({
  render: h => h(App),
}).$mount('#app')
```

等价于

```vue
new Vue({
  el: "#root",
  render: h => h(App),
})
```

#### 为什么这里是render?

因为 `import Vue from 'vue'`引入的是精简版的vue.js，不能够解析template代码：

```vue
template: `
<app></app>
`,
components: {App}
```

所以这里得用render。

## 配置文件

### 查看配置文件：

`vue inspect > output.js` 会生成一个配置文件。

生成的配置文件会报很多错，加上 `const a =` 就行了，因为后面的是一个对象，单独出现一个对象不合理吧。

但是这个文件只是让你看配置文件的，不能修改（修改没有作用）。

`public` `favicon` `index.html` `src` `main.js` 不能改。不能改是指名字不能改，但内容可以。

### 修改配置文件

创建文件名为 `vue.config.js` 的文件，根据官网的配置对相关配置进行修改。

语法检查：`lintOnSave: false`

## 关键字

ref

类似于id，

```vue
<h1 v-text="msg" ref="title"></h1>

alert(this.$refs.title);
console.log(this.$refs.title);
```



props

假如有个组件叫 School

```vue
<School country="China"/>
```

传入参数country

在定义组件时用props接收这个参数：

```vue
export default {
    name: 'School',
    data() {
    return {
      name: "DLUT",
      address: "Dalian"
        }
    },
    
    props:['country']
    
};
```

mixin

用于重用配置

1. 创建重用代码

```js
// a.js
export const chongyong = {
    重用代码
}
```

2. 导入

```js
<script>
    import {chongyong} from "xxx/a.js"
    new Vue({
        
        ...
    mixins: [chongyong]
    
})
</script>
```

slot

自定义组件 diy.vue

```vue
<template>
	<h1>
        ....
    </h1>
	<slot></slot>
</template>
```

使用插槽，会自动把img放到插槽那个位置

```vue
<diy>
	<img> 
</diy>
```

router

路由

创建页面

Home.vue

About.vue

路由文件 router.js

```js
import ...

export default new VueRouter({
    routes:[
        {
            path:"/Home",
            component: Home
        },
        {
            path:"/About",
            component:About
        }
    ]
})
```



配置main.js

```vue
import myrouter from "xxx/router.js"

new Vue({
	....
	router: myrouter
})
```

