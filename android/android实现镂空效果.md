##  Android中实现View的镂空效果

使用

```xml
 <com.FrameLayoutWithHole
        android:layout_width="match_parent"
        app:radius_y="705dp"
        app:hole_radius="228dp"
        android:layout_height="match_parent"/>
```

代码

```kotlin
import android.content.Context
import android.graphics.*
import android.util.AttributeSet
import android.widget.FrameLayout

class FrameLayoutWithHole : FrameLayout {

    private var mEraserBitmap: Bitmap? = null
    private var mEraserCanvas: Canvas? = null
    private var mEraser: Paint? = null
    private var mDensity: Float = 0.toFloat()
    private var mContext: Context? = null

    private var mRadius: Float = 0.toFloat()
    private var mBackgroundColor: Int = -1
    private var mRx: Float = 0.toFloat() // 默认在中心位置
    private var mRy: Float = 0.toFloat()

    private var mType: Int = 0

    private var sizeWidth = 0
    private var sizeHeight = 0

    constructor(context: Context) : super(context) {
        mContext = context
    }

    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs) {
        mContext = context

        val ta = context.obtainStyledAttributes(attrs, R.styleable.FrameLayoutWithHole)
        mBackgroundColor = ta.getColor(R.styleable.FrameLayoutWithHole_background_color, -1)
        mRadius = ta.getDimension(R.styleable.FrameLayoutWithHole_hole_radius, 0f)
        mRx = ta.getDimension(R.styleable.FrameLayoutWithHole_radius_x, 0f)
        mRy = ta.getDimension(R.styleable.FrameLayoutWithHole_radius_y, 0f)
        mType = ta.getInt(R.styleable.FrameLayoutWithHole_hole_type, 0)

        // init(null, 0)
        ta.recycle()
    }

    private fun init(nothing: AttributeSet?, i: Int) {
        setWillNotDraw(false)
        mDensity = mContext!!.resources.displayMetrics.density
        val size = Point()
        size.x = sizeWidth
        size.y = sizeHeight
        mRx = if (mRx !== 0.toFloat()) mRx else size.x / 2.toFloat()
        mRy = if (mRy !== 0.toFloat()) mRy else size.y / 2.toFloat()

        mRadius = if (mRadius !== 0.toFloat()) mRadius else 150.toFloat()
        mBackgroundColor =
            if (mBackgroundColor !== -1) mBackgroundColor else Color.parseColor("#99000000")

        mEraserBitmap = Bitmap.createBitmap(size.x, size.y, Bitmap.Config.ARGB_8888)
        mEraserCanvas = Canvas(mEraserBitmap!!)
        mEraser = Paint()
        mEraser!!.color = Color.RED
        mEraser!!.xfermode = PorterDuffXfermode(PorterDuff.Mode.CLEAR)
        mEraser!!.flags = Paint.ANTI_ALIAS_FLAG
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
         // 背景颜色
        mEraserBitmap!!.eraseColor(Color.parseColor("#78000000"))
        mEraserCanvas!!.drawColor(mBackgroundColor)
        if (mType == 0) {
            mEraserCanvas!!.drawCircle(
                mRx,
                mRy,
                mRadius,
                mEraser!!
            )
        } else {
            val oval = Rect(
                (mRx - mRadius).toInt(),
                (mRy - mRadius).toInt(),
                (mRx + mRadius).toInt(),
                (mRy + mRadius).toInt()
            )
            mEraserCanvas!!.drawBitmap(mEraserBitmap!!, null, oval, mEraser)
        }

        canvas.drawBitmap(mEraserBitmap!!, 0.toFloat(), 0.toFloat(), null)
    }

    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)
        sizeWidth = MeasureSpec.getSize(widthMeasureSpec)
        sizeHeight = MeasureSpec.getSize(heightMeasureSpec)
        init(null, 0)
    }
}
```

xml文件的对应的属性配置

```
  <declare-styleable name="FrameLayoutWithHole">
        <attr name="hole_radius" format="dimension"/>
        <attr name="background_color" format="color"/>
        <attr name="radius_x" format="dimension"/>
        <attr name="radius_y" format="dimension"/>
        <attr name="hole_type">
            <enum name="circle" value="0"/>
            <enum name="rectangle" value="1"/>
        </attr>
    </declare-styleable>
```



