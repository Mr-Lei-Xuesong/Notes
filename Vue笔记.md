# MVVM模式

![](.\img\143.jpg)

* Model层：
  * 数据层
  * 数据可能是我们固定的死数据，更多的是来自我们服务器，从网络上请求下来的数据
* View层：
  * 视图层
  * 通常就是DOM层，主要的作用是给用户展示各种信息
* VueModel层：
  * 视图模型层
  * 视图模型层是View和Model沟通的桥梁
  * 一方面它实现了Data Binding，也就是数据绑定，将Model的改变实时的反应到View中
  * 另一方面它实现了DOM Listener，也就是DOM监听，当DOM发生一些事件(点击、滚动、touch等)时，可以监听到，并在需要的情况下改变对应的Data

# VUE实例

* 在创建Vue实例的时候，传入了一个对象options
  * el:
    * 类型：string | HTMLElement
    * 作用：决定之后Vue实例会管理哪一个DOM
  * data:
    * 类型：Object | Function （组件当中data必须是一个函数）
    * 作用：Vue实例对应的数据对象
  * methods:
    * 类型：{ [key: string]: Function }
    * 作用：定义属于Vue的一些方法，可以在其他地方调用，也可以在指令中使用

# VUE的生命周期

<img src=".\img\144.jpg" style="zoom: 50%;" />

<img src="D:\Notes\img\145.jpg" style="zoom:150%;" />

# 模板语法

## 插值操作

* **Mustache语法**
  * mustache是胡须的意思，因为`{{}}`像胡须，又叫大括号语法
  * 在vue对象挂载的dom元素中，`{{}}`不仅可以直接写变量，还可以写简单表达式
* **v-once**
  *  v-once表示该dom元素只渲染一次，之后数据改变，不会再次渲染
* **v-html**
  * 转化为html页面的形式展示
* **v-text**
  * v-text会覆盖dom元素中的数据，相当于js的innerHTML方法
* **v-pre**
  * 用于跳过这个元素和它子元素的编译过程，用于显示原本的Mustache语法
* **v-cloak**
  * 有时候因为加载延时问题,数据没有及时刷新,就造成了页面显示从`{{message}}`到message变量“你好啊”的变化，这样闪动的变化，会造成用户体验不好,此时需要使用到`v-cloak`的这个标签。在vue解析之前，div属性中有`v-cloak`这个标签，在vue解析完成之后，v-cloak标签被移除。简单，类似div开始有一个css属性`display:none;`，加载完成之后，css属性变成`display:block`，元素显示出来。

## 动态绑定属性

* v-bind指令
  * 作用：动态绑定属性
  * 缩写：**:**
  * 预期：pany (with argument) | Object (without argument)
  * 参数：pattrOrProp (optional)

### v-bind绑定class

* 绑定方式：对象语法

  * 对象语法的含义是:class后面跟的是一个对象

    ```html
    用法一：直接通过{}绑定一个类
    <h2 :class="{'active': isActive}">Hello World</h2>
    
    用法二：也可以通过判断，传入多个值
    <h2 :class="{'active': isActive, 'line': isLine}">Hello World</h2>
    
    用法三：和普通的类同时存在，并不冲突
    注：如果isActive和isLine都为true，那么会有title/active/line三个类
    <h2 class="title" :class="{'active': isActive, 'line': isLine}">Hello World</h2>
    
    用法四：如果过于复杂，可以放在一个methods或者computed中
    注：classes是一个计算属性
    <h2 class="title" :class="classes">Hello World</h2>
    ```

* 绑定方式：数组语法

  * 数组语法的含义是:class后面跟的是一个数组

    ```html
    用法一：直接通过{}绑定一个类
    <h2 :class="['active']">Hello World</h2>
    
    用法二：也可以传入多个值
    <h2 :class=“[‘active’, 'line']">Hello World</h2>
    
    用法三：和普通的类同时存在，并不冲突
    注：会有title/active/line三个类
    <h2 class="title" :class=“[‘active’, 'line']">Hello World</h2>
    
    用法四：如果过于复杂，可以放在一个methods或者computed中
    注：classes是一个计算属性
    <h2 class="title" :class="classes">Hello World</h2>
    ```

