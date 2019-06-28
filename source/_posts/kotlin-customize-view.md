---
title: Kotlin 自定义环形进度控件
date: 2019-06-27 18:07:24
tags: android
categories: Android
---

Android 的自定义 View 属于 Android 开发的基础技能，但是要能够熟练的实现设计所需的 View 也需要多练习实践。

### 自定义 View 步骤

1. 创建 View
2. 处理 View 布局
3. 绘制 View
4. 处理 交互事件
5. 优化自定义 View

### 自定义 View 关键方法

```kotlin
onMeasure
onDraw
onTouchEvent
```

### 继承 View

这里记录实现一个圆环进度控件 RingView 的步骤。

```kotlin
class RingView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) :
    View(context, attrs, defStyleAttr)
```

这里使用了 Kotlin 中的 `@JvmOverloads` 注解来标记构造函数，`@JvmOverloads` 的作用就是能够使 RingView 的单个构造函数在编译过程中通过默认值来生成三个不同的构造函数，等同于 Java 实现三个参数不同的构造函数。

### 测量 View 的绘制区域

因为 RingView 是一个圆环进度控件，所以所需的绘制区域是一个正方形，View 的可绘制区域受到父控件的限制，所以正方形区域的边长则取 width 和 height 中的 min 值。

```kotlin
  override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        val widthSpecSize = MeasureSpec.getSize(widthMeasureSpec)
        val heightSpecSize = MeasureSpec.getSize(heightMeasureSpec)
        val minSize = min(widthSpecSize, heightSpecSize)
        setMeasuredDimension(minSize, minSize)//保证绘制区域为正方形
    }
```

### 绘制 View

定义绘制图形所需要的一些属性

```kotlin
    private val maxValue = 100f//圆环最大值
    private var currentValue = 10f//圆环当前值
    private var ringColor = Color.WHITE//外层圆环颜色
    private var currentColor = Color.BLUE//内层圆弧颜色
    private var textColor = Color.BLACK//字体颜色
    private var ringWidth = 100f//外层圆环宽度
    private val currentRect = RectF()//画内层圆弧所需
```

如果需要在 xml 中能够配置某些属性则需要在 attrs 文件中声明属性

```xml
<resources>
    <declare-styleable name="RingView">
        <attr name="ringColor" format="color" />
        <attr name="currentColor" format="color" />
        <attr name="textColor" format="color" />
    </declare-styleable>
</resources>
```

在自定义 View 中获取这些 xml 中配置的属性

```kotlin
    init {
        attrs?.let {
            val typeArray = context.obtainStyledAttributes(it, R.styleable.RingView)
            ringColor = typeArray.getColor(R.styleable.RingView_ringColor, Color.WHITE)
            currentColor = typeArray.getColor(R.styleable.RingView_currentColor, Color.BLUE)
            textColor = typeArray.getColor(R.styleable.RingView_textColor, Color.BLACK)
            typeArray.recycle()
        }
    }
```

绘制图形的关键类 `Paint Canvas`

```kotlin
    override fun onDraw(canvas: Canvas) {
        val center = width / 2f//圆心点
        val radius = center - ringWidth / 2//圆环半径
        //画圆环底色
        paint.isAntiAlias = true
        paint.color = ringColor
        paint.style = Paint.Style.STROKE
        paint.strokeWidth = ringWidth
        canvas.drawCircle(center, center, radius, paint)
        //画圆环进度弧形
        val currentAngle = currentValue / maxValue * 360f//进度弧形角度
        currentRect.set(ringWidth / 2f, ringWidth / 2f, width - ringWidth / 2f, width - ringWidth / 2f)
        paint.color = currentColor
        paint.strokeWidth = ringWidth - 20f
        canvas.drawArc(currentRect, 90f, currentAngle, false, paint)
        //画百分比文字
        paint.style = Paint.Style.FILL
        paint.color = textColor
        paint.strokeWidth = 12f
        paint.textSize = 100f
        paint.typeface = Typeface.DEFAULT_BOLD
        paint.textAlign = Paint.Align.CENTER
        val text = "${(currentValue / maxValue * 100).toInt()}%"
        val fontMetrics = paint.fontMetrics
        val distance = (fontMetrics.bottom - fontMetrics.top) / 2f - fontMetrics.bottom
        canvas.drawText(text, center, center + distance, paint)
    }
```

这个控件并没有重载 `onTouchEvent` 方法，因为暂时不需要处理用户的触摸点击事件，但是添加了进度动画，使用 `ValueAnimator` 实现。

```kotlin
    fun setProgress(progress: Float) {
        if (animator.isRunning) {
            animator.cancel()
        }
        animator = ValueAnimator.ofFloat(currentValue, progress)
        animator.duration = 1000L
        animator.addUpdateListener {
            currentValue = it.animatedValue as Float
            invalidate()
        }
        animator.start()
    }
```

### 结束

这只是最基础的自定义 View 实现过程。在 Android 开发这条路上还有很长的路要走。[这里是源码](https://github.com/hotsx/hotsx-tools/blob/master/ringview/src/main/java/com/hotsx/ringview/RingView.kt)
