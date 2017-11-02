#Groovy的简单入门
### 参考信息：
1. http://www.jianshu.com/p/777cc61a6202

### 简介
Groovy是运行在JVM上的编程语言，可以说是一种运行在jvm中的脚本语言，Groovy可以与Java无缝衔接互相调用，因为groovy编译出来的也是Java字节码；

我们这里定义Groovy为脚本语言；
脚本语言目标是将应用程序连接起来实现一些繁琐、变动频繁的细节；如：copy文件、打包等等；用少量的代码，实现相当强大的功能；

比如： Gradle
这个构建工具就是基于Groovy来编写而成的，大有替换传统的Maven、Ant等构建工具的趋势；

### 开发环境的搭建
>以为为Windows平台
>
1. 当然是JDK的配置；
2. 下载Groovy sdk，地址为：[groovy sdk](http://groovy-lang.org/download.html)；下载好了，直接解压；
3. 配置Groovy的环境变量； 建立`GROOVY_HOME`，值为上解压目录；
	编辑Path追加 `%GROOVY_HOME%\bin`
4. 验证：在命令行输入：`groovy -v`提示OK，表示环境成功了；

### Hello world的编写
命令行输入：`groovyconsole` 可进入Groovy自带的编写器，来体验一把！

如下：

![Hello World](https://github.com/zhaoyubetter/MarkdownPhotos/raw/master/img/groovy1.jpg)

当然了，我们肯定要使用 idea 这个东东；

略！

# 基础语法

####1. 变量 OR 方法定义

使用 `def` 来定义变量,或者方法, 变量的定义不需要具体的类型；groovy的语句结束，不需要添加分号；

如下：

```java

	
	def a = 'hello'  
	println(a)
	
	def fun(c) {   // c 为动态类型
		println(c)
	}
	
```

####2.方法返回值
groovy 方法的最后一行即为方法的返回值，可省略掉return 语句；

```java
	
	@Test
	void testFunc() {
        println(groovyFunc(1,2))        // 输出 1 2
    }

    def groovyFunc(arg1, arg2) {
        println arg1
        println arg2
        arg1 + " " + arg2    // 最后一行是返回值
    }
```


# Groovy 列表、映射和范围
这是Groovy最常用的数据类型了；

###列表
用来存储数据项的有序集合，并且集合可以嵌套，而且可以包含不同类型的数据项；
如下：



| 例子        | 说明           | 
|:-------------:|:-------------:| 
| [1,2,3,4]     | 整形列表 | 
| ['better','Joy',124]      | 不同类型的列表 |   
| ['better','Joy',[124,11,33]]      | 不同类型的列表 |