### v-bind绑定style

* 在写CSS属性名的时候，比如font-size
  * **我们可以使用驼峰式 (camelCase) fontSize**
  * **或短横线分隔 (kebab-case，记得用单引号括起来) ‘font-size’** 

* 绑定方式：对象语法

  ```
  :style="{color: currentColor, fontSize: fontSize + 'px'}"
  
  style后面跟的是一个对象类型
  	对象的key是CSS属性名称
  	对象的value是具体赋的值，值可以来自于data中的属性
  ```

* 绑定方式：数组语法

  ```html
  :style="[baseStyles, overridingStyles]"></div>
  
  style后面跟的是一个数组类型
  	多个值以，分割即可
  ```

## 计算属性

> 在某些情况下，可能需要对数据进行一些转化后再显示，或者需要将多个数据结合起来进行显示

```html
<div id="app">
    <h2>{{fullName}}</h2>
</div>
<script src="../../js/vue.js"></script>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            firstName: 'Lucy',
            lastName: 'Tom'
        },
        //计算属性
        computed: {
            fullName: function () {
                return this.firstName + ' ' + this.lastName
            }
        }
    })
</script>
```

```html
<div id="app">
    <h2>总价格:{{totalPrice}}</h2>
</div>
<script src="../../js/vue.js"></script>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            books: [
                {id: 110, name: 'VueJs', price: 99},
                {id: 111, name: 'Java编程思想', price: 87},
                {id: 112, name: 'Spring实战', price: 66},
                {id: 113, name: 'VueJs', price: 88}
            ]
        },
        computed: {
            totalPrice: function () {
                let result = 0;
                /*for (let i = 0; i < this.books.length; i++) {
                    result += this.books[i].price
                }
                return result*/

                /*for (let i in this.books) {
                    result += this.books[i].price
                }
                return result*/

                for (let book of this.books) {
                    result += book.price
                }
                return result
            }
        }
    })
</script>
```

### 计算属性的setter和getter

> 每个计算属性都包含一个getter和setter

```html
<div id="app">
    <h2>{{fullName}}</h2>
    <h2>{{firstName}}</h2>
    <h2>{{lastName}}</h2>
</div>
<script src="../../js/vue.js"></script>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            firstName: 'Tom',
            lastName: 'Jerry'
        },
        computed: {
            fullName: {
                get() {
                    console.log("===调用了fullName的Get方法");
                    return this.firstName + ' ' + this.lastName
                },
                set(newValue) {
                    console.log("===调用了fullName的Set方法",newValue);
                    const names = newValue.split(" ");
                    this.firstName = names[0];
                    this.lastName = names[1]
                }
            }
        }
    })
</script>
```

## 事件监听

* v-on
  * 作用：绑定事件监听器
  * 缩写：@
  * 预期：Function | Inline Statement | Object
  * 参数：event

### v-on参数

* 当通过methods中定义方法，以供@click调用时，需要注意参数问题：
  * 如果该方法不需要额外参数，那么**方法后的()可以不添加**
    * 注意：如果方法本身中有一个参数，那么会**默认将原生事件event参数传递进去**
  * 如果需要同时传入某个参数，同时需要event时，可以通过**$event**传入事件

### v-on修饰符

* 在某些情况下，我们拿到event的目的可能是进行一些事件处理
  * .**stop**
    * 调用event.stopPropagation()
  * **.prevent**
    * 调用event.preventDefault
  * **.{keyCode | keyAlias}**
    * 只当事件是从特定键触发时才触发回调
  * **.native**
    * 监听组件根元素的原生事件
  * **.once**
    * 只触发一次回调

## 条件判断

