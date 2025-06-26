# 安卓开发笔记 Day05
## 权限控制
- 什么是 Android 的权限控制
- 普通权限
- 危险权限
- 权限组
## 数据存储
- 轻量存储（SharedPreferences、MMKV）
- SQLite 数据库
## 网络请求
- Http 基本介绍（请求方式/状态码）
- 基本的 Android 网络通讯
- OkHttp 框架
- Retrofit 框架
## Handler
- Handler 的基本使用
- Handler 机制
- 子线程 Handler  

## 权限控制
### 权限是什么？
Android 设置权限是为了保护用户隐私。对于敏感数据，应用必须向用户申请授权后才能访问。
Android 6.0 以后，权限分为两类：
- **Normal Permission**：无需用户操作即可授权，如访问网络。
- **Dangerous Permission**：涉及用户隐私，需用户在操作时授权，如读取联系人。如果用户拒绝，将无法进行相关操作。
### 动态权限处理常用方法
- `checkSelfPermission`：检查是否拥有某个权限
- `requestPermissions`：请求某个权限
- `onRequestPermissionsResult`：获取权限请求结果
- `shouldShowRequestPermissionRationale`：是否应显示请求权限的说明
### 权限组
- Android 会将所有敏感权限进行分组，称为权限组。  
  同一权限组内的敏感权限会自动合并授权。
- 权限组是逻辑相关的权限集合。例如，发送和接收短信都属于短信权限组。
- 作用：当应用请求密切相关的多个权限时，系统会合并弹窗，减少对用户的打扰。
## 网络请求
### Http 基本介绍

- **请求方式**：常用有 GET、POST
- **状态码**：用于标识请求结果，如 200（成功）、404（未找到）、500（服务器错误）等
---
### GET 请求
1. 用于从服务器获取数据
2. 参数拼接在 URL 后面，用 `?` 分割，多个参数用 `&` 连接（如 `?a=1&b=2`）
3. 数据最大提交长度一般为 1024 字节
4. 适合请求静态资源，会被缓存
5. 不安全，参数可见
---
### POST 请求
1. 用于向服务器写入/提交数据
2. 参数放在 request body 中，更安全
3. 发送数据长度无限制
4. 适合提交敏感或大量数据

---
### 常见网络请求流程

    A(发起请求) --> B(开启子线程)--> C(请求服务器)--> D(获取结果)--> E{根据业务场景}--> F1(切换主线程)
    F1 --> G1(更新UI)
    E --> F2(当前线程)
    F2 --> G2(更新数据)
    E --> F3(切换任务线程)
    F3 --> G3(与其他任务协同)

#### HttpClient 简介
- HttpClient 是 Apache 的一个三方网络框架，对网络请求做了完善的封装，API 丰富、易用，开发效率高。
- 优点：实现稳定、Bug 较少、扩展性强。
- 缺点：API 太多，难以维护兼容性。
- Android 团队对 HttpClient 的优化积极性不高，从 Android 5.0 起被废弃，6.0 起彻底移除。
#### OkHttp 简介
- OkHttp 是HTTP 客户端，支持 HTTP/2、SPDY、连接池、GZIP 压缩、HTTP 缓存等特性。
**优点：**
1. 支持 HTTP/2，多路复用同一 TCP 连接，提升并发性能
2. 支持连接池技术，减少延时
3. 支持 GZIP，自动压缩和解压数据
4. 自动缓存，可直接重发请求
5. 连接问题自动恢复，常用场景更稳定
### OkHttp 使用

