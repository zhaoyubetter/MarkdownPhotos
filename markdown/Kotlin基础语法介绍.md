## Kotlin 基础语法
## Kotlin 的基础语法

###定义变量###
- val 表示不可更改的,类似java中的final；
- var 可更新的；
		
		val a:Int = 55	 // final
		var b:Int = 100	

### 空的操作
####1. 允许为null的定义(Any?)
	
	val a:Int? = null   // Int? 表示a可以为null

####2.if not null 缩写(?.)
	var str: String? = null     // ?表示允许为null
    println(str?.length)  // null
####3.if not null and else 缩写(?. ?:)
	var str: String? = null     // ?表示允许为null
	println(str?.length ?: "empty")     // 为null打印empty
####4.if null (?:)缩写
	val data = mapOf("a" to 1, "b" to 2)
    val c = data["c"] ?: throw IllegalAccessException("c is missing!")


###定义集合
集合用的是java中的集合，定义方式如下：

- list：listOf()
- set: setOf()
- map: mapOf()

>
	val items = listOf("apple", "android", "windows")
    for (i in items) {
       	println(i)
   	}
   	// 根据索引来
   	for (index in items.indices) {
       	println("item at ${index} is ${items[index]}")
   	}
	// map
	val map = mapOf("a" to 1, "b" to 2, "c" to 3)       // 创建map
    println(map["a"])

###区间操作(range)
	for (x in 0..5) {        // [0,5]
        println(x)
    }

    for (x in 1..20 step 2) {	// 步进2
        println(x)
    }

    for (x in 9 downTo 0 step 3) {
        println(x)
    }

###when操作
when类似switch，但是比switch更为强大，

	fun describe(obj: Any): String =
        when (obj) {
            1 -> "one"
            "Hello" -> "Greeting"
            is Long -> "Long"
            !is String -> "Not a string"
            else -> "Unknown"
        }

	fun main(args:Array<String>) {
    	println(describe(1))
    	println(describe("Hello"))
    	println(describe(10000L))
	}

### filter 过滤

	val brand = listOf("apple", "android", "windows", "linux")
    brand
            .filter { it.startsWith("a") }
            .sortedBy { it }
            .map { it.toUpperCase() }
            .forEach { println(it) }

### Lambda表达式
```
fun main(args: Array<String>) {
    // 类似于函数调用
    sayHello.invoke("better", "hello world")
}

val sayHello = { name: String, msg: String ->
    println("$name say $msg to you")
}
```