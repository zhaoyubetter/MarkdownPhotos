## 类和对象
##### 使用 class 关键字来定义类，如果一个类，没有类体使用Empty
如：

	class Myclass Empty

##### 构造函数
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

#### 创建类的实例
调用构造函数，kotlin不用new来创建对象
	
	val person = Person()

####类成员包括
- 构造函数和初始化块
- 函数
- 属性
- 嵌套类和内部类
- 对象声明

#### 继承
Kotlin 所有类的超类是 Any，类似于Java的Object；继承使用 冒号 : ;

- 显示的超类型 **(open标注表示该类可以允许被继承，与java中的final 相反)**

		open class Base(p:Int)
		class Child(p:Int):Base(p)	// 继承自Base类
- 构造函数中，使用super初始化基类

		class MyView : View {
			constructor(ctx: Context)：super(ctx)
			constructor(ctx:Context, attrs:AttributeSet) : sper(ctx, attrs)	
		}


