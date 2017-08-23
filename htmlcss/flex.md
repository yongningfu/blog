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
- flex-grow 针对放大的情况， 即有剩余空间的情况，分配的是剩余的占据空间, 即比例乘以剩余空间
  比如
  ```html
    <div class="parent">
      <div class="child1"></div> 
      <div class="child2"></div> 
    </div>
  ```
  ```css
    .parent {
      width: 100px;
    }
    .child1 {
      width: 50px;
      height: 10px;
      flex-grow: 3;
      background: green;
    }

    .child2 {
      width: 10px;
      flex-grow: 1;
      background: yellow;
    }
  ```
  按照上面计算的话就是
  .child1 width = 50 + ( 100 - 60 ) * 3 / 4 = 80px
  .child2 width = 10 + ( 100 - 60 ) * 1 / 4 = 20px;
  
- flex-shrink  针对的是缩小的情况，即子大小的和大于父
  比如
  ```html
    <div class="parent">
      <div class="child1"></div> 
      <div class="child2"></div> 
    </div>
  ```
  ```css
    .parent {
      width: 50px;
    }
    .child1 {
      width: 150px;
      height: 10px;
      flex-shrink: 3;
      background: green;
    }

    .child2 {
      width: 50px;
      flex-shrink: 1;
      background: yellow;
    }
  ```
  **注意** flex-shrink的比例是相对于自身大小来说
  由于上面 (150 + 50) > 50 所以 flex-shrink起作用
  计算公式为
  child1的缩小比例为child2的三倍, 即 child1缩小 3x, child2 缩小x
  50 = 150 * (1- 3x) + 50 * (1 - x)
  x = 0.3

  child1 width = 150 * (1 - 3 * 0.3) = 15 
  child2 width = 50 * (1- 0.3) = 35

- flex-basis  可以理解成用来替代width的

### 最佳实践为主轴只有一条 而不用多条