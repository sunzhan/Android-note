# TabLayout+ViewPager+Fragment组合用法
### 布局文件：
	
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <android.support.design.widget.TabLayout
        android:id="@+id/wx_tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/primary"
        app:tabGravity="fill"
        app:tabIndicatorColor="#fff"
        app:tabMode="scrollable"
        app:tabSelectedTextColor="#fff"
        app:tabTextAppearance="@style/MyTextAppearance"
        app:tabTextColor="#FFF5F5" />

    <android.support.v4.view.ViewPager
        android:id="@+id/wx_pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:background="#fff"></android.support.v4.view.ViewPager>
	</LinearLayout>

线性布局，上面是TabLayout，下面是ViewPager，需要注意的是TabLayout的字体默认是全部大写的，需要
设置其字体：

	app:tabTextAppearance="@style/MyTextAppearance"
	
	<style name="MyTextAppearance" parent="TextAppearance.Design.Tab">
        <item name="android:textAllCaps">false</item>
    </style>

还需要一个放置ViewPager内容页的Fragment：
	
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
	<TextView
    android:id="@+id/wxcontent_text"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
	</LinearLayout>

### ViewPager内容页Fragment：

	import android.os.Bundle;
	import android.support.annotation.Nullable;
	import android.support.v4.app.Fragment;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;
	import android.widget.TextView;
	
	import com.sun.wxtest_mvp.R;
	
	import butterknife.BindView;
	import butterknife.ButterKnife;
	
	/**
	 * Created by Administrator on 2016/8/3.
	 */
	public class WXContentFragment extends Fragment {
    @BindView(R.id.wxcontent_text)
    TextView wxcontentText;

    public static Fragment getInstance(Bundle bundle){
        WXContentFragment wxContentFragment = new WXContentFragment();
        wxContentFragment.setArguments(bundle);
        return wxContentFragment;
    }
    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.wxcontent_layout, container, false);
        ButterKnife.bind(this, view);
        return view;
    }

    @Override
    public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        wxcontentText.setText(getArguments().getString("title"));
    }
	}

这里主要是注意getInstance的写法，需要传入一个Bundle，以便Adapter传入数据。
### Adapter的写法

	import android.os.Bundle;
	import android.support.v4.app.Fragment;
	import android.support.v4.app.FragmentManager;
	import android.support.v4.app.FragmentPagerAdapter;
	import android.view.ViewGroup;
	
	import com.sun.wxtest_mvp.bean.SortResultBean;
	import com.sun.wxtest_mvp.view.fragment.WXContentFragment;
	
	import java.util.ArrayList;
	
	/**
	 * Created by Administrator on 2016/8/3.
	 */
	public class WXPagerAdapter extends FragmentPagerAdapter{
    private ArrayList<SortResultBean> sortResultBeenList;
    public WXPagerAdapter(FragmentManager fm, ArrayList<SortResultBean> sortResultBeenList) {
        super(fm);
        this.sortResultBeenList = sortResultBeenList;
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return sortResultBeenList.get(position).getName();
    }

    @Override
    public Object instantiateItem(ViewGroup container, int position) {
        return super.instantiateItem(container, position);
    }

    @Override
    public Fragment getItem(int position) {
        Bundle bundle = new Bundle();
        bundle.putString("title", sortResultBeenList.get(position).getName());
        bundle.putString("cid", sortResultBeenList.get(position).getCid());
        return WXContentFragment.getInstance(bundle);
    }

    @Override
    public int getCount() {
        return sortResultBeenList.size();
    }
	}
Adapter是最为重要的部分。
<br>定义WXPagerAdapter继承于**FragmentPagerAdapter**， 实现必要的方法，这里要手动添加
**getPageTitle** 和 **instantiateItem**方法。
<br>在构造器中初始化传入的List集合。
<br>这里最为重要的是getItem方法，这里要返回在ViewPager内容中的Fragment。使用之前在
WXContentFragment中定义的getInstance方法，用Bundle绑定相应的数据后返回。
### 使用TabLayout
在需要显示TabLayout的地方（Activity或Fragment）初始化ViewPager和TabLayout，并给ViewPager
设置Adapter，给TabLayout设置ViewPager：

	wxPager.setAdapter(new WXPagerAdapter(((AppCompatActivity) getActivity())
                .getSupportFragmentManager(), sortResultBeanList));
    wxTabs.setupWithViewPager(wxPager);


**我这里是在Fragment中显示的，因为Adapter需要传入getSupportFragmentManager，而在Fragment中
只有getFragmentManager，所以要先getActivity，这里将其强转为AppCompatActivity，然后才能得到
getSupportFragmentManager。**
<br>最后调用TabLayout的setupWithViewPager方法传入ViewPager。

