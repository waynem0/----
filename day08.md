# 安卓开发笔记 Day8
# 自定义控件的实现

## 目录
- View 绘制过程
- 自定义View
- 自定义ViewGroup‘
- 响应手势操作

## 一、View 绘制过程
### View 绘制过程

#### 知识点
1. **Activity 组件的职责有哪些？**  
   - 作为用户界面的载体，负责与用户交互  
   - 管理界面的生命周期和状态  
   - 响应用户操作，处理事件  
   - 启动和切换其它 Activity  

2. **学习了哪些 View（控件）？**  
   - TextView  
   - EditText  
   - Button  
   - ImageView
   - ...

3. **学习了哪些 ViewGroup（布局）？**  
   - LinearLayout  
   - RelativeLayout  
   - FrameLayout  
   - ConstraintLayout  
   - GridLayout  
   - TableLayout    
   - RecyclerView  
   - FlexboxLayout（流式布局/第三方）  

### Activity和View的关系

1. Activity中展示内容的职责由Window类（由子类PhoneWindow具体实现）承担
2. Window创建出根布局DecorView（继承自FrameLayout）

> 结构示意图：
> - Activity
>   - Window（PhoneWindow）
>     - DecorView（FrameLayout）

### 实现界面
我们根据需求在根布局下完成布局树

```xml
<LinearLayout>
    <TextView />
    <ImageView />
    <LinearLayout>
        <Button />
    </LinearLayout>
</LinearLayout>
```
布局在内存中会形成树状结构
ViewGroup 负责管理子 View 的层级和排列

简化结构示意：

- ViewGroup
    - ViewGroup---------View--------- View
        - View
        - View

### 标签云控件的布局文件

```xml
<com.example.demo.MiniFlowLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView ... />
    <TextView ... />
    <TextView ... />
    <!-- 添加多个宽度不一的子View -->

</com.example.demo.MiniFlowLayout>
```

## 二、View 绘制过程
### View 绘制过程概述
1. **测量阶段**：确定每个View的大小和位置
   - 测量View的宽高
   - 计算子View的大小和位置 
2. **布局阶段**：确定每个View在父容器中的位置
   - 确定子View的左上角位置
   - 设置子View的边界和位置
3. **绘制阶段**：将View的内容绘制到屏幕上
   - 调用draw方法进行绘制
  
### measure函数
1. 作用：测量View的大小
2. 实现：通过调用子View的measure方法来测量子View的大小
3. 测量模式：有三种测量模式
   - **EXACTLY**：精确值，View的大小已确定
   - **AT_MOST**：最大值，View的大小不超过指定值
   - **UNSPECIFIED**：未指定，View可以任意大小
   - **MeasureSpec**：由父View传递给子View的测量要求，包含大小和模式
   - **MeasureSpec的计算公式**：`MeasureSpec = View的大小 + View的测量模式`
   - **MeasureSpec的类型**：`MeasureSpec.EXACTLY`、`MeasureSpec.AT_MOST`、`MeasureSpec.UNSPECIFIED`
   - **MeasureSpec的获取方法**：`View.getMeasuredWidth()`、`View.getMeasuredHeight()`
   - **MeasureSpec的设置方法**：`View.setMeasuredDimension(int width, int height)`
   - **MeasureSpec的使用**：在onMeasure方法中使用MeasureSpec来测量子View的大小
### onMeasure函数
1. 作用：测量View的大小 
2. 实现：在onMeasure方法中调用子View的measure方法来测量子View的大小
3. 流程：
   - 获取父View传递的MeasureSpec
   - 根据MeasureSpec和子View的布局参数计算子View的测量要求
   - 调用子View的measure方法进行测量
   - 根据子View的测量结果计算当前View的大小
   - 调用`setMeasuredDimension(int width, int height)`保存当前View的大小
  
### onlayout函数
1. 作用：为View的子View分配位置
2. 实现：在onLayout方法中调用子View的layout方法来设置子View的位置
3. 流程：
   - 获取当前View的大小
   - 遍历所有子View
   - 根据子View的布局参数和当前View的大小计算子View的位置
   - 调用子View的layout方法设置位置
   - 注意：onLayout方法只负责设置子View的位置，不负责测量子View的大小
  
### draw函数

1. 作用：绘制内容在给定的Canvas上
2. 要保证layout之后再调用这个方法
3. 自定义View时优先考虑重写onDraw方法而不是这个方法
4. 如果一定要重写draw方法，要记得调用父类的这个方法
5. 实现：有7个绘制步骤

### draw函数和onDraw函数

| draw函数      | 定义了绘制的7个步骤，其中在第四个步骤绘制子View（即调用childView.draw）            |
|--------------|-------------------------------------------------------------------------------------|
| onDraw函数   | 由draw函数在绘制步骤中的第三步时调用，负责绘制内容                                  |

---

#### 问题：

1. 在draw方法中定义好绘制步骤，预留下onDraw方法让子类实现，这是什么设计模式？

   **答：模板方法模式（Template Method Pattern）。父类draw方法规定整体流程和骨架，具体实现细节（如onDraw方法）由子类实现，既保证流程统一，又支持灵活扩展。**

2. 考虑优先重写onDraw方法，但是，什么时候适合重写draw方法？

   **答：通常只需重写onDraw来绘制内容。只有在需要改变整个View的绘制流程（如自定义子View的绘制顺序、跳过某些系统绘制环节、特殊的控件嵌套等场景）时，才建议重写draw方法。普通自定义View建议只重写onDraw。**

### Canvas绘图API分类整理

