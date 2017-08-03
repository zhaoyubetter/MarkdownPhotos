##函数
###函数声明
kotlin 中函数使用 `fun` 关键字声明

	fun sum(val a:Int, val b:Int):Int {
		return a + b
	}
	
###中缀表示法
函数可以用中缀表示法调用,当：

- 函数是成员函数 or 扩展函数；
- 函数只有一个参数
- 函数使用 `infix` 关键字标注   

```
	// 添加扩展函数 
	infix fun Int.double(x:Int):Int {
    	return x*2
	}

	fun main(args: Array<String>) {
    	println(2 double 5)	// 等价于 2.double(5)
	}
```

###参数
参数使用 `name:type` 形式声明，每个参数必须有显示类型，参数可添加默认值，用来减少重载方法；默认值通过 = 赋值；

	infix fun Int.double(x:Int=2):Int {
    	return x*2
	}
	
###命名参数
调用函数的时候，可以带上参数如：	
	
	println(2.double(x=5))	// x 为参数名称，多个参数时，可以打乱顺序
	
###返回 Unit 的函数

如果一个函数不返回任何有用的值，默认返回类型是 `Unit`,Unit是一种只有一个值 - Unit 的类型；Unit 可选；

### 单表达式函数
当函数返回单个表达式时，可以省略花括号，并在 = 后加上 代码体即可
	
	// 单表达式函数
	fun Int.double2(x:Int=2):Int = x * 2
	
	// 返回值类型可由编译器推断时，显示声明返回类型也是可选的
	fun Int.double2(x:Int=2) = x * 2

### 显示返回类型
具有块代码，即：有花括号的代码，必须始终显示指定返回类型，除非函数返回`Unit`;
Kotlin不推断块代码体的函数返回类型；

###可变数量的参数（vararg）
函数的参数（一般最后一个参数），可用 `vararg`修饰标记：
	
	fun sum(vararg ar: Int): Int {
    	var sum: Int = 0
    	for (i in ar) {
        	sum += i
    	}
    	return sum
	}

	fun main(args: Array<String>) {
    	println(sum(1,2,3,4,5,6,6,7))
	}
	
###局部函数

即函数的嵌套,局部函数可以访问外部函数（闭包）的局部变量；
没啥意思的示例代码：
	
	infix fun Int.divide(x: Int): Int {
    	fun check() {
        	if (x == 0)
            	throw IllegalAccessError()
    	}
    	check()
    	return this / x
	}

	fun main(args: Array<String>) {
    	println(5 divide 2)
	}

### 成员函数
类或对象内部定义的函数；

###扩展函数

###尾递归函数
允许将一些循环写的算法改用递归函数，不用的担心堆栈溢出；
当一个函数用 `tailrec` 修饰符标记，并满足所需的形式时，编译器会优化该递归，形成一个快速而高效的循环版本




##高阶函数与Lambda表达式

###高阶函数

高阶函数是将函数用作`参数或返回值`的函数, 如下例子，接受一个锁对象和一个函数，获取锁，函数运行结束，释放锁

```java
// body 为函数类型: () -> T
fun <T> lock(lock: Lock, body: () -> T): T {
    lock.lock()
    try {
        return body()      // 执行
    } finally {
        lock.unlock()
    }
}

// 函数
fun add(x: Int, y: Int) = x + y

fun main(args: Array<String>) {
    val lock: Lock = ReentrantLock()
    println(lock(lock, { add(5, 6) }))	// 传递方式 lambda
}
```

**map的例子：**

```java
fun <T, R> List<T>.map(transform: (T) -> R): List<R> {    val result = arrayListOf<R>()    for (item in this)        result.add(transform(item))    return result}
```

**单个参数的隐士名称 it**

*约定：*如果函数只有一个参数，那么它的声明可以省略（连同 ->）,参数名称为 it；  
*注意：*如果lambda是该调用的唯一参数，则调用的 `()` 圆括号可以省略；

```java
	 val list = arrayListOf<Int>(1, 2, 3, 4, 5)
	 // 省略圆括号,原本可写成 list.map() {...} 这种
    val doubles = list.map { value -> value * 2 }
    for (a in doubles) {
        println(a)
    }

	 // 省略声明，lambda为唯一参数时
    val three = list.map { it * 3 }		// 省略圆括号
    for (a in three)
        println(a)
```

**下划线用于未使用的变量**
如果 lambda 表达式的参数未使用，可使用下划线取代其名称：

```java
    val map = mapOf("a" to 1, "b" to 2)
    map.forEach { _, u -> println("$u") }
```


###Lambda表达式
一个Lambda表达式或匿名函数是一个 “函数字面值”，即一个未声明的函数，但立即作为表达式传递；

