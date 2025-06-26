# 安卓开发笔记 Day11 

## 课程目录
1. **Kotlin 是什么**
    - 1.1 Kotlin 的前世今生
    - 1.2 为什么要用 Kotlin
    - 1.3 开发环境介绍
    - 1.4 第一个 KT 程序
2. Kotlin 变量和类型系统
3. Kotlin 函数和流程控制
4. Kotlin 面向对象和特殊类
5. Kotlin 特殊语法糖
6. Kotlin 协程
7. 总结

## 2.1 变量

在 Java/C 当中，如果我们要声明变量，必须要声明它的类型，后面跟着变量的名称和对应的值，然后以分号结尾。例如：

```java
Integer price = 100;
```

而 Kotlin 不一样，需要使用 val 或 var 这样的关键字作为开头，后面跟“变量名称”，接着是“变量类型”和“赋值语句”，最后结尾无需分号。例如：

```kotlin
var price: Int = 100
// 关键字 变量名称 变量类型 赋值
// var price Int = 100
```

像 Java 那样每一行代码都写一个分号，其实挺麻烦的。所以为了省事，在 Kotlin 里面，一般会把代码末尾的分号省略，比如：

```kotlin
var price: Int = 100
```
由于 Kotlin 支持类型推导，大部分情况下，变量类型可以省略不写，比如：

```kotlin
var price = 100  // 默认推导类型为 Int
```
在 Kotlin 当中，应该尽可能避免使用 `var`，尽可能多地去使用 `val`。

```kotlin
var price = 100
price = 101

val i = 0
i = 1 // 编译器报错
```
val 声明的变量叫做不可变变量，值在初始化后无法再被修改，相当于 Java 里的 final 变量。

var 声明的变量叫做可变变量，对应 Java 里的普通变量。

### 使用 val 的好处:
- 无需担心后续赋值，代码更易于维护
- 线程安全
- 编译器可进行更多优化


### 2.2 类型

从某种程度上讲，Java 的类型系统并不是**全面向对象**的，因为它存在原始类型（primitive type），原始类型并不属于对象。而 Kotlin 则不一样，从语言设计层面规避了这个问题，类型系统是**全面向对象**的。

#### Kotlin vs Java 基本类型对比

| Kotlin           | Java                         |
|------------------|-----------------------------|
| Byte             | byte / Byte                 |
| Int & Long       | int / Integer & long / Long |
| Float & Double   | float / Float & double / Double |
| Char             | char / Character            |
| String           | String                      |

#### 类型及比特宽度

| Type     | Bit width | 备注                        |
|----------|-----------|----------------------------|
| Double   | 64        | Kotlin 没有 double         |
| Float    | 32        |                            |
| Long     | 64        | Kotlin 没有 long           |
| Int      | 32        | Kotlin 没有 int/integer    |
| Short    | 16        | Kotlin 没有 short          |
| Char     | 16        | Kotlin 没有 char           |
| Byte     | 8         | Kotlin 没有 byte           |
| Boolean  | 8         | Kotlin 没有 boolean        |

> **备注说明：**  
Kotlin 只有对象类型（没有 Java 的原始类型 primitive），更纯粹的面向对象类型系统。

#### 2.2.1 基础类型——数字类型

在数字类型上，Kotlin 和 Java 几乎一致，包括对数字“字面量”的定义方式：

```kotlin
val int = 1
val long = 1234567L
val double = 13.14
val float = 13.14F
val hexadecimal = 0xAF
val binary = 0b01010101
```
对于数字类型的转换，Kotlin 与 Java 的转换行为不同：
- Java 可以隐式转换数字类型

- Kotlin 更推崇显式转换

#### 2.2.2 基础类型-包装类

在 Java 里面，基础类型分为**原始类型**（Primitive Types）和**包装类型**（Wrapper Type）。  
比如，整型有对应的 `int`（原始类型） 和 `Integer`（包装类型）：

```java
int i = 0;      // 原始类型
Integer j = 1;  // 包装类型
```
而在 Kotlin 语言体系中，没有原始类型这个概念，这意味着一切都是对象。

##### 基础类型-空安全

既然 Kotlin 中的一切都是对象，那么对象就有可能为 null。也许你会写如下代码：
```kotlin
val i: Double = null // 编译器报错
```
上述代码不能通过 Kotlin 编译。因为 Kotlin 强制开发者在定义变量时，必须显式指定变量是否可以为 null。
如果变量可能为 null，需要在类型后面加一个问号 ?：

```kotlin
val j: Double? = null // 正确，表示可空类型
```

由于 Kotlin 对可能为空的变量类型做了强制区分，这意味着：
- “可能为空的变量” **无法直接赋值给** “不可为空的变量”，反向赋值则没有问题。

示例：
```kotlin
var i: Double = 1.0
var j: Double? = null
i = j // 编译器报错
j = i // 正确
```
Kotlin 这样设计的原因很简单：
如果“可空变量”直接赋值给“不为空的变量”，就会与自身的定义产生冲突。
如果真的有这样的需求，也可以通过判断实现：
```kotlin
var i: Double = 1.0
var j: Double? = null

if (j != null) {
    i = j
}
```

### Kotlin 类型系统

