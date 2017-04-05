# DOM扩展 #
对于DOM的主要扩展有 Selectors API（选择符API） 和 HTML5

## 选择符API ##
通过CSS选择符来选择某个与之匹配的DOM元素。W3C目前把这种方法加入到标准之中，通过Document 和Element类型的实例来调用。

### querySelector() 方法 ###
`document.querySelector()` 方法接受一个CSS选择符，返回与该模式匹配的第一个元素，如果没有找到，返回null.

	document.querySelectorAll("div");
	document.querySelectorAll(".div");
    document.querySelectorAll("#div");
	document.querySelector("img.button")

### querySelectorAll() ###

`document.querySelectorAll()` 方法接受一个CSS选择符，但会返回一个NodeList，里面包含所有符合要求的DOM对象

### matchesSelector()  ###
`console.log(document.body.webkitMatchesSelector("body"));`
这个方法可以让我们验证调用元素是否和CSS选择符匹配，如果匹配则返回true
但目前各浏览器支持的并不够完善。 如果要使用这个函数最好写一个包装函数，在内部检测是什么浏览器然做出相应的检测最后返回。

# 元素遍历 #

其目的在于规范化各个浏览器对于子节点的差异化，规范增加了一些方法 `p288`

# HTML5 #
HTML规范针对于新增标记定义了大量的JavaScriptAPI。

## 与类相关的扩充 ##

### getElementsByClassName()方法 ###
HTML添加的 `getElementsByClassName()` 是最受欢迎的方法。
他接受一个参数，包含一个或多个类名的字符串。返回带有指定类的所有元素的NodeList，传入多个类名时，类名的先后顺序不重要。

## classList属性 ##

在操作类名时，我们需要通过className 属性来添加删除或者替换类名，这样操作非常的麻烦，因为className是一整个字符串，假设你需要删除三个类名中 的一个那么你需要提取字符串，然后切割，并把单个项检测，最后把剩下的字符串拼接成一个classname然后重新设置DOM对象的类名。

但是在HTML5规范之中，新增了一种操作类名的方式，那就是classList属性，这个属性是新集合类型DOMTokenList的实例

classList具有其他类数组的特性，比如通过item方法访问，支持length访问长度，还可以使用[]来访问,这个了类型还定义如下方法。
`add(value)` 将给定的字符串添加到列表中，如果存在 忽略
`contains(value)` 表示列表中是否存在给定的值，存在返回true
`remove(value)` 移除给定的字符串
`toggle(value)` 如果列表中有给定的值，删除它，如果没有，添加他。

支持classList的浏览器有Firefox 和 Chrome


## 焦点管理 ##

主要的方法有两个
`document.activeElement` 这个对象始终指向文档中获得焦点的对象

`document.hasFocus()` 这可以检测调用对象是否获得的了当前的焦点。

## HTMLDocument的变化 ##

- `readyState`属性，指示当前是页面是否被加载完毕 指向可能为"loading""Complete"
- 兼容模式`document.compatMode` 检测页面的兼容性
- `head`属性 可以直接引用文档的head元素

## 字符集属性 ##

- `document.charset` 指向了当前页面的字符集，可以获取，也可以修改这个值。
- `defaultCharset` 表示根据默认操作系统和浏览器下的默认字符集，可以检测这个值和charset


## 自定义数据属性 ##

HTML5规定可以为元素添加非标准的属性，但要添加data-前缀，其目的为元素提供与渲染无关的信息，或者提供语义信息。这些属性可以随便添加，任意命名，只要添加data-前缀

添加了自定义属性之后，可以通过元素的dataset属性来访问自定义属性的值
	
	var div = document.getElementById("myname");

	var appid = div.dataset.type;

	alert(appid);

dataset属性的值是DOMSrtingMap的一个实例，也就是一个名值对的映射，每个data-name形式的属性都会有个对应的属性，只不过去除了data-前缀

## 插入标记 ##

插入标记一般用于插入大规模的HTML片段，它可以直接插入HTML字符串。
一下的三种方法其共同特点就是支持直接的字符串输入，而不是和插入节点一样需要经过比较复杂的节点嵌套与拼接

### innerHTML属性 ###
在读模式下，他返回与调用元素的所有子节点对应的HTML标记，在写模式下，他会根据指定的值创建DOM树，然后把这个树完全替换调用元素的所有子节点。

他也有一些兼容性问题 `p295` 所以在插入代码之前，最好先检查一下里面的文本内容

### outerHTML属性 ###

与上面的innerHTMl类似，但区别在于所有的读取和写入是包括调研用的元素的自身的。也就是说如果div.outerHTML = "somecode" 他会连带这个div一起重写。somecode会覆盖div这个元素本身。

### insertAdjacentHTML()方法 ###

这个方法接受两个参数 插入位置和插入内容 插入位置必须是以下的四个选项之一
`beforebegin` 在当前元素之前插入一个紧邻的同辈
`afterbeing` 在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素
`beforeend` 在当前元素之下插入一个新的子元素或在最后一个子元素之前再插入新的子元素
`afterend` 在当前元素之后插入一个紧邻的同辈元素


## scrollIntoView()方法 ##

调用某个元素的这个方法让元素出现在页面中。但是给某个元素设置焦点同样可以导致页面滚动和显示出获得焦点的元素，而且这个方法是不支持Chrome的。

# 专有扩展（以下都是） #
 专有扩展来源于各个浏览器的一些独特做法，现在已经在HTML5中被标准化
## 文档模式 ##

文档模式由IE8引入，他决定了你可以使用那个级别的CSS

到IE9共有四种文档模式，

如果想让页面强制使用某种文档模式渲染可以在META标签设置`p299`

## children属性 ##

这个属性是HTMLCollection的实例，他只包含调用元素中同样也是元素的子节点，除此之外和childNodes基本相同。
`element.children[0]` element的第一个子元素节点

## contains方法 ##
在开发中经常需要知道某个节点是不是另一个节点的后代。
`contains` 方法的调用者应该是祖先节点，而参数是需要验证的子节点

`alert(document.documentElement.contains(document.body));` 

除此之外 DOM lv3的compareDocumentPosition()也可以确定两个节点的关系 

通用的contains检测函数`p300`

## 插入文本 ##

### innerText属性 ###

这个属性没有被纳入HTML规范

`p301`


## 滚动 ##

`p303`