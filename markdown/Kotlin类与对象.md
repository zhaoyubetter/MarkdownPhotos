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

####类成员包括
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
在JVM上，可以使用注解 `@JVMStatic`可将伴生对象成员，生成真的静态；