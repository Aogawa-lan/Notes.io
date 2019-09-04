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

## 使用bat直接调用node文件/node直接发起浏览器

bat文件设置,同目录下

```ba
node xxx.js
```

直接发起浏览器

```javascript
const cp = require('child_process')

cp.exec('star http://localhost:3000/')
```

## Node中的session

安装

```shell
# npm install express-session --save
```

参数/方法

```javascript
secret : 一个String类型的字符串，作为服务端生成session的签名
resave : （是否允许）当客户端并行发送多个请求时，其中一个请求在另一个请求结束时对session进行覆盖保存
saveUninitialized : 初始化session时是否保存到存储
cookie : 设置返回前段key的属性，默认值{ path:'/',httpOnly : true,secure : false,maxAge: null }

session.destroy() : 删除session，当检测到客户端关闭时调用
session.reload() : 当session有修改时，刷新session
session.regenerate() : 将已有session初始化
session.save() : 保存session
```

配置

```javascript
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true,
  cookie: { secure: false }
}))

var user = {
  name: 'lan',
  age: '22'
}
req.session.user = user

var name = req.session.user.name 
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
  //原型是
  module.exports = 'bbb'
	//此时exports为单个对象，多个可使用对象构造
	module.exports = {
  	add: function(){},
  	...
	}
  //底层最后是 return module.exports
  //因为node里封装了var exports = module.exports
```

### 引第三方包时加载规则

既不是核心模块、也不是路径形式时，会先找到当前文件所处目录的 `node_modules` 目录寻找引用的第三方名称

```javascript
const template = require('art-template')
```

然后寻找在 node_modules/art-template/package.json 中的 `main` 属性，这里记录了入口模块

```json
{
  "main" : "index.js"
}
```

如果本目录下没有 `node_modules` 则会进入上一级目录中寻找，直到根目录还找不到便报错 `can not find module`  

## 使用模版引擎（art-template）

### 下载使用
```javascript
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

## npm与package.json

### npm

* npm网站

  npmjs.com

* npm命令行

  常用命令

  ```bash
  # npm init //初始化项目
  	# npm init -y //可以跳过向导，快速生成
  # npm install //安装包
  # npm uninstall //卸载包
  # npm help
  ```

### package.json

package.json可以用来描述说明依赖包，使用npm可直接写入

```bash
npm install XXX --save
```

```json
{
  "dependencies": {
    "XXX": "^4.12.2"
  }
}
```

建议每一个项目都要有一个 `package.json` 文件，可通过 `npm init` 的方式来自动初始化出来

```bash
# npm init
	name: (npm-demo) //项目名称
	version: (1.0.0) //版本号
	description: 这是一个测试项目  //描述
	entry point: (index.js) //项目入口文件
	test command: //测试命令
	git repository: //github地址
	keywords: //关键字
	author: //作者
	license: (ISC) //开源许可证
```

当不小心将 `node_modules` 删除时，可以接使用 `npm install` 安装回来

### package-lock.json

npm5以后加入此文件。当你安装包的时候都会生成更新这个文件，保存所有包的依赖（版本，下载地址）

lock比之package在于可以锁版本，防止自动升级最新。

## 修改代码自动重启服务器

#### 使用第三方模块 `nodemon` 

```bash
# npm install -g nodemon
```

#### 安装完毕后，使用：

```bash
# nodemon app.js
```



## Express

原生http在某些方面不足以应对我们的开发需求，需要借助框架加快开发

### 下载安装express

`````bash
# npm init -y
# npm install express --save
`````

### 创建连接服务器

```javascript
const express = require('express')

//express()为原来的http.createServer
let app = express()

//当服务器收到 get 请求 / 时，执行回调函数
app.get('/',(req,res) => {
    res.send('Hello Node')
})

app.post('/',(req,res) => {
  res.send('Get a Post request')
})

