---
layout: post
title: android 自定义范围选取控件CustomRangeSeekBar
categories: 自定义view
description: 
keywords: android自定义view, android自定义键盘
---

最近在做一款音乐剪切小工具， 需要用到一个范围选取的控件。没有合适的系统控件只能自定义一个。

# 效果图：
![test.gif](http://upload-images.jianshu.io/upload_images/2229793-54d58fa733068726.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 源码：
https://github.com/zyl409214686/CustomRangeSeekBar

# 实现思路

1. 构造方法中初始化paint以及自定义属性等其他参数。
2. 重写`onMeasure`方法确定view大小。
3. 重写`onSizeChanged` 方法初始化进度条rectF类对象。
4. 重写`onDraw`方法绘画出进度以及滑块和进度文字并将需要改变的参数改为变量。 
5. 重写`onTouchEvent`处理检测点击滑块，以及相应的滑块的滑动和触发重绘等。

# 实现过程

1)初始化自定义属性以及需要的各个参数。
```
 public CustomRangeSeekBar(Context context, AttributeSet attrs) {
        super(context, attrs);
        TypedArray a = getContext().obtainStyledAttributes(attrs, R.styleable.CustomRangeSeekBar, 0, 0);
        this.mAbsoluteMinValue = new Float(a.getFloat(R.styleable.CustomRangeSeekBar_absoluteMin, (float) 0.0));
        this.mAbsoluteMaxValue = new Float(a.getFloat(R.styleable.CustomRangeSeekBar_absolutemMax, (float) 100.0));
        mThumbImage = BitmapFactory.decodeResource(getResources(), a.getResourceId(R.styleable.CustomRangeSeekBar_thumbImage, R.mipmap.btn_seekbar_normal));
        mProgressBarBg = BitmapFactory.decodeResource(getResources(), a.getResourceId(R.styleable.CustomRangeSeekBar_progressBarBg, R.mipmap.seekbar_bg));
        mProgressBarSelBg = BitmapFactory.decodeResource(getResources(), a.getResourceId(R.styleable.CustomRangeSeekBar_progressBarSelBg, R.mipmap.seekbar_sel_bg));
        mBetweenAbsoluteValue = a.getFloat(R.styleable.CustomRangeSeekBar_betweenAbsoluteValue, 0);
        mProgressTextFormat = a.getInt(R.styleable.CustomRangeSeekBar_progressTextFormat, HINT_FORMAT_NUMBER);
        mWordSize = a.getDimension(R.styleable.CustomRangeSeekBar_progressTextSize, 16);
        mPaint.setTextSize(mWordSize);
        mThumbWidth = mThumbImage.getWidth();
        mThumbHalfWidth = 0.5f * mThumbWidth;
        mThumbHalfHeight = 0.5f * mThumbImage.getHeight();
        mProgressBarHeight = 0.3f * mThumbHalfHeight;
        mWidthPadding = mThumbHalfHeight;
        Paint.FontMetrics metrics = mPaint.getFontMetrics();
        mWordHeight = (int) (metrics.descent - metrics.ascent);
        setPercentMinValue(0.0);
        setPercentMaxValue(100.0);
        a.recycle();
    }
```

2)重写`onSizeChanged（） `方法中初始化进度条以及进度条被选中状态的矩形区域。
```
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        mProgressBarRect = new RectF(mWidthPadding, mWordHeight + 0.5f * (h - mWordHeight - mProgressBarHeight),
                w - mWidthPadding, mWordHeight + 0.5f*(h - mWordHeight + mProgressBarHeight));
        mProgressBarSelRect = new RectF(mProgressBarRect);
    }
```

3)重写`onMeasure`方法，设置view宽高。
    View高度 = 滑块高度+进度文字高度。

@Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int width = MIN_WIDTH;
        if (MeasureSpec.UNSPECIFIED != MeasureSpec.getMode(widthMeasureSpec)) {
            width = MeasureSpec.getSize(widthMeasureSpec);
        }
        int height = mThumbImage.getHeight() + mWordHeight;
        if (MeasureSpec.UNSPECIFIED != MeasureSpec.getMode(heightMeasureSpec)) {
            height = Math.min(height, MeasureSpec.getSize(heightMeasureSpec));
        }
        setMeasuredDimension(width, height);
    }

