# Android架构设计

## MVC

### MVC 概念

VC全名是Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。

其中M层处理数据，业务逻辑等；V层处理界面的显示结果；C层起到桥梁的作用，来控制V层和M层通信以此来达到分离视图显示和业务逻辑层。

![image.png](https://upload-images.jianshu.io/upload_images/3947109-47846adf5bc59e5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### MVC for Android

- M层：适合做一些业务逻辑处理，比如数据库存取操作，网络操作，复杂的算法，耗时的任务等都在model层处理。

- V层：应用层中处理数据显示的部分，XML布局可以视为V层，显示Model层的数据结果。

- C层：在Android中，Activity处理用户交互问题，因此可以认为Activity是控制器，Activity读取V视图层的数据（eg.读取当前EditText控件的数据），控制用户输入（eg.EditText控件数据的输入），并向Model发送数据请求（eg.发起网络请求等）。

### 代码示例

#### Model

这里设计了一个WeatherModel模型接口，然后实现了接口WeatherModelImpl类。controller控制器activity调用WeatherModelImpl类中的方法发起网络请求，然后通过实现OnWeatherListener接口来获得网络请求的结果通知View视图层更新UI 。

至此，Activity就将View视图显示和Model模型数据处理隔离开了。activity担当contronller完成了model和view之间的协调作用。

```java
public interface WeatherModel {
    void getWeather(String cityNumber, OnWeatherListener listener);
}

##########################################################################################

/**
 * Description:从网络获取天气信息接口实现
 * User: xjp
 * Date: 2015/6/3
 * Time: 15:40
 */

public class WeatherModelImpl implements WeatherModel {

    @Override
    public void getWeather(String cityNumber, final OnWeatherListener listener) {

        /*数据层操作*/
        VolleyRequest.newInstance().newGsonRequest("http://www.weather.com.cn/data/sk/" + cityNumber + ".html",
                Weather.class, new Response.Listener<Weather>() {
                    @Override
                    public void onResponse(Weather weather) {
                        if (weather != null) {
                            listener.onSuccess(weather);
                        } else {
                            listener.onError();
                        }
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        listener.onError();
                    }
                });
    }
}
```

#### Controller

Activity持有了WeatherModel模型的对象，当用户有点击Button交互的时候，Activity作为Controller控制层读取View视图层EditTextView的数据，然后向Model模型发起数据请求，也就是调用WeatherModel对象的方法 getWeathre（）方法。

当Model模型处理数据结束后，通过接口OnWeatherListener通知View视图层数据处理完毕，View视图层该更新界面UI了。然后View视图层调用displayResult（）方法更新UI。至此，整个MVC框架流程就在Activity中体现出来了。

至于这里为什么不直接设计成类里面的一个getWeather（）方法直接请求网络数据？你考虑下这种情况：现在代码中的网络请求是使用Volley框架来实现的，如果哪天老板非要你使用Afinal框架实现网络请求，你怎么解决问题？难道是修改 getWeather（）方法的实现？ no no no，这样修改不仅破坏了以前的代码，而且还不利于维护， 考虑到以后代码的扩展和维护性，我们选择设计接口的方式来解决着一个问题，我们实现另外一个WeatherModelWithAfinalImpl类，继承自WeatherModel，重写里面的方法，这样不仅保留了以前的WeatherModelImpl类请求网络方式，还增加了WeatherModelWithAfinalImpl类的请求方式。Activity调用代码无需要任何修改。

```java
public class MainActivity extends ActionBarActivity implements OnWeatherListener, View.OnClickListener {

    private WeatherModel weatherModel;
    private Dialog loadingDialog;
    private EditText cityNOInput;
    private TextView city;
    private TextView cityNO;
    private TextView temp;
    private TextView wd;
    private TextView ws;
    private TextView sd;
    private TextView wse;
    private TextView time;
    private TextView njd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        weatherModel = new WeatherModelImpl();
        initView();
    }

    private void initView() {
        cityNOInput = findView(R.id.et_city_no);
        city = findView(R.id.tv_city);
        cityNO = findView(R.id.tv_city_no);
        temp = findView(R.id.tv_temp);
        wd = findView(R.id.tv_WD);
        ws = findView(R.id.tv_WS);
        sd = findView(R.id.tv_SD);
        wse = findView(R.id.tv_WSE);
        time = findView(R.id.tv_time);
        njd = findView(R.id.tv_njd);
        findView(R.id.btn_go).setOnClickListener(this);

        loadingDialog = new ProgressDialog(this);
        loadingDialog.setTitle("加载天气中...");


    }

    public void displayResult(Weather weather) {
        WeatherInfo weatherInfo = weather.getWeatherinfo();
        city.setText(weatherInfo.getCity());
        cityNO.setText(weatherInfo.getCityid());
        temp.setText(weatherInfo.getTemp());
        wd.setText(weatherInfo.getWD());
        ws.setText(weatherInfo.getWS());
        sd.setText(weatherInfo.getSD());
        wse.setText(weatherInfo.getWSE());
        time.setText(weatherInfo.getTime());
        njd.setText(weatherInfo.getNjd());
    }

    public void hideLoadingDialog() {
        loadingDialog.dismiss();
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_go:
                loadingDialog.show();
                weatherModel.getWeather(cityNOInput.getText().toString().trim(), this);
                break;
        }
    }

    @Override
    public void onSuccess(Weather weather) {
        hideLoadingDialog();
        displayResult(weather);
    }

    @Override
    public void onError() {
        hideLoadingDialog();
        Toast.makeText(this, "获取天气信息失败", Toast.LENGTH_SHORT).show();
    }

    private <T extends View> T findView(int id) {
        return (T) findViewById(id);
    }
}
```

### 总结

1. 利用MVC设计模式，使得这个天气预报小项目有了很好的可扩展和维护性，当需要改变UI显示的时候，无需修改Contronller（控制器）Activity的代码和Model（模型）WeatherModel模型中的业务逻辑代码，很好的将业务逻辑和界面显示分离。

2. 在Android项目中，业务逻辑，数据处理等担任了Model（模型）角色，XML界面显示等担任了View（视图）角色，Activity担任了Contronller（控制器）角色。contronller（控制器）是一个中间桥梁的作用，通过接口通信来协同 View（视图）和Model（模型）工作，起到了两者之间的通信作用。

3. 什么时候适合使用MVC设计模式？当然一个小的项目且无需频繁修改需求就不用MVC框架来设计了，那样反而觉得代码过度设计，代码臃肿。一般在大的项目中，且业务逻辑处理复杂，页面显示比较多，需要模块化设计的项目使用MVC就有足够的优势了。

4. 在MVC模式中我们发现，其实控制器Activity主要是起到解耦作用，将View视图和Model模型分离，虽然Activity起到交互作用，但是找Activity中有很多关于视图UI的显示代码，因此View视图和Activity控制器并不是完全分离的，也就是说一部分View视图和Contronller控制器Activity是绑定在一个类中的。

### MVC的优点

- 耦合性低。所谓耦合性就是模块代码之间的关联程度。利用MVC框架使得View（视图）层和Model（模型）层可以很好的分离，这样就达到了解耦的目的，所以耦合性低，减少模块代码之间的相互影响。

- 可扩展性好。由于耦合性低，添加需求，扩展代码就可以减少修改之前的代码，降低bug的出现率。

- 模块职责划分明确。主要划分层M,V,C三个模块，利于代码的维护。

## MVP

### MVP 简介

相信大家对 MVC 都是比较熟悉了：M-Model-模型、V-View-视图、C-Controller-控制器，MVP作为MVC的演化版本，也是作为用户界面（用户层）的实现模式，那么类似的MVP所对应的意义：M-Model-模型、V-View-视图、P-Presenter-表示器。从MVC和MVP两者结合来看，Controlller/Presenter在MVC/MVP中都起着逻辑控制处理的角色，起着控制各业务流程的作用。

而MVP与MVC最不同的一点是M与V是不直接关联的也是就Model与View不存在直接关系，这两者之间间隔着的是Presenter层，其负责调控View与Model之间的间接交互，MVP的结构图如下所示，对于这个图理解即可而不必限于其中的条条框框，毕竟在不同的场景下多少会有些出入的。在Android中很重要的一点就是对UI的操作基本上需要异步进行也就是在MainThread中才能操作UI，所以对View与Model的切断分离是合理的。此外Presenter与View、Model的交互使用接口定义交互操作可以进一步达到松耦合也可以通过接口更加方便地进行单元测试。

![1](https://static.rocko.xyz/img/Android%E4%B8%AD%E7%9A%84MVP_1.png)

### Model

Model 是用户界面需要显示数据的抽象，也可以理解为从业务数据（结果）那里到用户界面的抽象（Business rule, data access, model classes）。本文 Demo 为了简单处理就直接把业务放到了对应的 Model 之中。

### View

视图这一层体现的很轻薄，负责显示数据、提供友好界面跟用户交互就行。MVP下Activity和Fragment体现在了这一层，Activity一般也就做加载UI视图、设置监听再交由Presenter处理的一些工作，所以也就需要持有相应Presenter的引用。例如，Activity上滚动列表时隐藏或者显示Acionbar（Toolbar），这样的UI逻辑时也应该在这一层。另外在View上输入的数据做一些判断时，例如，EditText的输入数据，假如是简单的非空判断则可以作为View层的逻辑，而当需要对EditText的数据进行更复杂的比较时，如从数据库获取本地数据进行判断时明显需要经过Model层才能返回了，所以这些细节需要自己掂量。

### Presenter

Presenter这一层处理着程序各种逻辑的分发，收到View层UI上的反馈命令、定时命令、系统命令等指令后分发处理逻辑交由业务层做具体的业务操作，然后将得到的 Model 给 View 显示。

### 代码示例

包图中明显的三层：Model包、Presenter包、UI包，其中，三者都实现各自的结构，Model为WeatherModel、Presenter为WeatherPresenter、View为Weather，那么具体实现类就是impl包里的了，View层的即为Activity。此外的app和util包无关紧要可以不看。可以看到采用MVP设计后项目明显多了很多东西，这也是不可避免的，使用原始方法可以使项目开起来简单些但是以后还有维护呢、测试呢、加功能呢、。。。

#### View 接口

```java
public interface WeatherView {
    void showLoading();
    void hideLoading();
    void showError();
    void setWeatherInfo(Weather weather);
}

/**
 * 天气界面
 */
public class WeatherActivity extends BaseActivity implements WeatherView, View.OnClickListener {
    private Dialog loadingDialog;
    private EditText cityNOInput;
    private TextView city;
    private TextView cityNO;
    private TextView temp;
    private TextView wd;
    private TextView ws;
    private TextView sd;
    private TextView wse;
    private TextView time;
    private TextView njd;

    private WeatherPresenter weatherPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();

    }

    private void init() {
        cityNOInput = findView(R.id.et_city_no);
        city = findView(R.id.tv_city);
        cityNO = findView(R.id.tv_city_no);
        temp = findView(R.id.tv_temp);
        wd = findView(R.id.tv_WD);
        ws = findView(R.id.tv_WS);
        sd = findView(R.id.tv_SD);
        wse = findView(R.id.tv_WSE);
        time = findView(R.id.tv_time);
        njd = findView(R.id.tv_njd);

        findView(R.id.btn_go).setOnClickListener(this);

        weatherPresenter = new WeatherPresenterImpl(this); //传入WeatherView
        loadingDialog = new ProgressDialog(this);
        loadingDialog.setTitle("加载天气中...");
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_go:
                weatherPresenter.getWeather(cityNOInput.getText().toString().trim());
                break;
        }
    }


    @Override
    public void showLoading() {
        loadingDialog.show();
    }

    @Override
    public void hideLoading() {
        loadingDialog.dismiss();
    }

    @Override
    public void showError() {
        //Do something
        Toast.makeText(getApplicationContext(), "error", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void setWeatherInfo(Weather weather) {
        WeatherInfo info = weather.getWeatherinfo();
        city.setText(info.getCity());
        cityNO.setText(info.getCityid());
        temp.setText(info.getTemp());
        wd.setText(info.getWD());
        ws.setText(info.getWS());
        sd.setText(info.getSD());
        wse.setText(info.getWS());
        time.setText(info.getTemp());
        njd.setText(info.getNjd());
    }
}
```

#### Presenter 接口

```java
public interface WeatherPresenter {
    /**
     * 获取天气的逻辑
     */
    void getWeather(String cityNO);
}

public interface OnWeatherListener {
    /**
     * 成功时回调
     *
     * @param weather
     */
    void onSuccess(Weather weather);
    /**
     * 失败时回调，简单处理，没做什么
     */
    void onError();
}

/**
 * Created by Administrator on 2015/2/6.
 * 天气 Prestener实现
 */
public class WeatherPresenterImpl implements WeatherPresenter, OnWeatherListener
{
    /*Presenter作为中间层，持有View和Model的引用*/
    private WeatherView weatherView;
    private WeatherModel weatherModel;

    public WeatherPresenterImpl(WeatherView weatherView) {
        this.weatherView = weatherView;
        weatherModel = new WeatherModelImpl();
    }

    @Override
    public void getWeather(String cityNO) {
        weatherView.showLoading();
        weatherModel.loadWeather(cityNO, this);
    }

    @Override
    public void onSuccess(Weather weather) {
        weatherView.hideLoading();
        weatherView.setWeatherInfo(weather);
    }

    @Override
    public void onError() {
        weatherView.hideLoading();
        weatherView.showError();
    }
}
```

#### Model 接口

```java
public interface WeatherModel {
    void loadWeather(String cityNO, OnWeatherListener listener);
}

/**
 * Created by Administrator on 2015/2/6.
 * 天气Model实现
 */
public class WeatherModelImpl implements WeatherModel {
    @Override
    public void loadWeather(String cityNO, final OnWeatherListener listener) {
        /*数据层操作*/
        VolleyRequest.newInstance().newGsonRequest("http://www.weather.com.cn/data/sk/" + cityNO + ".html",
                Weather.class, new Response.Listener<Weather>() {
                    @Override
                    public void onResponse(Weather weather) {
                        if (weather != null) {
                            listener.onSuccess(weather);
                        } else {
                            listener.onError();
                        }
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        listener.onError();
                    }
                });
    }
}
```

### demo的代码流程

1. Activity做了一些UI初始化的东西并需要实例化对应WeatherPresenter的引用和实现WeatherView的接口，监听界面动作，Go按钮按下后即接收到查询天气的事件，在onClick里接收到即通过WeatherPresenter的引用把它交给WeatherPresenter处理。

2. WeatherPresenter接收到了查询天气的逻辑就知道要查询天气了，然后把查询天气的具体业务实现交给WeatherModel去实现同时把WeatherListener即WeatherPresenter自己传给WeatherModel。

3. WeatherModel进行查询天气业务后即把结果通过WeatherListener回调通知WeatherPresenter，WeatherPresenter再把结果返回给View层的Activity，最后Activity显示结果。

## MVVM

MVVM可以算是MVP的升级版，其中的VM是ViewModel的缩写，ViewModel可以理解成是View的数据模型和Presenter的合体，ViewModel和View之间的交互通过Data Binding完成，而Data Binding可以实现双向的交互，这就使得视图和控制层之间的耦合程度进一步降低，关注点分离更为彻底，同时减轻了Activity的压力。

在比较之前，先从图上看看三者的异同。

![image.png](https://upload-images.jianshu.io/upload_images/3947109-fed2f6ba3f1b0d40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

刚开始理解这些概念的时候认为这几种模式虽然都是要将view和model解耦，但是非此即彼，没有关系，一个应用只会用一种模式。后来慢慢发现世界绝对不是只有黑白两面，中间最大的一块其实是灰色地带，同样，这几种模式的边界并非那么明显，可能你在自己的应用中都会用到。实际上也根本没必要去纠结自己到底用的是MVC、MVP还是MVVP，不管黑猫白猫，捉住老鼠就是好猫。

### 演变

MVC -> MVP -> MVVM 这几个软件设计模式是一步步演化发展的，MVVM 是从 MVP 的进一步发展与规范，MVP 隔离了MVC中的 M 与 V 的直接联系后，靠 Presenter 来中转，所以使用 MVP 时 P 是直接调用 View 的接口来实现对视图的操作的，这个 View 接口的东西一般来说是 showData、showLoading等等。M 与 V已经隔离了，方便测试了，但代码还不够优雅简洁，所以 MVVM 就弥补了这些缺陷。在 MVVM 中就出现的 Data Binding 这个概念，意思就是 View 接口的 showData 这些实现方法可以不写了，通过 Binding 来实现。

### 相同点

如果把这三者放在一起比较，先说一下三者的共同点，也就是Model和View：

- Model：数据对象，同时，提供本应用外部对应用程序数据的操作的接口，也可能在数据变化时发出变更通知。Model不依赖于View的实现，只要外部程序调用Model的接口就能够实现对数据的增删改查。

- View：UI层，提供对最终用户的交互操作功能，包括UI展现代码及一些相关的界面逻辑代码。

### 不同点

三者的差异在于如何粘合View和Model，实现用户的交互操作以及变更通知

#### Controller

Controller接收View的操作事件，根据事件不同，或者调用Model的接口进行数据操作，或者进行View的跳转，从而也意味着一个Controller可以对应多个View。Controller对View的实现不太关心，只会被动地接收，Model的数据变更不通过Controller直接通知View，通常View采用观察者模式监听Model的变化。

#### Presenter

Presenter与Controller一样，接收View的命令，对Model进行操作；与Controller不同的是Presenter会反作用于View，Model的变更通知首先被Presenter获得，然后Presenter再去更新View。一个Presenter只对应于一个View。根据Presenter和View对逻辑代码分担的程度不同，这种模式又有两种情况：Passive View和Supervisor Controller。

#### ViewModel

注意这里的“Model”指的是View的Model，跟MVVM中的一个Model不是一回事。所谓View的Model就是包含View的一些数据属性和操作的这么一个东东，这种模式的关键技术就是数据绑定（data binding），View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上。这种模式实际上是框架替应用开发者做了一些工作，开发者只需要较少的代码就能实现比较复杂的交互。

## 参考链接

[練習在 Android 設計上的 MVC, MVP, MVVM 架構](https://windsuzu.github.io/learn-android-architecture-pattern/)

[框架模式 MVC 在Android中的使用](https://blog.csdn.net/feiduclear_up/article/details/46363207)

[Android中的MVP](https://rocko.xyz/2015/02/06/Android%E4%B8%AD%E7%9A%84MVP/)

[Android App的设计架构：MVC,MVP,MVVM与架构经验谈](https://www.tianmaying.com/tutorial/AndroidMVC)

[学习Android Data Binding](https://windsuzu.github.io/learn-android-databinding/)
