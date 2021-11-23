# VUE

## 第一章 VUE基础

### Vue简介

- 创建第一个Vue页面

```html
<!--引入Vue-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

```html
<body>
<div id="app">
    {{message}}
    <h2>{{school.name}}</h2>
    <h2>{{school.mobile}}</h2>
    <ul>
        <li>
            {{campus[0]}}
        </li>
        <li>
            {{campus[1]}}
        </li>
    </ul>
</div>
<script>
    var app = new Vue({
        el:"#app",
        data:{
            message:"hello Vue!",
            school:{
                name:"itHeima",
                mobile:"1234567"
            },
            campus:["beijing","shanghai"]
        }
    })
</script>
</body>
```

- el:挂载点
  - #：id选择器
  - data：具体数据



## 第二章 Vue本地应用

- 内容绑定，事件绑定
- 显示切换，属性绑定
- 列表循环，表单元素绑定

### v-text

```html
<div id="app">
  <h2 v-text="message"></h2>
</div>
```

### v-html

```html
<div id="app">
  <p v-html="content"></p>
</div>
```

### v-on

- 为用户绑定事件

```html
<div id="app">
  <input type="button" value="do" v-on:click="doSomeThing"/>
  <input type="button" value="do" v-on:monseenter="doSomeThing"/>
  <input type="button" value="do" v-on:dblclick="doSomeThing"/>
</div>
```

```js
var app = new Vue({
	el:'#app',
	methods:{
		doSomeThing:function(){
			//事件
		}
	}
})
```

```html
<!--动态元素-->
<h2 @click="change">
  {{food}}
</h2>
```

### v-show

```html
<div id="app-9">
    <img src="" v-show="isShow">
    <img src="" v-show="age>18">
</div>
```

```js
var app9 = new Vue({
        el:'#app-9',
        data:{
            isShow:true,
            age:16
        }
    });
```

### v-if

```html
<p v-if="true/false">
  标签
</p>
```

### v-bind

- 此方法的xxx是Vue对象中data的某个值，可以动态变更
- active为css的id

```html
<img v-bind:src="xxx" v-bind:title="xxx" alt="">
<img :src="xxx" :title="xxx" :class="active?'css':''" alt="">
<img :src="xxx" :title="xxx" :class="{active:isActive}" alt="">
```

### 切换案例

```js
var app10 = new Vue({
        el:'#app-10',
        data:{
            index:0,
            photo: ['backiee-1.jpg','backiee-2.jpg','backiee-3.jpg','backiee-4.jpg']
        },
        methods:{
            next:function (){
                if (this.index === this.photo.length-1){
                    alert("这是最后一张了");
                }else {
                    this.index++;
                }
                console.log(this.index);
            },
            prev:function (){
                if (this.index===0){
                    alert("这是第一张");
                }else {
                    this.index--;
                }
                console.log(this.index);
            }

        }
    });
```

```html
<div id="app-10">
    <img :src="photo[index]">
    <a href="javascript:void(0)" @click="next">下一张</a>
    <a href="javascript:void(0)" @click="prev">上一张</a>
</div>
```

### v-for

```html
<li v-for="todo in todo">
    {{todo.text}}
</li>
```

```js
var app4 = new Vue({
    el: "#app-4",
    data: {
        todo: [
            {text: 'study js'},
            {text: 'study Vue'},
            {text: 'study everything i like'}
        ]
    }
});
```

![截屏2021-10-23 下午11.10.22](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-23%20%E4%B8%8B%E5%8D%8811.10.22.png)

- 集合类型直接使用Collection.key

### v-on补充

- 自定义参数

```html
<input type="button" @click="do(props)">
```

- 事件修饰符
  - @keyup.enter="" 可以设置为回车即点击

```html
<input type="text" @keyup="do">
```

### v-model

- 获取和设置表单元素的值（双向数据绑定）
  - ``<input type="text" v-model="message"/>``
  - ``data:{message:"xxx"}`
  - 此时message将双向绑定

