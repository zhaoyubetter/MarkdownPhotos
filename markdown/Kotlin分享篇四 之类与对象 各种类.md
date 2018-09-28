title: Kotlin里的类和对象 之扩展
speaker: 赵宇
transition: slide3
theme: moon
usemathjax: yes

[slide]
#各种类

#数据类
* 什么是数据类（data）？
* 有啥特性？
	* equals() / hashCode()
	* toString()
	* copy()
	* componentN() 对应属性
* 定义数据类的约束是什么？
	* 构造函数必须有一个参数
	* 所有参数必须标记为val or var；
	* 数据类不能被继承 （final类型）；
* 数据类与解构声明？
	* 解构声明：val(name, age) = data
	