app.listen(666,() => {
    console.log('app is running');
})
```

### 处理静态资源，公开指定目录

```javascript
app.use('/public/',express.static('./public'))
//XXXX:XXX/public/XXX.html

//当省略第一个参数的时候
app.use(express.static('./public'))
//XXXX:XXX/XXX.html

app.use('/a/',express.static('./public'))
//XXX:XXX/a/XXX.html  物料放在public
```

## 在express中使用art-template

#### 下载安装

```bash
# npm install art-template --save
# npm install express-art-template --save
```

#### 配置

```javascript
app.engine('art',require('express-art-template'))
```

第一个参数表示当渲染以 `.art` 为后缀的文件时，使用 `art-template` 模版引擎 

express-art-template 是专门用来在express中整合art-template的

```jab
res.render('html模版名',{模版数据})
```

 `Express`为`Response`相应对象提供了一个方法：`render`， `render` 方法默认时不可用的，配置后模版引擎即可

第一个参数不可卸路径，默认去项目中的 `views` 目录查找该模版文件，即把所有视图文件放到 `views` 目录中

```javascript
app.get('/',(req,res) => {
    res.render('404.art')
})
app.get('/admin', (req, res) => {
    res.render('admin/index.html',{
        title: '管理界面'
    })
})
```

若向修改 `views` 目录的路径，则可以

```javascript
app.set('views',render函数的默认路径)
```

### express中重定向

```javascript
res.redirect('/')
```

## Express表单重写

### 原表单GET请求

使用 res.query

```javascript
//app.js

const express = require('express')
const template = require('art-template')

//express()为原来的http.createServer
let app = express()

app.use('/public/' , express.static('./public/'))

app.engine('html',require('express-art-template'))

var comments = [
    {
        name : 'yy1',
        message : 'today'
    },
    {
        name: 'yy2',
        message: 'today'
    },
    {
        name: 'yy3',
        message: 'today'
    },
    {
        name: 'yy4',
        message: 'today'
    }
]

//当服务器收到 get 请求 / 时，执行回调函数
app.get('/',(req,res) => {
    res.render('index.html',{
        title : '主页',
        comments : comments
    })
})

app.get('/post', (req, res) => {
    res.render('post.html',{
        title: '留言'
    })
})

app.get('/pinglun',(req,res) => {
    var comment = req.query
    comments.unshift(comment)
    res.redirect('/')
})

app.listen(3000,() => {
    console.log('app is running');
})

//index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{ title }}</title>
</head>
<body>
    <h1>{{ title }}</h1>
    <button><a href="/post">留言</a></button>

    <table>
        <tr>
            <th>name</th>
            <th>message</th>
        </tr>
        {{each comments}}
        <tr>
            <td>{{$value.name}}</td>
            <td>{{$value.message}}</td>
        </tr>
        {{/each}}
    </table>
</body>
</html>
          
          
//post.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{title}}</title>
</head>
<body>
    <h1>{{title}}</h1>
    <hr>
    <form action="/pinglun" method="get">
        <input type="text" name="name">
        <input type="text" name="message">
        <button type="submit">提交</button>
    </form>
</body>
</html>
```



### 现修改使用app.post与form post

express没有内置获取表单POST请求的API，这里需要使用第三方包 `body-parser` 

安装

```shell
# npm install body-parser --save
```

配置

```javascript
const bodyParser = require('body-parser')
//只要加入，则在 req 请求对象上会多出来一个属性：body
//也就是说你就可以直接通过 req.body 来获取表单 POST 请求体数据
app.use(bodyParser.urlencoded({ extended: false }))

app.use(bodyParser.json())
```



```javascript
//app.js
...
	app.get('/post', (req, res) => {
    res.render('post.html',{
        title: '留言'
    })
})

//app.get('/pinglun',(req,res) => {
//   var comment = req.query
//    comments.unshift(comment)
//    res.redirect('/')
//})