在 Kotlin 当中，`Any` 是所有类型的父类，我们可以称之为**根类型**。  
同时，Kotlin 的类型还分为**可空类型**和**不可空类型**。

### 基础类型-数组

在 Kotlin 当中，我们一般会使用 `arrayOf()` 来创建数组。括号内可以用于传递数组元素进行初始化，同时，Kotlin 编译器会根据传入参数进行类型推导。

```kotlin
val arrayInt = arrayOf(1, 2, 3)
val arrayString = arrayOf("apple", "pear")
```
虽然 Kotlin 的数组仍然不属于集合，但它的一些操作与集合是统一的。例如：
```kotlin
val array = arrayOf("apple", "pear")
println("Size is ${array.size}")
println("First element is ${array[0]}")
```
输出
```
Size is 2
First element is apple
```

### 函数

在 Kotlin 当中，函数的声明与 Java 不太一样。

```kotlin
// 关键字 fun，参数类型在参数名后面，返回值类型在参数列表后面
fun helloFunction(name: String): String {
    return "Hello $name !"
}
```
- 使用 fun 关键字来定义函数。
- 函数名一般用驼峰命名法。
- 参数用 (name: String) 这种“参数名:类型”形式。

### 单表达式函数

如果一个函数体只有一行代码，可以省略花括号 `{}` 和 `return`，直接用 `=` 连接，实现类似变量赋值的形式：

```kotlin
fun helloFunction(name: String): String = "Hello $name !"
```
这种写法称为单表达式函数。
注意：
表达式中省略了 return。
如果 Kotlin 能自动推断返回值类型，还可以省略返回值类型声明：
```kotlin
fun helloFunction(name: String) = "Hello $name !"
```
```Kotlin
fun createUser(
    name: String,
    age: Int,
    gender: Int,
    friendCount: Int,
    feedCount: Int,
    likeCount: Long,
    commentCount: Int
) {
    // 函数体···
}

createUser("Tom", 30, 1, 78, 2093, 10937, 3285)
```

这样调用像 Java 一样，参数顺序容易混乱，难以阅读和维护。
```kotlin
createUser(
    name = "Tom",
    age = 30,
    gender = 1,
    friendCount = 78,
    feedCount = 2093,
    likeCount = 10937,
    commentCount = 3285
)
```
Kotlin 的具名参数（推荐，可读性强）
好处：不用记顺序，一眼看出每个值对应的含义，代码更易读、易维护。

### 流程控制 - Elvis 表达式

由于 Kotlin 明确区分“可空类型”和“不可空类型”，我们经常会遇到可空变量，并且要判断是否为 null。

#### 普通写法
```kotlin
fun getLength(text: String?): Int {
    return if (text != null) text.length else 0
}
```
#### Elvis 表达式
Kotlin 提供了 Elvis 表达式 ?: ，让代码更简洁：
```kotlin
fun getLength(text: String?): Int {
    return text?.length ?: 0
}
```
text?.length ?: 0 的意思是：如果 text 不为 null，返回 text.length，否则返回 0。

Kotlin 单表达式函数+Elvis表达式，写法可以更简洁：
```kotlin
fun getLength(text: String?) = text?.length ?: 0
```
### 流程控制 - when 表达式
Kotlin 的 when 表达式类似于 Java 的 switch 语句，但更强大。它可以用于替代 if-else 语句。
```kotlin
fun getColorName(color: String): String {
    return when (color) {
        "red" -> "红色"
        "green" -> "绿色"
        "blue" -> "蓝色"
        else -> "未知颜色"
    }
}
```                                                       
当 when 表达式只有一个分支时，可以省略花括号：
```kotlin   
fun getColorName(color: String) = when (color) {
    "red" -> "红色"
    "green" -> "绿色"
    "blue" -> "蓝色"
    else -> "未知颜色"
}
```            
### 流程控制 - for 循环
Kotlin 的 for 循环与 Java 类似，但更简洁。可以直接遍历集合或数组：
```kotlin
fun printColors(colors: List<String>) {
    for (color in colors) {
        println(color)
    }
}
```
如果需要获取索引，可以使用 withIndex() 方法：
```kotlin
fun printColorsWithIndex(colors: List<String>) {
    for ((index, color) in colors.withIndex()) {
        println("索引 $index 的颜色是 $color")
    }
}
```

### 流程控制 - while 循环
Kotlin 的 while 循环与 Java 类似，但更简洁。可以直接使用 while 关键字：
```kotlin
fun printNumbers(max: Int) {
    var i = 0
    while (i < max) {
        println(i)
        i++
    }
}
```

### 面向对象 - 类的继承

Kotlin 的继承和 Java 的本质没有区别，只是语法稍有不同：

#### Java 写法
```java
public class MainActivity extends Activity {
    @Override
    void onCreate() {
        // ...
    }
}
```
#### Kotlin 写法
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate() {
        // ...
    }
}
``` 

### 面向对象 - 特殊类
Kotlin 中有一些特殊的类，比如数据类（Data Class）、密封类（Sealed Class）等。
#### 数据类（Data Class）
数据类是用于存储数据的类，自动生成 equals()、hashCode()、toString() 等方法。定义数据类时，需要使用 data 关键字：
```kotlin
data class User(val name: String, val age: Int)

``` 



























































































































































































































































































































































































































































































































































































































































































































































































































































































































































































