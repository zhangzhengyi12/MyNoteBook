# DOM2 和 DOM3  #
DOM一级主要定义的是HTML和XML文档的底层结构。DOM2和DOM3则在这个结构的基础之上引入了更多的交互能力，同时也支持了更高级的XML特性。
DOM3级和2级分成了许多模块，描述了DOM的子集，具体如下。
 - DOM2级核心 在一级核心的基础之上构建，为节点提供了更多的方法和属性
 - DOM2级视图 为文档定义了基于样式信息的不同视图
 - DOM2级事件 说明了如何使用事件与DOM交互
 - DOM2级样式 定义了如何以编程方式来访问和改变CSS样式信息
 - DOM2级遍历和范围 引入了遍历DOM文档和选择其特定部分的新接口
 - DOM2级HTML 在一级HTML基础上构建，添加了更多属性
 
# DOM变化 #
DOM2级和3级的目的在于扩展DOM API。

    var supportsDOM2Core = document.implementation.hasFeature("Core","2.0");
	var supportsDOM3Core = document.implementation.hasFeature("Core","3.0");
	var supportsDOM2HTML = document.implementation.hasFeature("HTML","2.0");
	var supportsDOM2Views = document.implementation.hasFeature("Views","2.0");
	var supportsDOM2XML = document.implementation.hasFeature("XML","2.0");

## 针对XML命名空间的变化 ##
`p307`

## 其他方面的变化 ##
`p309` 
和上面一样，主要针对于XML文档，再次不做记录
# 样式 #
在HTML中，定义样式的方法主要有三种，
- 通过Link导入CSS文件
- 使用style内部嵌入
- 使用style特性定义特定元素的样式
DOM2级样式围绕这三种方式提供了一套API


	var supportsDOM2CSS = document.implementation.hasFeature("css","2.0");
	var supportsDOM2CSS2 = document.implementation.hasFeature("css2","2.0");
## 访问元素的样式 ##
任何支持style特性的HTML元素在JS中都有一个相对应的style属性。这个style属性是CSSStyleDeclaration的实例。包含通过HTML的style **特性**制定的样式信息，但不包括外部CSS文件和嵌入样式表经层叠而来的样式。（经过测试，只支持特性，不支持嵌入和导入）

属性的名字需要和特性Name进行一个转换，对于使用短划线的CSS属性名，必须转换成驼峰标识。唯一有所例外的是Float,float是JS的保留字，在IE中要使用styleFloat，在其他浏览器中一般使用的是cssFloat

    var dom = document.querySelector("#myname");

	dom.style.backgroundColor = "red";

	dom.style.width = "600px";
	dom.style.height = "500px";

在使用中，最好一直都加上度量单位，比如500px

### DOM样式属性和方法 ###
DOM2级样式，针对style属性
`p314`
在此记录两个方法 `cssText` `length`

`dom.style.cssText = "background-color:blue;width:60px;height:60px;";` 可以方便快捷的去给一个元素添加一系列的style特性

注意，如果你对style对象使用[]或者Item访问，访问到的都是和特性中name一样的值
`alert(dom.style.item(0)); //background-color`

`getPropertyValue`方法返回的始终都是CSS属性值的字符串表示，你可以像下面那样组合使用 或者使用style的length来遍历数组，然后提取数组的每个项（也就是属性名字），通过Name来取得里面的CSS属性值
`alert(dom.style.getPropertyValue(dom.style[0])); `


### 计算的样式 ###
计算样式是浏览器经过层叠计算优先级，最终展现的样式。

DOM二级增强了defaultView属性 添加了一个方法，相比访问style而言，这种方法可以访问其他样式表层叠而来的样式信息。
他接受两个参数，要取得计算样式的元素和一个伪元素字符串。
`alert(document.defaultView.getComputedStyle(dom).backgroundColor);
`
返回一个CSSStyleDeclaration对象，其中包含当前元素的所有计算的样式，他的属性访问为驼峰

IE不支持上面的方法，但支持`dom.currentStyle`方法

**在所有的浏览器中，所有计算的样式都是只读的，不能修改计算后样式对象中的CSS属性，计算后的样式还包含了浏览器一些默认的样式，**


## 操作样式表 ##
CSSStyleSheet类型表示的是样式表，包括通过<link>元素包含的样式表和在style标签中定义的样式表。他只表示是样式表，而不管这些样式表在HTML是如何定义的。
计算样式和Style对象允许修改HTML特性，而CSSStyleSheet是一套只读的接口
    
    var supportsDOMStyleSHeets = document.implementation.hasFeature("StyleSheets","2.0");

	alert(supportsDOMStyleSHeets); //检测是否支持

