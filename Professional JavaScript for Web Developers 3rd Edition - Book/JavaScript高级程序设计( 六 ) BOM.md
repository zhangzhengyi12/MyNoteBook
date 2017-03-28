# **BOM**#
---
## **window对象**##
在浏览器中，window对象是JS访问浏览器窗口的接口，同时也是ECMAScript规定的全局对象。
在全局作用域中声明的函数，变量都会成为winodw对象的属性和方法，但这些函数和变量不能被delete操作符删除。
要注意的是尝试访问未声明的变量会抛出错误，但可以通过查询window对象，可以知道某个未声明的变量是否存在。

```
var newvalue = oldvalue; //错误 无法直接访问一个未声明的变量

var newvalue2 = window.oldvalue2;
alert(newvalue2); //undefiend
```

### **窗口关系及框架**
> 在使用框架的情况下 浏览器中会存在多个全局对象，不同全局对象中的构造函数并不相等。跨框架使用instanceof操作符会容易出现问题。

### **窗口位置**
各浏览器对于窗口位置的实现有所不同

```
var leftPos = (typeof window.screenLeft == "number") ?
    window.screenLeft : window.screenX;
//screenX是Firefox浏览器的实现
var topPos = (typeof window.screenTop == "number") ?
    window.screenTop : window.screenY;
```

通过上面代码可以在不同浏览器中获取窗口的位置，但要注意的是，screenTop在ie,opera中保存的是屏幕上方到window对象可视页面上方的距离 并不是整个浏览器窗口。而在Chrome,Firefox,Safari中，保存的是整个浏览器相对于屏幕中的位置。所以我们很难在跨浏览器的情况下精确的获取位置。

`moveTo()`方法接收两个参数，新位置的X和Y的坐标值，而`moveBy()`接收的是在水平和垂直方向上移动的像素数。
但这两个方法基本上都被禁用了。


### **窗口大小**
在不同浏览器下，有些浏览器支持获取窗口大小，但是有些浏览器只支持获取视口大小。但视口大小是可以在所有浏览器下确定的。

```
var pageWidth = window.innerWidth;
var pageHeight = window.innerHeight;

if(typeof pageHeight != "number"){
    if(document.compatMode == "CSS1Compat"){ //检测是否是标准模式
        pageHeight = document.documentElement.clientHeight;
        pageWidth = document.documentElement.clientWidth;
    }else {
        pageHeight = document.body.clientHeight;
        pageWidth = document.body.clientWidth;
    }
}
```

`resizeTo() 和 resizeBy()`可以修改浏览器窗口的大小，但是和修改浏览器窗口位置相同，在大多数浏览器中被禁用。

### **导航和打开窗口**
> 使用`window.open()`方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口，这个方法接收四个参数，分别是：要加载的URL，窗口目标，一个特性字符串，以及一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。
> 如果为`window.open()`传递了第二个参数，而且这个参数为已有窗口或者框架的名称 他会在已有的窗口或框架中加载第一个参数指定的URL。
> <br>
> 在绝大多数情况下，这个窗口弹出都会被拦截，如果open方法被拦截，很可能返回null

```
var blocked = false;

try{
    var wroxWin = window.open("http://www.baidu.com");
    if(wroxWin == null){
        blocked = true
    }
} catch(e) {
    blocked = true;
}

if(blocked){
    alert("The window was blocked")
}
```
以上的代码可以方便的检测窗口是否被拦截，无论是window.open方法返回null 或是在open过程中出现了异常（多数情况下是被拦截插件拦截了），他都会弹出一个窗口。

### **间歇调用和超时调用**
#### **超时调用**
超时调用需要的是`setTimeout()`方法，她接受两个参数，第一个为需要执行的代码，第二个是以毫秒表示的时间。

```
setTimeout("alert('Hello World!')",2000);

setTimeout(function () {
    alert("hello world!");
},2000);

//两者都行 但字符串形势性能损失较大
```
虽然第二个参数指定多久后执行代码， 但是在该时间后代码不一定会被执行，因为JS是单线程的解释器，在一段时间内只能执行一段代码，为了控制JS的执行流程，会有一个JS执行队列，而在预定时间之后，这个任务会被添加到执行队列的末端。
<br>
调用setTimeout()，该方法返回一个数字ID，这个ID是计划执行代码的唯一标识符，可以用他取消将要执行的代码。clearTimeout方法可以取消一个尚未执行的超时代码，他的参数是代码的数字ID

```
var timeoutId = setTimeout(function () {
    alert("hello world!");
},2000);
clearTimeout(timeoutId);
```

#### **间歇调用**

```
var intervalId = setInterval(function () {
    alert("hello world!");
},20);
```
和超时调用类似，但是会自动重复执行代码，所以取消间歇调用非常重要

```
 var num = 0;
        var max = 10;
        var intervalId = null;
        
        function incrementNumber() {
            num++;
        
            //if the max has been reached, cancel all pending executions
            if (num == max) {
                clearInterval(intervalId);
                alert("Done");
            }
        }
        
        intervalId = setInterval(incrementNumber, 500);

```
可以使用超时调用来模拟间歇调用

