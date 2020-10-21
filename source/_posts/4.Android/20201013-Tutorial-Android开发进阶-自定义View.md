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

######### ColorMatrixColorFilter(colorMatrix)

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

####### Canvas绘制文字的方式

######## drawText(String text, float x, float y, Paint paint)

方法的参数很简单： text 是文字内容，x 和 y 是文字的坐标。但需要注意：这个坐标并不是文字的左上角，而是一个与左下角比较接近的位置。大概在这里：

![](http://wx3.sinaimg.cn/large/52eb2279ly1fig60bobb0j20ek04dwex.jpg)

######## drawTextRun(CharSequence text, int start, int end, int contextStart, int contextEnd, float x, float y, boolean isRtl, Paint paint)

text：要绘制的文字
start：从那个字开始绘制
end：绘制到哪个字结束
contextStart：上下文的起始位置。contextStart 需要小于等于 start
contextEnd：上下文的结束位置。contextEnd 需要大于等于 end
x：文字左边的坐标
y：文字的基线坐标
isRtl：是否是 RTL（Right-To-Left，从右向左）

######## drawTextOnPath(String text, Path path, float hOffset, float vOffset, Paint paint)

参数里，需要解释的只有两个： hOffset 和 vOffset。它们是文字相对于 Path 的水平偏移量和竖直偏移量，利用它们可以调整文字的位置。例如你设置 hOffset 为 5， vOffset 为 10，文字就会右移 5 像素和下移 10 像素。

记住一条原则： drawTextOnPath() 使用的 Path ，拐弯处全用圆角，别用尖角。

######## StaticLayout(CharSequence source, TextPaint paint, int width, Layout.Alignment align, float spacingmult, float spacingadd, boolean includepad)

width 是文字区域的宽度，文字到达这个宽度后就会自动换行；
align 是文字的对齐方向；
spacingmult 是行间距的倍数，通常情况下填 1 就好；
spacingadd 是行间距的额外增加值，通常情况下填 0 就好；
includepad 是指是否在文字上下添加额外的空间，来避免某些过高的字符的绘制出现越界。

####### Paint对文字绘制的辅助

######## 显示效果类

######### setTextSize(float textSize)

######### setTypeface(Typeface typeface)

```java
paint.setTypeface(Typeface.DEFAULT);
canvas.drawText(text, 100, 150, paint);
paint.setTypeface(Typeface.SERIF);
canvas.drawText(text, 100, 300, paint);
paint.setTypeface(Typeface.createFromAsset(getContext().getAssets(), "Satisfy-Regular.ttf"));
canvas.drawText(text, 100, 450, paint);
```

######### setFakeBoldText(boolean fakeBoldText)

是否使用伪粗体。

之所以叫伪粗体（ fake bold ），因为它并不是通过选用更高 weight 的字体让文字变粗，而是通过程序在运行时把文字给「描粗」了。

######### setStrikeThruText(boolean strikeThruText)

是否加删除线。

######### setUnderlineText(boolean underlineText)

######### setTextSkewX(float skewX)

######### setTextScaleX(float scaleX)

######### setLetterSpacing(float letterSpacing)

设置字符间距。默认值是 0。

######### setFontFeatureSettings(String settings)

用 CSS 的 font-feature-settings 的方式来设置文字。

```java
paint.setFontFeatureSettings("smcp"); // 设置 "small caps"
canvas.drawText("Hello HenCoder", 100, 150, paint);
```

######### setTextAlign(Paint.Align align)

######### setTextLocale(Locale locale) / setTextLocales(LocaleList locales)

Locale 直译是「地域」，其实就是你在系统里设置的「语言」或「语言区域」（具体名称取决于你用的是什么手机），比如「简体中文（中国）」「English (US)」「English (UK)」。有些同源的语言，在文化发展过程中对一些相同的字衍生出了不同的写法（比如中国大陆和日本对于某些汉字的写法就有细微差别。注意，不是繁体和简体这种同音同义不同字，而真的是同样的一个字有两种写法）。系统语言不同，同样的一个字的显示就有可能不同。你可以试一下把自己手机的语言改成日文，然后打开微信看看聊天记录，你会明显发现文字的显示发生了很多细微的变化，这就是由于系统的 Locale 改变所导致的。

Canvas 绘制的时候，默认使用的是系统设置里的 Locale。而通过 Paint.setTextLocale(Locale locale) 就可以在不改变系统设置的情况下，直接修改绘制时的 Locale。

######### setHinting(int mode)

现在的 Android 设备大多数都是是用的矢量字体。矢量字体的原理是对每个字体给出一个字形的矢量描述，然后使用这一个矢量来对所有的尺寸的字体来生成对应的字形。由于不必为所有字号都设计它们的字体形状，所以在字号较大的时候，矢量字体也能够保持字体的圆润，这是矢量字体的优势。不过当文字的尺寸过小（比如高度小于 16 像素），有些文字会由于失去过多细节而变得不太好看。 hinting 技术就是为了解决这种问题的：通过向字体中加入 hinting 信息，让矢量字体在尺寸过小的时候得到针对性的修正，从而提高显示效果。
![hinting](http://wx3.sinaimg.cn/large/52eb2279ly1fig65wwv1yj20ki0bywje.jpg)
功能很强，效果很赞。不过在现在（ 2017 年），手机屏幕的像素密度已经非常高，几乎不会再出现字体尺寸小到需要靠 hinting 来修正的情况，所以这个方法其实……没啥用了。可以忽略。

######### setElegantTextHeight(boolean elegant)

把「大高个」文字的高度恢复为原始高度；
增大每行文字的上下边界，来容纳被加高了的文字。

不过就像前面说的，由于中国人常用的汉语和英语的文字并不会达到这种高度，所以这个方法对于中国人基本上是没用的。

######### setSubpixelText(boolean subpixelText)

是否开启次像素级的抗锯齿（ sub-pixel anti-aliasing ）。

次像素级抗锯齿这个功能解释起来很麻烦，简单说就是根据程序所运行的设备的屏幕类型，来进行针对性的次像素级的抗锯齿计算，从而达到更好的抗锯齿效果。更详细的解释可以看这篇文章。

不过，和前面讲的字体 hinting 一样，由于现在手机屏幕像素密度已经很高，所以默认抗锯齿效果就已经足够好了，一般没必要开启次像素级抗锯齿，所以这个方法基本上没有必要使用。

######### setLinearText(boolean linearText)

######## 测量文字尺寸类

######### float getFontSpacing()

获取推荐的行距。

即推荐的两行文字的 baseline 的距离。这个值是系统根据文字的字体和字号自动计算的。它的作用是当你要手动绘制多行文字（而不是使用 StaticLayout）的时候，可以在换行的时候给 y 坐标加上这个值来下移文字。

######### FontMetircs getFontMetrics()

FontMetrics 是个相对专业的工具类，它提供了几个文字排印方面的数值：ascent, descent, top, bottom, leading。

![FontMetrics](http://wx3.sinaimg.cn/large/52eb2279ly1fig66iud3gj20ik0bn41l.jpg)

- baseline: 上图中黑色的线。前面已经讲过了，它的作用是作为文字显示的基准线。

- ascent / descent: 上图中**绿色和橙色**的线，它们的作用是限制普通字符的顶部和底部范围。
普通的字符，上不会高过 ascent ，下不会低过 descent ，例如上图中大部分的字形都显示在 ascent 和 descent 两条线的范围内。具体到 Android 的绘制中， ascent 的值是图中绿线和 baseline 的相对位移，它的值为负（因为它在 baseline 的上方）； descent 的值是图中橙线和 baseline 相对位移，值为正（因为它在 baseline 的下方）。

- top / bottom: 上图中**蓝色和红色**的线，它们的作用是限制所有字形（ glyph ）的顶部和底部范围。
除了普通字符，有些字形的显示范围是会超过 ascent 和 descent 的，而 top 和 bottom 则限制的是所有字形的显示范围，包括这些特殊字形。例如上图的第二行文字里，就有两个泰文的字形分别超过了 ascent 和 descent 的限制，但它们都在 top 和 bottom 两条线的范围内。具体到 Android 的绘制中， top 的值是图中蓝线和 baseline 的相对位移，它的值为负（因为它在 baseline 的上方）； bottom 的值是图中红线和 baseline 相对位移，值为正（因为它在 baseline 的下方）。

- leading: 这个词在上图中没有标记出来，因为它并不是指的某条线和 baseline 的相对位移。 leading 指的是行的额外间距，即对于上下相邻的两行，上行的 bottom 线和下行的 top 线的距离，也就是上图中**第一行的红线和第二行的蓝线**的距离（对，就是那个小细缝）。

######### getTextBounds(String text, int start, int end, Rect bounds)

参数里，text 是要测量的文字，start 和 end 分别是文字的起始和结束位置，bounds 是存储文字显示范围的对象，方法在测算完成之后会把结果写进 bounds。

```java
paint.setStyle(Paint.Style.FILL);
canvas.drawText(text, offsetX, offsetY, paint);

paint.getTextBounds(text, 0, text.length(), bounds);
bounds.left += offsetX;
bounds.top += offsetY;
bounds.right += offsetX;
bounds.bottom += offsetY;
paint.setStyle(Paint.Style.STROKE);
canvas.drawRect(bounds, paint);
```

![Bounds](http://wx3.sinaimg.cn/large/52eb2279ly1fig66pdyg4j20ct02tmxf.jpg)

######### float measureText(String text)

测量文字的宽度并返回。

![Measure](http://wx3.sinaimg.cn/large/52eb2279ly1fig671on56j20or04a0te.jpg)

如果你用代码分别使用 getTextBounds() 和 measureText() 来测量文字的宽度，你会发现 measureText() 测出来的宽度总是比 getTextBounds() 大一点点。这是因为这两个方法其实测量的是两个不一样的东西。

- getTextBounds: 它测量的是文字的显示范围（关键词：显示）。形象点来说，你这段文字外放置一个可变的矩形，然后把矩形尽可能地缩小，一直小到这个矩形恰好紧紧包裹住文字，那么这个矩形的范围，就是这段文字的 bounds。

-measureText(): 它测量的是文字绘制时所占用的宽度（关键词：占用）。前面已经讲过，一个文字在界面中，往往需要占用比他的实际显示宽度更多一点的宽度，以此来让文字和文字之间保留一些间距，不会显得过于拥挤。上面的这幅图，我并没有设置 setLetterSpacing() ，这里的 letter spacing 是默认值 0，但你可以看到，图中每两个字母之间都是有空隙的。另外，下方那条用于表示文字宽度的横线，在左边超出了第一个字母 H 一段距离的，在右边也超出了最后一个字母 r（虽然右边这里用肉眼不太容易分辨），而就是两边的这两个「超出」，导致了 measureText() 比 getTextBounds() 测量出的宽度要大一些。

######### getTextWidths(String text, float[] widths)

######### int breakText(String text, boolean measureForwards, float maxWidth, float[] measuredWidth)

这个方法也是用来测量文字宽度的。但和 measureText() 的区别是， breakText() 是在给出宽度上限的前提下测量文字的宽度。如果文字的宽度超出了上限，那么在临近超限的位置截断文字。

![breakText](http://wx3.sinaimg.cn/large/52eb2279ly1fig67950cnj21080m4grf.jpg)

breakText() 的返回值是截取的文字个数（如果宽度没有超限，则是文字的总个数）。参数中， text 是要测量的文字；measureForwards 表示文字的测量方向，true 表示由左往右测量；maxWidth 是给出的宽度上限；measuredWidth 是用于接受数据，而不是用于提供数据的：方法测量完成后会把截取的文字宽度（如果宽度没有超限，则为文字总宽度）赋值给 measuredWidth[0]。

这个方法可以用于多行文字的折行计算。

######## 光标相关

######### getRunAdvance(CharSequence text, int start, int end, int contextStart, int contextEnd, boolean isRtl, int offset)

对于一段文字，计算出某个字符处光标的 x 坐标。 start end 是文字的起始和结束坐标；contextStart contextEnd 是上下文的起始和结束坐标；isRtl 是文字的方向；offset 是字数的偏移，即计算第几个字符处的光标。

```java
int length = text.length();
float advance = paint.getRunAdvance(text, 0, length, 0, length, false, length);
canvas.drawText(text, offsetX, offsetY, paint);
canvas.drawLine(offsetX + advance, offsetY - 50, offsetX + advance, offsetY + 10, paint);
```

![RunAdvance](http://wx3.sinaimg.cn/large/52eb2279ly1fig67hkga6j20cx0373ys.jpg)

其实，说是测量光标位置的，本质上这也是一个测量文字宽度的方法。上面这个例子中，start 和 contextStart 都是 0， end contextEnd 和 offset 都等于 text.length()。在这种情况下，它是等价于 measureText(text) 的，即完整测量一段文字的宽度。而对于更复杂的需求，getRunAdvance() 能做的事就比 measureText() 多了。

######### getOffsetForAdvance(CharSequence text, int start, int end, int contextStart, int contextEnd, boolean isRtl, float advance)

给出一个位置的像素值，计算出文字中最接近这个位置的字符偏移量（即第几个字符最接近这个坐标）。

方法的参数很简单： text 是要测量的文字；start end 是文字的起始和结束坐标；contextStart contextEnd 是上下文的起始和结束坐标；isRtl 是文字方向；advance 是给出的位置的像素值。填入参数，对应的字符偏移量将作为返回值返回。

getOffsetForAdvance() 配合上 getRunAdvance() 一起使用，就可以实现「获取用户点击处的文字坐标」的需求。

######### hasGlyph(String string)

![alone](http://wx1.sinaimg.cn/large/006tNc79ly1flgaf31rskj31120damyn.jpg)

###### 初始化类

####### reset()

####### set(Paint src)

####### setFlags(int flags)

######## paint.setFlags(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);

##### 辅助类方法

###### 范围裁切

####### canvas.clipRect(left, top, right, bottom);

记得要加上 Canvas.save() 和 Canvas.restore() 来及时恢复绘制范围，所以完整代码是这样的:

```java
canvas.save();
canvas.clipRect(left, top, right, bottom);
canvas.drawBitmap(bitmap, x, y, paint);
canvas.restore();
```

####### canvas.clipPath(path1);

###### 几何变换

####### 使用 Canvas 来做常见的二维变换

######## Canvas.translate(float dx, float dy) 平移

######## Canvas.rotate(float degrees, float px, float py) 旋转

参数里的 degrees 是旋转角度，单位是度（也就是一周有 360° 的那个单位），方向是顺时针为正向； px 和 py 是轴心的位置。

######## Canvas.scale(float sx, float sy, float px, float py) 放缩

参数里的 sx sy 是横向和纵向的放缩倍数； px py 是放缩的轴心。

```java
canvas.save();
canvas.scale(1.3f, 1.3f, x + bitmapWidth / 2, y + bitmapHeight / 2);
canvas.drawBitmap(bitmap, x, y, paint);
canvas.restore();
```

######## skew(float sx, float sy) 错切

####### 使用 Matrix 来做常见和不常见的二维变换

######## 使用 Matrix 来做常见变换

Matrix 做常见变换的方式：

创建 Matrix 对象；
调用 Matrix 的 pre/postTranslate/Rotate/Scale/Skew() 方法来设置几何变换；
使用 Canvas.setMatrix(matrix) 或 Canvas.concat(matrix) 来把几何变换应用到 Canvas。

```java
Matrix matrix = new Matrix();

...

matrix.reset();
matrix.postTranslate();
matrix.postRotate();

canvas.save();
canvas.concat(matrix);
canvas.drawBitmap(bitmap, x, y, paint);
canvas.restore();
```

把 Matrix 应用到 Canvas 有两个方法： Canvas.setMatrix(matrix) 和 Canvas.concat(matrix)。

- Canvas.setMatrix(matrix)：用 Matrix 直接替换 Canvas 当前的变换矩阵，即抛弃 Canvas 当前的变换，改用 Matrix 的变换（注：根据下面评论里以及我在微信公众号中收到的反馈，不同的系统中 setMatrix(matrix) 的行为可能不一致，所以还是尽量用 concat(matrix) 吧）；
- Canvas.concat(matrix)：用 Canvas 当前的变换矩阵和 Matrix 相乘，即基于 Canvas 当前的变换，叠加上 Matrix 中的变换。

######## Matrix.setPolyToPoly(float[] src, int srcIndex, float[] dst, int dstIndex, int pointCount) 用点对点映射的方式设置变换

参数里，src 和 dst 是源点集合目标点集；srcIndex 和 dstIndex 是第一个点的偏移；pointCount 是采集的点的个数（个数不能大于 4，因为大于 4 个点就无法计算变换了）。

poly 就是「多」的意思。setPolyToPoly() 的作用是通过多点的映射的方式来直接设置变换。「多点映射」的意思就是把指定的点移动到给出的位置，从而发生形变。例如：(0, 0) -> (100, 100) 表示把 (0, 0) 位置的像素移动到 (100, 100) 的位置，这个是单点的映射，单点映射可以实现平移。而多点的映射，就可以让绘制内容任意地扭曲。

```java
Matrix matrix = new Matrix();
float pointsSrc = {left, top, right, top, left, bottom, right, bottom};
float pointsDst = {left - 10, top + 50, right + 120, top - 90, left + 20, bottom + 30, right + 20, bottom + 60};

...

matrix.reset();
matrix.setPolyToPoly(pointsSrc, 0, pointsDst, 0, 4);

canvas.save();
canvas.concat(matrix);
canvas.drawBitmap(bitmap, x, y, paint);
canvas.restore();
```

####### 使用 Camera 来做三维变换

######## Camera.rotate*() 三维旋转

Camera.rotate*() 一共有四个方法： rotateX(deg) rotateY(deg) rotateZ(deg) rotate(x, y, z)。

Camera 和 Canvas 一样也需要保存和恢复状态才能正常绘制，不然在界面刷新之后绘制就会出现问题。

如果你需要图形左右对称，需要配合上 Canvas.translate()，在三维旋转之前把绘制内容的中心点移动到原点，即旋转的轴心，然后在三维旋转后再把投影移动回来：

```java
canvas.save();

camera.save(); // 保存 Camera 的状态
camera.rotateX(30); // 旋转 Camera 的三维空间
canvas.translate(centerX, centerY); // 旋转之后把投影移动回来
camera.applyToCanvas(canvas); // 把旋转投影到 Canvas
canvas.translate(-centerX, -centerY); // 旋转之前把绘制内容移动到轴心（原点）
camera.restore(); // 恢复 Camera 的状态

canvas.drawBitmap(bitmap, point1.x, point1.y, paint);
canvas.restore();
```

> Canvas 的几何变换顺序是反的，所以要把移动到中心的代码写在下面，把从中心移动回来的代码写在上面。

![Android Camera坐标系](https://upload-images.jianshu.io/upload_images/3947109-232cbcea7fdfe9b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######## Camera.translate(float x, float y, float z) 移动

它的使用方式和 Camera.rotate*() 相同，不常用

######## Camera.setLocation(x, y, z) 设置虚拟相机的位置

这个方法有点奇葩，它的参数的单位不是像素，而是 inch，英寸。

在 Camera 中，相机的默认位置是 (0, 0, -8)（英寸）。8 x 72 = 576，所以它的默认位置是 (0, 0, -576)（像素）。

如果绘制的内容过大，当它翻转起来的时候，就有可能出现图像投影过大的「糊脸」效果。而且由于换算单位被写死成了 72 像素，而不是和设备 dpi 相关的，所以在像素越大的手机上，这种「糊脸」效果会越明显。

#### 绘制顺序

绘制过程中最典型的两个部分是上面讲到的主体和子 View，但它们并不是绘制过程的全部。除此之外，绘制过程还包含一些其他内容的绘制。具体来讲，一个完整的绘制过程会依次绘制以下几个内容：

- 背景
- 主体（onDraw()）
- 子 View（dispatchDraw()）
- 滑动边缘渐变和滑动条
- 前景

一般来说，一个 View（或 ViewGroup）的绘制不会这几项全都包含，但必然逃不出这几项，并且一定会严格遵守这个顺序。例如通常一个 LinearLayout 只有背景和子 View，那么它会先绘制背景再绘制子 View；一个 ImageView 有主体，有可能会再加上一层半透明的前景作为遮罩，那么它的前景也会在主体之后进行绘制。需要注意，前景的支持是在 Android 6.0（也就是 API 23）才加入的；之前其实也有，不过只支持 FrameLayout，而直到 6.0 才把这个支持放进了 View 类里。

这其中的第 2、3 两步，前面已经讲过了；第 1 步——背景，它的绘制发生在一个叫 drawBackground() 的方法里，但这个方法是 private 的，不能重写，你如果要设置背景，只能用自带的 API 去设置（xml 布局文件的 android:background 属性以及 Java 代码的 View.setBackgroundXxx() 方法，这个每个人都用得很 6 了），而不能自定义绘制；而第 4、5 两步——滑动边缘渐变和滑动条以及前景，这两部分被合在一起放在了 onDrawForeground() 方法里，这个方法是可以重写的。

![绘制顺序](http://wx4.sinaimg.cn/large/006tKfTcly1fiiwb2nr63j30ga0bddgg.jpg)

滑动边缘渐变和滑动条可以通过 xml 的 android:scrollbarXXX 系列属性或 Java 代码的 View.setXXXScrollbarXXX() 系列方法来设置；前景可以通过 xml 的 android:foreground 属性或 Java 代码的 View.setForeground() 方法来设置。而重写 onDrawForeground() 方法，并在它的 super.onDrawForeground() 方法的上面或下面插入绘制代码，则可以控制绘制内容和滑动边缘渐变、滑动条以及前景的遮盖关系。

关于绘制方法，有两点需要注意一下：

出于效率的考虑，ViewGroup 默认会绕过 draw() 方法，换而直接执行 dispatchDraw()，以此来简化绘制流程。所以如果你自定义了某个 ViewGroup 的子类（比如 LinearLayout）并且需要在它的除 dispatchDraw() 以外的任何一个绘制方法内绘制内容，你可能会需要调用 View.setWillNotDraw(false) 这行代码来切换到完整的绘制流程（是「可能」而不是「必须」的原因是，有些 ViewGroup 是已经调用过 setWillNotDraw(false) 了的，例如 ScrollView）。
有的时候，一段绘制代码写在不同的绘制方法中效果是一样的，这时你可以选一个自己喜欢或者习惯的绘制方法来重写。但有一个例外：如果绘制代码既可以写在 onDraw() 里，也可以写在其他绘制方法里，那么优先写在 onDraw() ，因为 Android 有相关的优化，可以在不需要重绘的时候自动跳过 onDraw() 的重复执行，以提升开发效率。享受这种优化的只有 onDraw() 一个方法。

![draw](http://wx3.sinaimg.cn/large/006tKfTcly1fii5jk7l19j30q70e0di5.jpg)

##### super.onDraw() 前 or 后？

###### 写在 super.onDraw() 的下面

把绘制代码写在 super.onDraw() 的下面，由于绘制代码会在原有内容绘制结束之后才执行，所以绘制内容就会盖住控件原来的内容。

###### 写在 super.onDraw() 的上面

如果把绘制代码写在 super.onDraw() 的上面，由于绘制代码会执行在原有内容的绘制之前，所以绘制的内容会被控件的原内容盖住。

相对来说，这种用法的场景就会少一些。不过只是少一些而不是没有，比如你可以通过在文字的下层绘制纯色矩形来作为「强调色」

##### dispatchDraw()：绘制子 View

###### 写在 super.dispatchDraw() 的下面

只要重写 dispatchDraw()，并在 super.dispatchDraw() 的下面写上你的绘制代码，这段绘制代码就会发生在子 View 的绘制之后，从而让绘制内容盖住子 View 了。

###### 写在 super.dispatchDraw() 的上面

同理，把绘制代码写在 super.dispatchDraw() 的上面，这段绘制就会在 onDraw() 之后、 super.dispatchDraw() 之前发生，也就是绘制内容会出现在主体内容和子 View 之间。

##### onDrawForeground()

###### 写在 super.onDrawForeground() 的下面

如果你把绘制代码写在了 super.onDrawForeground() 的下面，绘制代码会在滑动边缘渐变、滑动条和前景之后被执行，那么绘制内容将会盖住滑动边缘渐变、滑动条和前景。

###### 写在 super.onDrawForeground() 的上面

如果你把绘制代码写在了 super.onDrawForeground() 的上面，绘制内容就会在 dispatchDraw() 和 super.onDrawForeground() 之间执行，那么绘制内容会盖住子 View，但被滑动边缘渐变、滑动条以及前景盖住

##### draw() 总调度方法

```java
// View.java 的 draw() 方法的简化版大致结构（是大致结构，不是源码哦）：

public void draw(Canvas canvas) {
    ...
    
    drawBackground(Canvas); // 绘制背景（不能重写）
    onDraw(Canvas); // 绘制主体
    dispatchDraw(Canvas); // 绘制子 View
    onDrawForeground(Canvas); // 绘制滑动相关和前景
    
    ...
}
```
从上面的代码可以看出，onDraw() dispatchDraw() onDrawForeground() 这三个方法在 draw() 中被依次调用，因此它们的遮盖关系也就像前面所说的——dispatchDraw() 绘制的内容盖住 onDraw() 绘制的内容；onDrawForeground() 绘制的内容盖住 dispatchDraw() 绘制的内容。而在它们的外部，则是由 draw() 这个方法作为总的调度。所以，你也可以重写 draw() 方法来做自定义的绘制。

![draw()](http://wx2.sinaimg.cn/large/006tKfTcly1fiix28rb6mj30ru0c8jsb.jpg)

###### 写在 super.draw() 的下面

由于 draw() 是总调度方法，所以如果把绘制代码写在 super.draw() 的下面，那么这段代码会在其他所有绘制完成之后再执行，也就是说，它的绘制内容会盖住其他的所有绘制内容。

###### 写在 super.draw() 的上面

同理，由于 draw() 是总调度方法，所以如果把绘制代码写在 super.draw() 的上面，那么这段代码会在其他所有绘制之前被执行，所以这部分绘制内容会被其他所有的内容盖住，包括背景。是的，背景也会盖住它。

### 属性动画

使用方式：

- 如果是自定义控件，需要添加 setter / getter 方法；
- 用 ObjectAnimator.ofXXX() 创建 ObjectAnimator 对象；
-用 start() 方法执行动画。

#### ViewPropertyAnimator

使用方式：

- 如果是自定义控件，需要添加 setter / getter 方法；
- 用 ObjectAnimator.ofXXX() 创建 ObjectAnimator 对象；
- 用 start() 方法执行动画。

```java
public class SportsView extends View {
    float progress = 0;
    
    ......
    
    // 创建 getter 方法
    public float getProgress() {
        return progress;
    }

    // 创建 setter 方法
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }
    
    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        
        ......
        
        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);
        
        ......
    }
}

......

// 创建 ObjectAnimator 对象
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);
// 执行动画
animator.start();
```

#### ObjectAnimator

使用方式：

- 如果是自定义控件，需要添加 setter / getter 方法；
- 用 ObjectAnimator.ofXXX() 创建 ObjectAnimator 对象；
- 用 start() 方法执行动画。

```java
public class SportsView extends View {
    float progress = 0;
    
    ......
    
    // 创建 getter 方法
    public float getProgress() {
        return progress;
    }

    // 创建 setter 方法
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }
    
    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        
        ......
        
        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);
        
        ......
    }
}

......

// 创建 ObjectAnimator 对象
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);
// 执行动画
animator.start();
```

![img](http://wx3.sinaimg.cn/large/006tKfTcgy1fj7y2vnw5jg30ek0dijwq.gif)

##### setDuration(int duration) 设置动画时长

##### setInterpolator(Interpolator interpolator) 设置 Interpolator

###### AccelerateDecelerateInterpolator

先加速再减速。这是默认的 Interpolator

###### LinearInterpolator

匀速

###### AccelerateInterpolator

持续加速

它主要用在离场效果中，比如某个物体从界面中飞离，就可以用这种效果。

###### DecelerateInterpolator

持续减速直到 0

它的效果和上面这个 AccelerateInterpolator 相反，适用场景也和它相反：它主要用于入场效果，比如某个物体从界面的外部飞入界面后停在某处。

###### AnticipateInterpolator

先回拉一下再进行正常动画轨迹。

###### OvershootInterpolator

动画会超过目标值一些，然后再弹回来。

###### AnticipateOvershootInterpolator

开始前回拉，最后超过一些然后回弹。

###### BounceInterpolator

在目标值处弹跳。有点像玻璃球掉在地板上的效果。

###### CycleInterpolator

这个也是一个正弦 / 余弦曲线，不过它和 AccelerateDecelerateInterpolator 的区别是，它可以自定义曲线的周期，所以动画可以不到终点就结束，也可以到达终点后回弹，回弹的次数由曲线的周期决定，曲线的周期由 CycleInterpolator() 构造方法的参数决定。

参数为0.5f

![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8in23hktg30lg0bu197.gif)

###### PathInterpolator

用这个 Interpolator 你可以定制出任何你想要的速度模型。定制的方式是使用一个 Path 对象来绘制出你要的动画完成度 / 时间完成度曲线。例如：

```java
Path interpolatorPath = new Path();

...

// 先以「动画完成度 : 时间完成度 = 1 : 1」的速度匀速运行 25%
interpolatorPath.lineTo(0.25f, 0.25f);
// 然后瞬间跳跃到 150% 的动画完成度
interpolatorPath.moveTo(0.25f, 1.5f);
// 再匀速倒车，返回到目标点
interpolatorPath.lineTo(1, 1);
```
![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8jmom7kaj30cd0ay74f.jpg)

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8jsmxr3eg30lg0buto5.gif)

不过要注意，这条 Path 描述的其实是一个 y = f(x) (0 ≤ x ≤ 1) （y 为动画完成度，x 为时间完成度）的曲线，所以同一段时间完成度上不能有两段不同的动画完成度（这个好理解吧？因为内容不能出现分身术呀），而且每一个时间完成度的点上都必须要有对应的动画完成度（因为内容不能在某段时间段内消失呀）。所以，下面这样的 Path 是非法的，会导致程序 FC

出现重复的动画完成度，即动画内容出现「分身」——程序 FC
![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8lidbk4gj30c909jq34.jpg)

有一段时间完成度没有对应的动画完成度，即动画出现「中断」——程序 FC

![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8lk0do93j30c109baa6.jpg)

###### FastOutLinearInInterpolator

和 AccelerateInterpolator 一样，都是一个持续加速的运动路线。只不过 FastOutLinearInInterpolator 的曲线公式是用的贝塞尔曲线，而 AccelerateInterpolator 用的是指数曲线。具体来说，它俩最主要的区别是 FastOutLinearInInterpolator 的初始阶段加速度比 AccelerateInterpolator 要快一些。

###### FastOutSlowInInterpolator

同样也是先加速再减速的还有前面说过的 AccelerateDecelerateInterpolator，不过它们的效果是明显不一样的。FastOutSlowInInterpolator 用的是贝塞尔曲线，AccelerateDecelerateInterpolator 用的是正弦 / 余弦曲线。具体来讲， FastOutSlowInInterpolator 的前期加速度要快得多。

用更直观一点的表达就是，AccelerateDecelerateInterpolator 像是物体的自我移动，而 FastOutSlowInInterpolator 则看起来像有一股强大的外力「推」着它加速，在接近目标值之后又「拽」着它减速。总之，FastOutSlowInterpolator 看起来有一点「着急」的感觉。

###### LinearOutSlowInInterpolator

它和 DecelerateInterpolator 比起来，同为减速曲线，主要区别在于 LinearOutSlowInInterpolator 的初始速度更高。

#### 设置监听器

##### ViewPropertyAnimator.setListener() / ObjectAnimator.addListener()

这两个方法的名称不一样，可以设置的监听器数量也不一样，但它们的参数类型都是 AnimatorListener，所以本质上其实都是一样的。 AnimatorListener 共有 4 个回调方法：

3.1.1 onAnimationStart(Animator animation)
当动画开始执行时，这个方法被调用。

3.1.2 onAnimationEnd(Animator animation)
当动画结束时，这个方法被调用。

3.1.3 onAnimationCancel(Animator animation)
当动画被通过 cancel() 方法取消时，这个方法被调用。

需要说明一下的是，就算动画被取消，onAnimationEnd() 也会被调用。所以当动画被取消时，如果设置了 AnimatorListener，那么 onAnimationCancel() 和 onAnimationEnd() 都会被调用。onAnimationCancel() 会先于 onAnimationEnd() 被调用。

3.1.4 onAnimationRepeat(Animator animation)
当动画通过 setRepeatMode() / setRepeatCount() 或 repeat() 方法重复执行时，这个方法被调用。

由于 ViewPropertyAnimator 不支持重复，所以这个方法对 ViewPropertyAnimator 相当于无效。

##### ViewPropertyAnimator.setUpdateListener() / ObjectAnimator.addUpdateListener()

当动画的属性更新时（不严谨的说，即每过 10 毫秒，动画的完成度更新时），这个方法被调用。

##### ViewPropertyAnimator.withStartAction/EndAction()

这两个方法是 ViewPropertyAnimator 的独有方法。它们和 set/addListener() 中回调的 onAnimationStart() / onAnimationEnd() 相比起来的不同主要有两点：

withStartAction() / withEndAction() 是一次性的，在动画执行结束后就自动弃掉了，就算之后再重用 ViewPropertyAnimator 来做别的动画，用它们设置的回调也不会再被调用。而 set/addListener() 所设置的 AnimatorListener 是持续有效的，当动画重复执行时，回调总会被调用。

withEndAction() 设置的回调只有在动画正常结束时才会被调用，而在动画被取消时不会被执行。这点和 AnimatorListener.onAnimationEnd() 的行为是不一致的。

#### 针对特殊类型的属性来做属性动画

##### TypeEvaluator

它的作用是让你可以对同样的属性有不同的解析方式，对本来无法解析的属性也可以打造出你需要的解析方式。有了 TypeEvaluator，你的属性动画就有了更大的灵活性，从而有了无限的可能。

```java
ObjectAnimator animator = ObjectAnimator.ofInt(view, "color", 0xffff0000, 0xff00ff00);
animator.setEvaluator(new ArgbEvaluator());
animator.start();
```

##### ofObject()

借助于 TypeEvaluator，属性动画就可以通过 ofObject() 来对不限定类型的属性做动画了。方式很简单：

为目标属性写一个自定义的 TypeEvaluator
使用 ofObject() 来创建 Animator，并把自定义的 TypeEvaluator 作为参数填入

```java
private class PointFEvaluator implements TypeEvaluator<PointF> {
   PointF newPoint = new PointF();

   @Override
   public PointF evaluate(float fraction, PointF startValue, PointF endValue) {
       float x = startValue.x + (fraction * (endValue.x - startValue.x));
       float y = startValue.y + (fraction * (endValue.y - startValue.y));

       newPoint.set(x, y);

       return newPoint;
   }
}

ObjectAnimator animator = ObjectAnimator.ofObject(view, "position",
        new PointFEvaluator(), new PointF(0, 0), new PointF(1, 1));
animator.start();
```

#### 针对复杂的属性关系来做属性动画

##### PropertyValuesHolder 同一个动画中改变多个属性

```java
PropertyValuesHolder holder1 = PropertyValuesHolder.ofFloat("scaleX", 1);
PropertyValuesHolder holder2 = PropertyValuesHolder.ofFloat("scaleY", 1);
PropertyValuesHolder holder3 = PropertyValuesHolder.ofFloat("alpha", 1);
 
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder1, holder2, holder3)
animator.start();
```

##### AnimatorSet 多个动画配合执行

有的时候，你不止需要在一个动画中改变多个属性，还会需要多个动画配合工作，比如，在内容的大小从 0 放大到 100% 大小后开始移动。这种情况使用 PropertyValuesHolder 是不行的，因为这些属性如果放在同一个动画中，需要共享动画的开始时间、结束时间、Interpolator 等等一系列的设定，这样就不能有先后次序地执行动画了。

这就需要用到 AnimatorSet 了。

```java
ObjectAnimator animator1 = ObjectAnimator.ofFloat(...);
animator1.setInterpolator(new LinearInterpolator());
ObjectAnimator animator2 = ObjectAnimator.ofInt(...);
animator2.setInterpolator(new DecelerateInterpolator());
 
AnimatorSet animatorSet = new AnimatorSet();
// 两个动画依次执行
animatorSet.playSequentially(animator1, animator2);
animatorSet.start();

// 两个动画同时执行
animatorSet.playTogether(animator1, animator2);
animatorSet.start();

// 使用 AnimatorSet.play(animatorA).with/before/after(animatorB)
// 的方式来精确配置各个 Animator 之间的关系
animatorSet.play(animator1).with(animator2);
animatorSet.play(animator1).before(animator2);
animatorSet.play(animator1).after(animator2);
animatorSet.start();
```

##### PropertyValuesHolders.ofKeyframe() 把同一个属性拆分

除了合并多个属性和调配多个动画，你还可以在 PropertyValuesHolder 的基础上更进一步，通过设置 Keyframe （关键帧），把同一个动画属性拆分成多个阶段。例如，你可以让一个进度增加到 100% 后再「反弹」回来。

```java
// 在 0% 处开始
Keyframe keyframe1 = Keyframe.ofFloat(0, 0);
// 时间经过 50% 的时候，动画完成度 100%
Keyframe keyframe2 = Keyframe.ofFloat(0.5f, 100);
// 时间见过 100% 的时候，动画完成度倒退到 80%，即反弹 20%
Keyframe keyframe3 = Keyframe.ofFloat(1, 80);
PropertyValuesHolder holder = PropertyValuesHolder.ofKeyframe("progress", keyframe1, keyframe2, keyframe3);

ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder);
animator.start();
```

![img](http://wx4.sinaimg.cn/large/006tNc79ly1fjfig8edhmg30ck07046i.gif)

### 硬件加速

硬件加速指的是使用 GPU 来完成绘制的计算工作，代替 CPU。它从工作分摊和绘制机制优化这两个角度提升了绘制的速度。

硬件加速可以使用 setLayerType() 来关闭硬件加速，但这个方法其实是用来设置 View Layer 的：

- 参数为 LAYER_TYPE_SOFTWARE 时，使用软件来绘制 View Layer，绘制到一个 Bitmap，并顺便关闭硬件加速；
- 参数为 LAYER_TYPE_HARDWARE 时，使用 GPU 来绘制 View Layer，绘制到一个 OpenGL texture（如果硬件加速关闭，那么行为和 VIEW_TYPE_SOFTWARE 一致）；
- 参数为 LAYER_TYPE_NONE 时，关闭 View Layer。

View Layer 可以加速无 invalidate() 时的刷新效率，但对于需要调用 invalidate() 的刷新无法加速。

View Layer 绘制所消耗的实际时间是比不使用 View Layer 时要高的，所以要慎重使用。

#### 概念

所谓硬件加速，指的是把某些计算工作交给专门的硬件来做，而不是和普通的计算工作一样交给 CPU 来处理。这样不仅减轻了 CPU 的压力，而且由于有了「专人」的处理，这份计算工作的速度也被加快了。这就是「硬件加速」。

而对于 Android 来说，硬件加速有它专属的意思：在 Android 里，硬件加速专指把 View 中绘制的计算工作交给 GPU 来处理。进一步地再明确一下，这个「绘制的计算工作」指的就是把绘制方法中的那些 Canvas.drawx3X() 变成实际的像素这件事。

#### 原理

在硬件加速关闭的时候，Canvas 绘制的工作方式是：把要绘制的内容写进一个 Bitmap，然后在之后的渲染过程中，这个 Bitmap 的像素内容被直接用于渲染到屏幕。这种绘制方式的主要计算工作在于把绘制操作转换为像素的过程（例如由一句 Canvas.drawCircle() 来获得一个具体的圆的像素信息），这个过程的计算是由 CPU 来完成的。

而在硬件加速开启时，Canvas 的工作方式改变了：它只是把绘制的内容转换为 GPU 的操作保存了下来，然后就把它交给 GPU，最终由 GPU 来完成实际的显示工作。

硬件加速能够让绘制变快，主要有三个原因：

- 本来由 CPU 自己来做的事，分摊给了 GPU 一部分，自然可以提高效率；
- 相对于 CPU 来说，GPU 自身的设计本来就对于很多常见类型内容的计算（例如简单的圆形、简单的方形）具有优势；
- 由于绘制流程的不同，硬件加速在界面内容发生重绘的时候绘制流程可以得到优化，避免了一些重复操作，从而大幅提升绘制效率。

在硬件加速关闭时，绘制内容会被 CPU 转换成实际的像素，然后直接渲染到屏幕。具体来说，这个「实际的像素」，它是由 Bitmap 来承载的。在界面中的某个 View 由于内容发生改变而调用 invalidate() 方法时，如果没有开启硬件加速，那么为了正确计算 Bitmap 的像素，这个 View 的父 View、父 View 的父 View 乃至一直向上直到最顶级 View，以及所有和它相交的兄弟 View，都需要被调用 invalidate()来重绘。一个 View 的改变使得大半个界面甚至整个界面都重绘一遍，这个工作量是非常大的。

而在硬件加速开启时，前面说过，绘制的内容会被转换成 GPU 的操作保存下来（承载的形式称为 display list，对应的类也叫做 DisplayList），再转交给 GPU。由于所有的绘制内容都没有变成最终的像素，所以它们之间是相互独立的，那么在界面内容发生改变的时候，只要把发生了改变的 View 调用 invalidate() 方法以更新它所对应的 GPU 操作就好，至于它的父 View 和兄弟 View，只需要保持原样。那么这个工作量就很小了。

#### 限制

硬件加速不只是好处，也有它的限制：受到 GPU 绘制方式的限制，Canvas 的有些方法在硬件加速开启式会失效或无法正常工作。比如，在硬件加速开启时， clipPath() 在 API 18 及以上的系统中才有效。具体的 API 限制和 API 版本的关系如下图：

![img](http://wx2.sinaimg.cn/large/006tKfTcly1fjn0huxdm5j30lr0q0n25.jpg)

所以，如果你的自定义控件中有自定义绘制的内容，最好参照一下这份表格，确保你的绘制操作可以正确地在所有用户的手机里能够正常显示，而不是只在你的运行了最新版本 Android 系统的 Nexus 或 Pixel 里测试一遍没问题就发布了。

不过有一点可以放心的是，所有的原生自带控件，都没有用到 API 版本不兼容的绘制操作，可以放心使用。所以你只要检查你写的自定义绘制就好。

#### View Layer

如果你的绘制操作不支持硬件加速，你需要手动关闭硬件加速来绘制界面，关闭的方式是通过这行代码：

```java
view.setLayerType(LAYER_TYPE_SOFTWARE, null);
```

事实上，这个方法的本来作用并不是用来开关硬件加速的，只是当它的参数为 LAYER_TYPE_SOFTWARE 的时候，可以「顺便」把硬件加速关掉而已；并且除了这个方法之外，Android 并没有提供专门的 View 级别的硬件加速开关，所以它就「顺便」成了一个开关硬件加速的方法。

setLayerType() 这个方法，它的作用其实就是名字里的意思：设置 View Layer 的类型。所谓 View Layer，又称为离屏缓冲（Off-screen Buffer），它的作用是单独启用一块地方来绘制这个 View ，而不是使用软件绘制的 Bitmap 或者通过硬件加速的 GPU。这块「地方」可能是一块单独的 Bitmap，也可能是一块 OpenGL 的纹理（texture，OpenGL 的纹理可以简单理解为图像的意思），具体取决于硬件加速是否开启。采用什么来绘制 View 不是关键，关键在于当设置了 View Layer 的时候，它的绘制会被缓存下来，而且缓存的是最终的绘制结果，而不是像硬件加速那样只是把 GPU 的操作保存下来再交给 GPU 去计算。通过这样更进一步的缓存方式，View 的重绘效率进一步提高了：只要绘制的内容没有变，那么不论是 CPU 绘制还是 GPU 绘制，它们都不用重新计算，而只要只用之前缓存的绘制结果就可以了。

基于这样的原理，在进行移动、旋转等（无需调用 invalidate()）的属性动画的时候开启 Hardware Layer 将会极大地提升动画的效率，因为在动画过程中 View 本身并没有发生改变，只是它的位置或角度改变了，而这种改变是可以由 GPU 通过简单计算就完成的，并不需要重绘整个 View。所以在这种动画的过程中开启 Hardware Layer，可以让本来就依靠硬件加速而变流畅了的动画变得更加流畅。实现方式大概是这样：

```java
view.setLayerType(LAYER_TYPE_HARDWARE, null);
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "rotationY", 180);

animator.addListener(new AnimatorListenerAdapter() {
    @Override
    public void onAnimationEnd(Animator animation) {
        view.setLayerType(LAYER_TYPE_NONE, null);
    }
});

animator.start();
```

或者如果是使用 ViewPropertyAnimator，那么更简单：

```
view.animate()
        .rotationY(90)
        .withLayer(); // withLayer() 可以自动完成上面这段代码的复杂操作
```

不过一定要注意，只有你在对 translationX translationY rotation alpha 等无需调用 invalidate() 的属性做动画的时候，这种方法才适用，因为这种方法本身利用的就是当界面不发生时，缓存未更新所带来的时间的节省。所以简单地说——

**这种方式不适用于基于自定义属性绘制的动画。**一定记得这句话。

另外，除了用于关闭硬件加速和辅助属性动画这两项功能外，Layer 还可以用于给 View 增加一些绘制效果，例如设置一个 ColorMatrixColorFilter 来让 View 变成黑白的：

```java
ColorMatrix colorMatrix = new ColorMatrix();
colorMatrix.setSaturation(0);

Paint paint = new Paint();
paint.setColorFilter(new ColorMatrixColorFilter(colorMatrix));

view.setLayerType(LAYER_TYPE_HARDWARE, paint);
```

## 布局

布局的过程，就是程序在运行时利用布局文件的代码来计算出实际尺寸的过程。

![image](https://upload-images.jianshu.io/upload_images/3947109-fe8da0a2b47b8aee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### View 或 ViewGroup 的布局过程

#### 测量阶段

从上到下递归地调用每个 View 或者 ViewGroup 的 measure() 方法，测量他们的尺寸并计算它们的位置；

测量阶段，measure() 方法被父 View 调用，在 measure() 中做一些准备和优化工作后，调用 onMeasure() 来进行实际的自我测量。 onMeasure() 做的事，View 和 ViewGroup 不一样：

View：View 在 onMeasure() 中会计算出自己的尺寸然后保存；
ViewGroup：ViewGroup 在 onMeasure() 中会调用所有子 View 的 measure() 让它们进行自我测量，并根据子 View 计算出的期望尺寸来计算出它们的实际尺寸和位置（实际上 99.99% 的父 View 都会使用子 View 给出的期望尺寸来作为实际尺寸，原因在下期或下下期会讲到）然后保存。同时，它也会根据子 View 的尺寸和位置来计算出自己的尺寸然后保存；

#### 布局阶段

从上到下递归地调用每个 View 或者 ViewGroup 的 layout() 方法，把测得的它们的尺寸和位置赋值给它们。

布局阶段，layout() 方法被父 View 调用，在 layout() 中它会保存父 View 传进来的自己的位置和尺寸，并且调用 onLayout() 来进行实际的内部布局。onLayout() 做的事， View 和 ViewGroup 也不一样：

View：由于没有子 View，所以 View 的 onLayout() 什么也不做。
ViewGroup：ViewGroup 在 onLayout() 中会调用自己的所有子 View 的 layout() 方法，把它们的尺寸和位置传给它们，让它们完成自我的内部布局。

### 布局过程自定义的方式

#### 重写 onMeasure() 来修改已有的 View 的尺寸；

重写 onMeasure() 来修改已有的 View 的尺寸的具体做法：

- 重写 onMeasure() 方法，并在里面调用 super.onMeasure()，触发原有的自我测量；
- 在 super.onMeasure() 的下面用 getMeasuredWidth() 和 getMeasuredHeight() 来获取到之前的测量结果，并使用自己的算法，根据测量结果计算出新的结果；
- 调用 setMeasuredDimension() 来保存新的结果。

#### 重写 onMeasure() 来全新定制自定义 View 的尺寸；

全新定制尺寸和修改尺寸的最重要区别是需要在计算的同时，保证计算结果满足父 View 给出的的尺寸限制

父 View 的尺寸限制
1. 由来：开发者的要求（布局文件中 layout_ 打头的属性）经过父 View 处理计算后的更精确的要求；
2. 限制的分类：
- UNSPECIFIED：不限制
- AT_MOST：限制上限
- EXACTLY：限制固定值

全新定义自定义 View 尺寸的方式:
1. 重新 onMeasure()，并计算出 View 的尺寸；
2. 使用 resolveSize() 来让子 View 的计算结果符合父 View 的限制（当然，如果你想用自己的方式来满足父 View 的限制也行）。

#### 重写 onMeasure() 和 onLayout() 来全新定制自定义 ViewGroup 的内部布局。

## 触摸反馈
