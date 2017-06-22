##解构声明
某个时候，对一个对象 解构 成很多变量会比较方便，如下为解构语法：

	// 注意： 对应的必须是：data 数据类
	data class User(val name: String, val age: Int) {}

	fun main(args: Array<String>) {
    	val user: User = User("better", 29)

		// 解构,成2个单独的变量
    	val (name, age) = user  
    	println("$name : $age")
    	
    	// 以下等价
    	val name1 = user.component1()
    	val age1 = user.component2()
    	println("$name1 : $age1")
	}

遍历map新方式：
	
	// 遍历 map
    val map = mapOf("one" to 1, "two" to 2)
    for ((key, value) in map) {	// 使用 解构 Pair key value
        println("$key ----> $value")
    }
    
### Lambda解构
如果 lambda 表达式具有 `Pair类型（或Map.Entry）或其他有对应的 componentN 函数`的参数,如：；

```java
	map.mapValues { (key, value) -> println("$key ----> $value") }
    map.mapValues { entry -> println("${entry.value}") }
```

## 集合

kotlin区分可变集合和不可变集合（list、set、map），精确控制什么样的集合，有助于消除bug；

```java
val readOnlyList = listOf<Int>(1,2,3)	// 只读，没有删掉，设置方法
val list = mutableListOf<Int>(4,5,6)
```

### list set 的一些扩展方法实例：
参考：http://www.cnblogs.com/figozhg/p/5031398.html

####聚合相关操作

```java
fun juhe() {
    println("=== 一些聚合操作运算 ==")

    val list = listOf(1, 2, 3, 4, 5, 6, 7, 8)

    // any: 至少有一个与条件相符
    println(list.any { it > 0 })

    // all: 与所有条件符合，才返回true
    println(list.all { it > 6 })

    // count: 返回与条件匹配相同的个数
    println(list.count { it % 2 == 0 })

    // fold: 从初始值 递减 list 中的每一项
    println(list.fold(100) { original, it -> original - it })  // 64
    println(list.fold(0) { original, it -> original + it })     // 求和

    // forEach： 让每一项执行相同的操作
    list.forEach { it * 2 }

    // none: 没有元素与这个条件符合时，返回true
    println(list.none { it % 100 == 0 })

    // reduce: 同 fold，只是没有初始值
    println(list.reduce { total, next -> total + next }) // 求个和
}
```

####筛选相关

```java
fun filter() {
    println("=== 一些筛选操作运算 ==")

    val list = listOf(1, 2, 3, 4, 5, 6, 7, 8)
    // filter: 过滤
    println(list.filter { it % 2 == 0 })

    // slice：返回指定索引初的元素
    println(list.slice(listOf(1, 2)))   // 2,3

    // takeWhile: 返回满足条件的第一个列表《切割的意思》
    println(list.takeWhile { it < 3 })  // 1,2

    // dropWhile: 返回所有元素类别，但不包括 满足条件的
    println(list.dropWhile { it < 3 })
}
```

####映射相关操作

```java
fun map() {
    println("=== 一些映射操作运算 ==")

    val list = listOf(1, 2, 3, 4, 5, 6, 7, 8)

    // flatMap：遍历每个元素创建一个新集合，并加入新元素，把所有集合整合到包含所有元素的唯一列表中。
    println(list.flatMap { listOf(10) }) // 10,10,10,10,10,10,10

    // groupBy:分组，返回映射表
    println(list.groupBy { if (it % 2 == 0) "even" else "odd" })

    // map: 返回列表，并且列表中的每个元素进行了转换
    println(list.map { it * 2 })
}
```

##区间
区间表达式 由操作符 `..` 辅助以 `in !in` 形成：

	(i in 1..10)  // 1 到 10 ：[1,10]
	(i in 1 until 10) // 范围：[1, 10)
	
## 类型检查与转换
### is 和 !is 操作符
用来判断类型，

	if(a is Int) {}
	
### 智能转换

	fun demo(x:Any) {
		if(x is String) {
			print(x.length)  // x 自动转换了
		}
	}

### 转换操作符

`as 与 as?`, 前者为不安全转换，后者为安全转换

	var a: Any = 123
    println(a as Int)       // 强制转换

    var b: String = "123"
    println(b as Int)   // 类型转换错误
    println(b as? Int)  // 安全转换 失败时不抛异常，返回 null
    
  
## this 表达式
表示当前 接收者，使用 this 表达式：
- 在类的成员中，this指的是该类的当前对象；
- 在扩展函数或带接收者的函数字面值中，this表示在 点 左侧传递的 接收者 参数；

### 限定的 `this`
要访问来自外部作用域的 this，使用 `this@label`,其中 `@label` 是一个代指 `this` 来源的标签；

```java
class A { // 隐式标签 @A	inner class B { // 隐式标签 @B		fun Int.foo() { // 隐式标签 @foo 
			val a = this@A	// A 的 this
			val b = this@B	// B 的 this
						val c = this // foo() 的接收者，一个 Int			val c1 = this@foo // foo() 的接收者，一个 Int
						val funLit = lambda@ fun String.() { 
				val d = this // funLit 的接收者			}
						val funLit2 = { s: String ->				// foo() 的接收者，因为它包含的 lambda 表达式 // 没有任何接收者				val d1 = this			} 
		}	}
}
```

