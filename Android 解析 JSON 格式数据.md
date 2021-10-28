# Android 解析 JSON 格式数据

### 1. OkHttp 库

### 1.1 添加依赖包

首先往 build.gradle 中添加如下依赖包：

~~~xml
dependencies {
    implementation 'com.squareup.okhttp3:okhttp:4.1.0'
}
~~~

#### 1.2 发送 GET 请求

第一步，在 Java 文件中引入 OkHttpClient 的实例了：

~~~java
OkHttpClient client = new OkHttpClient();
~~~

第二步，发起 Http 请求，需要一个 Request 对象，当然，使用的是 OkHttp 中的请求对象：

~~~java
import okhttp3.Request;
...
Request request = new Request.Builder().build();
~~~

以上的 build 方法仅仅创建了一个空的请求对象，我们还可以在 build 方法之后连接一些其他方法给它加上url等：

~~~java
Request request = new Request.Builder()
        .url("http://www.baidu.com")
        .build();
~~~

第三步，调用 OkHttpClient 的 newCall() 方法来创建一个 Call 对象，并调用它的 execute 方法来发送请求并获取服务器返回的数据：

~~~java
Response response = client.newCall(request).execute();
~~~

第四步，获取 response 中的内容，其中 body 为响应正文

```java
// 获取 response 中的内容，其中 body 为响应正文
String responseData = response.body().string();
```

#### 1.3 发送 POST 请求

以上是发送一条 GET 请求的步骤，下面要发送一条 POST 请求：

POST 请求通常需要将表单发送出去，表单中的数据以键值对的形式保存，在 HTML 表单中， id 为键，value 为值，然后再构建成 JSON 格式的请求正文。

在 OkHttpClient 中也是类似，首先要构建一个 RequestBody 对象，这是请求正文：

~~~java
RequestBody requestBody = new FormBody.Builder()
        .add("username", "admin")
        .add("password", "123456")
        .build();
~~~

第二步，在 Request.Builder 中调用一下 post 方法，并将请求正文传入：

~~~java
Request request = new Request.Builder()
        .url("http://www.baidu.com")
        .post(requestBody)
        .build();
~~~

第三步，第四步就和上面的构建 GET 请求一样了。



### 2. 将响应正文转化为 JSON 格式

 接下来将响应正文解析成 JSON 格式数据，并提取数据：

~~~java
private void parseJSONWithJSONObject(String jsonData) {
        try {
            JSONArray jsonArray = new JSONArray(jsonData);
            for (int i = 0; i < jsonArray.length(); i++) {
                JSONObject jsonObject = jsonArray.getJSONObject(i);
                String city = jsonObject.getString("city");
                Log.d(TAG, "city is " + city);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
~~~

#### 2.1 分析 JSON 格式

~~~json

~~~



### 3. 后台自动更新天气（Android 多线程）

#### 3.1 在子线程中更新 UI

Android 不允许在子线程中进行 UI 操作，但是有时候，我们要进行一些耗时操作，比如 IO 或网络操作，这就要用到子线程。然后根据子线程结果来更新 UI。

 Android 提供一套异步消息处理机制。这解决了在子线程中进行 UI 操作的。

第一步，创建静态的标志变量，该变量可以在主线程和子线程中使用，它只有一份内存：

~~~java
public static final int UPDATE_TEXT = 1;
~~~

第二步，在主线程中创建消息回调的处理函数，子线程结束后会调用该函数：

~~~java
// 消息回调函数
private Handler handler = new Handler(){
        public void handleMessage(Message msg){
            switch (msg.what){
                case UPDATE_TEXT:
                    // 可以在此处进行 UI 操作
                    responseContent.setText("msg.what: "+msg.what);
                    break;
                default:
                    break;
            }
        }
    };
~~~

第三步，在子线程中调用消息回调函数：

~~~java
// 进行消息回调，将 JSON 文本传递会主线程
Message msg = new Message();
msg.what = UPDATE_TEXT;
handler.sendMessage(msg);
~~~

第四步，在子线程中将数据传入 Message 中，并且传回主线程。

这里使用 Message 的 setData 方法，该方法接受一个 Bundle （捆绑）对象，该对象的使用方法与键值对相似：

~~~java
// 创建 Bundle ，子线程到父线程的数据载体
// bundle.putString(key,value);
Bundle bundle = new Bundle();
bundle.putString("city",city);
bundle.putString("wea",wea);
bundle.putString("wea_img",wea_img);
bundle.putString("tem",tem);
bundle.putString("hour",hour);
...
// Bundle 传入 Message 中
Message msg = new Message();
msg.setData(bundle);
...
// 从主线程的消息回调函数中中取出 message 中的 bundle
// msg.getData().getString(Key)
private Handler handler = new Handler(){
    public void handleMessage(Message msg){
        switch (msg.what){
            case UPDATE_TEXT:
                // 可以在此处进行 UI 操作
                responseContent.setText("msg.city: "+msg.getData().getString("city"));
                break;
            default:
                break;
        }
    }
};
~~~



#### 3.2 创建定时任务，从子线程定时回传数据

Android 中的定时任务一般有两种实现方式，一种是使用 Java API 中的 Timer 类，另一种是使用 Android 中的 Alarm 机制。

Timer 不大适用于需要长期在后台运行的定时任务，因为 Android 手机为了保护电池，会有自己的休眠机制，当 Android 使 CPU 进入睡眠状态，Timer 中的定时任务就可能无法正常使用。而 Alarm 具有唤醒 CPU 的功能，它能保证定时任务能够正常工作。

##### 3.2 1 Alarm 机制

第一步，获取一个 AlarmManager 实例：

~~~java
AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
~~~

第二步，使用 AlarmManager 的 set 方法设置一个定时任务：

~~~java
long triggerAtTime = SystemClock.elapsedRealtime() +10 *1000; // 定时 10 秒钟
manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pendingIntent);
~~~

第三步，新建一个 Service ，这里 Service 新建一个线程，Service 可以看成没有视图显示的 Activity ，它是一种即使不与用户交互，也可以在后台运行的组件。

~~~java
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    // 开启线程来发起网络请求
    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                OkHttpClient client = new OkHttpClient();
                // 要想发起请求，必须创建请求对象
                Request request = new Request.Builder()
                        .url("https://www.tianqiapi.com/free/day?appid=31225177&appsecret=eqIA7pd6&unescape=1")
                        .build();
                // 调用 OkHttpClient 的 newCall() 方法来创建一个 Call 对象，
                // 并调用它的 execute 方法来发送请求并获取服务器返回的数据
                Response response = client.newCall(request).execute();
                // 获取 response 中的内容，其中 body 为响应正文
                String responseData = response.body().string();
                Log.d(TAG, responseData);

                // 接下来将响应正文解析成 JSON 格式数据
                Bundle bundle = parseJSONWithJSONObject(responseData);
                // 进行消息回调，将 JSON 文本传递会主线程
                Message msg = new Message();
                msg.what = UPDATE_TEXT;
                msg.setData(bundle);
                handler.sendMessage(msg);

            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }).start();
    // 获取一个 AlarmManager 实例
    AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
    // 设置一个定时任务
    int temSec = 10 * 1000; // 10秒钟的毫秒数
    long triggerAtTime = SystemClock.elapsedRealtime() + temSec; // 10秒钟
    Intent i = new Intent(this, LongRunningService.class);
    PendingIntent pi = PendingIntent.getService(this, 0, i, 0);
    manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,triggerAtTime, pi);
    return super.onStartCommand(intent, flags, startId);
}
~~~

