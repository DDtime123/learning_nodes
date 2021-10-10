### Android学习第二天

#### 1. Android日志工具

Android中的日志工具类是Log（android.util.Log），这个类提供了以下5个方法来打印日志：

* Log.v()：打印那些最为琐碎，意义最小的日志信息，对应的级别是verbose，在Android日志里级别最低。
* Log.d()：打印一些调试信息，这些信息对调试应用程序有帮助，对应级别是debug，比verbose高一级。
* Log.i()：打印一些比较重要的数据，这些数据是你想要看到的，有利于分析用户行为的数据，对应级别是info。
* Log.w()：打印程序中的警告信息，提示程序在这个地方有潜在的风险，需要最好去修复一下，对应级别是warn。
* Log.e()：打印程序中的错误信息，比如程序进入了catch语句中，一般代表程序出现了严重错误，必须尽快修复，对应级别是error。

为什么要使用Log而不是System.out？

System.out的日志打印不可控制，打印时间无法确定，不能添加过滤器，日志没有级别划分。

#### 2. Activity活动

##### 2.1 活动是什么？

活动（Activity）是一种可以包含用户界面的组件，主要用于程序与用户进行交互，一个应用程序可以包含零个或多个活动。

##### 2.2 如何使用活动？

###### 2.2 1 手动创建活动

首先新建一个不带Activity的project

![1632314255436](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632314255436.png)

点击左上角Android，将项目目录结构切换至Project结构

![1632314383952](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632314383952.png)

这时我们可以发现app/src/main/java/com.example.activitytest目录是空的，这是当然，因为并没有创建任何的Activity。

![1632314652549](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632314652549.png)

右击com.example.activitytest -> New -> Activity -> Empty -> Activity，在对话框中填入新的Activity的信息。取消选择Generate Layout File复选框，为了更好地学习，这个文件要在后边手动创建。

新建的Activity如下：

~~~java
public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
~~~

项目中任何活动都应该重写onCreate方法，基本的onCreate方法需要调用父类的onCreate方法。

###### 2.2.2 创建和加载布局

Android的设计讲究程序逻辑和视图分离，并且最好一个活动对应一个布局。现在来手动创建一个视图文件。

右击app/src/main/res -> New -> Directory，新建一个名为layout的目录。再右击layout目录 -> New -> Layout resource file，新建一个名为first_layout的布局文件。

![1632316636227](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632316636227.png)

这是Android Studio提供的可视化编辑器，可以使用可视化的方式编辑布局文件，窗口下方有两个选项卡Design和Text，点击Text，可以看到布局文件的代码：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

</LinearLayout>
~~~

可以看见布局文件以LinearLayout作为根元素，我们可以在其中添加一个Button

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 1"/>
</LinearLayout>
~~~

可以在Preview轻布局编辑器中预览布局

![1632317286269](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632317286269.png)

Button中有这样一句：

~~~xml
@+id/button_1
~~~

这条语句的意思是在xml中新定义一个id，id的变量名为button_1，与它相似的

~~~xml
@id/button
~~~

这条语句的意思是在xml中引用一个id

在@后面使用 “+”，系统会在R.java文件中新建一个相应的整型变量，但是如果R.java里已经有同名变量，则不在生成新的变量而是使用该同名变量的值作为标识。



在生成了布局文件后，需要将布局文件加载到活动之上，这时要使用setContentView方法。

在项目中添加的任何资源都会在 R.java 文件中生成一个独一无二的资源ID，通过这个ID可以将指定的布局文件与Activity绑定在一起。

~~~java
// FirstActivity.java
public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // setContentView 通过接受 first_layput布局文件的资源ID绑定在一起
        setContentView(R.layout.first_layout);
    }
}
~~~

###### 2.2.3 在AndroidManifest文件中注册活动，配置主活动

所有活动要在AndroidManifest.xml中注册才能生效，事实上Android Studio已经帮我们在AndroidManifest.xml中注册过了：

~~~xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FirstActivity"></activity>
    </application>
~~~

然而，即使是以及注册了活动，程序此时还是不能运行的，因为主活动还没有配置完成，当程序启动时，不知道应该以哪个活动作为入口。

注册主活动的方式是，在 activity 标签的内部加入 intent-filter 标签，并在标签里添加 <action android:name="android.intent.action.Main"/> 和 <category android:name="android.intent.category.LAUNCHER"/> 这两句声明。

