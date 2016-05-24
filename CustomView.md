#自定义view相关知识<br>
#####学会自定义view对于android开发者来说是非常重要的。在很多情况下，android系统提供的自带view可能会不，满足我们的需求的，所以这个时候就需要我们自己来实现一个自定义的view。下面就讲解一下自定义view的相关知识。<br>
##一 继承View的自定义view<br>
###继承View的自定义view需要复写一下方法<br>
* 声明自定义的属性
* View的几个构造方法（主要是在这里获取自定义属性的值，初始化属性值）
* onMeasure()
* onDraw()

####（1）声明自定义属性<br>
在values文件夹下面创建attrs的属性文件。其中自定义属性相关知识请看 [ `自定义属性`](https://github.com/jiangML/MyBlog/blob/master/CustomAttribute.md)。
####（2）获取自定义属性值<br>
在自定义View复写的构造方法中获取属性值。<br>

```java

	TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.CustomTextView, defStyleAttr, 0); 
```

然后在利用a这个TypedArray实例对象获取相关属性值。
**在用完TypedArray以后一定要调用recycle()方法释放资源。**
####（3）onMeasure()方法
在这个方法里面主要是做测量view的width和height工作。<br>

```java

	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
        int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
        int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
        if (widthSpecMode == MeasureSpec.AT_MOST
                && heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(200, 200);
        } else if (widthSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(200, heightSpecSize);
        } else if (heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(widthSpecSize, 200);
        }
    }
```

在这个方法中要获取view尺寸的大小和模式，然后根据模式来分情况设置具体的尺寸。<br>
#####MeasureSpec对应有三中mode
* UNSPECIFIED<br>
   父视图不对子视图有任何约束，它可以达到所期望的任意尺寸。比如 ListView、ScrollView，一般自定义 View 中用不到。
* EXACTLY<br>
  父元素决定自元素的确切大小，子元素将被限定在给定的边界里而忽略它本身大小，对应的属性为match_parent或具体值。
* AT_MOST<br>
  子元素至多达到指定大小的值，即父视图指定一个最大尺寸，对应属性为wrap_content。<br>

这里只是对View尺寸的简单测量，所以还没有考虑更多的情况。 


#### （4）onDraw()方法<br>
在这个方法中主要是利用Canvas来画图像

```java

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        final int paddingLeft = getPaddingLeft();
        final int paddingRight = getPaddingRight();
        final int paddingTop = getPaddingTop();
        final int paddingBottom = getPaddingBottom();
        int width = getWidth() - paddingLeft - paddingRight;
        int height = getHeight() - paddingTop - paddingBottom;
        int radius = Math.min(width, height) / 2;
        canvas.drawCircle(paddingLeft + width / 2, paddingTop + height / 2,
                radius, mPaint);
    }
```


这里面也要考虑到view的paddingd的值。这就是继承View的自定义View的基本步骤。<br>
##二 继承自ViewGroup的自定义view<br>

#####基本步骤如下<br>
* 自定义属性
* 在构造方法中获取属性
* onMeasure()测量view
* onLayout()布局子view
* dispatchDraw()
####(1) 自定义属性<br>
这个和一中的一样就不多讲解了<br>
####(2) 获取自定义属性<br>
这个也和一中的一样<br>
####(3) onMeasure()方法<br>
这个方法中基本和一中的一样为一多的就是要测量子view的大小。<br>

```java

	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    	measureChildren(widthMeasureSpec, heightMeasureSpec);
    	super.onMeasure(widthMeasureSpec, heightMeasureSpec);	
	}

```

就多了一个measureChildren()方法。这个是必须的。<br>
####(4) onLayout()

改方法的原型如下：

```java

    //@param changed 该参数指出当前ViewGroup的尺寸或者位置是否发生了改变
    //@param left top right bottom 当前ViewGroup相对于其父控件的坐标位置
    protected void onLayout(boolean changed,int left, int top, int right, int bottom);
```

可以通过getMeasuredWidth()得到View的宽，getMeasuredHeight()得到View的高。getChildCount()得到子view的个数，然后根据子view的个数，宽高，对子view进行布局。调用子view的layout()方法就可以对子view布局<br>
#####当然在布局的时候不仅仅只是考虑view的宽高，还要考虑到view的margin值的大小，margin值也是要计算进去的。<br>

###例子代码如下：<br>

```java
   
      @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {

    int mViewGroupWidth  = getMeasuredWidth();  //当前ViewGroup的总宽度
    int mViewGroupHeight = getMeasuredHeight(); //当前ViewGroup的总高度

    int mPainterPosX = left; //当前绘图光标横坐标位置
    int mPainterPosY = top;  //当前绘图光标纵坐标位置  
    
    int childCount = getChildCount();	
    for ( int i = 0; i < childCount; i++ ) {
  
    View childView = getChildAt(i);

    int width  = childView.getMeasuredWidth();
    int height = childView.getMeasuredHeight();	     

     CustomViewGroup.LayoutParams margins = (CustomViewGroup.LayoutParams)(childView.getLayoutParams());
  
    //如果剩余的空间不够，则移到下一行开始位置
    if( mPainterPosX + width + margins.leftMargin + margins.rightMargin > mViewGroupWidth ) {	       
        mPainterPosX = left; 
        mPainterPosY += height + margins.topMargin + margins.bottomMargin;
      }		    
  
    //执行ChildView的绘制
    childView.layout(mPainterPosX+margins.leftMargin, mPainterPosY+margins.topMargin,mPainterPosX+margins.leftMargin+width, mPainterPosY+margins.topMargin+height);
  
      mPainterPosX += width + margins.leftMargin + margins.rightMargin;
    }       


```











