#  函数 #
  函数的返回会终止函数的指向 return之后的语句不会被执行
 - 参数 
	 - js并不关心你传了什么类型的参数 传了多少个因为内部函数接收的时候，接收的只是一个参数组成的数组 他不会访问里面的value 而这个数组可以通过arguments对象来访问 ，这个对象具有一个数组的基本特征。所以你可以通过检查这个对象的特性来执行一些语句 比如length为0就如何如何。<b>这也算是削弱版的重载。<b>
	 -  arguments支持和形参一起使用 比如`arguments[0] + num1;`
	 - arguments还有一个非常重要的特性 他会和实参进行随时的同步 也就是说 当你修改了arguments或者实参的值 两者都会立马同步。 但这并不代表两者指向同一块区域，两者只是保持同步而已。
	 - 参数 还有一个特性 当你传入的个数与实际不符 实参<形参 那么少出来的用undefined填充 如果实参>形参 多出来被忽略。
	- 没有重载
		- 重载加java等语言中一个非常重要的特性 其表现为多个函数名称相同（同一等级内）但其签名（参数的个数 与 类型）不同，当你实际调用时，通过不同的签名来进入不同的函数
		- 在javascript中 如果你定义了两个名称相同的函数 控制权永远在后定义的函数之中（猜测可能在运行前 提升过程中 后声明的被提升 覆盖前面的函数）
		- 因为JavaScript中没有签名这个概念，他并不会检查进来的参数。所以在JavaScript 不可能有完美重载，你只能靠对arguments进行一些检查来做到模仿重载的效果。
		



# 变量与作用域和内存问题 #

1.  两种变量类型
  - 基本类型和引用类型 基本类型保存在stack中 引用类型保存在heap中 
2. 变量的复制
 - 基本类型变量的复制为创建一个空间 然后命名 把字面量放入到新创建的空间之中 复制完成后 两者没有任何关系。
 - 引用类型的复制复杂一些 创建空间并命名 接着并不是放入字面量对象 而是一个指针 他指向了存储在堆中的一个对象。你修改了任何一个引用的属性 都会在另外一个引用上体现出来，因为两者实质上指向的是同一个对象。
3.  传递参数
 - 参数的传递和复制变量是相同的，复制基本类型按照基本类型的原则，也就是吧传进去的参数变成一个局部变量，在传递过程中发生了复制，但这两个并没关系 你修改一个并不会对另个产生任何影响。事实上 这函数结束之后 内部的那个局部变量会被删除。
 - 引用类型的参数传递，传递的同样也是具体的值 而不是引用，他创建一个变量接着命名 ，然后这个空间内存放原始变量的指针。尽管两者指向同一个对象，但并不代表两者是同一个变量。你无法通过一个变量修改另外一个变量的指针对象。
 - 也就是在函数准备运行环境的时候 他会帮你隐式的去创建局部变量好来传递参数，这些局部变量在函数结束之后会被立马删除。
4.  检测类型
 - 检测类型的方法主要有两种，一种针对基本类型，一种针对引用类型。
 - `typeof s`针对基本类型 
 - `person instanceof Object` 针对引用类型，他可以让我知道一个对象是XX类型的对象吗。他的查询路线是通过自己的原型链逐级往上查找。

5.  执行环境和作用域
- 我把执行环境看作是函数的运行环境，里面包含了你可以使用的变量，函数等。也就是函数执行的工具。
- 最外围的全局变量也是一个执行环境。
- 所有的函数都有一个变量对象，里面包含了你可以使用的所有变量（当前函数内的，至少包含arguments对象 也就是参数对象）
- 内部的变量对象和外部的变量对象会通过一个作用域链连接。当需要某个变量时，首先寻找自己内部的变量对象。如果有则调用，如果内部没有声明则通过作用域链寻找外级的变量对象。直到找到最外部的全局变量对象，还是找不到就报错。
- 作用域链是固定的，但有办法延长他 比如try catch语句和 with 语句，这两者的原理都类似，就是在块执行之前，加载一个变量对象。把这个变量对象加载到他的作用域链之上。

## 没有块级作用域 ## 
- Js没有块级作用域，也就是说if for语句的块内部和外部的完全联通的。js中的是函数作用域
- 通过var声明的变量，会被添加到最近的函数环境之中，如果没有var声明 则会被添加到全局环境之中。
- 标识符其实就是变量的name 我们查找变量其实就是寻找响应的标识符 找到之后就取出里面的字面量
- 所有关于变量的查找，都是由内而外 无法由外到内，如果内部就有，是不会继续向外查找的。

7. 内存管理
- js里面的内存管理通过垃圾收集器工作。他觉得这东西没有用了就打个标记，隔XX时间就一次性收集删除。
- 我们可以通过手动标记的方法来让垃圾收集器在下一次清理时清理他 `var object = new Object(); object = null;` 这个方法叫手动解除应用。

