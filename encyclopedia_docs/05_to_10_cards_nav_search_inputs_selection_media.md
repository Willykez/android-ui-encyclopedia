# Module 05 — Modern Card Systems
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Elevated Card
```xml
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="6dp"
    app:cardCornerRadius="16dp"
    app:cardBackgroundColor="?attr/colorSurface">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>
```

## 2. Outlined Card
```xml
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="0dp"
    app:strokeWidth="1dp"
    app:strokeColor="?attr/colorOutlineVariant"
    app:cardCornerRadius="12dp">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>
```

## 3. Expandable Card
```kotlin
class ExpandableCardAdapter : ListAdapter<CardItem, ExpandableCardAdapter.VH>(CardDiff()) {
    inner class VH(val b: ItemExpandCardBinding) : RecyclerView.ViewHolder(b.root)

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        with(holder.b) {
            tvTitle.text = item.title
            layoutDetails.visibility = if (item.isExpanded) View.VISIBLE else View.GONE
            ivExpand.rotation = if (item.isExpanded) 180f else 0f

            header.setOnClickListener {
                val updated = item.copy(isExpanded = !item.isExpanded)
                val list = currentList.toMutableList().also { it[holder.adapterPosition] = updated }
                submitList(list)
                ivExpand.animate().rotation(if (updated.isExpanded) 180f else 0f).setDuration(250).start()
            }
        }
    }
}
```

## 4. Swipe / Tinder Cards
```kotlin
// Using Yuyakaido/CardStackView
// implementation 'com.yuyakaido.android:card-stack-view:2.3.4'

class CardStackActivity : AppCompatActivity() {
    private val manager by lazy { CardStackLayoutManager(this, cardListener) }
    
    private val cardListener = object : CardStackListener {
        override fun onCardSwiped(direction: Direction) {
            when (direction) {
                Direction.Left -> viewModel.dislike(currentCard)
                Direction.Right -> viewModel.like(currentCard)
                Direction.Top -> viewModel.superLike(currentCard)
                else -> {}
            }
        }
        override fun onCardDragging(direction: Direction, ratio: Float) {
            binding.likeOverlay.alpha = if (direction == Direction.Right) ratio else 0f
            binding.dislikeOverlay.alpha = if (direction == Direction.Left) ratio else 0f
        }
        override fun onCardRewound() {}
        override fun onCardCanceled() {}
        override fun onCardAppeared(view: View, position: Int) {}
        override fun onCardDisappeared(view: View, position: Int) {}
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        manager.setStackFrom(StackFrom.None)
        manager.setVisibleCount(3)
        manager.setTranslationInterval(8f)
        manager.setScaleInterval(0.95f)
        manager.setSwipeThreshold(0.3f)
        binding.cardStack.layoutManager = manager
        binding.cardStack.adapter = cardAdapter
    }
    
    fun swipeRight() = binding.cardStack.swipe()
    fun rewind() = binding.cardStack.rewind()
}
```

## 5. Glassmorphism Card
```kotlin
// Glassmorphism requires API 31+ RenderEffect or third-party blur
fun createGlassCard(context: Context): MaterialCardView {
    return MaterialCardView(context).apply {
        cardElevation = 0f
        strokeWidth = 1
        setCardBackgroundColor(Color.parseColor("#40FFFFFF")) // semi-transparent white
        radius = 24f.dpToPx
        // Apply blur background via RenderEffect on the container behind it (API 31+)
    }
}

// API 31+ blur effect on a view
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    binding.blurBackground.setRenderEffect(
        RenderEffect.createBlurEffect(20f, 20f, Shader.TileMode.CLAMP)
    )
}
```

```xml
<!-- Glass card overlay -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="0dp"
    app:strokeWidth="1dp"
    app:strokeColor="#40FFFFFF"
    app:cardCornerRadius="20dp"
    app:cardBackgroundColor="#33FFFFFF">
    <!-- frosted glass content -->
</com.google.android.material.card.MaterialCardView>
```

## 6. Neumorphism Card
```xml
<!-- res/drawable/neumorph_card.xml -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Dark shadow (bottom-right) -->
    <item android:left="4dp" android:top="4dp">
        <shape android:shape="rectangle">
            <solid android:color="#D1D9E6" />
            <corners android:radius="16dp" />
        </shape>
    </item>
    <!-- Light shadow (top-left) -->
    <item android:right="4dp" android:bottom="4dp">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <corners android:radius="16dp" />
        </shape>
    </item>
    <!-- Main surface -->
    <item>
        <shape android:shape="rectangle">
            <solid android:color="#E0E5EC" />
            <corners android:radius="16dp" />
        </shape>
    </item>
</layer-list>
```

## 7. Dashboard / Statistic Card
```xml
<!-- res/layout/item_stat_card.xml -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    app:cardCornerRadius="16dp"
    app:cardBackgroundColor="?attr/colorPrimaryContainer">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <ImageView
            android:id="@+id/ivIcon"
            android:layout_width="24dp"
            android:layout_height="24dp"
            android:tint="?attr/colorOnPrimaryContainer" />

        <TextView
            android:id="@+id/tvValue"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:textAppearance="?attr/textAppearanceHeadlineMedium"
            android:textColor="?attr/colorOnPrimaryContainer" />

        <TextView
            android:id="@+id/tvLabel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceLabelMedium"
            android:textColor="?attr/colorOnPrimaryContainer"
            android:alpha="0.7" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

## 8. Carousel Card (ViewPager2 + Peek + Auto-scroll)

**Description:** Horizontally scrollable card carousel with side-peek, scale transform, dot indicator, and auto-scroll.

**Visual Use Case:** Featured banners, product highlights, onboarding slides.

```gradle
implementation 'androidx.viewpager2:viewpager2:1.1.0'
```

```xml
<!-- layout: carousel container -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/carouselPager"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:paddingStart="32dp"
        android:paddingEnd="32dp"
        android:clipToPadding="false"
        android:clipChildren="false"
        android:overScrollMode="never" />

    <LinearLayout
        android:id="@+id/dotsContainer"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="12dp"
        android:orientation="horizontal" />
</LinearLayout>
```

```xml
<!-- res/layout/item_carousel_card.xml -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="8dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="4dp">

    <ImageView
        android:id="@+id/ivCarouselImage"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop" />

    <View
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@drawable/gradient_bottom_overlay" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvCarouselTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FFFFFF"
            android:textAppearance="?attr/textAppearanceTitleMedium" />

        <TextView
            android:id="@+id/tvCarouselSubtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#CCFFFFFF"
            android:textAppearance="?attr/textAppearanceBodySmall" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

```xml
<!-- res/drawable/gradient_bottom_overlay.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient android:angle="270"
        android:startColor="#00000000"
        android:endColor="#CC000000"
        android:type="linear" />
</shape>
```

```xml
<!-- res/drawable/dot_active.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <solid android:color="?attr/colorPrimary" />
    <size android:width="8dp" android:height="8dp" />
</shape>

<!-- res/drawable/dot_inactive.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <solid android:color="?attr/colorOutlineVariant" />
    <size android:width="8dp" android:height="8dp" />
</shape>
```

```kotlin
data class CarouselItem(val imageRes: Int, val title: String, val subtitle: String)

class CarouselAdapter(
    private val items: List<CarouselItem>,
    private val onClick: (CarouselItem) -> Unit
) : RecyclerView.Adapter<CarouselAdapter.VH>() {

    inner class VH(val binding: ItemCarouselCardBinding) :
        RecyclerView.ViewHolder(binding.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) =
        VH(ItemCarouselCardBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = items[position]
        holder.binding.ivCarouselImage.setImageResource(item.imageRes)
        holder.binding.tvCarouselTitle.text = item.title
        holder.binding.tvCarouselSubtitle.text = item.subtitle
        holder.binding.root.setOnClickListener { onClick(item) }
    }

    override fun getItemCount() = items.size
}

// Fragment setup
fun setupCarousel(items: List<CarouselItem>) {
    val adapter = CarouselAdapter(items) { /* handle click */ }
    binding.carouselPager.adapter = adapter
    binding.carouselPager.offscreenPageLimit = 1

    // Peek + scale neighbors
    binding.carouselPager.setPageTransformer(
        CompositePageTransformer().apply {
            addTransformer(MarginPageTransformer(16.dpToPx()))
            addTransformer { page, position ->
                page.scaleY = 1f - 0.1f * kotlin.math.abs(position)
                page.alpha  = 0.6f + (1f - kotlin.math.abs(position)) * 0.4f
            }
        }
    )

    setupDots(items.size)

    binding.carouselPager.registerOnPageChangeCallback(object :
        ViewPager2.OnPageChangeCallback() {
        override fun onPageSelected(position: Int) = updateDots(position)
    })

    startAutoScroll(items.size)
}

private fun setupDots(count: Int) {
    binding.dotsContainer.removeAllViews()
    repeat(count) { i ->
        val dot = View(requireContext()).apply {
            layoutParams = LinearLayout.LayoutParams(8.dpToPx(), 8.dpToPx()).also {
                it.marginStart = 4.dpToPx()
                it.marginEnd   = 4.dpToPx()
            }
            setBackgroundResource(if (i == 0) R.drawable.dot_active else R.drawable.dot_inactive)
        }
        binding.dotsContainer.addView(dot)
    }
}

private fun updateDots(active: Int) {
    for (i in 0 until binding.dotsContainer.childCount) {
        binding.dotsContainer.getChildAt(i).setBackgroundResource(
            if (i == active) R.drawable.dot_active else R.drawable.dot_inactive
        )
    }
}

private fun startAutoScroll(count: Int) {
    val handler = Handler(Looper.getMainLooper())
    val runnable = object : Runnable {
        override fun run() {
            val next = (binding.carouselPager.currentItem + 1) % count
            binding.carouselPager.setCurrentItem(next, true)
            handler.postDelayed(this, 3000)
        }
    }
    handler.postDelayed(runnable, 3000)
    // Cancel in onDestroyView: handler.removeCallbacks(runnable)
}

private fun Int.dpToPx() = (this * resources.displayMetrics.density).toInt()
```