* v-if、v-else-if、v-else

  * v-if后面的条件为false时，对应的元素以及其子元素不会渲染

    ```html
    <div id="app">
      <div v-if="score>=90">优秀</div>
      <div v-else-if="score>=80">良好</div>
      <div v-else-if="score>=60">及格</div>
      <div v-else>不及格</div>
    </div>
    <script src="../../js/vue.js"></script>
    <script>
      let app = new Vue({
        el: '#app',
        data: {
          score: 98
        }
      })
    </script>
    ```

* v-show

  * 是否渲染一个元素

    ```html
    <div id="app">
      <button @click="toggle">切换显示</button>
      <h2 v-show="isShow">我要不要显示</h2>
    </div>
    <script src="../../js/vue.js"></script>
    <script>
      let app = new Vue({
        el: '#app',
        data: {
          isShow: true
        },
        methods: {
          toggle() {
            this.isShow = !this.isShow
          }
        }
      })
    </script>
    ```

* **注意**：
  * v-if当条件为false时，压根不会有对应的元素在DOM中
  * v-show当条件为false时，仅仅是将元素的display属性设置为none而已

## 循环遍历

* v-for

  ```html
  <div id="app">
    <ul>
      <li v-for="(name,index) in names">{{index+1}}-{{name}}</li>
    </ul>
  </div>
  <script src="../../js/vue.js"></script>
  <script>
    let app = new Vue({
      el: '#app',
      data: {
        names:[
            "Tom",
            "Jerry",
            "Taylor Swift"
        ]
      }
    })
  </script>
  ```

## 表单绑定

> 使用v-model指令来实现表单元素和数据的双向绑定

* v-model

  ```html
  <div id="app">
    <input type="text" v-model="message">
    <h2>{{message}}</h2>
  </div>
  <script src="../../js/vue.js"></script>
  <script>
    let app = new Vue({
      el: '#app',
      data: {
        message: '双向绑定'
      }
    })
  </script>
  ```

### 修饰符

* **lazy修饰符：**
  * 默认情况下，v-model默认是在input事件中同步输入框的数据的。
  * 也就是说，一旦有数据发生改变对应的data中的数据就会自动发生改变。
  * plazy修饰符可以让数据在失去焦点或者回车时才会更新：
* **number修饰符：**
  * 默认情况下，在输入框中无论我们输入的是字母还是数字，都会被当做字符串类型进行处理
  * 但是如果我们希望处理的是数字类型，那么最好直接将内容当做数字处理。
  * number修饰符可以让在输入框中输入的内容自动转成数字类型
* **trim修饰符：**
  * 如果输入的内容首尾有很多空格，通常我们希望将其去除
  * trim修饰符可以过滤内容左右两边的空格

# 组件化

## 注册组件的基本步骤

![](D:\Notes\img\146.jpg)

1. 创建组件构造器

2. 注册组件

3. 使用组件

   ```html
   <div id="app">
     <!--使用组件-->
     <my-cpn></my-cpn>
   </div>
   <script src="../../js/vue.js"></script>
   <script>
     //1.创建组件构造器对象
     const create = Vue.extend({
       template: `
         <div>
           <h2>我是标题</h2>
           <p>我是内容，哈哈哈</p>
           <p>我是内容，哈哈哈</p>
         </div>
   `
     });
     //2.注册组件
     Vue.component('my-cpn', create);
     let app = new Vue({
       el: '#app',
       data: {}
     })
   </script>
   ```

## 注册主键步骤解析

1. Vue.extend()：
   * Vue.extend()创建的是一个组件构造器
   * 通常在传概念组件构造器时，传入template代表我们自定义组件的模板
   * 该模板就是在使用到组件的地方，要显示的HTML代码
2. Vue.component()：
   * 调用Vue.component()是将刚才的组件构造器注册为一个组件，并且给它起一个组件的标签名称。
   * 所以需要传递两个参数：1.注册组件的标签名 2.组件构造器
