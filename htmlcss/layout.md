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


