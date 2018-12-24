# MVP 架构实践

## 前言

## 什么是MVP
MVP是一种android的项目架构，属于Uncle Bob提出的Clean architecture。

![MVC和MVP对比](http://www.jcodecraeer.com/uploads/20150313/1426239008578777.png)

MVP是从MVC架构上演化而来，他们的基本思想有想通的地方：Cotroller和Presenter负责处理逻辑，model负责提供数据，view负责显示和更新ui。

但是mvp和mvc架构在流程上有很大区别，其中最主要的区别是,MVC中model层和view层能够直接通讯，但是在mvp中model层和view智能通过presenter层进行通讯，而不能够直接通讯。

所以，这里mvp就有以下三个优势：

* 解耦性强。视图和数据完全分离，对于团队开发来说，是比较适合的模块化开发。
* 可复用性强。可以抢一个presenter用在多个view上。同时model层数据和数据获取也可以抽取出来。
* 对于单元测试非常便利。如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑。

一般的程序，架构混乱：
![](http://upload-images.jianshu.io/upload_images/2562009-b3b7e5504c8b8bab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

mvp是使用清晰的架构：
![](http://upload-images.jianshu.io/upload_images/2562009-d09c98d10a4543ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Model
	
### View

### Presenter

## 为什么要用MVP

## 怎么使用MVP

## demo

model层 --> NewsListBean.class和NewsListModel.class
一个实体类，一个获取数据方法

contract契约类 --. NewsListContract.class

	public interface NewsListContract {

	    public interface IView {
	        // 显示新闻列表
	        void showNewsList(List<NewsListModel> data);
	        // 更新ui
			void showLoadingView();
	        void hideLoadingView();
	        void showErrorView();
	    }
	
	    public interface IPresenter<IView> {
	        // 获取新闻列表
	        void getNewsList(String type, String id, int startPage);
	    }
	}

presenter --> NewsListPresenter.class
	
	public class NewsListPresenter extends BasePresenter implements NewsListContract.IPresenter<NewsListContract.IView> {
	
	    NewsListContract.IView view;
	    NewsListsModel model;
	
	    public NewsListPresenter (NewsListContract.IView view) {
	        this.view = view;
	        model = new NewsListsModel();
	    }
	
	    @Override
	    public void getNewsList (String type, String id, int startPage) {
	        model.loadNews(type, id, startPage)
	                .subscribeOn(Schedulers.io())
	                .observeOn(AndroidSchedulers.mainThread())
	                .subscribe(new Subscriber<List<NewsListModel>>() {
	                    @Override
	                    public void onCompleted() {
	
	                    }
	
	                    @Override
	                    public void onError(Throwable e) {
	                        view.hideLoadingView();
	                        view.showErrorView();
	                    }
	
	                    @Override
	                    public void onNext(List<NewsListModel> newsListModels) {
	                        view.hideLoadingView();
	                        view.showNewsList(newsListModels);
	                    }
	
	                    @Override
	                    public void onStart() {
	                        super.onStart();
	                        view.showLoadingView();
	                    }
	                });
	    }
	}

view --> NewsFragment.class

	public class NewsFragment extends Fragment implements NewsListContract.IView {
	
	    @Bind(R.id.irc)
	    IRecyclerView irc;
	    @Bind(R.id.loadedTip)
	    LoadingTip loadedTip;
	
	    View rootView;
	
	    NewsListPresenter mPresenter;
	
	    String mNewsID;
	    String mNewsType;
	    int mStartPage;
	
	    List<NewsListModel> mNewsList;
	    NewsListAdapter mNewsListAdapter;
	
	    @Nullable
	    @Override
	    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
	        super.onCreateView(inflater, container, savedInstanceState);
	
	        if (rootView == null) {
	            rootView = inflater.inflate(R.layout.fragment_news, container, false);
	            ButterKnife.bind(this, rootView);
	            mPresenter = new NewsListPresenter(this);
	            initView();
	        }
	
	        ViewGroup parent = (ViewGroup) rootView.getParent();
	        if (parent != null) {
	            parent.removeView(rootView);
	        }
	
	        return rootView;
	    }
	
	    private void initView() {
	        if (getArguments() != null) {
	            mNewsID = getArguments().getString(AppConstants.NEWS_ID);
	            mNewsType = getArguments().getString(AppConstants.NEWS_TYPE);
	        }
	
	        mPresenter.getNewsList(mNewsType, mNewsID, mStartPage);
	
	        irc.setLayoutManager(new LinearLayoutManager(getActivity()));
	        mNewsListAdapter = new NewsListAdapter(getActivity(), mNewsList);
	        irc.setAdapter(mNewsListAdapter);
	    }
	
	    @Override
	    public void showNewsList(List<NewsListModel> data) {
	        if (data != null) {
	            mStartPage += 20;
	            if (mNewsListAdapter.getPageBean().isRefresh()) {
	                irc.setRefreshing(false);
	                mNewsListAdapter.replaceAll(data);
	            } else {
	                if (data.size() > 0) {
	                    irc.setLoadMoreStatus(LoadMoreFooterView.Status.GONE);
	                    mNewsListAdapter.addAll(data);
	                } else {
	                    irc.setLoadMoreStatus(LoadMoreFooterView.Status.THE_END);
	                }
	            }
	        }
	    }
	
	    @Override
	    public void showLoadingView() {
	        Toast.makeText(getActivity(), "Loading...", Toast.LENGTH_SHORT).show();
	    }
	
	    @Override
	    public void hideLoadingView() {
	
	    }
	
	    @Override
	    public void showErrorView() {
	        Toast.makeText(getActivity(), "ERROR...", Toast.LENGTH_SHORT).show();
	    }
	
	    @Override
	    public void onDestroyView() {
	        super.onDestroyView();
	        ButterKnife.unbind(this);
	    }
	}

## 结束语

## 参考