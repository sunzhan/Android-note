#Android Design Support Library的简单使用#
***
##AppBarLayout和ToolBar的使用##
  AppBarLayout一般与ToolBar配合使用，为了支持手势滑动使ToolBar隐藏于显示，需要在最外层使用一CoordinatorLayout布局。

	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/window_background"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBarLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        android:popupTheme="@style/ThemeOverlay.AppCompat.Light"
        app:layout_scrollFlags="scroll|enterAlways"/>

    <android.support.design.widget.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/primary"
        app:tabGravity="fill"
        app:tabIndicatorColor="#fff"
        app:tabMode="scrollable"
        app:tabSelectedTextColor="#fff"
        app:tabTextAppearance="@style/MyTextAppearance"
        app:tabTextColor="#FFF5F5" />
    </android.support.design.widget.AppBarLayout>
	</android.support.design.widget.CoordinatorLayout>

其中注意ToolBar的height为 ?attr/actionBarSize ，这样可以使ToolBar的高度和默认的ActionBar一样。还需要注意设置ToolBar的 popupTheme 。
### ToolBar的layout_scrollFlags属性要注意：
<br>
1. scroll: 所有想滚动出屏幕的view都需要设置这个flag- 没有设置这个flag的view将被固定在屏幕顶部。<br>
2. enterAlways: 这个flag让任意向下的滚动都会导致该view变为可见，启用快速“返回模式”。<br>
3. enterAlwaysCollapsed: 当你的视图已经设置minHeight属性又使用此标志时，你的视图只能已最小高度进入，只有当滚动视图到达顶部时才扩大到完整高度。<br>
4. exitUntilCollapsed: 滚动退出屏幕，最后折叠在顶端。

### 滑动
<br>为了使得Toolbar可以滑动，我们必须还得有个条件，就是CoordinatorLayout布局下包裹一个可以滑动的布局，比如 RecyclerView，NestedScrollView(经过测试，ListView，ScrollView不支持)具有滑动效果的组件。并且给这些组件设置如下属性来告诉CoordinatorLayout，该组件是带有滑动行为的组件，然后CoordinatorLayout在接受到滑动时会通知AppBarLayout 中可滑动的Toolbar可以滑出屏幕了。
### 为了使得Toolbar有滑动效果，必须做到如下三点：
1. CoordinatorLayout必须作为整个布局的父布局容器。
2. 给需要滑动的组件设置 app:layout_scrollFlags=”scroll|enterAlways” 属性。
3. 给你的可滑动的组件，也就是RecyclerView 或者 NestedScrollView 设置如下属性：

	 	app:layout_behavior="@string/appbar_scrolling_view_behavior"
		
## TabLayout的使用
TabLayout一般与ViewPager配合使用
	
	<?xml version="1.0" encoding="utf-8"?>
	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/window_background"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBarLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        android:popupTheme="@style/ThemeOverlay.AppCompat.Light"
        app:layout_scrollFlags="scroll|enterAlways"/>

    <android.support.design.widget.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/primary"
        app:tabGravity="fill"
        app:tabIndicatorColor="#fff" //指示器的颜色
        app:tabMode="scrollable" //是否可以左右滚动
        app:tabSelectedTextColor="#fff" //被选中的字体颜色
        app:tabTextAppearance="@style/MyTextAppearance" //文字的字体，默认为全部大写
        app:tabTextColor="#FFF5F5" //默认字体颜色/>
    </android.support.design.widget.AppBarLayout>
    <android.support.v4.view.ViewPager
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
    </android.support.v4.view.ViewPager>
	</android.support.design.widget.CoordinatorLayout>
需要注意TabLayout需要放在APPBarLayout布局中的ToolBar下面，需要给ViewPager设置属性：

	app:layout_behavior="@string/appbar_scrolling_view_behavior"
	
以保证ViewPager中的控件的滚动可以出发AppBarLayout的滚动。
## DrawerLayout与NavigationView侧滑菜单的使用
首先是布局文件，最外层用DrawerLayout包裹：


	<?xml version="1.0" encoding="utf-8"?>
	<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".activity.MainActivity">

    <include layout="@layout/main_content_layout" />

    <android.support.design.widget.NavigationView
        android:id="@+id/navi_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity = "start"
        app:headerLayout="@layout/navigation_head_layout"
        app:menu="@menu/navi_menu"/>
    <android.support.design.widget.FloatingActionButton
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_gravity = "bottom|right"
        android:src="@mipmap/ic_head"
        />

	</android.support.v4.widget.DrawerLayout>


其中NavigationView 中的 android:layout_gravity=”start” 属性来控制抽屉菜单从哪边滑出，一般“start ”从左边滑出，“end”从右边滑出。

这里最主要的两个属性分别是： 
<br>1.app:headerLayout: 给NavigationView添加头部布局 
<br>2.app：menu：给NavigationView添加menu菜单布局
<br>
app:headerLayout布局如下：

	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:background="@color/primary"
    xmlns:app="http://schemas.android.com/apk/res-auto">
   
    <de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/navi_head"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:src="@mipmap/ic_head"
        app:civ_border_width="2dp"
        app:civ_border_color="#FFffff00"
        android:layout_centerInParent="true"/>
	</RelativeLayout>

