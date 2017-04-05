# DOM是浏览器针对HTML/XML文档的API

# **节点层次**
文档元素是最外层的元素，在HTML中文档元素就是html元素。
## **节点类型**
一共有12种节点类型。最为常用的是元素节点和文本节点
> 节点有两个基本的属性 nodeName nodeValue

    if(someNode == 1){
    value = someNode.nodeName;
	}

	if(someNode == 3){
    value = someNode.nodeValue;
	}

DOM其实也是一种节点，为comment类型，Nodetype为8。

## **节点关系**
   每个节点都有一个属性 `childNodes` 其中保存一个NodeList对象，这个对象类似数组。它可以通过[]下标来访问项，也有length属性但他不是array的实例。他具有动态性，他基于HTML的动态查询，在文档中的所有操作都能马上在这个对象中体现出来。


    var firstChild = somenode.childNodes[1]; //使用类似数组下标的方式访问
    var secondChild = somenode.childNodes.item(1);//使用item方法访问 两者基本无差别
    
    var arrayOfNodes = Array.prototype.slice.call(somenode.childNodes,0)

    //将一个nodelist对象转换成array的实例 但在IE8及一下的浏览器中无效
    

**常见的节点属性**

 `parentNode`  指向该节点的父节点 
 
  `previousSibling` 指向该节点的上一个同胞节点 如果该节点为第一个则为null
  
  `nextSibling` 指向该节点的下一个同胞节点 与上同理
 
  `firstChild` 指向该节点的第一个子节点 等同于`someNode.childNodes[0]`
  
  `lastChild` 指向该节点的最后一个子节点 等同于`childNodes[someNode.childNodes.length-1]`

`hasChildNodes()`  如果该节点的内部有子节点 返回true 如果没有则返回false

`ownerDocument` 直接指向该文档的文档节点 也就是在HTML中是html元素节点


## **操作节点**

**从常见开始讲起**

` appendChild() ` 将一个节点添加到this的lastChild　参数是新增的节点　节点，更新完成之后返回那个新增的节点。如果这个节点已经是文档的一部分，那么会把要添加的节点转移到新位置。

`insertBefore()` 接收两个参数 要插入的节点和作为参照的节点，新节点会成为参照节点的previousNode,如果参照节点为null 会执行和` appendChild() ` 相同的操作

`replaceChild()` 接收两个参数 要插入的节点和替换的节点 替换的节点会被移除 而插入的节点会替换之前的节点 并且所有与之前节点相关的指针都会重新指向插入的节点 （被替换的节点仍在文档控制之中 但是没有了容身之处）

`removeChild()` 接收要删除的节点， 

**以上四种方法操作的都是某个节点的子节点，要使用这些方法必须取得你想要操作的节点的父节点（parentNode属性）。如果在不支持子节点的节点上调用了这些方法，会发生错误**

**有两个方法是所有节点都有的**

`cloneNode`  方法接收一个布尔参数 如果为true 则进行深度复制 连同这个节点的整个子节点数 一起进行复制 如果false 就返回节点本身，复制返回的节点如果没有添加到文档之中 是孤儿。 

`normalize()`  该方法会在该节点的后代节点中搜索 空文本节点就删除 两个相邻的文本节点就合并。

---

## Document类型

JavaScript通过Document类型（HTMLDocument的实例）来表示文档，在浏览器中，document类型是HTMLDocument类型的一个实例。并且document对象是window对象的一个属性。
### 文档的子节点
 document内含两个快捷方式 可以迅速的访问文档中的HTML对象
 
    alert(document.documentElement)//[object HTMLHtmlElement]
    alert (document.childNodes[0]);//[object DocumentType]
    alert (document.firstChild); //[object DocumentType]

**在书中的例子中出现了差异，在书中`document.documentElement` `document.childNodes[0]` 指向相同的对象,但是在我自己的试验中完全不同，`document.documentElement`经过我的试验与搜索查询 可以肯定他绝对指向了文档中的根节点 也就是HTML中HTML节点.
而`document.childNodes[0]`和`document.firstChild` 他实质上指向了网页的文档声明。**

