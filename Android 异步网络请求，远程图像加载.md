# Android 异步网络请求，远程图像加载

### 1. 第三方库使用

* Android AsyncHTTPClient——第三方异步网络请求库
* Picasso——远程图像加载库

~~~xml
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:21.0.2'
    compile 'com.loopj.android:android-async-http:1.4.6'
    compile 'com.squareup.picasso:picasso:2.4.0'
}
~~~

### 2. 应用程序的设计

#### 2.1 组件设计

应用程序应该具有以下五个组件：

* BookClient——负责执行 API 请求并检索 JSON
* Book——负责封装每本书的属性的模型对象
* BookAdapter——负责将每个 Book 映射到指定的视图布局
* BookListActivity——负责获取和反序列化数据——

#### 2.2 应用程序执行步骤

* 使用 OpenLiarary Search API 搜索图书列表
* 使用带有封面图片和详细信息的书籍列表
* 使用工具栏替换操作栏
* 使用 SearchView 搜索带有书名的书籍
* 在网络请求前显示 ProgressBar
* 添加详细视图以显示有关列表中所选书籍的更多信息
* 使用**分享意图**向朋友推荐一本书