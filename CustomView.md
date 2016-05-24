#自定义view相关知识<br>
#####学会自定义view对于android开发者来说是非常重要的。在很多情况下，android系统提供的自带view可能会不，满足我们的需求的，所以这个时候就需要我们自己来实现一个自定义的view。下面就讲解一下自定义view的相关知识。<br>
##一 继承View的自定义view<br>
###继承View的自定义view需要复写一下方法<br>
* 声明自定义的属性
* View的几个构造方法（主要是在这里获取自定义属性的值，初始化属性值）
* onMeasure()
* onDraw()

####（1）声明自定义属性<br>
在values文件夹下面创建attrs的属性文件。其中自定义属性相关知识请看 [ `自定义属性`](http://baidu.com)。
####（2）获取自定义属性值<br>
在自定义View复写的构造方法中获取属性值。<br>

``java

	TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.CustomTextView, defStyleAttr, 0); 
``

然后在利用a这个TypedArray实例对象获取相关属性值。
**在用完TypedArray以后一定要调用recycle()方法释放资源。**
####（3）onMeasure()方法
在这个方法里面主要是做测量view的width和height工作。<br>

```java

    @Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
          super.onMeasure(widthMeasureSpec, heightMeasureSpec);
          int widthMode = MeasureSpec.getMode(widthMeasureSpec);
          int widthSize = MeasureSpec.getSize(widthMeasureSpec);
          int heightMode = MeasureSpec.getMode(heightMeasureSpec);
          int heightSize = MeasureSpec.getSize(heightMeasureSpec);
          int width;
          int height;
           if (widthMode == MeasureSpec.EXACTLY) {
                 width = widthSize;
          } else {
                mPaint.setTextSize(textSize);
                mPaint.getTextBounds(text, 0, text.length(), mRect);
                float textWidth = mRect.width();
                int desired = (int) (getPaddingLeft() + textWidth + getPaddingRight());
                width = desired;
          }
          if (heightMode == MeasureSpec.EXACTLY) {
              height = heightSize;
          } else {
              mPaint.setTextSize(textSize);
              mPaint.getTextBounds(text, 0, text.length(), mRect);
              float textHeight = mRect.height();
               int desired = (int) (getPaddingTop() + textHeight + getPaddingBottom());
              height = desired;
           }
          setMeasuredDimension(width, height);
    }

```