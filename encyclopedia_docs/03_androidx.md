# Module 03 — AndroidX Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

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
12. [ViewBinding](#12-viewbinding)
13. [WindowInsets & Edge-to-Edge](#13-windowinsets--edge-to-edge)

---

## 1. RecyclerView

**Description:** Flexible, efficient list/grid view that recycles item views. The core list component for all modern Android apps.

**Required Dependency:**
```gradle
implementation 'androidx.recyclerview:recyclerview:1.3.2'
```

### XML Example
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:clipToPadding="false"
    android:paddingBottom="88dp"
    tools:listitem="@layout/item_card" />
```

### Adapter — Kotlin (Modern ListAdapter with DiffUtil)
```kotlin
data class Item(val id: Int, val title: String, val subtitle: String)

class ItemAdapter(
    private val onItemClick: (Item) -> Unit
) : ListAdapter<Item, ItemAdapter.ViewHolder>(DiffCallback()) {

    class ViewHolder(val binding: ItemCardBinding) :
        RecyclerView.ViewHolder(binding.root)

    class DiffCallback : DiffUtil.ItemCallback<Item>() {
        override fun areItemsTheSame(old: Item, new: Item) = old.id == new.id
        override fun areContentsTheSame(old: Item, new: Item) = old == new
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = ItemCardBinding.inflate(
            LayoutInflater.from(parent.context), parent, false
        )
        return ViewHolder(binding)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = getItem(position)
        holder.binding.apply {
            tvTitle.text = item.title
            tvSubtitle.text = item.subtitle
            root.setOnClickListener { onItemClick(item) }
        }
    }
}

// Usage in Fragment/Activity
val adapter = ItemAdapter { item -> navigate(item) }
binding.recyclerView.apply {
    layoutManager = LinearLayoutManager(context)
    this.adapter = adapter
    setHasFixedSize(true)
}

// Submit list (DiffUtil handles updates automatically)
adapter.submitList(items)
```

### Adapter — Java
```java
public class ItemAdapter extends ListAdapter<Item, ItemAdapter.ViewHolder> {

    public ItemAdapter(OnItemClickListener listener) {
        super(new DiffCallback());
        this.listener = listener;
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        ItemCardBinding binding;
        ViewHolder(ItemCardBinding b) {
            super(b.getRoot());
            binding = b;
        }
    }

    static class DiffCallback extends DiffUtil.ItemCallback<Item> {
        @Override public boolean areItemsTheSame(@NonNull Item a, @NonNull Item b) {
            return a.getId() == b.getId();
        }
        @Override public boolean areContentsTheSame(@NonNull Item a, @NonNull Item b) {
            return a.equals(b);
        }
    }

    @NonNull @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        return new ViewHolder(ItemCardBinding.inflate(
            LayoutInflater.from(parent.getContext()), parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Item item = getItem(position);
        holder.binding.tvTitle.setText(item.getTitle());
        holder.binding.getRoot().setOnClickListener(v -> listener.onItemClick(item));
    }
}
```

### Layout Managers
```kotlin
// Linear (vertical list)
binding.rv.layoutManager = LinearLayoutManager(context)

// Linear (horizontal list)
binding.rv.layoutManager = LinearLayoutManager(context, LinearLayoutManager.HORIZONTAL, false)

// Grid (2 columns)
binding.rv.layoutManager = GridLayoutManager(context, 2)

// Grid with variable span
val glm = GridLayoutManager(context, 3)
glm.spanSizeLookup = object : GridLayoutManager.SpanSizeLookup() {
    override fun getSpanSize(position: Int): Int {
        return if (adapter.getItemViewType(position) == TYPE_HEADER) 3 else 1
    }
}

// Staggered grid
binding.rv.layoutManager = StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL)
```

### Item Decorations
```kotlin
// Spacing decoration
class SpaceItemDecoration(private val spacing: Int) : RecyclerView.ItemDecoration() {
    override fun getItemOffsets(
        outRect: Rect, view: View,
        parent: RecyclerView, state: RecyclerView.State
    ) {
        outRect.set(spacing, spacing, spacing, spacing)
    }
}
binding.rv.addItemDecoration(SpaceItemDecoration(16))

// Material divider
binding.rv.addItemDecoration(
    MaterialDividerItemDecoration(context, LinearLayoutManager.VERTICAL)
)
```

### Item Touch (Swipe-to-delete, Drag-to-reorder)
```kotlin
val callback = object : ItemTouchHelper.SimpleCallback(
    ItemTouchHelper.UP or ItemTouchHelper.DOWN,   // drag directions
    ItemTouchHelper.LEFT or ItemTouchHelper.RIGHT  // swipe directions
) {
    override fun onMove(rv: RecyclerView, vh: RecyclerView.ViewHolder,
                        target: RecyclerView.ViewHolder): Boolean {
        adapter.onItemMoved(vh.adapterPosition, target.adapterPosition)
        return true
    }

    override fun onSwiped(vh: RecyclerView.ViewHolder, direction: Int) {
        adapter.onItemSwiped(vh.adapterPosition)
    }
}
ItemTouchHelper(callback).attachToRecyclerView(binding.rv)
```

---

## 2. ViewPager2

**Description:** Replaces the deprecated `ViewPager`. Supports vertical swiping, RTL, and is backed by `RecyclerView`.

**Required Dependency:**
```gradle
implementation 'androidx.viewpager2:viewpager2:1.1.0'
```

### XML Example
```xml
<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### Fragment Adapter — Kotlin
```kotlin
class OnboardingPagerAdapter(activity: FragmentActivity) :
    FragmentStateAdapter(activity) {

    private val pages = listOf(
        OnboardingPage1Fragment(),
        OnboardingPage2Fragment(),
        OnboardingPage3Fragment()
    )

    override fun getItemCount() = pages.size
    override fun createFragment(position: Int) = pages[position]
}

// Setup
binding.viewPager.adapter = OnboardingPagerAdapter(this)
binding.viewPager.isUserInputEnabled = true // enable/disable swipe

// Page change listener
binding.viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
    override fun onPageSelected(position: Int) {
        binding.dotsIndicator.setCurrentIndicator(position)
        updateNextButtonText(position)
    }
})

// Go to page
binding.viewPager.currentItem = 2
binding.viewPager.setCurrentItem(2, true) // animated
```

### View Adapter (non-Fragment)
```kotlin
class BannerAdapter(private val items: List<BannerItem>) :
    RecyclerView.Adapter<BannerAdapter.ViewHolder>() {

    inner class ViewHolder(val binding: ItemBannerBinding) :
        RecyclerView.ViewHolder(binding.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) =
        ViewHolder(ItemBannerBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = items[position % items.size] // infinite loop trick
        Glide.with(holder.itemView).load(item.imageUrl).into(holder.binding.ivBanner)
    }

    override fun getItemCount() = Int.MAX_VALUE // infinite scroll
}
```

### Page Transformations
```kotlin
// Zoom out page transformer
class ZoomOutPageTransformer : ViewPager2.PageTransformer {
    override fun transformPage(page: View, position: Float) {
        val minScale = 0.85f
        val minAlpha = 0.5f
        val absPos = Math.abs(position)
        when {
            position < -1 || position > 1 -> page.alpha = minAlpha
            else -> {
                val scaleFactor = Math.max(minScale, 1 - absPos)
                page.scaleX = scaleFactor
                page.scaleY = scaleFactor
                page.alpha = minAlpha + (scaleFactor - minScale) / (1 - minScale) * (1 - minAlpha)
            }
        }
    }
}
binding.viewPager.setPageTransformer(ZoomOutPageTransformer())

// Depth page transformer (slide in from behind)
class DepthPageTransformer : ViewPager2.PageTransformer {
    override fun transformPage(page: View, position: Float) {
        val minScale = 0.75f
        when {
            position <= 0 -> {
                page.translationX = 0f
                page.scaleX = 1f
                page.scaleY = 1f
                page.alpha = 1f
            }
            position <= 1 -> {
                page.alpha = 1 - position
                page.translationX = page.width * -position
                val scale = minScale + (1 - minScale) * (1 - Math.abs(position))
                page.scaleX = scale
                page.scaleY = scale
            }
            else -> page.alpha = 0f
        }
    }
}
```

---

## 3. SwipeRefreshLayout

**Description:** Pull-to-refresh container. Wrap any scrollable view.

```gradle
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'
```

### XML Example
```xml
<androidx.swiperefreshlayout.widget.SwipeRefreshLayout
    android:id="@+id/swipeRefresh"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.swiperefreshlayout.widget.SwipeRefreshLayout>
```

### Kotlin Example
```kotlin
binding.swipeRefresh.apply {
    setColorSchemeResources(
        R.color.md_theme_primary,
        R.color.md_theme_secondary,
        R.color.md_theme_tertiary
    )
    setOnRefreshListener {
        viewModel.refresh()
    }
}

// Stop refreshing after data loads
viewModel.isLoading.observe(viewLifecycleOwner) { loading ->
    binding.swipeRefresh.isRefreshing = loading
}
```

---

## 4. NestedScrollView

**Description:** ScrollView that supports nested scrolling, works with `CoordinatorLayout` and `AppBarLayout`.

### XML Example
```xml
<androidx.core.widget.NestedScrollView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Content sections -->
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Section 1" />

        <!-- Nested RecyclerView — use with caution (performance) -->
        <androidx.recyclerview.widget.RecyclerView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:nestedScrollingEnabled="false" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

### Best Practice
```kotlin
// When embedding RecyclerView inside NestedScrollView:
binding.innerRv.isNestedScrollingEnabled = false

// Monitor scroll position
binding.nestedScroll.setOnScrollChangeListener { v, scrollX, scrollY, _, oldScrollY ->
    val isAtBottom = scrollY == (v as NestedScrollView).getChildAt(0).measuredHeight - v.measuredHeight
    if (isAtBottom) loadMoreItems()
}
```

---

## 5. ConstraintLayout

**Description:** Flexible layout system that allows positioning views relative to each other and the parent using constraints. Flattens view hierarchy.

```gradle
implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
```

### XML Example — Common Patterns
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Center in parent -->
    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Title"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />

    <!-- Horizontal chain (spread) -->
    <Button android:id="@+id/btn1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toStartOf="@id/btn2"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintHorizontal_chainStyle="spread" />

    <Button android:id="@+id/btn2"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toEndOf="@id/btn1"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <!-- Barrier — aligns to the widest of multiple views -->
    <androidx.constraintlayout.widget.Barrier
        android:id="@+id/barrier"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="end"
        app:constraint_referenced_ids="tvLabel1,tvLabel2" />

    <!-- Guideline at 50% -->
    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guidelineCenter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.5" />

    <!-- Aspect ratio (16:9 image) -->
    <ImageView
        android:id="@+id/ivBanner"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintDimensionRatio="16:9" />

    <!-- Bias (off-center, 30% from top) -->
    <View
        android:layout_width="100dp"
        android:layout_height="100dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintVertical_bias="0.3" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### Kotlin — Programmatic Constraints
```kotlin
// Modify constraints programmatically
val constraintSet = ConstraintSet()
constraintSet.clone(binding.root)
constraintSet.setVisibility(R.id.tvSubtitle, ConstraintSet.GONE)
constraintSet.applyTo(binding.root)

// Animated constraint change
val constraintSetExpanded = ConstraintSet()
constraintSetExpanded.clone(this, R.layout.activity_main_expanded)
TransitionManager.beginDelayedTransition(binding.root)
constraintSetExpanded.applyTo(binding.root)
```

---

## 6. MotionLayout

**Description:** Extends `ConstraintLayout` with animation between constraint sets. Declarative, keyframe-based animation system.

### XML Example — Layout File
```xml
<androidx.constraintlayout.motion.widget.MotionLayout
    android:id="@+id/motionLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/motion_scene_main">

    <View
        android:id="@+id/animatedBox"
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:background="?attr/colorPrimary" />
</androidx.constraintlayout.motion.widget.MotionLayout>
```

### MotionScene XML — res/xml/motion_scene_main.xml
```xml
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <Transition
        motion:constraintSetStart="@id/start"
        motion:constraintSetEnd="@id/end"
        motion:duration="500">

        <OnSwipe
            motion:touchAnchorId="@id/animatedBox"
            motion:touchAnchorSide="top"
            motion:dragDirection="dragUp" />
    </Transition>

    <ConstraintSet android:id="@+id/start">
        <Constraint android:id="@+id/animatedBox"
            android:layout_width="64dp"
            android:layout_height="64dp"
            motion:layout_constraintBottom_toBottomOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintEnd_toEndOf="parent">
            <CustomAttribute
                motion:attributeName="cardCornerRadius"
                motion:customFloatValue="0" />
        </Constraint>
    </ConstraintSet>

    <ConstraintSet android:id="@+id/end">
        <Constraint android:id="@+id/animatedBox"
            android:layout_width="match_parent"
            android:layout_height="200dp"
            motion:layout_constraintTop_toTopOf="parent"
            motion:layout_constraintStart_toStartOf="parent"
            motion:layout_constraintEnd_toEndOf="parent">
            <CustomAttribute
                motion:attributeName="cardCornerRadius"
                motion:customFloatValue="16" />
        </Constraint>
    </ConstraintSet>
</MotionScene>
```

### Kotlin Example
```kotlin
// Programmatic transition
binding.motionLayout.transitionToEnd()
binding.motionLayout.transitionToStart()

// Set progress manually (e.g., driven by scroll)
binding.nestedScroll.setOnScrollChangeListener { _, _, scrollY, _, _ ->
    val progress = scrollY.toFloat() / maxScroll
    binding.motionLayout.progress = progress.coerceIn(0f, 1f)
}

// Listen for completion
binding.motionLayout.setTransitionListener(object : MotionLayout.TransitionListener {
    override fun onTransitionCompleted(motionLayout: MotionLayout, currentId: Int) {
        if (currentId == R.id.end) { /* fully expanded */ }
    }
    override fun onTransitionChange(ml: MotionLayout, startId: Int, endId: Int, progress: Float) {}
    override fun onTransitionStarted(ml: MotionLayout, startId: Int, endId: Int) {}
    override fun onTransitionTrigger(ml: MotionLayout, triggerId: Int, positive: Boolean, progress: Float) {}
})
```

---

## 7. CoordinatorLayout

**Description:** Super-powered `FrameLayout` that coordinates scrolling behavior between its children using `Behavior` classes.

### XML Example — Full Pattern
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways" />
    </com.google.android.material.appbar.AppBarLayout>

    <!-- Content must declare the appbar behavior -->
    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <!-- FAB above bottom nav/snackbar -->
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp" />

    <!-- Bottom Sheet -->
    <LinearLayout
        android:id="@+id/bottomSheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_behavior="com.google.android.material.bottomsheet.BottomSheetBehavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

---

## 8. FragmentContainerView

**Description:** Replaces `FrameLayout` as the recommended container for fragments. Handles fragment transactions more reliably.

### XML Example
```xml
<androidx.fragment.app.FragmentContainerView
    android:id="@+id/navHostFragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:defaultNavHost="true"
    app:navGraph="@navigation/nav_graph" />
```

### Kotlin Example
```kotlin
// Manual fragment transaction
supportFragmentManager.commit {
    setReorderingAllowed(true)
    replace<HomeFragment>(R.id.navHostFragment)
    addToBackStack("home")
}
```

---

## 9. DrawerLayout

**Description:** Provides the side navigation drawer sliding overlay.

### XML Example
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:id="@+id/drawerLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <!-- Main content (must be first child) -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/navHostFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Drawer panel (gravity=start for LTR, end for RTL) -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navView"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:menu="@menu/drawer_menu" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### Kotlin Example
```kotlin
// Open / close
binding.drawerLayout.openDrawer(GravityCompat.START)
binding.drawerLayout.closeDrawer(GravityCompat.START)

// Back press to close drawer
onBackPressedDispatcher.addCallback(this) {
    if (binding.drawerLayout.isDrawerOpen(GravityCompat.START)) {
        binding.drawerLayout.closeDrawer(GravityCompat.START)
    } else {
        isEnabled = false
        onBackPressedDispatcher.onBackPressed()
    }
}
```

---

## 10. GridLayout

**Description:** Lays out views in a two-dimensional grid. Simpler than `ConstraintLayout` for uniform grids.

### XML Example
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
        android:text="Cell 1"
        app:layout_columnWeight="1" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Cell 2 (spans 2 columns)"
        app:layout_columnWeight="1"
        app:layout_columnSpan="2" />
</androidx.gridlayout.widget.GridLayout>
```

---

## 11. FlexboxLayout

**Description:** CSS Flexbox-equivalent for Android. Supports wrapping, ordering, flex-grow, and alignment.

```gradle
implementation 'com.google.android.flexbox:flexbox:3.0.0'
```

### XML Example
```xml
<com.google.android.flexbox.FlexboxLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:flexWrap="wrap"
    app:flexDirection="row"
    app:justifyContent="flex_start"
    app:alignItems="center">

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Tag 1" />

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Tag 2" />
</com.google.android.flexbox.FlexboxLayout>
```

### With RecyclerView
```kotlin
val flexLayoutManager = FlexboxLayoutManager(context).apply {
    flexWrap = FlexWrap.WRAP
    flexDirection = FlexDirection.ROW
    justifyContent = JustifyContent.FLEX_START
}
binding.recyclerView.layoutManager = flexLayoutManager
```

---

## 12. ViewBinding

**Description:** Generates a binding class for each XML layout, providing type-safe view references without `findViewById`.

### Setup
```gradle
// build.gradle (app)
android {
    buildFeatures {
        viewBinding = true
    }
}
```

### Usage — Activity (Kotlin)
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.tvTitle.text = "Hello"
        binding.btnAction.setOnClickListener { doAction() }
    }
}
```

### Usage — Fragment (Kotlin)
```kotlin
class HomeFragment : Fragment() {
    private var _binding: FragmentHomeBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                              savedInstanceState: Bundle?): View {
        _binding = FragmentHomeBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null  // CRITICAL: Prevent memory leaks
    }
}
```

### Usage — RecyclerView ViewHolder (Kotlin)
```kotlin
class ItemViewHolder(val binding: ItemCardBinding) :
    RecyclerView.ViewHolder(binding.root)
