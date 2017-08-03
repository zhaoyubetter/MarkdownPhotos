## 类和对象
##### 使用 class 关键字来定义类，如果一个类，没有类体使用Empty
如：

	class Myclass Empty

### 构造函数
**主构造函数**

kotlin 中可有一个*主构造函数* 和 多个次构造函数，主构造为类的一部分，如主构造函数没有任何注释or修饰可省略 **constructor** 关键字：
init 为初始化块
	
	class Myclass construcor(val value:String) {
		// 初始化块
		init{
		}
	}

**次构造函数**

声明使用：
	
	class Person {
		contructor（parent:Person） {
		}	
	}

在次要构造中，委托调用统一个类的构造用**this**
	
	class Person(val name: String) {
    	constructor(name: String, parent: Person) : this(name) {
    	}
	}

构造函数默认是 public类型的，如果不想对外暴漏，可以这样来声明

	class Person private constructor() {
	}

构造函数上的方法（其他方法也是一样）可以为参数添加默认值，这样可以解决java中的重载函数过多问题；

	class Person private constructor(val name: String) {
    	constructor(name: String= "better", parent: Person) : this(name) {    
    	}
	}

### 创建类的实例
调用构造函数，kotlin不用new来创建对象
	
	val person = Person()

**类成员包括**
- 构造函数和初始化块
- 函数
- 属性
- 嵌套类和内部类
- 对象声明

### 继承
Kotlin 所有类的超类是 Any，类似于Java的Object；继承使用 冒号 : ;

- 显示的超类型 **(open标注表示该类可以允许被继承，与java中的final 相反)**

		open class Base(p:Int)
		class Child(p:Int):Base(p)	// 继承自Base类
- 构造函数中，使用**
- **初始化基类

		class MyView : View {
			constructor(ctx: Context)：super(ctx)
			constructor(ctx:Context, attrs:AttributeSet) : sper(ctx, attrs)	
		}
		
- 覆盖方法：要求覆盖的方法，必须加前缀 override，与类的定义一致，如果方法前，没有加 open 关键字，则，默认方法是 final，不允许被override
		
		class MyClass(val name:String) {
			open fun() {	// 允许override
				
			}
		}
		
		class ExtendsClass(val name:String):MyClass(name) {
			override fun() {
				
			}
		}
		
- 覆盖属性：类似于方法的覆盖，基类的属性用 open 来修饰；如果属性被覆盖必须以override开头，并且具有相同的类型
		
		open class Base {
			open val x: Int get {...}
		}
		class One : Base {
			override val x:Int = 0		// 覆盖属性
		}
		// 在构造函数中覆盖
		class Two(override val x:Int) : Base {
			
		}

- 覆盖的特殊情况：父类中存在相同的方法，使用尖括号来区分来自哪个父类
		
		open class Base {
			open fun f() {}
			fun a() {}
		}
		
		// 接口默认是 open
		interface B {
			fun f() {}
			fun b() {}
		}
		
		class MyClass() : Base(), B {
			override fun f() {
				super<Base>.f()		// 调用 Base.f()
				super<B>.f()			// 调用B.f()
			}
		}
		
### 抽象类
类似与java，类的某些成员可以声明为 *abstract*， 被声明为 abstract，默认就是open；
	
	open class Base {
		open fun f() {}
	}
	abstract class Other:Base() {
		override abstract fun f()		// 抽象的
	}
		
### 伴生对象
Kotlin 中没有静态对象，如果无需类的实例来调用、但需要访问类的内部的函数，使用关键字 **companion** 来标记类的内部对象为伴生对象；来达到 静态使用;

```
class A {
   // 伴生对象
   	companion object {
        fun method() {
            println("Companion object called")
        }
        
        @JvmStatic		// 表示为真正的静态方法
        fun realStaticMethod() {
            println("Companion object called")
        }
    }
    fun test() = "124"
}

fun main(args: Array<String>) {
    A.method()      // 可直接访问，类型java中 static
}
```

