## 类和对象 扩展

Kotlin可对一个类的属性或方法进行扩展，不需要继承，或使用`Decorator`模式

[装饰模式](http://www.jianshu.com/p/0d8bb988d0a8)

### 扩展函数
扩展函数可在已有类中添加新方法，不会对原类进行改动；定义格式如：

	fun Type.functionName(params) {
		// method body
	}

其中：

1. Type：表示函数扩展的对象；
2. functionName： 扩展函数名称；
3. params:参数；