3. 组件必须挂载在某个Vue实例下，否则它不会生效

## 全局组件和局部组件

> 当我们通过调用Vue.component()注册组件时，组件的注册时全局的
>
> 这意味着该组件可以在任意Vue示例下使用

* 注册局部组件是挂载在某个实例下

  ```html
  <div id="app">
    <cpm></cpm>
  </div>
  <script src="../../js/vue.js"></script>
  <script>
    const create = Vue.extend({
      template: `
        <div>
          <h2>我是标题</h2>
          <p>我是内容</p>
        </div>
      `
    });
    //全局组件注册
    // Vue.component('cpm',create);
    
    let app = new Vue({
      el: '#app',
      data: {},
      //局部组件注册
      components: {
        cpm: create
      }
    });
  </script>
  ```

## 父组件和子组件

```html
<div id="app">
  <cpn2></cpn2>
</div>
<script src="../../js/vue.js"></script>
<script>
  //1.创建第一个组件(子组件)
  const create1 = Vue.extend({
    template: `
      <div>
        <h2>我是标题1</h2>
        <p>我是内容</p>
      </div>
    `
  });
  //2.创建第二个组件(父组件)
  const create2 = Vue.extend({
    template: `
      <div>
        <h2>我是标题2</h2>
        <p>我是内容</p>
        <hr>
        <cpn1></cpn1>
      </div>
    `,
    //注册子组件
    components: {
      cpn1: create1
    }
  });
  
  let app = new Vue({
    el: '#app',
    data: {},
    components: {
      cpn2: create2
    }
  })
</script>
```

## 模板的分离写法

* Vue提供两种方案来定义HTML模块内容
  * 使用<script>标签
  * 使用<template>标签

```html
<div id="app">
  <cpn></cpn>
  <hr>
  <cpn2></cpn2>
</div>

<!--script标签，注意：类型必须是text/x-template-->
<script type="text/x-template" id="cpn1">
  <div>
    <h2>这是模板分离--script标签</h2>
    <p>这是内容</p>
  </div>
</script>

<!--template标签-->
<template id="cpn2">
  <div>
  <h2>这是模板分离--template标签</h2>
  <p>这是内容</p>
  </div>
</template>

<script src="../../js/vue.js"></script>
<script>
  Vue.component('cpn', {
    //script标签
    template: `#cpn1`
  });
  
  let app = new Vue({
    el: '#app',
    data: {},
    components:{
      cpn2:{
        //template标签
        template: `#cpn2`
      }
    }
  })
