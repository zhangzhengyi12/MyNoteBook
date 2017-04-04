# **理解对象**##
---
## **属性类型**
  JavaScript中有两种属性类型 分别是 数据属性和访问器属性 
### **数据属性**
  数据属性具有四个特征值
	   
	   [Configurble] 表示能否修改特性或者是通过delete重新定义属性 默认为true
	   [Enumerable] 表示能否通过forin遍历循环返回属性
	   [Writable] 表示能否修改这个属性值 默认为true
	   [Value] 包含这个属性的数据值

  如果要修改属性的特征值 那么可以通过以下的方法

```
var person = {
    name:"zhang"
}

Object.defineProperty(person,"name",{
    writable:false,
    value:"zhao" //小写
});

person.name = "wang";
console.log(person.name)
```
 但要注意的是如果你修改了属性的Configurble 你将无法再修改这个属性的特性。
### **访问器属性**
 访问器属性不包含数据值，它们包含一块getter和setter函数，在读取和调用时会调用这些函数，从而负责返回 和 处理。访问器有下面四个特性。

访问器有下面四个特性。
> [Configurble] 表示能否修改特性或者是通过delete重新定义属性 默认为true
	   [Enumerable] 表示能否通过forin遍历循环返回属性
	   [Get] 在读取时调用的函数 ，默认为undefined
	   [Set] 在写入时调用的函数， 默认为undefined
	


 

```
var person = {
    name:"zhang",
    _year:2004,
    edition:1
}

Object.defineProperty(person,"year",{
    get: function () {
        return this._year;
    },
    set: function (newValue) {
        if(newValue > 2004){
            this._year = newValue; //这里的设置新值 设置的是_year的值
            this.edition += newValue - 2004;//每次更新同时去设置版本
        }
    }
});
//这里的year就是一个典型的**访问器属性**，他不保存实际的value 但是有拥有getset方法，他只能通过这种方法去定义。而数据函数则是修改特性需要调用，生成是不需要的
person.year = 2005; //其实是调用了year属性的set方法 然后实质上设置了_year的值 并且跟新版本
alert(person.year); //调用year的get方法 返回了_year的值
alert(person.edition);
//所以实质上 _year 和 year 是两个完全不同的值.这是访问器属性的常见用法 get set一个属性 影响其他属性。
```


### **定义多个属性**
 在一些情况下我们需要为一个对象的多个属性设置特性，当然js也为我们提供了方便的方法 `Object.defineProperties()`.它提供了两个参数，第一个参数代表了你要修改的对象，第二个参数也是一个对象 其中的属性和第一个对象中需要修改的一一对应


```
var cat = {};

Object.defineProperties(cat,{
    _year:{
        writable:true,
        value:2004
    },

    edtions:{
        wirtable:true,
        value:1
    },

    year:{
        get: function () {
            return this._year;
        },
        set: function (newValue) {
            if(newValue > 2004){
                this._year = newValue; //这里的设置新值 设置的是_year的值
                this.edition += newValue - 2004;//每次更新同时去设置版本
            }
        }
    }

});

alert(cat.year);
alert(cat.edtions);
```

### **读取属性的特性**
 既然有定义方法，自然有读取方法 `Object.getOwnPropertyDescriptor()` <i>获得自身特性描述符</i>，返回一个具有四个属性的对象，读取不同对象的时候,他的属性也不一样。具体看上面的列表。

## **创建对象**
 虽然用Obj构造函数或对象字面量可以创建单个对象，但是当你需要创建大量对象的时候，就会非常的麻烦并且产生大量重复的代码。

### **工厂模式**
 用函数来封装特定接口创建对象，但这种方法无法知道他属于什么类型的对象
```
function createPerson(name,age,job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function () {
        alert(this.name);
    }
    return o;
}

var tom = createPerson("tom",19,"doctor");
```


####**构造函数模式**
 我们都知道，我们可以通过构造函数快捷构造特定的对象 比如Object和Array，此外，我们可以创建自己的构造函数。

