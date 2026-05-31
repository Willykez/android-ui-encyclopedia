# Module 24 — Advanced UI Patterns
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Table of Contents
1. [Collapsing Profile Header](#1-collapsing-profile-header)
2. [Parallax Image Detail Screen](#2-parallax-image-detail-screen)
3. [Multi-Step Form / Wizard](#3-multi-step-form--wizard)
4. [Floating Toolbar (Context Actions)](#4-floating-toolbar-context-actions)
5. [Pull-to-Reveal Action Panel](#5-pull-to-reveal-action-panel)
6. [Pinned Header with Tabs](#6-pinned-header-with-tabs)
7. [Side-by-Side Master Detail (Tablet)](#7-side-by-side-master-detail-tablet)
8. [Overlay / Spotlight Onboarding](#8-overlay--spotlight-onboarding)
9. [Notification-style Banners](#9-notification-style-banners)
10. [Drag & Drop Reordering](#10-drag--drop-reordering)

---

## 1. Collapsing Profile Header

**Pattern used by:** Twitter/X, Instagram, LinkedIn, Google+

```xml
<!-- fragment_profile.xml -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="280dp"
        android:fitsSystemWindows="true">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:fitsSystemWindows="true"
            app:layout_scrollFlags="scroll|exitUntilCollapsed|snap"
            app:contentScrim="?attr/colorSurface"
            app:titleEnabled="false">

            <!-- Cover image -->
            <ImageView
                android:id="@+id/ivCover"
                android:layout_width="match_parent"
                android:layout_height="180dp"
                android:scaleType="centerCrop"
                android:fitsSystemWindows="true"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.5" />

            <!-- Profile info row (avatar + name, pinned at bottom of appbar) -->
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_gravity="bottom"
                android:orientation="horizontal"
                android:padding="16dp"
                android:gravity="center_vertical"
                app:layout_collapseMode="pin">

                <com.google.android.material.imageview.ShapeableImageView
                    android:id="@+id/ivAvatar"
                    android:layout_width="64dp"
                    android:layout_height="64dp"
                    android:scaleType="centerCrop"
                    app:shapeAppearanceOverlay="@style/ShapeAppearance.Circle"
                    app:strokeWidth="3dp"
                    app:strokeColor="?attr/colorSurface" />

                <LinearLayout
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:layout_marginStart="12dp"
                    android:orientation="vertical">

                    <TextView
                        android:id="@+id/tvName"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:textAppearance="?attr/textAppearanceTitleLarge"
                        android:textColor="?attr/colorOnSurface" />

                    <TextView
                        android:id="@+id/tvHandle"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:textAppearance="?attr/textAppearanceBodyMedium"
                        android:textColor="?attr/colorOnSurfaceVariant" />
                </LinearLayout>

                <com.google.android.material.button.MaterialButton
                    android:id="@+id/btnFollow"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Follow"
                    style="@style/Widget.Material3.Button" />
            </LinearLayout>

            <!-- Back button, pinned -->
            <com.google.android.material.appbar.MaterialToolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:navigationIcon="@drawable/ic_arrow_back"
                app:layout_collapseMode="pin" />
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <!-- Tab layout + ViewPager for profile sections -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tabLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <androidx.viewpager2.widget.ViewPager2
            android:id="@+id/viewPager"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```kotlin
class ProfileFragment : Fragment() {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Load data
        Glide.with(this).load(user.coverUrl).into(binding.ivCover)
        Glide.with(this).load(user.avatarUrl).circleCrop().into(binding.ivAvatar)
        binding.tvName.text = user.displayName
        binding.tvHandle.text = "@${user.username}"

        // Toolbar navigation
        binding.toolbar.setNavigationOnClickListener {
            findNavController().popBackStack()
        }

        // Tab + ViewPager
        val pagerAdapter = ProfilePagerAdapter(this, user.id)
        binding.viewPager.adapter = pagerAdapter
        TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, pos ->
            tab.text = when (pos) { 0 -> "Posts"; 1 -> "Media"; else -> "Likes" }
        }.attach()

        // Follow button toggle
        binding.btnFollow.setOnClickListener {
            viewModel.toggleFollow(user.id)
        }
        viewModel.isFollowing.observe(viewLifecycleOwner) { following ->
            binding.btnFollow.text = if (following) "Following" else "Follow"
            binding.btnFollow.isSelected = following
        }
    }
}
```

---

## 2. Parallax Image Detail Screen

```kotlin
class DetailFragment : Fragment() {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Parallax on NestedScrollView
        binding.nestedScroll.setOnScrollChangeListener {
            v: NestedScrollView, _, scrollY, _, _ ->
            // Move image at 40% of scroll speed (parallax)
            binding.ivHero.translationY = scrollY * 0.4f
            // Fade in overlay as we scroll
            val fraction = (scrollY / 400f).coerceIn(0f, 1f)
            binding.overlayScrim.alpha = fraction
            // Show/hide title in toolbar
            if (fraction > 0.8f) {
                binding.toolbar.title = item.title
            } else {
                binding.toolbar.title = ""
            }
        }
    }
}
```

---

## 3. Multi-Step Form / Wizard

```kotlin
class OnboardingActivity : AppCompatActivity() {

    private val steps = listOf(
        StepAccountFragment(),
        StepProfileFragment(),
        StepPreferencesFragment(),
        StepCompleteFragment()
    )
    private var currentStep = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        showStep(0)
        updateProgress()
    }

    fun nextStep() {
        if (currentStep < steps.lastIndex) {
            currentStep++
            showStep(currentStep)
            updateProgress()
        } else {
            finishOnboarding()
        }
    }

    fun previousStep() {
        if (currentStep > 0) {
            currentStep--
            showStep(currentStep)
            updateProgress()
        }
    }

    private fun showStep(index: Int) {
        supportFragmentManager.commit {
            setReorderingAllowed(true)
            setCustomAnimations(
                R.anim.slide_in_right, R.anim.slide_out_left,
                R.anim.slide_in_left, R.anim.slide_out_right
            )
            replace(R.id.stepContainer, steps[index])
        }
    }

    private fun updateProgress() {
        val progress = ((currentStep + 1) / steps.size.toFloat() * 100).toInt()
        binding.progressStepper.setProgressCompat(progress, true)
        binding.tvStepLabel.text = "Step ${currentStep + 1} of ${steps.size}"
        binding.btnBack.isVisible = currentStep > 0
        binding.btnNext.text = if (currentStep == steps.lastIndex) "Finish" else "Next"
    }
}
```

---

## 4. Floating Toolbar (Context Actions)

```kotlin
// Contextual Action Mode (multi-select toolbar)
class MultiSelectActivity : AppCompatActivity() {

    private var actionMode: ActionMode? = null

    private val actionModeCallback = object : ActionMode.Callback {
        override fun onCreateActionMode(mode: ActionMode, menu: Menu): Boolean {
            mode.menuInflater.inflate(R.menu.context_action_menu, menu)
            return true
        }

        override fun onPrepareActionMode(mode: ActionMode, menu: Menu): Boolean = false

        override fun onActionItemClicked(mode: ActionMode, item: MenuItem): Boolean {
            return when (item.itemId) {
                R.id.action_delete -> {
                    deleteSelectedItems()
                    mode.finish()
                    true
                }
                R.id.action_share -> {
                    shareSelectedItems()
                    true
                }
                else -> false
            }
        }

        override fun onDestroyActionMode(mode: ActionMode) {
            actionMode = null
            adapter.clearSelection()
        }
    }

    fun startSelectionMode() {
        actionMode = startActionMode(actionModeCallback)
    }

    fun updateSelectionCount(count: Int) {
        actionMode?.title = "$count selected"
    }
}
```

---

## 5. Pull-to-Reveal Action Panel

```kotlin
// Custom behavior: pull down reveals quick actions
class PullRevealBehavior<V : View>(
    context: Context, attrs: AttributeSet?
) : CoordinatorLayout.Behavior<V>(context, attrs) {

    private var initialY = 0f
    private var isDragging = false

    override fun onInterceptTouchEvent(
        parent: CoordinatorLayout, child: V, ev: MotionEvent
    ): Boolean {
        return when (ev.action) {
            MotionEvent.ACTION_DOWN -> {
                initialY = ev.y
                false
            }
            MotionEvent.ACTION_MOVE -> {
                val dy = ev.y - initialY
                dy > 50f // Intercept if pulling down
            }
            else -> false
        }
    }

    override fun onTouchEvent(
        parent: CoordinatorLayout, child: V, ev: MotionEvent
    ): Boolean {
        when (ev.action) {
            MotionEvent.ACTION_MOVE -> {
                val dy = (ev.y - initialY).coerceAtLeast(0f)
                child.translationY = (dy * 0.5f).coerceAtMost(child.height.toFloat())
                child.alpha = child.translationY / child.height
            }
            MotionEvent.ACTION_UP -> {
                if (child.translationY > child.height * 0.5f) {
                    revealPanel(child)
                } else {
                    hidePanel(child)
                }
            }
        }
        return true
    }

    private fun revealPanel(view: V) {
        view.animate().translationY(0f).alpha(1f).setDuration(250).start()
    }

    private fun hidePanel(view: V) {
        view.animate().translationY(-view.height.toFloat()).alpha(0f).setDuration(200).start()
    }
}
```

---

## 6. Pinned Header with Tabs

```xml
<!-- Pattern: AppBarLayout does NOT scroll; tabs pin below toolbar -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:liftOnScroll="true">

        <!-- This toolbar scrolls away -->
        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:title="Explore"
            app:layout_scrollFlags="scroll|enterAlways|snap" />

        <!-- These tabs are PINNED (no scroll flags) -->
        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tabLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:tabMode="scrollable" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

---

## 7. Side-by-Side Master Detail (Tablet)

```xml
<!-- activity_main.xml — adaptive layout -->
<androidx.slidingpanelayout.widget.SlidingPaneLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/slidingPane"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Master pane (list) — 280dp min width -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/masterPane"
        android:name="com.example.ItemListFragment"
        android:layout_width="280dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <!-- Detail pane — takes remaining space -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/detailPane"
        android:name="com.example.ItemDetailFragment"
        android:layout_width="300dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
</androidx.slidingpanelayout.widget.SlidingPaneLayout>
```

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)

        // On phones: SlidingPaneLayout shows one pane at a time
        // On tablets: both panes show side-by-side automatically

        binding.slidingPane.lockMode = SlidingPaneLayout.LOCK_MODE_LOCKED

        // When item selected in list, show detail
        supportFragmentManager.setFragmentResultListener("item_selected", this) { _, bundle ->
            val itemId = bundle.getInt("itemId")
            supportFragmentManager.commit {
                replace(R.id.detailPane, ItemDetailFragment.newInstance(itemId))
            }
            binding.slidingPane.openPane() // On phones, slides to detail
        }
    }
}
```

---

## 8. Overlay / Spotlight Onboarding

```kotlin
// TapTargetView — highlight UI elements for onboarding
// implementation 'com.getkeepsafe.taptargetview:taptargetview:1.13.3'

class OnboardingHelper(private val activity: AppCompatActivity) {

    fun showOnboardingSequence() {
        TapTargetSequence(activity)
            .targets(
                TapTarget.forView(
                    activity.findViewById(R.id.fab),
                    "Create New Item",
                    "Tap this button to add something new"
                )
                    .outerCircleColor(R.color.md_theme_primary)
                    .outerCircleAlpha(0.96f)
                    .targetCircleColor(R.color.white)
                    .titleTextSize(20)
                    .descriptionTextSize(14)
                    .cancelable(false)
                    .tintTarget(true)
                    .transparentTarget(true),

                TapTarget.forToolbarMenuItem(
                    activity.findViewById(R.id.toolbar),
                    R.id.action_search,
                    "Search",
                    "Find anything quickly"
                )
                    .outerCircleColor(R.color.md_theme_secondary)
                    .cancelable(false),

                TapTarget.forView(
                    activity.findViewById(R.id.bottomNav),
                    "Navigation",
                    "Switch between sections here"
                )
                    .outerCircleColor(R.color.md_theme_tertiary)
            )
            .listener(object : TapTargetSequence.Listener {
                override fun onSequenceFinish() {
                    // Mark onboarding complete
                    prefs.edit { putBoolean("onboarding_done", true) }
                }
                override fun onSequenceStep(tapTarget: TapTarget, targetClicked: Boolean) {}
                override fun onSequenceCanceled(tapTarget: TapTarget) {}
            })
            .start()
    }
}
```

---

## 9. Notification-style Banners

```kotlin
// Custom in-app banner (like iOS notification banner)
class InAppBanner private constructor(
    private val activity: Activity,
    private val message: String,
    private val type: BannerType,
    private val action: (() -> Unit)? = null
) {
    enum class BannerType { SUCCESS, ERROR, WARNING, INFO }

    companion object {
        fun show(
            activity: Activity,
            message: String,
            type: BannerType = BannerType.INFO,
            action: (() -> Unit)? = null
        ) = InAppBanner(activity, message, type, action).display()
    }

    private fun display() {
        val banner = LayoutInflater.from(activity)
            .inflate(R.layout.view_in_app_banner, null)

        val tvMessage = banner.findViewById<TextView>(R.id.tvBannerMessage)
        val ivIcon = banner.findViewById<ImageView>(R.id.ivBannerIcon)
        val root = banner.findViewById<MaterialCardView>(R.id.cardBanner)

        tvMessage.text = message
        root.setCardBackgroundColor(ContextCompat.getColor(activity, when (type) {
            BannerType.SUCCESS -> R.color.md_theme_primary
            BannerType.ERROR -> R.color.md_theme_error
            BannerType.WARNING -> android.R.color.holo_orange_light
            BannerType.INFO -> R.color.md_theme_secondary
        }))
        ivIcon.setImageResource(when (type) {
            BannerType.SUCCESS -> R.drawable.ic_check_circle
            BannerType.ERROR -> R.drawable.ic_error
            BannerType.WARNING -> R.drawable.ic_warning
            BannerType.INFO -> R.drawable.ic_info
        })

        val parent = activity.window.decorView as ViewGroup
        parent.addView(banner)

        // Slide in from top
        banner.translationY = -banner.height.toFloat()
        banner.animate()
            .translationY(0f)
            .setDuration(350)
            .setInterpolator(DecelerateInterpolator())
            .start()

        // Auto-dismiss after 3 seconds
        banner.postDelayed({
            banner.animate()
                .translationY(-banner.height.toFloat())
                .setDuration(250)
                .withEndAction { parent.removeView(banner) }
                .start()
        }, 3000)

        action?.let { banner.setOnClickListener { it() } }
    }
}

// Usage
InAppBanner.show(this, "File saved successfully!", BannerType.SUCCESS)
InAppBanner.show(this, "Network error. Retrying...", BannerType.ERROR) {
    viewModel.retry()
}
```

---

## 10. Drag & Drop Reordering

```kotlin
class DragDropCallback(
    private val adapter: DragDropAdapter
) : ItemTouchHelper.SimpleCallback(
    ItemTouchHelper.UP or ItemTouchHelper.DOWN or
    ItemTouchHelper.START or ItemTouchHelper.END,
    0
) {
    override fun onMove(
        recyclerView: RecyclerView,
        viewHolder: RecyclerView.ViewHolder,
        target: RecyclerView.ViewHolder
    ): Boolean {
        adapter.onItemMoved(viewHolder.adapterPosition, target.adapterPosition)
        return true
    }

    override fun onSwiped(viewHolder: RecyclerView.ViewHolder, direction: Int) {}

    // Visual feedback during drag
    override fun onSelectedChanged(viewHolder: RecyclerView.ViewHolder?, actionState: Int) {
        super.onSelectedChanged(viewHolder, actionState)
        if (actionState == ItemTouchHelper.ACTION_STATE_DRAG) {
            viewHolder?.itemView?.apply {
                animate().scaleX(1.05f).scaleY(1.05f).setDuration(150).start()
                elevation = 16f
                alpha = 0.9f
            }
        }
    }

    override fun clearView(recyclerView: RecyclerView, viewHolder: RecyclerView.ViewHolder) {
        super.clearView(recyclerView, viewHolder)
        viewHolder.itemView.apply {
            animate().scaleX(1f).scaleY(1f).setDuration(150).start()
            elevation = 2f
            alpha = 1f
        }
        adapter.onDragComplete()
    }
}

class DragDropAdapter : ListAdapter<Item, DragDropAdapter.VH>(ItemDiff()) {
    private val currentItems = mutableListOf<Item>()

    override fun submitList(list: List<Item>?) {
        super.submitList(list)
        currentItems.clear()
        list?.let { currentItems.addAll(it) }
    }

    fun onItemMoved(from: Int, to: Int) {
        Collections.swap(currentItems, from, to)
        notifyItemMoved(from, to)
    }

    fun onDragComplete() {
        // Persist the new order
        viewModel.saveOrder(currentItems)
    }

    inner class VH(val b: ItemDragBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
        VH(ItemDragBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        holder.b.tvTitle.text = item.title
        // Drag handle activates drag
        holder.b.ivDragHandle.setOnTouchListener { _, event ->
            if (event.action == MotionEvent.ACTION_DOWN) {
                itemTouchHelper.startDrag(holder)
            }
            false
        }
    }

    lateinit var itemTouchHelper: ItemTouchHelper
}
```

---

# Module 25 — Foldable & Large Screen Support
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Window Size Classes

```kotlin
// WindowSizeClass — the recommended adaptive breakpoints
// Compact: phones (< 600dp)
// Medium: large phones / small tablets (600–840dp)
// Expanded: tablets / desktops (> 840dp)

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)

        val windowMetrics = WindowMetricsCalculator.getOrCreate()
            .computeCurrentWindowMetrics(this)
        val widthDp = windowMetrics.bounds.width() / resources.displayMetrics.density
        val heightDp = windowMetrics.bounds.height() / resources.displayMetrics.density

        applyAdaptiveLayout(widthDp, heightDp)
    }

    private fun applyAdaptiveLayout(widthDp: Float, heightDp: Float) {
        when {
            widthDp >= 840f -> {
                // Expanded: show navigation rail + side-by-side panels
                binding.navRail.isVisible = true
                binding.bottomNav.isVisible = false
                binding.detailPane.isVisible = true
                setupTwoPaneLayout()
            }
            widthDp >= 600f -> {
                // Medium: show navigation rail, single pane
                binding.navRail.isVisible = true
                binding.bottomNav.isVisible = false
                binding.detailPane.isVisible = false
            }
            else -> {
                // Compact: bottom navigation, single pane
                binding.navRail.isVisible = false
                binding.bottomNav.isVisible = true
                binding.detailPane.isVisible = false
            }
        }
    }
}
```

## 2. Foldable Hinge Detection

```kotlin
// Detect fold state using Jetpack WindowManager
class FoldableActivity : AppCompatActivity() {

    private lateinit var windowInfoTracker: WindowInfoTracker

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        windowInfoTracker = WindowInfoTracker.getOrCreate(this)
    }

    override fun onStart() {
        super.onStart()
        lifecycleScope.launch(Dispatchers.Main) {
            windowInfoTracker.windowLayoutInfo(this@FoldableActivity)
                .collect { layoutInfo ->
                    val foldingFeature = layoutInfo.displayFeatures
                        .filterIsInstance<FoldingFeature>()
                        .firstOrNull()

                    when {
                        foldingFeature == null -> {
                            // Normal phone / no fold
                            showSinglePaneLayout()
                        }
                        foldingFeature.state == FoldingFeature.State.HALF_OPENED -> {
                            // Tabletop or book mode
                            if (foldingFeature.orientation == FoldingFeature.Orientation.HORIZONTAL) {
                                showTabletopLayout(foldingFeature)
                            } else {
                                showBookLayout(foldingFeature)
                            }
                        }
                        foldingFeature.state == FoldingFeature.State.FLAT -> {
                            // Fully open foldable — treat as tablet
                            showExpandedLayout()
                        }
                    }
                }
        }
    }

    private fun showTabletopLayout(feature: FoldingFeature) {
        // Content above fold, controls below fold
        val hingeBounds = feature.bounds
        binding.contentArea.apply {
            layoutParams.height = hingeBounds.top
            requestLayout()
        }
        binding.controlsArea.apply {
            layoutParams.height = binding.root.height - hingeBounds.bottom
            requestLayout()
        }
    }
}
```

## 3. Responsive Grid Columns

```kotlin
// Dynamically calculate grid columns based on screen width
fun calculateGridSpanCount(context: Context, itemWidthDp: Int = 180): Int {
    val displayMetrics = context.resources.displayMetrics
    val screenWidthDp = displayMetrics.widthPixels / displayMetrics.density
    return (screenWidthDp / itemWidthDp).toInt().coerceAtLeast(1)
}