```java
// Java — adapter
public class CarouselAdapter extends RecyclerView.Adapter<CarouselAdapter.VH> {
    private final List<CarouselItem> items;

    public CarouselAdapter(List<CarouselItem> items) { this.items = items; }

    @NonNull @Override
    public VH onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        ItemCarouselCardBinding b = ItemCarouselCardBinding.inflate(
            LayoutInflater.from(parent.getContext()), parent, false);
        return new VH(b);
    }

    @Override
    public void onBindViewHolder(@NonNull VH holder, int position) {
        CarouselItem item = items.get(position);
        holder.b.ivCarouselImage.setImageResource(item.imageRes);
        holder.b.tvCarouselTitle.setText(item.title);
    }

    @Override public int getItemCount() { return items.size(); }

    static class VH extends RecyclerView.ViewHolder {
        final ItemCarouselCardBinding b;
        VH(ItemCarouselCardBinding b) { super(b.getRoot()); this.b = b; }
    }
}
```

### Key Points
| Attribute | Purpose |
|-----------|---------|
| `android:clipToPadding="false"` | Allows adjacent cards to peek |
| `android:clipChildren="false"` | Cards not clipped by parent bounds |
| `offscreenPageLimit = 1` | Pre-loads neighbors for smooth animation |
| `CompositePageTransformer` | Combines scale + margin transforms |

### Best Practices
- ✅ Always cancel auto-scroll handler in `onDestroyView` to prevent memory leaks
- ✅ Use `clipToPadding=false` + horizontal padding for natural peek effect
- ✅ Set `offscreenPageLimit = 1` for smooth scale animation on neighbors
- ✅ Pair with dot indicators for clear page position feedback

---

# Module 06 — Navigation Systems
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Bottom Navigation (Full Setup)

```xml
<!-- activity_main.xml -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/navHostFragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_graph"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        app:menu="@menu/bottom_nav_menu" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(binding.root)

        val navController = findNavController(R.id.navHostFragment)
        val appBarConfig = AppBarConfiguration(
            setOf(R.id.homeFragment, R.id.searchFragment, R.id.profileFragment)
        )
        binding.topAppBar.setupWithNavController(navController, appBarConfig)
        binding.bottomNav.setupWithNavController(navController)

        // Hide bottom nav on certain destinations
        navController.addOnDestinationChangedListener { _, destination, _ ->
            binding.bottomNav.isVisible = when (destination.id) {
                R.id.detailFragment, R.id.editFragment -> false
                else -> true
            }
        }
    }
}
```

## 2. Navigation with Navigation Rail + Bottom Nav Adaptive

```kotlin
// WindowSizeClass adaptive navigation
class MainActivity : AppCompatActivity() {
    private val windowSizeClass by lazy {
        WindowMetricsCalculator.getOrCreate().computeCurrentWindowMetrics(this)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        val widthDp = windowSizeClass.bounds.width() / resources.displayMetrics.density
        
        val navController = findNavController(R.id.navHostFragment)
        
        if (widthDp >= 600f) {
            // Tablet / Large screen: Navigation Rail
            binding.navRail.isVisible = true
            binding.bottomNav.isVisible = false
            binding.navRail.setupWithNavController(navController)
        } else {
            // Phone: Bottom Navigation
            binding.navRail.isVisible = false
            binding.bottomNav.isVisible = true
            binding.bottomNav.setupWithNavController(navController)
        }
    }
}
```

## 3. Gesture Navigation & Back Handling

```kotlin
// Predictive back gesture (API 33+)
class MyFragment : Fragment() {
    private val backCallback = object : OnBackPressedCallback(true) {
        override fun handleOnBackPressed() {
            if (viewModel.hasUnsavedChanges()) {
                showDiscardDialog()
            } else {
                isEnabled = false
                requireActivity().onBackPressedDispatcher.onBackPressed()
            }
        }
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        requireActivity().onBackPressedDispatcher.addCallback(viewLifecycleOwner, backCallback)
    }
}
```

## 4. Modal Bottom Sheet Navigation (Action Sheet)

```kotlin
class ActionSheetFragment : BottomSheetDialogFragment() {

    data class Action(val icon: Int, val label: String, val id: String)

    private val actions = listOf(
        Action(R.drawable.ic_share, "Share", "share"),
        Action(R.drawable.ic_edit, "Edit", "edit"),
        Action(R.drawable.ic_delete, "Delete", "delete")
    )

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                              savedInstanceState: Bundle?): View {
        return inflater.inflate(R.layout.fragment_action_sheet, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val adapter = ActionAdapter(actions) { action ->
            (parentFragment as? ActionHandler)?.onAction(action.id)
            dismiss()
        }
        view.findViewById<RecyclerView>(R.id.rvActions).adapter = adapter
    }
}
```

## 5. Tabs-Based Navigation (TabLayout + ViewPager2)

**Visual Use Case:** Profile sections (Posts/About/Photos), dashboard tabs, content categories.

```xml
<!-- layout -->
<LinearLayout android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar" android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize" app:title="Profile" />
        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tabLayout" android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:tabMode="fixed" app:tabGravity="fill"
            app:tabIndicatorFullWidth="false"
            app:tabIndicatorColor="?attr/colorPrimary"
            app:tabTextColor="?attr/colorOnSurfaceVariant"
            app:tabSelectedTextColor="?attr/colorPrimary" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

```kotlin
class TabbedFragment : Fragment(R.layout.fragment_tabbed) {

    private val tabTitles = listOf("Posts", "About", "Media")
    private val tabIcons  = listOf(R.drawable.ic_post, R.drawable.ic_info, R.drawable.ic_photo)

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val pagerAdapter = ProfilePagerAdapter(this)
        binding.viewPager.adapter = pagerAdapter

        TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, position ->
            tab.text = tabTitles[position]
            tab.setIcon(tabIcons[position])
        }.attach()

        // Badge on tab
        binding.tabLayout.getTabAt(0)?.orCreateBadge?.apply {
            number = 3; isVisible = true
        }
    }
}

class ProfilePagerAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
    override fun getItemCount() = 3
    override fun createFragment(position: Int): Fragment = when (position) {
        0 -> PostsFragment()
        1 -> AboutFragment()
        else -> MediaFragment()
    }
}
```

```java
// Java
new TabLayoutMediator(binding.tabLayout, binding.viewPager,
    (tab, position) -> {
        String[] titles = {"Posts", "About", "Media"};
        tab.setText(titles[position]);
    }
).attach();
```

---

## 6. Multi-Pane Layout (Two-Column — Phone + Tablet)

**Visual Use Case:** Email (inbox + message), news (list + article), settings.

```xml
<!-- res/layout/activity_main.xml — phone: single pane -->
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/fragmentContainer"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

```xml
<!-- res/layout-w600dp/activity_main.xml — tablet: two panes -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="horizontal">

    <FrameLayout android:id="@+id/listContainer"
        android:layout_width="320dp" android:layout_height="match_parent"
        android:background="?attr/colorSurface" />

    <com.google.android.material.divider.MaterialDivider
        android:layout_width="1dp" android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/detailContainer"
        android:layout_width="0dp" android:layout_height="match_parent"
        android:layout_weight="1" />
</LinearLayout>
```

```kotlin
class MultiPaneActivity : AppCompatActivity() {

    private val isTwoPane by lazy { binding.detailContainer != null }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        if (savedInstanceState == null) {
            supportFragmentManager.commit { replace(R.id.listContainer, ListFragment()) }
            if (isTwoPane) supportFragmentManager.commit {
                replace(R.id.detailContainer, EmptyDetailFragment())
            }
        }
    }

    fun showDetail(itemId: String) {
        val detail = DetailFragment.newInstance(itemId)
        if (isTwoPane) {
            supportFragmentManager.commit { replace(R.id.detailContainer, detail) }
        } else {
            supportFragmentManager.commit {
                replace(R.id.listContainer, detail)
                addToBackStack(null)
            }
        }
    }
}
```

```java
// Java
boolean isTwoPane = binding.detailContainer != null;

public void showDetail(String itemId) {
    Fragment detail = DetailFragment.newInstance(itemId);
    if (isTwoPane) {
        getSupportFragmentManager().beginTransaction()
            .replace(R.id.detailContainer, detail).commit();
    } else {
        getSupportFragmentManager().beginTransaction()
            .replace(R.id.listContainer, detail)
            .addToBackStack(null).commit();
    }
}
```

