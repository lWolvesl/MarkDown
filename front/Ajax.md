# Ajax

- 无需刷新网页的情况下向服务端发送数据请求

- 异步的JS和XML

## 一、简介

### 1.1 XML

- 可扩展标记语言
- 被设计用来传输和储存数据
- XML无预定标签，全为自定义标签
- `name="Dasheng";age=18;gender="man"`

```xml
<student>
  <name>Dasheng</name>
  <age>18</age>
  <gender>man</gender>
</student>
```

### 1.2 JSON

```json
{"name":"Dasheng","age":18,"gender":"man"}
```

### 1.3 Ajax特点

#### 1.3.1 优点

- 无需刷新页面即可与服务器通信
- 允许根据用户事件来更新页面内容

#### 1.3.2 缺点

- 无浏览记录，不能回退
- 存在跨域问题
- SEO不友好（搜索引擎优化）（爬虫）

### 1.4 HTTP

- 「超文本传输协议」：详细规定了浏览器和万维网服务器之间互相通信的规则

#### 1.4.1 请求报文

- 重点是格式与参数

```
行		 post /参数 http协议版本（1.1）
头		 host：
			cookie：
			content-type：
			user-agent：
空行
体		 username=xxx
```



#### 1.4.2 响应保文

```
行		 http协议版本（1.1） 响应状态码404 ok
头		 content-type：
		 content-length：
空行
体		 <html>
```



#### 1.5 EXPRESS

- 轻量级的小型应用框架

- `npm install express --save`

- `npm i express`

- ```js
  server.js
  1.引入EXPRESS
  const express = require('express');
  2.创建应用对象
  const app = express();
  3.创建路由规则
  request是对请求报文的封装
  response是对响应报文的封装
  app.get('/',(request,response)=>{
    response.send('Hello EXPRESS');
  })
  4.监听端口启动服务
  app.listen(8000,()=>{
    console.log("服务已启动，8000");
  })
  ```

- `node server.js`

## 二、Ajax案例

### 2.1前端准备

![image-20211024172036774](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024172036774.png)

![image-20211024172043782](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024172043782.png)

- 在app.get中

- ```js
  设置响应头，允许跨域
  response.setHeader('Access-Control-Allow-Origin','*');
  设置响应体
  response.send('Hello AJAX');
  ```

<img src="https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024172820200.png" alt="image-20211024172820200"  />

<img src="https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024172914268.png" alt="image-20211024172914268" style="zoom: 55%;" />

- 在状态码为200+中可以设置对应id的文本

### 2.2 设置请求参数

`xhr.open('GET','http://127.0.0.1:8000/server?a=100&b=200');`

- 在Headers-Query String Parameters中可以看到具体参数

### 2.3 发送POST请求

`result.addEventListener("mouseover",function())`

- 鼠标移入框中向服务器获取数据
  - `xhr`对象的`open`中改为POST请求
- 在`server.js`文件中`app.add`响应体

![image-20211024173710579](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211024173710579.png)

- `xhr.send`可以发送数据