## 相等性
有2种类型的相等性：

- 引用相等 （2个引用指向同一个对象）
- 结构相等（equals检查）

### 引用相等
引用相等 由 `===` (否定形式 `!==`) 操作判断，a===b 并且 a 和 b 指向同一个对象时为true；

### 结构相等
结构相等 由 `==` (否定形式 `!=`) 判断，`a===b`会翻译成：
	
	a?.equals(b) ?: (b === null)

如果 a 不是 null，调用 equals(Any?)函数，否则（即 a 为 null），检查 b 是否与 null 引用相等；

## 操作符重载

重载 操作符的函数需要用 `operator` 修饰符标记   
Kotlin 允许为自己的类型提供预定义的一组操作符实现。这些操作符具有固定的符号表示（如： `+` 或 `*`）和固定的优先级；为相应的类型（二元操作符左侧的类型和一元操作符的参数类型）
提供了一个固定名字的函数；重载操作符的函数需要用 `operator`修饰符标记；

### 约定
不同操作符规范重载的约定
####一元操作

|表达式  |翻译为|
| ---  | -----: |
|+a		|	a.unaryPlus()|
|-a    | a.unaryMnus()|
|!a| a.not()|

当编译器处理例如表达式 +a 时，它执行以下步骤:  
- 确定 a 的类型，令其为 T 。- 为接收者 T 查找一个带有 operator 修饰符的无参函数 unaryPlus() ，即成员函数或 扩展函数。- 如果函数不存在或不明确，则导致编译错误。- 如果函数存在且其返回类型为 R ，那就表达式 +a 具有类型 R 。


## 空安全
在 kotlin 中，如果定义的变量允许 null，需要这样定义：

	var a:String = "abc"  // a 不允许为null
	a = null 	// 报错
	
	// 允许 null
	var a:String? = "abc"		// ? 运行 null
	a = null
	
	// 调用 a.length 不会掉null pointer
	a.length
	
###在条件中检查 null
	
	val l = if(a != null) a.length else -1
	
### 安全调用
`if not null` 的缩写，如果 a 不为 null 执行 .length ，否则返回 null 

	a?.length 

链式调用（如果任一环节有 null，这个表达式 就为 null了）：
	
	bob?.department?.head?.name // 
	
如果要只对非空值执行某个操作，安全调用操作符可以与 `let` 一起使用：

	val withNullList = listOf("abc", null, "bbcc")
    for (item in withNullList) {
        item?.let { println(it) }		// 过滤了 null
    }
    
### `!!`操作符

为 null pointer 抛出异常，可以写成 `b!!`

	///////// 为 null pointer 爱好者 准备的，这里将抛出异常
    println("===== !! ====")
    val b: String? = null
    println(b!!.length)
   
### 安全的类型转换

如果对象不是目标类型，常规的类型转换可能导致 `ClassCastException`，可以使用安全的类型转换来，如果尝试不成功，返回 `null`

	
	fun main(args: Array<String>) {
    	val a: String? = "abc"
    	val b = a as? Int
    	println(b)      // null ，转换失败时，为null
	}

## 异常

### 异常类
kotlin 中所有异常类都是 Throwable 类的子类，使用 `throw`表达式来抛出异常

	throw MyException("exception")

### 使用 `try` 表达式来 捕获异常
类似于 Java

	try {
	} catch(e:Throwable) {
	} finally {
	}
	
###受检的异常
Kotlin 中没有受检的异常，Java中有，这是什么意思呢，就是在方法的定义加上 `throws`抛出异常，表示，该方法可能抛出异常，所以很多情况下，代码中经常出现 try ... 这些，而且，很多是什么都不做；

#### Nothing类型
在Kotlin中，throw 是表达式，可以直接使用，如：

	val s = person.name ?: throw Exception("name is null")

`throw` 表达式的类型是特殊类型 `Nothing` ,该类型没有值，而是用于标记

```java
    
testException()

class User(val name: String?) {
}

fun fail(message: String): Nothing {
    throw IllegalArgumentException(message)
}

fun testException() {
    val user: User = User(null)
    val s = user.name ?: fail("Name required!")
    println(s)
}
```

##注解
注解是将元数据附加到代码的方法，使用 `anonotation`修饰符放在类的前面：

```java
// 定义注解
annotation class ReplaceWith(val expression: String)

// 定义注解
annotation class Deprecated(
        val message: String,
        val replaceWith: ReplaceWith = ReplaceWith(""))

@Deprecated("not use", replaceWith = ReplaceWith("other")) class Foo {
    fun baz(foo: Int): Int {
        return (1)
    }
}
```

##反射

允许再运行时自省程序结构；

### 类引用

最基本的反射功能是获取 Kotlin 类的运行时引用；获取已知的 Kotlin 类的引用；

	val c = List::class         // 获取kotlin的类引用
    println(c)
    println(List::class.java)   // 获取 Java 的类引用，java 需要加 .java
    
