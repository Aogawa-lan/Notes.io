# 关于Node JS的学习笔记
## win dos与Mac bash命令
### dos
计算机端口  netstat -ano
关闭进程  taskkill /f /im 进程名称
### Mac
计算机端口  lsof -i tcp:端口号
关闭进程  kill PID

## 创建web服务器
```javascript
  const http = require('http');//调用模块

  let cs = (req,res) =>{
    res.write('Hello Node!');
    res.end();//创建后关闭
  }

  http.createSever(cs).listen(666);//666为端口号

  console.log('http is ok');
```