```
function Person(name,age,job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function () {
        alert(name);
    }
}

var tom = new Person("tom",90,"doctor");
var jerry = new Person("jerry",18,"teacher");
```

 以上是一个典型的构造函数 我们可以和工厂模式做一个简单的对比
 - 没有显示的创建对象
 - 直接通过this来给对象添加属性
 - 没有通过return把对象返回
 - 构造函数的首字母应大写

既然生成了构造函数，我们可以通过new关键字来调用这个构造函数，当new的时候 其实发现了以下步骤
- 创建了一个新的对象
-  把这个构造函数的作用域连接到这个新生成的对象 也就是说 这个函数内的this 指向新对象
-  执行构造函数的代码（添加属性和方法）
-  返回构造完毕的对象

在上方的例子中tom和jerry分别保存了一个**Person的一个实例** 他们其实都有一个constructor(构造函数)属性，该属性指向了他们的**构造函数Person**.
但实质上这种方法可能并不是非常的保险，我们可以使用更加可靠的`instanceof`操作符 就像下面这样

```
console.log(tom instanceof Person);//true
console.log(tom instanceof Object);//true
console.log(jerry instanceof Person);//true
console.log(jerry instanceof Object);//true
//所有对象都继承自Object
```

**构造函数模式相比工厂模式最大的优势在于他提供了一个访问对象类型的方法**

 构造函数也是函数,这意味这个函数能够被new的方式调用，同样可以被当成普通的方式来调用，在普通情况下 他的所有特征会和普通函数表现的完全一样。以下是一个简单的例子。

```
Person("drivd",66,"ko");
console.log(window.name);//drivd

var cat = {};
Person.call(cat,"cat",10,"catch mourse");
console.log(cat.name);
//或者使用以下的方法 在对象内把函数置入到对象之中

cat.fnc = Person;

cat.fnc("wang",15,22);
```
 首先是一个在全局内调用这个Person函数的情况下 Person函数正常执行 这里的**this指向了**运行他的作用域对象 也就是**window对象**。所以window对象的name就被设置成了drivd.
 第二是在其他对象的作用域情况下，通过call方法来增加作用域 进入函数时**this指向为cat对象**。

**构造函数的问题**

```
this.sayName = function () {
        alert(name);
    }
```
 在创建实例时，都会调用构造函数内的方法，但是每次在构造方法的时候，它实质上是**创建一个新的函数**（也就是一个对象）**然后让sayName指向这个new出来的函数**，不同的Person实例中的方法虽然函数名和做的事情完全相同，但他们并不指向同一个函数对象。所以当你比较（==）两个相同类型不同实例的相同方法时，**他们并不相同**。
 
 所以我们需要一种能够把一些函数封装 然后在构造函数内部可以直接引用，这样就可以让不通实例的相同方法的指针指向同一个函数。也许把方法写在全局变量之中，在内部调用是种方法。他达到了两者方法里面存放一个指针 并且指向了同一个函数，但这种方法的**封装性**实在差劲，并且并不是很好管理（如果需要非常多函数的情况下）。

### **原型模式**
我们所创建的所有对象都拥有着一个属性`prototype`，他包含了一个指针，指向了这个对象的原型对象，而这个原型对象的作用是提供一种类型的不同实例所共享的方法和属性。让我们接下来深入了解原型的本质。

**理解原型的本质**
 首先当我们创建的一个新的函数，马上按特定规则创造一个当前函数的`prototype`属性，这个属性指向了函数的原型对象。而原型对象也会生成`constructor`属性，他指向了prototype属性所在的函数。而在没有实例生成的时候 这个原型属性里其实除了`constructor`没有任何东西

 第二步发生在实例调用构造函数的过程中，创建的实例时，实例内部也会产生一个prototype属性，然后内部产生一个指针，他指向了构造函数所指向的原型对象，（尽管实例的这个属性对脚本不可见，不可直接访问）。要注意的是，这个连接存在于实例与原型对象之间，这连接与构造函数并无太大关系。

 对于实例可以用`Person.prototype.isPrototypeOf(Person1)` 的方法来查看是否和某个构造函数的原型建立了链接， 如果两者确实有链接，返回true,

 在EMCAScript中新增加了一个方法 它能让我们快速的返回一个对象的原型对象 `Object.getPrototypeOf(person1)`

 当实例的属性被我们读取时，他首先访问自己本身有没有这个属性，如果没有的情况下，就继续查询他的原型，如果在原型中找到了这个属性，他的值就会被返回。

 如果在某个实例的自身和他的原型中有同名的属性，那么自身的属性将会屏蔽原型的属性。所以即使删除这个属性，修改的也永远是其自身的属性，他不会修改原型的属性，我们可以通过删除对象自身的某个属性，来让取消对原型属性的屏蔽

 `person1.hasOwnProperty("name")`继承自Object 他会检查参数是否是对象其中的一个属性，他不会去检查对象的原型。


