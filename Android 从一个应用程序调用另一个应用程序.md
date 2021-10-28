# Android 从一个应用程序调用另一个应用程序

### 1. 在不知道其它应用程序的包名的前提下

使用 intent filter 执行活动。

### 2. 根据包名启动应用

~~~java
// 打开B站 包名：tv.danmaku.bili
Intent intent = getPackageManager().getLaunchIntentForPackage("tv.danmaku.bili");
startActivity(intent);
~~~