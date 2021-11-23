# cn.vuejs.org/v2/guide

# 基础

## 一、介绍

### 1.x 组件化应用构建

- 组件系统是Vue的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。如下组件树：

![截屏2021-10-22 下午4.58.17](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-22%20%E4%B8%8B%E5%8D%884.58.17.png)

- 组件本质上是一个拥有预定义选项的一个Vue实例。

```html
<div id="app-7">
    <ol>
        <todo-item
                v-for="item in groceryList"
                v-bind:todo="item"
                v-bind:key="item.id"
        ></todo-item>
    </ol>
</div>

<script>
		Vue.component('todo-item', {
        props: ['todo'],
        template: '<li>{{todo.text}}</li>'
    });
    var app7 = new Vue({
        el: '#app-7',
        data: {
            groceryList: [
                {id: 0, text: "蔬菜"},
                {id: 1, text: "奶酪"},
                {id: 2, text: "anything"}
            ]
        }
    });
</script>
```

## 二、Vue实例

### 2.1 创建一个Vue实例

- new Vue通过创建根实例

```
根实例
└─ TodoList
   ├─ TodoItem
   │  ├─ TodoButtonDelete
   │  └─ TodoButtonEdit
   └─ TodoListFooter
      ├─ TodosButtonClear
      └─ TodoListStatistics
```

### 2.2 数据与方法

- 当一个Vue实例被创建时，它将data对象中的property加入到Vue到**相应式系统**中。当这些property的值发生改变时，视图将产生相应，即匹配更新后的值

- 只有当被实例被创建时就已经存在于data中的property才是有效的，因此要声明或者赋初值
- 若使用`Object.freeze(对象名)`则会阻止修改现有的property

### 2.3 实例生命周期钩子

- 每个Vue实例在被创建时都要经过一系列的初始化过程，在这个过程中会运行一些叫做**声明周期钩子**的函数
- 比如`created`钩子可以用来在一个实例被创建之后执行代码：

```html
new Vue({
    data: {
        a: 1
    },
    created: function () {
        console.log('a is:' + this.a)
    }
});
```

![截屏2021-10-22 下午5.30.11](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-22%20%E4%B8%8B%E5%8D%885.30.11.png)

- 还有一些其他钩子，可以作用在实例生命周期的不同阶段。
  - `mounted`
  - `updated`
  - `destoryed`

![截屏2021-10-22 下午5.31.35](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-22%20%E4%B8%8B%E5%8D%885.31.35.png)

- 生命周期图时

![lifecycle](https://cn.vuejs.org/images/lifecycle.png)

## 三、模版语法

- Vue.js使用了基于HTML的模版语法

### 3.1 文本

- 此文本的`msg`值会实时更新，若通过`v-once`可以实现只执行一次插值

  - ```html
    <span>message:{{msg}}</span>
    ```
  
  - ```html
    <span v-once>{{msg}}</span>
    ```

### 3.2 原始HTML值

- 双大括号将数据解释为纯文本，而非HTML代码，若要输出真正的HTML，则需要使用`v-html`

  - ```html
    <p>Using v-html directive:<span v-html="xxx"></span></p>
    ```

  - ```javascript
    var app_1=new Vue({
        el:'#app--1',
        data:{
            rawHtml:'<p>123</p>'
        }
    });
    ```

![截屏2021-10-22 下午6.01.22](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-22%20%E4%B8%8B%E5%8D%886.01.22.png)

### 3.3 Attribute

- Mustache 语法不能作用在 HTML attribute 上，遇到这种情况应该使用 [`v-bind` 指令](https://cn.vuejs.org/v2/api/#v-bind)：

  ```
  <div v-bind:id="dynamicId"></div>
  ```

- 对于布尔 attribute (它们只要存在就意味着值为 `true`)，`v-bind` 工作起来略有不同，在这个例子中：

```
<button v-bind:disabled="isButtonDisabled">Button</button>
```

- 如果 `isButtonDisabled` 的值是 `null`、`undefined` 或 `false`，则 `disabled` attribute 甚至不会被包含在渲染出来的 `<button>` 元素中。

### 3.4 使用js表达式

- 对于所有的js数据绑定，Vue都提供了完整的js表达式支持

- 如：

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

- 上述表达式可以生效，但是每个绑定都只能包含单个表达式

``` html
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```

![截屏2021-10-22 下午6.05.51](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-22%20%E4%B8%8B%E5%8D%886.05.51.png)

### 3.5 指令

- 带有v-前缀的特殊attribute。指令attribute的预期值时单个JavaScript表达式（除v-for）。
- 指责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于DOM。

```html
<p v-if="seen">
  你现在看到我了
</p>
```

- 此处v-if可以根据指令表达式seen的值的真假来插入/移除\<p>元素



### 3.6 参数

- 指令可以接受参数，在指令名之后以冒号表示

```html
<a v-bind:href="url">...</a>
```

```html
<a v-on:click="do">...</a>
```



### 3.7 动态参数

- 用方括号括起来的JavaScript表达式作为指令的一个指令的参数：

```html
<!--参数表达式的写法存在一定约束-->
<a v-bind:[attributeName]="url">...</a>
```

- 如果你的Vue实例中有一个`data`property`attributeName`其值为`"href"`，那么这个绑定相当于`v-bind:href`

```html
<a v-on:[eventName]="do">...</a>
```

- 当 `eventName` 的值为 `"focus"` 时，`v-on:[eventName]` 将等价于 `v-on:focus`

- 动态参数预期会求出一个字符串，异常情况下值为`null`这个特殊的`null`值可以被显性地用于移除绑定，任何其他非字符串类型的值都会触发一个警告
- `<a v-bind:['foo'+bar]="value">...\</a>`这会触发一个编译警告
- 在DOM使用模版时，要避免大写，因为浏览器会把attribute名全部强制转换为小写



### 3.7 修饰符

- 修饰符是以半角句号`.`指明的特殊后缀，用于指出一个指令应该以特殊方式绑定

```html
<form v-on:submit.prevent="onSubmit">
  ...
</form>
```

- 此`.prevent`修饰符告诉`v-on`指令对于触发事件的调用`event.preventDefault()`



### 3.8 缩写

- `v-`前缀作为一种视觉提示，用来识别模版中Vue特定的attribute
- 当用Vue为现有元素添加动态行为时,`v-`前缀很有帮助
- Vue为`v-bind`和`v-on`这两个最常用的指令，提供了特定简写

#### `v-bind`缩写

```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a :[key]="url"> ... </a>
```

#### `v-on`缩写

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

## 四、计算属性和监听器

### 4.1 计算属性

- 模版内的表达式非常便利，但是设计他们的初衷是用于简单运算的。
- 在模版中放入太多的逻辑会让模版过重且难以维护

```html
<div id="example">
  {{message.split('').reverse().join('')}}
</div>
```

- 在这个地方，模版不再是简单的声名式逻辑，而是略微复杂的表达式
- 对于任何复杂逻辑，都应当使用**计算属性**

