# css实现三角形

css实现三角形是个非常有意思的事情。

先看看如果 width height都为0，边框不为零的时候，浏览器是如何渲染的？ 莫非是中间留出空位？当然并不是，它会自动把边框变成三角形
```css
  width: 0;
  height: 0;
  border-width: 30px;
  border-style: solid;
  border-color: yellow red green blue;
```

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5216235-fb3f2e17b3942808.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以，如果我们想要三角形的话，只需要 只设置边框，让某些边框变成透明即可

把三个边框都设置为透明
```css
#triangle-up {
  width: 0;
  height: 0;
  border-width: 30px;
  border-style: solid;
  border-color: transparent transparent red transparent;
}
```
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5216235-ae4677ec2b35ae21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面可以看到，三角形的形状就出来了。其他方向其实也同理

左下的效果
```css
#triangle-left-down {
  width: 0;
  height: 0;
  border-width: 30px;
  border-style: solid;
  border-color: transparent transparent red red;
}
```

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5216235-cba7f36b40623ef2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)