~~~xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FirstActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
~~~

接下来点击运行，程序就可以顺利启动了。

![1632467133408](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632467133408.png)

###### 2.2.4 在活动中使用Toast显示信息

在活动中可以使用Toast显示一些短小的信息，这些信息过一段时间会自动消失，并且不会占用任何屏幕空间。

使用toast首先要定义一个触发点，在这里定义为：当屏幕上的按钮被点击：

~~~java
public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.first_layout);

        Button button_1=(Button) findViewById(R.id.button_1);
        button_1.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                Toast.makeText(FirstActivity.this,"You clicked Button_1",Toast.LENGTH_SHORT).show();
            }

        });
    }
}
~~~

在活动中，可以通过findViewById方法获取布局文件中定义的元素，这里通过 R.id.button_1 获取按钮的引用。

![1632468161006](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632468161006.png)

###### 2.2.6 在活动中使用Menu创建菜单

首先在res下新建名为menu的目录，右击res -> New -> Directory ，命名为menu。

然后右击menu目录，New -> menu ResourceFile，命名为 main。

在main.xml中添加如下代码：

~~~xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"
        android:title="Add"/>
    <item
        android:id="@+id/remove_item"
        android:title="Remove"/>
</menu>
~~~

这里通过 item 标签创建了两个菜单项，android:id 是这两个菜单项的唯一标识符，android:title 则是菜单项的名称。

这时回到FirstActivity里重写onCreateOptionMenu方法。按下快捷键Control + O，再敲出OnCreateO... 可以快速定位到重写方法：

![1632470929497](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632470929497.png)

然后再OnCreateOptionMenu方法中编写如下代码：

~~~java
@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main,menu);
        return true;
    }
~~~

通过getMenuInflater方法可以获得MenuInflater对象，再调用inflate方法就能够给当前活动创建菜单，而参数R.menu.main相当于指定创建菜单所用的资源文件。第二个参数menu是传入OnCreateOptionMenu方法的参数，用于指定菜单项将添加到哪一个Menu对象中。然后将这个方法返回true，表示允许将创建的菜单展现出来，如果返回false，创建的菜单将无法显示。

但是，当前展现出来的菜单仅仅是虚有其表，并无实际功能，要让它拥有真正的菜单功能，还要为菜单项绑定响应事件。

在FirstActivity中重写onOptionsItemSelected方法，往方法中添加如下代码：