</script>
```

## 组件数据的存放

* 组件对象也有一个data属性(也可以有methods等属性)

* 只是这个data属性必须是一个函数

* 而且这个函数返回一个对象，对象内部保存着数据

  ```html
  <div id="app">
    <div>这是实例消息：{{message}}</div>
    <hr>
    <cpn></cpn>
  </div>
  <template id="myCpn">
    <div>这是组件消息：{{message}}</div>
  </template>
  <script src="../../js/vue.js"></script>
  <script>
    let app = new Vue({
      el: '#app',
      data: {
        message: 'Hello,World'
      },
      components: {
        cpn: {
          template: '#myCpn',
          data() {
            return {
              message: '这是组件内数据'
            }
          },
          methods:{}
        }
      }
    })
  </script>
  ```

## 父子组件通信

> 在开发中，往往一些数据确实需要从上层传递到下层
>
> * 比如在一个页面中，我们从服务器请求到了很多数据。
> * 其中一部分数据，并非是我们整个页面的大组件来展示的，而时需要下面的子组件进行展示
> * 这个时候，并不会让子组件再次发送一个网络请求，而是让**大组件(父组件)**将数据传递给**小组件(子组件)**
>
> 1. 通过props向子组件传递数据
>
> 2. 通过事件向父组件发送消息
>
>    ![](D:\Notes\img\147.jpg)

### 父级向子级传递

* 在组件中，使用选项**props**来声明需要从父级接收到的数据。
* props的值有两种方式:
  * 方式一：字符串数组，数组中的字符串就是传递时的名称。
  * 方式二：对象，对象可以设置传递时的类型，也可以设置默认值等

* props基本用法

  ```html
  <div id="app">
    <!--绑定对应关系-->
    <cpn v-bind:cmovies="movies" v-bind:cmessage="message"></cpn>
  </div>
  <template id="temp">
    <div>
      <ul>
        <li v-for="item in cmovies">{{item}}</li>
      </ul>
      <p>{{cmessage}}</p>
    </div>
  </template>
  <script src="../../js/vue.js"></script>
  <script>
    let app = new Vue({
      el: '#app',
      data: {//父组件数据
        message: 'Hello,World',
        movies: ['四驱兄弟', '黑猫警长', '葫芦娃']
      },
      components: {
        cpn: {
          template: '#temp',
          //子组件数据
          props: ['cmovies', 'cmessage'],
          },
        }
      }
    })
  </script>
  ```

### 子级向父级传递

* 在子组件传递数据或事件到父组件中，需要使用**自定义事件**来完成

* 自定义事件的流程：

  * 在子组件中，通过**$emit()**来触发事件
  * 在父组件中，通过v-on来监听子组件事件

  ```html
  <!--父组件模板-->
  <div id="app">
    <cpn @item-click="cpnClick"></cpn>
  </div>
  <!--子组件模板-->
  <template id="cpn">
    <div>
      <button v-for="item in categories" @click="btnClick(item)">
        {{item.name}}
      </button>
    </div>
  </template>
  <script src="../../js/vue.js"></script>
  <script>
    let app = new Vue({
      el: '#app',
      data: {},
      components: {
        cpn: {
          template: '#cpn',
          data() {
            return {
              categories: [
                {id: 'aaa', name: '热门推荐'},
                {id: 'bbb', name: '手机数码'},
                {id: 'ccc', name: '家用家电'},
                {id: 'ddd', name: '电脑办公'},
              ]
            }
          },
          methods: {
            btnClick(item) {
              //发射事件
              this.$emit('item-click',item);
            }
          }
        }
      },
      methods:{
        cpnClick(item){
          console.log("cpnClick",item)
        }
      }
    })
  </script>
  ```

## 父子组件的访问

> 有时候我们需要父组件直接访问子组件，子组件直接访问父组件，或者子组件访问根组件。
>
> * 父组件访问子组件：使用**$children**或者**$refs**
> * 子组件访问父组件：使用**$parent**
>
> ---
>
> * this.$children是一个数组类型，它包含所有子组件对象
>
> * $childer的缺陷
>   * 通过$children放问子组件时，是一个数组类型，访问其中的子组件必须通过索引值
>   * 但是当子组件过多，我们需要拿到其中一个时，往往不能确定它的索引值，甚至还可能发生变化
>   * 所以想明确获取其中一个特定的组件，这个时候就可以用**$refs**
> * $refs的使用
>   * $refs和red指令通常是一起使用的
>   * 首先，通过ref给其中一个子组件绑定一个特定的id
>   * 其次，通过this.$refs.id就可以访问到该组件了
>
> 

### 父访问子

```html
<div id="app">
  <cpn></cpn>
  <cpn></cpn>
  <cpn ref="aaa"></cpn>
  <button @click="btnClick">按钮</button>
</div>
<template id="temp">
  <div>我是子组件</div>
</template>
<script src="../../js/vue.js"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {},
    methods: {
      btnClick(){
/*        console.log(this.$children);
        this.$children[0].showMessage()*/
        console.log(this.$refs.aaa.name);
      }
    },
    components: {
      cpn: {
        template: '#temp',
        methods: {
          showMessage() {
            console.log('showMessage...')
          }
        }
      }
    }
  })
</script>
```

### 子访问父

```html
<div id="app">
  <cpn></cpn>
