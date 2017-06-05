

----
----
----
----
----


## Kotlin 枚举
枚举类里的每一个常量，都是一个对象，常量用逗号分开；  

####示例：

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