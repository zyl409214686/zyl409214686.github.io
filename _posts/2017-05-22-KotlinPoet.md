---
layout: post
title: KotlinPoet
categories: Kotlin
description: 
keywords: Kotlin, KotlinPoet
---

##### 简介

KotlinPoet是一个用于生成.kt源文件的Kotlin和Java的 API。源文件生成在进行诸如注释处理或与元数据文件（例如，数据库模式，协议格式）交互等操作时可能是有用的。 通过生成代码，您不需要编写引用，同时也为元数据保留真实的单一来源。

##### 示例
这是一个helloworld类：
```
class Greeter(name: String) {
  val name: String = name

  fun greet() {
    println("Hello, $name")
  }
}

fun main(args: Array<String>) {
  Greeter(args[0]).greet()
}
```
使用KotlinPoet生成它：
```
val greeterClass = ClassName.get("", "Greeter")
val kotlinFile = KotlinFile.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter(String::class, "name")
            .build())
        .addProperty(PropertySpec.builder(String::class, "name")
            .initializer("name")
            .build())
        .addFun(FunSpec.builder("greet")
            .addStatement("println(%S)", "Hello, \$name")
            .build())
        .build())
    .addFun(FunSpec.builder("main")
        .addParameter(ArrayTypeName.of(String::class), "args")
        .addStatement("%T(args[0]).greet()", greeterClass)
        .build())
    .build()

kotlinFile.writeTo(System.out)
```
KotlinPoet完整版 [API文档](https://square.github.io/kotlinpoet/0.x/kotlinpoet/com.squareup.kotlinpoet/)，灵感来源于[javaPoet](https://github.com/square/javapoet/)。
##### Gradle集成方式:
`compile 'com.squareup:kotlinpoet:0.2.0'`