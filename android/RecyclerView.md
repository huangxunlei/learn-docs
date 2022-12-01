## 1、动态更改每个item占用的列数



实现效果,每行数据显示动态显示不同的item数量。如图所示：

<img src="../images/spanCount.jpg" style="zoom:20%;" />

> 实现思路，让么个item占用不同的spanCount 对于需要显示 3列的 行，那么每个item 需要占用的空间是2，需要显示2列的行，那么每个item占用的span空降为3。通过动态修改GridLayoutManager的spanSizeLookup对象来实现。
>
> 这里的每个item占用的大小取，不同行的最小公倍数数来划分。
>
> 通过对position  每个不不同行的数量求和；来取余数，根据余数的出现规则进行修改。

代码如下

```kotlin
        val manager = GridLayoutManager(requireContext(), 6)
        manager.spanSizeLookup = object : GridLayoutManager.SpanSizeLookup() {
            override fun getSpanSize(position: Int): Int {
                if ((position) % 5 < 3) {
                    return 2
                } else {
                    return 3
                }
            }
        }
```

```kotlin
private class AdapterTest : RecyclerView.Adapter<AdapterTest.MyViewHolder>() {

    private val list = mutableListOf<String>()


    internal class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val wo: TextView

        init {
            tvText = itemView.findViewById(R.id.tv_text)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyViewHolder {
        val layout = LayoutInflater.from(parent.context).inflate(R.layout.rv_item_text, null)
        val holder = MyViewHolder(layout)
        return holder
    }

    override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        holder.tvText.text = list.get(position)
        
        // 当每行为2个item时，需要居中对齐 item 布局采用FrameLayout
        val parent = holder.tvText.layoutParams as FrameLayout.LayoutParams
        if (position % 5 == 4) {
            parent.gravity = Gravity.LEFT
        }
        if (position % 5 == 3) {
            parent.gravity = Gravity.RIGHT
        }
        holder.tvText.layoutParams = parent
    }

    override fun getItemCount(): Int {
        return list.size
    }

    fun add(list: MutableList<String>) {
        this.list.addAll(list)
        notifyItemRangeInserted(this.list.size - list.size, this.list.size)
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="dsadad" />
</FrameLayout>
```

后续通过查询源码可知：

官方其实已经考虑到这种情况，只是我们不知道而已，官方只是给定了默认的SpanSizeLookup 对象 其中的spanCount为1而已

源码中会对每个item的占用的spanCount 大小进行计算的。源码中会对每个position的spanCount进行缓存

-- SpanSizeLookup部分源码

```java
int getCachedSpanIndex(int position, int spanCount) {
    if (!mCacheSpanIndices) {
        return getSpanIndex(position, spanCount);
    }
    final int existing = mSpanIndexCache.get(position, -1);
    if (existing != -1) {
        return existing;
    }
    final int value = getSpanIndex(position, spanCount);
    mSpanIndexCache.put(position, value);
    return value;
}

/**
         * Returns the final span index of the provided position.
         * <p>
         * If you have a faster way to calculate span index for your items, you should override
         * this method. Otherwise, you should enable span index cache
         * ({@link #setSpanIndexCacheEnabled(boolean)}) for better performance. When caching is
         * disabled, default implementation traverses all items from 0 to
         * <code>position</code>. When caching is enabled, it calculates from the closest cached
         * value before the <code>position</code>.
         * <p>
         * If you override this method, you need to make sure it is consistent with
         * {@link #getSpanSize(int)}. GridLayoutManager does not call this method for
         * each item. It is called only for the reference item and rest of the items
         * are assigned to spans based on the reference item. For example, you cannot assign a
         * position to span 2 while span 1 is empty.
         * <p>
         * Note that span offsets always start with 0 and are not affected by RTL.
         *
         * @param position  The position of the item
         * @param spanCount The total number of spans in the grid
         * @return The final span position of the item. Should be between 0 (inclusive) and
         * <code>spanCount</code>(exclusive)
*/
 public int getSpanIndex(int position, int spanCount) {
            int positionSpanSize = getSpanSize(position);
            if (positionSpanSize == spanCount) {
                return 0; // quick return for full-span items
            }
            int span = 0;
            int startPos = 0;
            // If caching is enabled, try to jump
            if (mCacheSpanIndices) {
                int prevKey = findFirstKeyLessThan(mSpanIndexCache, position);
                if (prevKey >= 0) {
                    span = mSpanIndexCache.get(prevKey) + getSpanSize(prevKey);
                    startPos = prevKey + 1;
                }
            }
            for (int i = startPos; i < position; i++) {
                int size = getSpanSize(i);
                span += size;
                if (span == spanCount) {
                    span = 0;
                } else if (span > spanCount) {
                    // did not fit, moving to next row / column
                    span = size;
                }
            }
            if (span + positionSpanSize <= spanCount) {
                return span;
            }
            return 0;
        }
```





