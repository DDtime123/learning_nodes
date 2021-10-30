# Android 打包发布 APK

#### 1. 生成秘钥

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028103858.png)

如果第一次发布 APK ，电脑上是没有秘钥的，这时候就新生成一个：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028103927.png)

生成秘钥之前，首先要选择秘钥的存储位置，秘钥要在 .jks 文件中存储，第一次生成秘钥之前要先生成 .jks 文件：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028104053.png)

#### 2. 发布APK

选择好秘钥后，填入 .jks 文件的密码和秘钥的密码，再点击 next：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028104458.png)

选择 Release 发布版本，在勾选下方的两个，点击 Finish 就可以生成能够在手机上自由安装的 APK 了：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028104610.png)

最后在 release 文件夹下找到生成的 APK：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211028110324.png)