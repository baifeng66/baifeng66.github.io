+++
date = '2024-11-24T19:51:39+08:00'
title = 'Vue2+3'
categories = ["前端"]
tags = ["Vue"]

+++

# Vue2

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://v2.cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

## 起步

- [vue2安装页面](https://v2.cn.vuejs.org/v2/guide/installation.html)
- 所有代码只保留核心代码！！！

```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
```

### 第一个Vue程序

```html
		<div id="app">
    </div>
    <script>
      const vm = new Vue({
            template : '<h1>hello vue!<h1/>'
        })
        vm.$mount('#app')
    </script>
```

对第一个程序进行解释说明：

1. 当使用 script 引入 vue.js 之后，**Vue** 会被注册为一个全局变量。就像引入 jQuery 之后，jQuery 也会被注册为一个全局变量一样。

2. 我们必须 new 一个 Vue 实例，因为通过源码可以看到 this 的存在。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124195949169.png" alt="image-20241124195949169" style="zoom:50%;" />

3. Vue 的构造方法参数是一个 options 配置对象。配置对象中有大量 Vue 预定义的配置。每一个配置项都是

key:value 结构。一个 key:value 就是一个 Vue 的配置项。

4. template 配置项：value 是一个**模板字符串**。在这里编写符合 Vue 语法规则的代码（Vue 有一套自己规定的语法规则）。写在这里的字符串会被 Vue 编译器进行编译，将其转换成浏览器能够识别的 HTML 代码。template称之为模板。

5. Vue 实例的$mount 方法：这个方法完成挂载动作，将 Vue 实例挂载到指定位置。也就是说将 Vue 编译后的

HTML 代码**渲染**到页面的指定位置。注意：指定位置的元素被**替换**。

6. ‘#app’的语法类似于 CSS 中的 id 选择器语法。表示将 Vue 实例挂载到 id=’app’的元素位置。当然，如果编写

原生 JS 也是可以的：vm.$mount(document.getElementById(‘app’))

7. ‘#app’是 id 选择器，也可以使用其它选择器，例如类选择器：’.app’。类选择器可以匹配多个元素（位置），

这个时候 Vue 只会选择第一个位置进行挂载（从上到下第一个）。

### data配置项

在 Vue 中有一个 data 配置项，它可以帮助我们动态的渲染页面。

```html
		<div id="app">
    </div>
    <script>
      const vm = new Vue({
            data: {
                msg: 'hello vue1!'
            },
            template : '<h1>{{msg}}<h1/>'
        })
        vm.$mount('#app')
    </script>
```

对以上程序进行解释说明：

1. data 是 Vue 实例的数据对象。并且这个对象必须是纯粹的对象 (含有零个或多个的 key/value 对)。
2. {{message}}是 Vue 框架自己搞的一个语法，叫做**插值语法**（或者叫做胡子语法），可以从 data 中根据 key 来获取 value，并且将 value 插入到对应的位置。

3. data 可以是以下几种情况，但不限于这几种情况：

   ```html
   <div id="app">
       </div>
       <script>
         const vm = new Vue({
               data: {
                   msg: 'hello vue1!', // 取值语法： {{msg}}
                 	user: { // 取值语法： {{user.id}}
                     id: 101, 
                     name: 'jack'
                   },
                 	colors: [yellow,red,black], // 取值语法： {{colors[0]}}
               },
               template : '<h1>{{msg}}<h1/>'
           })
           vm.$mount('#app')
       </script>
   ```

   

4. 以上程序执行原理：Vue 编译器对 template 进行编译，遇到胡子{{}}时从 data 中取数据，然后将取到的数据插到对应的位置。生成一段 HTML 代码，最终将 HTML 渲染到挂载位置，呈现。

5. 当 data 发生改变时，template 模板会被重新编译，重新渲染。

### template配置项

1. template 只能有一个根元素。

```html
 		<div id="app">
    </div>
    <script>
      const vm = new Vue({
            data: {
                msg: 'hello vue1!',
                name: 'bob'
            },
            template : '<h1>{{msg}}</h1><h1>{{name}}</h1>',
        		//修正方法 template : '<div> <h1>{{msg}}</h1><h1>{{name}}</h1> </div>'
        })
        vm.$mount('#app')
    </script>
```

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124200947851.png" alt="image-20241124200947851" style="zoom:50%;" />

2. template 编译后进行渲染时会将挂载位置的原有元素**替换**。

3. template 后面的代码如果需要换行的话，建议将代码写到``符号当中，不建议使用 + 进行字符串的拼接。

   ```html
   		<div id="app">
       </div>
       <script>
         const vm = new Vue({
               data: {
                   msg: 'hello vue1!',
                   name: 'bob'
               },
               template : `
                   <div>
                       <h1>{{msg}}</h1>
                       <h1>{{name}}</h1>
                   </div>
               `
           })
           vm.$mount('#app')
       </script>
   ```

4. template 配置项可以省略，将其直接编写到 HTML 代码当中。

   ```html
   		<div id="app">
         <h1>{{msg}}</h1>
         <h1>{{name}}</h1>
       </div>
       <script>
         const vm = new Vue({
           data: {
             msg: "hello vue1!",
             name: "bob",
           },
         });
         vm.$mount("#app");
       </script>
   ```

   需要注意两点：

   第一：这种方式不会产生像 template 那种的元素替换。

   第二：虽然是直接写到 HTML 代码当中的，但以上程序中第 3~6 行已经不是 HTML 代码了，它是具有 Vue 语法特色的模板语句。这段内容在 data 发生改变后都是要重新编译的。

5. 将 Vue 实例挂载时，也可以不用$mount 方法，可以使用 Vue 的 el 配置项。

```html
		<div id="app">
      <h1>{{msg}}</h1>
      <h1>{{name}}</h1>
    </div>
    <script>
      new Vue({
        data: {
          msg: "hello vue1!",
          name: "bob",
        },
        el: '#app'
      })
    </script>
```

el 是 element 单词的缩写，翻译为“元素”，el 配置项主要是用来指定 Vue 实例关联的容器。也就是说 Vue 所管

理的容器是哪个。

## 核心技术

### 事件处理

(1) 指令的语法格式：<标签 v-指令名:参数=”表达式”></标签>

(2) 事件绑定的语法格式：v-on:事件名。例如鼠标单击事件的绑定使用 v-on:click。

(3) 绑定的回调函数需要在 Vue 实例中使用 methods 进行注册。methods 可以配置多个回调函数，采用逗号隔开。

(4) 绑定回调函数时，如果回调函数没有参数，( )可以省略。

(5) 每一个回调函数都可以接收一个事件对象 event。

(6) 如果回调函数有参数，并且还需要获取事件对象，可以使用$event 进行占位。

(7) v-on:click 可以简写为@click。简写的语法格式：@事件名

(8) 回调函数中的 this 是 vm。如果回调函数是箭头函数的话，this 是 window 对象，因为箭头函数没有自己的 this，它的 this 是继承过来的，默认这个 this 是箭头函数所在的宿主对象。这个宿主对象其实就是它的父级作用域。而对象又不能构成单独的作用域，所以这个父级作用域是全局作用域，也就是 window。

(9) 回调函数并没有在 vm 对象上，为什么通过 vm 可以直接调用函数呢？尝试手写 Vue 框架。[**数据代理**]

(10) 可以在函数中改变 data 中的数据，例如：this.counter++，这样会联动页面上产生动态效果。

```html
 <div id="app">
      <h1>{{msg}}</h1>
      <h1>{{name}}</h1>
   		<!--省略()-->
      <button v-on:click="m1">点击我触发事件1</button>
   		<!--v-on 简写 @-->
      <button @click="m2(2, $event)">点击我触发事件2</button>
      <button v-on:click="m3">点击我触发事件3</button>

    </div>
    <script>
      new Vue({
        data: {
          msg: "hello vue1!",
          name: "bob",
        },
        methods: {
           //方法定义方式一
            m1: function(){
                console.log('m1方法执行了');
                console.log(event);
              // vm
                console.log(this);
            },
          //方法定义方式二
            m2(num, event){
                console.log('m2方法执行了' + num);
                console.log(event);
            },
            m3: ()=>{
              // windows对象
                console.log(this)
            }
        },
        el: '#app'
      })
    </script>
```

#### 按键修饰符

1. 常用的按键修饰符包括：

(1) .enter

(2) .tab （只能配合 keydown 使用）

(3) .delete (捕获“删除”和“退格”键)

(4) .esc

(5) .space

(6) .up

(7) .down

(8) .left

(9) .right

2. 可以直接将 KeyboardEvent.key 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。

<input type=”text” @keyup.page-down=”getInfo”>

3. 可以通过全局 config.keyCodes 对象自定义按键修饰符别名

Vue.config.keyCodes.huiche = 13

#### 系统修饰键

1. 系统修饰键包括 4 个

   (1) .ctrl

   (2) .alt

   (3) .shift

   (4) .meta

2. 系统修饰键在使用时应注意：

   (1) 只有当系统修饰键和其他键组合使用，并且组合键释放时，才会触发 keyup 事件。

   (2) 只要按下系统修饰键，就会触发 keydown 事件。

3. 小技巧

```html
<input type=”text” @keyup.ctrl.c=”getInfo”/>
```

### 计算属性

data 中的是属性。用 data 的属性经过计算得出的全新的属性就是计算属性。

```html
		<div id="app">
      <h1>{{msg}}</h1>
      3. 输入的信息：<input type="text" v-model="info" /><br />
      4. 反转的信息：{{reversedInfo}} <br />
      5. 反转的信息：{{reversedInfo}} <br />
      6. 反转的信息：{{reversedInfo}} <br />
      7. 反转的信息：{{reversedInfo}} <br />
    </div>
    <script>
        new Vue({
            el: '#app',
            data: {
                msg : '计算属性-反转字符串案例',
                info: ''
            },
            computed: {
                reversedInfo:{
                    get(){
                        console.log('get方法执行');
                        return this.info.split('').reverse().join('')
                    },
                    set(newValue){
                        this.info = newValue.split('').reverse().join('')
                    }
                }
            }
        })
    </script>
```

(1) 计算属性需要使用：computed

(2) 计算属性通过 vm.$data 是无法访问的。计算属性不能通过 vm.$data 访问。

(3) 计算属性的 getter/setter 方法中的 this 是 vm。

(4) 计算属性的 getter 方法的调用时机：

1 第一个时机：初次访问该属性。

2 第二个时机：计算属性所依赖的数据发生变化时。

(5) 计算属性的 setter 方法的调用时机：

1 当计算属性被修改时。（在 setter 方法中通常是修改属性，因为只有当属性值变化时，计算属性的值就

会联动更新。注意：**计算属性的值被修改并不会联动更新属性的值**。）

(6) 计算属性没有真正的值，每一次都是依赖 data 属性计算出来的。

(7) 计算属性的 getter 和 setter 方法不能使用箭头函数，因为箭头函数的 this 不是 vm。而是 window。

> 只考虑读取，不考虑修改时，可以启用计算属性的简写形式

```js
	reversedInfo(){
          console.log('get方法执行');
          return this.info.split('').reverse().join('')    
      }
```

### **侦听属性**

1. 侦听属性的变化其实就是监视某个属性的变化。当被监视的属性一旦发生改变时，执行某段代码。
2. 监视属性变化时需要使用 watch 配置项。

3. 如何深度监视：

   (1) 监视多级结构中某个属性的变化，写法是：’a.b.c’ : {}。注意单引号哦。

   (2) 监视多级结构中所有属性的变化，可以通过添加深度监视来完成：deep : true

4. 如何后期添加监视：

   (1) 调用 API：vm.$watch(‘number1’, {})

5. watch 的简写：

   (1) 简写的前提：当不需要配置 immediate 和 deep 时，可以简写。

   (2) 如何简写？

   watch:{ number1(newVal,oldVal){}, number2(newVal, oldVal){} }

   (3) 后期添加的监视如何简写？

   vm.$watch(‘number1’, function(newVal, oldVal){})

6. computed 和 watch 如何选择？

   (1) 以上比较大小的案例可以用 computed 完成，并且比 watch 还要简单。所以要遵守一个原则：computed

   和 watch 都能够完成的，优先选择 computed。

   (2) 如果要开启异步任务，只能选择 watch。因为 computed 依靠 return。watch 不需要依赖 return。

7. 关于函数的写法，写普通函数还是箭头函数？

   (1) 不管写普通函数还是箭头函数，目标是一致的，都是为了让 this 和 vm 相等。

   (2) 所有 Vue 管理的函数，建议写成普通函数。

   (3) 所有不属于 Vue 管理的函数，例如 setTimeout 的回调函数、Promise 的回调函数、AJAX 的回调函数，

   建议使用箭头函数。

### **class** **与** **style** **绑定**

数据绑定的一个常见需求场景是操纵元素的 CSS class 列表和内联样式。因为 class 和 style 都是 attribute，我们

可以和其他 attribute 一样使用 v-bind 将它们和动态的字符串绑定。但是，在处理比较复杂的绑定时，通过拼接

生成字符串是麻烦且易出错的。因此，**Vue** **专门为** **class** **和** **style** **的** **v-bind** **用法提供了特殊的功能增强**。除了字

符串外，表达式的值也可以是对象或数组。

#### class绑定

前提：

```html
		<style>
        .static {
            border: 1px solid black;
            background-color: beige;
        }
        .big {
            width: 200px;
            height: 200px;
        }
        .small {
            width: 100px;
            height: 100px;
        }
      .activate {
        background-color: green;
      }
      .text-danger {
        color: 'red'
      }
    </style>
```

1. 绑定字符串

   适用于样式的名字不确定，需要动态指定。

   ```html
   <div class="static" :class="c1"></div>
   ```

2. 绑定数组

   适用于绑定的样式名字不确定，并且个数也不确定。

   ```html
   <div class="static" :class="['activate','small']"></div>
   <!-- data中 classArr : ['active', 'text-danger'] -->
   <div class="static" :class="classArr"></div>
   ```

3. 绑定对象

   适用于样式名字和个数都确定，但是要动态决定用或者不用。

   ```html
   <!-- data中 classObj : { 'activate': true,'text-danger':false}-->
   <div class="static" :class="classObj"></div>
   ```

#### syle绑定

1. 绑定对象

   ```html
   <!-- data 中 styleArr :{fontSize: '40px',color: 'red'}-->
   <div class="static" :style="styleObject">对象形式</div>
   ```

2. 绑定数组

   ```html
   <!-- data 中 styleObject :[{fontSize: '40px'},{color: 'red'}]-->
   <div class="static" :style="styleArr">对象形式</div>
   ```

### **条件渲染**

#### **v-if**

- 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 true 时才被渲染

#### **v-show**

- 另一个可以用来按条件显示一个元素的指令是 v-show。其用法基本一样。

- 不同之处在于 v-show 会在 DOM 渲染中保留该元素；v-show 仅切换了该元素上名为 display 的 CSS 属性。

- v-show 不支持在 <template> 元素上使用，也不能和 v-else 搭配使用。

#### **v-if** **VS** **v-show**

1. v-if 是“真实的”按条件渲染，因为它确保了在切换时，条件区块内的事件监听器和子组件都会被销毁与重建

2. v-if 也是惰性的：如果在初次渲染时条件值为 false，则不会做任何事。条件区块只有当条件首次变为 true 时才

被渲染。

3. 相比之下，v-show 简单许多，元素无论初始条件如何，始终会被渲染，只有 CSS display 属性会被切换。

4. 总的来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要频繁切换，则使用 v-show 较

好；如果在运行时绑定条件很少改变，则 v-if 会更合适。

### **列表渲染**

语法格式：v-for 指令。该指令用在被遍历的标签上。

in 是 Vue 的传统语法，主要是为了一致性，因为它与 JavaScript 的 for...in 语法风格相符。

```html
<template>
  <div>
    <p v-for="item in items" :key="item.id">{{ item.name }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { id: 1, name: "Item 1" },
        { id: 2, name: "Item 2" },
      ],
    };
  },
};
</script>
```

of 是现代 JavaScript 中 for...of 的语法，它在 Vue 中同样可以使用，功能与 in 基本一致。

```html
<template>
  <div>
    <p v-for="item of items" :key="item.id">{{ item.name }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { id: 1, name: "Item 1" },
        { id: 2, name: "Item 2" },
      ],
    };
  },
};
</script>
```

#### 列表过滤

可以搭配watch,computed使用

```html
delBugCallBack(id){
     this.bugList = this.bugList.filter(bug=>{
        return bug.id !== id
     })
    }
```



## 组件化

### 单文件组件

1. 什么是单文件组件？

​	(1) 一个文件对应一个组件（之前我们所学的是非单文件组件，一个 html 文件中定义了多个组件）

​	(2) 单文件组件的名字通常是：x.vue，这是 Vue 框架规定的，只有 Vue 框架能够认识，浏览器无法直接打

开运行。需要 Vue 框架进行编译，将 x.vue 最终编译为浏览器能识别的 html js css。

​	(3) 单文件组件的文件名命名规范和组件名的命名规范相同：

		- 全部小写：userlist
		- 首字母大写，后面全部小写：Userlist
		- kebab-case 命名法：user-list
		- CamelCase 命名法：UserList（我们使用这种方式，和 Vue 开发者工具呼应。）

2. x.vue 文件的内容包括三块：

   (1) 结构：<template>HTML 代码</template>

   (2) 交互：<script>JS 代码</script>

   (3) 样式：<style>CSS 代码</style>

3. export 和 import，ES6 的模块化语法。

   1. 使用 export 导出（暴露）组件，在需要使用组件的 x.vue 文件中使用 import 导入组件

记住一个要领：不管是单文件组件还是非单文件组件，永远都包括三步：创建组件、注册组件、使用组件。

### **Vue** **脚手架**

Vue 的脚手架（Vue CLI: Command Line Interface）是 Vue 官方提供的标准化开发平台。它可以将我们.vue 的

代码进行编译生成 html css js 代码，并且可以将这些代码自动发布到它自带的服务器上，为我们 Vue 的开发提供

了一条龙服务。脚手架官网地址：https://cli.vuejs.org/zh

注意：Vue CLI 4.x 需要 Node.js v8.9 及以上版本，推荐 v10 以上。

不过现在官方推荐使用Vite去搭建Vue程序~ [Vite官网](https://cn.vite.dev/)

脚手架架构

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124223454710.png" alt="image-20241124223454710" style="zoom:50%;" />

## **props** **配置**（父传子）

使用 props 配置可以接收其他组件传过来的数据，让组件的数据变为动态数据，三种接收方式：

1. 简单接收

   ```html
   props:['name','age','sex']
   ```

2. 接收时添加类型限制

   ```html
   props: {
   	name: String,
   	age: Number
   }
   ```

3. 接收时添加类型限制，必要性限制，默认值

   ```html
   props:{
   	name: {
   		type: String,
   		required: true
   },
   	age: {
   		type: Number,
   		default: 18
   }
   }
   ```

   其他组件怎么把数据传过来？

   ```html
   <User name="jack" age="20" sex="男"></User>
   ```

   注意事项：

   1. 不要乱接收，接收的一定是其它组件提供的。
   2. props 接收到的数据不能修改。（修改之后会报错，但页面会刷新。）可以找个中间变量来解决。

### **从父组件中获取子组件**数据

在组件上使用 ref 属性进行标识：

```html
<User ref=”userJack”></User>
```

在程序中使用$refs 来获取子组件:

```html
this.$refs.userJack
```

访问子组件的属性：

```html
this.$refs.userJack.name
```

访问子组件的子组件属性：

```html
this.$refs.userJack.$refs.name
```

ref 也可以使用在普通的 HTML 标签上，这样获取的就是这个 DOM 元素：

```html
<input type=”text” ref=”username”>
this.$refs.username
```

## BugList案例

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125173956568.png" alt="image-20241125173956568" style="zoom:50%;" />

### 实现功能

1. 先使用静态组件的方式把页面效果实现出来。

2. 在 BugList.vue 中提供 bugList 数据，实现动态数据展示。

3. 保存 bug

   (1) 获取用户输入的信息采用双向数据绑定。

   ​	通过 Date.now()获取时间戳的方式来搞定 id。

   (2) 将 BugList.vue 中的 bugList 数据提升到父组件 App.vue 中。

   (3) 父组件向子组件传递，采用 :bugList=”bugList”，在子组件当中使用 props 接收。

   (4) 子组件向父组件传递，父组件可以提前定义一个函数，将函数传递给子组件，在子组件中调用这个函数

   即可。

   (5) 该功能的小问题：

   ​	保存完成后自动清空。

   ​	输入为空时不能保存（可以加 trim 去除空白），并且提示必须输入。

4. 修改 bug 的状态

   (1) 勾选和取消勾选，会触发 click 事件或者 change 事件。

   (2) 事件发生后，获取 bug 的 id，将 id 传递给 App 组件中的回调函数，遍历数组，拿到要修改的 bug 对象，

   更改 bug 对象的 resolved 属性值。

5. 删除 bug

   (1) 删除时可以调用数组的 filter 方法进行过滤，将过滤之后的新数组赋值给 this.bugList

6. 统计 bug

   (1) 第一种：普通计数器统计。

   (2) 第二种：数组的 reduce 方法完成条件统计。

7. 全选和取消全选

   (1) 全选复选框的状态维护：

   ​	已解决的数量 === 总数量 时，勾选。

   ​	全部删除后，应该取消勾选。

   (2) 全部删除了可以将 footer 隐藏。v-show

   (3) 全选和取消全选

8. 清除已解决

   (1) 调用数组的 filter 方法进行过滤，生成新数组，将其赋值给 this.bugList

9. 实现编辑功能

   (1) 功能描述

   1 

   鼠标移动到描述信息上之后，光标变成小手。

   2 

   点击描述信息之后，将描述信息放入文本框。并且同时让文本框获得焦点。

   3 

   用户开始修改描述信息（要注意避免将信息修改为空）

   4 

   输入修改信息之后，文本框失去焦点，显示修改后的描述信息。

   (2) 实现功能的核心技术：

   ​	 给 bug 对象扩展一个具有响应式的 editState 属性，如果是 true 表示处于编辑状态，false 表示处于

   未编辑状态：this.$set(bug, ‘editState’, true)

   获得焦点的动作如何完成：

   1) 在文本框上添加 ref=”inputDesc”，然后通过 this.$refs.inputDesc 获取到 dom 元素，调用 focus()

   让其获取焦点。

   2) 以上操作需要在下一次渲染 DOM 完成后执行：nextTick

   ​	this.$nextTick(function(){this.$refs.inputDesc.focus()})

