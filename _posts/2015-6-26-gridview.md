---
layout: post
title: GridView的高度自适应
---

接着上面一篇文章。

当GridView不知道parent高度的时候，也就是MeasureSpec是UNSPECIFIED，这个时候，GridView高度为第一个child的高度，并显示滚动条。

{% highlight Java %}
mItemCount = mAdapter == null ? 0 : mAdapter.getCount();
final int count = mItemCount;
if (count > 0) {
    final View child = obtainView(0, mIsScrap);

    AbsListView.LayoutParams p = (AbsListView.LayoutParams) child.getLayoutParams();
    if (p == null) {
        p = (AbsListView.LayoutParams) generateDefaultLayoutParams();
        child.setLayoutParams(p);
    }
    p.viewType = mAdapter.getItemViewType(0);
    p.forceAdd = true;

    int childHeightSpec = getChildMeasureSpec(
            MeasureSpec.makeMeasureSpec(0, MeasureSpec.UNSPECIFIED), 0, p.height);
    int childWidthSpec = getChildMeasureSpec(
            MeasureSpec.makeMeasureSpec(mColumnWidth, MeasureSpec.EXACTLY), 0, p.width);
    child.measure(childWidthSpec, childHeightSpec);

    childHeight = child.getMeasuredHeight();
    childState = combineMeasuredStates(childState, child.getMeasuredState());

    if (mRecycler.shouldRecycleViewType(p.viewType)) {
        mRecycler.addScrapView(child, -1);
    }
}

if (heightMode == MeasureSpec.UNSPECIFIED) {
    heightSize = mListPadding.top + mListPadding.bottom + childHeight +
            getVerticalFadingEdgeLength() * 2;
}

if (heightMode == MeasureSpec.AT_MOST) {
    int ourSize =  mListPadding.top + mListPadding.bottom;
   
    final int numColumns = mNumColumns;
    for (int i = 0; i < count; i += numColumns) {
        ourSize += childHeight;
        if (i + numColumns < count) {
            ourSize += mVerticalSpacing;
        }
        if (ourSize >= heightSize) {
            ourSize = heightSize;
            break;
        }
    }
    heightSize = ourSize;
}
{% endhighlight %}

我们可以发现，确实是只设置为第一个child的高度，当MeasureSpec是AT_MOST的时候，才会读取所有child的高度。

在网上，搜到了下面的办法。

{% highlight Java %}
heightMeasureSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE, MeasureSpec.AT_MOST); 
{% endhighlight %}

其实这是不对的，MeasureSpec的前两个bits是mode，size的大小不能超过30位。于是，稍微修改，size设置为1000000，应该是足够了，当不足够的时候，你的app如果还没更新早就该被淘汰了。

{% highlight Java %}
 heightMeasureSpec = MeasureSpec.makeMeasureSpec(1000000, MeasureSpec.AT_MOST);
{% endhighlight %}