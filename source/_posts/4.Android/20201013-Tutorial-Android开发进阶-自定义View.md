# 自定义View

## 绘制

### 自定义绘制

#### 方式: 重写绘制方法

##### OnDraw()

#### 绘制的关键: Canvas

##### 绘制类方法

###### 颜色

####### drawColor(Color.parse("#66660000"))

####### drawRGB(int r, int g, int b)

####### drawARGB(int a, int r, int g, int b)

###### 点

####### drawPoint(float x, float y, Paint paint) 

####### drawPoints(float[] pts, Paint paint)

####### drawPoints(float[] pts, int offset, int count, Paint paint) 

###### 线

####### drawLine(float startX, float startY, float stopX, float stopY, Paint paint)

由于直线不是封闭图形，所以 setStyle(style) 对直线没有影响。

####### drawLines(float[] pts, Paint paint) 

####### drawLines(float[] pts, int offset, int count, Paint paint) 

###### 形状

####### drawCircle(float centerX, float centerY, float radius, Paint paint) 

####### drawRect(float left, float top, float right, float bottom, Paint paint) 

####### drawOval(float left, float top, float right, float bottom, Paint paint) 画椭圆

####### drawRoundRect(float left, float top, float right, float bottom, float rx, float ry, Paint paint) 画圆角矩形

left, top, right, bottom 是四条边的坐标，rx 和 ry 是圆角的横向半径和纵向半径。

####### drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint) 绘制弧形或扇形

drawArc() 是使用一个椭圆来描述弧形的。left, top, right, bottom 描述的是这个弧形所在的椭圆；startAngle 是弧形的起始角度（x 轴的正向，即正右的方向，是 0 度的位置；顺时针为正角度，逆时针为负角度），sweepAngle 是弧形划过的角度；useCenter 表示是否连接到圆心，如果不连接到圆心，就是弧形，如果连接到圆心，就是扇形。

###### Path

####### drawPath(Path path, Paint paint) 

画心形

``` java
public class PathView extends View {

    Paint paint = new Paint();
    Path path = new Path(); // 初始化 Path 对象
    
    ......
    
    {
      // 使用 path 对图形进行描述（这段描述代码不必看懂）
      path.addArc(200, 200, 400, 400, -225, 225);
      path.arcTo(400, 200, 600, 400, -180, 225, false);
      path.lineTo(400, 542);
    }

    @Override
    protected void onDraw(Canvas canvas) {
      super.onDraw(canvas);
      
      canvas.drawPath(path, paint); // 绘制出 path 描述的图形（心形），大功告成
    }
}
```

####### 描述方法

######## 直接描述路径

######### addXxx() ——添加子图形

########## addCircle(float x, float y, float radius, Direction dir)

########## addOval(float left, float top, float right, float bottom, Direction dir) / addOval(RectF oval, Direction dir) 添加椭圆

########## addRect(float left, float top, float right, float bottom, Direction dir) / addRect(RectF rect, Direction dir) 添加矩形

########## addRoundRect(RectF rect, float rx, float ry, Direction dir) / addRoundRect(float left, float top, float right, float bottom, float rx, float ry, Direction dir) / addRoundRect(RectF rect, float[] radii, Direction dir) / addRoundRect(float left, float top, float right, float bottom, float[] radii, Direction dir) 添加圆角矩形

########## addPath(Path path) 添加另一个 Path

######### xxxTo() ——画线（直线或曲线）

########## moveTo(float x, float y) / rMoveTo(float x, float y) 移动到目标位置

########## lineTo(float x, float y) / rLineTo(float x, float y) 画直线

从当前位置向目标位置画一条直线， x 和 y 是目标位置的坐标。这两个方法的区别是，lineTo(x, y) 的参数是绝对坐标，而 rLineTo(x, y) 的参数是相对当前位置的相对坐标 （前缀 r 指的就是 relatively 「相对地」)。

########## quadTo(float x1, float y1, float x2, float y2) / rQuadTo(float dx1, float dy1, float dx2, float dy2) 画二次贝塞尔曲线

########## cubicTo(float x1, float y1, float x2, float y2, float x3, float y3) / rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3) 画三次贝塞尔曲线

########## arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(RectF oval, float startAngle, float sweepAngle) 画弧形

