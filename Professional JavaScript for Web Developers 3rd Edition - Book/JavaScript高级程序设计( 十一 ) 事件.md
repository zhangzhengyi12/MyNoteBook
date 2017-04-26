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
currentTarget和this的值永远相等，接受到事件并执行事件的对象，
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

增强的eventUtil可以让我添加事件删除事件，还添加了跨浏览器的事件方法
分别是 获得事件对象、获得事件的绑定目标，关闭事件的默认行为、关闭事件传播 具体代码在笔记中
在调用这些方法的时候要把事件对象传入进去，就算其中一个方法是获得事件对象，那你也得假装传入一个event对象。

# 事件类型 #
DOM3级规定了以下几类事件
- UI事件 当用户与页面上的元素交互时触发；
- 焦点事件 当用户通过鼠标在页面上执行操作时触发；
- 滚轮事件 使用鼠标滚轮触发；
- 文本事件 当在文档中输入文本时触发；
- 键盘事件 当用户通过键盘在页面上执行操作时触发
- 合成事件 当为IME（输入法）输入字符时触发；
- 变动事件 当底层DOM结构发生变化时触发

## UI事件 ##
UI事件指的是那些不一定与用户操作有关的事件。下面记录一下常见的UI事件

### load事件 ###
JavaScript做常用的的事件就是Onload，当页面完全加载完成之后就会触发window.onload事件。可以使用eventUtil的addHandler来添加事件。不光是window对象，任何元素节点都拥有这个事件，一下就是一个常见的使用技巧
	
	window.onload = function () {
    var img = document.createElement("img");
    img.src = "timg (1).jpg";
    img.onload = function (event) {
        alert(event.target.src)
    }
    document.body.appendChild(img); //加入节点。加入完毕触发onload事件 其要点在于加入节点之前要添加事件
	}

其实在图片添加到文档之前，设置好了图片的src的时候 图片就已经开始了加载。

通过这种方法你同样可以添加script或者link在link添加完毕之后就触发一些事件。

### unload事件 ###
与Load事件对应的是Unload事件，这个事件在文档被完全卸载之后触发，最典型的方式就是用户从一个页面切换到另一个页面就会触发unload事件

利用这个事件最多的方式就是清除引用；

### resize事件 与 scroll事件 ###
当浏览器的窗口被调整宽高的时候，就会触发resize这个事件只在window上面触发，

scroll同样在window对象上触发，但是实际监控的是元素的变化，可以使用Body的scrollLeft和scrollTop来监控到这一变化；

这两者共同的特点是会重复调用事件，所有不应写大量的代码。


## 焦点事件 ##
焦点事件的主要内容在`p368页`
焦点事件的最主要的有两个 blur和focus事件 分别在元素被取消焦点和获取焦点的时候触发。

## 鼠标事件 ##
鼠标事件是最常用的事件因为用户浏览器网页的主体是鼠标。
DOM三级定义了以下鼠标事件
- click 单击鼠标或回车
- dblclick 双击鼠标
- mousedown 按下任意鼠标按钮
- mouseenter 从元素外部首次移入元素内部
- mouseleave 从元素内部移动到元素外部
- mousemove 鼠标在元素内部移动时触发
- mouseout 鼠标指针从一个元素上方移动到另外一个元素
- mouseover 鼠标从一个元素的外部移入一个元素
- mouseup 在用户释放鼠标按钮时触发

实质上 click和 dblclick是 依赖于其他事件的， click等于触发一次mousedown和mouseup dblclick等于触发量词click

### 客户区位置 ###
鼠标事件都发生在窗口之中，这个位置信息被保存在事件对象的clientX和clientY中，这两个位置只和点击位置和窗口直接的距离有关，和滚动无关。

### 页面坐标位置 ###
客户区位置和滚动无关，页面坐标就是相对于页面的top和Left来计算的，pageX/pageY

在页面没有滚动的情况下 页面坐标应该和客户区位置相等；

### 屏幕坐标位置 ###
相对于整个屏幕的位置，screenX screenY

