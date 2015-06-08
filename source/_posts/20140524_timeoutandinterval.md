title: setTimeout()和setInterval()
date: 2014-05-24 09:47:53
tags: js
---

够了解setTimeout()和setInterval()么，来试试下面的内容，可以测试下，反正我是学到了很多
<!--more-->

##函数的介绍##

###1.1 setTimeout()###
首先看下，这个函数的定义:([具体请见MDN][1])

		setTimeout(code,millisec);//在指定的延迟时间之后调用一个函数或者执行一个代码片段
 
函数的定义也说的很明白，他主要是在指定的时间之后运行一个函数，提前在这里指出一下，这个指定只是一个期望值，但不是一个实际的，实际他什么时候运行还是一个未知数，原因在下面会有介绍。

###1.2 setInterval###
函数的定义：([MDN][2])

		setInterval(codes, millisec);//周期性地调用一个函数(function)或者执行一段代码

有一个返回值是该函数的ID标识，可以用clearInterval函数来取消这样的周期执行

大概的作用就是周期行的调用一个函数，然后周期的话由millisec来决定，就是数学里的周期函数一样的概念，同理，他这个周期也是一个“期望值”，实际归实际。

##参数millisec的介绍##
在说之前首先推荐看下js定时器是如何工作的。推荐一个文章，[How JavaScript Timers Work][3]，如果英文看不明白的可以看下翻译版本[how-javascript-timers-work翻译][4],看完估计你就会明白很多了，贴上一张里面的图
![js][5]

我也稍微总结一下自己看的个人理解（如果不对希望能指出QQQ~~）：js里面有以一个类似任务的队列，js是单线程的，在每一个时刻里面都只能执行一小段的代码，暂时理解为一个代码块，新的任务将会添加到尾巴上去，所以说任务是有先后的，然后当一个任务在执行的时候，纵然有另一个任务调用了，都必须需要等待到此刻代码块运行结束之后再调用。

###2.1 setTimeout()###
在前面提到了这个时间的参数定义的是js运行到setTimeout函数到他要执行这个函数的时间间隔，由于代码的执行效率是非常的高的，所有一般的情况下，我们是分辨不出这个中间的间隙。类似于setTimeout()这样的函数，执行的过程中，先将该任务放到执行队列中去，然后过了millisec时间后，再去调用，如果此时任务队列里面没有新的任务，那么很自然的按照理想的时间，他会调用，但是极端的情况下就不是那么如意了，如果时间到了，但是执行的任务队列里还堆积着任务，那么他将会被堆积着，直到前面的运行完毕，来一段代码
        
        var sleep = function (_sleepTime){
		var _curTime = new Date(),
			_nextTime;
		console.log("before sleep "+_curTime);
		while(!_nextTime || _nextTime - _curTime < _sleepTime){
			_nextTime = new Date();
		}
		console.log("after sleep "+_nextTime+"  last for "+(_nextTime-_curTime));};
    	var time = function(){
    		console.log("begin setTimeout block");
    		sleep(100);
    		console.log("end setTimeout block");
    	};

	    setTimeout(time,100);
	    sleep(10000);
再看下最后控制台的输出结果

    before sleep Fri May 23 2014 15:25:51 GMT+0800 (中国标准时间) 
    after sleep Fri May 23 2014 15:26:01 GMT+0800 (中国标准时间)      last for 10000 
    begin setTimeout block 
    before sleep Fri May 23 2014 15:26:01 GMT+0800 (中国标准时间) 
    after sleep Fri May 23 2014 15:26:01 GMT+0800 (中国标准时间)      last for 100 
    end setTimeout block 
分析下，按照正常的理想的状态，先执行的是setTimeout的函数，那么过了100ms之后应该会执行他，但是后面的一个sleep函数，直接执行了10000ms，虽然过了100ms确实需要去调用他，但是此时任务被阻塞了。所以你可以从上面的时间发现，setTimeout并没有按照期望的时间来执行。所以不能把有些任务太依赖于它

###2.1 setInterval()###
这个函数还是很有意思的一个，以前也没发现，也是看了那篇博客之后才发现。
1.首先看一个比较正常的情况，比如你需要没过**时间间隔调用一个函数，就可以应用他
        
        var time2 = function(){
		    console.log("sleep for 10ms");
		    sleep(10);
	    };
	    setInterval(time2,1000);
很简单的情况，每个1秒调用一次
- - -
2.然后开始看一个情况，跟1差不多，不过是每隔两秒调用一次time3函数，每次跑time3函数要1秒中，代码如下
        
        var globle =0;
	    var time3 = function(){
    		console.log("I'm the No."+globle);
    		globle++;
    		sleep(1000);
    	};
    	setInterval(time3,2000);
看下console的情况

    I'm the No.0 
    before sleepFri May 23 2014 16:19:04 GMT+0800 
    after sleep Fri May 23 2014 16:19:05 GMT+0800    last for 1000
    I'm the No.1 
    before sleepFri May 23 2014 16:19:06 GMT+0800 
    after sleep Fri May 23 2014 16:19:07 GMT+0800    last for 1000 
    I'm the No.2
    before sleepFri May 23 2014 16:19:08 GMT+0800 
    after sleep Fri May 23 2014 16:19:09 GMT+0800    last for 1000 