// In Fragment
val spanCount = calculateGridSpanCount(requireContext(), itemWidthDp = 160)
binding.recyclerView.layoutManager = GridLayoutManager(context, spanCount)

// React to configuration changes (rotation, foldable)
override fun onConfigurationChanged(newConfig: Configuration) {
    super.onConfigurationChanged(newConfig)
    val newSpan = calculateGridSpanCount(requireContext())
    (binding.recyclerView.layoutManager as GridLayoutManager).spanCount = newSpan
}
```

---

# Module 26 — Performance Optimization Guide
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. RecyclerView Performance Checklist

```kotlin
binding.recyclerView.apply {
    // 1. Fixed size optimization
    setHasFixedSize(true)

    // 2. Pre-fetch items (default on, tune count)
    val prefetcher = recycledViewPool
    recycledViewPool.setMaxRecycledViews(0, 10)

    // 3. Prefetch for LinearLayoutManager
    (layoutManager as? LinearLayoutManager)?.initialPrefetchItemCount = 4

    // 4. Avoid nested scrolling in parent scroll views
    isNestedScrollingEnabled = false

    // 5. Use DiffUtil via ListAdapter (avoids full notifyDataSetChanged)
    // ✅ Already covered in Module 03

    // 6. Item animator — disable if not needed
    itemAnimator = null // for chat-style lists with frequent updates
    // Or use default with shorter duration
    (itemAnimator as SimpleItemAnimator).supportsChangeAnimations = false

    // 7. Stable IDs
    adapter?.setHasStableIds(true)
}
```

## 2. Image Performance

```kotlin
// Glide — Optimized configuration
@GlideModule
class AppGlideModule : AppGlideModule() {
    override fun applyOptions(context: Context, builder: GlideBuilder) {
        // Memory cache size (default is ~15% of app memory)
        val memoryCacheSizeBytes = 1024 * 1024 * 20 // 20 MB
        builder.setMemoryCache(LruResourceCache(memoryCacheSizeBytes.toLong()))

        // Disk cache
        builder.setDiskCache(InternalCacheDiskCacheFactory(context, 250L * 1024 * 1024))

        // Decode format (use ARGB_8888 for full quality, RGB_565 for memory saving)
        builder.setDefaultRequestOptions(
            RequestOptions().format(DecodeFormat.PREFER_RGB_565)
        )
    }
}

