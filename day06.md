# 安卓开发笔记 Day06
## 第一部分
重点介绍了组件库的基本使用和定制：
1. 组件库的简介
2. 组件库的构建、依赖方式
## 组件库文件格式
### Android 中常用组件的文件格式有 AAR 和 JAR 两种

| 格式 | 介绍 | 差异 |
|------|------|------|
| **AAR** | AAR 文件包含了 Android 库项目的代码、资源文件和清单文件等信息，可以被其他 Android 应用程序引用和使用 | Android 特有的归档文件格式，主要用于打包 Android 库项目 |
| **JAR** | JAR 文件包含了 Java 类文件、资源文件和清单文件等信息，可以被其他 Java 应用程序和使用 | Java 中常用的归档文件格式，用于打包 Java 类库和应用程序 |

#### 注意事项

1. **JAR 文件包中**是**不能包含**清单文件 `AndroidManifest.xml` 和资源 `res` 文件的
2. **AAR 文件包中**是**可以包含 JAR 文件**的

#### 文件目录示例

- `libs/` 文件夹下可能同时存在 `.aar` 和 `.jar` 文件，如 `CircleImageLib.aar`、`player.jar`  
- `.aar` 文件中可包含 `AndroidManifest.xml`、`res` 资源目录等  
- `.jar` 文件一般只包含 `.class` 字节码文件  


## Gradle 组件库依赖方式说明

在 Android 中，组件基于 Gradle 构建和发布。Gradle 支持组件库的依赖方式常用的有 `implementation` 和 `api`。

| 2.x 版本    | 3.x 版本         | 说明 |
|-------------|------------------|------|
| apk         | runtimeOnly      | apk 功能同 runtimeOnly。只在生成 apk 的时候参与打包，编译时不会参与。Gradle 只会将依赖项添加到构建输出，以便在运行时使用。 |
| provided    | compileOnly      | provided 功能同 compileOnly。只在编译时有效，不会参与打包至 apk。 |
| compile     | api              | compile 功能同 api。该依赖方式会传递所依赖的库，当其他 module 依赖了该 module 时，可以使用该 module 下 api 依赖的库。 |
| -           | implementation   | 该依赖方式所依赖的库不会传递，只会在当前 module 中生效。 |

## 组件库 - Room 数据库框架

Room 是 Android 架构组件之一，**基于 SQLite 构建**，在提供 SQLite 功能的基础上，进一步提供了更高级别的抽象。

Room 主要由三大组件组成：

- **Entity**（实体）
- **DAO**（Data Access Object，数据访问对象）
- **Database**（数据库）

相比直接使用 SQLite，Room 更加简单高效。开发者只需定义方法即可进行数据操作，无需手写大量 SQL 查询语句。

### Room 的主要优点

- **静态类型安全**  
  编译时检查 SQL 查询，减少运行时错误。

- **简化数据库操作**  
  提供简洁 API，减少模板代码。

- **支持 Kotlin 协程**  
  通过 Room 的 KTX 扩展，可与 Coroutines 结合异步操作。

- **迁移支持**  
  支持自动迁移和自定义迁移，方便数据库版本管理。

- **查询优化**  
  自动优化 SQL 查询，提高数据库访问性能。

### 基础依赖引入方式

```groovy
implementation "androidx.room:room-runtime:2.5.0"
annotationProcessor "androidx.room:room-compiler:2.5.0"
```

### Room 数据库结构汇总
描述数据库中的一张表。

```java
@Entity
public class User {
    @PrimaryKey
    public int id;
    public String name;
    public int age;
}

@Dao
public interface UserDao {
    @Insert
    void insertUser(User user);

    @Query("SELECT * FROM users")
    List<User> getAllUsers();

    @Update
    void updateUser(User user);

    @Delete
    void deleteUser(User user);
}

@Database(entities = {User.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract UserDao userDao();
}
```

## 组件库 - LiveData 数据绑定框架

**LiveData** 是一个可观察的数据持有者类，属于 Android 架构组件的一部分。  
它允许数据在生命周期感知的范围内进行观察，确保在数据变化时，UI 会自动更新。

### 优点

- **生命周期感知**  
  LiveData 会根据生命周期的状态自动管理观察者的订阅和取消订阅，避免内存泄漏。

- **主线程安全**  
  LiveData 确保在主线程中更新数据，避免在非主线程中更新 UI 导致的崩溃。

- **简化数据绑定**  
  通过观察 LiveData，可以轻松地在 UI 中绑定数据，减少样板代码。

- **支持双向绑定**  
  可以用于数据的读取和写入，实现数据的双向绑定。

## 组件库 - DataBinding 数据绑定框架


**Data Binding** 是一个强大的数据绑定框架，能够显著减少样板代码，提高代码的可读性和可维护性。  
通过使用 Data Binding，开发者可以更轻松地管理数据和 UI 之间的绑定逻辑。