`document.body`是针对HTML文档的一种扩展，他可以快速访问到文档中body对象，取得body的引用。

书中提到了对于文档声明也就是文档第一行的解释。在不用的浏览器中对他的处理都不太一样书可能时间比较久远，当时的chrome对于文档声明的处理的解析但不把他加入到文档对象的子节点。

### 文档信息
 Document对象作为HTMLDocument(我把HTMLDocument类型看成了普通Document对象的加强版本，针对于HTML文档)的实例，它具有一些普通Document对象没有的东西。比如说
`document.title` 他指向了HTML中的title对象 `document.title = "ver"` `alert(document.title);`

接下来的三个属性都和网页请求有关 他们是`URL`、`domain`、`referrer` 
`URL` 包含了当前页面完整的URL
`domain` 包含了当前页面的域名
`referrer` 包含链接到当前页面的URL 如果没有源页面 则可能为空字符串
所有的这些三个属性都来源HTTP头部
`domain` 和 `URL` 相互关联 

只有`domain`是可以设置的 并且限制很多 设置的domain必须是默认domain的一部分 也就是说默认的domain包含设置后的域。

在JS的跨域问题中，不同域中的JS对象是无法通讯的，而把两个域中的JS对象的domain设置为同个值的时候，两者是可以通讯的。另外域名只能由紧到松，如果松完了还想紧是会报错的。

### 查找元素 ###

`document.getElementById();` 参数为ID 返回引用
 
`document.getElementsByTagName();` 他的返回是一个NodeList，在HTML中的返回是一个HTMLCollection对象。与NodeList非常的类似，具有动态性。他同样支持通过itme方法来访问规定下标的项`image.item(0)`。
	
HTMLCollection对象具有一个特殊的方法`namedItem`，他可以通过元素的name特性来取得对象中的项。

	var nae = document.getElementsByTagName("img");
	nae.namedItem("goImg"); //取得特性name为goImg的子节点  

HTMLCollection对象不但支持单纯索引下标的方式来取得项，还支持名称访问项
`nae.["goImg"];` 如果我们传入的是个数值，他会调用item方法，如果是个字符串他调用的就是namedItem方法
 
如果想取得文档中所有的元素  `var allElement = document.getElementsByTagName("*");`

最后一个方法是`document.getElementsByName();` 为HTMLDocument类型专有的方法，返回的同样也是HTMLCollection对象，这个方法返回带有所有相同Name的项（name一般用于表单，用来保证在提交时，name特性相同的项只有一个会被提交。）

### 特殊集合 ###
document对象的集合，这些集合都是HTMLCollection对象，作为快捷访问方式使用.

`document.anchors` 所有带name特性的a元素
`document.forms`所有的表单元素，和getByTagName填入forms相同
`document.images`所有的元素
`document.links`取得所有带href特性的a元素。

### DOM检测 ###
在书中p259页 不做记录

### 文档写入 ###

这种方式让输出流得以写入文档。

    document.write("<em>" + (new Date().toString() + "</em>"))

这种方式会将流写入文档的末尾，如同你创建了一个元素一样，并且这个元素在后续是是可以被读取和修改的。`wriite` 和 `writen`的差别在于一个原样写入，后者在末尾添加一个换行符。

如果你想写入一个JS的导入 那么你可能需要转义。因为这个脚本导入的闭合标签可能会和其他的标签开头产生联系。

**要注意的是，如果在页面加载期间调用这两个方法只会在末尾添加，但如果在页面加载完成之后调用，则会重写整个页面**

而`close()` 和 `open()` 方法用于关闭和打开网页的输出流。

## Element类型
Element类型用于XML/HTML文档，他提供了对元素标签名，子节点及特性的访问。
访问元素的标签名可以用`nodeName`或者`tagName` 两者返回的值都是一样的。
在HTML中所有的标签名都以大写表示，而在XML中则可能和源码一样。

### HTML元素
所有的HTML元素都是HTMLElement类型表示，HTMLElement继承子Element类型并添加了一些属性
`id` 元素在文档中的唯一标识符
`title` 有关元素的附加说明
`dir` 文字方向
`className` 和元素的class特性对应
p263指出了所有HTML元素的类型（都基于HTMLElement）

