---
layout: post
title: Android Butter Knife框架
img: indonesia.jpg
date: 2017-10-16 12:35:00 +0800
description: Butter Knife框架是一个专用于Android的View注入框架。它能够极大的简化View的绑定代码，专业解决各种findViewById。
tag: Android
---
# Android Butter Knife框架

[toc]

## 背景
最近在看一个[Github](https://github.com/)上的项目，发现在它的代码中总是会出现一些从未见过的注解
```
@BindView
@OnClick
```
经过查询发现，这个注解就是[Butter Knife][Butter Knife]框架实现的。[Butter Knife框架][Butter Knife]是一个专用于Android的View注入框架。它能够解答的简化View的绑定代码，专业解决各种findViewById。

## 简介
![logo](../assets/img/butter-knife-logo.png)

**Butter Knife官网:** [Butter Knife官网][Butter Knife]

**Butter Knife源码库:** [Butter Knife源码库][github repo]

对一个成员变量使用`@BindView`注解并传入一个View ID, Butter Knife就能够帮你找到对应的View，并且自动的进行转换（将View转换为特定的子类）：
```
class ExampleActivity extends Activity {
    @BindView(R.id.title)
    TextView title;
    @BindView(R.id.subTitle)
    TextView subTitle;
    @BindView(R.id.footer)
    TextView footer;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(saveInstanceState);
        setContentView(R.layout.sample_activity);
        ButterKnife.bind(this);
        //TODO: use fields...
    }
}
```
与缓慢的反射不同，Butter Knife通过在编译时生成代码来执行View的查找，因此不需要担心注解的性能问题。你可以看到和调试调用`bind`方法的代码，而`bind`方法则委托给这些注解生成的代码来执行。

上面的例子生成的代码，大致如下：
```
public void bind(ExampleActivity `activity) {
    activity.title = (android.widget.TextView)activity.findViewById(R.id.title);
    activity.subtitle = (android.widget.TextView)activity.findViewById(R.id.subtitle);
    activity.footer = (android.widget.TextView)activity.findViewById(R.id.footer);
}
```

## 资源绑定
绑定预定义的资源到类成员上，可以使用`@BindBool`、`@BindColor`、`@BindDimen`、`@BindDrawable`、`@BindInt`、`@BindString`。使用时，对应的注解需要使用对应的资源ID，例如`@BindString`你需要传入`R.string.string_id`的字符串的资源ID。
```
class ExampleActivity extends Activity {
    @BindString(R.string.title)
    String title;
    @BindDrawable(R.drawable.graphic)
    Drawable graphic;
    @BindColor(R.color.red)
    int red;	//int or ColorStateList field
    @BindDimen(R.dimen.spacer)
    Float Spacer;	// int(for pixel size) or float(for exact value) field
    // TODO: Use the fields
    //...
}
```

## 在非Activity中使用绑定
Butter Knife提供了`bind`方法的几个重载。因此，只要传入根视图，便可以在任何对象中使用注解绑定。

例如在Fragment中使用绑定：
```
public class FancyFragment extends Fragment {
    @BindView(R.id.button1)
    Button button1;
    @BindView(R.id.button2)
    Button button2;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle saveInstanceState) {
        View view = inflater.inflate(R.layout.fancy_fragment, container, false);
        ButterKnife.bind(this, view);
        // TODO: Use fields
        return view;
    }
}
```
还有
一种比较常见的场景，就是在ListView的Adapter中，我们常常使用的ViewHolder:
```
public class MyAdapter extends BaseAdapter {
    @Override
    public View getView(int position, View view, ViewGroup parent) {
        ViewHolder holder;
        if(view != null) {
            holder = (ViewHolder)view.getTag();
        }else{
            view = inflater.inflate(R.layout.whatever, parent, false);
            holder = new ViewHolder(view);
            view.setTag(holder);
        }

        holder.name.setText("John Doe");
        // etc...

        return view;
    }

    static class ViewHolder {
        @BindView(R.id.titile)
        TextView name;
        @BindView(R.id.job_title)
        TextView jobTitle;

        public ViewHolder(View view) {
            ButterKnife.bind(this, view);
        }
    }
}
```
`ButterKnife.bind`方法的调用可以放在任何你想调用`findViewById`的地方。

### 其他提供的绑定API
* 使用Activity作为根布局在任意对象中进行绑定。*例如，如果你使用了类似MVC的编程模式，你可以在Controller中使用`ButterKnife.biind(this, activity)`将Activity作为根布局来进行绑定。*
* 使用`ButterKnife.bind(this)`绑定一个布局的子布局。*例如，如果你在布局中使用了`<merge>`标签并且在自定义的控件构造时inflate这个子布局，你可以在inflate之后立即调用它。或者，你可以在`onFinishInflate()`回调中调用它。*

## View列表
你可以一次性将多个view绑定到一个列表或者	数组中：
```
@BindViews({R.id.first_name, R.id.middle_name, R.id.last_name})
List<EditText> nameViews;
```

### apply方法
使用`apply`方法可以在列表中的所有View上执行一个动作：
```
ButterKnife.apply(nameViews, DISABLE);
ButterKnife.apply(nameViews, ENABLED, false);
```

### Action和Setter接口
`Action`和`Setter`接口能够让你指定一些简单的动作，用于`apply()`方法当中：
```
static final ButterKnife.Action<View> DISABLE = new ButterKnife.Action<View>(){
    @Override
    public void apply(View view, int index){
        view.setEnabled(false);
    }
}

