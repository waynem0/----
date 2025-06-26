# Day07 安卓动画基础与进阶笔记

## 一、目标与收益

### 目标

- 理解 Android 动画的基本概念和原理
- 掌握 Android 动画的使用方法

### 收益

- 能够独立设计、开发常见的动画效果

---

## 二、知识结构

### 1. 认识动画

- 动画的基本概念
- 动画的分类与应用场景

### 2. 帧动画（Drawable Animation）

- 利用一组图片帧，逐帧切换实现动画
- 常用于简单的逐帧动画效果

### 3. 补间动画（Tween Animation）

- 基础知识
- **平移动画**：控件在屏幕上移动
- **缩放动画**：控件尺寸变化
- **旋转动画**：控件旋转
- **透明动画**：控件透明度变化
- 进阶：组合动画（多种动画效果叠加使用）

### 4. 属性动画（Property Animation）

- 基础知识
- **ObjectAnimator**：对对象属性进行动画
- **组合动画**：多动画同时或顺序执行
- **ValueAnimator**：自定义动画数值变化过程
- **ViewPropertyAnimator**：简化的属性动画用法
- 进阶：估值器、插值器（自定义动画过程和速率）

---

## 三、动画实现要点

### 1. 基础动画

- 使用补间动画或简单属性动画实现位移、缩放、旋转、透明度等常规效果

### 2. 高阶复杂动画

- 组合动画：多个动画协同执行（如：ObjectAnimator + AnimatorSet）
- 插值器：调整动画的速率曲线（如加速、减速、反弹等）
- 估值器：自定义动画属性变化过程
- 动画监听器：监听动画的开始、结束、取消等事件

---

## 四、常见开发场景举例

- 按钮点击缩放反馈
- 页面切换滑动动画
- 列表刷新下拉动画
- 加载进度旋转动画

---

## Android 中的动画类型

### 1. 视图动画

- **帧动画**  
  帧动画就是将顺序播放预先定义好的一组图片，类似于看电影，一张图片一张图片连续播放。

- **补间动画**  
  补间动画是通过设置好动画的开始和结束位置，系统会在设置的动画时间内，从开始位置过渡到结束位置。

### 2. 属性动画

- **属性动画**  
  增强版的补间动画，区别是：
    - 补间动画只有四种类型，属性动画可以作用到任何属性上。
    - 补间动画只能作用在 View 及其子类上，属性动画可以作用在任何对象上。

---


### 帧动画

#### 作用对象

- 视图控件（View），如 TextView、Button 等
- 不可用于 View 组件的属性（如颜色、背景、长度等）

#### 使用实现方式

- 资源文件 XML
- 代码
  #### 资源文件方式 - XML 文件解释

**标签解析**
- `<animation-list>` 根标签：用于声明该文件是帧动画 xml 文件
- `<item>` 标签：用于声明帧动画每一帧的图片资源以及持续时间

**属性解析**
- `android:oneshot`：表示动画是否只执行一次，true 为只执行一次，false 为循环执行
- `android:drawable`：表示每一帧的图片资源
- `android:duration`：表示每一帧的图片显示多长时间，单位：毫秒（ms）

- **作用对象**  
  视图控件（View）

- **代码实现**  
  - `AnimationDrawable`
    - `addFrame()`
    - `setOneShot()`
    - `start()`

### 补间动画

#### 什么是补间动画

补间动画是通过设置好动画的开始和结束位置，系统会在我们设置的动画时间内，从开始位置过渡到结束位置。

#### 动画类型

- 平移动画（Translate）
- 缩放动画（Scale）
- 旋转动画（Rotate）
- 透明度动画（Alpha）

#### 作用对象

- 视图控件（View）
  - 如 Android 的 TextView、Button 等等
  - 不可作用于 View 组件的属性，如：颜色、背景、长度等

#### 实现方式

- 资源文件实现方式
- 代码实现方式

一般建议使用 XML 文件定义，因为更具可读性、可重用性。  
XML 文件放在 `res/anim/` 目录下。

#### 基础知识 - 标签和类

**XML 标签：**
- `<translate>`：平移动画
- `<scale>`：缩放动画
- `<rotate>`：旋转动画
- `<alpha>`：透明度动画
- `<set>`：组合动画

**类：**
- `TranslateAnimation`：平移动画
- `ScaleAnimation`：缩放动画
- `RotateAnimation`：旋转动画
- `AlphaAnimation`：透明度动画
- `AnimationSet`：组合动画

#### 基础知识 - XML 与类对应关系

| XML 标签      | Java 类             | 描述         |
| ------------- | ------------------- | ------------ |
| `<translate>` | TranslateAnimation  | 平移动画     |
| `<scale>`     | ScaleAnimation      | 缩放动画     |
| `<rotate>`    | RotationAnimation   | 旋转动画     |
| `<alpha>`     | AlphaAnimation      | 透明度动画   |
| `<set>`       | AnimationSet        | 组合动画     |
#### 基础知识 - 公共属性和方法