## Vuex

vuex 是实现数据集中式状态管理的插件。数据由 vuex 统一管理。其它组件都去使用 vuex 中的数据。只要有

其中一个组件去修改了这个共享的数据，其它组件会同步更新。一定要注意：全局事件总线和 vuex 插件的区别：

1. 全局事件总线关注点：组件和组件之间数据如何传递，一个绑定$on，一个触发$emit。数据实际上还是在局部的组件当中，并没有真正的让数据共享。只是数据传来传去。

   <img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125174635940.png" alt="image-20241125174635940" style="zoom:50%;" />

2. vuex 插件的关注点：共享数据本身就在 vuex 上。其中任何一个组件去操作这个数据，其它组件都会同

   步更新。是真正意义的数据共享。

   <img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125174657152.png" alt="image-20241125174657152" style="zoom:50%;" />

   使用 vuex 的场景是：多个组件之间依赖于同一状态。来自不同组件的行为需要变更同一状态。

   ### 搭建环境

   1. 安装 vuex

      (1) vue2 安装 vuex3 版本

      ​	npm i vuex@3

      (2) vue3 安装 vuex4 版本

      ​	npm i vuex@4

   2. 创建目录和 js 文件（目录和文件名不是必须叫这个）

      (1) 目录：vuex

      (2) js 文件：store.js

   3. 在 store.js 文件中创建核心 store 对象，并暴露

      ```js
      import Vue from "vue";
      import Vuex from 'vuex'
      
      Vue.use(Vuex)
      
      let actions = {}
      let mutations = {}
      let state = {}
      
      // let store = new Vuex.Store({
      //     actions: actions,
      //     mutations: mutations,
      //     state: state
      // })
      
      // export default store
      
      // 10-16 简写
      export default new Vuex.Store({ actions, mutations, state })
      ```

   4. 在 main.js 文件中关联 store，这一步很重要，完成这一步之后，所有的 vm 和 vc 对象上会多一个$store 属性

      <img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125175854779.png" alt="image-20241125175854779" style="zoom:50%;" />

