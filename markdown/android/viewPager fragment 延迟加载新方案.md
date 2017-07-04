## 新方式 - 实现ViewPager下的 Fragment 的延迟加载

### 为什么需要验证加载

>在Viewpager中，默认加载下一页的数据，比如在Page1页面时，会加载下一页的Page2一般我们结合Viewpager、fragment、tablayout一起联合使用；
fragment加载数据时，习惯从 `onViewCreated()` 回调方法中，进行数据的加载（比如：请求网络）,如果在Page1时，这个时候 Page2 的 `onViewCreated()` 会执行，如果用户不进行 Page2 的访问，会造成一定的资源浪费，用户体验不是很好；  
所以，最好的方式是用户滑动具体某个页面时（比如Page3），才去加载（Page3）的页面数据；

### 传统的解决方案

#### 原理：
利用 Fragment 的几个回调方法，`setUserVisibleHint` , `onResume` 并结合全局变量来进行判断；
具体可参考：

[http://www.cnblogs.com/tiantianbyconan/p/4303910.html](http://www.cnblogs.com/tiantianbyconan/p/4303910.html)

#### 隐含的问题：
这样做造成基类，会有大量判断，而且，需求一旦修改要求（如：实时加载数据），造成改动过大；

### 新方案 -> 从PagerAdapter入手

通过对FragmentPagerAdapter 源码进行分析，发现方法`setPrimaryItem`
具体代码如下：

```java
	public void setPrimaryItem(ViewGroup container, int position, Object  	object) {
        Fragment fragment = (Fragment)object;
        if (fragment != mCurrentPrimaryItem) {
            if (mCurrentPrimaryItem != null) {
                mCurrentPrimaryItem.setMenuVisibility(false);
                mCurrentPrimaryItem.setUserVisibleHint(false);
            }
            if (fragment != null) {
                fragment.setMenuVisibility(true);
                fragment.setUserVisibleHint(true);
            }
            mCurrentPrimaryItem = fragment;
        }
    }
		
```

可以发现`setUserVisibleHint` 这个是在 FragmentPagerAdapter 进行调用的；利用 `setPrimaryItem`这个方法，来实现自己的延迟加载Adapter；

#### 实现步骤：

1. 创建延迟加载接口`LazyLoadCallBack`:
	
	``` java
			
	public interface LazyLoadCallBack {
    	void onLoad();
	}
	```
2. 创建自己的验证加载Adapter：

	```java
	public class LazyFragmentPagerAdapter extends FragmentPagerAdapter {
	
	    private List<Fragment> mFragments;
	    private List<CharSequence> mTitles;
	    private int mLastPosition = -1;
	
	    public LazyFragmentPagerAdapter(FragmentManager fm) {
	        super(fm);
	    }
	
	    public LazyFragmentPagerAdapter(FragmentManager fm, List<Fragment> fragments, List<CharSequence> titles) {
	        this(fm);
	        this.mFragments = new ArrayList<>();
	        this.mTitles = new ArrayList<>();
	
	        mFragments.addAll(fragments);
	        mTitles.addAll(titles);
	
	        // 设置Load标记
	        if (mFragments.size() > 0) {
	            for (Fragment f : mFragments) {
	                Bundle arguments = f.getArguments();
	                if (arguments == null) {
	                    arguments = new Bundle();
	                }
	                arguments.putBoolean("load", false);
	                f.setArguments(arguments);
	            }
	        }
	
	        if (mTitles != null && mTitles.size() != mFragments.size()) {
	            throw new IllegalArgumentException("Title size must equals Fragment size");
	        }
	    }
	
	    @Override
	    public CharSequence getPageTitle(int position) {
	        return mTitles.get(position);
	    }
	
	    @Override
	    public Fragment getItem(int position) {
	        return mFragments.get(position);
	    }
	
	    @Override
	    public int getCount() {
	        return mFragments.size();
	    }
	
	    @Override
	    public void setPrimaryItem(ViewGroup container, int position, Object fragment) {
	        super.setPrimaryItem(container, position, fragment);
	        if (fragment instanceof Fragment) {
	            Fragment f = (Fragment) fragment;
	            if (f instanceof LazyLoadCallBack && mLastPosition != position
	                    && f.isResumed()) {
	                mLastPosition = position;
	                if (!f.getArguments().getBoolean("load")) {
	                    f.getArguments().putBoolean("load", true);
	                    ((LazyLoadCallBack) f).onLoad();
	                }
	            }
	        }
	    }
	
	    @Override
	    public void destroyItem(ViewGroup container, int position, Object object) {
	        super.destroyItem(container, position, object);
	        if (object instanceof Fragment) {
	            Bundle arguments = ((Fragment) object).getArguments();
	            if (arguments != null) {
	                arguments.remove("load");
	            }
	        }
	    }
	}
	```

3. 创建ViewPager 的Adapter继承自**步骤2**的Adapter，代码省略;
	
	```java
	PagerAdapter adapter = new LazyFragmentPagerAdapter(getSupportFragmentManager(), fragments, titles);
        viewPager.setAdapter(adapter);
        tabLayout.setupWithViewPager(viewPager);	
	```

4. 如有延迟加载需求，让fragment 实现 `LazyLoadCallBack`，接口，让在其 `onLoad`方法中来进行数据请求；


### 实例代码：

[https://github.com/zhaoyubetter/StateDemo/blob/master/app/src/main/java/state/better/statedemo/activity/lazy/LazyLoadActivity.java](https://github.com/zhaoyubetter/StateDemo/blob/master/app/src/main/java/state/better/statedemo/activity/lazy/LazyLoadActivity.java)





 