---

## 7. Master-Detail Pattern (Shared ViewModel)

**Visual Use Case:** Contacts → Contact detail, Settings → Settings item, News → Article.

```kotlin
// Shared ViewModel
class MasterDetailViewModel : ViewModel() {
    private val _selectedId = MutableStateFlow<String?>(null)
    val selectedId: StateFlow<String?> = _selectedId.asStateFlow()
    fun select(id: String) { _selectedId.value = id }
}

// Master (list) Fragment
class MasterFragment : Fragment(R.layout.fragment_master) {
    private val viewModel: MasterDetailViewModel by activityViewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val adapter = ItemAdapter { item ->
            viewModel.select(item.id)
            // On phone: navigate; on tablet: detail pane updates automatically
            if (requireActivity() is SinglePaneHost) {
                findNavController().navigate(
                    R.id.action_master_to_detail,
                    bundleOf("itemId" to item.id)
                )
            }
        }
        binding.recyclerView.adapter = adapter
    }
}

// Detail Fragment
class DetailFragment : Fragment(R.layout.fragment_detail) {
    private val sharedVm: MasterDetailViewModel by activityViewModels()
    private val args by navArgs<DetailFragmentArgs>()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val id = args.itemId ?: sharedVm.selectedId.value ?: return
        viewModel.loadDetail(id).observe(viewLifecycleOwner) { detail ->
            binding.tvTitle.text = detail.title
            binding.tvBody.text  = detail.body
        }
    }
}
```

---

## 8. Tablet / Large Screen Adaptive Navigation

**Description:** Bottom nav on phones → Rail on medium → Persistent drawer on large screens using layout qualifiers.

```gradle
implementation 'androidx.window:window:1.2.0'
```

```xml
<!-- res/layout/activity_main.xml — phone -->
<androidx.coordinatorlayout.widget.CoordinatorLayout ...>
    <androidx.fragment.app.FragmentContainerView android:id="@+id/navHostFragment" ... />
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav" android:layout_gravity="bottom"
        app:menu="@menu/nav_menu" ... />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```xml
<!-- res/layout-w600dp/activity_main.xml — tablet (rail) -->
<LinearLayout android:orientation="horizontal" ...>
    <com.google.android.material.navigationrail.NavigationRailView
        android:id="@+id/navRail" android:layout_width="wrap_content"
        android:layout_height="match_parent" app:menu="@menu/nav_menu" />
    <androidx.fragment.app.FragmentContainerView android:id="@+id/navHostFragment"
        android:layout_width="0dp" android:layout_weight="1" ... />
</LinearLayout>
```

```xml
<!-- res/layout-w1240dp/activity_main.xml — large screen (persistent drawer) -->
<LinearLayout android:orientation="horizontal" ...>
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navDrawer" android:layout_width="280dp"
        app:menu="@menu/nav_menu" ... />
    <androidx.fragment.app.FragmentContainerView android:id="@+id/navHostFragment"
        android:layout_width="0dp" android:layout_weight="1" ... />
</LinearLayout>
```

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        val navController = findNavController(R.id.navHostFragment)
        when {
            binding.navDrawer != null -> binding.navDrawer!!.setupWithNavController(navController)
            binding.navRail   != null -> binding.navRail!!.setupWithNavController(navController)
            else                      -> binding.bottomNav!!.setupWithNavController(navController)
        }
    }
}
```

```java
// Java
NavController navController = Navigation.findNavController(this, R.id.navHostFragment);
if (binding.navDrawer != null)
    NavigationUI.setupWithNavController(binding.navDrawer, navController);
else if (binding.navRail != null)
    NavigationUI.setupWithNavController(binding.navRail, navController);
else
    NavigationUI.setupWithNavController(binding.bottomNav, navController);
```

---

# Module 07 — Search Systems
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. SearchBar + SearchView (M3 — Full Implementation)

```kotlin
class SearchActivity : AppCompatActivity() {
    private val viewModel: SearchViewModel by viewModels()
    private lateinit var resultsAdapter: SearchResultsAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)

        setupSearch()
        observeResults()
    }

    private fun setupSearch() {
        resultsAdapter = SearchResultsAdapter { result ->
            navigateToDetail(result)
        }
        binding.rvResults.adapter = resultsAdapter

        // SearchView's EditText drives the query
        binding.searchView.editText.addTextChangedListener { text ->
            viewModel.onQueryChanged(text.toString())
        }

        // Submit on keyboard action
        binding.searchView.editText.setOnEditorActionListener { _, _, _ ->
            val query = binding.searchView.text.toString()
            viewModel.submitSearch(query)
            binding.searchBar.setText(query)
            binding.searchView.hide()
            false
        }
    }

    private fun observeResults() {
        lifecycleScope.launch {
            viewModel.searchResults.collectLatest { results ->
                resultsAdapter.submitList(results)
            }
        }
    }
}
```

## 2. Search with Filters

```kotlin
// Combine SearchView with ChipGroup filters
class FilteredSearchFragment : Fragment() {

    private val activeFilters = mutableSetOf<String>()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Filter chips
        binding.chipGroup.setOnCheckedStateChangeListener { group, checkedIds ->
            activeFilters.clear()
            checkedIds.forEach { id ->
                activeFilters.add(group.findViewById<Chip>(id).tag as String)
            }
            performSearch()
        }

        // Search input
        binding.searchView.editText.addTextChangedListener {
            performSearch()
        }
    }

    private fun performSearch() {
        val query = binding.searchView.text.toString()
        viewModel.search(query, filters = activeFilters.toList())
    }
}
```

## 3. Instant / Live Search with Debounce

```kotlin
// ViewModel with debounce
class SearchViewModel : ViewModel() {
    private val queryFlow = MutableStateFlow("")

    val results = queryFlow
        .debounce(300) // wait 300ms after user stops typing
        .filter { it.length >= 2 } // min 2 chars
        .distinctUntilChanged()
        .flatMapLatest { query ->
            if (query.isEmpty()) flowOf(emptyList())
            else repository.search(query)
        }
        .stateIn(viewModelScope, SharingStarted.Lazily, emptyList())

    fun onQueryChanged(q: String) { queryFlow.value = q }
}
```

## 4. Floating / Toolbar Search (Expand Animation)

**Visual Use Case:** Toolbar search icon that expands in-place to a full-width field.

```xml
<!-- Toolbar + collapsible search overlay in AppBarLayout -->
<com.google.android.material.appbar.AppBarLayout ...>

    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:title="Home"
        app:menu="@menu/menu_search_icon" />

    <!-- Slides in over the toolbar when search is opened -->
    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etFloatingSearch"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:hint="Search…"
        android:background="?attr/colorSurface"
        android:paddingStart="16dp" android:paddingEnd="16dp"
        android:imeOptions="actionSearch" android:singleLine="true"
        android:visibility="gone" android:alpha="0" />
</com.google.android.material.appbar.AppBarLayout>
```

```xml
<!-- res/menu/menu_search_icon.xml -->
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item android:id="@+id/action_search" android:icon="@drawable/ic_search"
        android:title="Search" app:showAsAction="ifRoom" />
</menu>
```

```kotlin
private var searchOpen = false

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    binding.toolbar.setOnMenuItemClickListener { item ->
        if (item.itemId == R.id.action_search) { openSearch(); true } else false
    }
    requireActivity().onBackPressedDispatcher.addCallback(viewLifecycleOwner) {
        if (searchOpen) closeSearch()
        else { isEnabled = false; requireActivity().onBackPressedDispatcher.onBackPressed() }
    }
}

private fun openSearch() {
    searchOpen = true
    binding.etFloatingSearch.visibility = View.VISIBLE
    binding.toolbar.animate().alpha(0f).setDuration(200)
        .withEndAction { binding.toolbar.visibility = View.GONE }.start()
    binding.etFloatingSearch.animate().alpha(1f).setDuration(200).start()
    binding.etFloatingSearch.requestFocus()
    requireContext().getSystemService(InputMethodManager::class.java)
        .showSoftInput(binding.etFloatingSearch, InputMethodManager.SHOW_IMPLICIT)
}

private fun closeSearch() {
    searchOpen = false
    binding.toolbar.visibility = View.VISIBLE
    binding.toolbar.animate().alpha(1f).setDuration(200).start()
    binding.etFloatingSearch.animate().alpha(0f).setDuration(200)
        .withEndAction {
            binding.etFloatingSearch.visibility = View.GONE
            binding.etFloatingSearch.setText("")
        }.start()
}
```

```java
// Java
private void openSearch() {
    binding.etFloatingSearch.setVisibility(View.VISIBLE);
    binding.toolbar.animate().alpha(0f).setDuration(200)
        .withEndAction(() -> binding.toolbar.setVisibility(View.GONE)).start();
    binding.etFloatingSearch.animate().alpha(1f).setDuration(200).start();
    binding.etFloatingSearch.requestFocus();
    ((InputMethodManager) getSystemService(INPUT_METHOD_SERVICE))
        .showSoftInput(binding.etFloatingSearch, InputMethodManager.SHOW_IMPLICIT);
}
```