**注意：**
伴生对象看起来像其他语言的静态成员，但在运行时，仍然是真是对象的实例成员；
在JVM上，可以使用注解 `@JVMStatic`可将伴生对象成员，生成真的静态czz；

### Getter 与 Setter
只读属性 用 val 声明，即没有对象的setter，可变用 var 声明；
整体语法为：

	var <propertyName>[:<PropertyType>] [=<init>]
		[<getter>]
		[<setter>]

具体例子：
	
	var str:String
		get() = this.toString
		set(value) {
			field = value		// 幕后字段
		}

可见性修饰

	var str:String="better"
		private set		// set 私有
		
### 幕后字段
通过默认字段来访问属性，kotlin提供 `field`关键字来访问幕后字段；
`field`只能在属性的访问器内；
如上代码；

### 编译期常量

已知值得可使用`const`修饰符来标记为 编译期常量，这些属性需满足以下：

- 位于顶层或是 `object` 的一个成员；
- 用 `String` 或原生类型值初始化；
- 没自定义getter；

		const val url:String = "http://"
	
### 可见性修饰符
用来修饰类、接口、方法，属性等，kotlin有：`private、protected、internal与 public`，默认没有显式指定时，为public；

- 默认为public；
- private 为声明的文件可见；
- internal，表示相同的模块内可见（一个模块是编译 在一起的一套 Kotlin 文件）；
- protected private + 子类可见

**包**

函数、属性、类，对象接口等，可以在顶层声明，即：直接在包内；
顶层就是包；
这样包内，可声明多个类了

	// 文件名：hello.kt
	package test
	
	private fun test()	// 在hello.kt 中可见
	
	publc var bar:Int = 5	// 到处可见
		private set			// setter 只在 hello.kt中可见
		
	internal val bz = 22	// 相同模块内可见
	
	class Test1
	class Test2


### 扩展
扩展一个类用来添加新功能，而无需继承该类，或使用类似装饰的设计模式，这就是kotlin中的扩展；
kotlin有 `扩展函数` 与 `扩展属性`

**扩展函数**

声明扩展函数，需要用到一个 `接收器类型`，也即是被扩展的类型作为前缀，如下代码中的Collection；

以下为Collection添加扩展函数 `joinString`，为了方便在Java中使用，给此类取个名字，如下注解：`@file:JvmName()`，这样表示此类，会生成java想用的工具类
	
	```java
	@file:JvmName("StringFunctions")

	// 扩展 Collection
	fun <T> Collection<T>.joinString(separator: String = ", ",
                                 prefix: String = "",
                                 postfix: String = ""): String {
    	val result = StringBuilder(prefix)
    	for ((index, item) in withIndex()) {
        	if (index > 0) result.append(separator)
        	result.append(item)
    	}
    	result.append(postfix)
    	return result.toString()
	}
	
	// 使用
	fun main(args: Array<String>) {
    	val list = arrayListOf("1","2","3")
    	println(list.joinString(","))	// 输出 1,2,3
	}
	
	// java 中的使用
	List<String> items = new ArrayList<>();
    items.add("one");
    items.add("two");
    items.add("three");
	// StringFunctions 为静态工具类，类似于Utils
    String str = StringFunctions.joinToString(items, ", ", "(", ")");
	```

	
`this` 关键字在扩展函数中，表示接收者对象（.符号前的对象）：


	``` java			
	fun <T> MutableList<T>.swap(index1:Int, index2:Int) {
    	val tmp = this[index1]			// this 表示 MutableList对象
    	this[index1] = this[index2]
    	this[index2] = tmp
	}
	```			


###扩展是静态解析

扩展不能真正修改所扩展的类；仅是通过该类型的变量通过 点 去调用这个新函数；
扩展函数是静态分发的，调用的扩展函数是由函数调用的所在表达式的类型来决定，而不是由表达式运行时的求值结果决定；

	```java
	open class C
	
	open class D : C()
	
	fun C.foo() = "from class C"            // 为C添加扩展方法
	
	fun D.foo() = "=====> from class D"     // 为D添加扩展方法
	
	// 顶级方法
	fun printFoo(c: C) {        // 传 C 打印C，传D打印D，不会根据运行时，动态匹配（没有多态）
	    println(c.foo())
	}
	
	fun main() {
	    printFoo(D())
	}
	```