<br>

```
 function Person() {

}

Person.prototype.name = "wang";
Person.prototype.age = 20;
Person.prototype.job = "T";
Person.prototype.sayName = function () {
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name")); //false
person1.name = "zhang";
person1.sayName();//这里的sayName会查找到原型并且原型方法内的this指向为person1
alert(person1.hasOwnProperty("name")); //true
```

**原型与in操作符**
 其实在之前我们就在forin中使用了in操作符 其实在检测某个属性是否属于某个对象的时候，这个操作符同样可以使用，他的特性与`hasOwnProperty()`不同，如果该属性属于该对象的原型对象，同样会返回true，如果原型不曾拥有，返回false。
 
 现在我们有了检测属性的两种方法 1 检测属性是否属于对象本身 2 检测属性对象是否属于对象本身或原型。那么我们缺少第三种。检测对象是否属于原型。其实实现的方法很简单 无法就是达成了两个条件 **第一** hasOwnProperty()返回false，该属性在本身不存在，**第二** in操作符返回true，在原型中存在。所以以下就是一个简单方法

```
function hasPrototypeProperty(obj,name) {
    return !obj.hasOwnProperty(name) && (name in obj);//如果条件都达成 返回true
}
```
 在我们通过forin循环遍历属性的时候，原型中的属性也会被遍历出来，（除非原型中属性的特效标记为无法遍历），但是这一点在IE8以一下版本中有一个BUG，**屏蔽不可枚举属性的实例属性不会出现在forin循环之中**，也就是说，如果原型中的某个方法屏蔽in循环，然后在实例自身中覆盖同名方法，连这个方法自身也会屏蔽In.无法发现。



 要取得一个对象中所有可以通过forin循环的属性，可以通过`Object.keys()`方法，他接受一个对象参数，返回一个包含所有可forin遍历的属性标识符字符串数组。他的顺序是forin中的出场顺序

 如果想取得一个对象内的所有实例属性 可以使用`Object.getOwnPropertyNames(Person)`他的参数是一个对象，他的返回值会包含非常多的属性，比如caller argunments coustrusta
 当然上方的返回值都只会是自身的属性，不会去查找他们的原型。

**更简单的原型语法**
在很多时候重复的调用.prototype属性的封装性并不是很好，我们可以通过创建一个新对象并设置的方法来

```
function Person() {
}

Person.prototype = {
    name:"null",
    age:0,
    job:"job"
    sayName:function () {
        alert(this.name);
    }
}
```

这个方法 唯一问题在于 Person的原型的constructor不再指向Person构造函数，因为我们实质上创建了一个新的对象并把构造函数的prototype指针指向了他，之前默认创建的prototype对象已经被覆盖，新对象里也没有constructor这个属性，办法就是在新的原型对象内重新指定一下constructor
 
 **原型的动态性**
 原型的动态性主要体现在增加和重定向两个方面


```
function Person() {
}

var person1 = new Person();

Person.prototype.name = "person1";

alert(person1.name);
```

 上面的例子首先创建一个person的实例person1 然后给person的原型添加的name属性。即使增加属性在创建实例之后，但依旧可以正常访问原型中的属性。这是因为获取值只是查询，他在自身内没找到就回到原型中去找，找到了，返回。