### 修改键 ###
event.shiftKey 可以用来检测在触发的事件的时候是否按住了某个键，

### 相关元素 ###
在发生mouseover和mouseout事件的时候，会有一个主目标元素和一个相关元素，再发生mouseover的时候，事件的主目标的是获得光标的元素，相关的元素是失去光标的元素。
在DOM之中通过event对象的relatedTarget属性提供了相关元素的信息，但在IE8一下之中虽然不支持这个属性，但有不同名字的属性保存着同样的信息。
**具体的兼容方法已经被添加到eventUtil之中**

### 鼠标按钮 ###
在mousedown 和 mouseup 事件中，其event对象存在一个button属性，button属性中可能有如下三个值：0表示主按钮 1表示中间的按钮 2 表示次按钮

在IE中的button属性值内容更为复杂，所以在**eventUtil中添加了getButton方法**

### 更多的事件信息 ###
event事件对象中还提供了detail属性用于给出有关事件的更多信息。对于鼠标事件中detail中包含了一个数值，表示在给定位置上发生了多少次单击。在一个元素上相继的发生一次mousedown和一次mouseup事件算作一次单击。detail属性从1开始计数，每次单击递增。如果鼠标在moursedown和mouseup直接移动了位置，重置为0；

### 鼠标滚轮事件 ###

主流浏览器都支持了mousewheel事件，当鼠标滚动的时候，就会触发这个事件，这个事件可以在任何元素上触发，最终冒泡到document或者window对象。mousewheel事件的event对象有个特殊的属性wheelDelta 当用户向前滚动wheelDelta是120的倍数，向下滚动是-120的倍数 

同样添加到了eventUtil之中，通常我们需要知道用户是向下滚动还是向上滚动就够了。

### 触摸设备 ###
在面对移动设备的开发中，有以下几点需要注意的地方

- **不支持dblclick事件。双击浏览器窗口会放大画面**
- **轻击可单击元素会触发mousemove事件，如果此操作会导致内容变化，将不再有其他事件发生；如果屏幕没有因此变化，那么会依次发生mousedown,mouseup,click事件。轻击不可单击的元素不会触发任何事件。可单击的元素是指那些单击可产生默认操作的元素，或这那些已经被指定了onclick事件处理程序的元素。**
- **mousemove 事件也会触发mouseover 和 mouseout事件。**
- **两个手指放在屏幕上且页面随手指移动而滚动会触发mousewheel和scroll事件。**


## 键盘与文本事件 ##
用户在使用键盘时会触发键盘事件，具体在P`p379页`


## 复合事件 ##
复合事件是DOM三级中新添加的一类事件，用于处理IME的输入序列，其核心目的在于让用户输入在物理键盘上找不到的字符。主要的事件有以下三种，且三种的事件对象event都具有一个data属性，但内容在不同事件下是不同的
    `p385`

## 变动事件 ##
DOM2级的变动事件能在DOM总的某一部分发生变化时给出提示。具体的事件类型在`p385页`


## HTML5事件 ##
有很多浏览器实现了自定义的事件，在HTML中将一部分事件纳入了规范，
### contextmenu ###
这个概念最初来源于win95 说白了就是右键菜单，正常来说浏览器自身会有一个上下文菜单，所以你得通过event.preventDefalut 取消默认行为，你还需要为document对象添加一个点击关闭事件。

### beforeunload ###
这个事件的用途在于让用户真正关闭页面之前询问一下是否真的要关闭。
为了让弹出的询问对话框显示内容，你需要在这个事件的event对象的returnValue设置为你要返回的内容

### DOMContentLoaded ###
这是一个比较常用的东西，页面的加载其实在图片内容比较复杂的情况下是非常慢的，如果你使用window.onload的话其实很多交互事件在这之前都是无法进行的。所以需要一个相对而言比较快，但又能避免JS代码早于DOM树构建之前运行的问题。

DOMContentLoaded事件的触发的基于DOM树构建的，不理会CSS图片及JS文件。

要处理这个事件可以为document对象或window添加相应的事件处理程序 





# p390暂停 先去学习canvas绘图 #