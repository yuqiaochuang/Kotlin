
varargs可变参数，相当于java的...

变量参数（也称为varargs）是Kotlin语言的一项功能，它允许你将一些数值作为单个参数变量传递给一个函数。
在定义一个函数时，你通常需要在函数的括号内指定函数可以接收的每个参数的名称。
下面的例子显示了一个简单的Kotlin函数，它将两个数字相加。

```kotlin
fun sumNumbers(a: Int, b: Int): Int {
return a + b
}
sumNumbers(2, 3) // 5
```

现在当你想对更多的数字求和时，你需要在函数定义中添加另一个参数。
下面的例子在sumNumbers 函数中添加了另一个参数c 。

```kotlin
fun sumNumbers(a: Int, b: Int, c: Int): Int {
return a + b + c
}
sumNumbers(2, 3, 4)
```

这可能是一个问题，因为如果我们有更多的数字要相加，那么我们就需要给函数本身增加更多的参数。

解决这个问题的一个方法可能是将数字转换成一个数组。
这样，你就可以向函数传递一个IntArray 类型的单一参数，并调用sum() 函数，而不是一个一个地传递它们。
考虑一下下面的代码例子。

```kotlin
fun sumNumbers(numbers: IntArray): Int {
return numbers.sum()
}
val numArray = intArrayOf(1, 2, 3, 4)
sumNumbers(numArray) // 1+2+3+4 = 10
```

varargs类似于数组的工作方式。它允许你传递一个包含多个值的单一变量。

但是当使用vararg时，你不需要创建一个数组。你可以传递你的值，用逗号隔开，就像正常的参数一样。

要定义一个参数变量，你需要在参数名称前添加vararg 关键字，如下图所示。
```kotlin
fun sumNumbers(vararg numbers: Int): Int {
return numbers.sum()
}
sumNumbers(1, 2, 3, 4, 5) // 15
```

请注意，在numbers 参数前添加了vararg 关键字，在sumNumbers() 调用过程中，要求和的值就像普通参数一样被传递。

这就是Kotlin中varargs的强大之处。它允许你将一个单一的变量定义为一个可以接受多个值的函数参数。

一个vararg 参数将在函数内部可用，就像它是一个Array 类型的数据。

在上面的例子中，你可以调用同样的sum() 函数，该函数可用于IntArray 类型。

这里是另一个将许多String 值串联为一个的例子。
```kotlin
fun concatWords(vararg words: String): String {
return words.joinToString(separator = ".. ")
}
concatWords("Banana", "Apple") // Banana.. Apple
```
一个vararg 参数通常被声明为函数定义中的最后一个参数，其他参数在它前面。

在下面的例子中，name 和age 参数将从函数调用的参数中占据第一个和第二个位置。
```kotlin
fun concatWords(name: String, age: Int, vararg words: String) {
println("$name is $age years old")
println("Favorite words:")
println(words.joinToString(separator = "// "))
}
concatWords("Nathan", 28, "Love", "Loyalty")
/*
Output:
Nathan is 28 years old
Favorite words:
Love// Loyalty
*/
```
你可以把vararg 参数放在其他参数之前，但你需要用命名参数语法来传递后续参数的参数。

考虑一下下面的例子。注意，在函数调用中，name 和age 参数是在words 参数之后传递的。
```kotlin
fun concatWords(vararg words: String, name: String, age: Int) {
 println("$name is $age years old")
println("Favorite words:")
println(words.joinToString(separator = "// "))
}
concatWords("Love", "Loyalty", name = "Nathan", age = 28)
```
如果你确实已经有了一个Array 类型，并且想把它传到vararg 参数中，你可以通过在Array 值之前使用传播操作符* 来实现。

下面的例子显示了如何将一个IntArray 传递到一个vararg 参数中。
```kotlin
val numArray = intArrayOf(1, 2, 3, 4, 5)
fun sumNumbers(vararg numbers: Int): Int {
return numbers.sum()
}
sumNumbers(*numArray) // 15
```
传播操作符* ，将数组的值拉出来，这样，这些值就像多个参数一样被传递。

最后，一个Kotlin函数只能有一个vararg 参数。

你不能添加两个或更多的vararg参数，因为Kotlin编译器会在编译时抛出一个错误。

作者：迪鲁宾
链接：https://juejin.cn/post/7123084805243142180
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。