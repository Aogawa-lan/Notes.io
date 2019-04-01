# 关于ES6的学习笔记
## 一·  var let const
### 1. var为函数作用域，在函数中声明为局部变量，外为全局变量。
### 2. let和const为块级作用域，以一个{}为作用域，在同一个作用域下无法重复声明。
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
### 3.立即执行函数
(1). 变量私有化方便，加{}。  
(2). for循环时可解决变量在循环外的使用  
```javascript
      for(let i = 0; i < 10; i++){
            console.log(i);
            setTimeout(function(){
                  console.log(`i:${i}`);  //&{}为模版字符串``的占位连接符，可插入函数
            },1000)
      }
```
### 4.变量提升
javascript中，function和variables会被提升。
```javascript
      console.log(foo);
      var foo = 'hello';
      
      //undefined
 ```
let 和 const则会not definde