### 优点

- **减少样板代码**  
  通过直接在 XML 布局文件中绑定数据，减少了手动 `findViewById` 调用和数据设置的代码。

- **提高代码可读性和可维护性**  
  数据绑定逻辑集中在一个地方，使代码更清晰、易于维护。

- **支持双向绑定**  
  支持数据从 UI 到数据模型的双向同步，简化复杂表单的处理。

- **事件处理**  
  可以在 XML 布局文件中直接处理事件，减少 Activity 或 Fragment 中的事件处理代码。

- **编译时检查**  
  Data Binding 提供编译时检查，减少运行时错误。

### 启用 DataBinding

在 `build.gradle` 中添加如下配置：

```groovy
defaultConfig {
    ...
    dataBinding.enabled = true
}
```  


## 组件库 - Glide

Glide 是 Android 常用图片加载库，支持网络、本地、GIF、视频帧等多种来源。  
特点：高效加载、自动缓存、支持图片变换、与生命周期绑定。

**依赖引入：**
```groovy
implementation 'com.github.bumptech.glide:glide:4.16.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.16.0'
```

## 组件库-媒体播放库

### 媒体播放库组件 - ExoPlayer

ExoPlayer 是 Google 开源的媒体播放器库，提供强大 API 和灵活架构，支持多种音视频格式（如 MP4、HLS、DASH 等）。  
因高性能、易扩展、兼容性好，在 Android 开发中广泛使用。

## 组件库-持久化存储

### 持久化存储组件 - MMKV
**概述**  
MMKV 是微信团队开源的高性能、通用 Key-Value 存储系统。基于 mmap 内存映射技术，提供快速、安全的读写操作，支持持久化存储，并能在多线程环境下保证数据一致性。

**技术特点**
1. **高效读写**：采用 mmap 内存映射技术，数据读写速度快。
2. **持久化存储**：数据持久化到磁盘，保证数据安全。
3. **智能内存管理**：自动回收不再使用的空间，内存占用低，避免泄漏。
4. **多线程安全**：支持多线程并发读写，保证数据一致性和完整性。
#### MMKV 的使用