// Load with explicit size (avoids over-decoding large images)
Glide.with(imageView)
    .load(url)
    .override(Target.SIZE_ORIGINAL) // or specific pixel size
    .thumbnail(0.1f) // load 10% size first as preview
    .transition(DrawableTransitionOptions.withCrossFade(200))
    .into(imageView)
```

## 3. View Hierarchy Optimization

```kotlin
// Use Layout Inspector to find overdraw and deep hierarchies

// Measure layout performance
if (BuildConfig.DEBUG) {
    Trace.beginSection("InflateMyLayout")
    val view = inflater.inflate(R.layout.fragment_home, container, false)
    Trace.endSection()
}

// Avoid these hierarchy killers:
// ❌ RelativeLayout inside RelativeLayout (double measurement)
// ❌ LinearLayout with weights nested inside LinearLayout with weights
// ❌ More than 5 levels of nesting
// ✅ Use ConstraintLayout to flatten everything to 1-2 levels
// ✅ Use merge tags for included layouts
// ✅ Use ViewStub for conditionally visible layouts
```

```xml
<!-- ViewStub: Only inflated when made visible -->
<ViewStub
    android:id="@+id/stubEmptyState"
    android:layout="@layout/empty_state"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
// Inflate lazily
binding.stubEmptyState.inflate()
// OR
binding.stubEmptyState.viewStub?.inflate()
```

## 4. Coroutine & Thread Best Practices

```kotlin
// Always use appropriate dispatchers
class MyRepository {
    suspend fun fetchItems(): List<Item> = withContext(Dispatchers.IO) {
        // Network or DB calls — IO dispatcher
        api.getItems()
    }
}