| XML 属性             | Java 方法                         | 描述                                                         |
|----------------------|-----------------------------------|--------------------------------------------------------------|
| android:duration     | setDuration(long)                 | 动画持续时间，单位：毫秒（ms）                               |
| android:repeatCount  | setRepeatCount(int)               | 动画重复次数                                                 |
| android:repeatMode   | setRepeatMode(int)                | 重复类型有两个值，reverse 表示倒序回放，restart 表示从头播放 |
| android:startOffset  | setStartOffset(long)              | 调用 start() 之后等待开始运行的时间，单位：毫秒（ms）        |
| android:interpolator | setInterpolator(Interpolator)     | 设置插值器                                                   |
#### 平移动画 - 属性介绍

**取值规则**

X、Y 轴坐标取值可以传入三种类型：

- **数值类型**  
  例如：`android:fromXDelta="150"`  
  表示以当前 View 左上角坐标加 150px 作为起始点。

- **百分比**  
  例如：`android:fromXDelta="50%"`  
  表示以当前 View 左上角加上当前 View 宽度（或高度）的 50% 作为起始点。

- **百分比p**  
  例如：`android:fromXDelta="50%p"`  
  表示以当前 View 左上角加上父 View 宽度（或高度）的 50% 作为起始点。

## 属性动画
### 什么是属性动画
属性动画是 Android 3.0 引入的动画框架，提供了更强大的灵活性和功能。它可以对任何对象的属性进行动画处理，而不仅限于 View。
### 属性动画的核心类
- **ObjectAnimator**：对对象的单个属性进行动画处理。
- **ValueAnimator**：提供动画数值变化的基础类，可以自定义动画过程
- **AnimatorSet**：将多个动画组合在一起，可以同时或顺序执行。
- **ViewPropertyAnimator**：简化的属性动画用法，专门用于 View
- **Interpolator**：用于控制动画的速率变化，可以自定义动画的加速、减速等效果。
### 属性动画的使用步骤
1. 创建动画对象（如 ObjectAnimator、ValueAnimator 等）。  
2. 设置动画属性，如起始值、结束值、持续时间等。
3. 设置插值器（可选），控制动画的速率变化。
4. 启动动画。
5. 添加动画监听器（可选），监听动画的开始、结束、取消等事件。
6. 在需要时取消动画。
7. 在动画结束后，更新 View 的属性状态（如位置、透明度等）。
### 属性动画的核心概念
- **属性**：动画作用于对象的属性，如位置、透明度等。
- **估值器**：计算动画属性的变化过程，可以自定义动画的数
值变化。


### 组合动画
- 实现方式：
  - 使用`AnimatorSet`类。
- 示例代码：
```java
AnimatorSet set = new AnimatorSet();
set.playTogether(
    ObjectAnimator.ofFloat(view, "translationX", 0f, 100f),
    ObjectAnimator.ofFloat(view, "rotation", 0f, 360f)
);
set.setDuration(1000);
set.start();
```
### ValueAnimator
- 定义：
  - 是属性动画的核心类，可以生成任意类型的值的动画。
- 示例代码：
```java
ValueAnimator animator = ValueAnimator.ofInt(0, 100);
animator.setDuration(1000);
animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator animation) {
        int value = (int) animation.getAnimatedValue();
        // 更新UI或其他逻辑
    }
});
animator.start();
```
### ViewPropertyAnimator
- 定义：
  - 是专门针对View的属性动画工具类，语法简洁。
- 示例代码：
```java
view.animate()
    .translationX(100f)
    .rotation(360f)
    .setDuration(1000)
    .start();
```
### 进阶：估值器、插值器
- 估值器
  - 自定义动画值的计算方式。
  - 示例代码：
  ```java
  ValueAnimator animator = ValueAnimator.ofObject(new TypeEvaluator<Color>() {
    @Override
    public Color evaluate(float fraction, Color startValue, Color endValue) {
        // 自定义颜色渐变逻辑
        return new Color(startValue.red + (endValue.red - startValue.red) * fraction,
                        startValue.green + (endValue.green - startValue.green) * fraction,
                        startValue.blue + (endValue.blue - startValue.blue) * fraction);
    }
    }, new Color(255, 0, 0), new Color(0, 255, 0));
    animator.setDuration(1000);
    animator.start();
    ```
    - 插值器：
      - 控制动画的速度曲线。
      - 示例：
      ```java
      ObjectAnimator animator = ObjectAnimator.ofFloat(view, "translationX", 0f, 100f);
      animator.setDuration(1000);
      animator.setInterpolator(new AccelerateDecelerateInterpolator()); // 加速减速插值器
      animator.start();
      ```
  