---

## 5. Voice Search

**Visual Use Case:** Hands-free search, accessibility, chat input.

```xml
<!-- AndroidManifest.xml -->
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

```xml
<!-- Mic button inside TextInputLayout -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:hint="Search"
    app:endIconMode="custom"
    app:endIconDrawable="@drawable/ic_mic"
    app:endIconContentDescription="Voice search"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etSearch"
        android:layout_width="match_parent" android:layout_height="wrap_content" />
</com.google.android.material.textfield.TextInputLayout>
```

```kotlin
private val voiceLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    if (result.resultCode == Activity.RESULT_OK) {
        val text = result.data
            ?.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS)
            ?.firstOrNull() ?: return@registerForActivityResult
        binding.etSearch.setText(text)
        performSearch(text)
    }
}

private fun startVoiceSearch() {
    if (!SpeechRecognizer.isRecognitionAvailable(requireContext())) {
        Snackbar.make(binding.root, "Speech not supported on this device", Snackbar.LENGTH_SHORT).show()
        return
    }
    val intent = Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH).apply {
        putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM)
        putExtra(RecognizerIntent.EXTRA_LANGUAGE, Locale.getDefault())
        putExtra(RecognizerIntent.EXTRA_PROMPT, "Speak to search…")
        putExtra(RecognizerIntent.EXTRA_MAX_RESULTS, 1)
    }
    try { voiceLauncher.launch(intent) }
    catch (e: ActivityNotFoundException) {
        Snackbar.make(binding.root, "Install Google app for voice search", Snackbar.LENGTH_SHORT).show()
    }
}

// Wire mic button
binding.tilSearch.setEndIconOnClickListener { startVoiceSearch() }
```

```java
// Java
private final ActivityResultLauncher<Intent> voiceLauncher =
    registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> {
        if (result.getResultCode() == Activity.RESULT_OK && result.getData() != null) {
            ArrayList<String> matches = result.getData()
                .getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);
            if (matches != null && !matches.isEmpty()) {
                binding.etSearch.setText(matches.get(0));
                performSearch(matches.get(0));
            }
        }
    });

private void startVoiceSearch() {
    Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
    intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
    intent.putExtra(RecognizerIntent.EXTRA_PROMPT, "Speak to search…");
    try { voiceLauncher.launch(intent); }
    catch (ActivityNotFoundException e) {
        Toast.makeText(getContext(), "Speech not available", Toast.LENGTH_SHORT).show();
    }
}
```

---

## 6. Expandable Search Bar (Slide-out from Icon)

**Visual Use Case:** Compact toolbars where search is secondary; slides out horizontally.

```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize">

    <TextView android:id="@+id/tvTitle"
        android:layout_width="0dp" android:layout_height="wrap_content"
        android:text="Home" android:textAppearance="?attr/textAppearanceTitleLarge"
        android:layout_marginStart="16dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toStartOf="@id/btnExpandSearch"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />

    <ImageButton android:id="@+id/btnExpandSearch"
        android:layout_width="48dp" android:layout_height="48dp"
        android:src="@drawable/ic_search"
        android:background="?attr/selectableItemBackgroundBorderless"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        android:layout_marginEnd="8dp" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etExpandSearch"
        android:layout_width="0dp" android:layout_height="match_parent"
        android:background="?attr/colorSurface"
        android:hint="Search…" android:padding="12dp"
        android:visibility="gone"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

```kotlin
binding.btnExpandSearch.setOnClickListener {
    with(binding.etExpandSearch) {
        visibility = View.VISIBLE
        translationX = width.toFloat()
        animate().translationX(0f).setDuration(280)
            .setInterpolator(DecelerateInterpolator()).start()
        requestFocus()
    }
    binding.tvTitle.animate().alpha(0f).setDuration(200).start()
    requireContext().getSystemService(InputMethodManager::class.java)
        .showSoftInput(binding.etExpandSearch, InputMethodManager.SHOW_IMPLICIT)
}

fun collapseSearch() {
    binding.etExpandSearch.animate()
        .translationX(binding.etExpandSearch.width.toFloat())
        .setDuration(250)
        .withEndAction {
            binding.etExpandSearch.visibility = View.GONE
            binding.etExpandSearch.setText("")
        }.start()
    binding.tvTitle.animate().alpha(1f).setDuration(200).start()
}
```

---

## 7. AI-Assisted Search (Suggestion Chips + Query Classification)

**Visual Use Case:** Smart search with AI-generated suggestions that update as user types.

```xml
<LinearLayout android:layout_width="match_parent"
    android:layout_height="wrap_content" android:orientation="vertical">

    <com.google.android.material.search.SearchBar
        android:id="@+id/searchBar"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:hint="Ask anything…" />

    <!-- Scrollable suggestion chips -->
    <HorizontalScrollView android:layout_width="match_parent"
        android:layout_height="wrap_content" android:scrollbars="none"
        android:paddingStart="16dp" android:paddingEnd="16dp" android:paddingTop="8dp">
        <com.google.android.material.chip.ChipGroup
            android:id="@+id/chipGroupSuggestions"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            app:singleLine="true" app:chipSpacingHorizontal="8dp" />
    </HorizontalScrollView>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvResults"
        android:layout_width="match_parent" android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

```kotlin
private val baseSuggestions = listOf(
    "Nearby restaurants", "Weather today",
    "Latest news", "Translate text", "Set reminder"
)

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    updateSuggestions(baseSuggestions)

    binding.searchBar.editText?.addTextChangedListener { text ->
        val query = text.toString()
        val filtered = if (query.length >= 2)
            baseSuggestions.filter { it.contains(query, ignoreCase = true) }
        else baseSuggestions
        updateSuggestions(filtered)
        if (query.length >= 2) viewModel.aiSearch(query)
    }
}

private fun updateSuggestions(items: List<String>) {
    binding.chipGroupSuggestions.removeAllViews()
    items.forEach { label ->
        val chip = Chip(requireContext()).apply {
            text = label
            isCheckable = false
            setChipIconResource(R.drawable.ic_search_small)
            setOnClickListener {
                binding.searchBar.setText(label)
                viewModel.aiSearch(label)
            }
        }
        binding.chipGroupSuggestions.addView(chip)
    }
}
```

---

# Module 08 — Input Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. OTP / PIN Input

```xml
<!-- 6-digit OTP using individual TextInputEditTexts -->
<LinearLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:gravity="center"
    android:layout_gravity="center_horizontal">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otpDigit1"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="4dp"
        android:inputType="number"
        android:maxLength="1"
        android:gravity="center"
        android:textAppearance="?attr/textAppearanceTitleLarge"
        android:background="@drawable/otp_box_bg"
        android:textAlignment="center" />

    <!-- Repeat for digits 2–6 -->
</LinearLayout>
```

```kotlin
// Auto-advance OTP fields
class OtpInputHelper(private val fields: List<EditText>) {
    init {
        fields.forEachIndexed { index, field ->
            field.addTextChangedListener {
                if (it?.length == 1 && index < fields.lastIndex) {
                    fields[index + 1].requestFocus()
                }
            }
            field.setOnKeyListener { _, keyCode, event ->
                if (keyCode == KeyEvent.KEYCODE_DEL
                    && event.action == KeyEvent.ACTION_DOWN
                    && field.text.isNullOrEmpty()
                    && index > 0) {
                    fields[index - 1].requestFocus()
                    fields[index - 1].setText("")
                }
                false
            }
        }
    }

    fun getOtp() = fields.joinToString("") { it.text.toString() }
    fun clear() = fields.forEach { it.setText("") }
    fun isComplete() = fields.all { it.text?.length == 1 }
}

// Use it
val otpHelper = OtpInputHelper(listOf(
    binding.otpDigit1, binding.otpDigit2, binding.otpDigit3,
    binding.otpDigit4, binding.otpDigit5, binding.otpDigit6
))
```

## 2. Password Field

```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/tilPassword"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Password"
    app:endIconMode="password_toggle"
    app:passwordToggleEnabled="true"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etPassword"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPassword"
        android:imeOptions="actionDone" />
</com.google.android.material.textfield.TextInputLayout>
```

```kotlin
// Password strength indicator
binding.etPassword.addTextChangedListener { text ->
    val strength = calculatePasswordStrength(text.toString())
    binding.progressStrength.progress = strength.score
    binding.tvStrength.text = strength.label
    binding.tvStrength.setTextColor(strength.color)
}

fun calculatePasswordStrength(password: String): PasswordStrength {
    var score = 0
    if (password.length >= 8) score += 25
    if (password.any { it.isUpperCase() }) score += 25
    if (password.any { it.isDigit() }) score += 25
    if (password.any { !it.isLetterOrDigit() }) score += 25
    return PasswordStrength(score, when {
        score <= 25 -> "Weak"
        score <= 50 -> "Fair"
        score <= 75 -> "Good"
        else -> "Strong"
    })
}
```

## 3. AutoComplete / Chips Input (Tag Editor)

```kotlin
// Chip-based tag input
class TagInputView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : com.google.android.material.chip.ChipGroup(context, attrs) {