static final ButterKnife.Setter<View, Boolean> ENABLED = new ButterKnife.Setter<View, Boolean>(){
    @Override
    public void set(View view, Boolean value, int index) {
        view.setEnabled(value);
    }
}
```

### 对property使用apply方法
Android中的`Property`属性也可以使用`apply`方法进行设置：
```
ButterKnife.apply(nameViews, View.ALPHA, 0.0f);
```

## 监听器绑定
使用ButterKnife框架，监听器能够自动的绑定到特定的方法上：
```
@OnClick(R.id.submit)
public void submit(View view) {
    // TODO: submit data to server...
}
```

并且，监听器方法的参数都是可选的：
```
@OnClick(R.id.submit)
public void submit() {
    // TODO: Submit data to server...
}
```

指定一个特定的参数类型，Butter Knife框架将会自动进行类型转换：
```
@OnClick(R.id.submit)
public void submit(Button button) {
    button.setText("Hello!");
}
```

可以指定多个View ID到一个方法上，这样，这个方法就成为了这些View共同的事件处理器：
```
@OnClick({R.id.door1, R.id.door2, R.id.door3})
public void pickDoor(DoorView door) {
    if(door.hasPrizeBehind()) {
        Toast.makeText(this, "You win!", LENGTH_SHORT).show();
    }else{
        Toast.makeText(this, "Try again", LENGTH_SHORT).show();
    }
}
```

自定义View时，在View内部为它绑定监听器不需要指定ID：
```
public class FancyButton extends Button {
    @OnClick
    public void onClick() {
        // TODO: do something...
    }
}
```

## 重置绑定
Fragment的View的生命周期与Activity的不同，如果你在`onCreateView`方法中绑定View，那么你需要在`onDestroyView`中设置所有View为`null`。

为此ButterKnife返回一个`Unbinder`实例以便于你进行这项处理。在合适的生命周期回调中调用`unbind`方法就可以完成重置。
```
public class FancyFragment extends Fragment {
    @BindView(R.id.button1)
    Button button1;
    @BindView(R.id.button2)
    Button button2;
    private Unbinder unbinder;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fancy_fragment, container, false);
        unbinder = ButterKnife.bind(this, view);
        // TODO: Use fields...

        return view;
    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        unbinder.unbind();
    }
}
```

## 可选绑定
在默认情况下，所有`@Bind`绑定和监听器绑定都是必须的，如果目标View没有找到的话，Butter Knife将会抛出个异常。

如果你并不想使用这样的默认行为而是想创建一个可选的绑定，那么你只需要在变量上使用`@Nullable`注解或者在函数上使用`@Optional`注解。

**注意：任何名为`@Nullable`的注解都可以使用在变量上。但还是强烈建议[Android注解库](https://developer.android.com/studio/write/annotations.html)中的`@Nullable`**
```
@Nullable
@BindView(R.id.might_not_be_there)
TextView mightNotBeThere;

@Optional
@OnClick(R.id.maybe_missing)
void onMaybeMissingClicked() {
    // TODO ...
}
```

## 包含多个回调函数的监听器
当一个监听器包含多个回调函数时，使用函数的注解能够对其中任何一个函数进行绑定。每一个注解都会绑定到一个默认的回调，你也可以使用`callback`参数来指定一个其他函数作为回调。
```
@OnItemSelected(R.id.list_view)
void onItemSelected(int position) {
    // TODO: ...
}

@OnItemSelected(value=R.id.maybe_missing, callback=NOTHING_SELECTED)
void onNothingSelected() {
    // TODO: ...
}
```

## 福利
Butter Knife提供了一个`findViewById`的简化版代码：`findById`，用这个方法可以在`View`、`Activity`和`Dialog`中找到想要的View。而且该方法使用泛型来对返回值进行转换，也就是说，你可以省去`findViewById`前面的强制转换了。
```
View view = LayoutInflater.from(context).inflate(R.layout.thing, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
```

**如果你只使用这个方法，可以静态引入`ButterKnife.findById`方法。**

## 下载
要在项目中使用ButterKnife,你需要在你项目的build.gradle文件中增加如下配置：
```
dependencies {
    compile 'com.jakewharton:butterknife:8.8.1'
    annotationProcessor 'com.jakewharton:butterknife-compiler:8.8.1'
}
```

## 注意事项
* **使用ButterKnife框架时，要求包名不能是以android.开头的，否则会在编译时报错**

[Butter Knife]: http://jakewharton.github.io/butterknife/ "Butter Knife官网"
[github repo]: https://github.com/JakeWharton/butterknife "Butter Knife源码库"
