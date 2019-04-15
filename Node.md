# 关于Node JS的学习笔记
## Win dos与Mac bash命令
### dos
* 计算机端口  netstat -ano  
* 关闭进程 taskkill /f /im 进程名称
### bash
* 计算机端口 lsof -i tcp:端口号  
* 关闭进程 kill PID

## 创建web服务器

### 简单的服务器创建
```javascript
  const http = require('http');//调用模块

  let cs = (req,res) =>{
    res.setHeader('Content-type','text/plain;charset=utf-8');
    res.write('Hello Node!');
    res.end();//创建后关闭
  }

  http.createServer(cs).listen(666);//666为端口号


  console.log('http is ok');
```

### 服务器使用详解
```javascript
  const http = require('http');//调用模块

  const server = http.createServer();//创建web服务器，返回一个server实例

  server.on('request',function(){//监听request事件
    console.log('收到服务器请求');
  });

  server.listen(端口号,function(){//绑定端口号启动服务器
    console.log('服务器启动成功');
  });
```
####   request 请求事件处理函数
request 请求事件处理函数可接收两个参数：  
* Request 请求对象  
    请求对象可以用来获取客户端的一些请求，例如请求路径  
* Response  
    响应对象响应对象可以用来给客户端发送响应消息

```javascript
  server.on('request',function(req,res){
    console.log('收到服务器请求');
    console.log('url:' + req.url);
  });
```

response 对象有一个方法 write 可以用来给客户端发送响应数据  
可以使用多次，但是最后一定要使用end来结束响应，否者客户端会一直等待

```javascript
  server.on('request',function(req,res){
    res.write('Hello Node');
    res.end();
  });
```



## 读取/写入文件操作
### 读取文件
异步 readFile() 同步readFileSync()
```javascript
  const fs = require('fs');//文件操作使用fs模块

  fs.readFile('文件路径',(error,data) =>{
    console.log(data.toString());//data此时为二进制，需通过toString()方法转为字符串
  });

  fs.readFileSync('文件路径');

  // fn为回调函数 有2个参数【error,data】
  // 读取成功 data为数据 error为null 失败data为undefined error返回错误对象
```

### 写入文件
```javascript
  const fs = require('fs');

  fs.writeFile('文件路径','文件内容',fn(error){

  })
```

### error判断
```javascript
  if(error){
    console.log('error');
    return 
  }
```

## 模块系统
### require
require用来加载模块
* 具名核心模块。fs，http，os等
* 用户自己编写的文件模块 （先对路径必须加 ./）
* npm下载第三方模块  

Node中没有全局作用域只有模块作用域
```javascript
  //a.js
  var  f00 = 'aaa';

  require('./b.js');

  console.log(foo);

  //b.js
  var foo = 'bbb';

  console.log('b end');

  //此处输出的foo为aaa，并不会受b.js影响
```

若需要外部访问变量以及函数交流，则需要使用接口对象

require方法有2个作用
* 1.加载文件模块并执行里面的代码
* 2.拿到被加载文件模块导出的接口对象：exports对象

```javascript
  //上一段代码
  ...//a.js
  const b = require('./b.js');

  console.log(b.foo);
  //b.js
  exports.foo = 'bbb'
  ...
```

## 使用模版引擎（art-template）
### 下载使用
```npm
  npm install art-template

  const template = require('art-template');//引用

  template.render('模版对象',替换对象)//render渲染

  const ret = template.render('hello {{ name }}', {
    name : 'Lan'
  })
```
### 使用实例

```javascript
  //index.html

  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <p>大家好我叫{{ name }}</p>
    <p>我今年{{ age }}岁了</p>
    <p>我来自{{ province }}</p>
    <p>我的爱好是{{ each hobbies }} {{ $value }} {{/each}}</p>
</body>
</html>

```
```javascript
  //index.js

  const template = require('art-template');

const fs = require('fs');

fs.readFile('./index.html',function(err,data){
    if(err){
        return console.log('读取失败');
    }else{
        const ret = template.render(data.toString(), {
            name: 'Lan',
            age : 18,
            province: 'gzs',
            hobbies : ['ps4','ns','lol']
        })

        console.log(ret)
    }
})
```
执行index.js得到数据渲染后的html。因模版引擎只看{{}}标签标记的代码，其他都归为字符串。

## 服务端加载静态资源问题
服务器请求加载 `link` , `script` , `img` , `iframe` , `video` , `audio` 等具有 `src` , `href` 属性的时候会自动对这些资源发起新的请求

如：
```javascript
  <link href="node_modules/...../XXX.css">
  //服务器端请求时为 XXX.XXX.XX.XX:XXX/node...../.css  此时会因为加载问题一直挂起

```
解决：将静态资源整合到 `public` 中并开放

```javascript
  const http = require('http');
  const fs = require('fs');

  let cs = (req, res) => {
    res.setHeader('Content-type', 'text/plain;charset=utf-8');
    const url = req.url
    if(url === '/'){    //当主页html请求时，加载XXX.html
      fs.readFile('./XXX.html',(err,data) => {
        if(err) { return res.end('404 Not Found.') }
        else {return res.end(data)}
      })
    }
    else if(url.indexOf('/public/') === 0){     //当以/public/开头的资源请求时，重写为./public/请求
      fs.readFile('.' + url ,(err,data) => {
        if(err) { return res.end('404 Not Found.') }
        else {return res.end(data)}
      })
    }
  }

  http.createServer(cs).listen(666,()=>{
    console.log('http is ok');
  });
```

## 处理表单问题
当form表单向js发起提交事件后，跳转的url可使用url模块的 `url.parse()` 获取需要的数据

```javascript
  const url = require('url')

  const obj = url.parse('/pinglun?name=Lan&message=HelloNode')

  console.log(obj)

  // Url {
  // protocol: null,
  // slashes: null,
  // auth: null,
  // host: null,
  // port: null,
  // hostname: null,
  // hash: null,
  // search: '?name=Lan&message=HelloNode',
  // query: 'name=Lan&message=HelloNode',
  // pathname: '/pinglun',
  // path: '/pinglun?name=Lan&message=HelloNode',
  // href: '/pinglun?name=Lan&message=HelloNode' }
```

若想将query直接变为对象，可加参数 `true`

```javascript
  ...
  const obj = url.parse('/pinglun?name=Lan&message=HelloNode',true)

  console.log(obj)

  // Url {
  // protocol: null,
  // slashes: null,
  // auth: null,
  // host: null,
  // port: null,
  // hostname: null,
  // hash: null,
  // search: '?name=Lan&message=HelloNode',
  // query: { name: 'Lan', message: 'HelloNode' },
  // pathname: '/pinglun',
  // path: '/pinglun?name=Lan&message=HelloNode',
  // href: '/pinglun?name=Lan&message=HelloNode' }
```
此时即可通过 `obj.query` 来操作获取的表单数据了

## 如何通过服务器让客户端重定向
* 状态码设置为 `302` 临时重定向
* 在响应头中通过 `Location` 告诉客户端往哪重定向
```javascript
  res.statusCode = 302;
  res.setHeader('Location','/')
```