~~~java
@Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch(item.getItemId()){
            case R.id.add_item:
                Toast.makeText(this,"You click Add",Toast.LENGTH_SHORT).show();
                break;
            case R.id.remove_item:
                Toast.makeText(this,"You click Remove",Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }
~~~

在onOptionsItemSelected方法中通过getItemId方法判断被点击的菜单项，然后可以给每一个菜单项添加自己的逻辑处理。

在运行程序之后，可以看见右上角的三点，表示菜单入口：

![1632472894862](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632472894862.png)

点击后展开菜单：

![1632472924632](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632472924632.png)

点击菜单项后触发对应的事件：

![1632473565955](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632473565955.png)

###### 2.2 7 活动的销毁

既然已经掌握了手动创建一个活动的方法，那么也要掌握手动销毁活动的方法。一般情况下，在手机上按下 back 键后程序会自动将当前活动销毁，但是现在我们想要在程序里通过代码来销毁活动，这可以通过在活动中调用由Activity类提供的finish方法来完成：

在main.xml菜单布局文件中添加菜单项：

~~~xml
<item
        android:id="@+id/exit_item"
        android:title="Exit"/>
~~~

然后在FirstActivity中的onOptionsItemSelected方法中添加对应菜单项的逻辑处理：

~~~java
case R.id.exit_item:
                finish();
~~~

![1632474587702](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632474587702.png)

在点击Exit菜单项后，由于主活动被销毁，应用程序也就退出了。



#### 3. Intent意图

仅仅创建一个活动，并且它进行交互是个简单的工作，但是为了创建一个更高级的应用程序，创建并利用上更多的活动是十分有必要的。所以本节介绍的是如何在活动之间创建联系，比如从一个活动跳转到另一个活动。这就要用到Android的intent组件。

##### 3.1 intent是什么？

intent是Android程序中组件之间交互的一种方式，它不仅可以指明当前组件想要执行的的行动，也可以在不同组件之间传递数据。

intent的主要作用场景有：启动活动，启动服务以及发送广播。本节主要介绍intent的启动活动方面应用。

intent分为显示intent和隐式intent。

##### 3.2 使用显示intent

首先创建第二个Activity，这次创建一个Empty Activity，可以选上Generate Layout File，并起名为second_activity：

![1632475370559](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632475370559.png)

Android Studio为我们生成了源文件second_activity.java以及布局文件activity_second_activity.xml，但是自动生成的布局文件里的代码对于现在的我们而言太过于复杂，所以手动将activity_second_activity.xml里的代码更改如下：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button_2"/>
    
</LinearLayout>
~~~

新的Activity以及准备好了，接下来进行intent的布置。

Intent有多个构造函数的重载，其中一个是Intent(Context packageContent, Class<?> cls)。第一个参数Context要求提供一个启动活动的上下文，第二个参数Class则是指定想要启动的目标活动。

通过这个构造函数可以创建出Intent的 “意图”。然后通过Activity类提供的startActivity方法，将构建好的Intent传递进startActivity里，就可以启动活动。

修改FirstActivity里的按钮的点击事件如下：

~~~java
@Override
            public void onClick(View v){
                startActivity(new Intent(
                   FirstActivity.this,
                        second_activity.class
                ));
            }
~~~

此时点击主Activity中的按钮，就会跳转到second_activity中。

![1632482373604](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632482373604.png)

当然，也可以通过setClassName方法传递包名+类名的字符串的形式指定目标活动，这种方式在应用程序内部进行Activity跳转时常用到：

~~~java
			public void onClick(View v){
                startActivity(new Intent().setClassName(FirstActivity.this,
                        "com.example.activitytest.second_activity"));
            }
~~~



当然，按下back键就可以退回前一个Activity，并且销毁当前Activity。

##### 3.2 使用隐式intent

显示intent的 “意图” 十分明显，将当前组件想要执行的行动以及目标Activity都明确指明了出来并且作为参数传递进intent内。

相比起显示intent，隐式intent的 “意图” 则含蓄了许多。它并不明确指出想要启动的活动，而是指定了一系列更为抽象的 action 和category （手动创建Activity的时候，为了指明主Activity而在AndroidManifest中手写过）等信息，然后交由系统去分析这个intent，并帮助我们找到合适的活动去启动。

如何给Activity配置对应的过滤条件？

打开AndroidManifest.xml，在 <activity> 标签内部添加 intent-filter（意图过滤器），就能够添加 action 和 category 等过滤条件。

在second_activity的标签内添加如下代码：

~~~xml
<activity android:name=".second_activity">
            <intent-filter>
                <action android:name="com.example.activitytest.ACTION_START"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
 </activity>
~~~

在 action 标签中我们指明了活动可以响应com.example.activitytest.ACTION_START这个action，而 category 标签则添加了一些附加信息，只有 action 和 category 中的信息全都匹配上 intent 中指定的条件时，这个活动才能够响应该intent。

这时，调用 intent 的第二种构造函数，仅仅将过滤条件传递进构造器内，将FirstActivity中 Intent 的声明改变如下：

~~~java
@Override
            public void onClick(View v){
                startActivity(new Intent(
                   "com.example.activitytest.ACTION_START"
                ));
            }
~~~

该构造函数仅仅接收字符串形式的 action 条件，但是既然之前说要 action 和 category 全部匹配上活动才能响应intent，为什么在second_activity 中声明的android.intent.category.DEFAULT" category没有被加入这个 intent 的过滤条件中？

这是因为android.intent.category.DEFAULT"是一个默认的category，任何activity都会默认地加上这个category。

每个Intent中只能指定一个 action ，但却可以指定多个 category ，这里我们给 intent 加上 category 过滤条件，但前提是在至少一个 activity 的配置中添加该category，否则会因为无法找到对应的 activity 而无法通过编译。

~~~xml
<!-- 修改过后的second_activity配置 -->
<activity android:name=".second_activity">
            <intent-filter>
                <action android:name="com.example.activitytest.ACTION_START"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.MY_CATEGORY"/>
            </intent-filter>
        </activity>
~~~

在FirstActivity修改intent代码，添加category：

~~~java
// 修改
@Override
            public void onClick(View v){
                startActivity(new Intent(
                   "com.example.activitytest.ACTION_START"
                ).addCategory("android.intent.category.MY_CATEGORY"));
            }
~~~

通过隐式intent，我们不仅可以启动自身程序内的活动，还可以启动其它程序内的活动，这就可以实现程序之间的功能共享，比如说在视频app上想要分享视频内容，就可以通过分享功能调用社交软件，再比如点击网页链接调用起浏览器。

在 first_layout.xml 上添加一个按钮：

~~~xml
<Button
        android:id="@+id/app_call"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Call Other App"/>
~~~

在FirstActivity里对这个新添加的按钮添加对点击的响应事件：

~~~java
Button app_call=(Button)findViewById(R.id.app_call);
        app_call.setOnClickListener(new View.OnClickListener(){
           @Override
           public void onClick(View v){
               startActivity(new Intent(Intent.ACTION_VIEW)
                       .setData(Uri.parse("http://www.baidu.com")));
           }
        });
~~~

点击CALL OTHER APP按钮后会调用起能够接受Uri类型的data的的软件：

![1632491688053](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632491688053.png)

setData方法接受一个Uri对象，用于指定当前intent所操作的数据，这些数据通常以字符串的形式传递到Uri的parse方法里。

因为上图中众多程序的activity能够接收Uri类型的data，所以才会在调用其他activity时让我们作出选择。那么如何让一个activity能够接收某种类型的data呢？

要让activity能够接收某种类型的data，就要在AndroidManifest.xml里修改 activity 的配置，在 intent-filter 中添加 data 标签以指定该活动能接收何种 data ，data 标签中主要可以配置以下内容：

* android:scheme：用于指定数据的协议部分，如上述的http部分。
* android:host：用于指定数据的主机名部分，如上例中的www.baidu.com 部分
* android:port：用于指定数据的端口部分，如一段网址中跟在域名之后的内容，
* android:mimeType：用于指定可以处理的数据类型，允许使用通配符的方式进行指定。

只有当 data 标签中指定的内容和intent中携带的Data完全一致时，activity才能响应该intent。不过一般 data 标签中不会指定过多内容，如上面浏览器示例中，只要指定 android:scheme 为 http，就可以响应所有http协议的intent。

现在我们来制作一个能够响应打开网页intent的activity。右击 com.example.activitytest -> New -> Activity -> Empty Activity，将新的Activity命名为ThirdActivity。

由于默认生成的activity_third.xml布局文件过于复杂，将其代码修改为如下：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button_3"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button_3"/>

</LinearLayout>
~~~

同时在 AndroidManifest.xml 中给ThirdActivity配置 intent-filter 中的 Data 标签，给它指定对 http 的响应。

~~~xml
<intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:scheme="http"/>
            </intent-filter>
~~~

其中 "android.intent.action.VIEW" 是android应用开发中常用的原生动作意图之一。 "android.intent.category.DEFAULT"  则是任意活动都默认拥有的 categoty。

重新运行Android程序，点击 CALL OTHER APP。可以看见，ThirdActivity也被加入候选APP列表中。

![1632531819413](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632531819413.png)

除了http协议以外，我们还可以指定其他很多协议，比如geo表示显示地理位置，tel表示拨打电话。

下面的代码展示是使用tel协议调用系统拨号界面：

在 first_layout 布局文件上添加新的button：

~~~xml
<Button
        android:id="@+id/phone_call"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PHONE CALL"/>
~~~

在FirstActivity中添加对新按钮的点击事件的响应：

~~~java
Button phone_call=(Button)findViewById(R.id.phone_call);
        phone_call.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(Intent.ACTION_DIAL)
                        .setData(Uri.parse("tel:10086")));
            }
        });
