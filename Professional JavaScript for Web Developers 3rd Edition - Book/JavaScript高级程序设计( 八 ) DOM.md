## DOM是浏览器针对HTML/XML文档的API

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

## **节点关系**
   每个节点都有一个属性 `childNodes` 其中保存一个NodeList对象，这个对象类似数组。它可以通过[]下标来访问项，也有length属性但他不是array的实例。他具有动态性，他基于HTML的动态查询，在文档中的所有操作都能马上在这个对象中体现出来。


    var firstChild = somenode.childNodes[1]; //使用类似数组下标的方式访问
    var secondChild = somenode.childNodes.item(1);//使用item方法访问 两者基本无差别
    
    var arrayOfNodes = Array.prototype.slice.call(somenode.childNodes,0)

    //将一个nodelist对象转换成array的实例 但在IE8及一下的浏览器中无效
    

**常见的节点属性**

 `parentNode`  指向该节点的父节点 
 
  `previousNode` 指向该节点的上一个同胞节点 如果该节点为第一个则为null
  
  `nextNode` 指向该节点的下一个同胞节点 与上同理
 
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