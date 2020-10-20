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

#########  getOffsetForAdvance(CharSequence text, int start, int end, int contextStart, int contextEnd, boolean isRtl, float advance)

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

#### 使用不同的绘制方法来控制遮挡关系

## 布局

## 触摸反馈