### **多组件数据共享**

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125184302330.png" alt="image-20241125184302330" style="zoom:50%;" />

store.js

```js
import Vue from "vue";
import Vuex from 'vuex'

Vue.use(Vuex)

let actions = {
    saveUser(context, value) {
        context.commit('SAVEUSER', value)
    },
    saveVip(context, value) {
        context.commit('SAVEVIP', value)
    }
}

let mutations = {
    SAVEUSER(state, value) {
        state.users.unshift(value)
    },
    SAVEVIP(state, value) {
        state.vips.unshift(value)
    }
}

let state = {
    users: [
        { id: 1, name: "张三" },
        { id: 2, name: "李四" },
        { id: 3, name: "王五" },
    ],
    vips: [
        { id: 1, name: "孙悟空" },
        { id: 2, name: "猪八戒" },
        { id: 3, name: "税务师" },
    ],
}

let store = new Vuex.Store({
    actions: actions,
    mutations: mutations,
    state: state
})

export default store
```

UserList.vue

```vue
<template>
  <div>
    <h1>{{ type1 }}列表</h1>
    <input type="text" v-model="username" />
    <button @click="saveUser">保存{{ type1 }}</button>
    <ul>
      <li v-for="user of $store.state.users" :key="user.id">
        {{ type1 }}名：{{ user.name }}
      </li>
    </ul>
    <h3>当前用户数量：{{ $store.state.users.length }}</h3>
    <h3>当前会员数量：{{ $store.state.vips.length }}</h3>
  </div>
</template>

<script>
export default {
  name: "UserList",
  props: ["type1"],
  data() {
    return {
      username: "",
    };
  },
  methods: {
    saveUser() {
      let userObj = { id: Date.now(), name: this.username };
      // this.$store.state.users.unshift(userObj);
      this.$store.dispatch("saveUser", userObj);
    },
  },
};
</script>

<style scoped></style>
```

