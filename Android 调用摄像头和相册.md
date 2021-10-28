# Android 调用摄像头和相册

### 1. 调用摄像头拍照

第一步，在调用方 Activity 中要设置如下三个变量，

~~~java
// 用于存储从相机拍摄回来的照片，用 ImageView 来存储
private ImageView picture;
// 用于指定照片存储位置的 Uri，这个 Uri 注册为 contentProvider
private Uri imageUri;
// 请求码，用于辨别返回的 Intent 应该对应的是哪一个结果，结果由请求方写
public static final int TAKE_PHOTO = 1;
~~~

第二步，调用摄像头：

~~~java
// 创建 Image 对象，用于存储拍摄的图片
File outputImage = new File(getExternalCacheDir() ,"output_image.jpg");
try{
    if(outputImage.exists()){
        outputImage.delete();
    }
    outputImage.createNewFile();
}catch(IOException e){
    e.printStackTrace();
}
if(Build.VERSION.SDK_INT >= 24){
    imageUri = FileProvider.getUriForFile(WeatherActivity.this,
            "com.example.weatherapp.fileprovider",outputImage);
}else{
    imageUri = Uri.fromFile(outputImage);
}
// 启动相机程序
Intent intent = new Intent("android.media.action.IMAGE_CAPTURE");
intent.putExtra(MediaStore.EXTRA_OUTPUT,imageUri);
startActivityForResult(intent, TAKE_PHOTO);
~~~

调用摄像头的逻辑步骤如下：

创建一个用于存储照片的文件对象 File ，这里设置了文件的存储位置和文件名称。存储位置被设置为当前应用的缓存位置，使用缓存位置的原因是自从 Android 6.0 开始，读写 SD 卡其他位置被视为危险操作，需要运行时权限。

第三步，使用 ImageUri 注册好内容提供者 contentProvider 。内容提供者是 Android 为了避免直接提供本地的真实目录，而构建的虚拟的文件目录。只有再内容提供者中注册的目录才能被应用程序访问，而这样的目录需要被 Uri 包装起来。

使用内容提供者之前，要先再 AndroidManifest 文件中注册内容提供者，这里注册的 android:name 将是你能够在应用程序中使用的虚拟文件目录：

~~~xml
<!-- 注册虚拟目录 androidx.core.content.FileProvider，其实际对应的目录在 @xml/file_paths 里 -->
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="com.example.cameraalbumtest.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
~~~

~~~xml
<!-- 这里的是绑定在虚拟目录下的真实目录，path 为空表示整个SD卡作为共享目录 -->
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="my_images" path="" />
</paths>
~~~

getUriForFile 方法从虚拟目录 "com.example.weatherapp.fileprovider" 获取 Uri ，还接受一个文件名，待会儿这个 ImageUri 将被发送给相机程序：

~~~java
imageUri = FileProvider.getUriForFile(WeatherActivity.this,
            "com.example.weatherapp.fileprovider",outputImage);
~~~

第四步，如果相机程序返回的结果码是 OK 的，那么就将 Uri 传回给内容提供者，获取真实文件，并通过 Bitmap 工厂的解析文件流方法将它存储到Bitmap 对象中，最后再放到 ImageView 对象中：

~~~java
if(resultCode == RESULT_OK){
    try{
        // 将拍摄的照片显示出来
        Bitmap bitmap = BitmapFactory.decodeStream(getContentResolver().openInputStream(imageUri));
        picture.setImageBitmap(bitmap);
    }catch (FileNotFoundException e){
        e.printStackTrace();
    }
}
~~~

最后，打开程序，拍照后就可以在 Android/data/com.example.weatherapp/cache 目录下找到拍摄的照片了。













