# 关于ES6的学习笔记
## var let const
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

## 箭头函数
### 删掉function关键字，加上一个胖箭头'=>',没有参数加括号，一个参数可无括，多个参数逗号分割。
```javascript
  const numbers = [5,6,13,0,1,18,23];
  
  const double = numbers.map(function(number){
      return number * 2;
  });
  
  //改为
  const double2 = numbers.map( number => {
      return number * 2;
  });
  
```

### 隐性返回：可不需要return即可返回参数。
```javascript
  const double2 = numbers.map( number => number * 2 );
```
箭头函数都为匿名函数，需要命名可赋值给一个变量
### this值的指向问题
```javascript
  const Jelly = {
    name : 'Jelly',
    hobbies : ['Coding','Sleeping','Reading'],
    printHobbies : function(){
      this.hobbies.map(function(hobby){ //因为this值是在运行时才绑定的，此处的this指向为正常指向Jelly对象
        console.log(`${this.name} loves ${hobby}`); //此处独立函数则指向window
      })
    }
  }
  
  一般把this赋值给一个参数先声明，然后后期调用；
  
  ......function(){
    var self = this;
    this.hobbies.map(function(hobby){ 
        console.log(`${self} loves ${hobby}`);
  }......
  
```
若用箭头函数则为
```javascript
......function(){
    var self = this;
    this.hobbies.map( hobby => { //下面this继承此处this
        console.log(`${this.name} loves ${hobby}`);
  }......
  
  //因为箭头函数没有自己的this值，this继承父作用域
```