第一步，首先在 onStartCommand() 方法中开启了一个子线程，有关时间和 IO 等耗时的操作都应该放在子线程中执行。

第二步，再创建构建消息回调函数，用于在 Service 中获取从子线程中得到的数据：

~~~java
public static final int UPDATE_TEXT = 1;

// 消息回调函数
private Handler handler = new Handler(){
    public void handleMessage(Message msg){
        switch (msg.what){
            case UPDATE_TEXT:
                // 可以在此处进行 UI 操作
                // responseContent.setText("msg.city: "+msg.getData().getString("city"));
                break;
            default:
                break;
        }
    }
};
~~~

第三步，在 Service 中传递数据到外部 Activity，这里通过广播来传送数据。

通过广播来在 Service 和 Activity 之间传输数据是在服务和活动之间传递数据的传统方法：

~~~java
// 接下来将响应正文解析成 JSON 格式数据
Bundle bundle = parseJSONWithJSONObject(responseData);
// 进行消息回调，将 JSON 文本传递会主线程
Message msg = new Message();
msg.what = UPDATE_TEXT;
msg.setData(bundle);
//handler.sendMessage(msg);
sendMessage(msg);
~~~

~~~java
// 在 Service 中使用广播向 Activity 传输数据
public void sendMessage(Message msg){
    Intent intent = new Intent("my-message");
    intent.putExtra("my-msg",msg);
   	LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
}
~~~

~~~java
// 在 Activity 中接收从 Service 传输过来的广播
// 处理从 Service 中获取的数据
@Override
protected void onResume() {
    super.onResume();
    LocalBroadcastManager.getInstance(this)
            .registerReceiver(messageReceiver, new IntentFilter("my-message"));
}

// Handling the received Intents for the "my-integer" event
private BroadcastReceiver messageReceiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        // Extract data included in the Intent
        Message msg =(Message) intent.getParcelableExtra("my-msg");
        String msgshow = msg.getData().getString("city") + msg.getData().getString("wea");
        responseContent.setText("msg: "+msgshow);

    }
};

@Override
protected void onPause() {
    // Unregister since the activity is not visible
    LocalBroadcastManager.getInstance(this).unregisterReceiver(messageReceiver);
    super.onPause();
}
~~~

完成定时任务。

#### 3.3 往 TextView 上添加天气图标

这里使用 TextView.setCompoundDrawablesWithIntrinsicBounds() 方法；这个方法可以往文本的上下左右添加图标在，它接收四个参数，分别代表在文本的左，上，右，下顺时针四个方位能够添加的图标：

~~~java
MyTextView.setCompoundDrawablesWithIntrinsicBounds(null,weather_img,null,null);
~~~

以上往文本的上方添加了一个图片。