    private val tags = mutableListOf<String>()
    private val inputEditText = EditText(context)

    init {
        isChipSpacingHorizontalSet = true
        addView(inputEditText)
        inputEditText.hint = "Add tag..."
        inputEditText.setOnEditorActionListener { _, actionId, _ ->
            if (actionId == EditorInfo.IME_ACTION_DONE) {
                val tag = inputEditText.text.toString().trim()
                if (tag.isNotEmpty() && !tags.contains(tag)) addTag(tag)
                inputEditText.setText("")
                true
            } else false
        }
    }

    fun addTag(tag: String) {
        tags.add(tag)
        val chip = Chip(context).apply {
            text = tag
            isCloseIconVisible = true
            setOnCloseIconClickListener { removeTag(tag); removeView(this) }
        }
        addView(chip, childCount - 1) // insert before EditText
    }

    fun removeTag(tag: String) = tags.remove(tag)
    fun getTags(): List<String> = tags.toList()
}
```

## 4. Exposed Dropdown Menu

```kotlin
// Reusable dropdown setup
fun TextInputLayout.setupDropdown(
    items: List<String>,
    onSelected: (String, Int) -> Unit
) {
    val adapter = ArrayAdapter(context, R.layout.list_item_dropdown, items)
    (editText as? AutoCompleteTextView)?.apply {
        setAdapter(adapter)
        setOnItemClickListener { _, _, position, _ ->
            onSelected(items[position], position)
        }
    }
}

// Usage
binding.tilCategory.setupDropdown(
    listOf("Technology", "Health", "Finance")
) { item, index ->
    viewModel.setCategory(item)
}
```

## 5. Rich Text Editor (Spannable Bold/Italic/Underline/Color)

**Visual Use Case:** Notes app, comment editor, any field needing inline formatting.

```xml
<LinearLayout android:layout_width="match_parent"
    android:layout_height="match_parent" android:orientation="vertical">

    <!-- Formatting toolbar -->
    <HorizontalScrollView android:layout_width="match_parent"
        android:layout_height="wrap_content" android:background="?attr/colorSurface">
        <LinearLayout android:layout_width="wrap_content"
            android:layout_height="48dp" android:orientation="horizontal"
            android:gravity="center_vertical">
            <ImageButton android:id="@+id/btnBold"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_format_bold"
                android:background="?attr/selectableItemBackgroundBorderless" />
            <ImageButton android:id="@+id/btnItalic"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_format_italic"
                android:background="?attr/selectableItemBackgroundBorderless" />
            <ImageButton android:id="@+id/btnUnderline"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_format_underlined"
                android:background="?attr/selectableItemBackgroundBorderless" />
            <ImageButton android:id="@+id/btnStrike"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_strikethrough_s"
                android:background="?attr/selectableItemBackgroundBorderless" />
            <ImageButton android:id="@+id/btnColor"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_format_color_text"
                android:background="?attr/selectableItemBackgroundBorderless" />
        </LinearLayout>
    </HorizontalScrollView>

    <com.google.android.material.divider.MaterialDivider
        android:layout_width="match_parent" android:layout_height="wrap_content" />

    <EditText android:id="@+id/etRichEditor"
        android:layout_width="match_parent" android:layout_height="0dp"
        android:layout_weight="1"
        android:padding="16dp" android:background="@null"
        android:hint="Start typing…"
        android:gravity="top"
        android:inputType="textMultiLine|textCapSentences"
        android:textAppearance="?attr/textAppearanceBodyLarge" />
</LinearLayout>
```

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    binding.btnBold.setOnClickListener      { applySpan(StyleSpan(Typeface.BOLD)) }
    binding.btnItalic.setOnClickListener    { applySpan(StyleSpan(Typeface.ITALIC)) }
    binding.btnUnderline.setOnClickListener { applySpan(UnderlineSpan()) }
    binding.btnStrike.setOnClickListener    { applySpan(StrikethroughSpan()) }
    binding.btnColor.setOnClickListener     { applySpan(ForegroundColorSpan(Color.parseColor("#E91E63"))) }
}

private fun applySpan(span: Any) {
    val et    = binding.etRichEditor
    val start = et.selectionStart
    val end   = et.selectionEnd
    if (start == end) return  // no selection

    val spannable = SpannableStringBuilder(et.text)
    val existing  = spannable.getSpans(start, end, span.javaClass)

    if (existing.isNotEmpty()) {
        existing.forEach { spannable.removeSpan(it) }  // toggle off
    } else {
        spannable.setSpan(span, start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE)
    }

    val cursor = et.selectionEnd
    et.text = spannable
    et.setSelection(cursor)
}

// Persist as HTML
fun getHtml(): String =
    Html.toHtml(binding.etRichEditor.text, Html.TO_HTML_PARAGRAPH_LINES_INDIVIDUAL)

// Load from HTML
fun setHtml(html: String) =
    binding.etRichEditor.setText(Html.fromHtml(html, Html.FROM_HTML_MODE_COMPACT))
```

```java
// Java
private void applySpan(Object span) {
    int start = binding.etRichEditor.getSelectionStart();
    int end   = binding.etRichEditor.getSelectionEnd();
    if (start == end) return;

    SpannableStringBuilder sb = new SpannableStringBuilder(binding.etRichEditor.getText());
    Object[] existing = sb.getSpans(start, end, span.getClass());
    if (existing.length > 0) {
        for (Object s : existing) sb.removeSpan(s);
    } else {
        sb.setSpan(span, start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
    }
    binding.etRichEditor.setText(sb);
}
```

---

## 6. Emoji Picker Bottom Sheet

**Visual Use Case:** Chat apps, reactions, comment fields.

```kotlin
class EmojiPickerSheet : BottomSheetDialogFragment() {

    var onEmojiSelected: ((String) -> Unit)? = null

    private val emojiCategories = mapOf(
        "😊" to listOf("😀","😂","😍","🥰","😎","🤔","😢","😡","👍","🙏","❤️","🔥","⭐","🎉","💯"),
        "🐶" to listOf("🐶","🐱","🐭","🐹","🦊","🐻","🐼","🐯","🦁","🐮"),
        "🍕" to listOf("🍕","🍔","🍟","🌭","🍿","🧁","🍰","🎂","🍩","🍪"),
        "⚽" to listOf("⚽","🏀","🏈","⚾","🎾","🏐","🏉","🎱","🏓","🏸")
    )

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?) =
        inflater.inflate(R.layout.fragment_emoji_picker, container, false)

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val categoryRow = view.findViewById<LinearLayout>(R.id.categoryRow)
        val rv = view.findViewById<RecyclerView>(R.id.rvEmojis)

        fun showCategory(key: String) {
            rv.layoutManager = GridLayoutManager(requireContext(), 8)
            rv.adapter = EmojiGridAdapter(emojiCategories[key] ?: emptyList()) { emoji ->
                onEmojiSelected?.invoke(emoji)
                dismiss()
            }
        }

        emojiCategories.keys.forEach { key ->
            val btn = MaterialButton(requireContext(), null,
                com.google.android.material.R.attr.materialButtonOutlinedStyle).apply {
                text = key; textSize = 20f
                setPadding(16, 0, 16, 0)
                setOnClickListener { showCategory(key) }
            }
            categoryRow.addView(btn)
        }

        showCategory(emojiCategories.keys.first())
    }

    companion object {
        fun show(fm: FragmentManager, target: EditText) {
            EmojiPickerSheet().also { sheet ->
                sheet.onEmojiSelected = { emoji ->
                    val cursor = target.selectionStart.coerceAtLeast(0)
                    target.text?.insert(cursor, emoji)
                }
            }.show(fm, "emoji_picker")
        }
    }
}
```

```xml
<!-- res/layout/fragment_emoji_picker.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="vertical">

    <HorizontalScrollView android:layout_width="match_parent"
        android:layout_height="48dp" android:scrollbars="none"
        android:background="?attr/colorSurface">
        <LinearLayout android:id="@+id/categoryRow"
            android:layout_width="wrap_content" android:layout_height="match_parent"
            android:orientation="horizontal" android:gravity="center_vertical"
            android:paddingStart="8dp" android:paddingEnd="8dp" />
    </HorizontalScrollView>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvEmojis"
        android:layout_width="match_parent" android:layout_height="260dp"
        android:padding="8dp" />
</LinearLayout>
```

```kotlin
// Lightweight emoji grid adapter
class EmojiGridAdapter(
    private val items: List<String>,
    private val onPick: (String) -> Unit
) : RecyclerView.Adapter<EmojiGridAdapter.VH>() {

    inner class VH(val tv: TextView) : RecyclerView.ViewHolder(tv)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = VH(
        TextView(parent.context).apply {
            layoutParams = RecyclerView.LayoutParams(48.dp(context), 48.dp(context))
            textSize = 24f; gravity = Gravity.CENTER
        }
    )

    override fun onBindViewHolder(h: VH, pos: Int) {
        h.tv.text = items[pos]
        h.tv.setOnClickListener { onPick(items[pos]) }
    }

    override fun getItemCount() = items.size

    private fun Int.dp(ctx: Context) = (this * ctx.resources.displayMetrics.density).toInt()
}
```

