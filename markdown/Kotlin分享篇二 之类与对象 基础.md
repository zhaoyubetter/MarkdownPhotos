## 类和对象
##### 使用 class 关键字来定义类，如果一个类，没有类体使用Empty

如：

	class Myclass Empty

### 构造函数
**主构造函数**

kotlin 中可有一个*主构造函数* 和 多个次构造函数，主构造为类的一部分，如主构造函数没有任何注释or修饰,可省略 `constructor` 关键字：
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

在次要构造中，委托调用同一个类的构造用**this**
	
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

>注意：在 JVM 虚拟机中，如果主构造函数的所有参数都有默认值，编译器会生成一个附加的无参的构造函数

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

- 构造函数中，初始化基类

		class MyView : View {
			constructor(ctx: Context)：super(ctx)
			constructor(ctx:Context, attrs:AttributeSet) : super(ctx, attrs)	
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
		
#### 嵌套类与内部类

嵌套（类似Java中的`static` 内部类）：
   
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
	
	
**内部类**（内部类用 inner 标记，以便可访问外部类的成员，内部类会带有一个外部类的引用，类似于Java中的内部类）：
	
	class Outer {
    	private val bar: Int = 1
    	var prop = "成员属性"

       inner class Inner {
        fun test() = bar    // 直接访问外部类成员
        	fun innserTest() {
            	var outer = this@Outer
            	println("访问外部类成员，${outer.prop}")
        	}
    	}
	}

**匿名内部类**(对象表达式 `object` 关键字)：

使用对象表达式来创建匿名内部类
	
	view.addKeyEventListener(object : KeyEvent) {
		override fun onKeyup() {}
		override fun onKeyDown() {}
	}

	// 如果是接口，可使用带有接口类型前缀的lambda表达式创建
	val listner = ActionListener {println()}

其他例子：
	
	class Test {
    	var v = "成员属性"
    	fun setInterFace(test: TestInterFace) {
        	test.test()
    	}
	}

	interface TestInterFace {
    	fun test()
	}

	fun main(args: Array<String>) {
    	var test = Test()
    	// 采用对象表达式来创建接口对象，即匿名内部类的实例。
    	test.setInterFace(object :TestInterFace {
        	override fun test() {}
    	})
	}

### 类的修饰符

类的修饰符包括 classModifier 和_accessModifier_:

**classModifier: 类属性修饰符，标示类本身特性。**

- abstract    // 抽象类  
- final       // 类不可继承，默认属性
- enum        // 枚举类
- open        // 类可继承，类默认是final的
- annotation  // 注解类

**accessModifier: 访问权限修饰符**

- private    // 仅在同一个文件中可见
- protected  // 同一个文件中或子类可见
- public     // 所有调用的地方都可见
- internal   // 同一个模块中可见

### 继承

Kotlin中所有的类都继承自`Any`,Any类类似于Java中的Object；

Any 类默认提供了3个函数：

> 1. equals()
> 2. hashCode()
> 3. toString()

Java 的Object 也有上面的3个方法；

一个类要被继承，需要使用`open`关键字修饰；

#### 继承时，构造函数的处理
	
> 子类有主构造函数，则基类必须在主构造函数中立即初始化

	open class Person(var name: String, var age: Int) {}

	class Student(name: String, age: Int, var score: Int) :
        Person(name, age) {}

> 子类没有主构造，则需用super调用基类构造

	class Teacher : Person {
    	var tech: String = ""
    	constructor(name: String, age: Int) : super(name, age)
    	constructor(name: String, age: Int, tech: String) :
            this(name, age) {
        	this.tech = tech
    	}
	}

#### 方法重写

不同于Java，Kotlin中，使用fun声明函数时，函数默认为final修饰，如果允许子类重写，则需要添加`open`修饰它，子类重写时使用`override`
关键字；
	
	open class Person(var name: String, var age: Int) {
    	fun doSth() {}
    	open fun doOthers() {}
	}

	class Student(name: String, age: Int, var score: Int) :
        Person(name, age) {
    	override fun doOthers() {
    	    super.doOthers()
    	}
	}


> 有多个相同的方法，需要覆盖时,使用`super<泛型>`去选择性地调用父类实现

	open class A {
    	open fun doSth() {
        	println("A.doSth() called...")
    	}
	}

	interface B {
    	fun doSth() {
        	println("B.doSth() called...")
    	}
	}

	class C : A(), B {	// 注意A()，调用A的构造
    	override fun doSth() {
        	super<A>.doSth()
        	super<B>.doSth()
    	}
	}

	fun main(args: Array<String>) {
    	val c = C()
    	c.doSth()
	}

#### 属性重写
属性重写使用 override 关键字，属性必须具有兼容类型，每一个声明的属性都可以通过初始化程序或者getter方法被重写：

可以用一个var属性重写一个val属性，但反过来不行。因为val属性本身定义了getter方法，重写为var属性会在衍生类中额外声明一个setter方法

	open class Foo(open val age: Int) {
    	open val x: Int
        	get() {
           	 return 1
        	}
	}

	class Bar1(override var age: Int) : Foo(age) {
    	override val x: Int = 20
	}

#### Kotlin 接口
接口使用 `interface`关键字定义，类似于java 8 ,允许有默认实现；
	
	interface MotionListener {
    	fun onDown()
    	fun onMove() {
        	println("onMoving")
    	}
    	fun onUp()
	}

类实现接口用 `:`冒号；

#### 接口中的属性
接口中的属性是抽象的，不能被初始化，这跟Java不一样，实现接口时，需要重写属性；

	interface MotionListener {
    	var name: String
    	fun onMove() {
        	println("onMoving")
    	}

    	fun onUp()
	}

	// 属性重写
	class Impl(override var name: String) : MotionListener {
    	override fun onUp() {
    	}
	}

通过反编译代码，我们可以看到属性，实际上会生成对应的方法；	