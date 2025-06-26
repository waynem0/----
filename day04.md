# Android 开发 day04笔记

## 一、四大组件简介

| 组件              | 作用说明                     | 典型场景        | 注意点         |
|-------------------|-----------------------------|----------------|--------------|
| Activity          | 展示界面，与用户交互         | 各种界面页面    | 生命周期管理   |
| Service           | 执行后台任务，无界面         | 音乐、下载      | 非进程，非独立线程 |
| BroadcastReceiver | 响应广播事件，系统/自定义    | 各种广播监听    | onReceive快速返回 |
| ContentProvider   | 跨应用共享数据，提供统一接口 | 通讯录、媒体库  | 权限和数据安全 |

---

## 二、四大组件之间的关系

- **通信方式**：Activity、Service、BroadcastReceiver 可通过 Intent 通信和启动。
- **数据共享**：ContentProvider 通过 URI 提供数据操作接口，供其他组件或应用调用。

---

## 三、Activity 生命周期

mermaid
graph TD
A[onCreate] --> B[onStart] --> C[onResume] 
C --> D[onPause] --> E[onStop] --> F[onDestroy]
E --> G[onRestart] --> B


- `onCreate()`：初始化、设置界面，必须实现。
- `onStart()`：Activity 变为可见。
- `onResume()`：Activity 获得焦点，开始与用户交互。
- `onPause()`：Activity 部分不可见，常用于保存数据。
- `onStop()`：Activity 完全不可见，适合释放资源。
- `onRestart()`：Activity 从停止状态重启。
- `onDestroy()`：销毁前清理资源。

---

## 四、Activity 启动模式

| 启动模式        | 特点                                         | 应用场景               |
|----------------|----------------------------------------------|-----------------------|
| standard       | 默认模式，每次启动都新建实例                    | 大部分普通页面         |
| singleTop      | 栈顶已是目标实例则复用，否则新建                | 消息详情等跳转自身页面  |
| singleTask     | 任务栈中只有一个实例，复用并清空其上的 Activity  | 浏览器主页、微信首页    |
| singleInstance | 独占任务栈，全局单例                           | 全局浮窗、通知中心      |

- **singleTop ,singleTask 区别**：  
  singleTop 只有栈顶是目标才复用；  
  singleTask 只要栈中有就复用，并清空其上的 Activity。

---

## 五、Activity 避坑指南

1. **资源回收**：及时释放资源、注销监听，防止内存泄漏。
2. **内存泄漏**：避免 Activity 静态引用或单例持有 Context。
3. **Context使用**：优先使用 Application Context，避免 Activity 泄漏。
4. **生命周期管理**：在合适的生命周期方法释放资源。
5. **横竖屏切换**：要保存与恢复状态，防止界面丢失。

---

## 六、Service 基础与避坑

### Bound Service 生命周期
- `onCreate()`：初始化服务，只执行一次。
- `onBind()`：返回 IBinder 供客户端通信。
- `onUnbind()`：所有客户端解绑时调用。
- `onDestroy()`：服务最终销毁释放资源。

### Service 避坑笔记
1. **服务被系统杀死**：   
   原因：后台服务优先级低，Android内存紧张时会直接回收。  
   优先用前台服务提升优先级。  
2. **内存泄漏**：  
   原因：服务没被正确停止，或没释放资源（比如handler、广播、线程等）。  
   服务用完及时 stopSelf()/stopService() 并释放资源。
3. **主线程阻塞**：  
   原因：在服务的主线程（也就是UI线程）执行耗时操作，影响应用响应。  
   耗时操作要开子线程或用异步任务，**切勿阻塞主线程**。
4. **无法与服务通信**：  
   解决：确保你的Service的 onBind() 方法返回的是正确的 Binder 实例。  
   确保你的service的onBind() 返回有效 IBinder 实例。
5. **ServiceConnection实现不完整**：  
   原因：ServiceConnection 没实现好，比如 onServiceDisconnected() 没处理意外断开。  
   正确实现 onServiceConnected/onServiceDisconnected，断开时释放引用。

---

## 七、AIDL 基础

- **全称**：Android Interface Definition Language
- **用途**：用于 Android 跨进程通信（IPC）
- **原理**：底层基于 Binder，C/S 架构

---

## 八、BroadcastReceiver 避坑笔记

1. **应用内广播优先 LocalBroadcastManager**：效率高、安全。
2. **避免静态注册唤醒问题**：优先动态注册，防止被系统唤醒影响性能。
3. **onReceive 不做耗时操作**：如需长时间操作，启动新线程或 Service。

---

## 九、ContentProvider 相关

### URI 及其结构

- **URI（统一资源标识符）**：唯一标识资源的字符串  
  例：content://com.example.app.provider/table/1
- **结构**：
  - scheme（协议）：content、http、file 等
  - authority（权限/主机）
  - path（资源路径）
  - 完整格式：scheme://host:port/path

### ContentResolver 作用
- 负责应用与 ContentProvider 通信，是数据访问的中介。

### ContentProvider 避坑笔记

1. **权限声明**：敏感数据需声明权限并动态申请（如 READ_CONTACTS）。
2. **异步加载**：大量数据应异步读取，避免阻塞主线程。
3. **Cursor 管理**：用完及时 close()，防止内存泄漏。
4. **数据缓存**：频繁读取建议缓存，减少数据库访问。
5. **错误处理**：注意查询结果为 null 的判断与处理。

---

## 十一、AIDL（Android Interface Definition Language）基础

- **作用**：用于不同进程间通信（IPC）。
- **实现方式**：C/S架构，客户端通过AIDL接口与服务端通信。
- **底层原理**：基于Binder驱动。
- **开发流程简述**：
  1. 定义 `.aidl` 文件声明接口。
  2. 编译生成对应的Java接口文件。
  3. 服务端实现该接口，客户端通过bindService绑定并调用方法。

---