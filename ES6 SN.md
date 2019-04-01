关于ES6的学习笔记
===
一·  var let const
---
  1. var为函数作用域，在函数中声明为局部变量，外为全局变量。
  2. let和const为块级作用域，以一个{}为作用域，在同一个作用域下无法重复声明。  
      let与const声明整型字符串等时，let可以重新赋值 const则不行，声明对象时let可重赋值可重声明，const则不可重声明，可重赋值
  ```javascript
  const person{
    name : 'lan',
    age : 23
  }
  
  person.age = 24
  
  24
  ```