| 类别                 | 方法示例                           | 说明                               |
|----------------------|------------------------------------|------------------------------------|
| 绘制基本几何图形     | `drawRect()`、`drawCircle()`、`drawOval()` | 矩形、圆、椭圆等简单形状           |
| 路径绘制             | `drawPath()`                        | 支持复杂曲线路径，如心形、波浪等   |
| 绘制文字             | `drawText()`                        | 控件中文字符、自定义文字布局       |
| 绘制图像             | `drawBitmap()`                      | 绘制位图、图标、图片等资源         |
| 设置填充颜色和样式   | `drawColor()`、`drawPaint()`         | 设置整体背景色或填充样式           |
| 图层与变换控制       | `save()`、`restore()`                | 创建局部画布状态（层叠效果）       |
| 坐标变换             | `translate()`、`rotate()`、`scale()` | 平移、旋转、缩放等坐标空间控制     |

## 三、自定义ViewGroup
### ViewGroup中MeasureSpec计算公式

子view的布局参数（通过`childView.getLayoutParams()`获取） + 父view的测量要求 = 子View的MeasureSpec

| 子View布局参数测量模式 | 精确值                                             | MATCH_PARENT                                   | WRAP_CONTENT                                   |
|----------------------|---------------------------------------------------|------------------------------------------------|------------------------------------------------|
| EXACTLY              | SpecSize: 精确值<br>SpecMode: EXACTLY             | SpecSize: View Group Size<br>SpecMode: EXACTLY | SpecSize: View Group Size<br>SpecMode: AT_MOST |
| AT_MOST              | SpecSize: 精确值<br>SpecMode: EXACTLY             | SpecSize: View Group Size<br>SpecMode: AT_MOST | SpecSize: View Group Size<br>SpecMode: AT_MOST |
| UNSPECIFIED          | SpecSize: 精确值<br>SpecMode: EXACTLY             | SpecSize: View Group Size<br>SpecMode: UNSPECIFIED | SpecSize: View Group Size<br>SpecMode: UNSPECIFIED |

### 自定义ViewGroup测量流程总结

- 父View测量要求（MeasureSpec）：作为onMeasure的入参，类型有 UNSPECIFIED、EXACTLY、AT_MOST
- onMeasure函数实现流程                          
1. 根据公式：子View布局参数 + 父View测量要求（+ 当前ViewGroup布局策略） = 子View测量要求 
2. 将子View测量要求作为参数，调用子View的measure方法
3. 根据**当前ViewGroup布局策略**，计算好自己的宽高

- 最后调用 `setMeasuredDimension(int, int)` 保存计算好的自己的宽高

### layout函数

1. 这是布局机制的第二阶段（第一阶段是测量）
2. 作用：给所有子View分配位置
3. 实现：在这个阶段，每个父View调用其所有子View的 `layout` 方法来确定它们的位置。
4. 派生类不应覆盖这个方法。有子View的派生类应该覆盖 `onLayout` 方法。
### onLayout函数

1. 被layout方法调用
2. 作用：为该布局的子View分配位置
3. 有子View的派生类应该重写这个方法，并对每个子View调用layout方法，通常是使用在测量过程中存储的子View尺寸来完成的。

### layout函数和onLayout函数

| layout函数   | 派生类不应覆盖这个方法。有子View的派生类应该覆盖 `onLayout` 方法。 |
|--------------|-------------------------------------------------------------|
| onLayout函数 | 被layout方法调用，有子View的派生类应该重写这个方法，并对每个子View调用layout方法，**通常是使用在测量过程中存储的子View尺寸来完成的。** |


## 四、响应手势操作
### 响应手势操作

MotionEvent类（运动事件）包含：ACTION（事件类型） + 事件位置

#### 事件类型说明：

| 事件常量              | 说明                       |
|-----------------------|----------------------------|
| ACTION_DOWN           | 按下                       |
| ACTION_POINTER_DOWN   | 第n个手指按下              |
| ACTION_MOVE           | 移动                       |
| ACTION_UP             | 抬起                       |
| ACTION_POINTER_UP     | 多指按下的前提下，抬起一个手指 |
| ACTION_CANCEL         | 取消                       |
#### 事件位置说明：
| 位置常量              | 说明                       |
|-----------------------|----------------------------|
| getX()                | 获取事件发生时的X坐标       |
| getY()                | 获取事件发生时的Y坐标       |
| getRawX()             | 获取事件发生时的原始X坐标    |
| getRawY()             | 获取事件发生时的原始Y坐标    |
### 手势操作的处理流程
用户的真实有意义的手势是由多个MotionEvent（运动事件）组合而成

例如滑动手势流程：

1. 用户按下View → **DOWN事件**
2. 用户移动手指 → **MOVE事件**（可能有无数个）
3. 用户抬起View → **UP事件** → 结束
4. 若因非人为原因（如系统打断） → **CANCEL事件** → 结束
### 手势操作的处理流程示意图
```      
用户按下View
    ↓
用户移动手指
    ↓
用户抬起View
    ↓
用户因非人为原因打断
      ↓
用户取消手势
```
### 常见手势的运动事件组合

| 手势           | 事件组合                                                                                                 |
|----------------|---------------------------------------------------------------------------------------------------------|
| 点击           | down + up                                                                                               |
| 长按           | down + 一段时间内没有move和up                                                                           |
| 滑动           | down + move + up                                                                                        |
| 双击           | down + up + down + up                                                                                   |
| 双指放大/缩小  | down + pointer_down + move + pointer_up + up                                                            |
| 长按拖动       | down + 满足触发长按的阈值（有视觉/震动反馈） + move + up                                                |
| 边缘滑动       | down + move + up（与普通滑动的区别在于起始位置在边缘）                                                  |