class MyViewModel : ViewModel() {
    fun loadData() {
        viewModelScope.launch {
            // Heavy processing off main thread
            val processed = withContext(Dispatchers.Default) {
                repository.fetchItems().map { it.toDisplayModel() }
            }
            // Back on main thread for UI
            _items.value = processed
        }
    }
}

// Avoid: launching coroutines in onBindViewHolder
// ✅ Use: preloaded data, submitList with DiffUtil
```

## 5. Memory Leak Prevention

```kotlin
// ✅ Null binding in Fragment.onDestroyView
override fun onDestroyView() {
    super.onDestroyView()
    _binding = null
}

// ✅ Use viewLifecycleOwner for LiveData in fragments (not 'this')
viewModel.data.observe(viewLifecycleOwner) { ... }

// ✅ Cancel coroutines in onStop/onDestroyView
private var searchJob: Job? = null
fun search(query: String) {
    searchJob?.cancel()
    searchJob = lifecycleScope.launch { ... }
}

// ✅ Remove listeners in onDestroyView
override fun onDestroyView() {
    super.onDestroyView()
    binding.recyclerView.clearOnScrollListeners()
    _binding = null
}

// ✅ Avoid capturing Activity context in long-lived objects
// Use applicationContext where possible
class MyAdapter(private val context: Context) // ❌ Activity context
class MyAdapter(context: Context) {           // ✅ Application context
    private val appContext = context.applicationContext
}
```

---

# Module 27 — Quick-Reference Cheat Sheets
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Cheat Sheet 1 — View Visibility

```kotlin
// Show
view.visibility = View.VISIBLE
view.isVisible = true          // KTX extension

