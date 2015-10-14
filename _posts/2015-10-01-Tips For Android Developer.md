---
layout:     post
title:      Android Developer Tips
date:       2015-10-01 01:02:18
summary:    Android Developer Tips
author:     Tneciv
categories: android
thumbnail: android
tags:
 - Android 
 - Developer 
 - Tips
---

##在Fragment中解析JSon数据

AsyncHttpClient请求网络


````java

	AsyncHttpClient httpClient = new AsyncHttpClient();
	AsyncHttpResponseHandler responseHandler = new BaseJsonHttpResponseHandler<DailyDetails>() {
		@Override
		public void onSuccess(int statusCode, Header[] headers, String rawJsonResponse, DailyDetails response) {
			dailyList.clear();

			if (response.stories != null) {
				for (Daily item : response.stories) {
					dailyList.add(item);
				}
				dailyListAdapter.notifyDataSetChanged();
				swipeRefreshLayout.setRefreshing(false);
			}

		}

		@Override
		public void onFailure(int statusCode, Header[] headers, Throwable throwable, String rawJsonData, DailyDetails errorResponse) {
			Toast.makeText(getActivity(), "请检查网络连接", Toast.LENGTH_SHORT).show();
			swipeRefreshLayout.setRefreshing(false);
		}

		@Override
		protected DailyDetails parseResponse(String rawJsonData, boolean isFailure) throws Throwable {
			Gson gson = new Gson();
			return gson.fromJson(rawJsonData, DailyDetails.class);
		}
	};

````

FragmentAdapter，内部使用bundle放置数据，通过``newFragment.setArguments(bundle);``传递，在Fragment中使用

````java
Bundle bundle = getArguments();
date = bundle.getString("date");
````
获得数据。

````java

private class MyPagerAdapter extends FragmentStatePagerAdapter {
        public MyPagerAdapter(FragmentManager fm) {
            super(fm);
        }

        @Override
        public Fragment getItem(int i) {
            Bundle bundle = new Bundle();
            Fragment newFragment = new NewsListFragment();

            Calendar dateToGetUrl = Calendar.getInstance();
            dateToGetUrl.add(Calendar.DAY_OF_YEAR, 1 - i);
            String date = Constants.simpleDateFormat.format(dateToGetUrl.getTime());

            bundle.putBoolean("first_page?", i == 0);
            bundle.putBoolean("single?", false);
            bundle.putString("date", date);

            newFragment.setArguments(bundle);
            return newFragment;
        }

        @Override
        public int getCount() {
            return Constants.PAGE_COUNT;
        }

        @Override
        public CharSequence getPageTitle(int position) {
            Calendar displayDate = Calendar.getInstance();
            displayDate.add(Calendar.DAY_OF_YEAR, -position);

            return DateFormat.getDateInstance().format(displayDate.getTime());
        }
    }

````

##解析json并填充：
1. 在Fragment中使用Async和Gson进行json获取及解析，Gson赋值，处理相关class
2. 对应的RecyclerViewAdapter的onBindViewHolder中对解析到的json进行填充
3. 在Fragment的onCreateView中，设置RecyclerView的Adapter时，数据进行绑定

Tips：
RecyclerViewAdapter中自定义的继承RecyclerView.ViewHolder的ViewHolder中，需要初始化所有需要用到的控件:

````java

class ListViewHolder extends RecyclerView.ViewHolder {

		@Bind(R.id.img_item)
		ImageView img;
		@Bind(R.id.title_item)
		TextView title;

		public ListViewHolder(View itemView) {
			super(itemView);
			ButterKnife.bind(this, itemView);
		}

		@OnClick(R.id.cardview_item)
		void click(View view) {
			Toast.makeText(context, "You have clicked me", Toast.LENGTH_SHORT).show();
		}
	}

````