####函数的属性与方法
既然在JavaScript中，函数也是一个对象，所有所以函数都会有两个属性，一个是`length` 一个是`prototype` 

1.  `length`表示函数希望接收的命名参数的个事，也可以认为是形参

2.  prototype属性真正保存了很多实例方法 比如说toString valueOf 当然这里面的东西是无法被枚举出来的 我怀疑之所以无法枚举Date对象就是因为 他的实际数值在prototype属性。

3.  每个函数都有两个自生成方法一个是apply 一个是call 他的作用比较类似 就是使用特殊的作用域调用函数。 apply方法接受两个参数 第一个是其运行函数的作用域 ，另一个是参数数组 这个数组可以是你自定义的 也可以把父函数的arguments直接扔过去 call方法类似apply方法 第一个参数相同 但是后面的参数都是挨个列举出来。

4.  apply和call的真正用武之地是扩充函数的作用域 一般来说我们都是逐级向上查找 无法向下 但是我们可以通过这两个方法来添加作用域。

```
window.color = "red";

var o = {color:"blue"}

function toCall() {
    console.log(this.color); //调用函数时 输出当前环境里面的变量 color
}

toCall(); //red 因为这里的this表示全局变量环境

toCall.call(o); //blue 因为这里的this是o对象。o.color当然是blue

```
这两种方法都是在方法执行之前定义环境和参数 然后执行方法 。其实JS还有返回一个函数实例，并且能对他进行设置环境的方法。这样我们就能得到一个函数的复制 并且这个实例函数的this等值已经重新修改了。`var obj = toCall.bind(0);`


####基本类型包装类

首先我们都知道基本类型是没有属性和方法的。但是实质上基本类型可以拥有方法 比如 `var s = "name";s.subString(2);`在JS中 基本类型就应该是单一的数据结构 他不该是数据方法的集合体。那么js是如何做到的呢。

1.  其实方法非常简单 他首先收到请求（需要字符串类型的一个方法），接着创建一个基本类型的实例 然后在这个实例上调用相应的方法 并把返回值返回，接着立马删除之前的实例。也就是说他把基本类型暂时包装成对象类型，他隐式的调用了new 但是这个实例会被很快的删除，你是无法给一个基本类型添加属性和方法的。但不要随意包装基本类型 因为你很难分清你操作的到底是对象还是基本类型

2.  字符串操作方法

-  首先是concat方法 这方法用于字符串的拼接 不会修改原字符串，返回一个拼接完成之后的字符串。
- 然后是三个基于字符串创建新字符串的方法。分别是 `substr` `silice` `substring`
-  其中 `substring` `silice` 比较相同，第一个参数指定你要获取的字符串起始位置（基于字符串的开头位置也就是0）第二个参数指定你结束位置（同样基于字符串的开头位置0）
- silice有点不同，第一个参数指定起始位置。但是第二个参数指定的是字符串的长度 也就是说等于你第一个参数之后的位数。如果没有第二个参数 默认吃到结束。
- 如果是负数 情况会有所不同 具体在高程p124

3.  字符串位置方法
-  在字符串中查找子字符串的方法有两种 indexOf 和 lastIndexOf 猜都能猜出 一个从左到右 一个从右到左，都支持第二个参数 指定从第几项之后开始找 之前的项都会被忽略。而且区分大小写 当没有找到的时候 会返回-1 否则返回在字符串中的第几项。我们可以通过这个方法循环调用 找到字符串中的所有符合项目。

```
var str = "Lorem ipsum dolor sit amet, consectetur adipisicing elit. A animi aut autem dignissimos eius maiores odio Odit provident, velit vitae! Accusantium at doloremque enim est reiciendis sed temporibus vero voluptatum?";

var position = new Array();
var pos = str.indexOf("o");

while(pos > -1){
    position.push(pos);
    pos = str.indexOf("o",pos+1);//必须加+ 否则找到的永远是他 死循环
}

console.log(position);
```

```
function searchString() {
    if(pos <= -1){
        return -1;
    }
    position.push(pos)
    pos = str.indexOf("o",pos+1);
    arguments.callee();
} //用递归也可以
```

1. trim()方法
- 他会创建字符串的副本 然后删除所有空格 并把处理完成后的字符串返回。

2. 大小写转换方法 
- JS中的大小写转换方法一共有四种。
- `str.toLocaleLowerCase();
str.toLowerCase();
str.toUpperCase();
str.toLocaleUpperCase();`
- 一般来说针对地区会跟稳定写，返回值都是处理完成的字符串。
- 正则表达式暂时跳过 p126

3.  `localeCompare（）`他比较两个字符串 如果如果字符串在参数字符之前（按字符表）返回1 相同0 之后-1
4. formCharCode方法和charCodeAt方法
- 两者相反 formCharCode方法把Unicode转换成字符串 另一个把字符串转换成utf `string.formCharCode(112,225,663,555)`他接收一系列的字符编码参数。

5. html方法
 - p130 不建议使用 没有语义化。


----------
###单体内置对象
官方的定义为有ECMAScript实现 不依赖任何宿主环境的对象 比如Array Object Math.


----------

###Global对象

不属于其他对象的任何属性和方法 都属于全局对象。在全局变量中声明的函数就属于全局对象的方法。

1. URL编码方法
 - URL编码方法有`encodeURI(url)` `encodeURIComponent(url)` 其中 encodeURI只会对http://后面的所有非字母数字进行URL编码，而encodeURIComponent会对所有他发现的字符串进行编码。
2.  URL解码方法 和上面的编码方法对应 `decodeURI()` `decodeURIComponent()` 其中decodeURI只能对encodeURI编码的进行解码。 

2.  eval()方法。
- eval()方法可以看成是一个完整的代码解析器，他的参数是字符串形式的JS代码，在运行eval方法的同时 会解析这个字符串 然后执行，并把执行结果插入代码之中，你可以像普通的代码一样去访问这里面定义的函数或者对象。

4.  Global对象的属性
高程p133

5.  window对象
- 在js中 全局对象都是通过window对象的一部分来实现的，所有全局变量和函数，都将成为window对象的属性。调用的方法有两种一种是直接使用window保留字，或者在一个全局函数内return this.

6.  Math对象
 - min和max方法
	 - 用于找到参数内最小的值 但是直接接收参数非常麻烦 可以一下的方式来接受一个参数数组。`var val = [6,5,6,82,8,2]; var min=Math.min.apply(Math,val);`这个技巧的重点在于调用apply 指定this为Math 并且参数数组为val（apply会把第二个参数数组，分解成单个参数喂入min方法之中）。
- 舍入方法.
		-  一共有三种方法 遵循不同的原则
		- `Math.ceil()//天花板`向上舍入
		- `Math.floor()//地板` 向下舍入
		-  `Math.round()//圆形`四舍五入
- random()方法
	- 他返回一个随机大于0小于1的浮点数 你可以通过他来输入一个随机的整数
	- `var int = Math.floor(Math.random() * 10 +1);`相乘的为最大数+1代表最小值为1

```
function selectForm(lowerValue,upperValue) {
    var choices = upperValue - lowerValue +1 ;//比如1到5 实际需要的不是四个而是五个 这里同理
    return Math.floor(Math.random() * choices + lowerValue);
}

var num = selectForm(2,10);//返回2到10的一个随机数 包括2和10

var array = array[a,d,f,f,g,s,d,d];

var getarray = array[selectForm(0,array.length-1)];
```
抽象化