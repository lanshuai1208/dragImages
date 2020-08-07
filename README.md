### ![image-20200807153229553](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200807153229553.png)



# 一. 实现效果

在图片上按住鼠标左键拖动图片，如果图片当前选择位置与其他图片有重叠，会显示红色表示不可放置；反之，显示绿色，表示没有重叠。若当前图片选择位置有红色，则不可放置，松开鼠标后回到原来的位置；若图片位置全是绿色，表示位置安全，松开鼠标后图片移动到新的位置。



初始状态

![初始效果](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200807154205442.png)



不可移动

![image-20200807155233028](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200807155233028.png)



可移动

![image-20200807155309359](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200807155309359.png)



#  二. 技术要点

##  1. canvas 拖动

建立2个canvas作为图层，其中一个图层作为基础图层，作用是绘制图形；另外一个是移动图层，用于移动组件的时候显示目标组件的虚像。
在2个图层外层套一个div容器，用于绑定事件，因为2层 canvas 本身重叠不能很好的绑定事件
拖动需要绑定3个事件，
1）onmousedown：鼠标左键按下，此时需要判断点击的位置是否在某个组件内，如果是的话，将该组件找出来
2）onmousemove：鼠标拖动，找到点击的元素后，将其虚像渲染到移动图层中，没拖动一下，清空所有移动图层，然后重新渲染目标组件虚像。
    这里需要将高频操作进行节流：我使用时间戳的方式进行节流，将控制器放到循环外面，没有用函数封装闭包，因为那样会难以获取当前event
3）onmouseup: 鼠标放开，此时要解除onmouseup和onmousemove的事件绑定，避免内存泄漏，onmousedown不需要，因为这个会被覆盖掉；
    然后判断当前位置是否合法（没有与其他组件重叠）

## 2. 矩形重叠判断

两个矩形不重叠，可以归结为四种情况：A在B左边/右边/上边/下边，如果A的左边界在B的右边界的右侧，则A一定在B右边，以此类推。
如果不满足任何一个重叠状况，这可判断两个矩形重叠。
重叠需要返回重叠部分：如果矩形重叠，则其在水平和垂直方向上一定是都重叠的。上下左右都取最靠近另一边的值，得到的就是重叠部分。



## 3. 响应式实现

仿照vue2的原理，通过Object.defineProperty劫持对象中属性的setter和getter实现对对象属性的监听及深度监听.