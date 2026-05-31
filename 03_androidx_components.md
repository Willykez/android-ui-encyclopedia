# AndroidX Components — Android UI Encyclopedia

> **File:** `03_androidx_components.md` | **API Target:** 35

---

## Table of Contents

1. [RecyclerView](#1-recyclerview)
2. [ViewPager2](#2-viewpager2)
3. [SwipeRefreshLayout](#3-swiperefreshlayout)
4. [NestedScrollView](#4-nestedscrollview)
5. [ConstraintLayout](#5-constraintlayout)
6. [MotionLayout](#6-motionlayout)
7. [CoordinatorLayout](#7-coordinatorlayout)
8. [FragmentContainerView](#8-fragmentcontainerview)
9. [DrawerLayout](#9-drawerlayout)
10. [GridLayout](#10-gridlayout)
11. [FlexboxLayout](#11-flexboxlayout)
12. [ViewStub](#12-viewstub)
13. [MergeLayout & Include](#13-mergelayout--include)
14. [SlidingPaneLayout](#14-slidingpanelayout)
15. [LinearLayoutCompat](#15-linearlayoutcompat)
16. [CircularReveal](#16-circularreveal)
17. [Dependencies Reference](#dependencies-reference)

---

## Dependencies Reference

### `build.gradle (app)`
```groovy
dependencies {
    // Core AndroidX
    implementation 'androidx.recyclerview:recyclerview:1.3.2'
    implementation 'androidx.viewpager2:viewpager2:1.1.0'
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.coordinatorlayout:coordinatorlayout:1.2.0'
    implementation 'androidx.drawerlayout:drawerlayout:1.2.0'
    implementation 'androidx.core:core-ktx:1.13.1'
    implementation 'androidx.fragment:fragment:1.8.1'
    implementation 'androidx.slidingpanelayout:slidingpanelayout:1.2.0'

    // Flexbox
    implementation 'com.google.android.flexbox:flexbox:3.0.0'
}
```

---

## 1. RecyclerView

### Description
Efficient scrolling list/grid view that recycles item views. The foundation of all modern Android lists.

### XML Layout
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:clipToPadding="false"
    android:paddingBottom="80dp"
    tools:listitem="@layout/item_card"
    tools:itemCount="6" />
```

### Item Layout — `res/layout/item_card.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="12dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="12dp">

        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/item_image"
            android:layout_width="56dp"
            android:layout_height="56dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle" />

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="12dp"
            android:orientation="vertical">

            <TextView
                android:id="@+id/item_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleMedium" />

            <TextView
                android:id="@+id/item_subtitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodySmall" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Adapter
```java
public class ItemAdapter extends RecyclerView.Adapter<ItemAdapter.ViewHolder> {

    public interface OnItemClickListener {
        void onItemClick(Item item, int position);
        void onItemLongClick(Item item, int position);
    }

    private final List<Item> items;
    private OnItemClickListener listener;

    public ItemAdapter(List<Item> items) {
        this.items = new ArrayList<>(items);
    }

    public void setOnItemClickListener(OnItemClickListener listener) {
        this.listener = listener;
    }

    @NonNull @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_card, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Item item = items.get(position);
        holder.bind(item);
        holder.itemView.setOnClickListener(v -> {
            if (listener != null) listener.onItemClick(item, position);
        });
        holder.itemView.setOnLongClickListener(v -> {
            if (listener != null) listener.onItemLongClick(item, position);
            return true;
        });
    }

    @Override
    public int getItemCount() { return items.size(); }

    // DiffUtil update
    public void updateItems(List<Item> newItems) {
        DiffUtil.DiffResult result = DiffUtil.calculateDiff(new DiffUtil.Callback() {
            @Override public int getOldListSize() { return items.size(); }
            @Override public int getNewListSize() { return newItems.size(); }
            @Override public boolean areItemsSame(int op, int np) {
                return items.get(op).getId() == newItems.get(np).getId();
            }
            @Override public boolean areContentsSame(int op, int np) {
                return items.get(op).equals(newItems.get(np));
            }
        });
        items.clear();
        items.addAll(newItems);
        result.dispatchUpdatesTo(this);
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        private final ShapeableImageView image;
        private final TextView title, subtitle;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            image = itemView.findViewById(R.id.item_image);
            title = itemView.findViewById(R.id.item_title);
            subtitle = itemView.findViewById(R.id.item_subtitle);
        }

        public void bind(Item item) {
            title.setText(item.getTitle());
            subtitle.setText(item.getSubtitle());
            Glide.with(itemView.getContext())
                .load(item.getImageUrl())
                .placeholder(R.drawable.ic_placeholder)
                .into(image);
        }
    }
}
```

### Java — Activity Setup
```java
RecyclerView recyclerView = findViewById(R.id.recycler_view);
recyclerView.setLayoutManager(new LinearLayoutManager(this));
recyclerView.setHasFixedSize(true);

ItemAdapter adapter = new ItemAdapter(dataList);
adapter.setOnItemClickListener((item, pos) -> {
    Toast.makeText(this, "Clicked: " + item.getTitle(), Toast.LENGTH_SHORT).show();
});
recyclerView.setAdapter(adapter);

// Grid layout
recyclerView.setLayoutManager(new GridLayoutManager(this, 2));

// Horizontal
LinearLayoutManager llm = new LinearLayoutManager(this, LinearLayoutManager.HORIZONTAL, false);
recyclerView.setLayoutManager(llm);

// Staggered Grid
StaggeredGridLayoutManager sglm = new StaggeredGridLayoutManager(2,
    StaggeredGridLayoutManager.VERTICAL);
recyclerView.setLayoutManager(sglm);
```

### Multi-Type Adapter (Java)
```java
public class MultiTypeAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_HEADER = 0;
    private static final int TYPE_ITEM = 1;
    private static final int TYPE_FOOTER = 2;

    private final List<Object> data;

    public MultiTypeAdapter(List<Object> data) { this.data = data; }

    @Override
    public int getItemViewType(int position) {
        if (data.get(position) instanceof Header) return TYPE_HEADER;
        if (data.get(position) instanceof Footer) return TYPE_FOOTER;
        return TYPE_ITEM;
    }

    @NonNull @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(parent.getContext());
        switch (viewType) {
            case TYPE_HEADER:
                return new HeaderVH(inflater.inflate(R.layout.item_header, parent, false));
            case TYPE_FOOTER:
                return new FooterVH(inflater.inflate(R.layout.item_footer, parent, false));
            default:
                return new ItemVH(inflater.inflate(R.layout.item_card, parent, false));
        }
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        // bind based on type
    }

    @Override public int getItemCount() { return data.size(); }

    static class HeaderVH extends RecyclerView.ViewHolder {
        HeaderVH(@NonNull View v) { super(v); }
    }
    static class FooterVH extends RecyclerView.ViewHolder {
        FooterVH(@NonNull View v) { super(v); }
    }
    static class ItemVH extends RecyclerView.ViewHolder {
        ItemVH(@NonNull View v) { super(v); }
    }
}
```

### ItemTouchHelper (Swipe to delete, Drag to reorder)
```java
ItemTouchHelper.SimpleCallback callback = new ItemTouchHelper.SimpleCallback(
    ItemTouchHelper.UP | ItemTouchHelper.DOWN,   // drag directions
    ItemTouchHelper.START | ItemTouchHelper.END  // swipe directions
) {
    @Override
    public boolean onMove(@NonNull RecyclerView rv,
            @NonNull RecyclerView.ViewHolder vh,
            @NonNull RecyclerView.ViewHolder target) {
        int from = vh.getAdapterPosition();
        int to = target.getAdapterPosition();
        Collections.swap(items, from, to);
        adapter.notifyItemMoved(from, to);
        return true;
    }

    @Override
    public void onSwiped(@NonNull RecyclerView.ViewHolder vh, int direction) {
        int position = vh.getAdapterPosition();
        Item removed = items.remove(position);
        adapter.notifyItemRemoved(position);
        Snackbar.make(recyclerView, "Item deleted", Snackbar.LENGTH_LONG)
            .setAction("UNDO", v -> {
                items.add(position, removed);
                adapter.notifyItemInserted(position);
            }).show();
    }

    @Override
    public void onChildDraw(@NonNull Canvas c, @NonNull RecyclerView rv,
            @NonNull RecyclerView.ViewHolder vh, float dX, float dY,
            int actionState, boolean isCurrentlyActive) {
        // Draw red background with delete icon during swipe
        if (actionState == ItemTouchHelper.ACTION_STATE_SWIPE) {
            View itemView = vh.itemView;
            Paint paint = new Paint();
            paint.setColor(Color.RED);
            c.drawRect(itemView.getLeft(), itemView.getTop(),
                itemView.getRight(), itemView.getBottom(), paint);
        }
        super.onChildDraw(c, rv, vh, dX, dY, actionState, isCurrentlyActive);
    }
};

new ItemTouchHelper(callback).attachToRecyclerView(recyclerView);
```

### ItemDecoration (Custom divider / spacing)
```java
public class GridSpacingDecoration extends RecyclerView.ItemDecoration {
    private final int spacing;

    public GridSpacingDecoration(int spacing) { this.spacing = spacing; }

    @Override
    public void getItemOffsets(@NonNull Rect outRect, @NonNull View view,
            @NonNull RecyclerView parent, @NonNull RecyclerView.State state) {
        outRect.left = spacing;
        outRect.right = spacing;
        outRect.bottom = spacing;
        if (parent.getChildLayoutPosition(view) < ((GridLayoutManager)
                parent.getLayoutManager()).getSpanCount()) {
            outRect.top = spacing;
        }
    }
}
recyclerView.addItemDecoration(new GridSpacingDecoration(dpToPx(8)));
```

---

## 2. ViewPager2

### Description
Replacement for ViewPager, built on RecyclerView. Supports RTL, vertical paging, and improved fragment state management.

### XML Layout
```xml
<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/view_pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal" />
```

### Fragment State Adapter (Java)
```java
public class ScreenSlidePagerAdapter extends FragmentStateAdapter {
    private final List<Fragment> fragments;
    private final List<String> titles;

    public ScreenSlidePagerAdapter(@NonNull FragmentActivity fa,
            List<Fragment> fragments, List<String> titles) {
        super(fa);
        this.fragments = fragments;
        this.titles = titles;
    }

    @NonNull @Override
    public Fragment createFragment(int position) {
        return fragments.get(position);
    }

    @Override public int getItemCount() { return fragments.size(); }

    public String getPageTitle(int position) { return titles.get(position); }
}
```

### Activity Setup (Java)
```java
ViewPager2 viewPager = findViewById(R.id.view_pager);
TabLayout tabLayout = findViewById(R.id.tab_layout);

List<Fragment> fragments = Arrays.asList(
    new HomeFragment(), new SearchFragment(), new LibraryFragment());
List<String> titles = Arrays.asList("Home", "Search", "Library");

ScreenSlidePagerAdapter adapter = new ScreenSlidePagerAdapter(this, fragments, titles);
viewPager.setAdapter(adapter);

// Attach to TabLayout
new TabLayoutMediator(tabLayout, viewPager,
    (tab, position) -> tab.setText(titles.get(position))
).attach();

// Page callbacks
viewPager.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
    @Override public void onPageSelected(int position) {
        super.onPageSelected(position);
        updatePageIndicator(position);
    }
    @Override public void onPageScrolled(int pos, float offset, int offsetPx) {}
    @Override public void onPageScrollStateChanged(int state) {}
});

// Programmatic navigation
viewPager.setCurrentItem(2, true); // animated
viewPager.setCurrentItem(0, false); // instant

// Disable swiping
viewPager.setUserInputEnabled(false);

// Vertical paging
viewPager.setOrientation(ViewPager2.ORIENTATION_VERTICAL);
```

### RecyclerView-based Adapter (non-fragment)
```java
public class BannerAdapter extends RecyclerView.Adapter<BannerAdapter.BannerVH> {
    private final List<Banner> banners;

    public BannerAdapter(List<Banner> banners) { this.banners = banners; }

    @NonNull @Override
    public BannerVH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_banner, parent, false);
        return new BannerVH(view);
    }

    @Override
    public void onBindViewHolder(@NonNull BannerVH holder, int position) {
        holder.bind(banners.get(position));
    }

    @Override public int getItemCount() { return banners.size(); }

    static class BannerVH extends RecyclerView.ViewHolder {
        ImageView image;
        BannerVH(@NonNull View v) {
            super(v);
            image = v.findViewById(R.id.banner_image);
        }
        void bind(Banner b) {
            Glide.with(itemView).load(b.getImageUrl()).into(image);
        }
    }
}
```

### Custom Page Transformer
```java
// Zoom-out
viewPager.setPageTransformer((page, position) -> {
    float MIN_SCALE = 0.85f;
    float MIN_ALPHA = 0.5f;
    int pageWidth = page.getWidth();
    int pageHeight = page.getHeight();

    if (position < -1 || position > 1) {
        page.setAlpha(MIN_ALPHA);
    } else if (position <= 0 || position <= 1) {
        float scaleFactor = Math.max(MIN_SCALE, 1 - Math.abs(position));
        float vertMargin = pageHeight * (1 - scaleFactor) / 2;
        float horzMargin = pageWidth * (1 - scaleFactor) / 2;
        page.setTranslationX(position < 0
            ? horzMargin - vertMargin / 2
            : -horzMargin + vertMargin / 2);
        page.setScaleX(scaleFactor);
        page.setScaleY(scaleFactor);
        page.setAlpha(MIN_ALPHA + (scaleFactor - MIN_SCALE)
            / (1 - MIN_SCALE) * (1 - MIN_ALPHA));
    }
});

// Depth transform
viewPager.setPageTransformer((page, position) -> {
    if (position < -1) {
        page.setAlpha(0f);
    } else if (position <= 0) {
        page.setAlpha(1f);
        page.setTranslationX(0f);
        page.setScaleX(1f);
        page.setScaleY(1f);
    } else if (position <= 1) {
        page.setAlpha(1 - position);
        page.setTranslationX(page.getWidth() * -position);
        float scale = 0.75f + (1 - 0.75f) * (1 - Math.abs(position));
        page.setScaleX(scale);
        page.setScaleY(scale);
    } else {
        page.setAlpha(0f);
    }
});
```

---

## 3. SwipeRefreshLayout

### Description
Adds pull-to-refresh gesture to a scrollable child. Shows a circular spinner while refreshing.

### XML Layout
```xml
<androidx.swiperefreshlayout.widget.SwipeRefreshLayout
    android:id="@+id/swipe_refresh"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.swiperefreshlayout.widget.SwipeRefreshLayout>
```

### Java Example
```java
SwipeRefreshLayout swipeRefresh = findViewById(R.id.swipe_refresh);
swipeRefresh.setColorSchemeResources(
    R.color.md3_primary,
    R.color.md3_secondary,
    R.color.md3_tertiary);
swipeRefresh.setProgressBackgroundColorSchemeResource(R.color.md3_surface);

swipeRefresh.setOnRefreshListener(() -> {
    fetchNewData(result -> {
        adapter.updateItems(result);
        swipeRefresh.setRefreshing(false);
    });
});

// Programmatic trigger
swipeRefresh.setRefreshing(true);

// Check state
boolean isRefreshing = swipeRefresh.isRefreshing();
```

---

## 4. NestedScrollView

### Description
A `ScrollView` that supports nested scrolling, enabling CoordinatorLayout behaviors like collapsing toolbars.

### XML Layout
```xml
<androidx.core.widget.NestedScrollView
    android:id="@+id/nested_scroll"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Multiple sections of content -->
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Section 1"
            android:textAppearance="?attr/textAppearanceHeadlineMedium" />

        <!-- More content... -->
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

### Java Example
```java
NestedScrollView nestedScroll = findViewById(R.id.nested_scroll);
nestedScroll.setOnScrollChangeListener((NestedScrollView.OnScrollChangeListener)
    (v, scrollX, scrollY, oldScrollX, oldScrollY) -> {
        if (scrollY > oldScrollY) {
            // Scrolling down
            fab.hide();
        } else {
            // Scrolling up
            fab.show();
        }
    });

// Scroll to position
nestedScroll.smoothScrollTo(0, 500);
nestedScroll.scrollTo(0, 0);
```

---

## 5. ConstraintLayout

### Description
A powerful layout that allows complex, responsive UIs without deep view nesting. Uses constraint relationships between views.

### XML Layout
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Profile image centered horizontally -->
    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/img_profile"
        android:layout_width="80dp"
        android:layout_height="80dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="32dp"
        app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle" />

    <!-- Title below profile image -->
    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="John Doe"
        app:layout_constraintTop_toBottomOf="@id/img_profile"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="12dp"
        android:textAppearance="?attr/textAppearanceHeadlineSmall" />

    <!-- Button constrained to bottom -->
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_follow"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Follow"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_margin="16dp" />

    <!-- Percentage-based width -->
    <View
        android:id="@+id/progress_bar_fill"
        android:layout_width="0dp"
        android:layout_height="8dp"
        android:background="?attr/colorPrimary"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/progress_track"
        app:layout_constraintWidth_percent="0.65"
        app:layout_constraintWidth_default="percent" />

    <!-- Barrier -->
    <androidx.constraintlayout.widget.Barrier
        android:id="@+id/barrier"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="end"
        app:constraint_referenced_ids="tv_name,tv_subtitle" />

    <!-- Group (visibility control) -->
    <androidx.constraintlayout.widget.Group
        android:id="@+id/loading_group"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="progress_circular,tv_loading" />

    <!-- Placeholder -->
    <androidx.constraintlayout.widget.Placeholder
        android:id="@+id/placeholder"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <!-- Flow for wrapping items -->
    <androidx.constraintlayout.helper.widget.Flow
        android:id="@+id/flow"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:flow_wrapMode="chain"
        app:flow_horizontalGap="8dp"
        app:flow_verticalGap="8dp"
        app:constraint_referenced_ids="chip1,chip2,chip3,chip4" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### Java — Dynamic Constraints
```java
ConstraintLayout layout = findViewById(R.id.constraint_layout);
ConstraintSet set = new ConstraintSet();
set.clone(layout);

// Move view to new position
set.connect(R.id.my_view, ConstraintSet.TOP, R.id.other_view, ConstraintSet.BOTTOM, 16);
set.setMargin(R.id.my_view, ConstraintSet.START, 24);

// Apply with animation
TransitionManager.beginDelayedTransition(layout);
set.applyTo(layout);

// Show/hide group
Group group = layout.findViewById(R.id.loading_group);
group.setVisibility(View.GONE);
```

### Kotlin — Dynamic Constraints
```kotlin
val layout: ConstraintLayout = findViewById(R.id.constraint_layout)
val set = ConstraintSet()
set.clone(layout)
set.connect(R.id.my_view, ConstraintSet.TOP, R.id.other_view, ConstraintSet.BOTTOM, 16)
TransitionManager.beginDelayedTransition(layout)
set.applyTo(layout)
```

### Constraint Types Reference
| Constraint | Usage |
|---|---|
| `constraintTop_toTopOf` | Align tops |
| `constraintTop_toBottomOf` | Below another view |
| `constraintStart_toStartOf` | Align starts |
| `constraintEnd_toEndOf` | Align ends |
| `constraintHorizontal_bias` | 0.0–1.0 position |
| `constraintDimensionRatio` | Aspect ratio e.g. `"16:9"` |
| `constraintWidth_percent` | Percentage of parent |
| `constraintCircle` | Circular positioning |
| `constraintCircleRadius` | Distance from center |
| `constraintCircleAngle` | Degrees from center |

---

## 6. MotionLayout

### Description
Subclass of `ConstraintLayout` that animates transitions between constraint sets. Defined in XML `MotionScene` files.

### XML Layout
```xml
<androidx.constraintlayout.motion.widget.MotionLayout
    android:id="@+id/motion_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/scene_main">

    <ImageView
        android:id="@+id/img_hero"
        android:layout_width="match_parent"
        android:layout_height="200dp" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hero Title" />
</androidx.constraintlayout.motion.widget.MotionLayout>
```

### `res/xml/scene_main.xml`
```xml
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <Transition
        motion:constraintSetStart="@id/start"
        motion:constraintSetEnd="@id/end"
        motion:duration="400">
        <OnSwipe
            motion:touchAnchorId="@id/img_hero"
            motion:touchAnchorSide="top"
            motion:dragDirection="dragUp" />
        <KeyFrameSet>
            <KeyAttribute
                motion:motionTarget="@id/tv_title"
                motion:framePosition="50"
                android:alpha="0.5" />
        </KeyFrameSet>
    </Transition>

    <ConstraintSet android:id="@+id/start">
        <Constraint
            android:id="@+id/img_hero"
            android:layout_width="match_parent"
            android:layout_height="200dp"
            motion:layout_constraintTop_toTopOf="parent" />
        <Constraint
            android:id="@+id/tv_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:alpha="1"
            android:textSize="24sp"
            motion:layout_constraintTop_toBottomOf="@id/img_hero"
            motion:layout_constraintStart_toStartOf="parent"
            android:layout_marginStart="16dp"
            android:layout_marginTop="8dp" />
    </ConstraintSet>

    <ConstraintSet android:id="@+id/end">
        <Constraint
            android:id="@+id/img_hero"
            android:layout_width="match_parent"
            android:layout_height="64dp"
            motion:layout_constraintTop_toTopOf="parent" />
        <Constraint
            android:id="@+id/tv_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:alpha="1"
            android:textSize="18sp"
            motion:layout_constraintTop_toTopOf="@id/img_hero"
            motion:layout_constraintBottom_toBottomOf="@id/img_hero"
            motion:layout_constraintStart_toStartOf="parent"
            android:layout_marginStart="72dp" />
    </ConstraintSet>
</MotionScene>
```

### Java Example
```java
MotionLayout motionLayout = findViewById(R.id.motion_layout);

// Programmatic transition
motionLayout.transitionToEnd();
motionLayout.transitionToStart();
motionLayout.transitionToState(R.id.end);

// Progress-based (for manual dragging)
motionLayout.setProgress(0.5f);

// Listener
motionLayout.setTransitionListener(new MotionLayout.TransitionListener() {
    @Override public void onTransitionStarted(MotionLayout m, int start, int end) {}
    @Override public void onTransitionChange(MotionLayout m, int start, int end, float pos) {}
    @Override public void onTransitionCompleted(MotionLayout m, int currentId) {
        if (currentId == R.id.end) {
            // fully collapsed
        }
    }
    @Override public void onTransitionTrigger(MotionLayout m, int id, boolean pos, float prog) {}
});
```

---

## 7. CoordinatorLayout

### Description
A `FrameLayout` supercharged with `Behavior` support. Enables views to react to each other's scroll events.

### XML Layout
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        app:srcCompat="@drawable/ic_add" />

    <LinearLayout
        android:id="@+id/bottom_sheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="com.google.android.material.bottomsheet.BottomSheetBehavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Custom Behavior (Java)
```java
public class ScrollAwareFabBehavior extends CoordinatorLayout.Behavior<FloatingActionButton> {
    public ScrollAwareFabBehavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean onStartNestedScroll(@NonNull CoordinatorLayout coordinatorLayout,
            @NonNull FloatingActionButton child, @NonNull View directTargetChild,
            @NonNull View target, int axes, int type) {
        return axes == ViewCompat.SCROLL_AXIS_VERTICAL;
    }

    @Override
    public void onNestedScroll(@NonNull CoordinatorLayout coordinatorLayout,
            @NonNull FloatingActionButton child, @NonNull View target,
            int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed, int type) {
        if (dyConsumed > 0 && child.getVisibility() == View.VISIBLE) {
            child.hide();
        } else if (dyConsumed < 0 && child.getVisibility() != View.VISIBLE) {
            child.show();
        }
    }
}
```

---

## 8. FragmentContainerView

### Description
The recommended container for fragments. Properly handles fragment transactions and z-ordering.

### XML Layout
```xml
<androidx.fragment.app.FragmentContainerView
    android:id="@+id/fragment_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:name="com.example.app.HomeFragment" />
```

### Java Example
```java
// Add fragment
getSupportFragmentManager()
    .beginTransaction()
    .setCustomAnimations(
        R.anim.slide_in_right,
        R.anim.slide_out_left,
        R.anim.slide_in_left,
        R.anim.slide_out_right)
    .replace(R.id.fragment_container, new DetailFragment())
    .addToBackStack("detail")
    .commit();

// Back stack management
getSupportFragmentManager().addOnBackStackChangedListener(() -> {
    int backStackCount = getSupportFragmentManager().getBackStackEntryCount();
    if (getSupportActionBar() != null) {
        getSupportActionBar().setDisplayHomeAsUpEnabled(backStackCount > 0);
    }
});
```

---

## 9. DrawerLayout

### Description
Container for a navigation drawer that slides in from the edge of the screen.

### XML Layout
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <!-- Main content must come FIRST -->
    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <androidx.fragment.app.FragmentContainerView
            android:id="@+id/fragment_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </FrameLayout>

    <!-- End drawer (right side) -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:menu="@menu/nav_menu"
        app:headerLayout="@layout/nav_header" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### Java Example
```java
DrawerLayout drawer = findViewById(R.id.drawer_layout);

// Lock modes
drawer.setDrawerLockMode(DrawerLayout.LOCK_MODE_LOCKED_CLOSED);
drawer.setDrawerLockMode(DrawerLayout.LOCK_MODE_UNLOCKED);

// Drawer listener
drawer.addDrawerListener(new DrawerLayout.DrawerListener() {
    @Override public void onDrawerSlide(@NonNull View view, float v) {}
    @Override public void onDrawerOpened(@NonNull View view) {}
    @Override public void onDrawerClosed(@NonNull View view) {}
    @Override public void onDrawerStateChanged(int state) {}
});

// Open/close
drawer.openDrawer(GravityCompat.START);
drawer.closeDrawer(GravityCompat.START);
boolean isOpen = drawer.isDrawerOpen(GravityCompat.START);
```

---

## 10. GridLayout

### Description
Arranges children in a fixed grid of rows and columns. Simpler than `ConstraintLayout` for grid UIs.

### XML Layout
```xml
<androidx.gridlayout.widget.GridLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:columnCount="3"
    app:rowCount="2"
    app:useDefaultMargins="true">

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Item 1"
        app:layout_columnWeight="1" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Item 2"
        app:layout_columnWeight="1" />

    <!-- Span multiple columns -->
    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Wide Item"
        app:layout_columnWeight="1"
        app:layout_columnSpan="2" />
</androidx.gridlayout.widget.GridLayout>
```

---

## 11. FlexboxLayout

### Description
CSS Flexbox-inspired layout. Children wrap to the next line and can be sized relative to their container.

### Dependency
```groovy
implementation 'com.google.android.flexbox:flexbox:3.0.0'
```

### XML Layout
```xml
<com.google.android.flexbox.FlexboxLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:flexWrap="wrap"
    app:flexDirection="row"
    app:justifyContent="flex_start"
    app:alignItems="flex_start">

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Android"
        app:layout_flexGrow="0" />

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Material Design" />

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kotlin" />
</com.google.android.flexbox.FlexboxLayout>
```

### FlexboxLayoutManager with RecyclerView
```java
FlexboxLayoutManager flexLayoutManager = new FlexboxLayoutManager(this);
flexLayoutManager.setFlexWrap(FlexWrap.WRAP);
flexLayoutManager.setFlexDirection(FlexDirection.ROW);
flexLayoutManager.setJustifyContent(JustifyContent.FLEX_START);

recyclerView.setLayoutManager(flexLayoutManager);
```

### FlexboxLayout Attributes
| Attribute | Values |
|---|---|
| `flexDirection` | `row`, `row_reverse`, `column`, `column_reverse` |
| `flexWrap` | `nowrap`, `wrap`, `wrap_reverse` |
| `justifyContent` | `flex_start`, `center`, `flex_end`, `space_between`, `space_around` |
| `alignItems` | `stretch`, `flex_start`, `center`, `flex_end`, `baseline` |
| `layout_flexGrow` | 0.0–N (how much to grow) |
| `layout_flexShrink` | Shrink factor |
| `layout_alignSelf` | Per-item alignment override |

---

## 12. ViewStub

### Description
A zero-size invisible view that lazily inflates a layout on demand. Useful for conditional UI elements that may never be shown.

### XML Layout
```xml
<ViewStub
    android:id="@+id/stub_error"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout="@layout/layout_error_state"
    android:inflatedId="@+id/error_layout" />
```

### Java Example
```java
ViewStub stub = findViewById(R.id.stub_error);
View errorLayout = stub.inflate(); // inflates once

// Or check before inflating
TextView errorMsg = errorLayout.findViewById(R.id.tv_error_message);
errorMsg.setText("Connection failed");
errorLayout.setVisibility(View.VISIBLE);
```

---

## 13. MergeLayout & Include

### Description
`<merge>` eliminates redundant view groups when including layouts. `<include>` reuses layouts.

### `res/layout/layout_toolbar.xml`
```xml
<merge xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize" />
</merge>
```

### Usage in Activity layout
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <include layout="@layout/layout_toolbar" />

    <FrameLayout
        android:id="@+id/content"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

---

## 14. SlidingPaneLayout

### Description
Two-panel layout for list-detail patterns. On wide screens both panels show; on narrow screens only one is visible at a time. Ideal for tablet/foldable support.

### XML Layout
```xml
<androidx.slidingpanelayout.widget.SlidingPaneLayout
    android:id="@+id/sliding_pane"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- List pane — preferred width -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/list_pane"
        android:layout_width="280dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <!-- Detail pane — fills remaining space -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/detail_pane"
        android:layout_width="300dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
</androidx.slidingpanelayout.widget.SlidingPaneLayout>
```

### Java Example
```java
SlidingPaneLayout slidingPane = findViewById(R.id.sliding_pane);
slidingPane.lockMode = SlidingPaneLayout.LOCK_MODE_LOCKED;

// Open detail panel
slidingPane.open();
slidingPane.close();
boolean isOpen = slidingPane.isOpen();

// Listen to state
slidingPane.addPanelSlideListener(new SlidingPaneLayout.PanelSlideListener() {
    @Override public void onPanelSlide(@NonNull View panel, float slideOffset) {}
    @Override public void onPanelOpened(@NonNull View panel) { /* detail shown */ }
    @Override public void onPanelClosed(@NonNull View panel) { /* list shown */ }
});
```

---

## 15. LinearLayoutCompat

### Description
Backward-compatible `LinearLayout` from AndroidX supporting dividers and additional attributes on older APIs.

### XML Layout
```xml
<androidx.appcompat.widget.LinearLayoutCompat
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:divider="?attr/listDivider"
    app:showDividers="middle|beginning|end"
    app:dividerPadding="8dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="Item 1" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="Item 2" />
</androidx.appcompat.widget.LinearLayoutCompat>
```

---

## 16. CircularReveal

### Description
Animates a circular clipping mask expanding from a point. Used for screen transitions, FAB-to-content reveals.

### Java Example
```java
// Reveal a view
View myView = findViewById(R.id.my_view);
int cx = myView.getWidth() / 2;
int cy = myView.getHeight() / 2;
float finalRadius = (float) Math.hypot(cx, cy);

Animator anim = ViewAnimationUtils.createCircularReveal(myView, cx, cy, 0f, finalRadius);
myView.setVisibility(View.VISIBLE);
anim.start();

// Hide with circular reveal
float initialRadius = (float) Math.hypot(cx, cy);
Animator hideAnim = ViewAnimationUtils.createCircularReveal(myView, cx, cy, initialRadius, 0f);
hideAnim.addListener(new AnimatorListenerAdapter() {
    @Override public void onAnimationEnd(Animator animation) {
        myView.setVisibility(View.INVISIBLE);
    }
});
hideAnim.start();

// From FAB position
FloatingActionButton fab = findViewById(R.id.fab);
int[] fabLocation = new int[2];
fab.getLocationInWindow(fabLocation);
int fabCx = fabLocation[0] + fab.getWidth() / 2;
int fabCy = fabLocation[1] + fab.getHeight() / 2;

View content = findViewById(R.id.content);
float radius = (float) Math.hypot(content.getWidth(), content.getHeight());
Animator reveal = ViewAnimationUtils.createCircularReveal(
    content, fabCx, fabCy, 0f, radius);
reveal.setDuration(400);
content.setVisibility(View.VISIBLE);
reveal.start();
```

### Kotlin Example
```kotlin
val myView: View = findViewById(R.id.my_view)
val cx = myView.width / 2
val cy = myView.height / 2
val finalRadius = Math.hypot(cx.toDouble(), cy.toDouble()).toFloat()

val anim = ViewAnimationUtils.createCircularReveal(myView, cx, cy, 0f, finalRadius)
myView.visibility = View.VISIBLE
anim.start()
```

---

## Performance Best Practices

### RecyclerView
- Always implement `DiffUtil` instead of `notifyDataSetChanged()`.
- Use `setHasFixedSize(true)` when item size doesn't change with data.
- Pool views with `setRecycledViewPool()` for nested RecyclerViews.
- Use `setItemViewCacheSize(20)` for faster scrolling.
- Avoid heavy operations in `onBindViewHolder()` — prebind in background.

### ConstraintLayout
- Prefer flat hierarchies — one level of ConstraintLayout over nested LinearLayouts.
- Use `Barrier`, `Group`, and `Flow` helpers instead of extra ViewGroups.

### ViewPager2
- Use `setOffscreenPageLimit()` sparingly — higher values consume more memory.
- Use `FragmentStateAdapter` not `FragmentPagerAdapter` — it properly destroys off-screen fragments.

### General
- Use `ViewStub` for infrequently shown layouts (error states, empty states).
- Profile with Android Studio Layout Inspector and Hierarchy Viewer.
- Avoid overdraw — use `Show GPU Overdraw` in developer options.
