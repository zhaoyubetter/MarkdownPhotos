# ViewPager源码记录
## onMeasure方法


>**onMeasure测量，用来测量Viewpager自己**

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    // For simple implementation, our internal size is always 0.
    // We depend on the container to specify the layout size of
    // our view.  We can't really know what it is since we will be
    // adding and removing different arbitrary views and do not
    // want the layout to change as this happens.
    // 根据父的大小来设置自己的大小
    setMeasuredDimension(getDefaultSize(0, widthMeasureSpec),
            getDefaultSize(0, heightMeasureSpec));

    final int measuredWidth = getMeasuredWidth();
    final int maxGutterSize = measuredWidth / 10;
    mGutterSize = Math.min(maxGutterSize, mDefaultGutterSize);

    // Children are just made to fill our space.
    // 子的宽高需要减去 padding 区域
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