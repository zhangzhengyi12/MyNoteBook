最近看语言精粹这本小册子 结果发现难度有点太高了 很多东西都是一笔带过所以绝对看完函数先来看javascript高程 看起来第一感觉就是会平缓很多

1. 在函数内用var声明的变量 ，在完成这个函数的调用也就是读到这个函数｝的时候 会被销毁 注意是销毁。

2.  在函数内用省略var声明的变量在退出函数之后并不会被销毁，所以是可以在函数外围被访问的。


3.  null在javascript中的实现为指向一个空的对象 

	```
	var nu = null;
	
	console.log(typeof nu); //类型为object
	``` 
	所以当我们需要创建一个变量并在未来存储对象 我们可以给他初始化为null
	
	```
	if(nu != null){
	
	}
	```
	通过这样的方式来检查调用 我认为undefined属于没有右值的情况 而null其实是有有值的 是一个空的对象。

4. boolean可以被强制转换成数字

5. Number类型支持10/16/8进制 `var a = 078;//8进制` `var b = 0xAF;//16进制`

6. 浮点数 小数位为空或者小数位为0会被转换成整数

7.  NaN的意思为not a number 所以他是一个特殊数值  通过isNaN来测试是否不是数值`isNaN("HelloWorld")`
8.  数值转换有三种方法把其他变量转换成数字 `Number();` `parseInt();`
`parseFloat`常用的为parseInt 支持第二个参数帮助标记一下 基数 比如`parseInt("AF",16)//标记基数为16进制`

9.  字符串有转义比如`\n代表换行符` 一般有特殊用途
字符串一旦声明无法更改 所谓的重新绑定字面量只是销毁原来的填充新的
具体首先制造容器 然后填充 接着删除不需要的字符串字面量
几乎所有的值都有这个方法`toString(目标进制)//除了undefined null值没有`
也可以调用`String(value)` 这个函数范围最广 包含toString 如果支持toString就调用 如果不支持就把null undefined直接转换。

10. 操作符
	 一元操作符是只能操作一个值的操作符 是最简单的操作符。
	 - 递增和递减操作符
		 - 分前置和后置 前置的就是`++a` 先加后调用

		 - 后置的就是`a++` 先调用后加

     - 一元加减操作符
	     - `num = +num`对值进行转换 但对值的内容一般不会有所影响 比如true flase会有变化
	     
	     - `num = -num` 把值变成负数

 
	 - 位操作符（底层的更改 高效率 64转换成32位开始运算）
		 
		 - 按位非 ~ `var num1 = 25; var num2 = ~num1;//-26` 结果为内存保存的二进制树挨个取反   且数值-1 

		 - 按位与 `25&3//-1`转换成二进制数字对比 两者同位都为1则为1 否则都为0
		        
		 -  按位或`var num = 25|3;`同样是对比 二者都为0则为0 其他情况都为1
		 
		 - 按位或异 `var num = 25^3;` 二者相同为0 不同为1 
		 
		 - 左移 `num = num << 5`表示整个移5位  少出来的用0填充 结果一般是增大
		 
		 - 有符号右移 `num = num >> 5`表示符号位右移 少出来的用符号位填充
		 
		 - 无符号的右移 和上面的类似 但少出来的符号位用0填充 也就是说本来是负数会变成正数 正数还是正数（因为1代表负数 0代表正数嘛）

		
	 - 逻辑操作符
		 - 逻辑非 用一个！表示 首先把值转换成boolean值 然后取反
		 - 逻辑与 用两个 & 表示同样转换boolean然后对比 都为true则true 其他均为false 短路 第一个为false就直接结束了 
		 - 逻辑或 用两个|表示 转换 对比 都为false为false 其他均为true  同样短路 找到第一个为true则取消第二次对比 直接返回。而返回值并不一定是true或者false 根据对比的值不同 在obj的情况下返回的就是obj 这也就是利用短路或做后备赋值的原理。
		 
     - 乘性操作符
	     -乘 除 余 三大 余为`var a = 25 % 3;` 
	     
     - 关系操作符 大于 小于 大于等于 小于等于 
	     -  字符串之间的比较会比较奇怪 比如两个字符串会挨个对比每个字节 如果你想要进行对比需要都转换成小写`.toLowerCase()`不然大小写无法正确对比

	     - 两个数字字符串对比会比较编码 一个数字一个数字字符串会转换成数字比较
	     
	     - 任何值与NaN对比都为false
     
     - 相等操作符
	     - `==`  `!=` `===` ===为严格等于或者叫完全等于 字符串的3和数值3在完全等于下并不相等  
     - 条件运算符（三元运算）
	
     - 赋值操作符
	     - =等于可以和其他的运算符合作 比如

	     - ```a += 6;

      
      
	     