### 取得特性
每个元素都会有特性，其用途在于给出相应元素或其内容的附加信息。操作特性的主要方法有三个,可以针对于任何特性。
`getAttribute("class")//不需要加上className` 
`setAttribute()` 
`removeAttribute()`

特性的名称是不区分大小写的，自定义特性应加上data-前缀


任何元素的特性都可以通过属性的方式去访问，但是只有公认的（非自定义）特性才会以属性的方式添加到DOM对象之中

    div.id //可以找到
	div.data-myFirstName //自定义特性 无法通过属性的方式访问


有两种特殊的特性，他们虽然能够通过属性的方式去获取，但是和`getAttribute`获取到是不同的内容

第一类特性是style 在通过get访问的是文本 通过属性是一个对象
第二类特性是onclick这样的事件处理程序 在通过get访问的代码字符串 而通过属性返回的则是一个函数对象

**由于获取时有差别，所以一般使用属性的方式去操作ＤＯＭ，只有在自定义的特性时才使用get方法**

### 设置特性

与`getAttribute`与之对应的是`setAttribute()` 方法。他接收两个参数，特性的名称和值。如果特性存在则修改，如果不存在就创建这个特性并赋值。
这个方法可以操作HTML特性也可以操作自定义特性，**ID会被转换成小写形式。**

所有的特性都是属性，所有可以通过给Element对象赋值来设置特性。但仅限于非自定义特性。

`div.id = "someOtherId"`

在IE中可能有所不同

### attributes属性
Element类型是使用attributes的唯一一个DOM节点类型。attributes属性中包含一个NamedNodeMap，类似于Nodelist是一个动态的集合，元素的每个特性都由一个attr节点表示，每个节点都保存在NamedNodeMap对象中，NamedNodeMap对象包含以下方法。

**特性是节点，但不存在DOM树中，其parentNode为null**

`getNamedItem(name)` 返回nodename属性等于name的节点（也就是一个attr节点）
`removeNamedItem(name)` 删除nodename属性等于name的节点
`setNamedItem(node)` 向列表中添加节点，以节点的nodeName属性为索引
`item(pos)` 返回位于数字Pos位置处的节点

	var id = div.attributes.getNamedItem("id").nodeValue; //获取
	var id = div.attributes["id"].nodeValue; //简写方式

	div.attributes["id"].nodeValue = "777"; //设置特性ID的值

由于这些方法不方便，更多的会采用getsetrm方法，如果想要遍历某个Element对象的特性，还是可以用的。其基本原理就是将传入的节点的attributes里的项的nodename和nodevalue提取出来，然后push进一个数组之中 最后将这个数组的每个项用空格分割之后返回。p267

### 创建元素 ###
使用 `document.createElement()` 可以创建新元素 他的参数为要创建的标签名，这个标签名在HTML不区分大小写，XML中区分。
在创建这个元素节点的同时也为它指定了ownerDocument属性，还可以操作元素的特效及其他操作。

在未将元素插入到DOM中时，这个元素以孤儿存在，并不存在于DOM之中，在IE中可以直接将一些特性传入参数之中从而避免一些IE中的问题。

### 元素的子节点 ###

元素的子节点在不同浏览器中不太相同，所以在通过childNodes遍历的时候要注意这个问题 做一下Nodetype检验


## Text类型 ##
文本节点由Text类型表示，包含纯文本内容，可以包含转义后HTML字符，但不能包含HTML代码。

### 创建文本节点 ###

`var textNode = document.createTextNode("Hello World");`

如果一个元素内由两个文本节点，这两个节点会被连起来显示，中间不会有空格。

### 规范化文本节点 ###

如果出现相邻同步文本节点会很混乱，无法知道一个字符串到底属于哪个文本节点，所有有个文本节点合并方法

`element.normalize()` 假设element下面有两个相邻的文本节点，则将两个文本节点合并，出现一个新的文本节点，这个新的文本节点Nodevlaue为两个相加。

