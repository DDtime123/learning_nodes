# Android 请求应用授权

#### 1. 请求外部存储权限

在没有得到用户授权的情况下，应用程序仅允许操作程序的临时缓存区域，这个路径可以通过 getExternalCacheDir() 方法获取：

~~~java
// 获取无需授权的临时存储路径
File myDir = new File(getExternalCacheDir().toString());
// 将文件写入临时路径中

~~~

而如果要将文件存入外部存储空间内，比如说系统相册，就要请求用户授权：

#### 2. 请求授权步骤

步骤：

* 确定应用是否已经获得权限
* 说明应用为何需要获取权限
* 请求权限



##### 2.1 确定应用是否以及获得权限

检查用于是否以及授予本应用某特定权限，只需要将权限传入以下方法中：

~~~java
ContextCompat.chechSelfPermission()
~~~

如果应用已经拥有了权限，则返回 PERMISSION_GRANTED，否则返回 PERMISSION_DENIED。

##### 2.2 说明应用为何需要获取权限

如果检查应用是否获得权限的方法返回了 PERMISSION_DENIED，就调用以下方法：

~~~java
shouldShowRequestPermissionRationable(Activity Target_activity, String permission)
~~~

这个方法的意思是——是否有必要显示指导窗口。

这个方法是用来确认——在之前应用有没有已经查看过指导窗口，并且表示接受用户条款。如果用户之前以及接受用户条款，就不在显示请求指导窗口了。

如果该方法返回 true，则说明用户之前并没有接受条款，这时候请向显示用户指导窗口，并在窗口里说明为什么要获取权限。

如果请求的权限与位置信息，麦克风或相机相关，则在说明信息里解释获取敏感权限的理由。

显示指导窗口可以通过以下方法进行：

~~~java
showInContextUI()
~~~

用户指导界面通常包括 Ok 和 Cancel 两个选项。

##### 2.3 请求权限

用户查看指导界面且点击 Ok 之后，或者 shouldShowRequestPermissionRationable() 方法返回 false 之后，就向用户请求权限，请求权限使用如下类：

~~~java
RequestPermission // 请求一项权限
RequestMultiplePermission // 同时请求多项权限
~~~

以下是如何使用权限请求类的步骤：

1. 在 activity 或 fragment 的初始化逻辑中，将 ActivityResultCallback 的实现传入对 registerForActivityResult() 的调用中。ActivityResultCallback 定义应用如何处理用户对权限请求的响应。

   registerForActivityResult() 保留对返回值的引用，其类型为 ActivityResultLauncher。

2. 如需在必要时显示系统权限对话框，则对上一把保存的 ActivityResultLauncher 实例调用 launch() 方法。

   调用 launch() 方法后，就会显示系统权限对话框，当用户做出选择后，系统会异步调用我们在上一步定义的 ActivityResultCallBack 实现。

~~~java
requestPermissionLauncher.launch(Manifest.permission.REQUEST_PERMISSION)
~~~

从检查权限，到说明获取权限原因，再到请求权限的代码如下：

~~~java
// 1.检查是否已经授予了 REQUEST_PERMISSION 权限
if(ContextCompat.checkSelfPermission(
	<mContext>,Manifest.permission.<REQUESTED_PERMISSION>) == PackageManager.PERMISSION_GRANTED){
    // 1.1可以直接使用系统外设，访问系统存储文件
    ...
}else if(shouldShowRequestPermissionRationale(...)) { // 1.2 之前没有授权，现在检查是否需要弹出指导窗口
    
    // 1.2.1 弹出指导窗口
    showInContextUI(...);
}else{
    // 1.2.2 无需弹出指导窗口，进行请求授权
    ActivityCompat.requestPermissions(myActivity.this,
                    new String[] {请求字符串数组},
                    int RequestCode);
}
~~~



#### 3. 参考

[1] [Android 请求应用权限](https://developer.android.com/training/permissions/requesting)











