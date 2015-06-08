title: 实习中遇到的坑
date: 2014-07-13 10:39:46
tags: 总结
---

实习三个月来的遇到了各种坑，忘记了一些，记下了一些，特此mark

<!--more-->

转眼间实习了一半的时间，三个月了，三个月里学到了不少的东西，也遇到了不少的坑。记录下，三个月遇到的坑

##1. IE浏览器对tbody不可修改的问题##
查了下网上， IE下 COL, COLGROUP, FRAMESET, HTML, STYLE, TABLE, TBODY, TFOOT, THEAD, TITLE, TR 这些元素的 innerHTML 属性都是只读的，不能直接操作。在本地测试了下，貌似IE9就不能操作了，但是IE10是可以的；不过TD 的innerHTML 还是可以操作的.

##2. 注意宽屏的显示器##
一般设计切的图，大多都是1680的宽度，但是一些类似大mac上面的显示器明显宽度超出了，所以作为保险应该在背景设置一个图层，然后center居中下

##3.关于在浏览器中的imag的src属性##
  之前也没遇到过这样的情况，如果这个属性里面没有数值，在dom标签里看去是一个空的，但是当用js来调用的时候，_imag.src的值**竟然等于当页面的url数值**,这点确实让人很意外，而且这个坑还远远不止这点。后来网上搜索了下有关的东西，，貌似不处理好这些为空的src，会在一些性能上面有影响，而且不同的浏览器上所呈现的效果还不一样，具体可以见[博客][1]中的介绍，里面还给了几个额外链接的介绍
  
一点建议:现在的浏览器貌似都会对图片进行缓存，所以我的做法就是预设值一个默认的为空的图片，每次替换这个默认的图片。
  
##4. 关于console.log的问题##
平时调试的时候需要，经常会在一些代码的关键的地方加console.log的方法； 以前以为这个是没有兼容性问题的，后来查了下才知道低版本的IE上不支持。 否则遇到不兼容的，后续的js都会挂了

##5. 有关于mouseover和mouseout的问题##

上一段权威指南里的有关这俩属性的介绍
1.mouseover:当鼠标进入目标元素时触发，relatedTarget(IE中是fromELement)指的是鼠标来自的元素；
2.mouseout:当鼠标离开元素的时候触发，relatedTarget(IE是toElement)指的是鼠标去往的元素
3.mouseover:类似mouseouver，但**不发生冒泡**，尚未广泛实现
4.mouseleave:类似mouseleave，但**不发生冒泡**，尚未广泛实现

很明显考虑到兼容性的问题，必须要用前面一对，但是要注意的是这个冒泡问题，太严重了，之前都没这样遇到过，举个例子，比如一些网站会在右上角有一个登录的菜单列表，鼠标hover上去你要去触发一次mouseover的事件，将下拉给显示出来，然后当鼠标离开这个下拉节点时候要触发一次mouseout事件；
不过这只是理论上的，当那个下拉的节点里面有好几层的内容时，当你从最里面的一层节点离开的时候就会触发一次这样的mouseout事件，然后传播到父节点上，然后就会发生你还没移到这个下拉的节点外，节点就消失了的情况

解决的方法：不能说是最好的，但也能实现所需要的效果，就是在mouseout事件里面，设置一个定时器，比如延迟200ms后执行隐藏下拉的情况；然后在mouseover的事件里面检测一下有没有setTimeout的这个一个对象，有的话清除掉，这就可以实现所需要的效果了

##6. 关于float跟li##
一般这俩属性结合在一起的时候，会做一些水平的导航栏什么的，一般都会设置li的list-style-type为none。也不会在意这点，但是当需要的时候就出现问题了:当他们二个同时出现时，在ie下面呢，li前面的小黑块不见了，但是在firefox下面是好的
在写的时候本机里测试，IE低版本会有问题，然后chrome也有问题,可以参考下[博文][2]

##7. 浏览器的内置属性！！##
以前也只是知道过，但是没那么深的体会，这回在做一个web手机端的时候就遇到了body内置了margin属性，导致计算宽度的时候出现了问题，所以一般的都会在浏览的开始引如一些重置reset css的样式

##8. imag 下面几像素的高度空隙##
比如一个div里面单纯的放了一个img的元素，不仔细看是看不出的。仔细看就会发现img离div的底部有大概3px左右的像素空隙；一些解决的方法就是：
1.img 设置为display:block
2.vertical-align: bottom;

原因是：原因在于块级元素内部默认有行框，行框撑高了块级元素。img和其他块级元素并列的时候，由于img默认是display:inline-block，因此也会产生匿名块框包裹img，匿名块框内部生成了行框，多余了几个像素。这也是为何把图片添加上display:block，或者父层级font-size、line-height能够解决这个的原因。（取自[sf社区][3]）

##9. innerText在ff浏览器下问题##
ff浏览器不支持该方法，改用textContent来替代

##10. 获取页面的垂直滚动条的位置兼容问题##
针对获取页面的垂直滚动条的位置，IE Firefox Opera 标准模式下使用 document.documentElement.scrollLeft 获取页面的水平滚动条位置，而混杂模式下则是使用 document.body.scrollLeft。Chrome Safari 在两种文档模式下均使用 document.body.scrollLeft 获取页面的水平滚动条位置。
摘自[w3c help][4]
解决办法：
var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
var scrollLeft = document.documentElement.scrollLeft || document.body.scrollLeft;

##11. 邮件模版##
在邮件中的html有些比较严格的要求，要出来图片布局等等的问题，样式需要进行内联，因为是嵌套在其他的邮箱的页面下面，要写成以一个div为根节点的模块
具体的自己也整理了[一篇博文][5]

##12. xss过滤##
关于这点也只是初次的遇到，知道了xss是怎么一回事，详细的还需要以后多多研究，大概就是一些提供给用户输入信息的地方需要进行转义 ，防止插入一些恶意的脚本

##其他##
1.布局的时候首先考虑用display:block 、float 浮动，不行再考虑用其他的display，最后再考虑用position
2.浮动先行
3.一个页面h1只有一个，注意语意

[1]:http://www.cnblogs.com/pkurain/archive/2009/12/15/1624672.html
[2]:http://blog.51yip.com/htmlcss/858.html
[3]:http://segmentfault.com/q/1010000000441100
[4]:http://www.w3help.org/zh-cn/causes/BX9008
[5]:http://blog.csdn.net/tankpt/article/details/24602087