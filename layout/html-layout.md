## html 页面布局

这里主要分为整体布局和局部布局。

### 整体布局

在开发的过程中尝试使用flex进行布局，不使用float, postion, display: table进行页面的框架搭建，能用flex的尽量尝试使用flex。

[flex 整体布局案例-1](https://github.com/lvzhenbang/article/blob/master/layout/flex/html-layout.html)

[flex 整体布局案例-2](https://github.com/lvzhenbang/article/blob/master/layout/flex/html-layout2.html)

下面一个是对上面一个的优化，去除padding + position的布局方式

[flex 整体布局案例-3](https://github.com/lvzhenbang/article/blob/master/layout/flex/html-layout3.html)

待续...

### 局部布局

水平垂直居中布局（宽高自适应）

1. 使用display: table, display: table-cell

[demo](https://github.com/lvzhenbang/article/blob/master/layout/vertical & horizontal/table & table-cell.html)

2. 使用 line-height, vertical-align

[demo](https://github.com/lvzhenbang/article/blob/master/layout/vertical & horizontal/line-height + verticle-align.html)

3. 使用 transform: translate(-50%, -50%) 和 position: absolute; top: 50%； left: 50%;

[demo](https://github.com/lvzhenbang/article/blob/master/layout/vertical & horizontal/css3-transform.html)

4. 使用 display: flex; justify-content: center; align-items: center;

[demo](https://github.com/lvzhenbang/article/blob/master/layout/vertical & horizontal/flex.html)


[垂直居中布局问题总结](https://github.com/lvzhenbang/article/blob/master/layout/verticle-align & line-height.md)

表格的布局

[表格布局](https://github.com/lvzhenbang/article/blob/master/layout/table.md)

多列的布局

[实现像报纸内容排版一样的多列布局](https://github.com/lvzhenbang/article/blob/master/layout/multi-col.md)
