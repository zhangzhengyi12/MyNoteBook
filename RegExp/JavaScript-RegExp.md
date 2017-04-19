# JavaScript 正则表达式 #
JavaScript正则表达式使用RegExp类型来支持正则表达式。基本语法如下
  `var expression = /pattern/flags `
其中pattern为表达式本体 
flags为一个或多个标志，用以标明正则表达式的行为，正则表达式支持以下三个标志，
- g:表示全局模式，应用与所有符合的字符串，默认为找到第一个就停止
- i:表示不区分大小写
- m:表示多行模式

RegExp的生成方式的有两种 通过RegExp的字面量或者构造函数
# 基本正则语法记录 ###

## 元字符 ##
元字符就是有特殊含义的字符，非字面含义

> `* + ? $ ^ . | \ () {} []`

## 字符类 ##
类让我实现可以选择一些或者说是一类字符，泛指。
**使用[]来构建一个简单的类**
- `[abc]` 可以是abc中的任意一个
**类取反**
- `[^abc]` 不属于abc的

## 范围类 ##
范围类可以让我更简单的创建具有规则的类

- `[a-z]` 代表一个所有的小写字母
- `[a-zA-z]` 连写范围 代表所有的字母
- `[0-9-]` 代表选中所有的字母或者是-

## 预定义 ###
预定了一些常用的类

- `.` 除了回车符和换行符之外的所有字符
- `\d`  数字字符
- `\D` 非数字字符
- `\s` 空白符
- `\S` 非空白符
- `\w` 单词字符（字母、数字下划线）
- `\W` 非单词字符

## 边界 ##
- `^` 以XXX开始 `/^Th/` 
- `$` 以XXX结束 `\w$` 
- `\b` 单词边界 
- `\B` 非单词边界 `/\Bis\b/` 代表is的前方紧贴字母 而后方是个空格

## 量词 ##
用于让一种规则连续出现比如`/\d{20}/`

- `?` 出现0次或一次 最多出现一次
- `+` 出现一次或多次 最少出现一次
- `*` 出现任意次
- `{n}` 出现N次
- `{n,m}` 出现n到M次
- `{n,}` 最少出现N次 `{0,n}`最多出现N次

## 贪婪模式与非贪婪模式 ##
JS正则在默认情况下优先匹配更多的量词 比如
    `\d{3,6}` js会尝试能实现的最多次数 这里为6次
非贪婪模式需要在量词的后面加上一个问号

## 分组 ##
量词无法作用于整个单词，只能作用于紧挨他前方的词
`/this{3}/` 代表thi 然后重复三个s 如果需要让整个单词重复 则要分组

`/(this){3}/`

### 或 ###
用 `|` 可以实现或的效果

`Byron|casper` `this is a (boy|girl)` `Byr(on|ca)`

### 反向引用 ###

    alert("2017-04-15".replace(/(\d{4})-(\d{2})-(\d{2})/gi,"$3-$2-$1"));

首先分组，然后通过$加上其分组的序列号来引用其中的内容

### 忽略分组 ###
不希望捕获某些分组，只用来做量词或者 或只用，只需要在分组的前部分加入`?:`

`(?:Byrong).(ok)`

## 前瞻 ##
正则表达式从文本头部向尾部进行解析，文本尾部方向成为前瞻
前缀就是在正则表达式匹配到规则的时候，向前检查是否断言，后顾/后瞻方向相反
JS不支持后顾

- 正向前瞻 `exp(?=assert)` 在正则检查完毕之后检查断言是否正确
- 反向前瞻 `exp(?!assert)` 在正则检查完毕之后检查断言是否不符合
- 断言不属于规则部分，如果为正向前缀 前缀为false那么就不会加入到匹配列表之中，如果反向为true那么也不会添加到匹配列表之中


	alert("2017-04-15".replace(/\d{4}(?!-)/gi,"omg")); //如果四个数字后面不为-那么就匹配成功
	alert("2017-04-15".replace(/\d{4}(?=-)/gi,"omg")); //如果四个数字后面为- 匹配成功

## 对象属性 ##

前三个对象属性与gim一一对应
- global
- ingnoreCase
- multiline
- 在不设置gim的前提下，三者均为flase,且这三个为只读属性
- lastIndex 是当前表达式匹配内容的最后一个字符的下一个位置
- sourece: 正则表达式的文本字符串

## 正则方法 ##

### test ###
`RegExp.prototype.test(str)`
用于测试字符串参数中是否存在匹配正则表达式模式的字符串
如果存在则返回true, 否则返回false
在test过程中，可能会出现ture和false，这是因为test方法是检测是否存在，所以并不需要选中全局，那么在加上了全局的情况之下，多次执行test可能会导致正则对象的lastIndex不断变化，当lastIndex不再存在的时候，就会返回flase,之后Last被重置

test的本意是为了是否存在，可以取消g标志。

### exec ###

`RegExp.prototype.exec(str)`

使用正则表达式模式对字符串执行搜索，并将更新全局 RegExp的属性(lastIndex)以反映匹配结果

如果没有返回匹配的文本则返回null,否则返回一个数组,数组有以下key

- index 声明匹配文本的第一个字符的位置
- input存放被检索的字符串String
- 匹配到的项目以及子正则（分组匹配，如果有）的字符串，这些key通常为数字


	
		var reg = /(\w)(\d)/g;
		var string = "a1b2c3d4";
		
		var ret;
		
		//console.log(ret.index + \n + reg.lastIndex + ret.toString);
		
		while(ret = reg.exec(string)){
		console.log(ret.index+ "找到首个位置" + "\t" + reg.lastIndex+ "下次开始查找的位置" + "\t" + ret);
		}

		"0找到首个位置  2下次开始查找的位置  a1,a,1"
		"2找到首个位置  4下次开始查找的位置  b2,b,2"
		"4找到首个位置  6下次开始查找的位置  c3,c,3"
		"6找到首个位置  8下次开始查找的位置  d4,d,4"

**需要注意的是，不要在循环内部调用exec,直接打印缓冲器ret的属性，如果调用就会再次触发lastindex跳动，这样会少遍历一半的次数**


---

### search ###

    `String.prototype.search(reg)`
- search（）方法用于检索字符串中指定的子字符串，如果参数非正则，尝试转换成正则表达式。
- 方法返回第一个匹配 index,查找不到返回-1.k

 

### match ###
 
    `String.prototype.match(reg)`

- match() 方法将解锁字符串，以找到一个或多个与regexp匹配的文本
- regexp是否具有标志g对接过影响很大


### split ###

    `String.prototype.split(reg)`
用split方法可以把字符串分割为字符串数组 参数为字符或者是正则表达式 用作分割的部分会被忽略

与Array的Join方法正好相反，join是把一个数组转换成字符串

### replace ###
字符串的replace方法，参数有两个 要找谁 替换成什么

- `String.prototype.replace(str,replaceStr);`
- `String.prototype.replace(str,replaceStr);`
- `String.prototype.replace(reg,function);`

function实质上是将一个函数的返回值当成替换字符，那么就有四个传进去的函数，每次找到匹配结果就调用一次这个函数
- 匹配字符串
- 正则表达式分组内容，没有分组则没有该参数
- 匹配项在字符串中 index
- 原字符串