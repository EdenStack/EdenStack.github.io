---
layout:     post
title:      ButterKnife使用
date:       2015-09-07 10:47:18
summary:    Butter Knife
author:     Tneciv
categories: android
thumbnail: android
tags:
 - android
 - butter
 - knife
 - bind
 - inject
---

###视图绑定
``@Bind``与view Id同时使用
> ``@Bind(R.id.tv) TextView textview;``
> ``onCreate``中使用``Butterknife.bind(this);``进行初始化

###资源绑定
````java
class ExampleActivity extends Activity {
    @BindString(R.string.title)
    String title;
    @BindDrawable(R.drawable.graphic)
    Drawable graphic;
    @BindColor(R.color.red)
    int red; // int or ColorStateList field
    @BindDimen(R.dimen.spacer)
    Float spacer; // int (for pixel size) or float (for exact value) field
    // ...
}
````

###无activity绑定
````java
public class FancyFragment extends Fragment {
    @Bind(R.id.button1)
    Button button1;
    @Bind(R.id.button2)
    Button button2;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fancy_fragment, container, false);
        ButterKnife.bind(this, view);
        // TODO Use fields...
        return view;
    }
}
````

###简化ViewHolder
````java
public class MyAdapter extends BaseAdapter {
  @Override public View getView(int position, View view, ViewGroup parent) {
    ViewHolder holder;
    if (view != null) {
      holder = (ViewHolder) view.getTag();
    } else {
      view = inflater.inflate(R.layout.whatever, parent, false);
      holder = new ViewHolder(view);
      view.setTag(holder);
    }

    holder.name.setText("John Doe");
    // etc...

    return view;
  }

  static class ViewHolder {
    @Bind(R.id.title) TextView name;
    @Bind(R.id.job_title) TextView jobTitle;

    public ViewHolder(View view) {
      ButterKnife.bind(this, view);
    }
  }
}
````

###将多个view放置到List或者Array数组中
````java
@Bind({ R.id.first_name, R.id.middle_name, R.id.last_name })
List<EditText> nameViews;
````

``apply``方法可以对整个view数组进行整体操作

````java
ButterKnife.apply(nameViews, DISABLE);

ButterKnife.apply(nameViews, ENABLED, false);
````

``Porperty``属性也可以通过apply方法在view数组中使用

````java
ButterKnife.apply(nameViews, View.ALPHA, 0.0f);
````

###绑定监听器
````java
@OnClick(R.id.submit)
public void submit(View view) {
  // TODO submit data to server...
}
````

####监听器参数可选
````java
@OnClick(R.id.submit)
public void submit() {
  // TODO submit data to server...
}
````

####可以自定义所绑定的参数类型
````java
@OnClick(R.id.submit)
public void sayHi(Button button) {
  button.setText("Hello!");
}
````

####为多个事件共同绑定同一个监听器
````java
@OnClick({ R.id.door1, R.id.door2, R.id.door3 })
public void pickDoor(DoorView door) {
  if (door.hasPrizeBehind()) {
    Toast.makeText(this, "You win!", LENGTH_SHORT).show();
  } else {
    Toast.makeText(this, "Try again", LENGTH_SHORT).show();
  }
}
````

####自定义的views可以绑定到其自己的listener(不用指定ID)
````java
public class FancyButton extends Button {
  @OnClick
  public void onClick() {
    // TODO do something!
  }
}
````

###绑定重置（针对Fragment）
由于Fragment与Activity生命周期不同，在``onCreateView``中绑定Fragment时，如果需要在``onDestroyView``中将view设为null，可以使用ButterKnife的``unbind``方法

````java
public class FancyFragment extends Fragment {
  @Bind(R.id.button1) Button button1;
  @Bind(R.id.button2) Button button2;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    ButterKnife.bind(this, view);
    // TODO Use fields...
    return view;
  }

  @Override public void onDestroyView() {
    super.onDestroyView();
    ButterKnife.unbind(this);
  }
}
````

###绑定其他属性
``@Nullable``注解可允许相对应的view或者listener为空

````java
@Nullable @Bind(R.id.might_not_be_there) TextView mightNotBeThere;

@Nullable @OnClick(R.id.maybe_missing) void onMaybeMissingClicked() {
  // TODO ...
}
````

###BONUS
可以针对activity、view、dialog使用findById方式(需要import ButterKnife.findById)

````java
View view = LayoutInflater.from(context).inflate(R.layout.thing, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
````


