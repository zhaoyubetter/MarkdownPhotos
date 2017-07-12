# ViewPager源码记录
## 1. onMeasure方法


>**onMeasure测量，用来测量Viewpager自己**

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    // For simple implementation, our internal size is always 0.
    // We depend on the container to specify the layout size of
    // our view.  We can't really know what it is since we will be
    // adding and removing different arbitrary views and do not
    // want the layout to change as this happens.
    
    // 测量自身的大小根据父的大小来设置自己的大小
    setMeasuredDimension(getDefaultSize(0, widthMeasureSpec),
            getDefaultSize(0, heightMeasureSpec));

    final int measuredWidth = getMeasuredWidth();
    final int maxGutterSize = measuredWidth / 10;
    mGutterSize = Math.min(maxGutterSize, mDefaultGutterSize);

    // Children are just made to fill our space.
    // 获取宽高
    int childWidthSize = measuredWidth - getPaddingLeft() - getPaddingRight();
    int childHeightSize = getMeasuredHeight() - getPaddingTop() - getPaddingBottom();
    
    ...
```

>**测量孩子的(这个比较复杂)**

```java
/*
 * Make sure all children have been properly measured. Decor views first.
 * Right now we cheat and make this less complicated by assuming decor
 * views won't intersect. We will pin to edges based on gravity.
 */
int size = getChildCount();
for (int i = 0; i < size; ++i) {
    final View child = getChildAt(i);
    if (child.getVisibility() != GONE) {
        final LayoutParams lp = (LayoutParams) child.getLayoutParams();
        if (lp != null && lp.isDecor) {
            final int hgrav = lp.gravity & Gravity.HORIZONTAL_GRAVITY_MASK;
            final int vgrav = lp.gravity & Gravity.VERTICAL_GRAVITY_MASK;
            int widthMode = MeasureSpec.AT_MOST;
            int heightMode = MeasureSpec.AT_MOST;
            boolean consumeVertical = vgrav == Gravity.TOP || vgrav == Gravity.BOTTOM;
            boolean consumeHorizontal = hgrav == Gravity.LEFT || hgrav == Gravity.RIGHT;

            if (consumeVertical) {
                widthMode = MeasureSpec.EXACTLY;
            } else if (consumeHorizontal) {
                heightMode = MeasureSpec.EXACTLY;
            }

            int widthSize = childWidthSize;
            int heightSize = childHeightSize;
            if (lp.width != LayoutParams.WRAP_CONTENT) {
                widthMode = MeasureSpec.EXACTLY;
                if (lp.width != LayoutParams.MATCH_PARENT) {
                    widthSize = lp.width;
                }
            }
            if (lp.height != LayoutParams.WRAP_CONTENT) {
                heightMode = MeasureSpec.EXACTLY;
                if (lp.height != LayoutParams.MATCH_PARENT) {
                    heightSize = lp.height;
                }
            }
            final int widthSpec = MeasureSpec.makeMeasureSpec(widthSize, widthMode);
            final int heightSpec = MeasureSpec.makeMeasureSpec(heightSize, heightMode);
            child.measure(widthSpec, heightSpec);

            if (consumeVertical) {
                childHeightSize -= child.getMeasuredHeight();
            } else if (consumeHorizontal) {
                childWidthSize -= child.getMeasuredWidth();
            }
        }
    }
}
```

### 1.1 测量DecorView

如果viewPager在layout一下定义：即没有直接添加子View：
	
		
	<android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

`onMeasure()` 中 childsize 为 0，将不会执行上面那得 `for` 循环那段；

### 1.2 从 adapter 获取 childview并测量

没有直接在布局文件中添加view的话，我们直接跳过`测量decor的for循环`，来到`onMeasure()` 方法的后面：

```java
// 组装 measureSpec
mChildWidthMeasureSpec = MeasureSpec.makeMeasureSpec(childWidthSize, MeasureSpec.EXACTLY);
mChildHeightMeasureSpec = MeasureSpec.makeMeasureSpec(childHeightSize, MeasureSpec.EXACTLY);

// Make sure we have created all fragments that we need to have shown.
// 1.从adapater中获取childView
mInLayout = true;
populate();
mInLayout = false;

// 2. 测量非decor的childView
// Page views next.
size = getChildCount();
for (int i = 0; i < size; ++i) {
    final View child = getChildAt(i);
    if (child.getVisibility() != GONE) {
        if (DEBUG) {
            Log.v(TAG, "Measuring #" + i + " " + child + ": " + mChildWidthMeasureSpec);
        }

        final LayoutParams lp = (LayoutParams) child.getLayoutParams();
        if (lp == null || !lp.isDecor) {
            final int widthSpec = MeasureSpec.makeMeasureSpec(
                    (int) (childWidthSize * lp.widthFactor), MeasureSpec.EXACTLY);
            child.measure(widthSpec, mChildHeightMeasureSpec);
        }
    }
}

```

#### 1.2.1 populate()
populate也是一个超级复杂的方法:

```java
void populate(int newCurrentItem) {
    ItemInfo oldCurInfo = null;
    if (mCurItem != newCurrentItem) {
        oldCurInfo = infoForPosition(mCurItem);
        mCurItem = newCurrentItem;
    }

    if (mAdapter == null) {
        sortChildDrawingOrder();
        return;
    }

    if (mPopulatePending) {
        if (DEBUG) Log.i(TAG, "populate is pending, skipping for now...");
        sortChildDrawingOrder();
        return;
    }

    // 检查避免绘制
    if (getWindowToken() == null) {
        return;
    }

    mAdapter.startUpdate(this);

    ...
```