```
Person.prototype = {
    name:"name"
}

alert(person1.name);//undefined 因为他访问的仍旧是创建时绑定的原型对象。 
```

 但是在这种情况下，就会发生无法访问的情况。因为实例原型的连接出现在构造函数的那一刻，他被绑定成原来的原型对象，而后来的原型对象被重新更改成另一个新创建的对象，也就是说，这个实例对象的原型仍旧是刚开始创建的那个原型对象。 和上面的例子对比，一个是在新的原型对象内创建属性，而另一个则是在原本的原型中去添加属性。

**原生对象的原型**
不仅仅是自创建的对象，就连很多原生对象（比如Object Array String）都使用了原型的方式来封装方法，通过原生对象的原型不仅可以获取原生方法的应用，也可以为原生对象添加新的方法。

```
String.prototype.startsWith = function (tex) {
    return this.indexOf(tex) == 0;
}

var text = "Hello World";

alert(text.startsWith("World"));
```
以上就为基本包装类的原型添加了一个方法

**原型对象的问题**
原生模式其实也存在一些问题，首先他没有传参数然后初始化的环节，也就是说原型中的值默认值都相同而且会被共享。假设我们两个实例的原型相同，且内部有一个数组，被两个共享，那么我们操作其中一个实例的该数组，会改变到另外一个实例的该数组。（如果重新创建 则创建在对象内部 和原型无关）

**组合使用构造函数和原型模式**
构造函数用于定义实例属性，而原型模式用来定义需要方法和共享的属性

```
function Person(name,age) {
    this.name = name;
    this.age = age;
    this.friends = ["tom","jerry"]
}

Person.prototype = {
    sayName:function () {
        alert(this.name);
    }
}

var person1 = new Person("wang",15);
var person2 = new Person("zhang",12);
```

**动态原型模式**
如果把分离的原型和构造函数可能会造成一些困惑，可以把初始化原型和构造函数封装在一起

```
function Person(name,age) {
    this.name = name;
    this.age = age;
    this.friends = ["tom","jerry"]
    if(this.sayName != "function"){
        Person.prototype.sayName = function () {
            alert(this.name);
        }//不能调用对象字面量的方式去重写原型,只能增加方法，不然第一个创建的对象会被隔绝
    }
}
```
 以上的原型初始化只会执行一次，在第一次调用构造函数生成实例时，this指向的是new出来的对象，这个对象有原型，但这个原型内部没有sayName函数。所以会执行一次初始化，而第二次开始new出来的对象原型中已经有sayName函数，以后就不会调用了，

**寄生构造函数模式**
 这种模式非常类似与工程模式，但是他使用了new 关键字来创建对象，并在最后主动return包装后的对象，但这种方法仍旧无法通过

```
function Person1(name,age) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.sayName = function () {
        alert(this.name);
    }
    return 0;
}

var person10 = new Person1("wang",15);
```

```
function SpecialArray() {
    var values = new Array;
    //创建数组
    values.push.apply(values,arguments); //把接收到的参数数组传入这个数组之中
    //这里使用push加上apply可以方便吧原参数数组的项目挨个喂入新数组
    values.toStringColor = function () {
        return this.join("|");
    }

    return values; //返回数组
}

var color = new SpecialArray("blue","red","greem");
alert(color.toStringColor());
```
在我无法修改一些原生对象的构造函数的时候，我们可以使用这种寄生构造函数来进行一个原生类型的创建。

**稳妥对象**
稳妥对象就是那些属性非公共，而且其内部方法没有使用this，并且不使用new关键字来生成对象,

### **继承**####
 在其他面对对象的语言之中，往往有两种继承，一种为方法签名的继承，一种为实现继承，方法的签名保存了函数的名称与参数顺序和类型，他可让函数实现重载，但是在JS之中没有签名。所以JS能实现的继承只有一种，也就是实现继承，而实现继承的主要实现依靠的就是原型链。

**原型链**
原型链的实现其实非常简单，让一个实例的原型对象变成一个实例。