- 导入组件：
  ```gradle
  implementation 'com.squareup.okhttp3:okhttp:4.12.0'
### OkHttp 关键请求流程

1. 创建 OkHttpClient 对象（全局复用）
2. 构建 Request 对象（指定 URL、方法、参数等）
3. 创建 Call 对象（由 client.newCall(request) 得到）
4. 执行请求（同步 execute() 或异步 enqueue()）
5. 处理 Response 对象（获取数据、状态码等）
### OkHttp 关键内容总结

1. **OkHttpClient 复用**
   - OkHttpClient 是所有请求的核心，负责连接池、线程池管理，建议全局只建一个实例。

2. **Request/Response 封装**
   - Request 封装请求细节（URL、Header、Body），Response 封装服务端返回（数据、状态码等），链式操作，易扩展。

3. **同步与异步请求**
   - `execute()` 为同步请求，`enqueue()` 为异步请求（回调主/子线程处理结果），实际开发更常用异步，避免主线程阻塞。

4. **拦截器（Interceptor）机制**
   - 支持全局/单次请求添加拦截器，可统一处理日志、Token、重试等，方便扩展。

5. **高效连接池与缓存**
   - 内置连接池、GZIP 压缩、缓存，提升性能和请求速度。

6. **与 Retrofit 集成**
   - Retrofit 网络请求底层全部通过 OkHttp 实现。


> **一句话总结：**
>
> OkHttp 是 Android 网络请求的事实标准，结构清晰，扩展性强，是 Retrofit 等主流框架的底层实现。
### Retrofit 简介与优点

- Retrofit 是一个流行的网络请求框架，底层用 OkHttp 实现请求，负责网络接口封装和数据解析。
- 实质上，Retrofit 组装参数和接口，实际网络操作交由 OkHttp 完成，结果返回后 Retrofit 解析数据。

**优点：**
1. 接口定义和实现解耦，代码结构清晰
2. 可配置多种 httpClient（如 OkHttp、HttpClient）
3. 支持同步、异步和 RxJava
4. 支持多种数据反序列化工具（如 JSON、XML）
5. 请求速度快，使用简单灵活
### SharedPreferences

- SharedPreferences 是 Android 平台上的轻量级存储类，用于保存应用的各种配置信息。
- 本质是以“键-值”对的方式存储数据到 XML 文件。
- 文件路径：`/data/data/<包名>/shared_prefs/` 目录下。
### MMKV

- MMKV 是基于 mmap 内存映射和 protobuf 序列化优势结合的高性能存储框架。
- 相比 SharedPreferences，MMKV 性能更高，稳定性更好，能解决性能瓶颈和 ANR 问题，常用于替代 SharedPreferences。

- 导入组件：
  ```gradle
  implementation 'com.tencent:mmkv:1.3.1'
### SQLite
- 在 Android 应用中使用 SQLite，需要自己创建数据库、表、索引，并填充数据。
- Android 提供了 `SQLiteOpenHelper` 类，继承它可以方便地管理数据库的创建与更新。
- 数据库存储路径：`/data/data/<包名>/databases/`
- `SQLiteOpenHelper` 封装了数据库的创建和升级逻辑，简化了操作流程。- 
### SQLiteOpenHelper

- Android 提供的辅助类，用于数据库的创建和升级。
- SQLiteOpenHelper 是一个抽象类，使用时需自定义类继承它。
- 必须重写两个方法：
  - `onCreate()`：实现数据库的创建逻辑
  - `onUpgrade()`：实现数据库升级（表结构变更等）逻辑

| 参数         | 含义说明                                         |
| ------------ | ------------------------------------------------ |
| values       | 指定要更新的字段及对应的字段值                   |
| whereClause  | 指定条件语句，可以使用占位符                     |
| whereArgs    | 当条件中含有占位符时，改参数用于指定各占位符的值 |
| 返回值       | 返回影响的数据条数                               |


### 什么是 UI 线程

- UI 线程是 Android 应用中的特殊线程，负责处理界面更新和事件处理。
- 所有 UI 操作（界面刷新、事件响应、视图绘制）都必须在 UI 线程中执行。
- 由于 Android 的界面工具包不是线程安全的，所有 UI 操作只能在同一个线程被串行执行。
- UI 线程也叫主线程。
### 什么是 Handler

- Handler 是 Android 的消息传递机制，主要用于**线程**间通信。    
- 常见场景：子线程通过 Handler 把更新 UI 的操作消息发送到主线程（UI 线程），完成 UI 刷新。
- 这样可以在多线程应用中实现异步消息处理，保证 UI 安全更新。

---
| 方法名                          | 说明                                                                                       |
| ------------------------------ | ------------------------------------------------------------------------------------------ |
| sendMessage() 系列              | 发送普通消息、延迟消息，最终调用 `queue.enqueueMessage()` 方法将消息存入消息队列           |
| post() 系列                     | 提交普通/延迟 Runnable，封装成 Message 后调用 sendMessage() 存入消息队列                   |
| dispatchMessage(Message msg)    | 分发消息，优先执行 `msg.callback`（即 runnable），其次 `mCallback.handleMessage()`，最后 `handleMessage()` |

[工作线程] --(更新UI的操作信息)--> [Handler] --(传递到工作线程更新的UI操作信息)--> [主线程(UI线程)]
↑
[在主线程中更新UI]

### Handler 机制组成部分

- **Message**  
  就是消息，包含消息的描述和数据。

- **MessageQueue**  
  消息队列，用于存储 Message 对象。每个线程只有一个消息队列。

- **Looper**  
  每个线程只能有一个 Looper，用于管理 MessageQueue，负责循环读取 MessageQueue 中的消息并交给 Handler 处理。

- **Handler**  
  用于发送和处理消息。在子线程或当前线程发送 Message 加入消息队列，同时 Looper 消费（分发）消息并回调 Handler 的处理方法。
- **设计模式：生产者——消费者模式** 
### 子线程中如何使用 Handler
- 子线程中如果要用 Handler，必须手动调用 `Looper.prepare()` 和 `Looper.loop()` 方法，给该线程创建消息队列和消息循环。

