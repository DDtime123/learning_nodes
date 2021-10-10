# Android 应用程序开发

### 1. 与其他应用程序交互

#### 1.1 将用户发送到另一个应用程序

Andorid 的重要功能之一就是应用程序能够根据它想要执行的 “操作” 将用户发送到另一个应用程序。比如，想要在自己的应用上显示地图上的商家地址，那么无需再应用中构建显示地图的活动，而只需要创建一个 Intent ，然后，Android 系统就会启动一个能够在地图上显示地址的应用程序。

这里我们要让能够启动显示地图这项服务的应用程序执行我们的操作（“显示地图”），没有办法指定确切的组件的类名，所以一定要使用隐式意图。

##### 1.1.1 构建隐式意图

意图通常包括与操作相关的数据，例如要查看的地址或是要发送的电子邮件。根据我们要创建的意图，数据的类型可以从 Uri 到其它类型数据不等，又或者不需要任何数据。

下面是传递 Uri 作为数据的 Intent 构造示例，它发起了通过电话号码发送电话呼叫的意图：

~~~java
Uri number = Uri.parse("tel:10086");
Intent callIntent = new Intent(Intent.ACTION_DIAL,number);
~~~

电话呼叫结果：

![1633142458368](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633142458368.png)

查看地图示例：

~~~java
Uri location = Uri.parse("geo:90,90?z=14");
Intent mapIntent = new Intent(Intent.ACTION_VIEW,location);
~~~

查看地图结果：

![1633142712290](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633142712290.png)

![1633143013009](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633143013009.png)

查看网页示例：

~~~java
Uri webpage = Uri.parse("https://www.android.com");
Intent webIntent = new Intent(Intent.ACTION_VIEW,webpage);
~~~

发送短信示例：

~~~java
startActivity(new Intent(Intent.ACTION_SENDTO)
                        .setData(Uri.parse("smsto:10086"))
                        .putExtra("sms_body","The SMS text"));
~~~

发送短信结果：

![1633142346993](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633142346993.png)



为意图添加额外的内容：

一些类型的隐式意图需要提供不同数据类型的额外数据，比如字符串。我们可以通过 putExtra() 方法添加一个或多个额外数据。

默认情况下，系统会根据 Uri 包含的数据来确定意图所需的 MIME 类型，如果意图中没有包含 Uri ，呢通常使用 setType() 来指定与意图关联的数据类型。

以下是一些通过添加额外数据来指定所需操作的意图的示例：

发送带有附件的电子邮件：

~~~java
Intent emailIntent = new Intent(Intent.ACTION_SEND);
// 这个意图没有指定 Uri，所以我们要声明它的 MIME 类型为 "text/plain"
emailIntent.setType("text/plain");
emailIntent.putExtra(Intent.EXTRA_EMAIL,new String[] {"jan@example.com"});
emailIntent.putExtra(Intent.EXTRA_SUBJECT,"Email subject");
emailIntent.putExtra(Intent.EXTRA_TEXT,"Email message text");
emailIntent.putExtra(Intent.EXTRA_STREAM,Uri.parse("content://path/to/email/attachment"));
~~~



##### 1.1.2 使用相机

###### 1.1.2.1 请求相机功能

如果这个应用程序的基本功能之一是拍照，那么我们要声明应用程序依赖于摄像头，在 AndroidMainfest.xml 中 Activity 的 uses-feature 标签中放置一个标签：

~~~xml
<manifest ...>
	<uses-feature android:name="android.hardware.camera"
                  android:required="true"/>
    ...
</manifest>
~~~

Android 调用一个 Intent 描述想要完成的操作的过程包括三个部分：Intent 本身，一个启动外部 Activity 的呼叫，以及一些用于在返回调用活动时用于处理图像数据的代码。

###### 1.1.2.2 使用相机进行拍照

这是一个使用意图来捕获照片的函数：

~~~java
static final int REQUEST_IMAGE_CAPTURE = 1;

private void dispatchTakePictureIntent(){
    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    try{
        startActivityForResult(takePictureIntent,REQUEST_IMAGE_CAPTURE);
    }catch(ActivityNotFoundException e){
        
    }
}
~~~

以下声明应用程序所需权限：

~~~xml
<manifest ...>
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    ...
</manifest>
~~~

我们希望拍照后将照片在手机上显示出来，因此添加一个用于显示图片的组件：

~~~xml
<ImageView
        android:id="@+id/picture"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"/>
~~~

接下来拍照并将图片传输到 ImageView 组件上：

