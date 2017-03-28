# 函数表达式#
---
> 定义函数的方法有两种 分别是函数声明和函数表达式。
> **函数声明**的其中一个重要特征就是函数提升，意思是在执行具体代码之前，函数会被预先读取，所以执行函数的语句可以放到函数声明的前面
> 函数表达式无法做到先执行后声明，尽管也有提升过程，但提升的只有标识符，没有具体指向的函数。

<br>
> 这两者的差别还不止这么点，来看下面的例子

```
if(condition){
    function sayHi() {
        alert("Hi!");
    }
} else {
    function sayHi() {
        alert("Yo!");
    }
} //报错 在JS中属于无效语法 且不同浏览器解决方法不一
```

> 但如果是函数表达式这么做 是被允许的

```
var sayHi;
if(condition){
    sayHi=  function sayHi() {
        alert("Hi!");
    }
} else {
    sayHi= function sayHi() {
        alert("Yo!");
    }
}
```
> 函数也已是匿名的 你可以把一个函数作为返回值返回。

## 递归##
> 和其他的语言相同，递归是一个函数通过自身的名字来调用自身的过程。


这是一个简单的递归乘阶函数
```
function factorial(num) {
    if(num <= 1){
        return 1;
    } else {
        return num * factorial(num-1)
    }
}

alert(factorial(10));
//n的阶乘表示1乘以2乘以3乘以4……一直乘到n 最低层为num=2 * 1 到达最底层之后开始返回数值 一直return
```
但如果我们对执行的过程稍加改动，就会很容易出错

```
var anotherFactorial = factorial; //执行到这边函数有两个入口
factorial = null;//清除一个入口
alert(anotherFactorial(5));//出错 因为执行到第一次递归调用他不知道factorial是谁了 他只认识anotherFactorial
```
所以我们可以使用`arguments.callee`来递归调用自身，因为`arguments.callee`他指向的是正在执行的函数的指针，但这种方法在严格模式内无效，但我们还有另一种方法，命名函数表达式

```
var factorial = (function f(num) {
    if(num <= 1){
        return 1;
    } else {
        return num * f(num -1);
    }
});//把函数声明外面的括号去掉就会好理解很多，第一他被赋值给factorial 并且他自己也有名字 f 。
```

## **闭包**##
> 闭包是指有权访问另一个函数作用域中变量的**函数**。
> 创建闭包的常见方式就在一个函数内部创建另一个函数。

```
function com(prototypeName) {
    return function (object1,object2) {
        var value1 = object1[prototypeName];
        var value2 = object2[prototypeName];

        if(value1 < value2){
            return -1;
        }else if(value1 > value2){
            return 1;
        }else {
            return 0;
        }
    }
}
```
在上面的例子中 外部函数包含了一个内部函数，而内部函数调用了外部函数的一个变量`prototypeName`即使这个函数被返回 但依旧可以访问外部的变量。而之所以能够做到，是因为内部的函数的作用域链包含外部函数的作用域，让我们继续往下看作用域链的创建流程。

> 当一个函数被调用的时候，首先 **创建一个执行环境，和相应的作用域链**，然后**依赖arguments和其他命名参数的值创建该函数的活动对象（这个对象内可能含有实参和形参等一些参数）**
> 在函数的作用域链中，外部的函数活动对象处于第二位，外部的函数的外部函数活动对象处于第三位，直到最外围的全局作用域，（第一位是自身的活动对象）

```
     function c(value1,value2) {
        if(value1 < value2){
            return -1;
        }else if(value1 > value2){
            return 1;
        }else {
            return 0;
        }
     }

    var a = c(5,10);
```
在上面的函数中，当在调用函数c的时候，创建一个包含了value1,value2,arguments对象的活动对象，然后作用域链的第二位是被包含有变量a和c函数的全局变量对象。
> 后台的执行环境都有一个表示变量的对象——变量对象。在全局变量之中，这个变量对象会持续存在，而函数内部的变量对象在函数退出之后就会被立马删除。在创建c函数的时候，首先创建一个预先包含全局变量对象的作用域链，这个作用域链被保存在内部的`[scope]`属性中。
> 接下来开始调用这个函数，进行环境准备，通过复制函数内部的`[scope]`构建起执行环境的作用域链。
> 此后，创建了一个活动对象（看作是一个变量对象），被推入到执行环境作用域链的前端，
> 至此 这个函数的作用域链有两个变量对象，本地活动对象和全局变量对象。
> 当在函数内访问某个变量，将会通过作用域链来查询变量。当函数执行完毕的时候，这个作用域对象会被销毁，但是在**闭包**之中，情况有所不同。

