## 布局结构
    
    <?xml version="1.0" encoding="utf-8"?>
        <android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
            <android.support.design.widget.AppBarLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:fitsSystemWindows="true"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">
            <android.support.design.widget.CollapsingToolbarLayout
                android:id="@+id/collapsing"
                android:layout_width="match_parent"
                android:layout_height="250dp"
                app:contentScrim="@color/primary"
                android:fitsSystemWindows="true"
                app:expandedTitleMarginStart="48dp"
                app:expandedTitleMarginEnd="64dp"
                app:layout_scrollFlags="scroll|exitUntilCollapsed"
                >
                <ImageView
                    android:id="@+id/artical_img"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:scaleType="centerCrop"
                    android:fitsSystemWindows="true"
                    app:layout_collapseMode="parallax"
                    app:layout_collapseParallaxMultiplier="0.7"
                    android:src="@mipmap/androidschoolbus"/>
                <android.support.v7.widget.Toolbar
                    android:id="@+id/artical_toolbar"
                    android:layout_width="match_parent"
                    android:layout_height="?attr/actionBarSize"
                    android:minHeight="?attr/actionBarSize"
                    android:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                    app:layout_collapseMode="pin"
                    >
    
                </android.support.v7.widget.Toolbar>
    
            </android.support.design.widget.CollapsingToolbarLayout>
        </android.support.design.widget.AppBarLayout>
        <android.support.v4.widget.SwipeRefreshLayout
            android:id="@+id/artical_swipe"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior">
            <android.support.v4.widget.NestedScrollView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                >
                <TextView
                    android:id="@+id/artical_text"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:padding="10dp"
                    android:textSize="18dp"
                    android:lineSpacingMultiplier="1.5"
                    android:layout_marginBottom="20dp"/>
            </android.support.v4.widget.NestedScrollView>
        </android.support.v4.widget.SwipeRefreshLayout>
    </android.support.design.widget.CoordinatorLayout>
        
从最外层到最内层依次是CoordinatorLayout、AppBarLayout、CollapsingToolbarLayout、(这里可以加入一个想要折叠的布局，如)ImageView、Toolbar,然后在AppBarLayout外、CoordinatorLayout内加上可滑动的布局，如RecycleView、NestedScrollView。最后在滑动布局中加上让AppBarLayout响应滚动的behavior:

    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    
AppBarLayout是一种支持响应滚动手势的app bar布局（比如工具栏滚出或滚入屏幕），CollapsingToolbarLayout则是专门用来实现子布局内不同元素响应滚动细节的布局。

与AppBarLayout组合的滚动布局（Recyclerview、NestedScrollView等）需要设置app:layout_behavior="@string/appbar_scrolling_view_behavior"（上面代码中NestedScrollView控件所设置的）。没有设置的话，AppBarLayout将不会响应滚动布局的滚动事件。

CollapsingToolbarLayout和ScrollView一起使用会有滑动bug，注意要使用NestedScrollView来替代ScrollView。

AppBarLayout的子布局有5种滚动标识(就是上面代码CollapsingToolbarLayout中配置的app:layout_scrollFlags属性)：

1.scroll:将此布局和滚动时间关联。这个标识要设置在其他标识之前，没有这个标识则布局不会滚动且其他标识设置无效。

2.enterAlways:任何向下滚动操作都会使此布局可见。这个标识通常被称为“快速返回”模式。

3.enterAlwaysCollapsed：假设你定义了一个最小高度（minHeight）同时enterAlways也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。

4.exitUntilCollapsed：当你定义了一个minHeight，此布局将在滚动到达这个最小高度的时候折叠。

5.snap:当一个滚动事件结束，如果视图是部分可见的，那么它将被滚动到收缩或展开。例如，如果视图只有底部25%显示，它将折叠。相反，如果它的底部75%可见，那么它将完全展开。

CollapsingToolbarLayout可以通过app:contentScrim设置折叠时工具栏布局的颜色，通过app:statusBarScrim设置折叠时状态栏的颜色。默认contentScrim是colorPrimary的色值，statusBarScrim是colorPrimaryDark的色值。这个后面会用到。

CollapsingToolbarLayout的子布局有3种折叠模式（Toolbar中设置的app:layout_collapseMode）

1.off：这个是默认属性，布局将正常显示，没有折叠的行为。

2.pin：CollapsingToolbarLayout折叠后，此布局将固定在顶部。

3.parallax：CollapsingToolbarLayout折叠时，此布局也会有视差折叠效果。

当CollapsingToolbarLayout的子布局设置了parallax模式时，我们还可以通过app:layout_collapseParallaxMultiplier设置视差滚动因子，值为：0~1。

FloatingActionButton这个控件通过app:layout_anchor这个设置锚定在了AppBarLayout下方。FloatingActionButton源码中有一个Behavior方法，当AppBarLayout收缩时，FloatingActionButton就会跟着做出相应变化。

深坑：使用CollaspingToolbarLayout时，Toolbar不要设置background属性！切记！
