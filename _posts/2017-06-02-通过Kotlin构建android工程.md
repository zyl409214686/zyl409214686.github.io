---
layout: post
title: 通过Kotlin构建android工程
categories: Kotlin
description: 
keywords: Kotlin
---

####介绍

Kotlin是JetBrains创造的一种编程语言，IntelliJ IDEA也是这个公司的。它是专为大型软件项目而设计，旨在改进Java，重点是可读性，正确性和提升开发人员的生产力。
Kotlin是为了应对Java中的一些限制而创建的，这些限制阻碍了JetBrains软件产品的开发，并且对于其它所有JVM语言的评估也被证明是不合适的。 由于Kotlin的目标是用于改进产品，因此它非常强调与Java代码和Java标准库的互操作。

####特性
以下是为Android提供最大价值的特性。并解决了困扰客户端应用程序开发的特定问题。 有关特性的全面列表，请参阅[官方Kotlin参考文档](http://kotlinlang.org/docs/reference/)。

1. 互操作性
  Kotlin语言和运行时间的最重要特征是其核心关注互操作性。与其他JVM替代语言（特别是Scala）不同，Kotlin能够轻松地调用Java，并且Java轻松地调用Kotlin。事实上，你绝不会知道你正在向任何一个方向跨越边界。Kotlin的运行时只是为了支持语言特性，使其非常精益化。Java标准库类型，集合等都被重用，最终通过一些后续提到的特性增加了更多的实用性。(更多有关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/java-interop.html))

2. Lambdas
  在此不做介绍。(更多有关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/lambdas.html))

3. 空类型安全
  我们最好的朋友null是Kotlin系统类型的头等类型。类型知道它们的可空性，并且在流控制和解引用中都受到特殊的处理。
```
val x: String? = "Hi"
x.length // 编译不通过
val y: String = null // 编译不通过
//y 是null值是不可避免的, 但有很多方法可以处理它.
if (x != null) {
  x.length // 编译通过! 不符合习惯，只是为了获取长度!
}
// 与上面的相同(IntelliJ 建议使用的).
x?.length
// Elvis 操作符.
val len = x?.length ?: -1
val len = x!!.length // 如果为null将抛出空指针异常，很少使用。
```

类型系统中有null值就像在Java中一样，是对可空性注解有很大优势的。在这种情况下，IDE和编译器都严格执行契约而不是仅仅是提示。
通过将可空性推送到类型系统中，不需要诸如可选的传统解决方法。这在Android上尤其重要，因为它的堆和垃圾收集器对于微小的和/或短生命周期的对象更加敏感。Java 10将把可选引入堆栈，但我们在Android看到之都要等死了。
由于上述堆和GC问题，Android使用了大量的null。像很多情况一样，由于JAVA中所有的引用都可能为空，所以Android代码通常会被不必要的null检查或者由于取消引用某些不被认为是null的错误而导致崩溃。将其移动到类型系统中将消除这种歧义，消除嘈杂，不必要的检查和大多数疏忽的异常。（更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/null-safety.html)）

4.拓展方法
这类似于C＃的方式，Kotlin允许将静态和实例方法声明为不能控制的类型，包括来自Java标准库。然而，与C＃不同的是，这些方法使用导入方式进行静态解析，提供了它们的起源的透明度（就像在实用程序类中静态导入的辅助方法一样）。
```
fun String.last() : Char {
  return this[length - 1] // 为数组构建拓展
}

val x = "Hey!"
println(x.last()) // 打印 "!".
```
Guava的一半和Cash app的三分之二是由静态工具方法组成。虽然这不会消除他们的存在，但它将允许您直接使用它们实现它们的类型。这是可读性的一大胜利。（更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/extensions.html)）

5.数据类
类似于JAVA10将要实现的值类型，数据类是仅存在于以语义，不可变类型分组数据的类。 我们目前使用AutoValue轻松模拟值类型。
`data class Money(val currency: String, val amount: Int)`
这个类将会带给你equals, hashCode, toString 方法。每个组件以相同名称的只读属性公开。 还有一种将值更改为新实例的copy方法。
`val money = Money("USD", 100)
val moreMoney = money.copy(amount = 200)//amount被重新赋值`
可以通过多重分配解压缩实例。 这类似于Python元组，并且已经被运送到标准库中，例如在循环映射时用于Map.Entry。
```
for ((key, value) in map) {
  println(key + ": " + value)
}
```
(更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/data-classes.html))

