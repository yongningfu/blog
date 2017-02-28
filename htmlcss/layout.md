# 页面布局和排版的思考

## 圣杯布局和双飞翼布局
这两种布局是传统pc上面的经典布局 左边一般做导航, 右边一般做广告，二维码等,中间的话随着
内容自适应
### 圣杯布局
效果图:
![alt](https://github.com/yongningfu/blog/blob/master/image/grailLayout.png)

基本思路: 
- 利用一个container 设置左右padding
- 然后下面包含三个子块 全部float left
- 第一个为 main 内容设置100% 实现自适应
- 第二个sub margin-left定位到main内容的左边 然后相对定位到container左边padding
- 第三个extra思路一样

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
        body {
            min-width: 600px;  /*2*sub + extra */
        }
        .container {
            padding-left: 210px;
            padding-right: 190px;
        }
        .main {
            float: left;
            width: 100%;
            height: 300px;
            background-color: rgba(255, 0, 0, .5);
        }
        .sub {
            float: left;
            width: 200px;
            height: 300px;
            margin-left: -100%; /*拉伸到main的内容左边*/
            position: relative; /*再用相对定义 将其拖到container的左padding*/
            left: -210px;
            background-color: rgba(0, 255, 0, .5);
        }
        .extra {
            float: left;
            width: 180px;
            height: 300px;
            margin-left: -180px;  /*拉伸到main的内容右边*/
            position: relative;   /*再相对定位到container的右边paddng*/
            right: -190px;
            background-color: rgba(0, 0, 255, .5);
        }
    </style>
</head>
<body>
<div class="container">
    <div class="main"></div>
    <div class="sub"></div>
    <div class="extra"></div>
</div>
    
</body>
</html>
```
### 双飞翼布局
这个和圣杯布局大致差不多，只是它不是用container的padding包含左右两边,而是用main的margin
让左右两边放在margin里面去

效果图:  
和圣杯布局一样
基本思路: 
- 利用一个main-wraper 设置宽度100%, 而且和sub extra都设置有左浮动
- 然后将sub extra利用marign定位到main-wrapper的左右两边（这里不用相对定位）
- 这个时候 sub extra覆盖了main-wrapper内容的左右两边
- 在main-wrapper里面使用一个main, 让它又左右margin 即可实现去掉覆盖
- 即sub extra放入main的margin中

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
        .main-wrapper {
            float: left;
            width: 100%;
        }
        .main {
            height: 300px;
            margin-left: 210px; /*左右的外面距是用来放sub extra的*/
            margin-right: 190px;
            background-color: rgba(255, 0, 0, .5);
        }
        .sub {
            float: left;
            margin-left: -100%; /*让其放在main-wrapper的左边*/
            width: 200px;
            height: 300px;
            background-color: rgba(0, 255, 0, .5);
        }
        .extra {
            float: left;
            margin-left: -180px; /*放在main-wrapper的右边*/
            width: 180px;
            height: 300px;
            background-color: rgba(0, 0, 255, .5);
        }
    </style>
</head>
<body>
    <div class="main-wrapper">
        <div class="main"></div>
    </div>
    <div class="sub"></div>
    <div class="extra"></div>

</body>
</html>
```
参考:

[网页布局](http://www.cnblogs.com/greatluoluo/p/5906926.html)  
[CSS布局 -- 圣杯布局 & 双飞翼布局](http://www.cnblogs.com/imwtr/p/4441741.html)