// Hide (takes no space)
view.visibility = View.GONE
view.isGone = true             // KTX extension

// Hide (keeps space)
view.visibility = View.INVISIBLE
view.isInvisible = true        // KTX extension

// Conditional show/hide
view.isVisible = condition     // shows if true, hides if false
```

## Cheat Sheet 2 — Colors

```kotlin
// From resource
ContextCompat.getColor(context, R.color.md_theme_primary)

// From attribute
val color = MaterialColors.getColor(view, com.google.android.material.R.attr.colorPrimary)

// Alpha variant
val colorWithAlpha = ColorUtils.setAlphaComponent(color, 128) // 50% alpha

// Interpolate between two colors
val blended = ArgbEvaluator().evaluate(0.5f, colorA, colorB) as Int

// HSL manipulation
val hsl = FloatArray(3)
ColorUtils.colorToHSL(color, hsl)
hsl[2] = 0.8f // Lightness
val lighter = ColorUtils.HSLToColor(hsl)
```

## Cheat Sheet 3 — Animations Quick Reference

```kotlin
// Fade in
view.alpha = 0f; view.animate().alpha(1f).duration = 300

// Slide up
view.translationY = 100f.dp; view.animate().translationY(0f).duration = 300

// Scale in
view.scaleX = 0f; view.scaleY = 0f
view.animate().scaleX(1f).scaleY(1f).duration = 300

