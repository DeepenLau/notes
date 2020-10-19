# IFC

内联格式化上下文

> 默认基线对齐，按照 x 字母的基线对齐

![image-20200826180557686](/Users/deepenlau/Documents/notes/study/IFC.assets/image-20200826180557686.png)

## 其他

图片默认有下间隙，因为基线和 x 对齐

img ：vertical-align 默认基线对齐

解决办法

+ display:block => 块级元素 vertical-align 不生效
+ `vertical-align: middle / button / top`
+ 父级 `font-size: 0;` => 基线 === 中线

img 在垂直方向上 line-height + vertical-align 并不是完全居中，在父级在加一个 font-size: 0 即可以居中