- - -
3.现在来一个执行时间要比间隔时间长的看看下情况
    代码如下
    
        var globle = 0 ,
		    _limit = 5 ,
	    	_clear ;
    	var time3 = function(){
    		console.log("I'm the No."+globle);
    		sleep(3000);
    		globle++;
    		if(globle > 5){
    			clearInterval(_clear);
    		}
    	};
    	_clear = setInterval(time3,100);

主要的变化就是将time3函数的时间延长到3秒。这样就出现的时间的执行周期要比延迟的时间短的多多了，然后再来看下这回在console中的结果

        I'm the No.0 
        before sleepFri May 23 2014 16:29:44 GMT+0800
        after sleep Fri May 23 2014 16:29:47 GMT+0800      last for 3000 
        I'm the No.1 
        before sleepFri May 23 2014 16:29:47 GMT+0800 
        after sleep Fri May 23 2014 16:29:50 GMT+0800      last for 3000 
        I'm the No.2 
        before sleepFri May 23 2014 16:29:50 GMT+0800
        after sleep Fri May 23 2014 16:29:53 GMT+0800      last for 3000 
        I'm the No.3 
        before sleepFri May 23 2014 16:29:53 GMT+0800 
        after sleep Fri May 23 2014 16:29:56 GMT+0800      last for 3000 
        I'm the No.4 
        before sleepFri May 23 2014 16:29:56 GMT+0800 
        after sleep Fri May 23 2014 16:29:59 GMT+0800      last for 3000 
        I'm the No.5 
        before sleepFri May 23 2014 16:29:59 GMT+0800 
        after sleep Fri May 23 2014 16:30:02 GMT+0800      last for 3000 


不仔细看还不会发现，在每个执行的时间中，他的时间是连续的。并没有出现我们理想中时间的间隔100ms。中间的原因，可以这么来理解，当运行到第一个time3(1) 的时候，按照正常的情况执行，当时间过了100ms后，会再次调用time3(2)，可此时，前一个time3(1)还在执行，不能打断，只能将time3(2)放到任务队列中，又过了10ms，按照老样子，又会调用time3(3)，但是这个时候time3(1)还在执行，而time3(2)确还在任务队列中，这个时候就会发生一个比较奇怪的事情，就是time3(3)会被丢弃掉。确实很奇怪，下面用代码来测试下看看是否被丢弃掉

         var _beginTime ,
        _clear ,
        _globle = 0;

        var time4 = function(){
        	if(_globle ==0){
        		_beginTime = new Date();
        	}
            console.log("I'm the No."+_globle);
            if(new Date() - _beginTime > 2000){
            	clearInterval(_clear);
            }
            sleep(3000);
            _globle++;
        };
        _clear = setInterval(time4,100);
        
如果他是一味的将任务加到队列中，而没有进行丢弃的动作的话，周期是100ms，然后我做的限制是当执行时间超过2000ms后，清除定时，如果没丢弃那么队列中应该会有大概20个的动作等待操作，然后在看看console中的结果：

        I'm the No.0 
        before sleep Sat May 24 2014 09:25:40 GMT+0800  
        after sleep Sat May 24 2014 09:25:43 GMT+0800   last for 3000 
        I'm the No.1 
        before sleep Sat May 24 2014 09:25:43 GMT+0800  time.htm:14
        after sleep Sat May 24 2014 09:25:46 GMT+0800   
        
###2.3 补充###
经过上面的一些分析之后，应该稍微对这俩函数有点了解
1. setTimeout()执行的时间是>=规定的时间
2. setInterval()执行的时间可能会比预期的时间小，比如上面例子中的0ms，没有时间间隔，某些间隔可能会被跳（被丢弃了）
3. 如下代码

        setTimeout(function () {
            //code
            setTimeout(arguments.callee, interval);
        }, interval)
就是一个能防止中间间隔时间不出现那种0ms，或者是丢弃的方法，类似递归一样的解决

以上都是自己看博客加自己个人的理解，如有不对请指出，谢谢～
再附上两篇参考的文章：

1. [setTimeout()和setInterval() 何时被调用执行][6]

2. [setTimeout和setInterval的区别你真的了解吗?][7]

3. [JavaScript Event Loop 浅析][8]

4. [理解JavaScript定时器：setTimeout和setInterval][9]

[1]:https://developer.mozilla.org/zh-CN/docs/DOM/window.setTimeout
[2]:https://developer.mozilla.org/zh-CN/docs/Web/API/Window.setInterval
[3]:http://ejohn.org/blog/how-javascript-timers-work/
[4]:http://www.cnitblog.com/asfman/articles/55714.html
[5]:http://ejohn.org/files/Timers.png
[6]:http://www.cnblogs.com/dolphinX/archive/2013/04/05/2784933.html
[7]:http://www.jb51.net/article/26679.htm
[8]:http://heroicyang.com/2012/08/28/javascript-event-loop/
[9]:http://heroicyang.com/2012/09/06/javascript-timers/