~~~

该intent首先指定了动作意图是 ACTION_DIAL ，这是一个Android的原生动作意图，然后在data部分指定协议是tel，号码是10086。运行程序：

![1632533940702](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632533940702.png)



点击PHONE CALL按钮，会调用拨号程序并且指定号码为10086：

![1632533969568](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632533969568.png)

注意这里指定的动作意图ACTION_DIAL是为了在打开拨号程序后，让拨号程序停留在输入号码界面而没有立即拨出号码。而如果指定动作意图为ACTION_CALL，则点击PHONE CALL按钮后会立即拨出号码。

##### 3.3 使用intent向下一个活动传递数据

刚才已经尝试过使用intent启动另一个活动的操作，然而intent还可以用来在启动活动的时候传递数据。

在启动活动的时候传递数据的思路很简单，intent提供了一系列的putExtra方法的重载，可以将我们想要传递的数据暂存在intent里面，等到传递到下一个活动后，再将数据从intent里取出来就可以了。

比如说想要将一个字符串从FirstActivity传递到second_activity中，修改FirstActivity中 Button_1 的响应逻辑：

~~~java
button_1.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                String data="Hello SecondActivity";
                startActivity(new Intent().setClassName(FirstActivity.this,
                        "com.example.activitytest.second_activity").putExtra("extra_data",data));
            }

        });
