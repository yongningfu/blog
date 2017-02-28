## html css属性讲解
### html align属性
- div中 align属性用于设置div内容水平对齐
- table中 align属性用于设置table相对于周围元素的排列方式
```html
假设当前屏幕分别率为1024×768，定义一个居中的占屏幕一半大小的表格的语句是
<TABLE ALIGN=”CENTER” WIDTH=”50%”></TABLE>
<TABLE ALIGN=”CENTER” WIDTH=”512″></TABLE>
<DIV ALIGN=”CENTER”><TABLE WIDTH=”512″></TABLE></DIV>
<CENTER><TABLE WIDTH=”50%”></TABLE></CENTER>
```
上面全都可以
### clear both属性思考
clear both只能是针对浮动元素的下一个元素有效果，仅仅下一个元素
```html
<html>

<head>
<style type="text/css">
  .cle {
  float: left;
  clear: both;
  }

  img
    {
    float:left;
    clear:both;
  }
</style>
</head>

<body >
  <img src="/i/eg_smile.gif" />
  <img src="/i/eg_smile.gif" />
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
  <div >aaaaaaaa</div>
</body>
</html>
```