```
 var num = 0;
        var max = 100;
        
        function incrementNumber() {
            num++;
        
            //如果没达到既定条件，再次触发一个超时调用
            if (num < max) {
                setTimeout(incrementNumber, 500);
            } else {
                alert("Done");
            }
        }
        
        setTimeout(incrementNumber, 500);
```
超时调用在执行完代码之后是不会再次执行代码的，在实际开发中，用超时调用模拟间歇调用是一种比较好的方式，因为间歇调用很容音出现同步问题，前一个调用还没有结束，后一个又启动了起来。
### **系统对话框**

```
if(confirm("realy?")){
    alert("ok");
} else {
    alert("no");
}
```
这种方法多了一个按钮 取消。 通过返回值可以知道用户按了确定还是取消。

```
var reasult = prompt("what is your name?","Michel");
if(reasult != null){
    alert("Welcome  "+ reasult);
}
```
该方法支持两个参数，提示的内容和文本域默认值。如果用户点了确定就返回文本域中的内容，如果点了取消就返回null.

```
window.find();//在chrome下似乎无效
window.print();
```
这两个方法是异步的 在调用方法之后，控制权立即交还给JS。

## **location对象**
> location是最有用的BOM对象之一，它提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。它既是window对象的属性，也是document对象的属性;他会把URL解析为独立的片段，然后把这些片段放置到该对象的某个属性之中，让开发人员能通过这些属性来访问URL的部分。属性列表在p207.

## **查询字符串参数**

```
function getQueryStringArgs(){

    //删除开头的的问号
    var qs = (location.search.length > 0 ? location.search.substring(1) : ""),

        //初始化空数组
        args = {},

        //取得每一项
        items = qs.length ? qs.split("&") : [],
        item = null,
        name = null,
        value = null;


    //将拆分后的再次拆分，然后将等号前面的部分作为key 将等号后面的部分作为value
    for (var i=0; i < items.length; i++){
        item = items[i].split("=");
        name = decodeURIComponent(item[0]);
        value = decodeURIComponent(item[1]);

        if (name.length){
            args[name] = value;
        }
    }

    return args;
}

var args = getQueryStringArgs();

alert(args["_ijt"]);
```

### **位置操作**
> location对象可以用来改变浏览器的位置。

```
location.assign("http://www.baidu.com");
```
这是最常用的改变位置的方法，他会打开新的URL并在浏览器的历史记录中生成一条记录，如果你调用了`location.href`也会调用这个方法。还有其他方法在p209。 这些方法都会在浏览器的历史记录中生成信息，并且用户可以点击按钮返回。

> replace方法不会在历史记录中生成，并且页面无法返回。

```
location.replace("http://www.baidu.com");
```
back操作依赖的应该是历史记录，没有记录就无法返回。

> reload方法能够让页面发生重载，在默认情况下会用最有效的方式加载。

```
location.reload(); //有可能从缓存中加载
location.reload(); //从服务器重新加载

```
## **navigator对象**
navigator对象里保存了关于浏览器的信息。
### **检测插件**
plugins数组的每个项目保存了四个属性. name为插件的名字
```
//IE 无效 IE11是有效的
function hasPlugin(name){
    name = name.toLowerCase();
    //字符串转换成小写
    for (var i=0; i < navigator.plugins.length; i++){
        if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1){
            return true;
        }//挨个进行名字的比较
    }

    return false;
}

//detect flash
alert(hasPlugin("Flash"));

//detect quicktime
alert(hasPlugin("QuickTime"));

//detect Java
alert(hasPlugin("Java"));
```

如果是IE浏览器 必须使用如下方式来检测插件，通过创建一个专有的ActiveXObject类型，来尝试能否创建。


```
 function hasIEPlugin(name){
            try {
                new ActiveXObject(name);
                return true;
            } catch (ex){
                return false;
            }
        }
        
        //detect flash
        alert(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"));
        
        //detect quicktime
        alert(hasIEPlugin("QuickTime.QuickTime"));
```
更加好的做法，针对每个插件分别创建函数

```
 //plugin detection - doesn't work in IE
        function hasPlugin(name){
            name = name.toLowerCase();
            for (var i=0; i < navigator.plugins.length; i++){
                if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1){
                    return true;
                }
            }
        
            return false;
        }        
    
        //plugin detection for IE
        function hasIEPlugin(name){
            try {
                new ActiveXObject(name);
                return true;
            } catch (ex){
                return false;
            }
        }
        
        //detect flash for all browsers
        function hasFlash(){
            var result = hasPlugin("Flash");
            if (!result){
                result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash");
            }
            return result;
        }
        
        //detect quicktime for all browsers
        function hasQuickTime(){
            var result = hasPlugin("QuickTime");
            if (!result){
                result = hasIEPlugin("QuickTime.QuickTime");
            }
            return result;
        }
        
        //detect flash
        alert(hasFlash());
        
        //detect quicktime
        alert(hasQuickTime());

```
以上是做法定制了检测函数 如果在非IE检测调用中返回false 则继续尝试用IE的方法检测，如果一开始就返回true 那么不会执行第二次检测。

### **注册处理程序**
与MIME相关
### **screen对象**
screen对象基本用来表面客户端的能力
### **history对象**
history对象保存着用户的上网记录。history是window对象的属性，所以每个窗口每个便签页每个框架都有自己的hisroty对象

```
history.go(-1);
//后退一页
history.go(1)
//前进一页

history.go("baidu.com"); //跳转到最近的baidu.com

history.back();//类似浏览器的后退
history.forward();//类似浏览器的前进
```