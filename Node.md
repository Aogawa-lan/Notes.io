# 关于Node JS的学习笔记
## Win dos与Mac bash命令
### dos
计算机端口  netstat -ano  
关闭进程  taskkill /f /im 进程名称
### bash
计算机端口  lsof -i tcp:端口号  
关闭进程  kill PID

## 创建web服务器

### 简单的服务器创建
```javascript
  const http = require('http');//调用模块

  let cs = (req,res) =>{
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
    Request 请求对象  
        请求对象可以用来获取客户端的一些请求，例如请求路径  
    Response 响应对象  
        响应对象可以用来给客户端发送响应消息

```javascript
  server.on('request',function(req,res){
    console.log('收到服务器请求');
    console.log('url:' + req.url);
  });
```

  response 对象有一个方法 write 可以用来给客户端发送响应数据



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