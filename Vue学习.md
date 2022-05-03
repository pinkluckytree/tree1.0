## vue列表 v-for循环
```vue
<!-- 第一种方式 只穿一个数 -->
<!--style="list-style-type: none"这个可以去掉列表前的黑点-->
<li style="list-style-type: none" v-for="item in items" :key="item.message" >
        ----{{ item.message }}
</li>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})

<!-- 第二种方式,可以传多个数 -->
<li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
 </li>
 
 var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})

<!-- 第三种方式,可以通过v-for获取一个对象的属性值 -->
<li v-for="value in object">
    {{ value }}
 </li>
 
 new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})

<!-- 第四种方式,同时获取属性名和属性值-->
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```