//req.query因为是拿url后的字符串，所以只能使用在get

app.post('/post',(req,res) => {
    var comment = req.body
    comments.unshift(comment)
    res.redirect('/')
})
...
```

## 路由设计

### 页面逻辑

| 请求方法 |    请求路径    | get参数 |      post参数      |       备注       |
| :------: | :------------: | :-----: | :----------------: | :--------------: |
|   GET    |    /studens    |         |                    |     渲染主页     |
|   GET    | /students/new  |         |                    | 渲染添加学生页面 |
|   POST   |    /studens    |         | name、age、hobbies | 处理添加学生请求 |
|   GET    | /students/edit |   id    |                    |     渲染编辑     |

### 路由调用

将router封装成一个方法，app当参数传入

```javascript
//启动文件设置
//app.js
var router = require('./router')

var app = express()

router(app)

module.exports = app
```

```javascript
//路由文件设置
//router.js
module.exports = function(){
 	...路由
  app.get(...)
}


```

Express提供了一种更好的方式专门包装路由

```javascript
//启动文件设置
//app.js
var express = require('express')
var router = require('./router')

var app = express()

app.use(router)
```

```javascript
//路由文件设置
//router.js
var express = require('express')
//创建路由
var router = express.Router()
//挂载路由
router.get(...)

module.exports = router
```

## 回调函数获取异步数据

```javascript
//callback中的参数
	//第一个参数是err
		//成功是null
		//错误是错误对象
	//第二个参数是结果
		//成功是数组
		//错误是 undefined
exports.find = function(callback){
  fs.readFile(dbPath,function(err,data){
    if(err){
      return callback(err)
    }
    else {
      callback(null,JSON.parse(data).students)
    }
  })
}

---------
 //在此处定义callback的内容，既find()里的函数
Student.find(function(err,students){
  if (err){
    return res.status(500).send('Sever error.')
  }
  else{
    res.render(...)
  }
})
```

## Express + Multer + Ajax表单文件上传

### 使用FormData对象

通过把表单的编码类型设置为`multipart/form-data` ,则通过FormData传输的数据格式和表单通过submit()方法传输的数据格式相同

### 纯文本提交

```html
//html

<form class="form" method="post" enctype="multipart/form-data">
  <input type="text" class="form-control inputdata" name="name" value="">
  
  <a href="javascript:;" id="upload">提交</a>
</form>
```

```javascript
//js ajax异步提交

$('.form').on('click','#upload',function(){
 var formData = $(this).serialize() //
 
 $.ajax({
   url: '/upload',
   type: 'post',
   data: 'formData',
   dataType: 'json',
   success: function(data) {
    let err_code = data.err_code

    if(err_code === 0){
        $.hulla.send('上传成功','success')
    }else if(err_code === 1){
        $.hulla.send('订单号已存在', 'danger')
    }else if(err_code === 500){
        $.hulla.send('服务器繁忙，请稍后再试！', 'danger')
    }
}
 })
})
```

```javascript
//node 

router.post('/upload', function (res, req) {
    var comm = req.body

    User.finndOne({
        username: comm.name
    }, function (err, data) {
        if (err) {
            return res.status(500).json({
                err_code: 500,
                message: 'Sever Error'
            })
        }else{
            if(data){
                //ajax返回
                return res.status(200).json({
                    err_code: 1,
                    message: '订单号已存在'
                })
            }else{
                let nesUser = new User({
                    username: comm.name
                })
                newUser.save(function(err,ret){
                    if(err){
                        return res.status(500).json({
                            err_code: 500,
                            message: 'Sever Error'
                        })
                    }else{
                        return res.status(200).json({
                            err_code: 0,
                            message: 'OK'
                        })
                    }
                })
            }
        }
    })
})
```

通过ajax提交，服务器返回状态码与设置的err_code，再于前端判定动作



### 文本数据+多图上传

```html
//html

