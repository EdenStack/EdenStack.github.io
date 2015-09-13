---
layout:     post
title:      创建RecyclerViewAdapter
date:       2015-09-13 18:34:18
summary:    创建RecyclerViewAdapter步骤，使用步骤
author:     Tneciv
categories: android
thumbnail: android
tags:
 - RecyclerViewAdapter
 - RecyclerView
---



###RecyclerViewAdapter步骤：
> 创建自定义ViewHolder：

````java

class PersonalViewHolder extends RecyclerView.ViewHolder {
	//对itemView进行初始化
	TextView tv;

	public PersonalViewHolder(View itemView) {

		super(itemView);
		tv = (TextView) itemView.findViewById(R.id.tv_item);
	}

````

> 创建Adapter构造方法：包含`context`,`data`

````java 

	private Context context;
	private List<String> mDatas;
	private LayoutInflater mInflater;

	public PersonalAdapter(Context context, List<String> mDatas) {
		this.context = context;
		this.mDatas = mDatas;
		mInflater = LayoutInflater.from(context);
	}

````

> 创建ViewHolder

````java

@Override
	public PersonalViewHolder onCreateViewHolder(ViewGroup viewGroup, int i) {
		//inflater itemView父布局文件，即指定itemView存在位置
		View view = mInflater.inflate(R.layout.item_fag, viewGroup, false);

		PersonalViewHolder viewHolder = new PersonalViewHolder(view);

		return viewHolder;
	}

````


> 进行数据绑定

````java

@Override
	public void onBindViewHolder(PersonalViewHolder viewHolder, int pos) {
		//绑定itemView数据内容
		viewHolder.tv.setText(mDatas.get(pos));

	}

````

###在activity中使用RecyclerViewAdapter：

````java

	private RecyclerView recyclerView;
	private List<String> mDatas;
	private PersonalAdapter mAdapter;

	mAdapter = new PersonalAdapter(this, mDatas);
	recyclerView.setAdapter(mAdapter);
	LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
	recyclerView.setLayoutManager(linearLayoutManager);

````