VipList.vue

```vue
<template>
  <div>
    <h1>{{ type1 }}列表</h1>
    <input type="text" v-model="username" />
    <button @click="saveVip">保存{{ type1 }}</button>
    <ul>
      <li v-for="vip of $store.state.vips" :key="vip.id">
        {{ type1 }}名：{{ vip.name }}
      </li>
    </ul>
    <h3>当前用户数量：{{ $store.state.users.length }}</h3>
    <h3>当前会员数量：{{ $store.state.vips.length }}</h3>
  </div>
</template>

<script>
export default {
  name: "VipList",
  props: ["type1"],
  data() {
    return {
      username: "",
    };
  },
  methods: {
    saveVip() {
      let vipObj = { id: Date.now(), name: this.username };
      //   this.$store.state.vips.unshift(vipObj);
      this.$store.dispatch("saveVip", vipObj);
    },
  },
};
</script>

<style scoped></style>
```

### 扩展运算符

https://blog.csdn.net/astonishqft/article/details/82899965

**`对象中的扩展运算符(...)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中`**

## Router

### **路由** **route** **与路由器** **router**

每一个路由都由 key 和 value 组成。

key1+value1===>路由 route1

key2+value2===>路由 route2

key3+value3===>路由 route3

...... 

路由的本质：一个路由表达了一组对应关系。