输出：`from class C` 是因为调用的扩展函数只取决于参数 `c` 的类型；

*如果那成员函数与扩展函数一致呢，以成员函数为准（即：废了），但不同函数重载签名是可以的*

**为可空的接收者添加扩展**
	
	fun Any?.toString() {
		if(this == null) return "null"
		return toString()	
	}

###扩展属性
类似于扩展函数，也支持扩展属性

	```java
	// 1.扩展函数，String为扩展的类，receiver type;  this 为接收器对象 receiver obj
	// 添加了方法到String类
	fun String.lastChar(): Char = this.get(this.length - 1)     // or get(length - 1)

	// 2.添加扩展属性
	val String.lastChar: Char
    	get() = get(length - 1)

	// 为 StringBuilder添加扩展属性
	var StringBuilder.lastChar: Char
    	get() = get(length - 1)     // getter
    	set(value: Char) {          // setter
        	this.setCharAt(length - 1, value)
    	}
	
	fun main(args: Array<String>) {
    	println("Kotlin".lastChar()) // Kotlin 为接收器对象
    	println("better".lastChar)   // 扩展属性

    	println("扩展属性 for StringBuilder")
    	var sb = StringBuilder("Kotlin?")
    	println(sb.lastChar)
    	sb.lastChar = '!'	 // 调用 setter
    	println(sb)
	}
	```
### 伴生对象的扩展

	// 伴生对象的扩展函数
	class MyClass {
    	companion object { }	// 名称为 Companion
	}

	fun MyClass.Companion.foo() {
    	println("ok")
	}
	
	// 调用
	MyClass.foo()

###扩展的作用域

