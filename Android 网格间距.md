# Android 网格间距

~~~xml
<LinearLayout android:orientation="vertical">

    <LinearLayout android:layout_width="match_parent"
                  android:layout_height="wrap_content">
    
    </LinearLayout>
</LinearLayout>
~~~

vertical （垂直）排列的布局中，元素的高度不能 match_parent；

horizontal （水平）排列的布局中，元素的宽度不能 match_parent。



#### 1. Android GridLayout 实现

如果想要线性地显示元素，就使用线性布局。

如果想要按照行和列来安排和显示元素，就使用 GridLayout。

~~~xml
<GridLayout>
</GridLayout>
~~~

GridLayout 的属性 rowCount 和 columnCount 定义网格布局的最大行数和最小行数。

接下来往布局里添加组件，可以指定它们位于第几行和第几列，通过设置组件的 layout_row 和 layout_column 属性。