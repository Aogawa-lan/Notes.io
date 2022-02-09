# 前端axios学习
## axios是目前最热门的前端ajax请求库
### 基于Promise的HTTP客户端，可以用在浏览器和node.js中，向服务器发送AJAX请求进行数据交换
(1). let与const声明整型字符串等时，let可以重新赋值 const则不行，声明对象时let可重赋值可重声明，const则不可重声明，可重赋值
```javascript
  const person{
    name : 'lan',
    age : 23
  }
  
  person.age = 24

  24
  
  //使用object.freeze()则可使const的对象属性无法重赋值
```