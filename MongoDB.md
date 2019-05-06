# MongoDB

## NoSQL

No only sql,非关系型数据库，不需要写sql语句。

数据库 => 数据库；数据表 => 集合(数组) ； 表记录 => (文档对象)

## 安装

### windows

1. .msi安装完

2. 加入环境变量

   ```bash
   # PATH路径
   # C:\Program Files\MongoDB\Server\4.0\bin
   ```

3. 启动与关闭

   默认执行在C盘下的 `/data/db` 

   ```bash
   # mongod
   
   #开启后在开一个终端窗口
   # monhgo
   ```

   

### Mac

1. 解压压缩包，Shift + Command+G 进入 `/usr/local` 放入解压包并改名为mongodb

2. 进入终端，加入PATH路径

   ```bash
   # export PATH=/usr/local/mongodb/bin:$PATH
   ```

3. 运行

   ```bash
   # 创建
   # sudo mkdir -p /data/db
   
   # 启动
   # ./mongod
   ```

   

## 基本命令

`show dbs`

查看显示所有数据库

`db`

查看当前操作的数据库

`use 数据库名称`

切换到指定数据库（如果没有会新建）

`数据库名.数据表明.find()`

查看全部数据

## 基本概念

```javascript
//MongoDB是一个大对象
{
  //数据库，也是一个对象
  qq: {
    //集合=数据表，是数组
    users: [
      //每一条记录，也是对象
      //并不限制结构性
      {},
      {},
      {}，
      //...
    ],
    products: [
      
    ],
     //...
  },
    taobao: {
      
    }
}
```



## 在Node中操作MongoDB数据库

### 官方mongoDB包

https://github.com/mongodb/node-mongodb-native

### 使用第三方mongoose包来操作

http://mongoosejs.com

#### 安装

```shell
# npm install mongoose
```

#### 使用

```javascript
const mongoose = require('mongoose');

//连接数据库
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});

//设计数据库
//生成小写数据表名cat，里面的name为字符串
const Cat = mongoose.model('Cat', { name: String });

//示例画一个 cat
const kitty = new Cat({ name: 'Zildjian' });
//持久化保存 kitty 实例
kitty.save().then(() => console.log('meow'));
```

####设计集合（表）结构

```javascript
const mongoose = require('mongoose');
const Scheme = mongoose.Scheme

mongoose.connect('mongodb://localhost:27017/test');

//设计集合结构
//字段名称就是表结构中的属性名称
let usersSchema = new Scheme({
  username: {
    type: String,
    required: true //约束必须
  },
  password: {
    type: String,
    required: true
  },
  
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs: Number
  }
})

//将文档结构发布为模型
//   mongoose.model 方法用来架构发布为 model
//	 第一个参数：传入一个大写名词单数字符串用来表示你的集合名称 会自动将大写名词字符串生成 小写复数 集合名称
//	 第二个参数：架构 Schema
//	 返回值： 模型架构函数
let User = mongoose.model('User',userSchema)
```

#### 增加数据

```javascript
//接上

let admin = new User({
  username: 'admin',
  password: '111',
  email: 'admin@admin.com'
})

//持久化
admin.save(function(err,ret){
  //ret 为刚插入的文档数据
  if(err){
    console.log('保存失败')
  }else{
    console.log('保存成功')
  }
})
```

#### 查询数据

```javascript
//查询所有
User.find(function(){
  if(err){
    console.log('查询失败')
  }else{
    console.log(ret)
  }
})

//按条件查
User.find({
  username: 'zs'
  //，可加条件
  //或条件为 { $or: [{username:'name'},{age:28}]}
},function(){
  if(err){
    console.log('查询失败')
  }else{
    console.log(ret)
  }
})

//findOne(),只取第一个
//方法时有更新，更多方法请查看文档
```

#### 更新与删除

```javascript
//更新
User.findByIdAndUpdate('id',{
  passage: '123'//修改数据
},function(err,ret){
  if(err){
    console.log('更新失败')
  }else{
    console.log(ret)
  }
})
//删除
//已开始使用deleteOne，deleteMany替换
User.remove({条件},function(err){
  if(err) return handleError(err)
})
```

