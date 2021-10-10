### Android Studio发布apk

在使用Android Studio调试时，调试使用的app可以安装在虚拟机或者真机上。但是要想让它在任何人的手机上安装，就要发布成release版本，而release版本的发布需要数字签名。

#### 1. 生成apk文件

1. 点击 Build -> Generate Signed Bundle / APK 

![1631757460240](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631757460240.png)

2. 选择apk

![1631757569975](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631757569975.png)

3. 生成新的keyStore

![1631757663200](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631757663200.png)

4. 自己选择合适的路径后填写新的KeyStore的文件名

![1631757972828](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631757972828.png)

其中密码1是KeyStore的密码，密码2是KeyStore中的Key0的密码。

5. 设置为发布模式并选择路径后，点击Finish开始生成apk

![1631758741146](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631758741146.png)

6. 生成apk的目录如下

   ![1631758905289](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631758905289.png)

#### 2. 总结

以上介绍了利用Android Studio发布release版本apk的过程。

