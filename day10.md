# 安卓开发笔记 Day10   

## 目标与收益

### 课程目标
- 掌握 Java 垃圾回收机制
- 掌握使用工具检测内存泄漏
- 掌握如何进行内存泄漏优化

## Java 垃圾回收机制

垃圾回收（GC）是由 Java 虚拟机（JVM）垃圾回收器提供的一种对内存回收的机制。  
它一般会在内存空间闲或者内存占用过高的时候，对那些没有任何引用的对象不定时地进行回收。

### JVM 内存结构示意图

- **Native Method Stacks（本地方法栈）**
- **Program Counter Register（程序计数器）**
- **JVM Stacks（虚拟机栈）**  
  包含局部变量表、操作数栈、动态链接、方法出口等
- **Heap（堆区）**  
  - Young 区（新生代）：Eden、S0、S1  
  - Old 区（老年代）
- **MetaSpace（元空间）**
- **Method Area（方法区）**
- **常量池**


## Java 垃圾回收算法
Java 垃圾回收器使用的算法主要有两种：引用计数算法和可达性分析法。

### 引用计数算法

一个对象被创建之后，系统会给这个对象初始化一个引用计数器，当这个对象被引用了，则计数器 +1，
而当该引用失效后，计数器 -1，直到计数器为 0，意味着该对象不再被使用了，则可以将其进行回收。

**优点**：判定比较简单，效率也很高  
**缺点**：无法避免循环引用，即两个对象之间循环引用的时候，各自的计数器始终不会变成 0


### 可达性分析法

根据搜索算法的中心思想，就是从某一些指定的根对象（GC Roots）出发，一步步遍历找到和这个根对象具有引用关系的对象，然后再从这些对象开始继续寻找，从而形成一个个的引用链（其实和图论的思想一致），  
然后不在这些引用链上的对象便被标识为引用不可达对象，也就是我们说的“垃圾”，这些对象便需要回收掉。

#### GCRoot 包含的对象

1. 虚拟机栈中引用的对象（栈帧中的本地变量表）
2. 方法区中类静态属性引用的对象；方法区中常量引用的对象
3. 本地方法栈中 JNI（Native方法）引用的对象

### 引用类型

#### 1. 强引用（Strong Reference）
如 `Object obj = new Object();`，这类引用是 Java 程序中最常见的。  
Object 这种对象具有强引用，属于**不可回收的资源**，垃圾回收器绝不会回收它。  
当内存空间不足时，JVM 宁愿抛出 `OutOfMemoryError` 错误，使程序异常终止，也不会回收具有强引用的对象，来解决内存不足的问题。

#### 2. 软引用（Soft Reference）
软引用是一种相对强引用**弱化**一些的引用，可以让对象避免一些垃圾收集。  
只有当 JVM 认为内存不足时，才会去尝试回收软引用指向的对象。  
JVM 会确保在抛出 `OutOfMemoryError` 之前，清理软引用指向的对象。  
软引用常用于实现内存敏感的缓存，如果还有空间内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存。
#### 3. 弱引用（Weak Reference）
弱引用的强度比软引用更弱一些。当 JVM 进行垃圾回收时，无论内存是否充足，都会回收只被弱引用关联的对象。

#### 4. 虚引用（Phantom References）
   虚引用也称为幽灵引用或者幻影引用，是最弱的一种引用关系。  
   一个对象是否有虚引用的存在，完全不会对其生命周期构成影响，也无法通过虚引用来取得一个对象实例。  
   为一个对象设置虚引用关联的唯一目的是**能在这个对象被收集器回收时收到一个系统通知**。

## 什么是内存泄漏

内存泄漏（Memory Leak）是指程序中已动态分配的堆内存由于某种原因未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。

### 内存泄漏特征

- 内存泄漏缺陷具有**隐蔽性、积累性**的特点，比其他内存非法访问错误更难检测。  
- 因为内存泄漏的产生原因是内存块未被释放，属于**遗漏型缺陷**而不是**错误型缺陷**。
- 内存泄漏通常不会直接产生可观察的错误症状，而是**逐渐积累**，降低系统整体性能，极端情况下可能导致系统崩溃。

### Android 内存泄漏说明

- Android 为不同类型的进程分配了不同的内存使用上限。
- 如果程序在运行过程中出现了内存泄漏而导致应用进程使用的内存超出了这个上限，则会被系统认为内存泄漏，从而**被 kill 掉**，这样仅有自己的进程被 kill，而不会影响其他进程（如果是 `system_process` 等系统进程出问题，可能会引起系统重启）。

### 内存泄漏的危害

1. 用户对单次的内存泄漏并没有什么感知，但是当泄漏积累到内存都被消耗完，就会导致卡顿，甚至崩溃；
2. 当内存不足的时候，GC 会主动回收没用的内存，但是内存回收也是需要时间的。
3. 内存回收和 GC 回收垃圾资源之间高频率交替的执行，就会产生内存抖动。
4. 任何线程的任何操作都需要暂停，等待 GC 操作完成之后，其他操作才能够继续运行，所以垃圾回收运行的次数越少，对性能的影响就越少。

