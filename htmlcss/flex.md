## flex布局
flex布局必须理解 主轴 和 交叉轴两个概念为了方便，这里主轴只考虑水平，交叉考虑垂直  
说明一下flex布局中值得注意的点:

### 在不同的盒子下面的不同体现
1. 如果flex盒子外面不是flex的盒子包裹，那么flex默认是自动填充完width, 高度自适应内容高
2. 如果flex盒子外面是flex盒子， 那么flex默认填充的是height. 宽度自适应内容宽, 即flex盒子默认的align-items是stretch  

### align-items align-self
这些都是相对于交叉轴来说的

### flex-grow flex-shrink flex-basis
这三个属性都是相对于主轴来说的
- flex-grow flex-shrink分配的是 剩余的占据空间
- flex-grow  在主轴上面是否可以放大  以及放大的相对倍数
- flex-shrink  在主轴上面是否可以缩小， 以及相应的缩小倍数
- flex-basis  在主轴分配剩余空间之前，它占据位置的大小， 默认大小为它本身自己的大小

### 最佳实践为主轴只有一条 而不用多条