// Rotate
view.animate().rotation(360f).duration = 500

// With interpolator
view.animate().translationX(200f)
    .setDuration(400)
    .setInterpolator(FastOutSlowInInterpolator())
    .start()

// Chain: fade out, change text, fade in
view.animate().alpha(0f).withEndAction {
    (view as? TextView)?.text = "New Text"
    view.animate().alpha(1f).start()
}.start()

// Cancel
view.animate().cancel()
```

## Cheat Sheet 4 — RecyclerView Snippets

```kotlin
// Scroll to top
binding.rv.smoothScrollToPosition(0)
binding.rv.scrollToPosition(0) // instant

// Check if at bottom
val isAtBottom = !binding.rv.canScrollVertically(1)

// Disable item change animation
(binding.rv.itemAnimator as? SimpleItemAnimator)?.supportsChangeAnimations = false

// Get visible range
val lm = binding.rv.layoutManager as LinearLayoutManager
val first = lm.findFirstVisibleItemPosition()
val last = lm.findLastVisibleItemPosition()

// Span all columns in GridLayoutManager (for headers)
val glm = GridLayoutManager(context, 3)
glm.spanSizeLookup = object : GridLayoutManager.SpanSizeLookup() {
    override fun getSpanSize(position: Int) =
        if (adapter.getItemViewType(position) == TYPE_HEADER) 3 else 1
}
```

## Cheat Sheet 5 — Fragment Navigation

```kotlin
// Navigate forward
findNavController().navigate(R.id.action_to_detail)

// Navigate with args
findNavController().navigate(
    HomeFragmentDirections.actionHomeToDetail(itemId = 42)
)