大多数情况，在顶层定义扩展，即直接在包里；如果要使用，需要在调用方导入：
	
	package top_level
	
	fun Collection<String>.join(separator: String = ", ",
                            prefix: String = "" 
	...

在其他包中的使用：

	package regex		// 其他包

	import top_level.joinString	// 导入扩展函数
	// import top_level.joinString as myJoin	// 取别名

	val arrays = arrayListOf<Int>(1,2,3)
    println(arrays.joinString(","))

### 扩展声明为成员

- 在一个类的内部，可以为另一个类声明扩展； 
- 在这样的扩展内部，有多个隐式接收者；
- 扩展声明所在的类的实例为 `分发接收者`，扩展方法调用所在的接收者类型的实例为 `扩展接收者`

---

	fun main(args: Array<String>) {
    	Class_C().caller(Class_D())
	}

	class Class_D {
    	override fun toString(): String {
    	    println("D 类 toString() 方法调用...")
    	    return super.toString()
    	}
	}

	class Class_C {
    	fun baz() {}
	
    	// 为D类增加扩展方法
    	fun Class_D.foo() {
    	    toString()          		// 扩展接收者 D 类的toString
    	    this@Class_C.toString()   	// 分发接收者使用 this
    	}

    	fun caller(d: Class_D) {
        	d.foo()       // 调用扩展函数
    	}

    	override fun toString(): String {
        	println("C 类 toString() 方法调用")
       	 	return super.toString()
    	}
	}


###数据类
需要创建一些只保存数据的类；在Kotlin中，叫做数据类 并标记为 `data`：

编译器将主动从主构造函数中声明中的所有属性导出一下成员：

>1. equals() / hashcode() 对；
>2. toString() 格式是 "类名(属性名=xxx)"
>3. `componentN` 函数按声明顺序对应所有属性
>4. copy 函数

以上函数，如果其中有一个在类中显示声明或继承而来，则不会生成；

**data 标记数据类的要求如下：**
>1. 主构造函数至少有一个参数；
>2. 主构造函数的所有参数需要标记为 val 或 var；
>3. 数据类不能是抽象、开放(open)、密封、内部的；

**注意：** JVM中，如果生成的需要一个无参构造，则需要对所有属性指定默认值
	
	data class User(var name:String = "", var age:Int = 0)


**复制**

上面的数据类，会自动生成对应的copy方法，类似实现如下代码：

	fun copy(name:String, age:Int) = User(name, age)	
###解构声明
把一个对象 `解构`成很多变量会很方便，这种语法称为 `解构声明`，下面的 `name` 和 `age` 可以独立使用，例如：

```java
 val user1: User = User(name = "better", age = 30)
 println(user1)
 // copy
 val user2: User = user1.copy(name = "cc")
 println(user2)

 // 解构
 val(name,age) = user2
 println("name: " + name + " age: " + age)
```

解构声明的代码会编译成如下代码：

```java
val name = user2.component1()
val age = user2.component2()
```

### 密封类
密封类用来表示受限的类继承解构，表示一个值得优先集中的类型，区别于枚举（枚举常量只存在一个实例，密封类的一个子类可包含状态的多个实例）
修饰符使用 `sealed`;
密封类可以有子类，但是子类必需嵌套在密封类声明之内；

```java
sealed class Expr

data class Const(val number: Double) : Expr()               // 子类
data class Sum(val num1: Expr, val num2: Expr) : Expr()     // 子类
object NotANumber : Expr()

// 密封类 when 的使用
fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.num1) + eval(expr.num2)
    NotANumber -> Double.NaN        // 不需要写 else
}


fun main(args: Array<String>) {
    val a1:Expr = Const(2.0)
    val a2:Expr = Const(3.0)
    val a3:Expr = Sum(a1, a2)
    println(eval(a1))
    println(a3)
}
```

###型变
更多内容请参考：https://zhuanlan.zhihu.com/p/26965437
看了这个博客，才有点理解这个，感谢作者；

在Java泛型中，有通配符，如：? extends T, ? super T 这种；
extends 指类型参数上限，super指下限，Kotlin抛弃了这个，引入了生产者与消费者概念；

**生产者**：只能*读取*数据的对象；
**消费者**：只能*写入*数据的对象；

把只能保证读取数据时类型安全的对象叫做生产者，用 out T 标记；只保证数据写入时安全时的对象叫做消费者，用 in T 标记；

生产者（Producer）使用extends，消费者（Consumer）使用super。
 
```java
class Anim {}
class FlyAnim extends Anim{}
class Bird extends FlyAnim {}
class RedBird extends Bird{}

void test() {
	 List<? extends Anim> extendAnims = new ArrayList<Bird>();
    extendAnims.add(new Bird());        // 编译错误
    extendAnims.add(new RedBird());		// 编译错误

    List<? super FlyAnim> superAnims = new ArrayList<>();
    superAnims.add(new Bird());        // 正常编译
    // 以下，这里的 super 不是指父类的意思，而表示是，可以放父类，必须可以放其子类，多态
    superAnims.add(new Anim());
      
}
```
 
这么记忆：**out T 等价于 ? extends T, in T等价于 ? super T, * 等价于 ?**

也就是说如：`List<? extends Foo>`，在该对象上不允许调用`add()`或者`set`；

**补充内容：**

摘自：[!http://www.kotliner.cn/2017/06/26/kotlin-generics/]

`out T` 表示型变定义，即对于 Kotlin 中 Collection接口，Collection<Number>，也是 Collection<Int>的父类;  
 
>对于协变类型，我们通常是不允许其涉及泛型参数的部分被改变的；  
逆变的情形正好相反，即不可以将泛型参数作为方法的返回值


```java
public interface Collection<out E> : Iterable<E>	
```
而对于MutableList 来说，他的元素是不能变的

``` java
public interface MutableCollection<E> : Collection<E>, MutableIterable<E> {

```


**声明初型变**

Kotlin对java泛型，最大的改动是添加了声明处的型变；  
Java中以下写法编译出错：

```java
interface Source<T> {
       T next();
}
void demo(Source<String> strs) {
      // 需改成：Source<? extends Object> objs = strs;
      Source<Object> objs = strs;
}
```
因为Java不支持型变，`Source<String>`不是`Source<Object>`的子类型,不能把Source<String>类型的变量赋给Source<Object>,所以编译出错；		
用Kotlin改下：

```java
// out T做生产者说明，此接口，只有一个读取的方法，可视为生产者
// T 为协变的类型参数
interface Source<out T> {	
    fun next(): T
}

fun demo(strs: Source<String>) {
    val objs: Source<Any> = strs
}
```
`out`修饰符称为型变注解，生产；`in` 为参数逆变，只能被消费；

**类型投影**

指定只能调用其get方法的例子，不加 out 提示编译错误：  
如：Array<in String> 对应于 Java 的 Array<? super String>

```java
// 类型投影
fun copy(from: Array<out Any>, to: Array<Any>) {
    for (i in from.indices) {
        to[i] = from[i]
    }
}

fun main(args: Array<String>) {
    val ints: Array<Int> = arrayOf(1, 2, 3)
    val others: Array<Any> = arrayOf("","2","00")

    copy(ints, others)

    for(i in others.indices) {
        println(others[i])
    }
}
```

**星投影（不理解）**  
语法:  

1. 对于`Foo<out T>`,其中 T 是个具有上界的协变类型参数，`Foo<*>`等价于`Foo<out TUpper>`，当T未知时，可从Foo<*>读取TUpper的值；
2. 对于`Foo<in T>`,其中 T 是逆变类型参数，Foo<*> 等价于 Foo<in Nothing>；
3. 对于 `Foo<T>`,其中 T是一个具有上界 TUpper 的不型变类型参数，`Foo<*>`对于读取值等价于 `Foo<out Tupper>`，对于写时等价于`Foo<in Nothing>`

**泛型函数**   
不仅类可以有类型参数，函数也可以有，如：

```java
fun <T> single(item: T): List<T>? {
    return null
}
```

**泛型约束**   
上界，类似java中的extends:		
		
	// 冒号后指定的类型是上界：Comparable与其子类，默认的上界是 Any?
	fun <T : Comparable<T>> sort(list: List<T>) { 
	}
	
**嵌套类与内部类**

嵌套（类似Java中的static 内部类）：
   
	class Button : View {
    	override fun getCurrentState(): State = ButtonState()
    	override fun restoreState(state: State) {}
    	// inner class
    	class ButtonState : State {}
		}
	}
	
		// 使用：
		fun main(args: Array<String>) {
    		val state = Button.ButtonState()
		}
	}
	
	
