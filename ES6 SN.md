# 关于ES6的学习笔记
## var let const
### 1. var为函数作用域，在函数中声明为局部变量，外为全局变量。
### 2. let和const为块级作用域，以一个`{}`为作用域，在同一个作用域下无法重复声明。
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

## 箭头函数(Arrow Function)
### 删掉function关键字，加上一个胖箭头`=>`,没有参数加括号，一个参数可无括，多个参数逗号分割。
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

### 隐性返回：可不需要`return`即可返回参数。
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
## 字符串模版（Template Strings/Literals）
### 反引号，${}可放变量，参数，函数，算式，可嵌套
示例
```javascript
  const lan = {
    name : 'lan',
    date : '2019-04-04',
    todos : [
      { name:'fanouxiang' , completed : true},
      { name:'playfootball' , completed : false},
      { name:'fanouxiang' , completed : true},
    ]
  }

  const template = `
  <ul>
    ${lan.todos.map( todo =>
      `<li>
      ${todo.name} ${todo.completed ? '完成' : '未完成'}
      </li>`
    ).join('')} //join()函数去掉map生成新数组的逗号
  </ul>
  `
  //为了可读性和拓展性，也可将ul写到一个函数里再引用
  function renderTodos(todos){
    return(
       `<ul>
          ${todos.map( todo =>
            `<li>
                ${todo.name} ${todo.completed ? '完成' : '未完成'}
            </li>`
          ).join('')} 
  </ul>`
    )
  }

  const template = `
  ......
  ${renderTods(lan.todos)}
  ......
  `

```
### 标签模版字符串
在模版字符串前设置一个标签
```javascript
const sentence = highlignt`${user} has commented on your topic ${topic}`;
```
这里的hightlight字符串对应的是函数名,最后调用sentence时，返回内容由highlight函数决定
```javascript
funbction highlight(string,...values){ 
  //string是一个数组，由模版字符串里面的普通字符组成
  //...values也是一个数组，由模版字符串的参数组成，也可直接写参数名
  //若以参数开头或结尾，string开头会有1个空字符串
}
```
过滤用户实践（防XSS攻击）
```javascript
<script src="https://cdnjs.cloudflare.com/ajax/libs/dompurify/1.0.10/purify.min.js"></script>
//使用dodompurify.js过滤
  <div class="container">
        <form class="add-comment">
            <textarea class="comment-text" placeholder="enter">
            </textarea>
            <button>Post Comment</button>
        </form>
        <div class="comment"></div>
    </div>

        const addCommentForm = document.querySelector('.add-comment');
        const textarea = document.querySelector('.comment-text');
        const commentDiv = document.querySelector('.comment');
        const user = 'Mary';

        function sanitize = (strings, ...values){
          const dirty = strings.reduce((prev,curr,i) => `
            ${prev}${curr}${values[i] || ''}
          `,'');
          return DOMPurify.sanitize(dirty);
        }

        addCommentForm.addEventListener('submit',function(event){
            event.preventDefault();
            const newComment = textarea.value.trim();

            if(newComment){
                commentDiv.innerHTML = sanitize`
                    <div class="commrnt-header">${user}</div>
                    <div class="commrnt-body">${newComment}</div>
                `
                textarea.value = '';
            }
        })

```

## 新增字符串函数
### statrsWith('string',位置) / endsWith('string',位置)
判断是否以指定字符串开头，判断大小写，空格占一位
```javascript
  const id = '51030019800730366x';

  id.startsWith('51'); //true

  id.startsWith('1980',6);  //true
```
### includes('string',位置)
查询是否包含字符串
```javascript

  const fan = 'I Love JLS';

  //旧方法为判断indexOF是否为-1

  fan.indexOf('Love') !== -1; //true

  //使用includes()可返回布尔值

  fan.includes('Love'); //true

  //同样可以加入位置参数判断
```

### repeat()
重复字符串
```javascript
  '哈'.repeat(10); // '哈哈哈哈哈哈哈哈哈哈'
```