# 事件 #
JavaScript和HTML之间的交互是通过事件实现的，**事件**就是文档或者浏览器窗口中发生的一些特定的**交互瞬间**。可以使用侦听器（或处理程序）来预定事件，以便事件发生时执行相应的代码。

# 事件流 #
当某个事件触发时，页面中的哪个部分拥有这一事件？（当你点击一个按钮的时候，同时也点击了他的容器，甚至是整个页面）
**
事件流描述的是从页面中接受事件的顺序**
对于这一问题，早期的IE和Netscape对这一问题的概念截然不同。IE的观念是事件冒泡流。而Netscape的观念是事件捕获流。

## 事件冒泡流 ##
IE的事件流叫做事件冒泡流，即事件开始时由最具体的元素接受，然后逐级向上传播到较为不具体的节点。现在所有的浏览器都支持事件冒泡。

## 事件捕获 ##
网景推出的事件流叫事件捕获。事件捕获的思想是不太具体的节点应该更早接收到事件。最具体的节点最后接受到节点。事件捕获的用意在于在事件达到预定目标之前捕获他。

## DOM 事件流 ##
“DOM2级事件”规定的事件流包括三个阶段，事件捕获阶段 处于目标阶段 事件冒泡阶段
- 首先发生捕获阶段，为截获事件提供了机会（这一阶段不会触发事件处理程序）
- 随机进入处于目标阶段，事件会在具体目标上发生
- 最后开始冒泡并触发一些事件处理

# 事件处理程序 #

事件就是用户或者浏览器执行的某种动作。而相应并作出动作的那个函数就是事件处理程序。事件处理程序以名字On开头。

## HTML事件 ##

HTMl事件就是利用HTML中元素的特性来添加事件
`<div  onclick="alert('rrrrrrr')">10</div>` 
HTML事件如果用户在函数未定义前点击是无效的，代码耦合太严重。

## DOM0级事件处理程序 ##
通过JavaScript指定处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。这种方式简单，跨浏览器。
要使用JS指定事件处理程序，必须先取得一个要操作的对象的引用

	var div = document.querySelector("#myname");

	div.onclick = function () {
    	alert(this.className);
	}

使用DOM0级方法指定的事件处理程序被认为是元素的方法，因此这个事件处理程序是在元素的作用域中运行；所有程序中的this引用当前元素

可以在事件处理程序中通过this访问元素的任何属性和方法。

如果你要删除事件指定事件为null就可以了

## DOM2级事件处理程序 ##
“DOM2级事件”定义了两个方法
- `addEventListener` 添加事件。接受三个参数，要处理的事件名，事件处理程序，一个布尔值，如果布尔为true,会在捕获阶段触发，如果为false就在冒泡阶段触发
- `removeEventListener()` 移除事件，addEvent方法所添加的事件只能依靠他来移除，接受和add同样的三个参数，如果在添加的是匿名函数，那么这个函数是无法移除的。

2级事件的特定是允许你在事件捕获期做出响应，并且可以添加两个同名的事件，而不会像0级那样是一个指针

	div.addEventListener("click",function () { //注意DOM2级的事件没有on
    	alert(this.className);
	},false); //这个事件处理函数为匿名函数，所以无法移除

**无法调用this与元素自身，指向的是window对象**

## IE 事件处理程序 ##
IE实现了与DOM中类似的两个方法 
- `div.attachEvent()`  添加一个事件 两个参数，事件名和事件处理函数
- `div.detachEvent()`  移除一个事件 两个参数 事件名和事件处理函数

与 `addEventListener`类似，IE中的事件处理程序也可以给一种事件类型添加多个事件处理程序

`div.detachEvent()`移除的条件是必须提供相同的参数，也就是说无法删除匿名函数

**无法调用this与元素自身，指向的是window对象**
## 跨浏览器的事件处理程序 ##
`EventUtil`对象
    `p354`

# 事件对象 #
在触发DOM上的某个事件时，会产生一个事件对象event，这个对象中包含着所有与事件相关的信息，包括导致事件的元素，事件的类型以及其他特定事件的相关信息，比如在键盘操作导致的事件对象中，会包含关于按下键的信息。所有浏览器都支持event对象，但支持方式不同。

注意这个事件对象和触发事件的元素宿主不相同，但可以通过这个事件对象获取触发事件的目标

如果你添加匿名事件函数是不要在参数里写入event的，但是在写命名函数的时候就需要了，在实际调用时，会被自动传入。

## DOM中的事件对象 ##
兼容DOM的浏览器会将一个event对象传入到事件处理程序中，无论指定事件处理程序使用什么方法都会传入event对象。
可以使用event.type来知道是什么类型的事件 不如onclick就会显示click

具体的属性在`p355`

要注意区分的有以下三个属性
currentTarget和this的值永远相等，即添加事件的对象
而target对象指向的是事件的目标，实际触发了事件的对象。

比如在body上添加了一个onclick事件处理程序，body内部有一个btn。当点击这个btn触发事件处理函数。在这个程序里面 currentTarget和 this指向了Body自身， 而targer指向了触发事件的btn。

通过event可以在给某个元素的不同事件添加同一个事件处理函数，如下

	var btn = document.getElementById("myBtn");
        var handler = function(event){
            switch(event.type){ //在调用这个事件处理函数时，检测调用事件的类型。
                case "click":
                    alert("Clicked");
                    break;
                    
                case "mouseover":
                    event.target.style.backgroundColor = "red";
                    break;
                    
                case "mouseout":
                    event.target.style.backgroundColor = "";
                    break;                        
            }
        };
        
        btn.onclick = handler;
        btn.onmouseover = handler;
        btn.onmouseout = handler;

调用事件对象的preventDefault可以取消事件的默认行为（只有在cancelable设置为true），比如a标签会打开一个href的链接

调用stopPropagation()方法用于立即停止事件在DOM层次中的传播

事件对象的eventPhase属性可以用来确定事件当前正处于事件流的那个阶段

	var btn = document.getElementById("myBtn");
        btn.onclick = function(event){
            alert(event.eventPhase);   //2 事件触发 经过实际的触发在第三阶段，冒泡阶段，但这里等于2阶段
        };
        
        document.body.addEventListener("click", function(event){
            alert(event.eventPhase);   //1
        }, true); //为true 支持在捕获期间抓取
        
        document.body.onclick = function(event){
            alert(event.eventPhase);   //3 冒泡阶段再次捕获
        }; 