路由器的本质：管理多组对应关系。 

**Vue** **中路由的工作原理：**

![image-20241125192318106](https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125192318106.png)

### **使用路由**

1. vue-router 也是一个插件，安装 vue-router

​	(1) vue2 要安装 vue-router3

​	npm i vue-router@3

​	(2) vu3 要安装 vue-router4

​	npm i vue-router@4

2. main.js 中引入并使用 vue-router

​	(1) 导入：import VueRouter from ‘vue-router’

​	(2) 使用：Vue.use(VueRouter)

​	(3) new Vue 时添加新的配置项：一旦使用了 vue-router 插件，在 new Vue 的时候可以添加一个全新的配置项：**router**

3. router 路由器的创建一般放在一个独立的 js 文件中，例如：router/index.js

​	(1) 创建 router 目录

​	(2) 创建 index.js，在 index.js 中创建路由器对象，并且将其暴露。然后在 main.js 文件中引入该路由器即可。

4. 使用 router-link 标签代替 a 标签（App.vue 中）router-link 标签最终编译之后的还是 a 标签。vue-router 库帮助我们完成的。
5. 添加激活样式,使用 active-class 属性，在激活时添加样式：selected

注意事项：

路由组件一般会和普通组件分开存放，路由组件放到 pages 目录，普通组件放到 components 目录下。