// Navigate with shared element
val extras = FragmentNavigatorExtras(binding.ivThumb to "detail_image")
findNavController().navigate(R.id.action_to_detail, null, null, extras)

// Pop back
findNavController().popBackStack()
findNavController().navigateUp()

// Pop to specific destination
findNavController().popBackStack(R.id.homeFragment, false)

// Check if can go back
findNavController().previousBackStackEntry != null
```

## Cheat Sheet 6 — Material Colors Quick Access

```kotlin
// Get color from Material attribute
fun View.colorAttr(@AttrRes attrRes: Int): Int {
    return MaterialColors.getColor(this, attrRes)
}

// Common tokens
val primary = view.colorAttr(com.google.android.material.R.attr.colorPrimary)
val onPrimary = view.colorAttr(com.google.android.material.R.attr.colorOnPrimary)
val primaryContainer = view.colorAttr(com.google.android.material.R.attr.colorPrimaryContainer)
val surface = view.colorAttr(com.google.android.material.R.attr.colorSurface)
val onSurface = view.colorAttr(com.google.android.material.R.attr.colorOnSurface)
val error = view.colorAttr(com.google.android.material.R.attr.colorError)
val outline = view.colorAttr(com.google.android.material.R.attr.colorOutline)
```

## Cheat Sheet 7 — Coroutines + Flow Patterns

```kotlin
// Collect in Fragment safely
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.flow.collect { value -> /* update UI */ }
    }
}

// Combine two flows
combine(flowA, flowB) { a, b -> Pair(a, b) }
    .collect { (a, b) -> /* use both */ }

// Debounce search
searchFlow.debounce(300).distinctUntilChanged().collect { query -> search(query) }

// StateFlow with initial value
private val _state = MutableStateFlow(UiState.Loading)
val state: StateFlow<UiState> = _state.asStateFlow()

// SharedFlow for one-time events
private val _events = MutableSharedFlow<UiEvent>()
val events = _events.asSharedFlow()
// Emit
viewModelScope.launch { _events.emit(UiEvent.ShowSnackbar("Done")) }
```

## Cheat Sheet 8 — Common Animations Resource Files

```xml
<!-- res/anim/slide_in_right.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate android:fromXDelta="100%" android:toXDelta="0%"
        android:duration="300" android:interpolator="@android:interpolator/fast_out_slow_in" />
    <alpha android:fromAlpha="0.0" android:toAlpha="1.0" android:duration="300" />
</set>

<!-- res/anim/slide_out_left.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate android:fromXDelta="0%" android:toXDelta="-100%"
        android:duration="300" android:interpolator="@android:interpolator/fast_out_slow_in" />
    <alpha android:fromAlpha="1.0" android:toAlpha="0.0" android:duration="300" />
</set>

<!-- res/anim/slide_in_left.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate android:fromXDelta="-100%" android:toXDelta="0%"
        android:duration="300" android:interpolator="@android:interpolator/fast_out_slow_in" />
</set>

<!-- res/anim/slide_out_right.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate android:fromXDelta="0%" android:toXDelta="100%"
        android:duration="300" android:interpolator="@android:interpolator/fast_out_slow_in" />
</set>

<!-- res/anim/fade_in.xml -->
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromAlpha="0.0" android:toAlpha="1.0"
    android:duration="250" android:interpolator="@android:interpolator/linear" />

<!-- res/anim/fade_out.xml -->
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromAlpha="1.0" android:toAlpha="0.0"
    android:duration="250" />

<!-- res/anim/scale_in.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <scale android:fromXScale="0.8" android:toXScale="1.0"
        android:fromYScale="0.8" android:toYScale="1.0"
        android:pivotX="50%" android:pivotY="50%"
        android:duration="250"
        android:interpolator="@android:interpolator/overshoot" />
    <alpha android:fromAlpha="0.0" android:toAlpha="1.0" android:duration="200" />
</set>

<!-- res/anim/bounce_in.xml -->
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <scale android:fromXScale="0.0" android:toXScale="1.0"
        android:fromYScale="0.0" android:toYScale="1.0"
        android:pivotX="50%" android:pivotY="50%"
        android:duration="400"
        android:interpolator="@android:interpolator/bounce" />
</set>
```

## Cheat Sheet 9 — String Formatting in UI

```kotlin
// Plurals
resources.getQuantityString(R.plurals.items_count, count, count)
// strings.xml: <plurals name="items_count"><item quantity="one">%d item</item><item quantity="other">%d items</item></plurals>

// Spannable (bold part of text)
val fullText = "Hello, World!"
val spannable = SpannableString(fullText)
spannable.setSpan(StyleSpan(Typeface.BOLD), 7, 12, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE)
spannable.setSpan(ForegroundColorSpan(primaryColor), 7, 12, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE)
textView.text = spannable

// HTML text
textView.text = HtmlCompat.fromHtml("<b>Bold</b> and <i>italic</i>", HtmlCompat.FROM_HTML_MODE_LEGACY)