### 2.1 内存泄漏的原因

短生命周期的对象被长生命周期的对象持有，导致其无法被回收，造成了内存泄漏。

### 2.2 Android 中哪些对象被长生命周期对象持有会导致内存泄漏？

- Activity
- Fragment
- View
- Service

### 内存泄漏检测工具 - LeakCanary

LeakCanary 是 Square 公司为 Android 开发者提供的一个**自动检测内存泄漏的工具**。  
LeakCanary 本质上是一个基于 MAT 进行 Android 应用程序内存泄漏自动化检测的开源工具。

我们可以通过集成 LeakCanary 提供的 jar 包到自己的工程中，一旦检测到内存泄漏，LeakCanary 就会 dump Memory 信息，并通过另一个进程分析内存泄漏的信息并展示出来，随时发现和定位内存泄漏问题，极大地方便了 Android 应用程序的开发。

**引用方式：**
```groovy
debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.12'
```


## 内存泄漏优化

### 常见的内存泄漏案例

1. **静态变量持有 Activity/Context**
   ```java
   // 错误写法
   public class Utils {
       public static Context sContext;
   }
  静态变量会导致 Context 始终无法释放，Activity 退出后也不会回收，造成泄漏。



2. **非静态内部类/匿名类持有外部类引用**

```java
   public class MyActivity extends Activity {
       private Handler handler = new Handler() {
           @Override
           public void handleMessage(Message msg) {
               // 处理消息
           }
       };
   }
   // Handler 的非静态内部类持有外部 Activity 的引用，导致 Activity 无法被回收
   //使用静态内部类或弱引用可以避免这种情况。

3. **单例模式持有 Activity/Context 引用**
  ```java
  public class MyManager {
      private static MyManager instance;
      private Context context;
      public static MyManager getInstance(Context ctx) {
          if (instance == null) {
              instance = new MyManager(ctx);
          }
          return instance;
      }
      private MyManager(Context ctx) {
          this.context = ctx;
      }
  }
  // 单例的生命周期比 Activity 长，Context 持有导致无法回收
  ```
4. **资源未关闭/未释放**
  ```java
  Cursor cursor = db.rawQuery("SELECT * FROM user", null);
  // cursor.close() 忘记调用，造成内存泄漏

  // 使用后未调用 release() 方法，导致资源未释放
  ```

5. **集合未清理**
```java
private List<Activity> activities = new ArrayList<>();
// 退出时未从集合中移除 Activity，导致无法回收
```

6. **注册未反注册（如广播、EventBus、Listener等）**
```java
// 注册监听器
someView.addOnAttachStateChangeListener(this);
// 忘记注销监听器，导致 Activity 一直被引用
```

7. **WebView 未正确销毁**
```java
WebView webView = new WebView(context);
// 忘记调用 webView.destroy()，导致内存泄漏
```

## 总结

1. Java 垃圾回收机制和四种引用类型
2. 使用 LeakCanary 检测内存泄漏
3. 学习常见的内存泄漏场景和解决方案
4. 深入学习 LeakCanary 的原理


## 1. ANR 是什么

ANR 全称为 Application Not Response。Android 设计 ANR 的用意，是系统通过与之交互的组件以及用户交互进行超时监控，用来判断应用进程是否存在卡死或响应过慢的问题。通俗来说就是很多系统中看门狗（watchdog）的设计思想。
---

## 2. 导致 ANR 的原因

耗时操作导致 ANR，并不一定是 app 的问题，实际上有很大概率是系统原因导致的 ANR。下面简单分析一下哪些操作是应用层导致的 ANR，哪些是系统导致的ANR。

### 应用层导致 ANR：

- **函数阻塞**：如死循环、主线程 IO、处理大数据。
- **锁出错**：主线程等待子线程的锁。
- **内存紧张**：系统分配给一个应用的内存有上限，长期处于内存紧张，会导致频繁内存交换，进而导致应用的一些操作超时。

### 系统层导致 ANR：

- **CPU 被抢占**：一般来说，前台在玩游戏、播放视频，可能会导致你的后台操作被抢占。
- **系统服务无法及时响应**：比如获取系统服务、系统跨进程 Binder 调用等，系统的服务能力有限，可能因服务长时间不响应导致 ANR。
- **其他应用占用大量内存**


## 3. 线下拿到 ANR 日志

- `adb pull /data/anr/`
- `adb bugreport`

**缺陷：**
- 只能线下，用户反馈时，无法获取 ANR 日志。
- 可能没有堆栈信息。

## 4. ANR 场景

- **Service Timeout**：比如前台服务在 20s 内未执行完成，后台服务 Timeout 时间是前台服务的 10 倍，即 200s；
- **BroadcastQueue Timeout**：比如前台广播在 10s 内未执行完成，后台为 60s；
- **ContentProvider Timeout**：内容提供者，在 publish 超过 10s；
- **InputDispatching Timeout**：输入事件分发超时 5s，包括按键和触摸事件。