6.其他一些了不起的特性
- 默认情况下类和方法是final的。如果你想拓展可以声明他们open。
```
class Foo {}
class Bar extends Foo {} //编译不通过
open class Foo {}
class Bar extends Foo {} // 编译成功!
```
（更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/classes.html#inheritance)）
- 隐式转换。如果类型检查成功，则变量则是更具体的类型。
```
val x: Object = "hi"
val length = x.length // 编译不通过
if (x is String) {
  val length = x.length // Compiles!
}
```
这也适用于可空性。
```
val x: Object? = "Hi"
if (x != null) {
  val y: Object = x // 'x' is a non-null type here
}
```
（更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/typecasts.html)）

- Delegates.observable（）接受两个参数：初始值和修改的处理程序。 每次分配给该属性（执行分配之后）调用处理程序。 处理程序有三个参数：一个属性被分配给，旧的值和新的一个：
  `val lazy: String by Delegates.lazy { expensiveOperation() }
  `
  （更多相关信息，请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/properties.html)）

- JetBrains正在积极的支持Square项目。最近，Retrofit和Dragger的运行已经被支持。 然而，Kotlin的很多语言功能消除了注释处理器的需要。 例如，我可以使用委托属性重新实现大多数ButterKnife作为运行时库。

####用例
使用JAVA开发Square Cash Android应用程序存在痛点以及Kotlin的功能如何改善情况时存在的一些具体例子。
- 实用方法
  所谓的静态实用方法丢弃了我们的代码库。存在大量图书馆，如Guava，commons-lang等，以提供这些简化与指定类型的常见交互的方法。
  科特林并没有消除这些实用方法 - 它对他们进行了增强！ 每个人都有他们希望被内置的类型像String的方法。
```
fun String.truncateAt(max: Int) : String {
  return substring(0, Math.min(length, max))
}
```
调用代码获得语义上更有意义的调用：
`val name = "Jake Wharton".truncateAt(4)
`
这个例子是微不足道的，但是任何在合理的时间内编写Java的人都可以推断出这将对代码的清晰度有多大的影响。
值得注意的另一个具体例子是从协议缓冲区模式定义自动生成的类型。 即使我们在源代码树中拥有这些类型，但是它们的生成事实阻止了我们有意义的方法来扩展它们的有用方法（您在哪些部分类？）。 Kotlin提升这些实用方法直接对生成的类型进行操作。
```
fun Money.add(other: Money): Money {
  if (currency_code != other.currency_code) {
    throw IllegalArgumentException("Currency code ${other.currency_code}"
        + " does not match ${currency_code}.") // Hey String interpolation!
  }
  return copy(amount = amount + other.amount)
}
```
现在我们可以像我们真正想要的那样添加钱。
```
val one = Money("USD", 100)
val two = Money("USD", 200)
val three = one.add(two)
```
但等等，还有更多！现在订购，您还将收到操作员重载（只需支付单独的运送和处理费用）。 如果我将上述方法重命名为加号，我可以像任何其他值类型一样添加这些实例。
`val three = one + two`
奖金：我可以把光标放在加号上，并按照任何其他方法命中CMD + B，直接进行扩展方法的实现！
记住，使用这些方法（包括操作符重载示例）取决于静态导入。 与C＃不同，这些都不是神奇地应用到处。

- RxJava, Listeners
  几乎所有的Android应用程序本身都是异步的。 这导致代码由回调，侦听器和处理RxJava的反应流（一种通过它们管理异步数据流的强大机制）组成的代码。
```
buttonView.setOnClickListener(new OnClickListener() {
  @Override public void onClick(View v) {
    finish();
  }
});
```
现在，lambdas的优点应该是一般性的知识，但Android在接下来的两年中不太可能会看到Java 8，即使只能在最新版本上使用。 Kotlin现在和所有版本的Android都提供给我们。
`buttonView.setOnClickListener { finish() }`
当您开始使用RxJava构建异步数据流时，可以将数百行代码减少到10以下。代码的实际行为不会改变。 只有通过匿名类，长泛型类型声明和中间值类型的详细创建才能消除过多的样板。


####练习
>附上自己的一个kotlin  练习项目， 一个十分简单天气app。
>https://github.com/zyl409214686/WeatherForKotlin

![效果图](https://github.com/zyl409214686/WeatherForKotlin/raw/master/screenshot/weather.png)