~~~

然后使用getIntent获取用于启动second_activity的intent，并使用getStringExtra方法将intent中的数据取出来：

~~~java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second_activity);

        String extra_data=getIntent().getStringExtra("extra_data");
        Toast.makeText(second_activity.this,extra_data,Toast.LENGTH_SHORT).show();
    }
~~~

这里使用的getStringExtra方法，它还有其它类型的变体，例如如果传入的数据是整型类型，就用getIntExtra方法，而如果是布尔类型，就用getBooleanExtra方法。

运行程序后，点击Send Msg to 2rd Activity按钮，可以second_activity处接收到数据并且显示出来：

![1632536903522](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632536903522.png)

##### 3.4 返回数据给上一个活动

既然能够传递数据给下一个活动，当然也能由下一个活动返回数据给启动它的上一个活动。但是返回上一个活动仅仅需要按下back就可以了，并没有一个可以用于启动活动的Intent来传递数据，这时就要在上一个活动调用下一个活动所用到的启动方法上做出改变。

将之前所使用的startActivity改为startActivityForResult。

首先在FirstActivity中添加新的按钮：

~~~xml
<Button
        android:id="@+id/start_for_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Start For Result"/>
~~~

新建一个forth_activity，按照之前的方法修改掉自动生成的布局文件。

然后在FirstActivity中创建活动forth_activity的启动事件：

~~~java
Button start_for_result=(Button)findViewById(R.id.start_for_result);
        start_for_result.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivityForResult(new Intent(FirstActivity.this,forth_activity.class),1);
            }
        });
~~~

观察上方的代码，startActivityForResult这个活动启动方法接受两个参数，第一个是intent，第二个参数是请求码，请求码用于在之后的回调中判断数据的来源（当创建出多个intent用于启动多个活动时时，判断从返回的数据来自哪一个下一级活动）。

在forth_activity的布局文件中新建一个按钮button_4。

接下来在forth_activity中设定好返回的数据：

~~~java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_forth_activity);

        Button button_4=(Button)findViewById(R.id.button_4);
        button_4.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                setResult(RESULT_OK,new Intent().putExtra("data_return","Hello FirstActivity"));
                finish();
            }
        });
    }
~~~

以上代码设定了forth_activity中按钮的点击逻辑事件：通过构建一个intent存放将要返回的数据，然后调用setResult方法用于向上一个活动返回数据，最后调用finish方法销毁activity。

setResult方法接收两个参数，第一个参数用于向上一个活动返回处理结果，一般只用RESULT_OK或RESULT_CANCELED两个值，第二个参数则把带有数据的intent传递回去。

接下来在上一个活动中获取从下一个活动传回来的数据，这要重写Activity的onActivityResult方法：

~~~java
@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        switch(requestCode){
            case 1:
                if(resultCode==RESULT_OK){
                    Toast.makeText(FirstActivity.this,data.getStringExtra("data_return"),Toast.LENGTH_SHORT).show();
                }
                break;
            default:
        }
    }
~~~

onActivityResult方法带有三个参数，第一个参数是由上一个活动启动下一个活动时传递进去的请求码（startActivityForResult的第二个参数），第二个参数是由下一个活动传递会去上一个活动时传递回去的结果码（一般为RESULT_OK或RESULT_CANCELED），第三个参数是在下一个活动内部创建的存储有待返回的数据的intent。

运行程序，点击START FOR RESULT按钮，启动forth_activity，再点击forth_activity中的BUTTON_4，就可以将数据传回到FirstActivity中：

![1632552753145](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632552753145.png)

![1632552734868](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632552734868.png)



![1632552710443](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632552710443.png)

如果我们想要在按下back退出activity时给上一个活动传输数据，那该怎么办呢？