```kotlin
// Usage: anywhere in a fragment
binding.btnEmoji.setOnClickListener {
    EmojiPickerSheet.show(childFragmentManager, binding.etMessage)
}
```

---

# Module 09 — Selection Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Switch (MaterialSwitch)

```xml
<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/switchDarkMode"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Dark Mode"
    android:checked="false" />
```

## 2. Checkbox

```xml
<com.google.android.material.checkbox.MaterialCheckBox
    android:id="@+id/checkTerms"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="I agree to the Terms &amp; Conditions"
    app:buttonTint="?attr/colorPrimary" />
```

## 3. Radio Group

```xml
<RadioGroup
    android:id="@+id/radioGroup"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <com.google.android.material.radiobutton.MaterialRadioButton
        android:id="@+id/rbOption1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Option 1" />

    <com.google.android.material.radiobutton.MaterialRadioButton
        android:id="@+id/rbOption2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Option 2" />
</RadioGroup>
```

```kotlin
binding.radioGroup.setOnCheckedChangeListener { _, checkedId ->
    when (checkedId) {
        R.id.rbOption1 -> viewModel.selectOption(1)
        R.id.rbOption2 -> viewModel.selectOption(2)
    }
}
```

## 4. Date Range Selector

```kotlin
// Material Date Range Picker
val picker = MaterialDatePicker.Builder.dateRangePicker()
    .setTitleText("Select rental period")
    .setCalendarConstraints(
        CalendarConstraints.Builder()
            .setStart(MaterialDatePicker.thisMonthInUtcMilliseconds())
            .setValidator(DateValidatorPointForward.now())
            .build()
    )
    .build()

picker.addOnPositiveButtonClickListener { selection ->
    val startMs = selection.first ?: return@addOnPositiveButtonClickListener
    val endMs = selection.second ?: return@addOnPositiveButtonClickListener
    val sdf = SimpleDateFormat("MMM d, yyyy", Locale.getDefault())
    binding.tvDateRange.text = "${sdf.format(startMs)} – ${sdf.format(endMs)}"
}
picker.show(childFragmentManager, "DATE_RANGE")
```

## 4. Segmented Control (M3 ToggleGroup — Icon + Text variants)

**Visual Use Case:** View mode switch (List/Grid/Map), period filter (Day/Week/Month).

```xml
<!-- Icon-only segmented control -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/segmentedView"
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    app:singleSelection="true" app:selectionRequired="true">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnList"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_list" android:contentDescription="List view" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnGrid"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_grid" android:contentDescription="Grid view" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnMap"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_map" android:contentDescription="Map view" />
</com.google.android.material.button.MaterialButtonToggleGroup>

<!-- Text-label segmented control -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/segmentedPeriod"
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    app:singleSelection="true" app:selectionRequired="true">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnDay" android:layout_width="wrap_content"
        android:layout_height="wrap_content" android:text="Day" android:textAllCaps="false"
        style="@style/Widget.Material3.Button.OutlinedButton" />
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnWeek" android:layout_width="wrap_content"
        android:layout_height="wrap_content" android:text="Week" android:textAllCaps="false"
        style="@style/Widget.Material3.Button.OutlinedButton" />
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnMonth" android:layout_width="wrap_content"
        android:layout_height="wrap_content" android:text="Month" android:textAllCaps="false"
        style="@style/Widget.Material3.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

```kotlin
binding.segmentedView.addOnButtonCheckedListener { _, checkedId, isChecked ->
    if (isChecked) when (checkedId) {
        R.id.btnList -> switchToList()
        R.id.btnGrid -> switchToGrid()
        R.id.btnMap  -> switchToMap()
    }
}
// Default selection
binding.segmentedView.check(R.id.btnList)

// Read current
val current = binding.segmentedView.checkedButtonId
```

```java
// Java
binding.segmentedPeriod.addOnButtonCheckedListener((group, checkedId, isChecked) -> {
    if (!isChecked) return;
    if (checkedId == R.id.btnDay)   showDayView();
    else if (checkedId == R.id.btnWeek)  showWeekView();
    else if (checkedId == R.id.btnMonth) showMonthView();
});
binding.segmentedPeriod.check(R.id.btnDay);
```

---

## 5. Toggle Group (Multi-Select — Text Formatting)

**Visual Use Case:** Bold/Italic/Underline toolbar, multi-tag selector, alignment picker.

```xml
<!-- Multi-select icon toggle group -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/formatGroup"
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    app:singleSelection="false" app:selectionRequired="false">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnBoldToggle"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_format_bold" android:checkable="true" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnItalicToggle"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_format_italic" android:checkable="true" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnUnderlineToggle"
        style="@style/Widget.Material3.Button.IconButton.Outlined"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:icon="@drawable/ic_format_underlined" android:checkable="true" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

```kotlin
binding.formatGroup.addOnButtonCheckedListener { group, _, _ ->
    val ids       = group.checkedButtonIds
    val isBold      = R.id.btnBoldToggle    in ids
    val isItalic    = R.id.btnItalicToggle  in ids
    val isUnderline = R.id.btnUnderlineToggle in ids
    applyFormatting(isBold, isItalic, isUnderline)
}

// Programmatic check / uncheck
binding.formatGroup.check(R.id.btnBoldToggle)
binding.formatGroup.uncheck(R.id.btnItalicToggle)
```

---

## 6. Time Selector (MaterialTimePicker — 12h / 24h / Keyboard)

**Visual Use Case:** Alarm time, meeting scheduler, opening hours picker.

```kotlin
fun showTimePicker(
    fm: FragmentManager,
    initialHour: Int = 8,
    initialMinute: Int = 0,
    use24h: Boolean = false,
    onSelected: (hour: Int, minute: Int) -> Unit
) {
    MaterialTimePicker.Builder()
        .setTimeFormat(if (use24h) TimeFormat.CLOCK_24H else TimeFormat.CLOCK_12H)
        .setHour(initialHour)
        .setMinute(initialMinute)
        .setTitleText("Select time")
        .setInputMode(MaterialTimePicker.INPUT_MODE_CLOCK) // or INPUT_MODE_KEYBOARD
        .build()
        .also { picker ->
            picker.addOnPositiveButtonClickListener {
                onSelected(picker.hour, picker.minute)
            }
            picker.show(fm, "TIME_PICKER")
        }
}

// Usage
binding.btnPickTime.setOnClickListener {
    showTimePicker(supportFragmentManager) { hour, minute ->
        binding.tvTime.text = "%02d:%02d".format(hour, minute)
    }
}
```

```java
// Java
MaterialTimePicker picker = new MaterialTimePicker.Builder()
    .setTimeFormat(TimeFormat.CLOCK_12H)
    .setHour(8).setMinute(0)
    .setTitleText("Select time")
    .build();
picker.addOnPositiveButtonClickListener(v ->
    binding.tvTime.setText(String.format(Locale.getDefault(),
        "%02d:%02d", picker.getHour(), picker.getMinute())));
picker.show(getSupportFragmentManager(), "TIME_PICKER");
```

| Input Mode | Usage |
|-----------|-------|
| `INPUT_MODE_CLOCK` | Clock face — default |
| `INPUT_MODE_KEYBOARD` | Direct number entry |

---

## 7. Chips Selection (Single-Select Filter + Multi-Select Tags)

**Visual Use Case:** Category filter bar, tag selector, interest picker.

```xml
<!-- Horizontal scrollable single-select filter chips -->
<HorizontalScrollView android:layout_width="match_parent"
    android:layout_height="wrap_content" android:scrollbars="none"
    android:paddingStart="16dp" android:paddingEnd="16dp">

    <com.google.android.material.chip.ChipGroup
        android:id="@+id/chipGroupFilter"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        app:singleSelection="true" app:selectionRequired="true"
        app:chipSpacingHorizontal="8dp">

        <com.google.android.material.chip.Chip
            android:id="@+id/chipAll"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="All" android:checkable="true" android:checked="true"
            style="@style/Widget.Material3.Chip.Filter" />

        <com.google.android.material.chip.Chip
            android:id="@+id/chipImages"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Images" android:checkable="true"
            style="@style/Widget.Material3.Chip.Filter" />

        <com.google.android.material.chip.Chip
            android:id="@+id/chipVideos"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Videos" android:checkable="true"
            style="@style/Widget.Material3.Chip.Filter" />
    </com.google.android.material.chip.ChipGroup>
</HorizontalScrollView>
```

```kotlin
// Single-select: get selected filter
binding.chipGroupFilter.setOnCheckedStateChangeListener { group, checkedIds ->
    val chip = group.findViewById<Chip>(checkedIds.firstOrNull() ?: return@setOnCheckedStateChangeListener)
    viewModel.setFilter(chip.tag as? String ?: chip.text.toString())
}

// Multi-select: dynamic tag chips
val tags = listOf("Kotlin", "Java", "XML", "Architecture", "Compose")
tags.forEach { tag ->
    val chip = Chip(requireContext()).apply {
        text = tag
        isCheckable = true
        tag = tag
    }
    binding.chipGroupTags.addView(chip)
}

binding.chipGroupTags.setOnCheckedStateChangeListener { group, checkedIds ->
    val selected = checkedIds.map { group.findViewById<Chip>(it).tag as String }
    viewModel.setTags(selected)
}
```

