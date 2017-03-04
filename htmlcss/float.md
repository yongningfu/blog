## 浮动详解
概念: CSS的 float 属性可以使一个元素脱离正常的文档流，然后被安放到它所在容器的的左端或者右端，并且其他的文本和行内元素环绕它。--mdn css

浮动可能会造成的问题:
### 父元素高度无法展开
由于浮动是脱离的文档流, 对于父元素来说，自然就没有了高度.  

**解决方案:**  
基本的思路都是让父元素撑起来。
- 让父元素设置overflow: hidden
- 让父元素的after伪类设置clearboth
```html
<!DOCTYPE html>
<html>
  <head>
    <style type="text/css">
      .container {
        background: red;
      }
      .container:after {
        content: '';
        display: block; /*这个必须设置为block*/
        clear: both;

        /*隐藏content内容*/
        height: 0;
        visibility: hidden;
      }
      .float {
        float: left;
        width: 100px;
        height: 100px;
        background: yellow;
      }

    </style>
  </head>
  <body>
    <div class="container">
        <div class="float"></div>
    </div>
  </body>
</html>
```
afer伪类必须设置为 block的理由是, **float会造成内联元素对它进行围绕，所以clear both对于内联元素没作用** 下面是 overflow做法
```html
```html
<!DOCTYPE html>
<html>
  <head>
    <style type="text/css">
      .container {
        background: red;
        overflow: hidden;
        zoom: 1; /*for ie 6*/
      }
      .float {
        float: left;
        width: 100px;
        height: 100px;
        background: yellow;
      }
    </style>
  </head>
  <body>
    <div class="container">
        <div class="float"></div>
    </div>
  </body>
</html>
```

### 同级的相连块元素重叠
- 这个可以使用clear: both进行解决
- 也可以使用bfc的原理，将同级的块元素变成bfc，是的bfc的规则生效 **bfc的区域不会和float box重叠**

**非常值得一读的文章BFC的介绍，弄清楚为何可以这样清楚浮动**[http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html]