这时我们应该重写 forth_activity 的 onBackPressed 方法。onBackPressed 是当 back 键被按下时程序默认调用的方法，重写的程序体与上文构建的 Button_4 的 onClick 方法的程序体相同：

~~~java
@Override
    public void onBackPressed() {
        setResult(RESULT_OK,new Intent().putExtra("data_return","Hello FirstActivity"));
        finish();
    }
~~~



#### 4. 活动的生命周期

##### 4.1 返回栈

Android的活动是可以不断创建的。每创建出一个新的活动，该活动就会叠加在启动它的活动之上，每按一次back键，就会销毁最上层的活动，下面一层的活动就会被重新显示出来。

这样的程序运行方式跟使用栈存储活动的活动管理机制有关。新的活动从栈顶加入，想要执行栈顶以下的活动，只能先从栈顶开始，一层一层往下销毁目标活动上层的活动。

##### 4.2 活动状态

活动在它的生命周期内最多有4种状态：

* 运行状态

当一个活动位于返回栈的栈顶时，该活动处于活动状态，系统最不愿意主动回收位于栈顶的程序。

* 暂停状态

当一个活动不位于栈顶，但任然可见时，活动进入暂停状态（有的活动以对话框形式出现在屏幕上，这时活动不位于栈顶，但也有部分可见）。处于暂停状态的活动是存活着的，系统也不愿意去回收这种状态的程序，但是在内存极低的状态下，系统还是会回收这种程序。

* 停止状态

当一个活动不位于栈顶，且完全不可见时，该活动进入停止状态，当其它地方需要内存时，系统会回收这种程序。

* 销毁状态

当活动从返回栈中被销毁时，活动进入销毁状态，系统会马上想要回收这种程序。

##### 4.3 活动的生存期

Activity中定义了7个回调方法，这些方法覆盖了活动生命周期的每一环：

* onCreate：在活动第一次创建时被调用。一般在该方法里我们设置布局以及给组件绑定事件。
* onStart：这个方法在活动由不可见变得可见时被调用。
* onResume：这个方法在栈顶元素变化，活动由栈中到达栈顶时被调用。
* onPause：这个方法在有新活动加入栈，使得当前活动由栈顶被压入栈内部的时候，或者恢复某个活动的时候被调用。
* onStop：这个方法在活动由可见变得完全不可见时被调用。
* onDestroy：这个方法在活动被销毁的时候被调用。
* onRestart：这个方法在活动由停止状态变为运行状态之前调用。

以上7个方法中除了 onRestart 方法以外，其它方法都是两两相对的，根据这个将活动分为3种生存期：

* 完整生存期：活动在 onCreate 和 onDestroy 期间叫做完整生存期。
* 可见生存期：活动在 onStart 和 onStop 期间叫做可见生存期。

在这期间活动对于用户总是可见的，但是也许活动无法与用户进行交互。

* 前台生存期：活动在 onResume 和 onPause 期间叫做前台生存期。

在这期间活动不仅对用户可见，还能够与用户进行交互。

Android官方提供活动的生命周期图：

![1632559069133](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632559069133.png)

##### 4.4 实际体验活动的生命周期

首先准备一个新的项目，命名为AndroidLifeCycleTest。然后在项目里新建两个activity，命名为 NormalActivity 和 DialogActivity。

修改两个新建的activity的布局文件如下：

activity_normal.xml：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="This is a normal activity"/>

</LinearLayout>
~~~

activity_dialog.xml：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="This is a dialog activity"/>

</LinearLayout>
~~~

两者的代码没有本质不同，仅仅是显示的文字不一样。

现在要让将 DialogActivity 设置为对话框的样式，这种 Activity 的全局样式设置要在 AndroidManifest.xml 文件中设置。

修改DialogActivity的配置代码如下：

~~~xml
<activity android:name=".DialogActivity"
                android:theme="@style/Theme.AppCompat.Dialog">
        </activity>
~~~

android:theme 属性设置活动的主题样式，除了上述的 Dialog 以外，还有很多其它样式可选择。

接下来在主活动的布局 activity_main.xml 中添加两个按钮，分别用于启动两个子活动：

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button android:id="@+id/start_normal_activity"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="START NORMAL ACTIVITY"/>

    <Button android:id="@+id/start_dialog_activity"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="START DIALOG ACTIVITY"/>

</LinearLayout>
~~~