如下：

	max(strings, { a, b -> a.length < b.length })

函数`max`是一个高阶函数，他接受一个函数作为第二个参数，其第二个参数是一个表达式，本身是一个函数，写成函数，相当于

	fun compare(a:String, b:String):Boolean = a.length < b.length
	
####函数类型
对于接受另一个函数作为参数的函数，需要为该参数指定函数类型，如上面`max`函数定义为：

```java
fun <T> max(collection: Collection<T>, less: (T, T) -> Boolean): T? {    var max: T? = null    for (it in collection)
    	  // 这里调用了函数        if (max == null || less(max, it))            max = it	return max 
}
```
参数 `less` 的类型是 `(T,T) -> Boolean`，即：接受2个类型为`T`的参数，并返回一个Boolean值的函数；这就是 `函数类型`；

####Lambda表达式语法
Lambda表达式的完整语法形式，如下：
	
	val sum = {x:Int, y:Int -> x + y}
	
lambda表达式总是被大括号扩着，完整语法在 大括号内，类型标记可选，
函数体跟在一个 `->` 之后；**如果推断出的该Lambda的返回类型不是 Unit,那么该 Lambda主体中的最后一个表达式会视为返回值；**

加上所有可选的标注如下：

	val sum:(Int,Int) -> Int = {x, y -> x+y}

大多 Lambda表达式只有一个参数，可以不声明唯一的参数，其隐含声明了，并且名称为 `it`:

	insts.filter { it > 0} 	// 字面值为 "(int:Int) -> Boolean" 类型

其隐含返回最后一个最后一个表达式的值；

####匿名函数
Lambda表达式缺少指定函数返回类型的能力，但返回类型可以自动推断，不是很有必要；如果 确实需要显示指定，可以使用另一种语法 `匿名函数`

	fun(x:Int, y:Int):Int = x + y
	
	// 或者这样定义：
	fun(x:Int, y:Int):Int {
		return x + y
	}
	
匿名函数，就是没有名称的常规函数，使用如下：
	
	// 匿名函数
    val list = arrayListOf<Int>(1, 2, 3, 4, 5)
    var fours = list.map(fun(item) = item * 4)
  
**注意：** 匿名函数的参数总在括号内传递，区别于Lambda表达式；

匿名函数语法允许你直接指定函数字面值的 接收者类型 如果你需要使用带接收者的函数类型 声明一个变量，并在之后使用它;

```java
// 带接收者的函数字面值： 类似于扩展函数
val sum = fun Int.(other: Int): Int = this + other

fun main(args: Array<String>) {
    println(sum(2,2))  // 4
}
```
	

	



**Lambda表达式的一些特征**

- lambda表达式总是被大括号扩着；
- 其参数(有的话) 在 `->` 之前声明（参数类型，可以忽略）；
- 函数体(有的话) 在 `->` 后面；

在 Kotlin中，如果函数最后一个参数是一个函数，并且如果传递一个 lambda表达式作为相应的参数，可以在圆括号之外指定他，这个语法比较重要；如下：
	
	lock(lock, { add(5, 6) })
   	lock(lock) {add(5,6) }  	// 圆括号之外指定 lambda
  
###内联函数
参考：http://www.jb51.net/article/115381.htm

内联函数就是在程序编译时，编译器将程序中出现的内联函数的调用表达式用内联函数的函数体来直接进行替换。显然，这样就不会产生转去转回的问题，但是由于在编译时将函数体中的代码被替代到程序中，因此会增加目标程序代码量，进而增加空间开销，而在时间代销上不象函数调用时那么大，可见它是以目标代码的增加为代价来换取时间的节省。

使用高阶函数会带来一些运行时的效率损失：每一个函数都是一个对象，并会捕获一个闭包；
在那些函数体内会访问到的变量，内存分配与虚拟调用会有一些开销；

使用 inline 修饰符标记 上面的 lock 函数：

	inline fun <T> lock(lock: Lock, body: () -> T): T 
	
`inline` 修饰符影响函数本身和传递给他的 lambda表示式：所有这些都将内联到调用处；

`noinline`为禁止内联；

太晕！！！

###协程
协程提供了一种避免阻塞线程并用更廉价、更可控的操作替换线程阻塞的方法：协程挂起；
协程通过将复杂性放入库来简化异步编程；

####阻塞 vs 挂起

基本上，协程技术可以被挂起而无需阻塞线程，阻塞会导致一些重要任务被延迟，而协程挂起代价低，不需要上下文切换，或者 os 的任何其他干预，而且挂起可以在很大程度上由用户控制；






	