8. 对象的创建
- 创建的方法有两种 一种是通过new 调用他的构造函数 ，第二种是通过对象字面量的方式创建

```
var obj = new Object();//调用构造函数

var obj2 = {
    name:"zhang",
    age:16
}//使用对象字面量 可读性更高
//注意这是对象不是数组 里面的key已经被重设了 你肯定没办法通过[0]之类的来访问

function display(args) {
    var output = "";
    if(typeof args.name == "string"){
        output += args.name;
    }
    if(typeof args.age == "number"){
        output += args.age;
    }
    alert(output);
}

display({
    name:"laoda",
})

display({
    name:"laoda2",
    age:66,
})
//通过对象字面量的方式去传递一些参数 如果检测到就进行一些操作。
//可以把必须的参数单独传进去 把一些可选的参数通过这种方式传递
```


9.  数组
- 数组其实就是特殊一点的对象，他帮我们预设好key 也就是0123的下标，构造的方法同样有两种
 
- 通过array对象的构造函数或者是数组字面量 比如`var ay = new Array(5)//创建一个长度为5的数组; var ay2 = new Array("blue","red")` 以及字面量创建 `var ay3 = ["blue","red"]`

- 如果数组的长度被修改小于原来的长度，少出来的会被删除，相反就增加为undefined。

- 我们可以通过length属性为我们便捷的在数组的末尾添加项，`ay[ay.length]=66;`因为length属性总是比末尾的下标多1。

- 数组的检测有两种 一种是通过`value instanceof Array`这种方法在多全局环境下会有问题 因为她可能调用的是不同的构造函数。第二种是ECMA5新增加的方法 `array.isArray（）`对浏览器的版本要求比较新。

- 可以用过数组的join()方法来定义中间用于分隔的符号（默认toString方法为,分割0）

10.  数组的栈方法
- 栈是一种数据结构 表现为后入先出 也就是说，所有的数据存取都在栈的顶部进行。你push会在栈的顶部添加 而pop会在栈的顶部移出，js为我们提供了类似栈方法的数组方法

- `array.push("red","blue");//推入两项`
- `array.pop();//推出最顶部的一项`


10.  数组的队列方法
- 既然有栈方法（后入先出）当然也有先入先出的方法 我们叫他队列方法 相对应的数据结构叫做队列数据结构


- 往数组末尾添加的方法我们已经有了 就是push() 那么只需要一个往数组前端提取的方法

- `array.shift()`取得我们数组前端的第一项
- `array.unshift("black")`在数组前端推入一项

- 无论是shift和pop方法 都会把里面的项真正return 然后删除这个项。

- <b>也就是说js为我们提供了四种操作 分别是 pop在后端弹出 push在后端推入 shift在前端弹出 unshift在前端推入<b>
- <b>我把栈看成往上搭积木 造从下往上造 拆从上往下拆。把队列看成排队 多的人排在后面，前面的完事了消失。<b>

11.  重排序方法
- 重排序的方法有两种 分别是 reverse 和 sort
- reverse让我可以把数组前后反转
- sort的基本规则是小在前 大在后，但是他比较的是字符串而不是数字 所以这并不能满足我们的需要，但是他允许提供一个比较函数，这个比较函数有两个参数 
 

```
var values = [1,2,3,4,5,6];

function compare(value1,value2) {
    if(value1 < value2){
        return -1;//如果返回-1 代表value1应该在value2之前
    }else if(value1 > value2){
        return 1; //如果返回1 代表value1应该在value2之后
    }else {
        return 0; //如果返回0 两者相等无需更改位置
    }
}//只要把-1 和 1 对换 就可以让数组倒序排列

values.sort(compare);

alert(values);
```

```
function compare(value1,value2) {
    return value1 - value2;
}//简写版本 第一个数大于第二个 返回正数 进行交换 如果要倒序排列 可以value2-value1
```


### 操作方法

1.  基础的副本操作方法concat和slice
 - `var colors2 =colors1.concat("yellow","blue")` 创建副本 并把新添加的项添加到新的副本之中 接着作为返回值返回 不会修改原来的数组
 - `var colors3 = colors1.slice(1,3)` 创建副本 把副本中的1项和3项删除 同样不会影响原来的数组。
2.  splice方法
- splice方法有三个参数 第三个可以被省略 分别代表了 起始下标 删除个数 以及需要添加的项。这个方法直接操作原本的数组，他的返回值是被删除的项组成的数组。
- `colors2.splice(0,2)`删除 从数组的第一个开始 删除两个
- `color2.splice(1,0,"blue")` 插入项 位置为第二个 删除0个
- `color2.splice(1,1,"blue")`从第二个开始 删除一个 然后插入一个

