---
layout:     post
title:      Floating Action Button使用
date:       2015-09-24 15:23:18
summary:    Floating Action Button
author:     Tneciv
categories: android
thumbnail: android
tags:
 - Floating Action Button
 - Material Design
---



Floating Action Button

```xml

<android.support.design.widget.FloatingActionButton 
	android:id="@+id/fab" android:layout_width="wrap_content" 
	android:layout_height="wrap_content" 
	android:layout_margin="16dp" 
	android:src=http://blog.csdn.net/u010687392/article/details/ "@mipmap/icon" 
	app:backgroundTint="#30469b" 
	app:borderWidth="0dp" 
	app:elevation="6dp" 
	app:fabSize="normal" 
	app:layout_anchor="@id/coordinator_layout" 
	app:layout_anchorGravity="bottom|right" 
	app:pressedTranslationZ="12dp" 
	app:rippleColor="#a6a6a6" />


```

**属性：**

* app:backgroundTint - 设置FAB的背景颜色。
* app:rippleColor - 设置FAB点击时的背景颜色。
* app:borderWidth - 该属性尤为重要，如果不设置0dp，那么在4.1的sdk上FAB会显示为正方形，而且在5.0以后的sdk没有阴影效果。所以设置为borderWidth="0dp"。
* app:elevation - 默认状态下FAB的阴影大小。
* app:pressedTranslationZ - 点击时候FAB的阴影大小。
* app:fabSize - 设置FAB的大小，该属性有两个值，分别为normal和mini，对应的FAB大小分别为56dp和40dp。
src - 设置FAB的图标，Google建议符合Design设计的该图标大小为24dp。
* app:layout_anchor - 设置FAB的锚点，即以哪个控件为参照点设置位置。
* app:layout_anchorGravity - 设置FAB相对锚点的位置，值有 bottom、center、right、left、top等。


&emsp;&emsp;一般情况下，FAB与Snackbar配合使用时候会出现Snackbar遮住FAB：如：
![](http://i.imgur.com/zvnXZDh.png)


&emsp;&emsp;为了解决这个问题，我们把Snackbar.make(View view,,).show();的第一个参数View设置为CoordinatorLayout，即：
把mCoordinatorLayout传给Snackbar Snackbar.make(mCoordinatorLayout.getRootView(), "Snackbar", Snackbar.LENGTH_SHORT).show();

&emsp;&emsp;这样CoordinatorLayout就可以协调各个View之间的动画效果，效果就变为nackbar不会遮挡FAB的显示了，当Snackbar出现时FAB会自动上移。

&emsp;&emsp;当然FAB的点击事件也是通过setOnClickListener()设置即可。

&emsp;&emsp;另一种效果：

![](http://i.imgur.com/4BFMDcA.gif)


```xml

<android.support.design.widget.CoordinatorLayout 
	xmlns:android="http://schemas.android.com/apk/res/android" 
	xmlns:app="http://schemas.android.com/apk/res-auto" 
	xmlns:tools="http://schemas.android.com/tools" 
	android:id="@+id/coordinator_layout" 
	android:layout_width="match_parent" 
	android:layout_height="match_parent" 
	tools:context=".MainActivity"> 

	<android.support.design.widget.AppBarLayout 
		android:layout_width="match_parent" 
		android:layout_height="256dp" 
		android:fitsSystemWindows="true"> 

		<android.support.design.widget.CollapsingToolbarLayout 
			android:id="@+id/collapsingToolbarLayout" 
			android:layout_width="match_parent" 
			android:layout_height="match_parent" 
			app:contentScrim="#30469b" 
			app:expandedTitleMarginStart="48dp" 
			app:layout_scrollFlags="scroll|exitUntilCollapsed"> 

			<ImageView 
				android:layout_width="match_parent" 
				android:layout_height="match_parent" 
				android:scaleType="centerCrop" 
				android:src=http://blog.csdn.net/u010687392/article/details/"@mipmap/bg" 
				app:layout_collapseMode="parallax" 
				app:layout_collapseParallaxMultiplier="0.7" /> 

		</android.support.design.widget.CollapsingToolbarLayout>

	</android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>


```