这个方法和 Canvas.drawArc() 比起来，少了一个参数 useCenter，而多了一个参数 forceMoveTo 。

少了 useCenter ，是因为 arcTo() 只用来画弧形而不画扇形，所以不再需要 useCenter 参数；而多出来的这个 forceMoveTo 参数的意思是，绘制是要「抬一下笔移动过去」，还是「直接拖着笔过去」，区别在于是否留下移动的痕迹。

########## addArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle) / addArc(RectF oval, float startAngle, float sweepAngle)

又是一个弧形的方法。一个叫 arcTo ，一个叫 addArc()，都是弧形，区别在哪里？其实很简单： addArc() 只是一个直接使用了 forceMoveTo = true 的简化版 arcTo() 。

########## close() 封闭当前子图形

close() 和 lineTo(起点坐标) 是完全等价的。

######## 辅助的设置或计算

######### setFillType(FillType fillType)

其中后面的两个带有 INVERSE_ 前缀的，只是前两个的反色版本，所以只要把前两个，即 EVEN_ODD 和 WINDING，搞明白就可以了。

![FillType](https://wx2.sinaimg.cn/large/006tNc79ly1fig820pdt3j30kw0ummzx.jpg)

########## WINDING （默认值）

non-zero winding rule （非零环绕数原则）：首先，它需要你图形中的所有线条都是有绘制方向的。

然后，同样是从平面中的点向任意方向射出一条射线，但计算规则不一样：以 0 为初始值，对于射线和图形的所有交点，遇到每个顺时针的交点（图形从射线的左边向右穿过）把结果加 1，遇到每个逆时针的交点（图形从射线的右边向左穿过）把结果减 1，最终把所有的交点都算上，得到的结果如果不是 0，则认为这个点在图形内部，是要被涂色的区域；如果是 0，则认为这个点在图形外部，是不被涂色的区域。

########## EVEN_ODD

even-odd rule （奇偶原则）：对于平面中的任意一点，向任意方向射出一条射线，这条射线和图形相交的次数（相交才算，相切不算哦）如果是奇数，则这个点被认为在图形内部，是要被涂色的区域；如果是偶数，则这个点被认为在图形外部，是不被涂色的区域。

射线的方向无所谓，同一个点射向任何方向的射线，结果都是一样的。
 
射线每穿过图形中的一条线，内外状态就发生一次切换，这就是为什么 EVEN_ODD 是一个「交叉填充」的模式。

########## INVERSE_WINDING

########## INVERSE_EVEN_ODD

###### 图片

####### drawBitmap(Bitmap bitmap, float left, float top, Paint paint) 画 Bitmap

###### 文字

####### drawText(String text, float x, float y, Paint paint) 绘制文字

##### 关键参数: Paint

###### 颜色

####### 基本颜色

######## paint.setColor(Color.RED)

######## paint.setARGB(int a, int r, int g, int b)

######## paint.setShader(shader)

######### LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode tile)

tile：端点范围之外的着色规则，类型是 TileMode。TileMode 一共有 3 个值可选： CLAMP, MIRROR 和 REPEAT。CLAMP 会在端点之外延续端点处的颜色；MIRROR 是镜像模式；REPEAT 是重复模式。

######### RadialGradient(float centerX, float centerY, float radius, int centerColor, int edgeColor, TileMode tileMode)

######### SweepGradient(float cx, float cy, int color0, int color1)

######### BitmapShader(Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY)

就是用 Bitmap 的像素来作为图形或文字的填充

######### ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)

######## PorterDuff.Mode

PorterDuff.Mode 是用来指定两个图像共同绘制时的颜色策略的。它是一个 enum，不同的 Mode 可以指定不同的策略。「颜色策略」的意思，就是说把源图像绘制到目标图像处时应该怎样确定二者结合后的颜色，而对于 ComposeShader(shaderA, shaderB, mode) 这个具体的方法，就是指应该怎样把 shaderB 绘制在 shaderA 上来得到一个结合后的 Shader。

SRC 是上层 
DST 是下层 
IN  是交集 
OUT 是不想交的

######### Alpha 合成 (Alpha Compositing)

源图像和目标图像：

![源图像和目标图像](https://wx3.sinaimg.cn/large/52eb2279ly1fig6ia1twgj20ds07tdgs.jpg)

Alpha合成:

![Alpha合成](https://wx3.sinaimg.cn/large/52eb2279ly1fig6im3hhcj20o50zt7bj.jpg)

######### 混合 (Blending)

混合，也就是 Photoshop 等制图软件里都有的那些混合模式（multiply darken lighten 之类的）。这一类操作的是颜色本身而不是 Alpha 通道，并不属于 Alpha 合成，所以和 Porter 与 Duff 这两个人也没什么关系，不过为了使用的方便，它们同样也被 Google 加进了 PorterDuff.Mode 里。

![颜色混合](https://wx3.sinaimg.cn/large/52eb2279ly1fig6iw04v0j20ny0hzmzj.jpg)

####### ColorFilter

######## Paint.setColorFilter(ColorFilter filter)

######### LightingColorFilter(int mul, int add)

参数里的 mul 和 add 都是和颜色值格式相同的 int 值，其中 mul 用来和目标像素相乘，add 用来和目标像素相加

一个「保持原样」的「基本 LightingColorFilter 」，mul 为 0xffffff，add 为 0x000000（也就是0）

基于这个「基本 LightingColorFilter 」，你就可以修改一下做出其他的 filter。比如，如果你想去掉原像素中的红色，可以把它的 mul 改为 0x00ffff （红色部分为 0 ）

如果你想让它的绿色更亮一些，就可以把它的 add 改为 0x003000 （绿色部分为 0x30 ）

######### PorterDuffColorFilter(int color, PorterDuff.Mode mode)

这个 PorterDuffColorFilter 的作用是使用一个指定的颜色和一种指定的 PorterDuff.Mode 来与绘制对象进行合成。它的构造方法是 PorterDuffColorFilter(int color, PorterDuff.Mode mode) 其中的 color 参数是指定的颜色， mode 参数是指定的 Mode。同样也是 PorterDuff.Mode ，不过和 ComposeShader 不同的是，PorterDuffColorFilter 作为一个 ColorFilter，只能指定一种颜色作为源，而不是一个 Bitmap。

#########  ColorMatrixColorFilter(colorMatrix)

``` java
// 使用 setColorFilter() 设置一个 ColorMatrixColorFilter
// 用 ColorMatrixColorFilter.setSaturation() 把饱和度去掉
ColorMatrix colorMatrix = new ColorMatrix();
colorMatrix.setSaturation(0);
paint.setColorFilter(new ColorMatrixColorFilter(colorMatrix));
````

####### Xfermode

Paint 最后一层处理颜色的方法是 setXfermode(Xfermode xfermode) ，它处理的是「当颜色遇上 View」的问题。

"Xfermode" 其实就是 "Transfer mode"，用 "X" 来代替 "Trans" 是一些美国人喜欢用的简写方式。严谨地讲， Xfermode 指的是你要绘制的内容和 Canvas 的目标位置的内容应该怎样结合计算出最终的颜色。但通俗地说，其实就是要你以绘制的内容作为源图像，以 View 中已有的内容作为目标图像，选取一个 PorterDuff.Mode 作为绘制内容的颜色处理方案。

``` Java
Xfermode xfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN);

...

canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方 DST
paint.setXfermode(xfermode); // 设置 Xfermode
canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆 SRC
paint.setXfermode(null); // 用完及时清除 Xfermode
```

######## 使用离屏缓冲（Off-screen Buffer）

要想使用 setXfermode() 正常绘制，必须使用离屏缓存 (Off-screen Buffer) 把内容绘制在额外的层上，再把绘制好的内容贴回 View 中。

######### Canvas.saveLayer()

``` java
  int saved = canvas.saveLayer(null, null, Canvas.ALL_SAVE_FLAG);

  canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方
  paint.setXfermode(xfermode); // 设置 Xfermode
  canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆
  paint.setXfermode(null); // 用完及时清除 Xfermode

  canvas.restoreToCount(saved);
```

######### View.setLayerType()

View.setLayerType() 是直接把整个 View 都绘制在离屏缓冲中。 setLayerType(LAYER_TYPE_HARDWARE) 是使用 GPU 来缓冲， setLayerType(LAYER_TYPE_SOFTWARE) 是直接直接用一个 Bitmap 来缓冲。

######## 控制好透明区域

使用 Xfermode 来绘制的内容，除了注意使用离屏缓冲，还应该注意控制它的透明区域不要太小，要让它足够覆盖到要和它结合绘制的内容，否则得到的结果很可能不是你想要的。

透明区域过小而覆盖不到的地方，将不会受到 Xfermode 的影响。

###### 效果

####### 抗锯齿

######## Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);

####### paint.setStyle(Paint.Style.STROKE) 

######## FILL 是填充模式

######## STROKE 是画线模式（即勾边模式）

######## FILL_AND_STROKE 是两种模式一并使用：既画线又填充

####### 线条形状

######## setStrokeWidth(float width)

线条宽度 0 和 1 的区别

默认情况下，线条宽度为 0，但你会发现，这个时候它依然能够画出线，线条的宽度为 1 像素。那么它和线条宽度为 1 有什么区别呢？

其实这个和后面要讲的一个「几何变换」有关：你可以为 Canvas 设置 Matrix 来实现几何变换（如放大、缩小、平移、旋转），在几何变换之后 Canvas 绘制的内容就会发生相应变化，包括线条也会加粗，例如 2 像素宽度的线条在 Canvas 放大 2 倍后会被以 4 像素宽度来绘制。而当线条宽度被设置为 0 时，它的宽度就被固定为 1 像素，就算 Canvas 通过几何变换被放大，它也依然会被以 1 像素宽度来绘制。

######## paint.setStrokeCap(cap)

![Cap](https://wx4.sinaimg.cn/large/006tNc79ly1fig74qv8rij30ct05rglp.jpg)

######### Paint.Cap.ROUND

######### Paint.Cap.BUTT

######### Paint.Cap.SQUARE

######## setStrokeJoin(Paint.Join join)

![Join](https://wx1.sinaimg.cn/large/006tNc79ly1fig75e27w6j30cp05ewem.jpg)

######## setStrokeMiter(float miter)

这个方法虽然名叫 setStrokeMiter(miter) ，但它其实设置的是「 线条在 Join 类型为 MITER 时对于 MITER 的长度限制」。它的这个名字虽然短，但却存在一定的迷惑性，如果叫 setStrokeJoinMiterLimit(limit) 就更准确了。

![长度限制](https://wx3.sinaimg.cn/large/006tNc79ly1fig7btolhij30e706dglp.jpg)

####### 色彩优化

######## setDither(boolean dither)

在实际的应用场景中，抖动更多的作用是在图像降低色彩深度绘制时，避免出现大片的色带与色块。

![抖动](https://wx4.sinaimg.cn/large/006tNc79ly1fig7d34s0jj30lf07t75x.jpg)

######## setFilterBitmap(boolean filter)

图像在放大绘制的时候，默认使用的是最近邻插值过滤，这种算法简单，但会出现马赛克现象；而如果开启了双线性过滤，就可以让结果图像显得更加平滑。

![双线性过滤](https://wx2.sinaimg.cn/large/006tNc79ly1fig7dbga6ij30jb0a00tr.jpg)

####### setPathEffect(PathEffect effect)

######## 单一效果

######### CornerPathEffect

![](https://wx1.sinaimg.cn/large/006tNc79ly1fig7dobrizj30iv0agt8z.jpg)

它的构造方法 CornerPathEffect(float radius) 的参数 radius 是圆角的半径。

######### DiscretePathEffect

把线条进行随机的偏离，让轮廓变得乱七八糟。乱七八糟的方式和程度由参数决定。

DiscretePathEffect 具体的做法是，把绘制改为使用定长的线段来拼接，并且在拼接的时候对路径进行随机偏离。它的构造方法 DiscretePathEffect(float segmentLength, float deviation) 的两个参数中， segmentLength 是用来拼接的每个线段的长度， deviation 是偏离量。

######### DashPathEffect

它的构造方法 DashPathEffect(float[] intervals, float phase) 中， 第一个参数 intervals 是一个数组，它指定了虚线的格式：数组中元素必须为偶数（最少是 2 个），按照「画线长度、空白长度、画线长度、空白长度」……的顺序排列，例如上面代码中的 20, 5, 10, 5 就表示虚线是按照「画 20 像素、空 5 像素、画 10 像素、空 5 像素」的模式来绘制；第二个参数 phase 是虚线的偏移量。

######### PathDashPathEffect

它的构造方法 PathDashPathEffect(Path shape, float advance, float phase, PathDashPathEffect.Style style) 中， shape 参数是用来绘制的 Path ； advance 是两个相邻的 shape 段之间的间隔，不过注意，这个间隔是两个 shape 段的起点的间隔，而不是前一个的终点和后一个的起点的距离； phase 和 DashPathEffect 中一样，是虚线的偏移；最后一个参数 style，是用来指定拐弯改变的时候 shape 的转换方式。style 的类型为 PathDashPathEffect.Style ，是一个 enum ，具体有三个值：TRANSLATE：位移
ROTATE：旋转
MORPH：变体

![dash path](https://wx1.sinaimg.cn/large/006tNc79ly1fig7efqw9qj30kn0h3dh5.jpg)

######## 组合效果

######### SumPathEffect

这是一个组合效果类的 PathEffect 。它的行为特别简单，就是分别按照两种 PathEffect 分别对目标进行绘制。

``` java
PathEffect dashEffect = new DashPathEffect(new float[]{20, 10}, 0);
PathEffect discreteEffect = new DiscretePathEffect(20, 5); 
pathEffect = new SumPathEffect(dashEffect, discreteEffect);

...

canvas.drawPath(path, paint);
```

![1](https://wx1.sinaimg.cn/large/006tNc79ly1fig7ekjh7lj30dw05jq2z.jpg)

######### ComposePathEffect

这也是一个组合效果类的 PathEffect 。不过它是先对目标 Path 使用一个 PathEffect，然后再对这个改变后的 Path 使用另一个 PathEffect。

``` java
PathEffect dashEffect = new DashPathEffect(new float[]{20, 10}, 0);
PathEffect discreteEffect = new DiscretePathEffect(20, 5); 
pathEffect = new ComposePathEffect(dashEffect, discreteEffect);

...

canvas.drawPath(path, paint);
```

![2](https://wx3.sinaimg.cn/large/006tNc79ly1fig7epf94aj30dr05eq2x.jpg)

####### setShadowLayer()

方法的参数里， radius 是阴影的模糊范围； dx dy 是阴影的偏移量； shadowColor 是阴影的颜色。

如果要清除阴影层，使用 clearShadowLayer() 。

######## paint.setShadowLayer(10, 0, 0, Color.RED);

####### setMaskFilter()

######## BlurMaskFilter

它的构造方法 BlurMaskFilter(float radius, BlurMaskFilter.Blur style) 中， radius 参数是模糊的范围， style 是模糊的类型。一共有四种：

NORMAL: 内外都模糊绘制
SOLID: 内部正常绘制，外部模糊
INNER: 内部模糊，外部不绘制
OUTER: 内部不绘制，外部模糊

######### paint.setMaskFilter(new BlurMaskFilter(50, BlurMaskFilter.Blur.NORMAL));

######## EmbossMaskFilter

浮雕效果的 MaskFilter。

它的构造方法 EmbossMaskFilter(float[] direction, float ambient, float specular, float blurRadius) 的参数里， direction 是一个 3 个元素的数组，指定了光源的方向； ambient 是环境光的强度，数值范围是 0 到 1； specular 是炫光的系数； blurRadius 是应用光线的范围。

######### paint.setMaskFilter(new EmbossMaskFilter(new float[]{0, 1, 1}, 0.2f, 8, 10));

####### 获取绘制的 Path

######## getFillPath(Path src, Path dst)

![path](https://wx3.sinaimg.cn/large/006tNc79ly1fig7ggbut0j30rw0me76k.jpg)

######## getTextPath(String text, int start, int end, float x, float y, Path path) / getTextPath(char[] text, int index, int count, float x, float y, Path path)

###### drawText() 相关

####### Paint.setTextSize(float textSize)

###### 初始化类

####### reset()

####### set(Paint src)

####### setFlags(int flags)

######## paint.setFlags(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);

##### 辅助类方法

###### 范围裁切

####### clipXXX()

###### 几何变换

####### Matrix

#### 使用不同的绘制方法来控制遮挡关系

## 布局

## 触摸反馈