`p317`
文档中的所有样式表是通过document.styleSheets集合来表示的。通过这个集合可以的length可以获知文档中样式表的数量，而通过方括号语法，或者item方法可以访问到每一个样式表

### CSS 规则 ###
CSSRule对象表示样式表中的每一条规则，在CSS中最常见的就是CSSStyleRule （还有一些比如 @import @font-face）样式规则。

CSSStyleRule对象包含一下属性`p318`
其最常用的有三个属性 `cssText` `selectorText` `style` CSSText是只读的。返回规则 不包括花括号等。

 	var sheet = document.styleSheets[0];
            var rules = sheet.cssRules || sheet.rules; //从样式表中获取规则列表
            var rule = rules[0]; //获取单条规则
            alert(rule.selectorText);
            alert(rule.style.cssText);
            alert(rule.style.backgroundColor);
            alert(rule.style.width);
            alert(rule.style.height);

### 创建规则 ###
`p319`
由于比较繁琐，更加推荐动态加载技术，及动态去插入标签

## 元素大小 ##
这部分不属于DOM二级。但与HTML元素的样式有关。其主要的作用是为了确定元素的大小。

### 偏移量 ###
- `dom.offsetHeight` 元素在垂直方向上占用的空间大小，包括上下边框，元素高度，水平滚动条
- `dom.offsetWidth` 元素在水平方向上占用的空间大小
- `dom.offsetLeft` 元素的左边框至包含元素的左边框之间的像素距离
- `dom.offsetTop` 元素的右边框至包含元素的右边框之间的像素距离
- `offsetParent` 指向当前元素的包含元素

如果想知道一个元素在页面中的偏移量可以使用一下的方法

	function getElementLeft(element) {
	    left = element.offsetLeft; //提取初始元素的左偏移
	    current = element.offsetParent;//提取初始元素的父元素
	
	    while (current != null){
	        left += current.offsetLeft; //将初始元素和父元素的偏移量相加
	        current = current.offsetParent;//重定向父元素
	    }
	    return left;
	}
上偏移类似

### 客户区大小 ###
元素的客户区大小指的是元素内容及其内边距所占据的大小，有两个属性`clientHeight` 和 `clientWidth` 

### 滚动大小 ###

指的是包含滚动内容的元素大小
`p323`

#### 确定元素大小 ####
`getBoundingClientRect()`方法返回一个矩形对象，这个对象中的top、left、right、boootm 这些属性给出了元素在页面中相对于视口的位置

	function getElementLeft(element){
    var actualLeft = element.offsetLeft;
    var current = element.offsetParent;

    while (current !== null){
        actualLeft += current.offsetLeft;
        current = current.offsetParent;
    }

    return actualLeft;
	}
	
	function getElementTop(element){
    var actualTop = element.offsetTop;
    var current = element.offsetParent;

    while (current !== null){
        actualTop += current. offsetTop;
        current = current.offsetParent;
    }

    return actualTop;
	}
	
	function getBoundingClientRect(element){

    var scrollTop = document.documentElement.scrollTop;
    var scrollLeft = document.documentElement.scrollLeft;

    if (element.getBoundingClientRect){
        if (typeof arguments.callee.offset != "number"){
            var temp = document.createElement("div");
            temp.style.cssText = "position:absolute;left:0;top:0;";
            document.body.appendChild(temp);
            arguments.callee.offset = -temp.getBoundingClientRect().top - scrollTop;
            document.body.removeChild(temp);
            temp = null;
        }

        var rect = element.getBoundingClientRect();
        var offset = arguments.callee.offset;

        return {
            left: rect.left + offset,
            right: rect.right + offset,
            top: rect.top + offset,
            bottom: rect.bottom + offset

        };
    } else {

        var actualLeft = getElementLeft(element);
        var actualTop = getElementTop(element);

        return {
            left: actualLeft - scrollLeft,
            right: actualLeft + element.offsetWidth - scrollLeft,
            top: actualTop - scrollTop,
            bottom: actualTop + element.offsetHeight - scrollTop
        }
    }
	}

这是通用的方法。
要注意的是，所谓的left right指的不是元素的border-left/border-right对于视口左右边框的距离，而是用于只和浏览器左上角的区域比较。

# 遍历 #
DOM2级遍历和范围定义了两个用于完成顺序遍历DOM结构的类型 `NodeIterator` 和 `TreeWalker`，这两个类型能够基于给定的起点对DOM结构执行深度优先的遍历操作。也就是说移动的方向至少有两个。遍历以根为起点，不能向上超出DOM树的根节点。