```xml
<!-- res/color/chip_state_color.xml — checked/unchecked background -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="?attr/colorPrimaryContainer" android:state_checked="true" />
    <item android:color="?attr/colorSurface" />
</selector>
```

```java
// Java — dynamic chip
Chip chip = new Chip(requireContext());
chip.setText(tag);
chip.setCheckable(true);
chip.setTag(tag);
binding.chipGroupTags.addView(chip);

binding.chipGroupTags.setOnCheckedStateChangeListener((group, checkedIds) -> {
    List<String> selected = new ArrayList<>();
    for (int id : checkedIds) {
        Chip c = group.findViewById(id);
        selected.add((String) c.getTag());
    }
    viewModel.setTags(selected);
});
```

---

# Module 10 — Media Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. ShapeableImageView (Circle Avatar)

```xml
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/ivAvatar"
    android:layout_width="48dp"
    android:layout_height="48dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearance.Circle"
    app:strokeWidth="2dp"
    app:strokeColor="?attr/colorPrimary" />
```

```kotlin
Glide.with(this)
    .load(user.avatarUrl)
    .placeholder(R.drawable.ic_person_circle)
    .error(R.drawable.ic_person_circle)
    .circleCrop()
    .into(binding.ivAvatar)
```

## 2. Story Ring View

```kotlin
// Story ring with gradient border
class StoryAvatarView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : FrameLayout(context, attrs) {

    private val gradientPaint = Paint(Paint.ANTI_ALIAS_FLAG)
    private val clearPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        xfermode = PorterDuffXfermode(PorterDuff.Mode.CLEAR)
    }
    private val ringWidth = 3f.dpToPx(context)
    private val gap = 2f.dpToPx(context)

    init {
        setWillNotDraw(false)
        setLayerType(LAYER_TYPE_HARDWARE, null)
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
        val cx = width / 2f
        val cy = height / 2f
        val outerRadius = minOf(cx, cy)

        // Draw gradient ring
        val gradient = SweepGradient(cx, cy,
            intArrayOf(Color.parseColor("#F58529"), Color.parseColor("#DD2A7B"),
                       Color.parseColor("#8134AF"), Color.parseColor("#515BD4")),
            null)
        gradientPaint.shader = gradient
        canvas.drawCircle(cx, cy, outerRadius, gradientPaint)

        // Clear inner area (gap)
        canvas.drawCircle(cx, cy, outerRadius - ringWidth - gap, clearPaint)
    }
}
```

## 3. Image Gallery (Grid with Detail Transition)

```kotlin
class GalleryAdapter(
    private val onImageClick: (ImageView, GalleryItem) -> Unit
) : ListAdapter<GalleryItem, GalleryAdapter.VH>(GalleryDiff()) {

    inner class VH(val b: ItemGalleryBinding) : RecyclerView.ViewHolder(b.root)

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        Glide.with(holder.b.ivPhoto)
            .load(item.thumbnailUrl)
            .placeholder(R.drawable.placeholder_image)
            .transition(DrawableTransitionOptions.withCrossFade())
            .into(holder.b.ivPhoto)

        // Set transition name for shared element
        holder.b.ivPhoto.transitionName = "image_${item.id}"
        holder.b.ivPhoto.setOnClickListener {
            onImageClick(holder.b.ivPhoto, item)
        }
    }
}

// In Fragment — start shared element transition
galleryAdapter = GalleryAdapter { imageView, item ->
    val extras = FragmentNavigatorExtras(
        imageView to "detail_image_${item.id}"
    )
    val action = GalleryFragmentDirections.toDetail(item.id)
    findNavController().navigate(action, extras)
}
```

## 4. Image Slider / Banner Carousel

```kotlin
// Auto-scrolling banner carousel
class BannerCarousel(
    private val viewPager: ViewPager2,
    private val indicator: TabLayout,
    private val items: List<BannerItem>,
    private val autoScrollDelayMs: Long = 3000
) {
    private val handler = Handler(Looper.getMainLooper())
    private val scrollRunnable = Runnable { autoScroll() }

    fun setup() {
        val adapter = BannerAdapter(items)
        viewPager.adapter = adapter
        viewPager.setCurrentItem(items.size * 500, false) // start in middle for infinite feel

        TabLayoutMediator(indicator, viewPager) { _, _ -> }.attach()

        viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
            override fun onPageSelected(position: Int) {
                resetAutoScroll()
            }
        })
        startAutoScroll()
    }

    private fun autoScroll() {
        val next = viewPager.currentItem + 1
        viewPager.setCurrentItem(next, true)
    }

    fun startAutoScroll() {
        handler.postDelayed(scrollRunnable, autoScrollDelayMs)
    }

    fun stopAutoScroll() {
        handler.removeCallbacks(scrollRunnable)
    }

    private fun resetAutoScroll() {
        stopAutoScroll()
        startAutoScroll()
    }
}
```

## 5. ExoPlayer Video Player

```gradle
implementation 'androidx.media3:media3-exoplayer:1.3.1'
implementation 'androidx.media3:media3-ui:1.3.1'
```

```xml
<androidx.media3.ui.PlayerView
    android:id="@+id/playerView"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    app:show_buffering="when_playing"
    app:use_controller="true"
    app:hide_on_touch="false" />
```

```kotlin
class VideoPlayerFragment : Fragment() {
    private var player: ExoPlayer? = null

    override fun onStart() {
        super.onStart()
        initPlayer()
    }

    private fun initPlayer() {
        player = ExoPlayer.Builder(requireContext()).build().also { exoPlayer ->
            binding.playerView.player = exoPlayer
            val mediaItem = MediaItem.fromUri(videoUri)
            exoPlayer.setMediaItem(mediaItem)
            exoPlayer.prepare()
            exoPlayer.playWhenReady = true
        }
    }

    override fun onStop() {
        super.onStop()
        player?.release()
        player = null
    }
}
```

---

## 6. Avatar System (Single + Status Dot + Initials Fallback + Group Stack)

**Visual Use Case:** User profiles, chat headers, comment threads, participant lists.

```xml
<!-- res/values/themes.xml — circle shape style -->
<style name="CircleImageView" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">50%</item>
</style>
```

```xml
<!-- Single avatar with online status dot -->
<FrameLayout android:layout_width="48dp" android:layout_height="48dp">

    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/ivAvatar"
        android:layout_width="48dp" android:layout_height="48dp"
        android:scaleType="centerCrop"
        app:shapeAppearanceOverlay="@style/CircleImageView" />

    <View android:id="@+id/statusDot"
        android:layout_width="12dp" android:layout_height="12dp"
        android:layout_gravity="bottom|end"
        android:background="@drawable/status_dot_online" />
</FrameLayout>
```

```xml
<!-- res/drawable/status_dot_online.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <solid android:color="#4CAF50" />
    <stroke android:width="2dp" android:color="?attr/colorSurface" />
</shape>
```

```kotlin
// Load with Glide (add to build.gradle: implementation 'com.github.bumptech.glide:glide:4.16.0')
Glide.with(this).load(user.avatarUrl).circleCrop()
    .placeholder(R.drawable.ic_person_placeholder)
    .transition(DrawableTransitionOptions.withCrossFade())
    .into(binding.ivAvatar)

// Status dot
binding.statusDot.visibility = if (user.isOnline) View.VISIBLE else View.GONE
```

```kotlin
// Initials fallback avatar (no image)
fun createInitialsDrawable(name: String, sizeDp: Int, context: Context): Drawable {
    val size = (sizeDp * context.resources.displayMetrics.density).toInt()
    val bitmap = Bitmap.createBitmap(size, size, Bitmap.Config.ARGB_8888)
    val canvas = Canvas(bitmap)

    val colors = listOf("#E91E63","#9C27B0","#2196F3","#009688","#FF5722")
    val bg = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.parseColor(colors[name.length % colors.size])
    }
    canvas.drawCircle(size / 2f, size / 2f, size / 2f, bg)

    val initials = name.split(" ").take(2)
        .mapNotNull { it.firstOrNull()?.uppercaseChar() }.joinToString("")

    val textPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.WHITE; textSize = size * 0.38f
        textAlign = Paint.Align.CENTER; typeface = Typeface.DEFAULT_BOLD
    }
    val yPos = size / 2f - (textPaint.descent() + textPaint.ascent()) / 2f
    canvas.drawText(initials, size / 2f, yPos, textPaint)

    return BitmapDrawable(context.resources, bitmap)
}

// Usage
binding.ivAvatar.setImageDrawable(
    createInitialsDrawable(user.name, 48, requireContext())
)
```

