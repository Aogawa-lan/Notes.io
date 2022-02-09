# 前端Promise学习
## 异步编程解决方案，封装函数解决异步任务
### 可有效解决回调地域问题
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