## NodeIterator ##
NodeIterator类型是两者中比较简单的一个，可以使用`document.createNodeIterator()`来创建他的新实例，他有一下四个参数
`root` 想要作为起点的树的节点
`whatToShow` 表示要访问那些节点的数字代码`p328`
`filter` 是一个NodeFilter对象，或者是一个表示应该接受还是拒绝某种特定节点的函数（一个类似过滤器的东西）
最后一个在HTML文档中无效

以下的代码创建了一个能够访问所有类型节点的NodeIterator
`document.createNodeIterator(document,NodeFilter.SHOW_ALL_ELEMENT,null,false);` 

NodeIterator类型的两个主要方法是nextNode()和previousNode()，在DOM树的遍历之中 nextNode方法用于前进一步，previousNode用于后退一步

	var dom_tree = document.createNodeIterator(dom,NodeFilter.SHOW_TEXT,null,false);

	dom.firstChild = "diff text";

	alert(dom_tree.nextNode().nodeValue); //10

迭代器似乎是一个快照，他并不具备动态性，不是类似与NodeList的东西

## TreeWalker ##
TreeWalker是NodeIterator的一个更高级的版本，除了包括nextNode()和PreviousNode()还包括一下在不同方向上游走的方法
- `parentNode()` 遍历到当前节点的父节点
- `firstChild()` 遍历到当前节点的第一个子节点
- `lastChild()`  遍历到当前节点的最后一个子节点
- `nextSibling()` 遍历到当前节点的下一个同辈节点
- `previousSibling()` 遍历到当前节点的上一个同辈节点

创建TreeWalker对象要使用 `document.createTreeWalker();` 其参数与NodeIterator相同

TreeWalker与节点迭代器之间的差距在于treeWalker提供了随意游走的方法，能够在DOM结构中沿任何方向移动。

# 范围 #
为了让开发人员更方便的控制页面，DOM2级定义了范围接口，通过范围可以选中文档中的一个区域。

## DOM中的范围 ##
DOM2级在Document类型中定义了createRange方法（）
这个方法属于document对象。使用hasFeature()可以检测浏览器是是否支持这个方法

`var range = document.createRange()` 此时的范围里是不包含任何DOM块的

每个范围都是一个Range类的实例，这个实例拥有一些属性和方法，
`p322`

### 使用DOM范围实现简单选择 ###
有如下两个比较简单的方式来让范围去选中文档中一部分
- `selectNode()` 
- `selectNodeContents()`

这两个方法都接受一个参数，一个DOM节点，然后使用该节点中的信息来填充范围。其中`selectNode()`选中该节点自身和他的子节点，而 `selectNodeContents()` 只选择该节点的子节点

对选区进行精确的控制`p333`

### 使用DOM范围实现复杂选择 ###
`p334`

### 操作DOM范围中的内容 ###
范围内部中的所有文档节点，都指向实际文档中的节点
第一个方法是`deleteContents()` 删除范围所包含的内容 会体现在实际的文档之中
第二个方法类似`deleteContents()` `extractContents()`不同是他会范围删除的内容，你可以把返回的内容删除到其他地方之中
`cloneContents`可以返回一个范围对象的副本

### 插入DOM范围中的内容 ###
`insertNode`方法可以向范围选区开始处插入一个节点，（经测试，如果你是用选择selectNode添加内容，会添加到这个节点之前）
由于这个范围和实际DOM是一一对应的关系，所以你修改范围中的内容会立即体现在DOM之中。

- 折叠范围 `p339`
- 比较范围 `p339`
- 复制范围 `p340`
- 清理范围 `p340`


## IE 中的范围 ##
p`340`

# 小结 #
DOM2级模块主要针对操作元素样式信息而开发，其特性简要总结如下。
- 每个元素都有一个相关联的style对象，可以用来确定和修改行内的样式
- 要确定一个元素的计算样式（和Chrome-debug工具里的元素样式类似）可以使用   `getComputedStyle方法`
- IE不支持上诉的计算方法，但支持currentStyle属性
- 可以用过document.styleSheets集合访问样式表


DOM二级遍历和范围提供了与DOM结构交互的不同方式
- 使用NodeIterator或TreeWalker对DOM执行深度优先的遍历（后者更加支持自定义的移动方式）
- 范围是选择DOM中的特定部分，然后我们可以对这些部分执行一些操作
- 使用范围选取可以让我在删除文档中的一部分，并且保持文档结构的格式良好。或者复制文档中的相应部分