<form class="form" method="post" enctype="multipart/form-data">
  <input type="text" class="form-control inputdata" name="name" value="">
    
    <input type="file" class="file" id="file1" name="fileupload" accept="image/jpeg,image/gif,image/png"/>
  
  <a href="javascript:;" id="upload">提交</a>
</form>
```

```javascript
//js ajax异步提交

$('.form').on('click','#upload',function(){
    
                //ajax异步提交
                e.preventDefault()
                var formData = new FormData()

                let inputdata = document.querySelectorAll('.inputdata')
                for (let item of inputdata) {
                    //支持循环中止
                    formData.append(item.name, item.value)
                }

                // 获取文件
                let picnum = document.querySelectorAll('.file').length

                // console.log("图片input数："+picnum)

                for (var i = 1; i < picnum+1 ;i++){
                    let pic = document.getElementById(`file${i}`)
                    // console.log(pic)//上传图片input控件
                    // console.log(pic.files)//上传图片inupt控件的文件
                    if (pic.files.length == 0){
                        // console.log('空input')
                    }else{
                        var upload_file = pic.files[0]
                        formData.append('fileupload', upload_file)
                    }

                }

                // console.log(formData)
                $.ajax({
                    url: '/postinfo',
                    type: 'post',
                    data: formData,
                    // dataType: 'json',
                    contentType: false,
                    //取消帮我们格式化数据，是什么就是什么
                    processData: false,
                    success: function (data) {
                        let err_code = data.err_code
                        if (err_code === 0) {
                            $.hulla.send('上传成功', 'success')
                            $('.modal').fadeOut()
                        } else if (err_code === 1){
                            $.hulla.send('订单号已存在', 'danger')
                            $('input[name = "paynum1"]').focus()
                            $('.modal').fadeOut()
                        } else if (err_code === 2) {
                            $.hulla.send('上传截图为空', 'danger')
                            $('.modal').fadeOut()
                        }
                         else if(err_code === 500){
                            $.hulla.send('服务器繁忙，请稍后重试！', 'warning')
                            $('.modal').fadeOut()
                        }
                    }
                })
})
```

```javascript
//node 

router.post('/upload', function (res, req) {
    var comm = req.body

    User.finndOne({
            username: comm.name
        }, function (err, data) {
            if (err) {
                return res.status(500).json({
                    err_code: 500,
                    message: 'Sever Error'
                })
            } else {
                if (data) {
                    //ajax返回
                    return res.status(200).json({
                        err_code: 1,
                        message: '订单号已存在'
                    })
                } else {


                    //图片上传
                    let filename = {}
                    if (req.files) {
                        let file = req.files
                        // console.log(file) //图片文件array
                        if (file.length == 0){
                            return res.status(200).json({
                                err_code: 2,
                                message: '上传截图为空'
                            })
                        }else{
                            for (i = 0; i < file.length; i++) {
                                let fileInfo = {}
                                fs.renameSync('./upload/' + file[i].filename, `./upload/${Date.now()}_${file[i].originalname}`)//这里修改文件名字，比较随意。
                                // 获取文件信息
                                filename[i] = `${Date.now()}_${file[i].originalname}`
                                fileInfo.mimetype = file[i].mimetype
                                fileInfo.originalname = file[i].originalname
                                fileInfo.size = file[i].size
                                fileInfo.path = file[i].path
                            }

                            //上传成功
                            console.log("截图上传成功！")
                        }
                    } else {
                        return res.status(200).json({
                            err_code: 2,
                            message: '上传截图为空'
                        })
                    }

                    let nesUser = new User({
                    username: comm.name
                })
                  newUser.save(function (err, ret) {
                        if (err) {
                            return res.status(500).json({
                                err_code: 500,
                                message: 'Sever Error'
                            })
                        } else {
                            return res.status(200).json({
                                err_code: 0,
                                message: 'ok'
                            })
                        }
                    })

                }
            }
        })
})
```

