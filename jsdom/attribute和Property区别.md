#property和attribute
两种有何区别？
1. property指的是 dom 本身内置的属性，比如tagName
2. 而attribute指的是 显示赋值给dom 元素的属性，
3. 如果attribute 和 property 一样的话， 可以同步赋值

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <div id="demo" tips="A"></div>
    <script type="text/javascript">
      var demo = document.getElementById('demo');;

      //id为dom对象本身内置 
      console.log(demo.getAttribute('id')); //demo
      console.log(demo.id); //demo  同步赋值带dome内置属性值

      //自定义一个 可见的tips属性 property访问不了
      console.log(document.getElementById('demo').getAttribute('tips')); //A
      console.log(document.getElementById('demo').tips); //undefined

      //tagName为对象本身内置
      console.log(document.getElementById('demo').getAttribute('tagName')); //null
      console.log(document.getElementById('demo').tagName); //null

    </script>
  </body>
</html>
```
[相关参考](https://segmentfault.com/a/1190000003727646)
