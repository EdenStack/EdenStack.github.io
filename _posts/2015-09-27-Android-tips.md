---
layout:     post
title:      Android Tips For Develop
date:       2015-09-27 10:59:18
summary:    开发过程tips
author:     Tneciv
categories: android
thumbnail: android
tags:
 - develop
 - android
 - tips
---

Fragment使用：

```java

private void initFragment() {

		FragmentManager fm = getSupportFragmentManager();
		FragmentTransaction transaction = fm.beginTransaction();
		transaction.replace(R.id.fl_content_main, new ListFragment());

		transaction.commit();

	}

```

//实现再按一次退出提醒


````java

    private long exitTime = 0;

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK
                && event.getAction() == KeyEvent.ACTION_DOWN) {

            if ((System.currentTimeMillis() - exitTime) > 3000) {
                Snackbar.make(mContainer, R.string.snack_exit_once_more, Snackbar.LENGTH_LONG).show();
                exitTime = System.currentTimeMillis();
            } else {
                finish();
            }
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }

````

//实现打开Activity

````java

private boolean prepareIntent(Class clazz) {
        startActivity(new Intent(MainActivity.this, clazz));
        return true;
    }

````

//图片填满width和height

``android:scaleType="centerCrop"``添加到imageview中

//tablayout设置显示模式

``tabLayout.setTabMode(TabLayout.MODE_SCROLLABLE);``当tab比较多时，使用此mode

``tabLayout.setTabMode(TabLayout.MODE_FIXED);``当标签不能充满屏幕时，使用此mode

//viewPager总共加载数量

``viewPager.setOffscreenPageLimit(7);``