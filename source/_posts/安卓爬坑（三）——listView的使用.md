---
title: 安卓爬坑（三）——listView的使用
date: 2019-07-07 22:16:23
tags: [Java,Android]
---
<br>

转自 <https://www.cnblogs.com/r-decade/p/5827841.html> 
# ListView

　　显示大量相同格式数据

## 常用属性:
方法 | 说明 
-|-
`listSelector`  | listView每项在选中、按下等不同状态时的Drawable
`divider` | ListView每项间的间隔Drawable
`dividerHeight` | ListView每项间间隔的间隔高度

{% asset_img 1.png %}

## 常用方法：
方法 | 说明 
-|-
`setAdapter()`   | 设置数据适配器
`setOnItemClickListener()`   | 设置每项点击事件监听
`addHeaderView()`   | 添加头视图
`addFooterView()`   | 添加脚视图
`setEmptyView()`   | 设置数据项为0时的空数据视图


## 监听事件

　　　　　　这个监听的例子是设置了一个头视图    项的下标改变    所以用 `position-listview.getHeaderViewsCount()`   计算项的下标位置 获取正确的对象  
{% asset_img 2.png %}

## Adapter数据适配器     
将各种数据以合适的形式绑定到控件上   像`listview`, `gridview`, `spinner` 等等等控件 都会用到`Adapter`绑定数据

## 介绍三种Adapter

`ArrayAdapter`：支持泛型操作，最简单的一个Adapter，只能展现一行文字

`SimpleAdapter`：同样具有良好扩展性的一个Adapter，可以自定义多种效果

`BaseAdapter`：抽象类，实际开发中我们会继承这个类并且重写相关方法，用得最多的一个Adapter

 

先从简单的介绍

### 1.ArrayAdapter
{% asset_img 3.png %}
### 显示效果   绑定了listview
{% asset_img 4.png %}

### 2.SimpleAdapter 

　　`simpleAdapter`的扩展性最好，可以定义各种各样的布局出来，可以放上ImageView（图片），还可以放上Button（按钮），CheckBox（复选框）等等

{% asset_img 5.png %}
### 显示结果
{% asset_img 6.png %}
### 3.BaseAdapter

`BaseAdapter`是开发中最常用的适配器`ArrayAdapter`, `SimpleAdapter` 都继承于`BaseAdapter`。`BaseAdapter`可以完成自己定义的`Adapter`，可以将任何复杂组合的数据和资源，以任何你想要的显示效果展示给大家。

继承`BaseAdapter`之后，需要重写以下四个方法：`getCount，getItem，getItemId，getView。`

系统在绘制ListView之前，将会先调用getCount方法来获取Item的个数。每绘制一个Item就会调用一次getView方法，在getView中引用事先定义好的layout布局确定显示的效果并返回一个View对象作为一个Item显示出来。

这两个方法是自定ListView显示效果中最为重要的，同时只要重写好了这两个方法，ListView就能完全按开发者的要求显示。而getItem和getItemId方法将会在调用ListView的响应方法的时候被调用到。

 

#### 自定义布局文件（listview的项的显示效果）
``` xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/image_photo"
        android:layout_width="70dp"
        android:layout_height="70dp"
        android:padding="10dp"/>
    <TextView
        android:id="@+id/name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20dp"
        android:layout_toRightOf="@id/image_photo"/>
    <TextView
        android:id="@+id/age"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20dp"
        android:layout_toRightOf="@id/image_photo"
        android:layout_below="@id/name"
        android:layout_marginTop="10dp"/>
</RelativeLayout>
```
#### student学生类

``` java

public class Student {
    private String name;
    private int age;
    private int photo;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getPhoto() {
        return photo;
    }

    public void setPhoto(int photo) {
        this.photo = photo;
    }
}

```

#### MainActivity

``` java

public class MainActivity extends AppCompatActivity {

    private ListView listView;
　　//自定义BaseAdapter
    private MyAdapter adapter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        listView= (ListView) findViewById(R.id.listview);
        List<Student> stuList=new ArrayList<>();
        for(int i=0;i<10;i++){
            Student stu=new Student();
            stu.setAge(10+i);
            stu.setName("name"+i);
            stu.setPhoto(R.mipmap.ic_launcher);
            stuList.add(stu);
        }
　　　　　　
        adapter=new MyAdapter(stuList,MainActivity.this);
        listView.setAdapter(adapter);
    }

```

#### MyAdapter

``` java

    public class MyAdapter extends BaseAdapter {

    private List<Student> stuList;
    private LayoutInflater inflater;
    public MyAdapter() {}

    public MyAdapter(List<Student> stuList,Context context) {
        this.stuList = stuList;
        this.inflater=LayoutInflater.from(context);
    }

    @Override
    public int getCount() {
        return stuList==null?0:stuList.size();
    }

    @Override
    public Student getItem(int position) {
        return stuList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        //加载布局为一个视图
        View view=inflater.inflate(R.layout.layout_student_item,null);
        Student student=getItem(position);
        //在view视图中查找id为image_photo的控件
        ImageView image_photo= (ImageView) view.findViewById(R.id.image_photo);
        TextView tv_name= (TextView) view.findViewById(R.id.name);
        TextView tv_age= (TextView) view.findViewById(R.id.age);
        image_photo.setImageResource(student.getPhoto());
        tv_name.setText(student.getName());
        tv_age.setText(String.valueOf(student.getAge()));
        return view;
    }

```
#### 显示结果
{% asset_img 7.png %}





jizhi生活要结束了，，加油。。。