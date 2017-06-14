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
- 构造函数中，使用**super**初始化基类

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