### 例子：小黑记事本

```html
<section id="todoapp">
    <header>
        <h1>小黑记事本</h1>
        <input v-model="input" @keyup.enter="add" autofocus="autofocus" autocomplete="off" placeholder="请输入任务">
    </header>
    <ul>
        <li v-for="(item,index) in list">
            <span>{{index + 1}}</span>
            <label>{{item}}</label>
            <button @click="remove(index)">X</button>
        </li>
    </ul>
    <span v-show="list.length!=0">{{list.length}}</span>
    <button v-show="list.length!=0" @click="clear">clear</button>
</section>
```

```js
var app = new Vue({
    el: '#todoapp',
    data: {
        list: ["写代码", "吃饭", "睡觉"],
        input: '',
        check: true
    },
    methods: {
        add: function () {
            this.list.push(this.input);
            this.input = ''
        },
        remove: function (index) {
            this.list.splice(index, 1);
        },
        clear: function () {
            this.list = [];
        }
    }
})
```

## Vue网络应用

### axios

- 网络请求库

`<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`

`axios.get(地址?参数).then(function(response){},function(err){})`

`axios.post(地址,{参数}).then(function(response){},function(err){})`

```js
document.getElementById('get').onclick = function () {
    axios.get("https://autumnfish.cn/api/joke/list?num=3")
        .then(function (response) {
            console.log(response);
        }), function (err) {
        console.log(err);
    }
}
document.getElementById('post').onclick = function () {
    axios.post("https://autumnfish.cn/api/user/reg", {username: "28034u258932"})
        .then(function (response) {
            console.log(response)
        }), function (err) {
        console.log(err);
    }
}
```

### axios+Vue

```html
<div id="app">
    <input type="button" value="get joke" @click="getJoke">
    <p>{{ message }}</p>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:''
    },
    methods:{
        getJoke:function (){
            var that = this;
            axios.get("https://autumnfish.cn/api/joke").then
            (function(response){
                that.message = response.data;
                console.log(response.data);
            },function (err){
                console.log(err);
            })
        }
    }
});
```

- 由于回调函数，此处的`this`状态会改变，可以通过var绑定到this再赋值

### 例子：天知道

- 查询天气



![截屏2021-10-24 下午6.46.27](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-24%20%E4%B8%8B%E5%8D%886.46.27.png)

```html
<!DOCTYPE html>
<html lang="zn">
<head>
    <meta charset="UTF-8">
    <title>天知道</title>
</head>
<body>
<div id="app">
    <div>
        <input type="text" placeholder="city" @keyup.enter="search" v-model="city">
        <button @click="search">search</button>
    </div>
    <div>
        <a href="javascript:;" @click="aclick('北京')">北京</a>
        <a href="javascript:;" @click="aclick('上海')">上海</a>
        <a href="javascript:;" @click="aclick('广州')">广州</a>
        <a href="javascript:;" @click="aclick('深圳')">深圳</a>
    </div>
    <ul>
        <li v-for="item in datas">
            {{item.date}} {{item.type}}
        </li>
    </ul>
</div>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="helloVue3.js"></script>
</body>
</html>
```

```js
var app = new Vue({
    el: '#app',
    data: {
        city: '',
        datas: []
    },
    methods: {
        search: function () {
            var that = this;
            console.log('search:' + this.city);
            axios.get('http://wthrcdn.etouch.cn/weather_mini?city=' + this.city)
                .then(function (response) {
                    //console.log(response);
                    console.log(response.data.data);
                    that.datas = response.data.data.forecast;
                }, function (err) {
                    {
                        console.log(err);
                    }
                })
        },
        aclick: function (city) {
            this.city = city;
            this.search();
        }
    }
})
```

## 综合应用

![image-20211024185423003](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024185423003.png)

![image-20211024185455194](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024185455194.png)

![image-20211024185628779](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024185628779.png)

![image-20211024185849566](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024185849566.png)

![image-20211024185950439](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024185950439.png)

![image-20211024190057338](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024190057338.png)