app：menu 布局如下：

	<?xml version="1.0" encoding="utf-8"?>
	<menu xmlns:android="http://schemas.android.com/apk/res/android"
    >
	<group
    android:checkableBehavior="single">
    <item
        android:id="@+id/navi_menu_1"
        android:title="First"
        />
    <item
        android:id="@+id/navi_menu_2"
        android:title="First"
        />
    <item
        android:id="@+id/navi_menu_3"
        android:title="First"
        />
	</group>
    <item android:title="设置"
          android:id="@+id/sub">
        <menu>
            <item android:title="First" android:id="@+id/sub_1"/>
            <item android:title="First" android:id="@+id/sub_2"/>
        </menu>
    </item>
	</menu>
这里要注意的是这是menu文件，要放在menu文件夹，在group中创建几个item，group的android:checkableBehavior属性有三种：single表示只能单选其中一个item，all表示可以多选，none表示不能被选中，一般使用single属性。
<br>其中group下面的item中的menu是此侧滑菜单的次要选项。他们都可以设置图标icon。
### 代码中控制NavigationView

	drawerLayout = (DrawerLayout) findViewById(R.id.drawer);
        //创建返回键，并实现打开关/闭监听
        ActionBarDrawerToggle mDrawerToggle = new ActionBarDrawerToggle(this, drawerLayout, toolbar, R.string.open, R.string.close);
        mDrawerToggle.syncState();//初始化状态
        drawerLayout.setDrawerListener(mDrawerToggle);
        NavigationView navigationView = (NavigationView) findViewById(R.id.navi_view);
        navigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(MenuItem item) {
                switch (item.getItemId()){
                    case R.id.navi_menu_1:
	//                        ShowToastUtil.showToast(MainActivity.this, "选中1");
                        item.setChecked(true);
                        break;
                    case R.id.navi_menu_2:
                        ShowToastUtil.showToast(MainActivity.this, "选中2");
                        item.setChecked(true);
                        break;
                    case R.id.navi_menu_3:
                        Snackbar.make(toolbar, "Hello", Snackbar.LENGTH_LONG).setAction("NO", new View.OnClickListener() {
                            @Override
                            public void onClick(View v) {
                                ShowToastUtil.showToast(MainActivity.this, "NO");
                            }
                        }).show();
                        item.setChecked(true);
                        break;
                    case R.id.sub_1:
                        ShowToastUtil.showToast(MainActivity.this, "sub_1");
                        break;
                    case R.id.sub_2:
                        ShowToastUtil.showToast(MainActivity.this, "sub_2");
                        break;

                }
                drawerLayout.closeDrawers();
                return true;
            }
首先实例化DrawerLayout，然后创建打开关闭侧滑菜单的按钮ActionBarDrawerToggle并监听它的事件来打开和关闭侧滑菜单。再实例化NavigationView，并监听菜单中的点击事件，可以在点击item后将其设置为选中状态

	item.setChecked(true);
    
最后记得在点击后关闭侧滑菜单：

	drawerLayout.closeDrawers();

关于NavigationView中item的字体颜色和icon选中状态颜色是去当前主题theme中的

	<--正常状态下字体颜色和icon颜色-->
	<item name="android:textColorPrimary">@android:color/darker_gray</item>
	<--选中状态icon的颜色和字体颜色-->
	<item name="colorPrimary">@color/accent_material_light</item>
当然你可以通过如下方法或者属性来改变这一状态：

setItemBackgroundResource(int)：给menu设置背景资源，对应的属性app:itemBackground
setItemIconTintList(ColorStateList)：给menu的icon设置颜色，对应的属性app:itemIconTint
setItemTextColor(ColorStateList)：给menu的item设置字体颜色，对应的属性app:itemTextColor



## 在ToolBar上设置菜单按钮
新建一个menu布局

	<?xml version="1.0" encoding="utf-8"?>
	<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
	<item
    android:id="@+id/action_find"
    android:icon="@drawable/abc_ic_search_api_mtrl_alpha"
    android:orderInCategory="80"
    android:title="搜索"
    app:showAsAction="always"
    />
    <item
        android:id="@+id/action_share"
        android:icon="@drawable/abc_ic_menu_share_mtrl_alpha"
        android:orderInCategory="90"
        android:title="分享"
        app:showAsAction="always"
        />
    <item
        android:id="@+id/action_setting"
        android:orderInCategory="100"
        android:title="设置"
        app:showAsAction = "never"
        />
	</menu>
在MainActivity中设置menu的初始化和点击事件：

	@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
	switch (item.getItemId()){
            case android.R.id.home:
                drawerLayout.openDrawer(GravityCompat.START);
                return true;
        }
        return super.onOptionsItemSelected(item);
    }
记得在这里设置监听android.R.id.home的事件，以关闭侧滑菜单。