接下来在MainActivity里添加两个按钮的点击事件，让它们绑定两个活动的启动。为了看见活动的生存周期，我们还重写 MainActivity 的其余6个回调函数，修改 MainActivity.java 如下：

~~~java
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Log.d(TAG, "onCreate");

        Button start_normal_activity=(Button)findViewById(R.id.start_normal_activity);
        start_normal_activity.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this,NormalActivity.class));
            }
        });

        Button start_dialog_activity=(Button)findViewById(R.id.start_dialog_activity);
        start_dialog_activity.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this,DialogActivity.class));
            }
        });


    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop");
    }
}
~~~

> 这里有快捷创建日志语句的办法：在 onCreate 方法外按下 logt ，再按Tab键，将会创建一条任何 log 打印语句都需要的 TAG 字符串，默认是当前Activity的名称。而在方法内部按下 logd ，再按下Tab后将创建一条 Log.d 打印语句，同理 logw 快捷键将创建 Log.w 打印语句。

运行程序，打开 Logcat ，我们会发现 Logcat 的无关消息太多，看不过来，这时就要设置消息的过滤条件。

将日志过滤器级别设置为 Debug ，设置消息范文为当前项目，再在搜索框里搜索主活动 MainActivity ，观察输出的信息：

![1632562533068](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632562533068.png)

***

程序启动时：

![1632563466908](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563466908.png)

可以看到，程序第一次启动时会调用 onCreate，onStart 和 onResume 方法。

***

然后点击 START NORMAL ACTIVITY 按钮：

![1632563550491](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563550491.png)

可以看到，当要启动下一个活动时，由于当前活动完全被下一个活动遮挡住，处于用户完全不可见状态，当前活动会先进入暂停状态，再进入停止状态。

***

按下back，退回上一个活动：

![1632563589947](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563589947.png)

当活动被恢复时，由于之前MainActivity进入了 Stop 停止状态，会先调用 onRestart，再调用 onStart，最后调用 onResume 方法。

***

然后点击 START DIALOG ACTIVITY 按钮，弹出一个窗口：

![1632568771721](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632568771721.png)

![1632563626868](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563626868.png)

可以看到，弹出一个窗口，当前活动仅仅调用 onPause 方法，进入暂停状态，这是因为MainActivity还处于部分消息可见状态，虽然不能与用户进行交互，但还不能停止。

***

再点击back，退出 dialog：

![1632563688484](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563688484.png)

跟 onPause 方法对应的就是 onResume，所以从暂停状态中恢复过来会调用 onResume 方法。

***

在主活动处点击back，退出应用：

![1632563733543](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632563733543.png)

销毁主活动的过程依次会执行 onPause，onStop，onDestroy方法。

##### 4.5 活动被回收了怎么办？

假设有以下场景：应用中有一个A活动，在A活动基础上启动了B活动，但是由于系统内存不足，将A活动回收了，这时候按下back，返回A活动，这时候会怎么样？这时候A活动不会执行 onRestart方法，而是会执行 onCreate 方法，重新创建一个A活动。

当A活动中原本有着临时数据和状态时，A活动被回收后，这些数据和状态将会丢失。这是十分影响用户体验的一种情况。

面对这种问题，Activity提供了一个保存临时数据用的方法，这个方法是 onSaveInstanceState ，在活动被回收之前一定会被调用。

onSaveInstanceState方法带有一个 Bundle 类型的参数，Bundle提供了一系列方法用以保存数据，比如通过 putString 方法保存字符串，putInt 方法保存整形数，以此类推。每个保存方法接受两个参数，第一个参数是用作键的字符串，作为将数据重新提取出来所需的钥匙，第二个参数是要保存的数据。

在MainActivity中重写 onSaveInstanceState 方法：

```java
@Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putString("Saved_String_MainActivity","Saved Data From MainActivity");
    }
```

接下来要恢复数据，我们需要在活动被创建时在 onCreate 方法中恢复数据，还要注意这些数据应该在它们所对应的变量被创建后（一般为在设置了对应的布局之后），被使用之前被恢复，所以一般 setContentView 方法调用之后马上恢复数据。onCreate 方法修改如下：

~~~java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        if(savedInstanceState!=null){
            Log.d(TAG, "Recovered Data: "+savedInstanceState.getString("Saved_String_MainActivity"));
        }
        
        ...
    }
~~~



##### 4.6 手机屏幕旋转时活动状态的变化

