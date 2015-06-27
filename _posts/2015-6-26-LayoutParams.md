---
layout: post
title: ListView的LayoutParams设置
---

{% highlight Java %}
// Temp is the root view that was found in the xml
final View temp = createViewFromTag(root, name, attrs, false);
ViewGroup.LayoutParams params = null;
if (root != null) {
    if (DEBUG) {
        System.out.println("Creating params from root: " +
                           root);
    }
    // Create layout params that match root, if supplied
    params = root.generateLayoutParams(attrs);
    if (!attachToRoot) {
        // Set the layout params for temp if we are not
        // attaching. (If we are, we use addView, below)
        temp.setLayoutParams(params);
    }
}
{% endhighlight %}

从上面可以看到，如果在inflate xml文件时，如果没有指定parent的时候，layout_width是不会生效的。

{% highlight Java %}
mInflater.inflate(R.layout.apps_listview_item, parent, false);
{% endhighlight %}

{% highlight XML %}
<com.helen.launcher4.TestLinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="160dp"
android:orientation="horizontal"
android:paddingTop="5dp"
android:paddingBottom="5dp">
{% endhighlight %}

上面的160dp，如果没有指定parent，是不会生效。

当然这里的parent其实就只有一个目的，使layoutparam生效，其他毫无目的，所以随便指定一个view即可，这里android设计的不太好。

如果没有指定parent，layoutparam默认值是什么呢？

Listview是设计成一个可以上下滚动的viewgroup，所以width是占ListView所有的宽度，即MeasureSpec.EXACTLY

height呢？高度是MeasureSpec.UNSPECIFIED, size = 0.

也就是说，高度是随意定的。这会带来一个问题，当子节点使用match_parent的时候，子节点并不知道parent有多少高度，所以子节点的MeasureSpec会是UNSPECIFIED。

GridView在处理UNSPECIFIED的时候，很奇怪，并不是wrap_content。到底怎么处理，下面的文章再来细述。