路由组件在进行切换的时候，切掉的组件会被销毁。

路由组件实例比普通组件实例多两个属性：$route 和$router

​	$route：属于自己的路由对象。

​	$router：多组件共享的路由器对象。

```js
import VueRouter from "vue-router";

import TeaList from "@/pages/TeaList.vue";
import FruitList from "@/pages/FruitList.vue";
import ShangDong from "@/pages/ShangDong.vue";
import XingJiang from "@/pages/XingJiang.vue";

let router = new VueRouter({
    routes: [
        {
            path: '/tea', component: TeaList
        },
        {
            path: '/fruit', component: FruitList,
            // 子路由
            children: [
                { path: 'shangdong', component: ShangDong },
                { path: 'xinjiang', component: XingJiang },
            ]
        }
    ]
})

export default router
```

### **路由的** **props**

props 配置主要是为了简化 query 和 params 参数的接收。让插值语法更加简洁。

第一种实现方式：

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125195021151.png" alt="image-20241125195021151" style="zoom:50%;" />

第二种实现方式：函数式

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125195900446.png" alt="image-20241125195900446" style="zoom:50%;" />

### **编程式路由导航**

需求中可能不是通过点击超链接的方式切换路由，也就是说不使用<router-link>如何实现路由切换。可以通过相关 API 来完成：