当手机屏幕旋转时，当前最上层的活动会被销毁（onPause -> onStop -> onDestroy），然后调用恢复数据的方法 onSaveInstanceState  进行活动临时数据的恢复，这个过程和活动被回收时有些相似，然后再启动一个新的活动实例（onStart），再恢复数据（onResume）：

![1633105983244](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633105983244.png)

 当手机屏幕旋转时，如果此时返回栈内某个活动处于完全不可见状态，则该活动状态不会发生任何变化。但是，如果此时该活动虽然并不在最上层，但任然有部分可见时，它会发生与在最上层是被旋转时十分相似的变化，除了因为本来就处于暂停状态而无需再暂停一次以外，其余行为皆相同：

![1633106590031](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633106590031.png)

![1633106452196](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633106452196.png)



#### 5. 活动的4种启动模式

Android的活动一共有4种不同的启动模式，每种启动模式适应不同需求的活动。4种模式分别为：

* standard
* singleTop
* singleTask
* singleInstance

standard启动模式：每个活动默认的启动模式。standard模式下，每当启动一个活动，它就会新建一个活动的实例，然后将活动从栈顶加入返回栈，而不会管栈中是否已经有该活动的实例。

下面以standard启动方式做出一个实例，在A活动之上启动A活动，并查看两个活动的内存情况。

在MainActivity的布局文件 activity_main.xml 中添加一个Button：

~~~xml
<Button
        android:id="@+id/standard_style"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="START ON STANDARD STYLE"/>
~~~

在MainActivity中建立这个按钮的点击相应事件：当按钮被点击时，新启动一个MainActivity活动，使用 toString 方法在 onCreate 方法中打印活动实例的唯一字符串：

~~~java
		Log.d(TAG, this.toString());
		Button standard_style=(Button)findViewById(R.id.standard_style);
        standard_style.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(MainActivity.this,MainActivity.class));
            }
        });
~~~

运行结果：

![1632575154144](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1632575154144.png)

可以看见在standard启动方式下每新启动一次activity就会创建出一个activity的实例。

***

singleTop启动模式：

有的时候，我们会觉得 standard 启动模式不合理。这时候我们可以根据需要改变启动模式，比如说 singleTop 启动模式。在该启动模式下，当应用已经在最上层时，再要求启动新的该活动实例时不会启动新的活动实例。

将 NormalActivity 的启动模式设置为 singleTop：

~~~xml
<!-- AndroidMainfest.xml -->
<activity android:name=".NormalActivity" 
                android:launchMode="singleTop"/>
~~~

再在 NormalActivity 上设置一个启动它自身的按钮：

~~~xml
<!-- activity_normal.xml -->
...
<LinearLayout ...>
<Button
        android:id="@+id/singleTop_style"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="START ON SINGLETOP STYLE"/>
</LinearLayout>	
...

~~~

~~~java
// NormalActivity.java
public class NormalActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_normal);

        Button singleTop_style=(Button)findViewById(R.id.singleTop_style);
        singleTop_style.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(NormalActivity.this,NormalActivity.class));
            }
        });
    }
}
~~~

然后再重写掉 NormalActivity 的其余6个状态方法，并且加上日志输出：

~~~java
// NormalActivity.java
@Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop");
    }
~~~

当以 singleTop 模式启动的 NormalActivity 在最上层试图再次启动一个自身的实例时，NormalActivity 只会先暂停，在恢复：

![1633104943129](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633104943129.png)

***

singleTask 启动模式：

使用 singleTop 启动模式可以避免活动在已经在最上层时再次创建自身的实例，而有的时候我们想要在任何时候，在整个应用程序的上下文里只有一个实例、这时候要使用 singleTask 启动模式。

将 NormalActivity 中的按钮修改为启动 MainActivity，将 MainActivity 的启动模式设置为 singleTask：

~~~xml
<!-- AndroidMainfest.xml -->
<activity android:name=".MainActivity"
            android:launchMode="singleTask">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
~~~

~~~java
// NormalActivity.java
...
Button singleTask_style=(Button)findViewById(R.id.singleTask_style);
        singleTask_style.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(NormalActivity.this,MainActivity.class));
            }
        });
...
~~~

在 singleTask 模式下，由其它活动启动 MainActivity 仅仅会让 它的唯一的活动实例重启，再启动，再恢复：

![1633105553504](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633105553504.png)

***

singleInstance 启动模式：





