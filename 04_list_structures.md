# Modern List Structures — Android UI Encyclopedia

> **File:** `04_list_structures.md` | **API Target:** 35

---

## Table of Contents

1. [Standard RecyclerView List](#1-standard-recyclerview-list)
2. [Grid RecyclerView](#2-grid-recyclerview)
3. [Staggered Grid](#3-staggered-grid)
4. [Horizontal Lists](#4-horizontal-lists)
5. [Nested RecyclerViews](#5-nested-recyclerview)
6. [Expandable Lists](#6-expandable-lists)
7. [Sectioned Lists with Headers](#7-sectioned-lists-with-sticky-headers)
8. [Infinite Scrolling / Pagination](#8-infinite-scrolling--pagination)
9. [Chat / Messaging Lists](#9-chat--messaging-lists)
10. [Feed Layouts](#10-feed-layouts)
11. [Timeline Layouts](#11-timeline-layouts)
12. [Settings / Preference Lists](#12-settings--preference-lists)
13. [Grouped / Categorized Lists](#13-grouped--categorized-lists)
14. [Swipe Actions List](#14-swipe-actions-list)
15. [Selection Mode Lists](#15-selection-mode-lists)

---

## 1. Standard RecyclerView List

### Description
A vertical scrolling list of homogenous items — the most common list pattern.

### `res/layout/activity_list.xml`
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:title="Items" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.swiperefreshlayout.widget.SwipeRefreshLayout
        android:id="@+id/swipe_refresh"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/recycler"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:clipToPadding="false"
            android:paddingBottom="80dp" />
    </androidx.swiperefreshlayout.widget.SwipeRefreshLayout>

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        app:srcCompat="@drawable/ic_add" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### `res/layout/item_list_standard.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="16dp"
    android:layout_marginVertical="4dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="12dp"
    app:cardElevation="1dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="12dp">

        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/img_thumbnail"
            android:layout_width="56dp"
            android:layout_height="56dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent" />

        <TextView
            android:id="@+id/tv_title"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            app:layout_constraintStart_toEndOf="@id/img_thumbnail"
            app:layout_constraintEnd_toStartOf="@id/tv_time"
            app:layout_constraintTop_toTopOf="parent"
            android:layout_marginStart="12dp" />

        <TextView
            android:id="@+id/tv_subtitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSurfaceVariant"
            app:layout_constraintStart_toEndOf="@id/img_thumbnail"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tv_title"
            android:layout_marginStart="12dp" />

        <TextView
            android:id="@+id/tv_time"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceLabelSmall"
            android:textColor="?attr/colorOnSurfaceVariant"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Adapter (full pattern)
```java
public class StandardListAdapter extends RecyclerView.Adapter<StandardListAdapter.VH> {

    public interface Callback {
        void onClick(ListItem item);
        void onLongClick(ListItem item);
    }

    private List<ListItem> items = new ArrayList<>();
    private Callback callback;

    public void setCallback(Callback callback) { this.callback = callback; }

    public void submitList(List<ListItem> newItems) {
        DiffUtil.DiffResult result = DiffUtil.calculateDiff(new DiffUtil.Callback() {
            @Override public int getOldListSize() { return items.size(); }
            @Override public int getNewListSize() { return newItems.size(); }
            @Override public boolean areItemsSame(int op, int np) {
                return items.get(op).getId().equals(newItems.get(np).getId());
            }
            @Override public boolean areContentsSame(int op, int np) {
                return items.get(op).equals(newItems.get(np));
            }
        });
        items = new ArrayList<>(newItems);
        result.dispatchUpdatesTo(this);
    }

    @NonNull @Override
    public VH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new VH(LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_list_standard, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull VH holder, int position) {
        ListItem item = items.get(position);
        holder.bind(item, callback);
    }

    @Override public int getItemCount() { return items.size(); }

    static class VH extends RecyclerView.ViewHolder {
        ShapeableImageView img;
        TextView title, subtitle, time;

        VH(@NonNull View v) {
            super(v);
            img = v.findViewById(R.id.img_thumbnail);
            title = v.findViewById(R.id.tv_title);
            subtitle = v.findViewById(R.id.tv_subtitle);
            time = v.findViewById(R.id.tv_time);
        }

        void bind(ListItem item, Callback callback) {
            title.setText(item.getTitle());
            subtitle.setText(item.getSubtitle());
            time.setText(item.getTime());
            Glide.with(itemView).load(item.getAvatarUrl())
                .placeholder(R.drawable.ic_avatar).into(img);
            itemView.setOnClickListener(v -> {
                if (callback != null) callback.onClick(item);
            });
            itemView.setOnLongClickListener(v -> {
                if (callback != null) callback.onLongClick(item);
                return true;
            });
        }
    }
}
```

---

## 2. Grid RecyclerView

### `res/layout/item_grid_card.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="12dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/img_grid"
            android:layout_width="match_parent"
            android:layout_height="140dp"
            android:scaleType="centerCrop" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">

            <TextView
                android:id="@+id/tv_grid_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleSmall"
                android:maxLines="2"
                android:ellipsize="end" />

            <TextView
                android:id="@+id/tv_grid_price"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorPrimary" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Setup
```java
RecyclerView grid = findViewById(R.id.recycler);
int spanCount = calculateSpanCount(); // dynamic based on screen width
GridLayoutManager glm = new GridLayoutManager(this, spanCount);
grid.setLayoutManager(glm);
grid.addItemDecoration(new GridSpacingDecoration(dpToPx(8)));
grid.setAdapter(new GridAdapter(items));

// Responsive span count
private int calculateSpanCount() {
    DisplayMetrics metrics = getResources().getDisplayMetrics();
    float dpWidth = metrics.widthPixels / metrics.density;
    return (int) (dpWidth / 180); // approx 180dp per card
}

// Span full width for headers
glm.setSpanSizeLookup(new GridLayoutManager.SpanSizeLookup() {
    @Override
    public int getSpanSize(int position) {
        return adapter.getItemViewType(position) == TYPE_HEADER ? spanCount : 1;
    }
});
```

---

## 3. Staggered Grid

### Description
Variable-height grid — like Pinterest. Each column fills independently.

### `res/layout/item_staggered.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="16dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/img_staggered"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true"
            android:scaleType="fitCenter" />

        <TextView
            android:id="@+id/tv_staggered_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="8dp"
            android:textAppearance="?attr/textAppearanceBodyMedium" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Setup
```java
StaggeredGridLayoutManager sglm = new StaggeredGridLayoutManager(
    2, StaggeredGridLayoutManager.VERTICAL);
sglm.setGapStrategy(StaggeredGridLayoutManager.GAP_HANDLING_MOVE_ITEMS_BETWEEN_SPANS);
recyclerView.setLayoutManager(sglm);

// Gap fix when scrolling back to top
recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrolled(@NonNull RecyclerView rv, int dx, int dy) {
        if (dy < 0) sglm.invalidateSpanAssignments();
    }
});
```

### Staggered Adapter (varying heights via random or data-driven)
```java
public class StaggeredAdapter extends RecyclerView.Adapter<StaggeredAdapter.VH> {
    private final List<StaggeredItem> items;

    @Override
    public void onBindViewHolder(@NonNull VH holder, int position) {
        StaggeredItem item = items.get(position);
        // Use actual image aspect ratio
        ViewGroup.LayoutParams lp = holder.img.getLayoutParams();
        lp.height = (int) (holder.img.getWidth() * item.getAspectRatio());
        holder.img.setLayoutParams(lp);
        Glide.with(holder.itemView).load(item.getImageUrl()).into(holder.img);
        holder.title.setText(item.getTitle());
    }
    // ...other overrides
}
```

---

## 4. Horizontal Lists

### Description
Scrollable horizontal strips — used in carousels, category chips, story rows.

### XML
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/horizontal_recycler"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clipToPadding="false"
    android:paddingHorizontal="16dp"
    android:orientation="horizontal"
    app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager" />
```

### Java Setup
```java
LinearLayoutManager llm = new LinearLayoutManager(this, LinearLayoutManager.HORIZONTAL, false);
recyclerView.setLayoutManager(llm);

// Snap to center (carousel behavior)
LinearSnapHelper snapHelper = new LinearSnapHelper();
snapHelper.attachToRecyclerView(recyclerView);

// Pager-style snap
PagerSnapHelper pagerSnap = new PagerSnapHelper();
pagerSnap.attachToRecyclerView(recyclerView);
```

### `res/layout/item_horizontal_card.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="160dp"
    android:layout_height="wrap_content"
    android:layout_marginEnd="8dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/img_h"
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:scaleType="centerCrop" />

        <TextView
            android:id="@+id/tv_h_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="8dp"
            android:maxLines="2"
            android:textAppearance="?attr/textAppearanceLabelMedium" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 5. Nested RecyclerView

### Description
A RecyclerView within another RecyclerView — used for category-based horizontal strips inside a vertical list (e.g., Google Play, Netflix).

### `res/layout/item_section.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:paddingVertical="8dp">

    <TextView
        android:id="@+id/tv_section_title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingHorizontal="16dp"
        android:paddingBottom="8dp"
        android:textAppearance="?attr/textAppearanceTitleMedium" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_inner"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:clipToPadding="false"
        android:paddingHorizontal="16dp"
        android:orientation="horizontal" />
</LinearLayout>
```

### Outer Adapter (Java)
```java
public class SectionAdapter extends RecyclerView.Adapter<SectionAdapter.SectionVH> {
    private final List<Section> sections;
    private final RecyclerView.RecycledViewPool sharedPool;

    public SectionAdapter(List<Section> sections) {
        this.sections = sections;
        this.sharedPool = new RecyclerView.RecycledViewPool();
        this.sharedPool.setMaxRecycledViews(0, 20);
    }

    @NonNull @Override
    public SectionVH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new SectionVH(LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_section, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull SectionVH holder, int position) {
        holder.bind(sections.get(position), sharedPool);
    }

    @Override public int getItemCount() { return sections.size(); }

    static class SectionVH extends RecyclerView.ViewHolder {
        TextView sectionTitle;
        RecyclerView innerRv;

        SectionVH(@NonNull View v) {
            super(v);
            sectionTitle = v.findViewById(R.id.tv_section_title);
            innerRv = v.findViewById(R.id.rv_inner);
        }

        void bind(Section section, RecyclerView.RecycledViewPool pool) {
            sectionTitle.setText(section.getTitle());
            LinearLayoutManager llm = new LinearLayoutManager(
                itemView.getContext(), LinearLayoutManager.HORIZONTAL, false);
            llm.setInitialPrefetchItemCount(4);
            innerRv.setLayoutManager(llm);
            innerRv.setRecycledViewPool(pool);
            innerRv.setAdapter(new HorizontalItemAdapter(section.getItems()));
        }
    }
}
```

### Activity (Java)
```java
RecyclerView outerRv = findViewById(R.id.outer_recycler);
outerRv.setLayoutManager(new LinearLayoutManager(this));
outerRv.setItemViewCacheSize(10);
outerRv.setAdapter(new SectionAdapter(sections));
```

---

## 6. Expandable Lists

### Java Adapter
```java
public class ExpandableAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_PARENT = 0;
    private static final int TYPE_CHILD = 1;

    private final List<Object> displayList = new ArrayList<>();
    private final List<ParentItem> parentItems;

    public ExpandableAdapter(List<ParentItem> parentItems) {
        this.parentItems = parentItems;
        displayList.addAll(parentItems);
    }

    @Override public int getItemViewType(int pos) {
        return displayList.get(pos) instanceof ParentItem ? TYPE_PARENT : TYPE_CHILD;
    }

    @NonNull @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup p, int type) {
        LayoutInflater inf = LayoutInflater.from(p.getContext());
        if (type == TYPE_PARENT)
            return new ParentVH(inf.inflate(R.layout.item_parent, p, false));
        return new ChildVH(inf.inflate(R.layout.item_child, p, false));
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int pos) {
        if (holder instanceof ParentVH) {
            ParentItem parent = (ParentItem) displayList.get(pos);
            ((ParentVH) holder).bind(parent, v -> toggleExpand(parent, pos));
        } else {
            ((ChildVH) holder).bind((ChildItem) displayList.get(pos));
        }
    }

    private void toggleExpand(ParentItem parent, int parentPos) {
        if (parent.isExpanded()) {
            // Collapse
            int childCount = parent.getChildren().size();
            for (int i = 0; i < childCount; i++) displayList.remove(parentPos + 1);
            notifyItemRangeRemoved(parentPos + 1, childCount);
        } else {
            // Expand
            int insertPos = parentPos + 1;
            displayList.addAll(insertPos, parent.getChildren());
            notifyItemRangeInserted(insertPos, parent.getChildren().size());
        }
        parent.setExpanded(!parent.isExpanded());
        notifyItemChanged(parentPos);
    }

    @Override public int getItemCount() { return displayList.size(); }

    static class ParentVH extends RecyclerView.ViewHolder {
        TextView title;
        ImageView arrow;

        ParentVH(@NonNull View v) {
            super(v);
            title = v.findViewById(R.id.tv_parent_title);
            arrow = v.findViewById(R.id.iv_arrow);
        }

        void bind(ParentItem item, View.OnClickListener toggleClick) {
            title.setText(item.getTitle());
            arrow.setRotation(item.isExpanded() ? 180f : 0f);
            itemView.setOnClickListener(toggleClick);
        }
    }

    static class ChildVH extends RecyclerView.ViewHolder {
        TextView title;
        ChildVH(@NonNull View v) {
            super(v);
            title = v.findViewById(R.id.tv_child_title);
        }
        void bind(ChildItem item) { title.setText(item.getTitle()); }
    }
}
```

---

## 7. Sectioned Lists with Sticky Headers

### Java — Sticky Header using ConcatAdapter
```java
// Section Header Adapter
public class HeaderAdapter extends RecyclerView.Adapter<HeaderAdapter.HeaderVH> {
    private final String title;
    public HeaderAdapter(String title) { this.title = title; }

    @NonNull @Override
    public HeaderVH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new HeaderVH(LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_section_header, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull HeaderVH holder, int pos) {
        holder.title.setText(title);
    }

    @Override public int getItemCount() { return 1; }

    static class HeaderVH extends RecyclerView.ViewHolder {
        TextView title;
        HeaderVH(@NonNull View v) { super(v); title = v.findViewById(R.id.tv_header); }
    }
}

// Combine with ConcatAdapter
ConcatAdapter concatAdapter = new ConcatAdapter(
    new HeaderAdapter("Today"),
    new ItemAdapter(todayItems),
    new HeaderAdapter("Yesterday"),
    new ItemAdapter(yesterdayItems),
    new HeaderAdapter("This Week"),
    new ItemAdapter(weekItems)
);
recyclerView.setAdapter(concatAdapter);
```

### `res/layout/item_section_header.xml`
```xml
<TextView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/tv_header"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:paddingHorizontal="16dp"
    android:paddingVertical="8dp"
    android:background="?attr/colorSurfaceVariant"
    android:textAppearance="?attr/textAppearanceLabelLarge"
    android:textColor="?attr/colorOnSurfaceVariant" />
```

---

## 8. Infinite Scrolling / Pagination

### Java — Scroll listener approach
```java
public class EndlessScrollListener extends RecyclerView.OnScrollListener {
    private final LinearLayoutManager layoutManager;
    private final int visibleThreshold = 5;
    private boolean isLoading = false;
    private boolean hasMoreData = true;
    private final Runnable loadMore;

    public EndlessScrollListener(LinearLayoutManager lm, Runnable loadMore) {
        this.layoutManager = lm;
        this.loadMore = loadMore;
    }

    @Override
    public void onScrolled(@NonNull RecyclerView rv, int dx, int dy) {
        if (!isLoading && hasMoreData && dy > 0) {
            int totalItems = layoutManager.getItemCount();
            int lastVisible = layoutManager.findLastVisibleItemPosition();
            if (lastVisible + visibleThreshold >= totalItems) {
                isLoading = true;
                loadMore.run();
            }
        }
    }

    public void setLoaded() { isLoading = false; }
    public void setNoMoreData() { hasMoreData = false; }
}

// Usage
EndlessScrollListener scrollListener = new EndlessScrollListener(llm, () -> {
    adapter.addLoadingFooter();
    loadNextPage(currentPage++, result -> {
        adapter.removeLoadingFooter();
        adapter.addItems(result.getItems());
        scrollListener.setLoaded();
        if (!result.hasNextPage()) scrollListener.setNoMoreData();
    });
});
recyclerView.addOnScrollListener(scrollListener);
```

### Footer Loader Item
```java
// Add to multi-type adapter
private static final int TYPE_LOADING = 99;

// In adapter
public void addLoadingFooter() {
    items.add(new LoadingItem());
    notifyItemInserted(items.size() - 1);
}

public void removeLoadingFooter() {
    int pos = items.size() - 1;
    if (items.get(pos) instanceof LoadingItem) {
        items.remove(pos);
        notifyItemRemoved(pos);
    }
}
```

### `res/layout/item_loading_footer.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center"
    android:padding="16dp">

    <com.google.android.material.progressindicator.CircularProgressIndicator
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:indicatorSize="32dp"
        app:trackThickness="3dp"
        android:indeterminate="true" />
</LinearLayout>
```

---

## 9. Chat / Messaging Lists

### Description
Reverse-scrolling list with left/right aligned message bubbles.

### XML Setup
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/rv_chat"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1"
    android:clipToPadding="false"
    android:paddingHorizontal="8dp"
    android:paddingVertical="4dp"
    android:transcriptMode="alwaysScroll" />
```

### Java Setup
```java
LinearLayoutManager llm = new LinearLayoutManager(this);
llm.setStackFromEnd(true); // start from bottom
llm.setReverseLayout(false);
chatRv.setLayoutManager(llm);
```

### `res/layout/item_message_sent.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:paddingVertical="2dp">

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end"
        android:layout_marginStart="48dp"
        android:layout_marginEnd="8dp"
        android:orientation="vertical"
        android:background="@drawable/bg_bubble_sent"
        android:padding="8dp">

        <TextView
            android:id="@+id/tv_message"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="@color/white"
            android:textAppearance="?attr/textAppearanceBodyMedium" />

        <TextView
            android:id="@+id/tv_time"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="end"
            android:textColor="#B3FFFFFF"
            android:textAppearance="?attr/textAppearanceLabelSmall" />
    </LinearLayout>
</FrameLayout>
```

### `res/drawable/bg_bubble_sent.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="?attr/colorPrimary" />
    <corners
        android:topLeftRadius="16dp"
        android:topRightRadius="16dp"
        android:bottomLeftRadius="16dp"
        android:bottomRightRadius="4dp" />
</shape>
```

### `res/layout/item_message_received.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:paddingVertical="2dp">

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="start"
        android:layout_marginStart="8dp"
        android:layout_marginEnd="48dp"
        android:orientation="vertical"
        android:background="@drawable/bg_bubble_received"
        android:padding="8dp">

        <TextView
            android:id="@+id/tv_message_r"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="?attr/colorOnSurfaceVariant" />

        <TextView
            android:id="@+id/tv_time_r"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="end"
            android:textAppearance="?attr/textAppearanceLabelSmall" />
    </LinearLayout>
</FrameLayout>
```

### Chat Adapter (Java)
```java
public class ChatAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_SENT = 1;
    private static final int TYPE_RECEIVED = 2;

    private final List<Message> messages;
    private final String currentUserId;

    public ChatAdapter(List<Message> messages, String currentUserId) {
        this.messages = messages;
        this.currentUserId = currentUserId;
    }

    @Override public int getItemViewType(int pos) {
        return messages.get(pos).getSenderId().equals(currentUserId)
            ? TYPE_SENT : TYPE_RECEIVED;
    }

    @NonNull @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup p, int type) {
        LayoutInflater inf = LayoutInflater.from(p.getContext());
        if (type == TYPE_SENT)
            return new SentVH(inf.inflate(R.layout.item_message_sent, p, false));
        return new ReceivedVH(inf.inflate(R.layout.item_message_received, p, false));
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int pos) {
        Message msg = messages.get(pos);
        if (holder instanceof SentVH) ((SentVH)holder).bind(msg);
        else ((ReceivedVH)holder).bind(msg);
    }

    @Override public int getItemCount() { return messages.size(); }

    public void addMessage(Message msg) {
        messages.add(msg);
        notifyItemInserted(messages.size() - 1);
    }

    static class SentVH extends RecyclerView.ViewHolder {
        TextView message, time;
        SentVH(@NonNull View v) {
            super(v);
            message = v.findViewById(R.id.tv_message);
            time = v.findViewById(R.id.tv_time);
        }
        void bind(Message m) {
            message.setText(m.getText());
            time.setText(m.getFormattedTime());
        }
    }

    static class ReceivedVH extends RecyclerView.ViewHolder {
        TextView message, time;
        ReceivedVH(@NonNull View v) {
            super(v);
            message = v.findViewById(R.id.tv_message_r);
            time = v.findViewById(R.id.tv_time_r);
        }
        void bind(Message m) {
            message.setText(m.getText());
            time.setText(m.getFormattedTime());
        }
    }
}
```

---

## 10. Feed Layouts

### Description
Social-media style vertical feed with mixed content types: text posts, image posts, story strips, ad cards.

### Feed Item Types
```java
public class FeedAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_STORIES = 0;
    private static final int TYPE_TEXT_POST = 1;
    private static final int TYPE_IMAGE_POST = 2;
    private static final int TYPE_VIDEO_POST = 3;
    private static final int TYPE_AD = 4;

    @Override
    public int getItemViewType(int pos) {
        FeedItem item = items.get(pos);
        if (item instanceof StoriesRow) return TYPE_STORIES;
        if (item instanceof TextPost) return TYPE_TEXT_POST;
        if (item instanceof ImagePost) return TYPE_IMAGE_POST;
        if (item instanceof VideoPost) return TYPE_VIDEO_POST;
        return TYPE_AD;
    }
    // ... rest of adapter
}
```

### `res/layout/item_feed_image_post.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginVertical="4dp"
    app:cardElevation="0dp"
    app:strokeWidth="1dp"
    app:strokeColor="?attr/colorOutlineVariant">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Header -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:padding="12dp"
            android:gravity="center_vertical">

            <com.google.android.material.imageview.ShapeableImageView
                android:id="@+id/img_avatar"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:scaleType="centerCrop"
                app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle" />

            <LinearLayout
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginStart="8dp"
                android:orientation="vertical">

                <TextView android:id="@+id/tv_username"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelLarge" />

                <TextView android:id="@+id/tv_timestamp"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>

            <ImageView
                android:id="@+id/btn_more"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/ic_more_vert"
                android:padding="4dp" />
        </LinearLayout>

        <!-- Image -->
        <ImageView
            android:id="@+id/img_post"
            android:layout_width="match_parent"
            android:layout_height="300dp"
            android:scaleType="centerCrop" />

        <!-- Actions -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:paddingHorizontal="4dp"
            android:paddingVertical="8dp">

            <ImageView android:id="@+id/btn_like"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:padding="8dp"
                android:src="@drawable/ic_heart_outline" />

            <ImageView android:id="@+id/btn_comment"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:padding="8dp"
                android:src="@drawable/ic_comment" />

            <ImageView android:id="@+id/btn_share"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:padding="8dp"
                android:src="@drawable/ic_share" />

            <View android:layout_width="0dp"
                android:layout_height="1dp"
                android:layout_weight="1" />

            <ImageView android:id="@+id/btn_bookmark"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:padding="8dp"
                android:src="@drawable/ic_bookmark_outline" />
        </LinearLayout>

        <!-- Caption -->
        <TextView android:id="@+id/tv_caption"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:paddingHorizontal="12dp"
            android:paddingBottom="12dp"
            android:textAppearance="?attr/textAppearanceBodyMedium" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 11. Timeline Layouts

### `res/layout/item_timeline.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="8dp">

    <!-- Timeline column -->
    <LinearLayout
        android:layout_width="32dp"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <View
            android:id="@+id/line_top"
            android:layout_width="2dp"
            android:layout_height="16dp"
            android:background="?attr/colorPrimary" />

        <View
            android:id="@+id/dot"
            android:layout_width="12dp"
            android:layout_height="12dp"
            android:background="@drawable/shape_circle_primary" />

        <View
            android:id="@+id/line_bottom"
            android:layout_width="2dp"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:background="?attr/colorPrimary" />
    </LinearLayout>

    <!-- Content -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginStart="8dp"
        android:layout_marginBottom="8dp"
        app:cardCornerRadius="8dp"
        app:cardElevation="1dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="12dp">

            <TextView android:id="@+id/tv_event_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleSmall" />

            <TextView android:id="@+id/tv_event_date"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceLabelSmall"
                android:textColor="?attr/colorOnSurfaceVariant" />

            <TextView android:id="@+id/tv_event_desc"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodySmall" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>
</LinearLayout>
```

---

## 12. Settings / Preference Lists

### Description
Structured settings UI using Jetpack Preference library.

### `build.gradle`
```groovy
implementation 'androidx.preference:preference:1.2.1'
```

### `res/xml/preferences.xml`
```xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <PreferenceCategory
        android:title="Account"
        app:iconSpaceReserved="false">

        <Preference
            android:key="pref_profile"
            android:title="Profile"
            android:summary="Edit your name, photo"
            android:icon="@drawable/ic_person" />

        <Preference
            android:key="pref_email"
            android:title="Email"
            android:summary="john@example.com" />
    </PreferenceCategory>

    <PreferenceCategory
        android:title="Notifications"
        app:iconSpaceReserved="false">

        <SwitchPreferenceCompat
            android:key="pref_notifications"
            android:title="Push notifications"
            android:defaultValue="true" />

        <SwitchPreferenceCompat
            android:key="pref_email_notifications"
            android:title="Email notifications"
            android:defaultValue="false" />
    </PreferenceCategory>

    <PreferenceCategory
        android:title="Appearance"
        app:iconSpaceReserved="false">

        <ListPreference
            android:key="pref_theme"
            android:title="Theme"
            android:entries="@array/theme_entries"
            android:entryValues="@array/theme_values"
            android:defaultValue="system" />
    </PreferenceCategory>

    <PreferenceCategory
        android:title="About"
        app:iconSpaceReserved="false">

        <Preference
            android:key="pref_version"
            android:title="Version"
            android:summary="1.0.0" />

        <Preference
            android:key="pref_oss"
            android:title="Open source licenses" />
    </PreferenceCategory>
</PreferenceScreen>
```

### SettingsFragment (Java)
```java
public class SettingsFragment extends PreferenceFragmentCompat {
    @Override
    public void onCreatePreferences(Bundle savedInstanceState, String rootKey) {
        setPreferencesFromResource(R.xml.preferences, rootKey);

        // Listen to changes
        findPreference("pref_theme").setOnPreferenceChangeListener((pref, value) -> {
            String theme = (String) value;
            switch (theme) {
                case "light":
                    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO);
                    break;
                case "dark":
                    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES);
                    break;
                default:
                    AppCompatDelegate.setDefaultNightMode(
                        AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM);
            }
            return true;
        });

        // Click listener
        findPreference("pref_oss").setOnPreferenceClickListener(pref -> {
            startActivity(new Intent(requireContext(), OssLicensesActivity.class));
            return true;
        });
    }
}
```

---

## 13. Grouped / Categorized Lists

### Java — Category Adapter using ConcatAdapter
```java
List<RecyclerView.Adapter<?>> adapters = new ArrayList<>();
for (Category category : categories) {
    adapters.add(new HeaderAdapter(category.getName()));
    adapters.add(new CategoryItemAdapter(category.getItems()));
}
ConcatAdapter concat = new ConcatAdapter(
    new ConcatAdapter.Config.Builder()
        .setIsolateViewTypes(false)
        .build(),
    adapters);
recyclerView.setAdapter(concat);
```

---

## 14. Swipe Actions List

### Java — ItemTouchHelper with custom swipe UI
```java
ItemTouchHelper.SimpleCallback swipeCallback = new ItemTouchHelper.SimpleCallback(
        0, ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT) {

    @Override public boolean onMove(@NonNull RecyclerView rv,
            @NonNull RecyclerView.ViewHolder vh,
            @NonNull RecyclerView.ViewHolder t) { return false; }

    @Override
    public void onSwiped(@NonNull RecyclerView.ViewHolder vh, int direction) {
        int pos = vh.getAdapterPosition();
        if (direction == ItemTouchHelper.LEFT) {
            // Delete action
            Item removed = items.remove(pos);
            adapter.notifyItemRemoved(pos);
            showUndoSnackbar(removed, pos);
        } else {
            // Archive action
            items.get(pos).setArchived(true);
            adapter.notifyItemChanged(pos);
        }
    }

    @Override
    public void onChildDraw(@NonNull Canvas c, @NonNull RecyclerView rv,
            @NonNull RecyclerView.ViewHolder vh,
            float dX, float dY, int actionState, boolean active) {
        View itemView = vh.itemView;
        Paint paint = new Paint();

        if (dX < 0) {
            // Left swipe — red delete
            paint.setColor(Color.parseColor("#F44336"));
            c.drawRect(itemView.getRight() + dX, itemView.getTop(),
                itemView.getRight(), itemView.getBottom(), paint);
            // Draw delete icon
            Drawable icon = ContextCompat.getDrawable(rv.getContext(), R.drawable.ic_delete);
            int iconMargin = (itemView.getHeight() - icon.getIntrinsicHeight()) / 2;
            int iconTop = itemView.getTop() + iconMargin;
            int iconRight = itemView.getRight() - iconMargin;
            icon.setBounds(iconRight - icon.getIntrinsicWidth(), iconTop,
                iconRight, iconTop + icon.getIntrinsicHeight());
            icon.setTint(Color.WHITE);
            icon.draw(c);
        } else if (dX > 0) {
            // Right swipe — green archive
            paint.setColor(Color.parseColor("#4CAF50"));
            c.drawRect(itemView.getLeft(), itemView.getTop(),
                dX, itemView.getBottom(), paint);
        }
        super.onChildDraw(c, rv, vh, dX, dY, actionState, active);
    }
};

new ItemTouchHelper(swipeCallback).attachToRecyclerView(recyclerView);
```

---

## 15. Selection Mode Lists

### Java — Multi-selection with action mode
```java
public class SelectableAdapter extends RecyclerView.Adapter<SelectableAdapter.VH> {
    private final SparseBooleanArray selectedItems = new SparseBooleanArray();
    private boolean isSelectionMode = false;
    private SelectionCallback callback;

    public interface SelectionCallback {
        void onSelectionChanged(int count);
        void onSelectionStarted();
    }

    public void setSelectionCallback(SelectionCallback cb) { this.callback = cb; }

    @Override
    public void onBindViewHolder(@NonNull VH holder, int position) {
        boolean isSelected = selectedItems.get(position, false);
        holder.checkIcon.setVisibility(isSelectionMode ? View.VISIBLE : View.GONE);
        holder.checkIcon.setChecked(isSelected);
        holder.itemView.setActivated(isSelected);

        holder.itemView.setOnLongClickListener(v -> {
            if (!isSelectionMode) {
                isSelectionMode = true;
                if (callback != null) callback.onSelectionStarted();
            }
            toggleSelection(position);
            return true;
        });

        holder.itemView.setOnClickListener(v -> {
            if (isSelectionMode) toggleSelection(position);
            else { /* normal click */ }
        });
    }

    private void toggleSelection(int position) {
        if (selectedItems.get(position, false)) {
            selectedItems.delete(position);
        } else {
            selectedItems.put(position, true);
        }
        notifyItemChanged(position);
        if (callback != null) callback.onSelectionChanged(selectedItems.size());

        if (selectedItems.size() == 0) {
            isSelectionMode = false;
        }
    }

    public List<Integer> getSelectedPositions() {
        List<Integer> positions = new ArrayList<>();
        for (int i = 0; i < selectedItems.size(); i++) {
            positions.add(selectedItems.keyAt(i));
        }
        return positions;
    }

    public void clearSelection() {
        selectedItems.clear();
        isSelectionMode = false;
        notifyDataSetChanged();
    }
    // ... rest of adapter
}
```
