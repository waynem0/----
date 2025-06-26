# Day01 学习笔记

## 一、Android 按照运行方式分为 3 个阶段

1. JIT（Just In Time，实时编译）
2. AOT（Ahead Of Time，预编译）
3. AOT+JIT（结合两者优势）

---

## 二、Android 系统可以分为 5 层

1. **Kernel 层**  
   - 基于 Linux 内核，提供硬件驱动和基础安全、内存管理等

2. **硬件抽象层（HAL）**  
   - 硬件和上层的桥梁，让不同硬件以统一接口和上层交互

3. **ART / C++ 层**  
   - Android Runtime（ART）和 C/C++ 库，支撑应用运行

4. **Java Framework 层**  
   - 提供系统核心服务与 API，如 Activity 管理、窗口管理、通知等

5. **App 层**  
   - 用户实际使用的 App（如微信、抖音等）


## 三、Android Runtime 启动流程

1. **Loader层：激活 Kernel**  
   当电源按下时，引导芯片代码从预定义的地方（固化在 ROM）开始执行，加载引导程序 BootLoader 到 RAM 中，然后执行。

2. **引导程序 BootLoader**  
   BootLoader 是在 Android 操作系统开始运行前的一个小程序，主要作用是把系统 OS 拉起来并运行。

3. **Linux 内核启动**  
   当内核启动时，设置缓存、受保护存储器、计划列表、加载驱动。在内核完成系统设置后，首先在系统文件中寻找 init.rc 文件，并启动 init 进程。

4. **init 进程启动**  
   - 创建和挂载启动所需的文件目录  
   - 初始化和启动属性服务  
   - 解析 init.rc 配置文件并启动 Zygote 进程

---
## 四、什么是 Git

> Git 是一个分布式源代码管理工具，用于高效地管理和协作开发代码。

### Git 上传到仓库的标准流程

1. **打开终端（Terminal）并切换到 day01 项目目录**  
   ```bash
   cd /DEMO/day0
   ```

2. **把文件添加到暂存区**

   ```bash
   git add .
   ```

3. **写一次 commit 信息**

   ```bash
   git commit -m "initial commit"
   ```

4. **推送到远程仓库**

   如果已经和远程库建立了关联（比如 origin/main），直接推：

   ```bash
   git push origin main
   ```

   如果第一次推送，先建立远程仓库链接（只需一次）：

   ```bash
   git remote add origin 仓库地址.git
   git push -u origin main
   ```

## 常用 Git 指令

- `git status` 查看当前状态

- `git log` 查看提交历史

- `git add 文件名` 添加文件到暂存区

- `git commit -m "注释"` 提交更改

- `git push` 推送到远程仓库

- `git pull` 拉取远程最新代码