内部类（内部类用 inner 标记，以便可访问外部类的成员，内部类会带有一个外部类的引用，类似于Java中的内部类）：
	
	class Outer {
    	private val bar: Int = 1

    	inner class Inner {
    	    fun test() = bar
    	}
	}

	fun main(args: Array<String>) {
    	Outer().Inner().test()  // 需要先创建外部类
	}  

匿名内部类(对象表达式 object 关键字)：
	
	view.addKeyEventListener(object : KeyEvent) {
		override fun onKeyup() {}
		override fun onKeyDown() {}
	}

	// 如果是接口，可使用带有接口类型前缀的lambda表达式创建
	val listner = ActionListener {println()}
	
	

	
###枚举类

枚举类最基本的用法是保证实现类型安全；每一个枚举常量都是一个对象；
 
	enum class Color(val r: Int, val g: Int, val b: Int) {
    	RED(255, 0, 0), ORANGE(255, 265, 0),
    	YELLOW(255, 255, 0), GREEN(0, 255, 0),
    	BLUE(0, 0, 255);        // 注意分号

    	fun rgb() = (r * 256 + g) * 256 + b     // 枚举中，定义的方法

    	// when 代替的 switch
    	fun mix(c1: Color, c2: Color) =
            when (setOf(c1, c2)) {
                setOf(RED, YELLOW) -> ORANGE
                setOf(YELLOW, BLUE) -> GREEN
                else -> throw Exception("not support")
          }

    	fun mix2(c1: Color, c2: Color) =
            // 不使用参数
            when {
                (c1 == RED && c2 == YELLOW ||
                        c1 == YELLOW && c2 == RED) -> ORANGE
                (c1 == YELLOW && c2 == BLUE
                        || c1 == BLUE && c2 == YELLOW) -> GREEN
                else -> throw Exception("not support")
            }
		}


