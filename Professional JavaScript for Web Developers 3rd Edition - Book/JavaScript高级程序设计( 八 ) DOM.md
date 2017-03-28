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

`normalize()`   该方法会在该节点的后代节点中搜索 空文本节点就删除 两个相邻的文本节点就合并

---

## **Document类型**