// Currency formatting
val formatted = NumberFormat.getCurrencyInstance(Locale("sw", "TZ")).format(1234.56)
// → "TZS 1,234.56"

// Date formatting
val sdf = SimpleDateFormat("MMM d, yyyy 'at' h:mm a", Locale.getDefault())
val dateStr = sdf.format(Date(timestampMs))

// Relative time
DateUtils.getRelativeTimeSpanString(
    timestampMs, System.currentTimeMillis(), DateUtils.MINUTE_IN_MILLIS
) // → "3 minutes ago"
```

## Cheat Sheet 10 — Common res/values/strings.xml Patterns

```xml
<resources>
    <!-- App metadata -->
    <string name="app_name">MyApp</string>

    <!-- Error messages -->
    <string name="error_generic">Something went wrong. Please try again.</string>
    <string name="error_network">No internet connection.</string>
    <string name="error_timeout">Request timed out.</string>
    <string name="error_empty_field">This field cannot be empty.</string>
    <string name="error_invalid_email">Please enter a valid email address.</string>
    <string name="error_password_short">Password must be at least 8 characters.</string>

    <!-- Loading states -->
    <string name="loading">Loading…</string>
    <string name="refreshing">Refreshing…</string>

    <!-- Empty states -->
    <string name="empty_list">Nothing here yet.</string>
    <string name="empty_search">No results for "%1$s".</string>

    <!-- Actions -->
    <string name="action_retry">Retry</string>
    <string name="action_cancel">Cancel</string>
    <string name="action_confirm">Confirm</string>
    <string name="action_delete">Delete</string>
    <string name="action_save">Save</string>
    <string name="action_edit">Edit</string>
    <string name="action_share">Share</string>
    <string name="action_close">Close</string>
    <string name="action_done">Done</string>
    <string name="action_next">Next</string>
    <string name="action_back">Back</string>
    <string name="action_see_all">See all</string>

    <!-- Accessibility -->
    <string name="cd_back">Go back</string>
    <string name="cd_close">Close</string>
    <string name="cd_loading">Loading content</string>
    <string name="cd_menu">Open menu</string>
    <string name="cd_avatar">User profile picture</string>

    <!-- Plurals -->
    <plurals name="items_count">
        <item quantity="one">%d item</item>
        <item quantity="other">%d items</item>
    </plurals>
    <plurals name="comments_count">
        <item quantity="one">%d comment</item>
        <item quantity="other">%d comments</item>
    </plurals>
</resources>
```

---

## Master Component Decision Tree

```
Need to display a LIST of items?
  ├─ Vertical → RecyclerView + LinearLayoutManager
  ├─ Horizontal → RecyclerView + LinearLayoutManager(HORIZONTAL)
  ├─ Grid → RecyclerView + GridLayoutManager
  ├─ Staggered → RecyclerView + StaggeredGridLayoutManager
  └─ Mixed (sections + items) → ListAdapter with multiple view types

Need NAVIGATION?
  ├─ Phone (3-5 destinations) → BottomNavigationView
  ├─ Tablet / Large screen → NavigationRailView
  ├─ Many destinations → NavigationView in DrawerLayout
  └─ Within a screen → TabLayout + ViewPager2

Need to COLLECT INPUT?
  ├─ Text → TextInputLayout + TextInputEditText
  ├─ Password → TextInputLayout (endIconMode=password_toggle)
  ├─ Dropdown → TextInputLayout + AutoCompleteTextView
  ├─ OTP / PIN → Multiple TextInputEditTexts
  ├─ Boolean → MaterialSwitch or MaterialCheckBox
  ├─ One of N → RadioGroup + MaterialRadioButton
  ├─ Multiple of N → ChipGroup (checkable)
  ├─ Date → MaterialDatePicker
  └─ Time → MaterialTimePicker

Need a LOADING STATE?
  ├─ First load (skeleton) → ShimmerFrameLayout + skeleton layout
  ├─ In-progress operation → CircularProgressIndicator
  ├─ File / step progress → LinearProgressIndicator
  └─ Pull to refresh → SwipeRefreshLayout

Need to SHOW FEEDBACK?
  ├─ Brief message → Snackbar
  ├─ Confirmation needed → MaterialAlertDialog
  ├─ Non-blocking info → InAppBanner or Tooltip
  └─ Persistent status → Status badge / chip

Need a CONTAINER?
  ├─ Simple card → MaterialCardView
  ├─ Scrolling screen → NestedScrollView or RecyclerView
  ├─ Complex positioning → ConstraintLayout
  ├─ Animated transitions → MotionLayout
  └─ Scroll-aware toolbar → CoordinatorLayout + AppBarLayout

Need VISUAL EFFECTS?
  ├─ Blur (API 31+) → RenderEffect.createBlurEffect
  ├─ Gradient → GradientDrawable
  ├─ Shimmer loading → ShimmerFrameLayout
  ├─ Lottie animation → LottieAnimationView
  └─ Spring animation → SpringAnimation
```