###对象

不显式声明新的之类来实现一些功能，常见监听各种按钮事件，在Java中，用匿名内部类来处理；在Kotlin中用**对象表达式和对象声明**来概括；

**对象表达式 （object 关键字）**	
	
创建匿名内部类对象的写法

	view.addKeyEventListener(object : KeyEvent) {
		override fun onKeyup() {}
		override fun onKeyDown() {}
	}

如果超类有构造，需要传递合适的参数；多个超类型可以用逗号分隔来指定
	
	open class A(x: Int) {
    	open val y: Int = x
	}

	interface B {}

	val ab: A = object : A(1), B {	// 多个超类
    	override val y = 15
	}

任何时候，如需要一个对象（无超类型时），可这么写：
	
	val addHoc = object {
        var x: Int = 0
        val y: Int = 0
    }
	
**对象声明（object来实现单例）**
	
	object Payroll {
    	val allEmployees = arrayListOf<Person>()

    	fun calculateSalary() {
        	for (p in allEmployees) {
        	}
    	}
	}

也可以添加超类型，比如,

	// 单例
	object CaseInsensitiveComparator : Comparator<String> {
    	override fun compare(o1: String, o2: String): Int {
        	return o1.compareTo(o2, ignoreCase = true)
    	}
	}

	fun main(args: Array<String>) {
    	println(CaseInsensitiveComparator.compare("abc", "ABC"))
    	val list = listOf<String>("a","d", "b","g", "c")
    	println(list.sortedWith(CaseInsensitiveComparator))
	}


**伴生对象 （compainion object）**

在Kotlin，没有直接 static关键字，要想实现类似于Java 中 static ，如：static方法，我们 使用 `compainion` 关键字

	/**
 	* 类似Java中的静态方法
 	*/
	class A {
    	companion object {
    	    fun method() {
    	        println(this)
    	        println(" Companion object called")
    	    }

        	@JvmStatic		// 生成真正的静态
        	fun realStaticMethod() {
            	println("Companion object called")
        	}
    	}

    	var str:String = "better"
        	set(value) {
        	    field = value
       	 }
	}

	class B {
    	companion object {
	
    	}
	}

	fun main(args: Array<String>) {
    	A.method()      // 可直接访问，类似java中 static
    	A.realStaticMethod()

    	val a = A()
    	a.str = "555"
    	println(a.str)
	}

**注意：** 伴生对象看上去有点像其他语言的 静态成员， 但实际在运行时，仍然是真是对象的实例成员，而且，还可以实现接口，如果在JVM上，可以使用`@JvmStatic`注解，这样会生成真正的静态方法与字段；

**对象表达式和对象声明之间的语法差异**

对象表达式用来创建匿名内部类， 对象声明用来创建类似于单例的使用，有区别：

- 对象表达式（匿名内部类）在使用它们的地方立刻执行；
- 对象声明 （单例）是在第一次访问时延迟初始化；
- 伴生对象（static）的初始化，是在相应的类被加载时，初始化，类似java静态初始化；

### 委托

委托有点类似于装饰设计模式，但比装饰显得更加完美，减少了继承，实现了对原方法的点缀；
如下代码，添加变量，用来记录添加元素的个数（例子不实用，但可说明委托的意图）
	
	class CountingSet<T>(val innerSet: MutableCollection<T> = HashSet<T>()) :
	 	MutableCollection<T> by innerSet {

    	// 1.委托MutableCollection的实现给innerSet
    	var objectsAdded = 0

    	override fun add(element: T): Boolean {
        	objectsAdded++
        	return innerSet.add(element)
    	}

    	override fun addAll(elements: Collection<T>): Boolean {
        	objectsAdded += elements.size
        	return innerSet.addAll(elements)
    	}
	}

	// 实用
	fun main(args: Array<String>) {
    	val cset = CountingSet<Int>()
    	cset.addAll(listOf(1, 3, 3))
    	println("${cset.objectsAdded} objects were added. ${cset.size} remain")
	}