push模式:

```js
this.$router.push({
  name: '',
  query:{}
})
```

replace模式：

```js
this.$router.replace({
  name: '',
  query:{}
})
```

前进后退：

```js
this.$router.forward() // 前进
this.$router.back()	// 后退
this.$router.go() //前进/后退
```

### **路由守卫**

#### **全局前置守卫**

```js
router.beforeEach((to, from, next) => {
    let username = 'zs'
    console.log(to.name);

    if (to.name === 'sd') {
        if (username === 'admin') {
            next()
        } else {
            alert('无权限!!')
        }
    } else {
        next()
    }
})
```

这种路由守卫称为全局前置路由守卫

初始化时执行一次，以后每一次切换路由之前调用一次

如果路由组件较多。to.path 会比较繁琐，可以考虑给需要鉴权的路由扩展一个布尔值属性，可以通过路由元来定

义属性：**meta:{isAuth : true}**

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125202140202.png" alt="image-20241125202140202" style="zoom:50%;" />

# Vue3

```
npm init vue@latest
```

## setup

1. setup 是一个函数，vue3 中新增的配置项
2. 组件中所用到的 data、methods、computed、watch、生命周期钩子....等，都要配置到 setup 中
3. setup 函数的返回值
   1. 返回一个对象，该对象的属性、方法等均可以在模板语法中使用，例如插值语法。
   2. 返回一个渲染函数，从而执行渲染函数，渲染页面。