```
function SuperType() {
    this.value = true;//这里是this为new出来的super实例
}

SuperType.prototype.getSuperVlaue = function () {
    return this.value;
}

function SubType() {

}

SubType.prototype = new SuperType();

var instance = new SuperType();
alert(instance.getSuperVlaue()); //true
//通过原型链向上查找方法，找到之后return this.value 接着找到了实例的属性
```

实现的本质是重写原型对象，要注意的是 instance.constructor所指向的是SuperType构造函数，因为SubType的基础原型被一个实例所覆盖，这个实例内部没有constructor属性，而这个实例的原型的constructor又指向了Super构造函数。

**默认的原型**
所有的对象都默认继承自Object 所以valueOf toString都是继承自Object原型的方法。要注意的是SuperType.prototype并不是直接等于Object的原型，在SuperType.prototype的内部有属性和方法，并且里面有个指针，指向了Object的原型。

**确定原型与实例的关系**
  可以通过两种方式来确定原型与实例之间的关系

```
alert(instance instanceof Object); //true
//测试实例与其原型链中出现过的构造函数
alert(Object.prototype.isPrototypeOf(instance)); //true
//原型链中出现过的原型的实例
```

**谨慎的定义方法**
给原型添加方法的代码一定要放在替换原型的语句之中，如果你先添加方法 之后替换原型，那么那个方法会被抛弃掉，因为那个方法存在于原来的原型之中，他现在已经覆盖了。
第二不要通过对象字面量的方式给原型添加属性和方法，这样等同于重新生成一个对象来作为原型

**原型链的问题**
第一 原型链中的数据会被共享 很容易导致一个实例修改之后，另一个实例的该属性同样被修改

```
function SuperType() {
    this.color = ["red","blue","black"];
}

function SubType() {

}

SubType.prototype = new SuperType();

var person1 = new SubType();
person1.color.push("GO"); //这里可以分解成两步 第一是找到person1.color 然后执行push 如果这里是简单等于，那么就会直接为person1添加一个数组的属性。

var person2 = new SubType();
alert(person2.color);//同样被修改了，两者修改的都是位于SuperType实例中的color数组
```
第二 你无法在子构造函数中去给父构造函数传递参数，

**借用构造函数**
 这种方法和原型链没什么关系 基本思想就是在一个构造函数内通过call或者apply来唤醒另外一个函数，与其说实现了继承，我觉得更加想两个函数的作用相加。

```
function Supertype() {
    this.color = ["1","2","3"];
}

function SubType() {
    //调用了Super构造函数并初始化this为子对象
    Supertype.call(this);
}

var person1 =  new SubType();
```
这种情况下color其实就保存在person1实例的内部。
让我们看如何在借用构造函数中传递参数

```
function Supertype(name) {
    this.name = name;
}

function SubType() {
    //调用了Super构造函数并初始化this为子对象
    Supertype.call(this,"wang");
    this.age = 99;
}

var person =  new SubType();
alert(person.name);
alert(person.age);
```
但是单纯使用这种方式是绝对无法实现方法的复用的，方法会在每个对象的内部生成一个相同的副本，做的事一样，但是其调用的方法却完全不在一个地方。所以这种方式也不太会被使用

### **组合继承**
 这种方式的核心就是组合了借用构造函数和原型， 如果这个数组想独吃，和共享 就用借用构造函数实现 和内部添加属性基本一样，而那些想要被共享的方法则可以用原型链的方式来实现。

```
function SuperType(name) {
    this.name = name;
    this.color = ["1","2","3"];
}
SuperType.prototype.sayName = function () {
    alert(this.name);
}

function SubType(name,age) {
    //调用了Super构造函数并初始化this为子对象
    SuperType.call(this,name);
    this.age = age;
}

SubType.prototype = new SuperType();

SubType.prototype.sayAge = function () {
    alert(this.age);
}//注意先后顺序

SubType.prototype.constructor = SubType; //重定向一下构造函数 因为之前的构造函数被实例所覆盖了


var instance1 = new SubType("zhang",16);
instance1.color.push("yellow");
alert(instance1.color);
instance1.sayName();
instance1.sayAge();

var instance2 = new SubType("wang",10);
alert(instance2.color);//不会受到任何影响 因为这个数组实质上保存在实例自己的内部
instance2.sayAge();
instance2.sayName();
```