通过 `by` 子句，将 `add` `addAll` 转给了内部委托 `innerSet`;其余方法，还是走老的；

另一个例子(记录登陆次数)：

	interface Login {
    	fun doLogin()	
	}

	class LoginImpl : Login {
    	override fun doLogin() {
    	    println("执行了doLogin方法...")
    	}
	}

	class CountLogin(val login: LoginImpl) : Login by login {
    	var count = 0
    	override fun doLogin() {
    	    count++
    	    login.doLogin()
    	    println("次数： ${count}")
    	}
	}


### 委托属性
	
- 延迟属性（lazy properties）: 某值只在首次访问时初始化；
- 可观察属性：监听器会收到有关此属性变更的通知；
- 把多个属性存储在一个映射中；

语法为：  
`val/var<属性名>：<类型> by <表达式>`，在by后面的表达式是 该委托，属性对应的setter于getter会被委托给它的 `getValue` `setValue` 方法

	class Example {
	    var p: String by Delegate()
	}

	class Delegate {
    	operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
    	    return "$thisRef, '${property.name}' to me"
    	}

    	operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
     	   println("$value assigned to '${property.name} in $thisRef'")
    	}
	}

	fun main(args: Array<String>) {
    	val e = Example()
    	// 监听属性的 get 与 set
    	println(e.p)
    	e.p = "better"
	}

**延迟属性Lazy**   

`lazy()`是接受一个lambda并返回一个`Lazy<T>`实例的函数，返回的实例可作为实现延迟属性的委托：第一次调用`get()`会执行已传递给`lazy()`的Lambda表达式并记录结果，后续调用，直接返回记录的结果；

	val lazyValue: String by lazy {
    	println("computed")
    	"better"
	}

	fun main(args: Array<String>) {
   	 	println(lazyValue)
   		println(lazyValue)
    }
默认情况下，对`lazy属性`的求值，是同步锁的；如果确定不需要同步，可以添加其他模式来取消；

**可观察属性Observable**

`Delegates.observable()`接受2个参数：初始值和修改时的处理程序，每当属性被赋值时，会调用处理程序；3个参数：被赋值的属性、旧值和新值；

	class User {
    	var name: String by Delegates.observable("<no name>") {
        	prop, old, new ->
        	println("$old -> $new")
    	}
	}

	fun main(args: Array<String>) {
    	val user = User()
    	user.name = "better"
    	user.name = "best"
	}
	
输出：
	
	<no name> -> better
	better -> best
	
**把属性存储在映射中**

类似解析Json的`动态`事情应用上，可以使用映射实例自身作为委托来实现委托属性。

	class MyUser(val map: Map<String, Any?>) {
    	val name: String by map  // 使用映射实例自身作为委托来实现委托属性。
    	val age: Int by map
	}

	fun main(args: Array<String>) {
		
		// 委托属性将从映射中取值（字符串键 - 属性的名称）
    	val user = MyUser(mapOf(
            "name" to "better",
            "age" to 25
    	))

   	 	println(user.name)
   	 	println(user.age)
	}

**委托属性要求**

- 对于一个只读属性（val 声明的），委托必须提供一个名为 `getValue`的函数，该函数接受以为参数：
	1. thisRef - 必须与 属性所有者类型相同或其超类型；
	2. property - 必须是类型 KProperty<*>或其超类型；

- 对于可变属性（var 声明的），委托提供额外的名为 setValue 的函数，参数如下：
	1. thisRef - 同上
	2. property - 同上
	3. new value - 必须和属性类型相同；
	
getValue 和 setValue 函数都需要用 `operator` 关键字来标记；

