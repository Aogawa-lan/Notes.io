# Vue学习
## 涉及Vue2.0+Vue3.0

构建用户界面的渐进式js框架，组件化、声明式编码

### 创建实例

```html
<div class="root">
      <h1>Hello! {{name}}</h1>
 </div>
<script>
  new Vue({
      el:'.root',
      data:{
          name:'world'
      }
  })
</script>
```



### 模板语法

```html
<!-- 指令模板 -->
    <div class="root">
        <h1>Hello! {{name}}</h1><!-- {{}}为插值模板 -->
        <a v-bind:href="url">点我跳转1</a>
        <a :href="url">点我跳转2</a>
    </div>
    <script>
    new Vue({
        el:'.root',
        data:{
            name:'world',
            url:'http://www.baidu.com'
            }
        })
    </script>
```

插值模板与指令模板区别：插值模板主要作用于标签体内容，指令模板作用于标签属性，绑定事件等

指令模板中，v-bind将属性值与数据绑定在一起实现数据动态展示，同时v-bind可以简写为:，即 v-bind:href 简写为 :href