<br>
> 在另一个函数内部定义的函数会把外部函数的活动对象添加到作用域之中
> 因此上方的com函数内部的匿名函数会把外部函数的活动对象添加到自身的作用域链之中
> 更重要的一点是，即使外部函数结束，这个在匿名函数作用域链之中的活动对象依旧不会被删除，因为这个匿名函数的作用域链依旧在引用这个活动对象，只有当匿名函数也被销毁，这个活动对象才会被销毁。
> 你可以把存放匿名函数的变量设置为null 这样就会通知垃圾回收器回收

### **闭包与变量**###

```
function creatFunction() {
    var array = new Array();

    for(var i=0;i<10; i++){
        array[i] = function () {
            return i;
        }
    }
    return array;
}

var j = creatFunction();

console.log(j[1]());
```
在上面的例子中下标0到9里面的方法运行时都会返回10 这些方法的作用域链中都指向了 creatFunction函数的活动对象，他的最后一次修改在creatFunction函数执行完毕之时，那个瞬间里面所包含的i为10
为了输出符合我们预期的结果 可以像下面这样做

```
function creatFunctions() {
    var result = new Array();

    for(var i=0; i<10; i++){
        result[i] = function (num) {
            return function () {
                return num;
            };
        }(i);//这种函数后面接个括号代表立即执行函数，当JS读到这里的时候会立即执行上分定义的函数
    }
    return result;//由于立即执行 在数组内部保存的实质上是一个匿名函数 而且这个匿名函数传进去的num值为i 
}
```

这种方法的重点在于通过函数内部嵌套return的匿名函数，随后通过立即执行调用传入I的参数并返回，别忘了基本类型的参数是通过值传递的，等于每个方法内部的Num都是在内部独立的一部分。
 
### **关于this对象**
this对象在运行时基于函数的执行环境，如果函数被当成某个对象的方法调用，this指向那个对象。但是匿名函数的执行是全局的(除了call,apply以外)。

```
var name = "window";

var object = {
    name:"obj",
    getNameFunc:function () {
        return function () {
            return this.name;
        }
    }

}

alert(object.getNameFunc()()); //window
//把返回的匿名函数直接执行
//即使删除了全局变量name 仍然无法访问object的变量，因为this不是普通的变量 他是一个具体的指向。
```
 > 由于匿名函数是全局性的,所以他搜索this.name直接搜索的全局变量区域，this已经被具体化了，永远无法访问外部函数的作用域，不过把外部作用域的this保存在闭包可以访问的变量里，就可以访问了
 
 

```
var object = {
    name:"obj",
    getNameFunc:function () {
        var that = this;
        return function () {
            return that.name;
        }
    }

}

console.log(object.getNameFunc()()); //obj
```
> 上方的代码里的that是object的应用，在执行方法时会被放到getNameFunc的活动对象里面，因为that不是关键词是一个变量，所以他会按照自身的活动对象 > 上级活动对象，这样来找到来自object的引用，不光是this,argument也可用这样的方式来访问上级作用域里的引用。
> this的值在不同的语法下可能会发生非常细微的变化。

### **内存泄漏**
> 闭包在IE内可能会发生内存泄漏（无法回收）的问题

```
 var element = document.getElementById("ele");
 var id = element.id;

 element.onclick = function () {
     alert(id);
 }

 element = null;
 //手动解除引用来释放内存
```

## **模仿块级作用域**
在JS之中是没有块级作用域的，这意味在块内部声明的变量，在外部依旧可以找到。
而JS不会检查你是否多次声明了一个同名变量，遇到这种情况，他只会对后续的声明视而不见（但初始化会被执行，比如赋值），我们可以使用匿名函数来创建私有作用域。

```
(function () {
    //私有作用域
})();
```
 > js不允许函数声明直接带括号没名字，而函数表达式是可以的，给一个函数声明加上括号就会让函数声明变成函数表达式，后面在家一个（）就代表立即执行该函数。
 > 这种技术经常被用于函数外部，从而保证全局作用域少受污染。

## **私有变量**
> 虽然在JS中没有私有成员这一概念，但却有私有变量这一概念，所有在函数内部定义的变量都可以认为是私有变量，因为无法在函数的外部访问这些变量。私有变量包括函数的参数，局部变量和在函数内部定义的函数。

```
function add(num1,num2) {
    var num = num1+num2;
    return num;
}
```
> 在这个函数内部一个有三个私有变量 分别是num1,num2，num 而在外部是无法访问的
> 如果在函数的内部创建一个闭包，那么就可以通过闭包的作用域来访问这些变量。
> 我们把有权访问私有变量的公有方法称之为特权方法，特权方法的创建有两种
> 第一是在构造函数内定义特权方法