### 函数引用

可以把函数当做一个值传递，传给另外一个函数，使用 `::`操作符

	fun isOdd(x: Int) = x % 2 != 0
	
	val list = listOf(1, 2, 3, 4, 6, 7)
    println(list.filter(::isOdd))

这里的 `::isOdd` 是函数类型 `(int) -> Boolean `的一个值；

当上下文已经函数期望的类型时，`::`可用于重载函数；

	val list = listOf(1, 2, 3, 4, 6, 7)
    println(list.filter(::isOdd))

    val list2 = listOf<String>("brillig", "b", "c")
    println(list2.filter(::isOdd))	// 引用到 isOdd(s:String)

	// 函数
	fun isOdd(x: Int) = x % 2 != 0
	// 重载
	fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"
	
**或者 通过将方法引用存储在具有显式指定类型的变量中来提供必要的上下文**
	
	 val predicate: (String) -> Boolean = ::isOdd // 引用到 isOdd(x: String)

####函数组合

	////// 函数组合 Start
	// 返回一个传给它的2个函数的组合： compose(f, g) = f(g(*))
	fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    	return { x -> f(g(x)) }
	}

	fun length(s: String) = s.length

	val oddLength = compose(::isOdd, ::length)
	val strings = listOf("a", "ab", "abc", "abcd")
	println(strings.filter(oddLength))	 // ["a", "abc"]
	
####属性引用

要把属性作为 `kotlin` 中的一等对象来访问，也可以使用 `::`运算符
	
	var x = 1

	fun main(args: Array<String>) {
    	println(::x.get())  // 1
    	::x.set(2)
    	println(x)  // 2	
	}
	
表达式 `::x` 求值为 `KProperty<Int>` 类型的属性对象，允许使用 get 读取值，可变属性有一个set方法；

**属性引用可以用在不需要参数的函数处:**

	val strs = listOf("a", "b", "def")
    println(strs.map(String::length))    // 属性引用 length
    println(strs.map { it.length }) // 等价上面的
    
**访问属于类的成员属性**

	class A(val p: Int)
	val prop = A::p
    println(prop.get(A(1)))     // 输出1
    
**对于扩展属性:**

	val String.lastChar: Char
    get() = this[length - 1]
    println(String::lastChar.get("abc")) // c
    
**构造函数的引用**

通过 `::` 操作符 + 类名来引用构造函数

	data class Person(val name: String)

	fun main(args: Array<String>) {
    	val p = ::Person            // 构造函数的引用
    	val person = p("better")

    	println(person.name)
	}
	
**绑定的函数与属性引用**
不是很清楚

	// 引用特定对象的实例方法
    val numberRegex = "\\d+".toRegex()
    println(numberRegex.matches("29"))  // true

    // 取代直接调用方法 matches 存储其引用
    // 引用会绑定到其接收者上
    val isNumber = numberRegex::matches
    println(isNumber("29"))

    val strings = listOf<String>("abc", "123")
    println(strings.filter(isNumber))       // 输出 123
    println(strings.filter(numberRegex::matches))

比较绑定的类型和相应的未绑定类型的引用。 绑定的可调用引用有其接收者“附加”到其上， 因此接收者的类型不再是参数:

	val isNumber: (CharSequence) -> Boolean = numberRegex::matches	val matches: (Regex, CharSequence) -> Boolean = Regex::matches
	
	
## 类型安全的构建器

如下：

```java
import com.example.html.* // 参见下文声明fun result(args: Array<String>) =    html {        head {            title {+"XML encoding with Kotlin"}		 } 
		 body {            h1 {+"XML encoding with Kotlin"}            p  {+"this format can be used as an alternative markup to XML"}         ...```

###实现原理

实现类型安全构建器的机制：		
首先需要定义构建的模型，如上面的 html标签，用类完成；


	html{
	
	}
	
上面代码中的 `html` 实际上是一个函数调用，接受一个 `lambda`表达式作为参数；

函数定义如下：

	fun html(init: HTML.() -> Unit) : HTML {
		val html = HTML()
		html.init()
		return html
	}
	
init参数 本身是一个函数，类型是 `HTML.() -> Unit` 它是一个带接收者的函数类型，所以需要传递一个 HTML类型的实例（接收者），并在函数内部调用该实例的成员；
	
	// this，可以省略，head 和 body 是 html 的成员函数；
	html {
		this.head {}
		this.body {}
	}
	
以上创建了一个 HTML 新实例，然后通过调用作为参数传入的函数来初始化它（在HTML实例上调用 head 和 body），然后返回，这是构建器所做的事情；

```java
val a = html {
    head {
        title = "www.jd.com"
        descriptin = "购物上京东"

    }
    body { }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}

class HTML {
    fun head(init: Head.() -> Unit): Head {
        val head = Head()
        head.init()
        return head
    }

    fun body(init: Body.() -> Unit): Body {
        val body = Body()
        body.init()
        return body
    }
}

class Head {
    var title: String? = null
    var descriptin: String? = null
}

class Body {

} 
```
	