```kotlin
// Stacked group avatar (overlapping circles with "+N" badge)
class AvatarStackView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : FrameLayout(context, attrs) {

    fun setAvatars(urls: List<String?>, maxVisible: Int = 3) {
        removeAllViews()
        val sizePx   = 36.dpToPx()
        val overlapPx = 20.dpToPx()

        urls.take(maxVisible).forEachIndexed { i, url ->
            val iv = ShapeableImageView(context).apply {
                layoutParams = LayoutParams(sizePx, sizePx).also { it.marginStart = i * (sizePx - overlapPx) }
                shapeAppearanceModel = ShapeAppearanceModel.builder()
                    .setAllCornerSizes(RelativeCornerSize(0.5f)).build()
                strokeWidth = 2.dpToPx().toFloat()
                strokeColor = ColorStateList.valueOf(
                    ContextCompat.getColor(context, R.color.md_theme_surface))
            }
            Glide.with(context).load(url).circleCrop().into(iv)
            addView(iv)
        }

        val overflow = urls.size - maxVisible
        if (overflow > 0) {
            val badge = TextView(context).apply {
                layoutParams = LayoutParams(sizePx, sizePx).also {
                    it.marginStart = maxVisible * (sizePx - overlapPx)
                }
                text = "+$overflow"; textSize = 11f; gravity = Gravity.CENTER
                setTextColor(ContextCompat.getColor(context, R.color.md_theme_onPrimaryContainer))
                background = GradientDrawable().apply {
                    shape = GradientDrawable.OVAL
                    setColor(ContextCompat.getColor(context, R.color.md_theme_primaryContainer))
                }
            }
            addView(badge)
        }
    }

    private fun Int.dpToPx() = (this * resources.displayMetrics.density).toInt()
}
```

---

## 7. Audio Player UI (MediaPlayer + SeekBar)

**Visual Use Case:** Podcast player, voice note playback, music player.

```xml
<!-- res/layout/layout_audio_player.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="vertical" android:padding="16dp"
    android:background="?attr/colorSurfaceVariant">

    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/ivAlbumArt"
        android:layout_width="180dp" android:layout_height="180dp"
        android:layout_gravity="center_horizontal"
        android:scaleType="centerCrop"
        app:shapeAppearanceOverlay="@style/RoundedImageView16" />

    <TextView android:id="@+id/tvTrackTitle"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_marginTop="16dp" android:gravity="center"
        android:textAppearance="?attr/textAppearanceTitleMedium"
        android:ellipsize="marquee" android:singleLine="true" />

    <TextView android:id="@+id/tvArtist"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:gravity="center" android:textAppearance="?attr/textAppearanceBodyMedium"
        android:textColor="?attr/colorOnSurfaceVariant" />

    <SeekBar android:id="@+id/seekBar"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:progressTint="?attr/colorPrimary" android:thumbTint="?attr/colorPrimary" />

    <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
        android:orientation="horizontal">
        <TextView android:id="@+id/tvElapsed"
            android:layout_width="0dp" android:layout_height="wrap_content"
            android:layout_weight="1" android:text="0:00"
            android:textAppearance="?attr/textAppearanceLabelSmall" />
        <TextView android:id="@+id/tvDuration"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="0:00" android:textAppearance="?attr/textAppearanceLabelSmall" />
    </LinearLayout>

    <!-- Controls -->
    <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_marginTop="8dp" android:gravity="center" android:orientation="horizontal">
        <ImageButton android:id="@+id/btnRewind"
            android:layout_width="48dp" android:layout_height="48dp"
            android:src="@drawable/ic_replay_10"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="Rewind 10s" />
        <com.google.android.material.floatingactionbutton.FloatingActionButton
            android:id="@+id/btnPlayPause"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginStart="16dp" android:layout_marginEnd="16dp"
            app:srcCompat="@drawable/ic_play_arrow" app:fabSize="normal"
            android:contentDescription="Play/Pause" />
        <ImageButton android:id="@+id/btnForward"
            android:layout_width="48dp" android:layout_height="48dp"
            android:src="@drawable/ic_forward_10"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="Forward 10s" />
    </LinearLayout>
</LinearLayout>
```

```kotlin
class AudioPlayerManager(private val context: Context) {

    private var mediaPlayer: MediaPlayer? = null
    private val handler = Handler(Looper.getMainLooper())
    var onProgress: ((elapsed: Int, duration: Int) -> Unit)? = null
    var onStateChange: ((isPlaying: Boolean) -> Unit)? = null

    fun prepare(url: String) {
        release()
        mediaPlayer = MediaPlayer().apply {
            setDataSource(url)
            setOnCompletionListener { onStateChange?.invoke(false); stopTick() }
            prepareAsync()
        }
    }

    fun togglePlayPause() {
        val mp = mediaPlayer ?: return
        if (mp.isPlaying) { mp.pause(); stopTick(); onStateChange?.invoke(false) }
        else              { mp.start(); startTick(); onStateChange?.invoke(true)  }
    }

    fun seekTo(ms: Int) = mediaPlayer?.seekTo(ms)
    fun rewind()  = mediaPlayer?.let { seekTo((it.currentPosition - 10_000).coerceAtLeast(0)) }
    fun forward() = mediaPlayer?.let { seekTo((it.currentPosition + 10_000).coerceAtMost(it.duration)) }

    private val tickRunnable = object : Runnable {
        override fun run() {
            mediaPlayer?.let { if (it.isPlaying) {
                onProgress?.invoke(it.currentPosition, it.duration)
                handler.postDelayed(this, 500)
            }}
        }
    }
    private fun startTick() = handler.post(tickRunnable)
    private fun stopTick()  = handler.removeCallbacks(tickRunnable)

    fun release() { stopTick(); mediaPlayer?.release(); mediaPlayer = null }
}

// Wiring in Fragment
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    val player = AudioPlayerManager(requireContext()).apply {
        onStateChange = { isPlaying ->
            binding.btnPlayPause.setImageResource(
                if (isPlaying) R.drawable.ic_pause else R.drawable.ic_play_arrow)
        }
        onProgress = { elapsed, duration ->
            binding.seekBar.max      = duration
            binding.seekBar.progress = elapsed
            binding.tvElapsed.text   = formatMs(elapsed)
            binding.tvDuration.text  = formatMs(duration)
        }
        prepare("https://example.com/audio.mp3")
    }

    binding.btnPlayPause.setOnClickListener { player.togglePlayPause() }
    binding.btnRewind.setOnClickListener    { player.rewind() }
    binding.btnForward.setOnClickListener   { player.forward() }
    binding.seekBar.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
        override fun onProgressChanged(sb: SeekBar, progress: Int, fromUser: Boolean) {
            if (fromUser) player.seekTo(progress)
        }
        override fun onStartTrackingTouch(sb: SeekBar) {}
        override fun onStopTrackingTouch(sb: SeekBar) {}
    })
}

private fun formatMs(ms: Int): String {
    val s = ms / 1000; return "%d:%02d".format(s / 60, s % 60)
}
```

```java
// Java — toggle play/pause
binding.btnPlayPause.setOnClickListener(v -> {
    if (mediaPlayer.isPlaying()) {
        mediaPlayer.pause();
        binding.btnPlayPause.setImageResource(R.drawable.ic_play_arrow);
    } else {
        mediaPlayer.start();
        binding.btnPlayPause.setImageResource(R.drawable.ic_pause);
    }
});
```

---

## 8. Infinite Horizontal Carousel (RecyclerView + LinearSnapHelper)

**Visual Use Case:** Image gallery, featured products, horizontal content browsing.

```kotlin
class InfiniteCarouselAdapter(
    private val realItems: List<Any>,
    private val onBind: (view: View, item: Any, realPos: Int) -> Unit
) : RecyclerView.Adapter<InfiniteCarouselAdapter.VH>() {

    private val multiplier = 500
    val startPosition get() = (itemCount / 2) - ((itemCount / 2) % realItems.size)

    inner class VH(view: View) : RecyclerView.ViewHolder(view)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = VH(
        LayoutInflater.from(parent.context)
            .inflate(R.layout.item_carousel_card, parent, false)
    )

    override fun onBindViewHolder(holder: VH, position: Int) =
        onBind(holder.itemView, realItems[position % realItems.size], position % realItems.size)

    override fun getItemCount() = realItems.size * multiplier
}

// Setup
fun setupInfiniteCarousel() {
    val layoutManager = LinearLayoutManager(requireContext(), LinearLayoutManager.HORIZONTAL, false)
    binding.rvCarousel.layoutManager = layoutManager

    val adapter = InfiniteCarouselAdapter(items) { view, item, realPos ->
        val card = item as CarouselItem
        view.findViewById<ImageView>(R.id.ivCarouselImage).setImageResource(card.imageRes)
        view.findViewById<TextView>(R.id.tvCarouselTitle).text = card.title
    }
    binding.rvCarousel.adapter = adapter

    // Snap to center of each item
    LinearSnapHelper().attachToRecyclerView(binding.rvCarousel)

    // Peek neighbors
    binding.rvCarousel.apply {
        setPadding(64.dpToPx(), 0, 64.dpToPx(), 0)
        clipToPadding = false
    }

    // Start at center so user can scroll both directions infinitely
    binding.rvCarousel.scrollToPosition(adapter.startPosition)
}
```

### Key Points
| Attribute | Purpose |
|-----------|---------|
| `multiplier = 500` | Creates illusion of infinite items |
| `startPosition` | Middle of the inflated list; enables scroll in both directions |
| `LinearSnapHelper` | Snaps each card to center after fling |
| `clipToPadding = false` | Shows peeking edges of neighbor cards |