```
function MyObject() {
    var i = 10;
    function privateFunctin() {
        return i;
    }//私有变量和函数

    this.publicMethod = function () {
        i++;
        return privateFunctin;
    }//特权方法

}//这里的闭包应该按照返回的方法来看
```
> 利用私有和特权成员，可以让我们隐藏那些不应该被直接修改的数据

```
function Person(name) {
    //传参数 等于var name = name;
    this.getName = function () {
        return name;
    }

    this.setName = function (value) {
        name = value;
    }
    //这这里，内部的变量通过闭包来保存 也就是被保存在Person实例的闭包的作用域链之中。
}

var person = new Person("666");
alert(person.name); //错误 这个对象内没有这个属性。
alert(person.getName()); //666
```
> 但这种方式也有缺点，也就是所有实例都拥有自己独立的公共方法，每次创建对象都会创建一组同样的新方法。而静态私有变量可以解决这个问题。


### **静态私有变量**
通过在私有作用域中定义私有变量或函数，同样也可以创建特权方法，其基本如下所示。

```
(function () {
   var i = 10;

   function provateFunction() {
       return false;
   }
   
   MyObject = function () {
       
   }//构造函数 创建一个全局对象
   
   MyObject.prototype.publicMethod = function () {
       return provateFunction();
   }
})()
```

```
(function () {
    var name = "55";

    Person = function (value) {
        name = value;
    };

    Person.prototype.getName = function () {
        return name;
    }

    Person.prototype.setName = function (value) {
        name = value;
    }
})();
//这样方法会返回一个全局变量Person 并且这个Person内部有一个构造函数，并且这个Person还引用着这个函数作用域的方法，但要注意的是这种方法的所有实例操作的都是同一个函数
 var person1 = new Person("666");
 alert(person1.getName()); //666

 var person2 = new Person("777");
 alert(person2.getName());
 alert(person1.getName());
```

> **这个模式与在构造函数中定义特权方法的主要区别在于私有变量和函数是由所有实例共享的，也就是说在一个实例上调用setName会影响所有实例，而调用setName或新建一个实例都会赋予name一个新的属性**关于为什么普通函数中的闭包会保存一个对象，里面的变量是最后一次，而构造函数里的闭包会分别创建，原因肯定是因为这两种情况，一种是指向了相同的一个活动对象，一种是指向了不同的活动对象，而构造函数每次被调用应该会创建一个新的活动对象。且创建闭包的时候，闭包是独立的引用了新创建的活动对象。

### **模块模式**
> 前面的模式是用于为自定义类型创建私有变量和特权方法的。而模块模式是为单例创建私有变量和特权方法。单例指的就是只有一个实例的对象。JS使用对象字面的方式来创建单例对象。

```
var singleton = {
    name:value,
    method:function () {
        //方法
    }
};
```

```
var singleton = function () {
    //创建私有变量和私有函数
    var privateVariable = 10;

    function privateFunction() {
        return false;
    }
    //特权
    return {
        publicProperty:true,

        publicMethod:function () {
            privateVariable++;
            return privateFunction();//这里是把运行pri函数的结果return
        }
    }
}(); //立即执行

alert(singleton.publicMethod());
```
> 这个匿名函数会返回一个对象，并且这个对象由于内部方法的闭包可以访问函数内部的私有变量和函数。这种模式在需要对单例进行某些初始化，同时又需要维护其私有变量时是非常有用的。

```
var application = function () {
    var compenents = new Array();

    compenents.push(new BaseComponent());

    return{
        getComentCount : function () {
            return compenents.length;
        },
        registerComponent : function (component) {
            if(typeof component == "object"){
                compenents.push(component);
            }
        }
    }
}();
```
> 这是一种经常使用的大致模式，首先对一些属性进行初始化，接着返回一个对象，这个对象有两个方法，通过这些方法可以操作那些基础属性的值，在这里所谓的单例的实例其实就return的那个通过对象字面量创建的值，这种方式操作一些重要的属性会比较的安全，不是通过简单的属性去修改。


### **增强的模块模式**
> 有人进一步改进了模块模式，即在返回对象之前加入对其增强的代码，这种增强的模块模式适合必须是某种类型的实例，同时还必须添加某些属性和方法对其增强的情况，

```
var application = function () {
    var compenents = new Array();

    compenents.push(new BaseComponent());
    //创建副本
    var app = new BaseComponent();
    //创建公共接口
    app.getComponentCount = function () {
        return compenents.length;
    }
    app.registerComponent = function (component) {
        if(typeof component == "object"){
            compenents.push(component);
        }
    }
    // 返回这个实例
    return app;
}();//最后将这个实例赋给全局变量application
```
 > 这种方法的最大区别在于通过new一个对象出来，然后添加特权方法，这样就可以创建一个既定类型的实例。


