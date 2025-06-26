## 安卓开发笔记 Day09
课程目标

- 掌握异常处理
- 掌握代码规范
- 掌握常用设计模式

## 1.1 异常处理

- 异常种类（简述）：分为编译时异常和运行时异常
- 编译时异常

### 编译时异常包括：
- **语法错误（Syntax Error）**：如少了分号、缺少括号、拼写错误等，编译器无法理解代码语法而发生错误。
- **类型不匹配错误（Type Mismatch Error）**：如把一个字符串变量赋值给整型变量，或在使用函数时传入错误类型的参数等，导致编译器无法将代码转换成二进制文件。
- **未声明的变量或函数（Error of undeclared variable or function）**：如果使用未声明的变量或函数，编译器无法理解代码含义而抛出异常。

代码示例：

```java
extends FragmentStatePagerAdapter {
}
```

### 运行时异常

- **空指针异常（NullPointerException）**：当程序试图访问空对象时，会触发空指针异常。
- **数组越界异常（ArrayIndexOutOfBoundsException）**：当程序试图访问不存在的数组元素时，会触发数组越界异常。
- **类型转换异常（ClassCastException）**：当程序试图将一个对象强制转换为另一种不兼容类型时，会触发类型转换异常。
- **算术异常（ArithmeticException）**：当程序试图执行除法操作时除数为0时，会触发算术异常。


### 受检时的异常

- 顾名思义，就是可以检测到的异常，在程序中会对我们进行提示。

##### 第一种做法：

- 大多数情况下，我们通过 try-catch 语句块来捕获异常。如果 try 语句块中的代码发生异常，会立即跳转到 catch 语句块，并执行其中的代码。
- catch 语句块中可以包含多个 catch 子句，每个子句用于捕获不同类型的异常。

代码示例：

```java
try {
    // 可能发生错误的代码
} catch (具体错误) {
    // 具体错误处理，没有就不写，有多个，就写多个catch
    e.printStackTrace(); // 在命令行打印错误信息
} catch (Exception e) {
    log(e.toString());
} finally {
    // 无论是否捕获到异常，都会执行的代码
}
``` 

### 主动抛出异常

- 主动使用 `throw` 抛出一个异常，并获取这个异常的引用。这个异常会被抛到外部的环境，由外部环境进行处理。
- 但是你还是要去外部环境进行异常处理，比如 try-catch。否则最终传递给系统捕获处理，那么就会引发崩溃。

### Crash 捕获

异常发生时的传递和捕获流程：

1. 抛出异常时，通过 `Thread.dispatchUncaughtException` 进行分发。
2. 依次由 Thread、ThreadGroup、Thread.getDefaultUncaughtExceptionHandler 处理。
3. 默认情况下，`KillApplicationHandler` 会被设置为 `defaultUncaughtExceptionHandler`。
4. `KillApplicationHandler` 中会调用 `Process.killProcess` 退出应用。

这样就形成了异常发生时的传播路径。如果通过 `Thread.setDefaultUncaughtExceptionHandler` 设置自定义处理器，就可以捕获异常并进行一些兜底操作。比如常用的 bugly 等异常捕获库，本质上也是这么实现的。


### 异常处理小结

- 异常主要分为两种：
  - 编译时异常
  - 运行时异常

- Crash 捕获：设置 `defaultUncaughtExceptionHandler`


## 2.1 代码规范


### 命名规范

1. **包命名：** 包名应该全部小写，使用域名格式。

    ```java
    com.example.myapp
    ```

2. **类命名：** 类名应该是名词，采用 PascalCase 命名法。

    ```java
    public class UserProfile {}
    ```

3. **方法命名：** 方法名应该是动词，采用 camelCase 命名法。

    ```java
    public void calculateTotal() {}
    ```

4. **变量命名：** 变量名应该是名词或名词短语，采用 camelCase 命名法。

    ```java
    int userAge;
    ```

5. **常量命名：** 常量名应该全部大写，使用下划线分隔单词。

    ```java
    public static final int MAX_USER_COUNT = 100;
    ```

---

## 代码格式

1. **缩进：** 使用 4 个空格缩进，不使用制表符。
2. **行宽：** 每行代码不超过 100 个字符。
3. **花括号：** 大括号 `{}` 应该在同一行开始，并在新的行结束。

    ```java
    if (user.isLoggedIn()) {
        showWelcomeMessage();
    } else {
        promptLogin();
    }
    ```

### XML 资源文件

1. **命名：** 资源文件名和 ID 应该使用小写字母和下划线。

    ```xml
    <TextView
        android:id="@+id/text_view_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Title" />
    ```

2. **格式：** 保持 XML 文件的良好缩进和对齐，使用 2 个空格缩进。

