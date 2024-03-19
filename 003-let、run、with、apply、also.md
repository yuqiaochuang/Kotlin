目录

- [1. run 和 apply 函数](#1-run-和-apply-函数)
	- [1.1 run 函数](#11-run-函数)
	- [1.2 apply 函数](#12-apply-函数)
	- [1.3 run 和 apply 函数的共同点是](#13-run-和-apply-函数的共同点是)
	- [1.4 使用场景](#14-使用场景)
	- [1.5 示例](#15-示例)
- [2. with 函数](#2-with-函数)
	- [2.1 with 函数](#21-with-函数)
	- [2.2 使用场景](#22-使用场景)
- [3. let 函数 和 also 函数](#3-let-函数-和-also-函数)
	- [3.1 let 函数](#31-let-函数)
	- [3.2 also函数](#32-also函数)
	- [3.3 let和also函数的共同点](#33-let和also函数的共同点)
	- [3.4 let函数的使用场景](#34-let函数的使用场景)
	- [3.5 also 的使用场景](#35-also-的使用场景)
	- [3.6 总结](#36-总结)
- [4. takeIf 和 takeUnless 函数](#4-takeif-和-takeunless-函数)
	- [4.1 takeif 函数](#41-takeif-函数)
	- [4.2 takeUnless 函数](#42-takeunless-函数)
	- [4.3 takeIf函数](#43-takeif函数)

<p>
Kotlin 标准库提供了几个函数：let、run、with、apply 以及 also，它们的唯一目的是在对象的上下文中执行代码块。当对一个对象调用这样的函数并提供一个 lambda 表达式时，它会形成一个临时作用域，在此作用域中，可以访问该对象而无需其名称，这些函数称为作用域函数。
</p>

这些函数的

- 相同点：在一个对象上执行一个代码块。
- 不同点：这个对象在代码块中如何使用，以及整个表达式的返回结果是什么。

# 1. run 和 apply 函数

## 1.1 run 函数

  上下文对象是 lambda 表达式的接收者，执行一个 lambda 表达式并返回 lambda 表达式的结果

```kotlin
public inline fun <T, R> T.run(block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block()
}
```

## 1.2 apply 函数

  上下文对象是 lambda 表达式的接收者，执行一个 lambda 表达式并返回 上下文对象

```kotlin
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

## 1.3 run 和 apply 函数的共同点是

上下文对象都作为 lambda 表达式的接收者 this, 只是返回结果不同, run 函数返回 lambda 表达式的结果, apply 函数返回上下文对象本身。

## 1.4 使用场景

- run 和 apply 函数都可以理解为对一个对象进行赋值或执行某些操作，都可以并常用于对象的初始化。
- run 函数返回值是 lambda 表达式的结果，所以常用于执行一些（初始化）操作后并要求返回值的场景。
- apply 函数返回值是上下文对象本身，所以可以用于链式调用中，对调用链中的某个环节的对象执行一些操作。
  
## 1.5 示例

示例1：如下是一个初始化操作，对返回值没有什么要求，我们可以使用run也可以使用apply函数，这是没有区别的。

```kotlin
binding.recyclerView.run {
  layoutManager = LinearLayoutManager(this)
  addOnItemTouchListener(RecyclerView.SimpleOnItemTouchListener())
}
```

或者

```kotlin
binding.recyclerView.apply{
  layoutManager = LinearLayoutManager(this)
  addOnItemTouchListener(RecyclerView.SimpleOnItemTouchListener())
}
```

示例2：如下，我们需要对一个Person执行一些操作，并把年龄增加10岁，最后把增加10岁的年龄返回赋值给一个常量，这里我们不能用apply，只能用run，因为我们不需要Person本身，而是增加后的年龄，也就是lambda 表达式的结果

```kotlin
data class Person(var name: String, var sex: String, var age: Int) {
  override fun toString(): String {
    return "姓名：$name , 性别：$sex , 年龄:$age"
  }
}

val user = Person("小慧", "女", 18)
val ageOlder = user.run {
  age += 10
  Log.e("时光流逝", "$name 的年龄增加了10岁")
  age
}
```

示例3：如下，我们进行链式调用，中途对 Person 对象进行修改，最后打印修改后的Person 对象，这时候我们需要使用 apply，而不是 run, 因为我们需要的是 Person 对象本身，而不是 lambda 表达式的结果

```kotlin
data class Person(var name: String, var sex: String, var age: Int) {
  override fun toString(): String {
    return "姓名：$name , 性别：$sex , 年龄:$age"
  }
}
fun String.logE(tag: String) {
  Log.e(tag, this)
}

val user = Person("小慧", "女", 18)
user.apply {
name = "小米"
sex = "男"
age = 20
}.toString().logE("人物信息")
```

# 2. with 函数

## 2.1 with 函数

指定 lambda 表达式的接收者，执行一个 lambda 表达式并返回 lambda 表达式的结果，和 run 函数很相似，

```kotlin
public inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return receiver.block()
}
```

## 2.2 使用场景

对某个对象执行某些操作后获取一个结果，语义更强，使用场景是引入一个辅助对象，其属性或函数将用于计算一个值。
案例：上面的代码就可以修改成 with 函数，利用 user 计算 ageOlder 。

```kotlin
val ageOlder = with(user) {
  age += 10
  Log.e("时光流逝", "$name 的年龄增加了10岁")
  age
}
```

# 3. let 函数 和 also 函数

## 3.1 let 函数

上下文对象是 lambda 表达式的参数，执行一个 lambda 表达式并返回 lambda 表达式的结果

```kotlin
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

## 3.2 also函数

  上下文对象是 lambda 表达式的参数，执行一个 lambda 表达式并返回 上下文对象本身

```kotlin
public inline fun <T> T.also(block: (T) -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block(this)
    return this
}
```

## 3.3 let和also函数的共同点

上下文对象都作为lambda 表达式的参数it，只是返回结果不同，let函数返回lambda 表达式的结果，also函数返回上下文对象本身。

## 3.4 let函数的使用场景

- 1.在调用链的结果上调用一个或多个函数：
  
官方示例：

```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
val resultList = numbers.map { it.length }.filter { it > 3 }
println(resultList)
```

替换为：

```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
numbers.map { it.length }.filter { it > 3 }.let { 
    println(it)
    // 如果需要可以调用更多函数
} 
```

- 2.let 经常用于仅使用非空值执行代码块。如需对非空对象执行操作，可对其使用安全调用操作符 ?. 并调用 let 在 lambda 表达式中执行操作。

官方示例：

```kotlin
val str: String? = "Hello" 
//processNonNullString(str)       // 编译错误：str 可能为空
val length = str?.let { 
    println("let() called on $it")
    processNonNullString(it)      // 编译通过：'it' 在 '?.let { }' 中必不为空
    it.length
}
```

- 3.使用 let 的另一种情况是引入作用域受限的局部变量以提高代码的可读性。如需为上下文对象定义一个新变量，可提供其名称作为 lambda 表达式参数来替默认的 it。

官方示例：

```kotlin
val numbers = listOf("one", "two", "three", "four")
val modifiedFirstItem = numbers.first().let { firstItem ->
    println("The first item of the list is '$firstItem'")
    if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
}.toUpperCase()
println("First item after modifications: '$modifiedFirstItem'")
```

## 3.5 also 的使用场景

also 对于执行一些将上下文对象作为参数的操作很有用。 对于需要引用对象而不是其属性与函数的操作，或者不想屏蔽来自外部作用域的 this 引用时，请使用 also。

官方示例：

```kotlin
val numbers = mutableListOf("one", "two", "three")
numbers
    .also { println("The list elements before adding new one: $it") }
    .add("four")
```

## 3.6 总结

let、run、with、apply 以及 also 的用法很相似，很多时候它们完全可以相互替换使用，具体的使用规范可以按照个人理解和公司的开发规范。
例如：

- run 和 apply 一般用于初始化的操作（更倾向于赋值操作），因为 apply 返回上下文对象本身，所以 apply 也常用于链式调用中，在链式调用中给链中的某个对象赋值修改等操作。
- with 一般用于根据某个对象计算处理后的结果赋值给另一个对象，和 run 很像
- let 三种官方文档的用法：
  1. 在调用链的结果上调用一个或多个函数
  2. 配合安全调用操作符 ?. 执行非空的操作
  3. 引入作用域受限的局部变量以提高代码可读性
- also 用于需要引用对象而不是其属性与函数的操作，和 apply 很像并且很难区分：apply 的上下文对象是 lambda 表达式的接收者，also 的上下文对象是 lambda 表达式的参数；并且 apply 多用于上下文对象的变量的赋值计算，而 also 用于上下文对象本身的操作。其实两者的界限完全可以由自己的理解或规范决定。

# 4. takeIf 和 takeUnless 函数

## 4.1 takeif 函数

```kotlin
public inline fun <T> T.takeIf(predicate: (T) -> Boolean): T? {
    contract {
        callsInPlace(predicate, InvocationKind.EXACTLY_ONCE)
    }
    return if (predicate(this)) this else null
}
```

## 4.2 takeUnless 函数

```kotlin
public inline fun <T> T.takeUnless(predicate: (T) -> Boolean): T? {
    contract {
        callsInPlace(predicate, InvocationKind.EXACTLY_ONCE)
    }
    return if (!predicate(this)) this else null
}
```

## 4.3 takeIf函数

<p>
上下文对象是 lambda 表达式的参数，lambda 表达式返回一个 Boolean 值，如果是true, 返回上下文对象本身，否则返回 null 。也就是对上下文对象执行一段逻辑，如果逻辑为 true, 返回上下文对象，逻辑为 false, 返回 null, takeUnless 函数与 takeIf 函数逻辑相反。应用场景不用想：数据按条件进行过滤。
官方示例：
</p>

```kotlin
val number = Random.nextInt(100)

val evenOrNull = number.takeIf { it % 2 == 0 }
val oddOrNull = number.takeUnless { it % 2 == 0 }
```

当在 takeIf 及 takeUnless 之后链式调用其他函数，不要忘记执行空检查或安全调用（?.），因为他们的返回值是可为空的。
————————————————

版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：<https://blog.csdn.net/weixin_43864176/article/details/128483725>