### 位置方法
1.  indexOf 从开头开始寻找 第一个参数为必选参数 第二个可选参数 `numbers.indexOf(4.4)`代表从前往后查询 目标项为4 从下标为4的项开始起步
2. lastIndexOf 从末尾开始查找 其他与上面类似。同样支持两个参数 第二个为起步位置参数。

### 迭代方法
1.  迭代方法有以下几种
 - every() 检查数组所项目 全部符合返回ture 否则flase

 - some()检查数组所有项目 只要有一项符合 返回ture 否则false

 - filter() 检查数组所有项目 进行条件判断 把符合条件的项目 整合成一个数组返回

 - forEach()就是普通的遍历 把每一项带入到相应的参数之中，并且没有返回值。

 - map()检查数组的所有项目 做出相应操作 比如x2 然后把结果返回成一个数组。

 - 他们的使用方法都非常的相似
 

```
array.method(fucntion(item,index,array){
 return (条件或操作);
})
```

### 归并方法
1.  他们都是遍历所有值 然后构建一个最终的值 `reduce` 和`reduceRight` reduce开头到结尾 reduceRight 从结尾到开头

2.  `var num = array.reduce(function(prev,cur,index,array){return prev+cur;})` 他们的参数都相同一共有四个参数  prev 前一个项 cur当前项 index当前项的下标 array当前数组对象。 这个函数所返回的值都会在下次遍历中被存储到prev参数之中，也就是说当前项时，prev参数里面是上个遍历的return。


### Date类型 
1.  js中的Date类型基于java的工具类里的Date类

2.  `var date1 = new Date();`构造方法 在没有传参数的情况下 会调用系统的时间 自动设置。我自己的测试是Date是个对象 但是比较特殊 因为他无法通过forin循环来遍历。根本没的循环。+


### RegExp
正则表达式 RegExp是用来支持正则表达式 同时页是引用类型的一种

### 函数
函数声明和函数表达式同样都是一个指针 他指向函数实例。因为都是指针所有没有重载 只能覆盖，因为一个指针无法同时指向两个函数对象。
函数声明也就是 `function num(){}`在函数环境搭建过程中会被提升到源码顶部 而函数表达式 `var num = function(){}` 也会被提升 但是只有num变量被提升。这个num里面没有对于函数的引用，也就是说指向的是undefined。

1.  函数支持函数作为参数 也支持吧函数作为返回值返回

比如下方这个例子 sort函数他需要的是什么 一个函数，而且是一个函数的指针，并不是需要运行他。所以你不能加括号和参数。但是你又必须有参数，通过这个参数来比较两个项里面的某个属性。所以我们可以进行函数嵌套 且外围函数又返回一个函数。在例子中，实质上的sort他传进去的是return后面的函数 而且这个函数里面页已经进行了相应的属性提取。并且sort不光需要函数指针，也无法使用有三个参数的函数。
```
function compare(prototypeName,obj1,obj2) {
        var obj1Pro = obj1[prototypeName];
        var obj2Pro = obj2[prototypeName];

        if(obj1Pro < obj2Pro){
            return -1;
        }else if(obj1Pro > obj2Pro){
            return 1;
        } else {
            return 0 ;
        }

}

var objArray = [{name:"zhang",age:69},{name:"wang",age:67}]

objArray.sort(compare("name"));//别忘了使用引号包裹参数

console.log(objArray[0]);
```


### 函数内部属性

我们会用到一些递归的情况 但是如果在函数内部直接调用自己的函数名，这样会造成耦合的问题，在我们更改函数名字的时候，这个递归函数就无法生效了。那么其实在前面我们就讲到了js函数中有两个特殊的对象 一个是this 一个是arguments 分别指向了 执行方法的对象 和 参数数组。而arguments里面就包含了一个特殊的属性 它指向了拥有这个数组的函数对象。 我们可以通过这个属性来进行递归

```
var i=0
function fact(num) {
    console.log(i);
    arguments.callee(i);
    //无线递归
}

fact(i);
```

就算你把这个函数复制一下指针，照样可以在副本函数中运行。


1.  还有一个属性是this,与Java等语言类似 他指向的是当前函数的环境对象 如果这个函数在全局中被调用 那么他的环境对象就是window对象。
2. JS还为我们定义了caller属性 他指向了调用当前函数的函数的引用 如果在全局作用域调用这个属性 那么他的返回会是null。

```
function fact(num) {
    fact2();
}

function fact2() {
    fact3();
}

function fact3() {
    console.log(arguments.callee.caller);
}

fact();
//在这种多次调用的情况下 会指向上一个调用他的函数的引用 不会是返回第一层fact的引用，或者你可以再加一个,caller来调用更上一级的函数引用
```







