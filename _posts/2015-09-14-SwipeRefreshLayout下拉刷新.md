---
layout:     post
title:      SwipeRefreshLayout下拉刷新
date:       2015-09-07 17:47:18
summary:    android.support.v4.widget.SwipeRefreshLayout控件
author:     Tneciv
categories: android
thumbnail: android
tags:
 - SwipeRefreshLayout
 - 下拉刷新
---

在xml文件中引用`android.support.v4.widget.SwipeRefreshLayout`控件，在里面可以放置任何一个控件，例如RecyclerView,ListView,gridview等。
本文使用RecyclerView。

布局文件：

````xml

<android.support.v4.widget.SwipeRefreshLayout  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:id="@+id/swipe_refresh_widget"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent" >  
  
    <android.support.v7.widget.RecyclerView  
        android:id="@android:id/list"  
        android:layout_width="match_parent"  
        android:layout_height="match_parent"  
        android:cacheColorHint="@null"  
        android:scrollbars="vertical" />  
  
</android.support.v4.widget.SwipeRefreshLayout>  

````

在Activity中引用这个布局并初始化:

````java

private SwipeRefreshLayout mSwipeRefreshWidget; 

@Override  
 protected void onCreate(Bundle savedInstanceState) {  
  super.onCreate(savedInstanceState);  
  setContentView(R.layout.activity_main);  
  
  mSwipeRefreshWidget = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh_widget);  
  mRecyclerView = (RecyclerView) findViewById(android.R.id.list);  
  // 设置刷新时动画的颜色，可以设置4个
  mSwipeRefreshWidget.setColorSchemeResources(R.color.color1, R.color.color2,  
    R.color.color3, R.color.color4);  
  mSwipeRefreshWidget.setOnRefreshListener(this);  
  
  // 这句话是为了，第一次进入页面的时候显示加载进度条  
  mSwipeRefreshWidget.setProgressViewOffset(false, 0, (int) TypedValue  
    .applyDimension(TypedValue.COMPLEX_UNIT_DIP, 24, getResources()  
      .getDisplayMetrics()));  
  
  mRecyclerView.setOnScrollListener(new RecyclerView.OnScrollListener() {  
  
   @Override  
   public void onScrollStateChanged(RecyclerView recyclerView,  
     int newState) {  
    super.onScrollStateChanged(recyclerView, newState);  
    if (newState == RecyclerView.SCROLL_STATE_IDLE  
      && lastVisibleItem + 1 == adapter.getItemCount()) {  
     mSwipeRefreshWidget.setRefreshing(true);  
     // 此处在现实项目中，请换成网络请求数据代码，sendRequest .....  
     handler.sendEmptyMessageDelayed(0, 3000);  
    }  
   }  
  
   @Override  
   public void onScrolled(RecyclerView recyclerView, int dx, int dy) {  
    super.onScrolled(recyclerView, dx, dy);  
    lastVisibleItem = mLayoutManager.findLastVisibleItemPosition();  
   }  
  
  });  
  
  mRecyclerView.setHasFixedSize(true);  
  mLayoutManager = new LinearLayoutManager(this);  
  mRecyclerView.setLayoutManager(mLayoutManager);  
  mRecyclerView.setItemAnimator(new DefaultItemAnimator());  
  
  adapter = new SampleAdapter();  
  mRecyclerView.setAdapter(adapter);  
  
  // 此处在现实项目中，请换成网络请求数据代码，sendRequest .....  
  handler.sendEmptyMessageDelayed(0, 3000);  
 }  

````

SwipeRefreshLayout里面需要注意的Api：
1. setOnRefreshListener(OnRefreshListener listener)  设置下拉监听，当用户下拉的时候会去执行回调
2. setColorSchemeResources(int... colors) 设置 进度条的颜色变化，最多可以设置4种颜色
3. setProgressViewOffset(boolean scale, int start, int end) 调整进度条距离屏幕顶部的距离
4. setRefreshing(boolean refreshing) 设置SwipeRefreshLayout当前是否处于刷新状态，一般是在请求数据的时候设置为true，在数据被加载到View中后，设置为false

RecyclerView的实现：
> 第一种，下拉刷新和上拉刷新都用SwipeRefreshLayout 自带的进度条

````java