并且这种方式可以被 `instanceof` 和 `isPrototypeOf()`所识别
其实这种方法在instance实例的原型对象内是包含有初始化的两个值的(可以通过__proto__来看到)，只不过这两个值会被子实例内的属性覆盖，所以没啥问题

**原型式继承**
原型的继承的核心在于 把一个对象设置成新实例的原型，最后把这个实例返回

```
var Person = {
    name:"l"
}

function Object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

var anotherPerson = new Object(Person);
var anotherPerson2 = new Object(Person);
//两者指向同一个实例
```
在ECMAScript这种有点像反射的构造方法被规范化 加上Object的方法智商

```
var Person = {
    name:"l"
}



var anotherPerson = Object.create(Person);

```
Object.creat方法的第二个参数与Object.definePrototies相同
```
var person  = {
    name:"lol",
    friends:["2"]
}

var anotherPerson = Object.create(person,{
    name:{
        value:"go"
    }
});

anotherPerson.friends.push("3");
//猜测是覆盖了传进去实例的同名属性
//实验证明这个name是保存在new出来的对象之中，和原型所指向的person无关系
alert(anotherPerson.name); //go
alert(person.name); //name
alert(person.friends);
alert(anotherPerson.hasOwnProperty("name"));
```

**寄生式继承**
他的核心思路就是创建一个只用于封装继承过程的一个函数，该函数在内部以增强的方式来增强对象，最好把这个对象返回，就像把一个新手送去一个刷等级的地方，刷完再把增强后的选手送回来。

```
var Person = {
    name:"l"
}

function createAnother(original) {
    var clone = Object(original);
    clone.sayHi = function () {
        alert("Hi");
    }
    return clone;
}

var another = createAnother(Person);
//他的原理就是传入一个对象 然后进行一步步的增强 最后返回增强后的版本
```
这种模式的缺点就是无法达到函数的复用，每个实例中的对象都是一个独立的副本。
###**寄生组合式继承**###
组合继承的不足在于会调用两次父类的构造函数 第一次调用在 `SubType.prototype = new SuperType()`第二次调用在借用另外一个构造函数的时候`SuperType.call(this)`

```
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function inheritPrototype(subtype,supertype) {
    // var prototype = supertype.prototype; //创建一个对象 可以认为是超类原型的副本
    var prototype = object(supertype.prototype); //忽略了一个非常重要的细节 这里调用的是小写的object 所以这里使用了原型式继承，这样给子类原型添加方法属性的时候其实都添加在一个空的对象之内，而这个空的对象原型又和超类原型相同
    prototype.constructor = subtype;
    subtype.prototype = prototype;
}

function SuperType(name) {
    this.name = name;
    this.color = ["1","2","3"];
}

SuperType.prototype.sayName = function () {
    alert(this.name);
}

function SubType(name,age) {
    SuperType.call(this,name);
    this.age = age;
}

inheritPrototype(SubType,SuperType); //

var i = new SubType("wang",15);

i.__proto__.go = 20;
alert(i.go);

var j = new SuperType("zang");
alert(j.go);//undefined 上面的添加 添加在一个通过原型继承的空的对象之内 j是无法访问到的
```
让我们看看一共使用了几种技术
 **构造函数** 实现通过操作符或方法查询类
 **原型模式**实现了方法继承
**借用构造函数** 解决了组合构造模式有些需要属性私有的问题
 但到目前为止无法解决超类构造函数会被调用两次的问题 所以我们加入两种技术
 **原型式继承**+**寄生式继承** 他实现了只调用一次超类构造函数，且不会出现原型污染，对子类原型进行属性和方法的添加不会影响超类原型。
 ![这里写图片描述](http://img.blog.csdn.net/20170314205227845?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4MjYyNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 寄生式继承负责调用和创建对象，重定向构造函数，然后实现真正的连接，而原型式继承的作用是创建空的对象，并且这个对象原型指向超类对象。