浏览器默认情况下是不会产生两个相邻文本节点。


### 分割文本节点 ###
Text类型提供一个作用与normalize相反的方法，将一个文本节点切割，并把新的节点返回

`element.firstChild.splitText()` 他的参数为指定的位置，如果为5 则到第五个之前的都为旧节点，之后为新节点

这是从文本节点中提取数据的常用手段。

## Comment类型 ##

注释节点，一般不做操作，如果要访问则通过父节点来访问，必须在HTML元素中间，否则无法操作。

## CDATASeciton ##

针对于XML文档。

## DocumentType类型 ##

`document.doctype` 表示文档的类型

## DocumentFragment类型 ##

文档片段是一种轻量级的文档，可以作为一个仓库，保存那些即将添加到文档之中的节点。

    var fragment = document.createDocumentFragment(); //创建仓库
            var ul = document.getElementById("myList"); 
            var li = null;
            
            for (var i=0; i < 3; i++){
                li = document.createElement("li");
                li.appendChild(document.createTextNode("Item " + (i+1)));
                fragment.appendChild(li); //加入仓库之中
            }
            
            ul.appendChild(fragment);   //将这个文档片段插入DOM

## Attr类型 ##

也是节点，但不属于DOM树。

# DOM操作技术 #

## 动态脚本 ##

动态脚本指的是页面加载时不存在，但在将来某一刻通过DOM动态添加的脚步，主要有两种方式

    var script = document.createElement("script");
	script.type = "text/javascript";
	script.src = "client.js";
	document.body.appendChild(script)

这种方式最大的问题是比较难鉴别这个脚本是否被成功引入

另外的方法是直接在节点的text里写入代码，但由于兼容性问题较为差，具体在p278。

## 动态样式 ##

动态样式类似于动态脚本，都是在页面加载时不存在，在将来的某个时间动态添加引入CSS代码

    function loadStyles(url) {
	    var link = document.createElement("link");
	    link.type = "text/css";
	    link.rel = "stylesheet";
	    link.src = url;
	    document.getElementsByTagName("head")[0].appendChild(link);
	}

	loadStyles("style.css");

直接写入CSS代码同样有兼容性问题具体在p280

## 操作表格 ##
涉及的标签非常多，在此不做记录，具体在p282.

## 使用NodeLIst ##

NodeList和其近親NamedNodeMap 和 HTMLCollection 的最大特征在于其動態性。也就是說每當文檔結構發生變化的時候，他們會立即得到更新。所有的NodeList對象都是在訪問DOM文檔實時運行的查詢.

    var divs = document.getElementsByTagName("div");

	for(var i=0;i<divs.length; i++){
    var div = document.createElement("div");
    document.body.appendChild(div);
	}

	//每次当i递增的时候,divs.length同样会递增 因为divs.length是一个NodeList 他会瞬间更新查询 無限循環

   

	//解決方法
    var divs = document.getElementsByTagName("div");


	for(var i=0,len=divs.length; i<len; i++){ //讓len保存一個divs.length的快照
    var div = document.createElement("div");
    document.body.appendChild(div);
	}

總而來說應該儘量減少訪問NodeList的次數,因為每次訪問Nodelist都會調用一次DOM查詢.可以考慮把NodeList保存一個快照來緩存起來.

# 小結 #

DOM是语言中立的API，用于访问和操作HTML和XML文档。DOM1级将文档看作一个形象的节点树。可以使用JS来改变DOM的外观和结构。

- 最基本的节点的Node,用于表现文档中一个独立的部分。
- Document类型表示整个文档，
- Element类型表示文档中的所有元素，可以操作这些元素的内容的特性
- 还有一些其他节点比如文本、注释、文档类型、CDATA区域、文档片段

在添加动态脚本和动态样式的时候，浏览器的处理会出现一些问题

理解DOM的关键在于DOM的性能，DOM是JS中开销最大的一部分，我们应该谨慎对待DOM尤其是NodeList 其动态的特性每次访问都会触发一次查询。有鉴于此最好的办法就是减少DOM操作。