```

### Usage — Activity (Java)
```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = ActivityMainBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        binding.tvTitle.setText("Hello");
    }
}
```

---

## 13. WindowInsets & Edge-to-Edge

**Description:** Make your app draw behind status bar and navigation bar for an immersive look. Required for modern Android apps.

### Setup — Activity
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Enable edge-to-edge
        enableEdgeToEdge()
        
        setContentView(binding.root)

        // Apply insets to the root view
        ViewCompat.setOnApplyWindowInsetsListener(binding.root) { view, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            view.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### Apply Insets to Specific Views
```kotlin
// Only top inset for toolbar
ViewCompat.setOnApplyWindowInsetsListener(binding.toolbar) { view, insets ->
    val statusBar = insets.getInsets(WindowInsetsCompat.Type.statusBars())
    view.updatePadding(top = statusBar.top)
    insets
}

// Only bottom inset for bottom navigation
ViewCompat.setOnApplyWindowInsetsListener(binding.bottomNav) { view, insets ->
    val navBars = insets.getInsets(WindowInsetsCompat.Type.navigationBars())
    view.updatePadding(bottom = navBars.bottom)
    insets
}

// IME (keyboard) insets for chat input
ViewCompat.setOnApplyWindowInsetsListener(binding.inputLayout) { view, insets ->
    val imeInsets = insets.getInsets(WindowInsetsCompat.Type.ime())
    val navInsets = insets.getInsets(WindowInsetsCompat.Type.navigationBars())
    view.updatePadding(bottom = maxOf(imeInsets.bottom, navInsets.bottom))
    insets
}
```

### Kotlin Extension Helpers
```kotlin
fun View.applyStatusBarPadding() {
    ViewCompat.setOnApplyWindowInsetsListener(this) { v, insets ->
        v.updatePadding(top = insets.getInsets(WindowInsetsCompat.Type.statusBars()).top)
        insets
    }
}

fun View.applyNavBarPadding() {
    ViewCompat.setOnApplyWindowInsetsListener(this) { v, insets ->
        v.updatePadding(bottom = insets.getInsets(WindowInsetsCompat.Type.navigationBars()).bottom)
        insets
    }
}
```