</div>
<template id="temp">
  <div>
    <h1>我是子组件</h1>
    <ccpn></ccpn>
  </div>
</template>
<template id="ctemp">
  <div>
    <h2>我是子子组件</h2>
    <button @click="btnClick">按钮</button>
  </div>
</template>
<script src="../../js/vue.js"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {
      message: '这是根组件的消息'
    },
    methods: {},
    components: {
      cpn: {
        template: '#temp',
        data() {
          return {
            name: '我是子组件的属性'
          }
        },
        components: {
          ccpn: {
            template: '#ctemp',
            methods: {
              btnClick() {
                console.log(this.$parent);
                //访问父组件
                console.log(this.$parent.name);
                //访问根组件
                console.log(this.$root.message);
              }
            }
          }
        }
      }
    }
  })
</script>
```

## 插槽slot

### 基本使用

> * 在子组件中，使用特殊的元素**<slot>**就可以为子组件开启一个插槽
> * 该插槽插入什么内容取决于父组件如何使用

```html
<div id="app">
  <cpn>
    <button>插槽</button>
  </cpn>
  <hr>
  <cpn><span>这是插槽</span></cpn>
  <hr>
  <cpn></cpn>
</div>
<template id="temp">
  <div>
    <h2>我是组件</h2>
    <p>这是组件内容</p>
    <slot>
      <button>默认</button>
    </slot>
  </div>
</template>
<script src="../../js/vue.js"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {},
    components: {
      cpn: {
        template: '#temp'
      }
    }
  })
</script>
```

### 具名插槽

* 给solt元素加一个name属性
* **<slot name='myslot'></slot>**

```html
<div id="app">
  <cpn>
    <span>替换默认</span>
    <span slot="center">替换中间</span>
  </cpn>
</div>
<template id="temp">
  <div>
    <slot name="left"><span>左边</span></slot>
    <slot name="center"><span>中间</span></slot>
    <slot name="right"><span>右边</span></slot>
    <slot><span>默认</span></slot>
  </div>
</template>
<script src="../../js/vue.js"></script>
<script>
  let app = new Vue({
    el: '#app',
    data: {},
    components: {
      cpn: {
        template: '#temp'
      }
    }
  })
</script>
```

# Webpack

> webpack可以帮助开发者进行模块化，并且处理模块间的各种复杂关系，将各种资源模块进行打包合并成一个或多个包(Bundle)，并且在打包过程中，还可以对资源进行处理，比如：压缩图片，将scss转成css，将ES6语法转成ES5语法，将TypeScript转成JavaScript等等操作

## 安装webpack

* 全局安装webpack

```shell
npm install webpack -g
```

* 局部安装webpack

```shell
cd 对应目录
npm install webpack --save-dev
```

## 文件解析

* dist文件夹：用于存放之后打包的文件
* src文件：用于存放开发者的源文件
* index.htm：浏览器打开展示的首页
* package.json：通过npm init生成的，npm包管理文件

## js文件的打包

```shell
webpack src/main.js dist/bundle.js
```

## 入口和出口

* 创建一个**webpack.config.js**文件

  ```javascript
  const path=require('path')
  
  module.exports={
      entry: '.src/main.js',
      output:{
          path: path.resolve(__dirname,'dist'),
          filename: 'bundle.js'
      }
  }
  ```

## package.json中定义启动

```javascript
"scripts":{
    "build": "webpack"
}
```

## loader

> 在开发中我们不仅仅有基本的js代码处理，我们也需要加载css、图片，也包括一些高级的将ES6转成ES5代码，将TypeScript转成ES5代码，将scss、less转成css，将.jsx、.vue文件转成js文件等等。对于webpack本身的能力来说，对于这些转化是不支持的。**给webpack扩展对应的loader**

* loader使用过程
  1. 步骤一：通过npm安装需要使用的loader
  2. 步骤二：在webpack.config.js中的modules关键字下进行配置

### css文件处理