4. **setup** **中的** **this** **是** **undefined****。所以** **setup** **中** **this** **是不可用的。**

## **ref** **函数（实现响应式）**

1. ref 是一个函数。完成响应式的

2. ref 使用时需要 import {ref} from 'vue'

3. 凡是要实现响应式的 data，需要使用 ref 函数进行包裹

   let name = ref(‘李四’)

   输出 name，你会发现，它是 **RefImpl** 对象（引用实现的实例对象，简称引用对象），在这个引用对象

   当中有一个 value 属性，value 属性的实现原理是：Object.defineProperty，通过它实现了响应式处理。

4. 修改数据的话必须这样做：name.value = ‘王五’

## **reactive**

注意：这个函数不能为简单类型服务，只能为**对象类型**服务

注意：reactive 为响应式做了递归处理。对象中的对象中的对象的属性，也是响应式的。

**重点** **1**

并且数组如果使用 reactive 函数包裹的话，数组中的数据，在通过下标去修改的时候，也是支持响应式的。（这个在 Vue2 中是不支持的。）

**重点** **2**

在开发中一般很少使用 ref，一般会使用 reactive。即使是简单类型的数据，也会将其存放到一个对象中，使用reactive 函数进行包括。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125205025665.png" alt="image-20241125205025665" style="zoom:50%;" />

## props

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241125205243013.png" alt="image-20241125205243013" style="zoom:50%;" />

