4)重写`onDraw()` 绘制进度、被选中范围进度、两个滑块以及两个滑块对应的进度文字。
```
 @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        // draw seek bar background line
        mPaint.setStyle(Style.FILL);
        canvas.drawBitmap(mProgressBarBg, null, mProgressBarRect, mPaint);
        // draw seek bar active range line
        mProgressBarSelRect.left = percentToScreen(mPercentMinValue);
        mProgressBarSelRect.right = percentToScreen(mPercentMaxValue);
        //canvas.drawBitmap(mProgressBarSelBg, mWidthPadding, 0.5f * (getHeight() - mProgressBarHeight), mPaint);
        canvas.drawBitmap(mProgressBarSelBg, null, mProgressBarSelRect, mPaint);
        // draw minimum thumb
        drawThumb(percentToScreen(mPercentMinValue), Thumb.MIN.equals(mPressedThumb), canvas);
        // draw maximum thumb
        drawThumb(percentToScreen(mPercentMaxValue), Thumb.MAX.equals(mPressedThumb), canvas);
        mPaint.setColor(Color.rgb(255, 165, 0));
//        mPaint.setTextSize(DensityUtils.dp2px(getContext(), 16));
        drawThumbMinText(percentToScreen(mPercentMinValue), getSelectedAbsoluteMinValue(), canvas);
        drawThumbMaxText(percentToScreen(mPercentMaxValue), getSelectedAbsoluteMaxValue(), canvas);
    }
```
5)重写`onTouchEvent ()`   
```
@Override
    public boolean onTouchEvent(MotionEvent event) {
        if (!mIsEnable)
            return true;
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                mPressedThumb = evalPressedThumb(event.getX());
                if (Thumb.MIN.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onClickMinThumb(getSelectedAbsoluteMaxValue(), getSelectedAbsoluteMinValue());
                }
                if (Thumb.MAX.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onClickMaxThumb();
                }
                invalidate();
                //Intercept parent TouchEvent
                if (getParent() != null) {
                    getParent().requestDisallowInterceptTouchEvent(true);
                }
                break;
            case MotionEvent.ACTION_MOVE:
                if (mPressedThumb != null) {
                    float eventX = event.getX();
                    float maxValue = percentToAbsoluteValue(mPercentMaxValue);
                    float minValue = percentToAbsoluteValue(mPercentMinValue);
                    float eventValue = percentToAbsoluteValue(screenToPercent(eventX));
                    if (Thumb.MIN.equals(mPressedThumb)) {
                        minValue = eventValue;
                        if (mBetweenAbsoluteValue>0 && maxValue - minValue <= mBetweenAbsoluteValue)
                            minValue = new Float((maxValue - mBetweenAbsoluteValue));
                        setPercentMinValue(absoluteValueToPercent(minValue));//Notice01
                        if (mThumbListener != null)
                            mThumbListener.onMinMove(getSelectedAbsoluteMaxValue(), getSelectedAbsoluteMinValue()); 
                    } else if (Thumb.MAX.equals(mPressedThumb)) {
                        maxValue = eventValue;
                        if (mBetweenAbsoluteValue>0 && maxValue - minValue <= mBetweenAbsoluteValue)
                            maxValue = new Float(minValue + mBetweenAbsoluteValue);
setPercentMaxValue(absoluteValueToPercent(maxValue));
                        if (mThumbListener != null)
                            mThumbListener.onMaxMove(getSelectedAbsoluteMaxValue(), getSelectedAbsoluteMinValue());
                    }
                }
                //Intercept parent TouchEvent
                if (getParent() != null) {
                    getParent().requestDisallowInterceptTouchEvent(true);
                }
                break;
            case MotionEvent.ACTION_UP:
                if (Thumb.MIN.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onUpMinThumb(getSelectedAbsoluteMaxValue(), getSelectedAbsoluteMinValue());
                }
                if (Thumb.MAX.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onUpMaxThumb();
                }
                //Intercept parent TouchEvent
                if (getParent() != null) {
                    getParent().requestDisallowInterceptTouchEvent(true);
                }
                break;
            case MotionEvent.ACTION_CANCEL:
                if (Thumb.MIN.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onUpMinThumb(getSelectedAbsoluteMaxValue(), getSelectedAbsoluteMinValue());
                }
                if (Thumb.MAX.equals(mPressedThumb)) {
                    if (mThumbListener != null)
                        mThumbListener.onUpMaxThumb();
                }
                mPressedThumb = null;
                //Intercept parent TouchEvent
                if (getParent() != null) {
                    getParent().requestDisallowInterceptTouchEvent(true);
                }
                break;
        }
        return true;
    }
```
`evalPressedThumb()`方法为检测事件源是否为滑块（左or右or不在滑块上）。其他还要一些转换的方法`percentToAbsoluteValue `为进度条比例值转换为具体值、
`percentToAbsoluteValue `为进度条的比例值转换到具体值、`screenToPercent`为view中具体像素转换为进度中的进度条的比例值。
滑动事件处理逻辑，当检测到滑动的为min滑块（左测滑块）时，看注释**Notice01** 代码                   `setPercentMinValue(absoluteValueToPercent(minValue));` 将绝对值转换为进度条比例，然后设置进去，看此方法。
```
    public void setPercentMinValue(double value) {
        mPercentMinValue = Math.max(0d, Math.min(1d, Math.min(value, mPercentMaxValue)));
        invalidate();
    }
```
将比例值设置进去并调用了重绘，其他逻辑都大同小异，具体可以看上面留的github源码。
# 注意事项
事件处理的结尾需要增加对父容器view拦截的阻止。`getParent().requestDisallowInterceptTouchEvent(true); `不然滑动过程事件被父容器拦截而出现滑动不顺畅的情况。
 `