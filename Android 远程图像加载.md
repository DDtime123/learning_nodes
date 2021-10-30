# Android 远程图像加载

### 1. 使用 Picasso 库

#### 1.1 在 Gradle 中添加 Picasso 依赖包

~~~xml
dependencies {
    compile 'com.squareup.picasso:picasso:2.4.0'
}
~~~

#### 1.2 使用 Picasso 从网络下载图像

~~~java
Picasso.get().load("url").into(imageView);
~~~

```
.addHeader("Authorization","563492ad6f917000010000012cedbf28021f4925a611b30a94dec7c8")
```

#### 1.3 使用 Picasso 的好处

* Picasso 自动处理 ImageView 在适配器中的回收和下载取消。在网络上的数据都是以字节流的形式在主机之间进行传输的。
* 以最少的内存使用复杂的图像转换
* 自动内存和磁盘缓存

#### 1.4 使用 OkHttp 和 Picasso 的组合库

Gradle 添加依赖包：

~~~xml
compile 'com.jakewharton.picasso:picasso2-okhttp3-downloader:1.1.0'
~~~

使用这个库，就可以达成直接使用 OkHttpClient 和 URL 直接创建出 Picasso 对象，同时也可以使用 OkHttpClient 添加 HTTP 标头：

~~~java

~~~

### 2. 图像搜索 API

#### 2.1 pexels api

我的 API 秘钥：

~~~ 
563492ad6f917000010000012cedbf28021f4925a611b30a94dec7c8
~~~

##### 2.1.1 pexels api 简介

pexels API 是一种 RESTFul 风格的 api。我们可以通过它获取包括照片和视频在内的完整的 Pexels 内容库。



##### 2.1.2 如何往 URL 中添加 HTTP header



### 3. 使用 OkHttp 进行异步网络请求



### 3. 模块设计

#### 3.1 adapters

接收器，将网络资源映射到视图布局中

























