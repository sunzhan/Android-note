# RecycleView加入下拉刷新和上拉加载
## 下拉刷新
在布局文件中，使用SwipeRefreshLayout包裹RecycleView：

	<?xml version="1.0" encoding="utf-8"?>
	<android.support.v4.widget.SwipeRefreshLayout 
	xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/wx_swiperefresh"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/wx_recycleview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

	</android.support.v4.widget.SwipeRefreshLayout>

然后在类中初始化SwipeRefreshLayout，它可以设置这几个属性：

	mSwipeRefresh.setColorSchemeColors(); //进度条的箭头颜色，可以设置4个
	mSwipeRefresh.setRefreshing(true); //刷新状态，true为显示
	mSwipeRefresh.setOnRefreshListener();//设置下拉滑动监听，可以在这写刷新逻辑

**如果想让它在页面刚初始化的时候就显示出来，可以这样：**

	mSwipeRefresh.post(new Runnable() {
            @Override
            public void run() {
                mSwipeRefresh.setRefreshing(true);
            }
        });

## 上拉加载
由于Google没有在RecycleView中加入官方的上拉加载，所以这玩意儿需要自己写。
### 设计思路
写一个上拉加载的FooterView布局，然后在Adapter中加一个FooterViewHolder，然后在onCreateViewHolder
中判断viewType，返回相应的ViewHolder。判断当RecycleView滑动到最后一个item的时候，加载这个footerView；然后在调用RecycleView的类中设置滑动监听，监听滑到
最后一个item的时候开始加载更多数据并add到List中，然后notifyDataSetChanged()。
### 实现步骤
首先定义一个FooterView布局文件，就是一个Progressbar和一个TextView，这里就不贴代码了。然后在
Adapter中，定义一个FooterView的ViewHolder：

	static class FooterViewHolder extends RecyclerView.ViewHolder {
        @BindView(R.id.footer_progress)
        ProgressBar footerProgress;
        @BindView(R.id.footer_text)
        TextView footerText;
        public FooterViewHolder(View itemView) {
            super(itemView);
            ButterKnife.bind(this, itemView);
        }
    }

接着，根据position的位置设置返回类型：

	@Override
    public int getItemViewType(int position) {
        if (position + 1 == getItemCount()) {
            return TYPE_FOOTER;
        } else {
            return TYPE_ITEM;
        }
    }

这里判断当前position，如果等于总item数，就代表正在绘制最后一个item（也就是FooterView）了，
所以此时返回代表FooterView的常量，否则就是没有到最后一个，继续返回普通item常量。
**这里需要注意的是getItemCount()返回的个数要加1，因为多了一个FooterView：**

	@Override
    public int getItemCount() {
        return newsListBeenList.size() + 1;
    }

接下来在onCreateViewHolder中根据viewType返回相应ViewHolder：

	@Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (viewType == TYPE_FOOTER) {
            FooterViewHolder footerViewHolder = new FooterViewHolder(LayoutInflater.from(context)
                    .inflate(R.layout.footer_loadmore, parent, false));
            return footerViewHolder;
        } else {
            MyViewHolder viewHolder = new MyViewHolder(LayoutInflater.from(context)
                    .inflate(R.layout.wx_cardview, parent, false));
            return viewHolder;
        }
    }

在onBindViewHolder中根据不同的ViewHolder设置不同的控件：

	@Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof MyViewHolder) {
            MyViewHolder viewHolder = (MyViewHolder) holder;
            viewHolder.wxTitle.setText(newsListBeenList.get(position).getTitle());
            viewHolder.wxTimeText.setText(newsListBeenList.get(position).getPubTime());
        } else if (holder instanceof FooterViewHolder){
            FooterViewHolder viewHolder = (FooterViewHolder)holder;
            if (loadStatus == PULLUPLOAD) {
                viewHolder.footerText.setText("上拉加载");
            }else {
                viewHolder.footerText.setText("正在加载...");
            }
        }
    }

在Adapter中定义一个记录上拉状态的方法，以便于在上面onBindViewHolder中设置不同的文字内容：

	public void changeLoadStatus(int status){
        loadStatus = status;
        notifyDataSetChanged();
    }

Adapter中的相关逻辑就写完了，接下来开始调用。
<br>在RecycleView处设置滑动监听：

	private void initRecycleView(final WXListAdapter wxListAdapter) {
        wxRecycleview.setOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                LinearLayoutManager layoutManager = (LinearLayoutManager) wxRecycleview.getLayoutManager();
                int totalItemCount = layoutManager.getItemCount();
                int lastVisibleItem = layoutManager.findLastVisibleItemPosition();
                int lastCompletelyVisibleItem = layoutManager.findLastCompletelyVisibleItemPosition();
                Log.d(TAG, "totalItemCount = " + totalItemCount + ", lastVisibleItem = "
						+ lastVisibleItem + ", lastCompletelyVisibleItem = " + lastCompletelyVisibleItem);
                if (newState == RecyclerView.SCROLL_STATE_IDLE  && lastVisibleItem+1 == wxListAdapter.getItemCount()){
                    wxListAdapter.changeLoadStatus(wxListAdapter.LOADINGMORE);
                    showNewsDataPresenter.loadMoreData();
                    wxListAdapter.changeLoadStatus(wxListAdapter.PULLUPLOAD);
                }
            }
            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
            }
        });
    }

先拿到LinearLayoutManager，然后得到lastVisibleItem，判断当滑动状态为RecyclerView.SCROLL_STATE_IDLE并且lastVisibleItem+1等于
总item数（也就是判断当前滑到了最后一个item）时，执行加载更多的业务逻辑。至此，RecycleView的下拉刷新和上拉加载就完成了。
### 总结
下拉刷新没什么说的，主要是上拉加载，实现上拉加载的过程中可以发现，RecycleView.Adapter支持根据不同的ViewHolder绘制相应的
布局样式，主要是通过viewType来判断，所以可以使用此方法在同一个RecycleView中加载不同布局的item，如每个item显示的图片数量不一定
的话就可以定义多个布局和多个ViewHolder，来显示需要的效果。