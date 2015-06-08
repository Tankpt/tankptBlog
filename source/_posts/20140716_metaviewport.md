title: 关于meta中viewportt
date: 2014-07-16 16:57:47
tags: js
---

之前做了一个手机端的页面。真是被吭出祥了，特比mark


<!--more-->

###1.1 关于viewport###
有两个viewport的概念：
1.visual viewport:字面意思就是可视的view
2.layout viewport：字面的意思就是布局的view
在meta中设置的viewport中的width其实设置的是layout的数值

在Apple实现viewport后，其他浏览器也加入了对viewport meta的支持，但彼此间还是有些差异，差异最大的是layout viewport的表现:Safari iPhone: 980px,Opera: 850px,Android WebKit: 800px,IE: 974px

###1.2 visual viewport###
暂时的理解就是能看到的屏幕的宽度

###1.3 layout viewport###
layout viewport不是指的页面在手机上最终显示的像素，而是指的整个页面所要显示的宽度，这个宽度到移动端最终显示出来的结果中间还会进行适当的处理；可以这么理解，layout viewport是你最初的页面宽度(比如是整个body的宽度，一般可能是设计稿中量出来的像素)，然后移动端要将这个页面投影到手机的屏幕上去，如果屏幕的宽度是320，那么他会进行适当的缩放，使这个layout viewport的内容显示在屏幕上。
把一张原始的页面放到移动端，首先，你先要把那个页面放到一个容器里面去，而这个layout viewport就是这个容器的宽度，放进去之后，这个容器会进行根据移动端屏幕进行缩放。

这里涉及到像素的问题，就是设计稿中写的1px，跟手中显示屏中的物理1px，其实是俩回事，当你在对页面进行用手指放大或者是缩小的时候(前提是页面里允许你缩放），当你进行放大设计中的1px会占用物理的不止1px，反之缩小的时候,物理的1px其实放了设计中的不止1px的东西，具体的可以看看[这个文章][1]

所以如果在移动端的页面中要是写有定宽的比如640px，那么在meta中，要设置width要大于等于这个值，这点也很好理解，你要用一个容器去装这个页面，但是容器怎么可以比页面还小呢？（这里我也遇到过一次坑，就是试用了width=device-width，这里一般的都会是设备的宽度，都是320，当然要出问题了）

###1.4 其他###
不过貌似现在的移动端的写法都是width=device-width，而且之前看了几篇文章都是建议在移动端写东西不要进行定宽，为了自适应也许，关于这点可以看下[自适应网页设计][2]，他就提倡不进行定宽，在我现有只是水平内，之前的那个问题暂时不知道怎么解决，就是定宽加width=device-width出现问题的情况。
对于不采用定宽，那就只能用百分比进行定位，那些出现的背景图，需要加一个background-position:contain撑满；或者用imag

最后来一个感觉不错的参考list
1.[两个viewport的故事（第二部分）][3] 貌似这个文章是很多写viewport的鼻祖，原文里也有英文的链接
2.[对于移动设备页面尺寸的理解][4] 看了很多文章一直都是晕的，看完这个就明了了很多
3.[探索viewport][5]相当不错
4.[坑爹的viewport][6]


ps:从来没意识到meta是这么叼～～，再附上一个meta的其他属性
[META http-equiv 大全][7]

[1]:http://www.w3cplus.com/css/A-pixel-is-not-a-pixel-is-not-a-pixel.html
[2]:http://www.ruanyifeng.com/blog/2012/05/responsive_web_design.html
[3]:http://weizhifeng.net/viewports2.html
[4]:http://blog.segmentfault.com/fishenal/1190000000495454?page=1
[5]:http://librajt.github.io/2013/03/18/how-web-pages-show-on-mobile/
[6]:http://hax.iteye.com/blog/978184
[7]:http://www.cnblogs.com/jerryshi/archive/2008/10/14/1310611.html