~~~java
Button camera_call=(Button)findViewById(R.id.camera_call);
        camera_call.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //创建File对象，用于存储拍照后的图片
                File outputImage=new File(getExternalCacheDir(),"outputImage.jpg");
                try {
                    if (outputImage.exists()){
                        outputImage.delete();
                    }
                    outputImage.createNewFile();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                if (Build.VERSION.SDK_INT>=24){
                    ImageUri= FileProvider.getUriForFile(second_activity.this,
                            "com.example.fileprovider",outputImage);
                }else {
                    ImageUri=Uri.fromFile(outputImage);
                }
                Log.d(TAG, "Hello");
                //启动相机程序
                Intent intent=new Intent("android.media.action.IMAGE_CAPTURE");
                intent.putExtra(MediaStore.EXTRA_OUTPUT,ImageUri);
                startActivityForResult(intent,TAKE_PHOTO);
            }
        });
~~~

~~~java
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode){
            case TAKE_PHOTO:
                if (resultCode==RESULT_OK){
                    try {
                        //将拍摄的照片显示出来
                        Bitmap bitmap= BitmapFactory.decodeStream(getContentResolver().openInputStream(ImageUri));
                        picture=(ImageView)findViewById(R.id.picture);
                        picture.setImageBitmap(bitmap);
                    } catch (FileNotFoundException e) {
                        e.printStackTrace();
                    }
                }
                break;
            default:
                break;
        }
    }
~~~

我们这里制定了一个 “com.example.android.fileprovider” ，因而要在应用程序中添加一个程序：

~~~xml
<application>
...
   <provider
             android:name="androidx.core.content.FileProvider"
             android:authorities="com.example.android.fileprovider"
             android:exported="false"
             android:grantUriPermissions="true">
       		<meta-data
                       android:name="android.support.FILE_PROVIDER_PATHS"
                       android:resource="@xml/file_paths">
       		</meta-data>
    </provider> 
...
</application>
~~~

然后我们还要在 res/ 目录下新建一个 xml 目录，并且新建一个名为 file_paths 的 xml 文件，添加如下代码：

~~~xml
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path
        name="external"
        path="." />
    <external-files-path
        name="external_files"
        path="." />
    <cache-path
        name="cache"
        path="." />
    <external-cache-path
        name="external_cache"
        path="." />
    <files-path
        name="files"
        path="." />
</paths>
~~~

显示效果：

![1633152090942](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633152090942.png)



##### 1.1.3 构建一个Activity，实现电话拨打，拍照，发短信等功能

设置一个输入框，接收电话和短信的目标号码：

~~~xml
<EditText
        android:id="@+id/call_number"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/phone_number_hint"
        android:inputType="number"/>
~~~

将 EditText 中的字符串作为电话号码拨出或者发送短信：

~~~java
Button phone_call2=(Button)findViewById(R.id.phone_call2);
        phone_call2.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(Intent.ACTION_DIAL)
                        .setData(Uri.parse("tel:"+number.getText().toString())));
            }
        });

        Button send_msg2=(Button)findViewById(R.id.send_msg2);
        send_msg2.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startActivity(new Intent(Intent.ACTION_SENDTO)
                        .setData(Uri.parse("smsto:"+number.getText().toString()))
                        .putExtra("sms_body","The SMS text"));
            }
        });
~~~

最后再加入上述的照相功能：

![1633156236101](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633156236101.png)



##### 1.1 4 构建一个应用，使得它能够将多字段信息从一个 Activity 传输到另一个 Activity

这里姓名和专业两个是学生的属性字段，我们可以构造学生对象，让学生对象实现序列化 Serializable 接口，然后使用对象序列化的方式通过 Intent 传递对象：

构造 Student 类：

~~~java
package com.example.activitytest;

import java.io.Serializable;

public class Student implements Serializable {
    String name;
    String specility;

    public Student(String name, String specility) {
        this.name = name;
        this.specility = specility;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSpecility() {
        return specility;
    }

    public void setSpecility(String specility) {
        this.specility = specility;
    }
}
~~~

使用 Intent 传递序列化的 Student 对象：

~~~java
Button send=(Button)findViewById(R.id.send);
        send.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent()
                        .setClassName(ThirdActivity.this,"com.example.activitytest.FifthActivity")
                        .putExtra("student",new Student(name.getText().toString(),speciality.getText().toString())));
            }
        });
~~~

在 Intent 的接收端将对象反序列化：

~~~java
Student student=(Student)getIntent().getSerializableExtra("student");
~~~

显示结果如下：

发送端 Activity ：

![1633158535891](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633158535891.png)

接收端 Activity：

![1633158550008](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633158550008.png)



### 2. 总结

Android 应用开发主要使用 Intent 作为应用程序之间的桥梁。