步骤一：引入 MMKV 库
- 项目地址：[https://github.com/Tencent/MMKV](https://github.com/Tencent/MMKV)
- 依赖集成：
  ```groovy
  implementation 'com.tencent:mmkv:1.3.5'
  ```
步骤二：初始化MMKV
MMKV.initialize(context);
指定自定义 MMKV 存储路径
MMKV.initialize(context, "/sdcard/my_mmkv");
### MMKV 的特点（一）：mmap 零拷贝
- MMKV 基于 mmap 内存映射，充分利用 mmap 的高效读写特性。
- mmap 可将文件直接映射到虚拟内存，实现高效的数据读写。
- 用户空间是 APP 运行的内存，内核空间由系统管理。
- MMKV 利用 mmap，将数据持久化和内存操作合二为一，读写快、占用低。
  
#### 传统 IO 操作内存流程

- 用户空间 → 内核空间（CPU copy）→ 虚拟内存（DMA copy）→ 物理内存
- 数据传输需要经过多次拷贝，效率较低。

#### mmap 操作内存流程

- 用户空间**直接映射**到虚拟内存（mmap 映射），只需 DMA copy 到物理内存
- 避免了 CPU copy，效率更高。

### MMKV 的特点（二）：跨进程

MMKV 支持跨进程键值存储，核心原理是通过 Shared Memory 映射技术实现数据共享。

1. **创建共享内存映射**  
   进程 A 初始化 MMKV 时，系统会创建一个共享内存映射区。这个区域会映射到进程 A 的地址空间，准备进行数据写入。

2. **数据写入共享内存**  
   进程 A 调用 MMKV 的 put 方法，将数据序列化后写入共享内存。  
   MMKV 使用 B+Tree 结构高效管理和查找这些数据。

3. **通知其他进程数据更新**  
   数据写入共享内存后，进程 A 会向其他进程发送更新通知（如信号或写锁），实现进程间的数据同步。

4. **共享内存读取数据**  
  进程 B 收到数据更新通知后，会将共享内存区映射到自己的地址空间，直接读取共享数据。

5. **反序列化读取**  
  进程 B 通过 B+Tree 索引定位并读取数据，再进行反序列化，转为可用格式。

6. **数据一致性锁机制**  
  多进程环境下，MMKV 采用 CAS（Compare and Swap）机制自旋锁，避免冲突，确保每次只有一个进程成功写入，从而保证数据一致性和完整性。

## 组件库-瀑布流

### 瀑布流效果和实现方式

瀑布流是一种常见的布局方式，通常用于展示不规则高度的内容，如图片、卡片等。其主要特点是能够充分利用可用空间，避免留白，同时保持内容的可读性和美观性。
### 实现方式


1. **使用 RecyclerView**  
   在 Android 中，可以使用 RecyclerView 结合 GridLayoutManager 或 StaggeredGridLayoutManager 实现瀑布流效果。  
   - GridLayoutManager：适用于等高的网格布局。
   - StaggeredGridLayoutManager：适用于不等高的瀑布流布局。

2. **自定义 ItemDecoration**  
   通过自定义 ItemDecoration，可以为每个 item 设置间距、边距等，进一步优化瀑布流效果。

3. **动态加载数据**  
   结合 RecyclerView 的滑动监听，可以实现动态加载数据的功能，提升用户体验。

4. **使用第三方库**  


## 组件库-下拉刷新
### 下拉刷新 - 流程

1. **手指下拉界面**  
   用户下拉屏幕时，应用会监听这个手势，并作出响应。

2. **显示刷新状态**  
   检测到下拉动作后，界面显示“正在刷新”或“下拉刷新”等状态提示。

3. **触发刷新**  
   下拉距离达到一定程度时，应用触发刷新操作，重新加载当前内容。

4. **显示刷新进度**  
   刷新内容时，界面展示进度状态，让用户知晓刷新过程。

5. **完成刷新**  
   当前页面内容加载完成后，刷新状态隐藏，提示刷新完毕。

#### 下拉刷新整体布局结构

- Header View（顶部刷新区）
- Content View（内容区，可见部分）
- Footer View（底部）

（右侧图示为 Header、Content、Footer 三部分的整体布局）
### 下拉刷新 - 原理

1. **布局测量与绘制**  
   对刷新布局（如 RefreshLayout）来说，onMeasure 和 onLayout 需要对 Header、Content、Footer 进行测量和绘制。

2. **拦截下拉滑动事件**  
   在 onInterceptTouchEvent 中，判断是否为向下滑动（如 y 轴大于 0），并判断 RecyclerView 等第一个 item 是否在顶部，再决定是否拦截事件。

3. **手指滑动判断**  
   松开手指时，根据滑动增量判断是否为有效滑动，若是，则处理下拉刷新的业务逻辑。

4. **异步刷新与 UI 更新**  
   触发刷新后，异步线程处理刷新数据，通过 Handler 回调，更新 UI，刷新页面内容。
### SwipeRefreshLayout 组件使用

#### 基本用法

```java
// 设置下拉刷新的监听
swipeRefreshView.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
        // 开始刷新，可以设置为刷新状态
        // swipeRefreshLayout.setRefreshing(true);

        // 比如模拟网络请求或耗时操作
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                // 添加新数据
                mList.add(0, "新数据");
                mAdapter.notifyDataSetChanged();

                // 结束刷新，收起刷新动画
                swipeRefreshView.setRefreshing(false);
            }
        }, 200);
    }
});
```
### 上拉加载 - 核心思想 & 步骤

1. 监听 RecyclerView 的滑动回调事件 `RecyclerView.OnScrollListener`
2. 判断加载更多的条件：滑动到列表最后一个 Item 完全可见
3. 触发加载更多动画，执行加载更多业务事件的处理


## 组件库-事件传递组件
### EventBus 组件简介

EventBus 适用于 Android 和 Java 的发布/订阅事件总线，方便实现多线程间的消息通信。

**组件优势：**
1. 简化组件间的通信复杂度
2. 避免复杂依赖性和生命周期问题
3. 速度快、性能好、代码简单优雅
4. 解耦事件的发送者和接收者
5. 灵活方便，支持指定工作线程和优先级
6. 库资源占用小，内存开销低
### EventBus 组件简介与基本用法

- 项目地址：[https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)
- 依赖集成：
  ```groovy
  implementation 'org.greenrobot:eventbus:3.3.1'

## EventBus 组件使用

### 发布/订阅模式

- 订阅者（Subscriber）将订阅的事件（Event）注册（register）到调度中心（EventBus）。
- 发布者（Publisher）发布事件时，事件会由 EventBus 分发给所有订阅该事件的 Subscriber。
- 事件分发统一通过 onEvent() 或 @Subscribe 注解的方法处理。



### EventBus 组件使用 - 线程模型

**EventBus 3.0 四种线程模式：**

- **POSTING（默认）**  
  事件处理函数运行在线程与事件发布线程相同的线程。

- **MAIN**  
  事件处理函数运行在主线程（UI线程），不能执行耗时操作。

- **BACKGROUND**  
  事件处理函数运行在后台线程，不能进行 UI 操作。  
  - 如果事件在主线程发布，则开一个后台线程处理。  
  - 如果事件在后台线程发布，则直接复用该线程。

- **ASYNC**  
  事件处理函数运行在单独的异步线程（和事件发布线程不同）。