3. **属性顺序：** 按一定顺序排列 XML 属性，例如 id、layout、style、其他。

    ```xml
    <Button
        android:id="@+id/button_submit"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"



#### 资源组织

1. 资源目录：按功能分别组织资源，例如 `drawable`、`layout`、`values`。
2. 字符串资源：所有的字符串应放在 `strings.xml` 文件中，不要在代码中硬编码。

####  代码结构

1. 单一职责原则：每个类应该且仅有一个明确的职责。

2. 注入依赖：使用依赖注入框架（如 Dagger）来管理依赖关系。

3. 避免神对象：不要让一个类做太多事情，应拆分成多个小类。

#### 性能优化
1. 避免冗余布局：减少嵌套布局层次，使用 ConstraintLayout 替代嵌套的 LinearLayout。

2. 使用 ViewHolder 模式：在 RecyclerView 中使用 ViewHolder 模式来优化性能。

3. 异步操作：在线程池执行耗时操作，避免在主线程进行网络请求或数据库查询。


### recyclerview的viewHolder的多级缓存是如何实现的
RecyclerView 的 ViewHolder 多级缓存是通过以下几个机制实现的：
1. **ViewHolder 缓存**：RecyclerView 内部维护了一个 ViewHolder 的池（Recycler），当一个 ViewHolder 不再使用时，它会被放回池中，而不是被销毁。这样可以减少频繁创建和销毁 ViewHolder 的开销。
2. **ViewType 缓存**：RecyclerView 支持多种类型的 ViewHolder。每种类型的 ViewHolder 都有自己的缓存池，这样可以更高效地复用不同类型的 ViewHolder。
3. **布局复用**：RecyclerView 在滚动时会复用屏幕外的 ViewHolder，将它们重新绑定到新的数据上，而不是创建新的 ViewHolder。这种方式可以显著提高性能，尤其是在列表项较多时。
4. **预加载机制**：RecyclerView 可以预加载屏幕外的 ViewHolder，以便在用户滚动时能够快速显示新的列表项。这通过设置 `setItemViewCacheSize` 方法来控制缓存的数量。
5. **布局管理器**：RecyclerView 的布局管理器（如 LinearLayoutManager、GridLayoutManager 等）负责管理 ViewHolder 的布局和位置。它们会根据当前的滚动状态和可见区域来决定哪些 ViewHolder 需要被创建或复用。
6. **回收机制**：当 ViewHolder 滚出屏幕时，RecyclerView 会将其放回池中，以便在需要时复用。这种回收机制可以减少内存使用和提高性能。
7. **DiffUtil**：RecyclerView 提供了 DiffUtil 工具，可以高效地计算列表数据的差异，并只更新需要变化的部分。这减少了不必要的 ViewHolder 创建和绑定，提高了性能。
8. **异步加载**：RecyclerView 支持异步加载数据，可以在后台线程中处理数据，避免阻塞主线程，提高用户体验。

## 异常处理

1. **使用特定异常**：尽量使用特定的异常类型，不要使用 `Exception` 或 `Throwable`。

2. **抛出异常**：在必要时抛出异常，并提供有用的异常消息。

3. **捕获异常**：只捕获你能处理的异常，避免捕获所有异常。





# 面向对象的四大特性

1. **封装**
    - 信息隐藏
    - 数据访问保护
    - 访问权限控制
    - 意义和解决问题：
        - 保护数据不被随意修改
        - 提高代码的可维护性
        - 仅暴露有限的必要接口，提高类的易用性

2. **抽象**
    - 隐藏方法的具体实现
    - 接口类、抽象类
    - is-a 关系
    - 意义和解决问题：
        - 提高代码的可扩展性、维护性
        - 处理复杂系统的有效手段
        - 过滤掉不必要关注的信息

3. **继承**
    - 单继承、多继承
    - 代码复用
    - 子类替换父类
    - 意义和解决问题：
        - 提高代码复用率
        - 简化代码结构

4. **多态**
    - 子类继承父类、实现接口
    - 接口类、duck typing
    - 意义和解决问题：
        - 提高代码的扩展性和复用性
        - 是很多设计模式、设计原则、编程技巧的代码实现基础

## 封装（Encapsulation）

如果我们对类中属性的访问不做限制，任何代码都可以访问、修改类中的属性。虽然这样看起来更灵活，但过度灵活也意味着不可控，属性可以随意被各种方式修改，修改逻辑可能分散在代码的各个角落，势必影响代码的可读性、可维护性。

### 1. 关于封装特性

- 封装也叫作信息隐藏或者数据访问保护。
- 类通过暴露有限的访问接口，授权外部仅能通过类提供的方式访问内部信息或数据。
- 需要编程语言提供权限访问控制语法来支持，比如 Java 中的 `private`、`protected`、`public` 关键字。
- 封装特性的意义：
  - 保护数据不被随意修改，提高代码的可维护性。
  - 仅暴露有限的必要接口，提高类的易用性。

## 抽象（Abstraction）

抽象（Abstraction）

- 编程语言中提供的接口语法通常被称为“接口类”而不是“接口”。因为“接口”一词含义太广，可以指 API 接口等各种概念，所以用“接口类”特指编程语言提供的接口语法。

- 在定义（或命名）类的方法时，要有抽象思维，不要在方法定义中暴露太多实现细节。这样可以保证在需要更改方法实现逻辑时，不必修改其定义，只需修改实现部分即可。



### 2. 关于抽象特性

- 封装主要讲如何隐藏信息、保护数据，抽象则是讲如何隐藏方法的具体实现，让使用者只需关心方法提供了哪些功能，不需要知道这些功能是如何实现的。
- 抽象可以通过接口类或者抽象类来实现，但并不需要特殊的语法机制来支持。
- 抽象的意义：
  - 提高代码的可扩展性、维护性，修改实现不需要改变定义，减少代码的改动范围。
  - 处理复杂系统的有效手段，能有效地过滤掉不必要关注的信息。

## 继承（Inheritance）
继承是用来表示类之间的 is-a 关系，比如猫是一种哺乳动物。从继承关系上来讲，继承可以分为两种模式：单继承和多继承。单继承表示一个子类只继承一个父类，多继承表示一个子类可以继承多个父类，比如猫既是哺乳动物，又是爬行动物。

例如：
- Java 使用 `extends` 关键字来实现继承
- C++ 使用冒号（`class B : public A`）
- Python 使用括号 `()`，Ruby 使用 `<`

有些编程语言只支持单继承，不支持多重继承，比如 Java、PHP、C#、Ruby 等；而有些编程语言既支持单重继承，也支持多重继承，比如 C++、Python、Perl 等。

### 3. 关于继承特性

- 继承是用来表示类之间的 is-a 关系，分为两种模式：单继承和多继承。
    - 单继承表示一个子类只继承一个父类
    - 多继承表示一个子类可以继承多个父类
- 为了实现继承这个特性，编程语言需要提供特殊的语法机制来支持。
- 继承主要是用来解决代码复用的问题。


## 多态（Polymorphism）

多态这种特性也需要编程语言提供特殊的语法机制来实现。

在上面的例子中，我们用到了三个语法机制来实现多态：

1. **父类引用子类对象**：编程语言要支持父类对象可以引用子类对象，也就是可以将 `SortedDynamicArray` 传递给 `DynamicArray`。
2. **继承**：编程语言要支持继承，也就是 `SortedDynamicArray` 继承了 `DynamicArray`，才能将 `SortedDynamicArray` 传递给 `DynamicArray`。
3. **方法重写（override）**：编程语言要支持子类可以重写父类中的方法，也就是 `SortedDynamicArray` 重写了 `DynamicArray` 中的 `add()` 方法。


# 常用的设计模式和设计模式在 Android 中的使用

（一）什么是设计模式

1. 基本定义：  
   设计模式（Design pattern）是一套被反复使用的代码设计经验的总结。  
   使用设计模式的目的是为了可重用代码，让代码更容易被他人理解。  
   设计模式是软件工程的基石脉络，如大厦的结构一样。

2. Design pattern 的四大要素：  
   - 模式名（Name）
   - 问题（Question）
   - 解决方案（Solution）
   - 效果（Efftive）

3. OO（面向对象）的六大原则：  
   - 单一职责原则
   - 开闭原则
   - 里氏替换原则
   - 依赖倒置原则.
   - 接口隔离原则
   - 迪米特原则
## 设计模式的分类

设计模式分为三种类型：

1. **创建型模式**（5种）  
   - 单例模式  
   - 抽象工厂模式  
   - 工厂模式  
   - 原型模式  
   - 建造者模式

2. **结构型模式**（7种）  
   - 适配器模式  
   - 桥接模式  
   - 装饰模式  
   - 组合模式  
   - 外观模式  
   - 享元模式  
   - 代理模式

3. **行为型模式**（11种）  
   - 观察者模式  
   - 中介者模式  
   - 访问者模式  
   - 解释器模式  
   - 迭代器模式  
   - 备忘录模式  
   - 责任链模式  
   - 状态模式  
   - 策略模式  
   - 命令模式  
   - 模板模式

## 3.1 设计模式 --- MVVM

常见的架构模式基本上就是 MVC、MVP、MVVM，这三种也是开发 GUI 应用程序常见的模式。

除此之外还有：
- 分层模式
- 客户端-服务器模式（CS模式）
- 主从模式
- 管道过滤器模式
- 事件总线模式 等等。

分析 MVC、MVP、MVVM 这三种架构模式。

---

### 1. MVC

- **Controller（控制器）：** 负责业务逻辑的处理
- **Model（模型）：** 负责数据逻辑的处理
- **View（视图）：** 负责界面的处理

基本流程：  
视图事件变化传递给 Controller（控制器），Controller 处理业务逻辑后更新 Model（模型），Model 更新数据后再通过 View（视图）更新界面。

---

## 3.1 mvvm

### MVC 解决什么问题

我们可以看到，上面不使用架构进行开发，带来的问题是 Activity / Fragment 逻辑臃肿，不利于扩展。

所以 MVC 就要解决的问题是：**控制逻辑、数据处理逻辑和界面交互耦合。**

---

### 如何划分角色

为了解决上面的问题，MVC 架构里，将逻辑、数据、界面的处理划分为三个部分：  
- 模型（Model）
- 视图（View）
- 控制器（Controller）

各个部分的功能如下：

- **Model（模型）**：负责数据的加载和存储。
- **View（视图）**：负责界面的展示。
- **Controller（控制器）**：负责逻辑控制。


### 3. 如何通信（数据的流向）

我们再看看三者（MVC 各层）之间是怎么通信的。

在介绍通信之前，先理解下通信中的“数据”：
- Android 开发中，通信数据可以分为两种：
    1. **数据结构**：如网络请求、本地存储等通信使用的 JavaBean；
    2. **事件**：如控件产生的动作，包括触摸、点击、滑动等。
- 我们在通信过程中，主要关注这两种数据。

在 MVC 架构中，**View** 产生事件，通知到 **Controller**，  
**Controller** 进行一系列逻辑处理，通知 **Model** 去更新数据，  
**Model** 更新数据后，再将数据结构通知给 **View** 去更新界面。

这就是一个完整 MVC 的数据流向。

## 3.1 MVP

### MVP 架构

MVP 要解决的问题和 MVC 大同小异：  
- 控制逻辑、数据处理逻辑和界面交互耦合，  
- 同时能够将 MVC 中的 View 和 Model 解耦。

---

### 2. 如何划分角色

MVP 架构里，将逻辑、数据、界面的处理划分为三个部分：
- 模型（Model）
- 视图（View）
- 控制器（Presenter）

各个部分的功能如下：

- **Model（模型）**：负责数据的加载和存储
- **View（视图）**：负责界面的展示
- **Presenter（控制器）**：负责业务逻辑的处理

MVP 中，Presenter 负责业务逻辑，Model 和 View 之间不会直接通信，而是通过 Presenter 进行连接。

---
## MVVM 基本概念

MVVM 架构是一种基于数据绑定的架构模式。它将应用程序分为三个主要组件：**Model、View 和 ViewModel**。每个组件有着不同的职责和功能：

- **Model**：负责处理数据层的逻辑，包括数据的获取、存储和处理等。可以是数据库、网络接口、API 等。

- **View**：负责用户界面的展示和用户输入的处理。可以是 Activity、Fragment、XML 布局文件等。

- **ViewModel**：负责处理 View 和 Model 之间的交互，将 Model 中的数据进行处理和转换后提供给 View 进行展示，同时接收 View 中的用户输入并进行处理。ViewModel 与 View 之间通过数据绑定进行通信。
---
Android Jetpack 是一套官方提供的用于简化 Android 应用开发的库集合，其中包含了很多组件，可以帮助开发者构建现代化的 Android 应用。在 MVVM 架构中，Jetpack 组件提供了一些关键的组件，包括 LiveData、ViewModel 和 Data Binding，它们在实现 MVVM 架构中起着重要的作用。

## MVVM

### LiveData
- LiveData 是一种具有生命周期感知能力的数据持有者，它可以在数据发生变化时通知观察者。
- LiveData 可以感知 Activity、Fragment 等组件的生命周期，当这些组件处于活跃状态时，LiveData 会自动更新数据并通知观察者，从而确保数据的一致性和安全性。
- 在 MVVM 架构中，LiveData 作为 ViewModel 与 View 之间的桥梁，负责将 Model 中的数据通过观察者模式通知给 View 进行展示。

### ViewModel
- ViewModel 是用于存储和管理与 UI 相关的数据的类，负责处理 View 和 Model 之间的交互。
- ViewModel 通常与某个特定的 View 关联，当 View 被销毁并重新创建时，ViewModel 会保持其数据的状态。
- ViewModel 可以在 View 中进行数据绑定，将 Model 中的数据通过 LiveData 暴露给 View，并处理 View 中的用户输入和事件，从而将业务逻辑从 View 中解耦。