mRecyclerView = (RecyclerView) findViewById(android.R.id.list);  
mRecyclerView.setOnScrollListener(new RecyclerView.OnScrollListener() {  
  
   @Override  
   public void onScrollStateChanged(RecyclerView recyclerView,  
     int newState) {  
    super.onScrollStateChanged(recyclerView, newState);  
    if (newState == RecyclerView.SCROLL_STATE_IDLE  
      && lastVisibleItem + 1 == adapter.getItemCount()) {  
     mSwipeRefreshWidget.setRefreshing(true);  
     // 此处在现实项目中，请换成网络请求数据代码，sendRequest .....  
     handler.sendEmptyMessageDelayed(0, 3000);  
    }  
   }  
  
   @Override  
   public void onScrolled(RecyclerView recyclerView, int dx, int dy) {  
    super.onScrolled(recyclerView, dx, dy);  
    lastVisibleItem = mLayoutManager.findLastVisibleItemPosition();  
   }  
  });  
  
  mRecyclerView.setHasFixedSize(true);  
  mLayoutManager = new LinearLayoutManager(this);  
  mRecyclerView.setLayoutManager(mLayoutManager);  
  mRecyclerView.setItemAnimator(new DefaultItemAnimator());  
  
  adapter = new SampleAdapter();  
  mRecyclerView.setAdapter(adapter);  

````

> 第二种实现下拉刷新用SwipeRefreshLayout 自带的进度条， 上拉刷新用类似ListView的刷新 提示“加载中”等信息
> 可以给RecyclerView 也添加一个类似FooterView的item。在Adapter中实现：

````java

public class SampleAdapter extends RecyclerView.Adapter<ViewHolder> {  
 private List<Integer> list;  
  
 private static final int TYPE_ITEM = 0;  
 private static final int TYPE_FOOTER = 1;  
  
 public List<Integer> getList() {  
  return list;  
 }  
  
 public SampleAdapter() {  
  list = new ArrayList<Integer>();  
 }  
  
 // RecyclerView的count设置为数据总条数+ 1（footerView）  
 @Override  
 public int getItemCount() {  
  return list.size() + 1;  
 }  
  
 @Override  
 public int getItemViewType(int position) {  
  // 最后一个item设置为footerView  
  if (position + 1 == getItemCount()) {  
   return TYPE_FOOTER;  
  } else {  
   return TYPE_ITEM;  
  }  
 }  
  
 @Override  
 public void onBindViewHolder(ViewHolder holder, final int position) {  
  if (holder instanceof ItemViewHolder) {  
   ((ItemViewHolder) holder).textView.setText(String.valueOf(list  
     .get(position)));  
  }  
 }  
  
 @Override  
 public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {  
  if (viewType == TYPE_ITEM) {  
   View view = LayoutInflater.from(parent.getContext()).inflate(  
     R.layout.list_item_text, null);  
   view.setLayoutParams(new LayoutParams(LayoutParams.MATCH_PARENT,  
     LayoutParams.WRAP_CONTENT));  
   return new ItemViewHolder(view);  
  }  
  // type == TYPE_FOOTER 返回footerView  
  else if (viewType == TYPE_FOOTER) {  
   View view = LayoutInflater.from(parent.getContext()).inflate(  
     R.layout.footerview, null);  
   view.setLayoutParams(new LayoutParams(LayoutParams.MATCH_PARENT,  
     LayoutParams.WRAP_CONTENT));  
   return new FooterViewHolder(view);  
  }  
  
  return null;  
 }  
  
 class FooterViewHolder extends ViewHolder {  
  
  public FooterViewHolder(View view) {  
   super(view);  
  }  
  
 }  
  
 class ItemViewHolder extends ViewHolder {  
  TextView textView;  
  
  public ItemViewHolder(View view) {  
   super(view);  
   textView = (TextView) view.findViewById(R.id.text);  
  }  
 }  
}  

````

###可能存在bug：
用Android提供的support包里的SwipeRefreshLayout和RecyclerView同时使用的时候会出现RecyclerView的item被裁剪的情况，
![bug](http://i.imgur.com/w82kHAk.png)
解决：

````java
mLayoutManager = new LinearLayoutManager(getActivity());  
            //mPicListView是RecyclerView对象  
            mPicListView.setLayoutManager(mLayoutManager);  
            mPicListView.setOnScrollListener(new OnScrollListener() {  
                @Override  
                public void onScrollStateChanged(RecyclerView recyclerView,  
                        int newState) {  
                    super.onScrollStateChanged(recyclerView, newState);  
                    //加载更多  
                    if (!isLoading  
                            && hasMore  
                            && mAdapter.getItemCount() == (mLayoutManager  
                                    .findLastVisibleItemPosition() + 1)  
                            && newState == RecyclerView.SCROLL_STATE_IDLE) {  
                        isLoading = true;  
                        requestServerArticleMore();  
                    }  
                }  
  
                @Override  
                public void onScrolled(RecyclerView recyclerView, int dx, int dy) {  
                    super.onScrolled(recyclerView, dx, dy);  
                    //解决RecyclerView和SwipeRefreshLayout共用存在的bug  
                    mRefreshLayout.setEnabled(mLayoutManager  
                            .findFirstCompletelyVisibleItemPosition() == 0);  
                }  
            });  

````



