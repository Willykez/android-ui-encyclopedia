# Module 04 — Modern List Structures
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Table of Contents
1. [Standard Vertical List](#1-standard-vertical-list)
2. [Grid & Staggered Grid](#2-grid--staggered-grid)
3. [Horizontal List](#3-horizontal-list)
4. [Nested RecyclerViews](#4-nested-recyclerview)
5. [Expandable Lists](#5-expandable-lists)
6. [Sectioned Lists with Headers](#6-sectioned-lists-with-headers)
7. [Sticky Headers](#7-sticky-headers)
8. [Infinite Scrolling / Pagination](#8-infinite-scrolling--pagination)
9. [Chat / Messaging List](#9-chat--messaging-list)
10. [Timeline Layout](#10-timeline-layout)
11. [Settings / Preference List](#11-settings--preference-list)
12. [Swipe Actions on List Items](#12-swipe-actions-on-list-items)
13. [Multi-Selection List](#13-multi-selection-list)
14. [Fast Scrolling](#14-fast-scrolling)

---

## 1. Standard Vertical List

The foundational list pattern using `RecyclerView` with `LinearLayoutManager`.

### Item Layout — XML
```xml
<!-- res/layout/item_standard.xml -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="16dp"
    android:layout_marginBottom="8dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="12dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="16dp"
        android:gravity="center_vertical">

        <!-- Leading icon/avatar -->
        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/ivIcon"
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearance.Circle" />

        <!-- Text section -->
        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="12dp"
            android:orientation="vertical">

            <TextView
                android:id="@+id/tvTitle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleMedium"
                android:textColor="?attr/colorOnSurface"
                android:maxLines="1"
                android:ellipsize="end" />

            <TextView
                android:id="@+id/tvSubtitle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodySmall"
                android:textColor="?attr/colorOnSurfaceVariant"
                android:maxLines="2"
                android:ellipsize="end" />
        </LinearLayout>

        <!-- Trailing meta -->
        <TextView
            android:id="@+id/tvMeta"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:textAppearance="?attr/textAppearanceLabelSmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Adapter — Kotlin
```kotlin
class StandardListAdapter(
    private val onClick: (Item) -> Unit,
    private val onLongClick: (Item) -> Boolean = { false }
) : ListAdapter<Item, StandardListAdapter.VH>(ItemDiff()) {

    inner class VH(val b: ItemStandardBinding) : RecyclerView.ViewHolder(b.root)

    class ItemDiff : DiffUtil.ItemCallback<Item>() {
        override fun areItemsTheSame(a: Item, b: Item) = a.id == b.id
        override fun areContentsTheSame(a: Item, b: Item) = a == b
    }

    override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
        VH(ItemStandardBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        with(holder.b) {
            tvTitle.text = item.title
            tvSubtitle.text = item.subtitle
            tvMeta.text = item.metaText
            Glide.with(ivIcon).load(item.iconUrl)
                .placeholder(R.drawable.ic_placeholder)
                .into(ivIcon)
            root.setOnClickListener { onClick(item) }
            root.setOnLongClickListener { onLongClick(item) }
        }
    }
}
```

---

## 2. Grid & Staggered Grid

### Grid — XML
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/gridRv"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:clipToPadding="false"
    android:padding="8dp" />
```

### Grid — Kotlin Setup
```kotlin
// Fixed 2-column grid
binding.gridRv.layoutManager = GridLayoutManager(context, 2)

// Responsive grid (auto-fit columns by item width)
val spanCount = calculateSpanCount(targetItemWidthDp = 180)
binding.gridRv.layoutManager = GridLayoutManager(context, spanCount)

fun calculateSpanCount(targetItemWidthDp: Int): Int {
    val displayWidth = resources.displayMetrics.widthPixels
    val density = resources.displayMetrics.density
    val targetWidthPx = (targetItemWidthDp * density).toInt()
    return (displayWidth / targetWidthPx).coerceAtLeast(1)
}

// Spacing decoration for grid
class GridSpacingDecoration(
    private val spanCount: Int,
    private val spacing: Int
) : RecyclerView.ItemDecoration() {
    override fun getItemOffsets(
        outRect: Rect, view: View, parent: RecyclerView, state: RecyclerView.State
    ) {
        val position = parent.getChildAdapterPosition(view)
        val column = position % spanCount
        outRect.left = column * spacing / spanCount
        outRect.right = spacing - (column + 1) * spacing / spanCount
        if (position >= spanCount) outRect.top = spacing
    }
}
binding.gridRv.addItemDecoration(GridSpacingDecoration(2, 16))
```

### Grid Item Layout
```xml
<!-- res/layout/item_grid.xml -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="12dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/ivThumbnail"
            android:layout_width="match_parent"
            android:layout_height="160dp"
            android:scaleType="centerCrop" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="12dp">

            <TextView
                android:id="@+id/tvTitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleSmall"
                android:maxLines="2"
                android:ellipsize="end" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Staggered Grid — Kotlin
```kotlin
// Staggered grid (Pinterest-style)
val staggeredManager = StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL)
staggeredManager.gapStrategy = StaggeredGridLayoutManager.GAP_HANDLING_MOVE_ITEMS_BETWEEN_SPANS
binding.staggeredRv.layoutManager = staggeredManager

// For variable-height items, set height in adapter:
override fun onBindViewHolder(holder: VH, position: Int) {
    val item = getItem(position)
    val params = holder.b.ivImage.layoutParams
    params.height = item.imageHeight // or random between min/max for demo
    holder.b.ivImage.layoutParams = params
}
```

---

## 3. Horizontal List

### Kotlin Setup
```kotlin
// Horizontal snap carousel
binding.horizontalRv.apply {
    layoutManager = LinearLayoutManager(context, LinearLayoutManager.HORIZONTAL, false)
    adapter = horizontalAdapter
    // Snap to items
    val snapHelper = LinearSnapHelper()
    snapHelper.attachToRecyclerView(this)
}

// Pager-snap (one item at a time)
val pagerSnap = PagerSnapHelper()
pagerSnap.attachToRecyclerView(binding.horizontalRv)
```

---

## 4. Nested RecyclerView

### Two-Level Nested List Pattern

```kotlin
// Outer adapter for sections
class SectionAdapter : ListAdapter<Section, SectionAdapter.VH>(SectionDiff()) {

    inner class VH(val b: ItemSectionBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, type: Int): VH {
        val b = ItemSectionBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        // Each section has its own inner RecyclerView
        b.innerRv.apply {
            setRecycledViewPool(innerPool) // Share pool for efficiency
            layoutManager = LinearLayoutManager(context, LinearLayoutManager.HORIZONTAL, false)
            isNestedScrollingEnabled = false
        }
        return VH(b)
    }

    override fun onBindViewHolder(holder: VH, position: Int) {
        val section = getItem(position)
        holder.b.tvSectionTitle.text = section.title
        
        // Create inner adapter per section
        val innerAdapter = ItemAdapter { item -> onItemClicked(item) }
        holder.b.innerRv.adapter = innerAdapter
        innerAdapter.submitList(section.items)
    }

    // Shared RecycledViewPool saves memory for nested RVs with same item types
    private val innerPool = RecyclerView.RecycledViewPool().apply {
        setMaxRecycledViews(0, 10)
    }
}
```

---

## 5. Expandable Lists

```kotlin
// Expandable item using ConcatAdapter or state management
data class ExpandableItem(
    val id: Int,
    val title: String,
    val details: String,
    var isExpanded: Boolean = false
)

class ExpandableAdapter : ListAdapter<ExpandableItem, ExpandableAdapter.VH>(Diff()) {

    inner class VH(val b: ItemExpandableBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
        VH(ItemExpandableBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        with(holder.b) {
            tvTitle.text = item.title

            // Expand/collapse details
            tvDetails.visibility = if (item.isExpanded) View.VISIBLE else View.GONE
            tvDetails.text = item.details
            ivArrow.rotation = if (item.isExpanded) 180f else 0f

            root.setOnClickListener {
                val pos = holder.adapterPosition
                val current = getItem(pos)
                val updated = current.copy(isExpanded = !current.isExpanded)
                val newList = currentList.toMutableList()
                newList[pos] = updated
                submitList(newList)

                // Animate arrow
                ivArrow.animate()
                    .rotation(if (updated.isExpanded) 180f else 0f)
                    .setDuration(200)
                    .start()
            }
        }
    }
}
```

---

## 6. Sectioned Lists with Headers

```kotlin
// Using multiple view types
class SectionedAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {

    companion object {
        const val TYPE_HEADER = 0
        const val TYPE_ITEM = 1
    }

    sealed class ListItem {
        data class Header(val title: String, val count: Int) : ListItem()
        data class Content(val item: Item) : ListItem()
    }

    private var items: List<ListItem> = emptyList()

    fun submitGroupedList(groups: Map<String, List<Item>>) {
        val flatList = mutableListOf<ListItem>()
        groups.forEach { (title, items) ->
            flatList.add(ListItem.Header(title, items.size))
            items.forEach { flatList.add(ListItem.Content(it)) }
        }
        items = flatList
        notifyDataSetChanged()
    }

    override fun getItemViewType(position: Int) = when (items[position]) {
        is ListItem.Header -> TYPE_HEADER
        is ListItem.Content -> TYPE_ITEM
    }

    override fun getItemCount() = items.size

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = when (viewType) {
        TYPE_HEADER -> HeaderVH(ItemSectionHeaderBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
        else -> ContentVH(ItemStandardBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        when (val listItem = items[position]) {
            is ListItem.Header -> (holder as HeaderVH).bind(listItem)
            is ListItem.Content -> (holder as ContentVH).bind(listItem.item)
        }
    }

    class HeaderVH(val b: ItemSectionHeaderBinding) : RecyclerView.ViewHolder(b.root) {
        fun bind(header: ListItem.Header) {
            b.tvHeader.text = header.title
            b.tvCount.text = "(${header.count})"
        }
    }
    class ContentVH(val b: ItemStandardBinding) : RecyclerView.ViewHolder(b.root) {
        fun bind(item: Item) {
            b.tvTitle.text = item.title
        }
    }
}
```

---

## 7. Sticky Headers

```gradle
// Use third-party library for sticky headers
implementation 'com.brandongogetap:stickyheaders:0.6.1'
```

```kotlin
// OR implement manually using ItemDecoration
class StickyHeaderDecoration(
    private val adapter: SectionedAdapter
) : RecyclerView.ItemDecoration() {

    override fun onDrawOver(c: Canvas, parent: RecyclerView, state: RecyclerView.State) {
        val topChild = parent.getChildAt(0) ?: return
        val topChildPosition = parent.getChildAdapterPosition(topChild)
        if (topChildPosition == RecyclerView.NO_ID) return

        val headerPos = adapter.getHeaderPositionForItem(topChildPosition)
        val currentHeader = getHeaderViewForItem(headerPos, parent)
        fixLayoutSize(parent, currentHeader)

        val contactPoint = currentHeader.bottom
        val childInContact = getChildInContact(parent, contactPoint, headerPos)

        if (childInContact != null && adapter.isHeader(parent.getChildAdapterPosition(childInContact))) {
            moveHeader(c, currentHeader, childInContact)
            return
        }
        drawHeader(c, currentHeader)
    }

    private fun drawHeader(c: Canvas, header: View) {
        c.save()
        c.translate(0f, 0f)
        header.draw(c)
        c.restore()
    }

    private fun moveHeader(c: Canvas, currentHeader: View, nextHeader: View) {
        c.save()
        c.translate(0f, (nextHeader.top - currentHeader.height).toFloat())
        currentHeader.draw(c)
        c.restore()
    }
    // ... additional helper methods
}
```

---

## 8. Infinite Scrolling / Pagination

### Using Paging 3 (Recommended)
```gradle
implementation 'androidx.paging:paging-runtime-ktx:3.3.0'
```

```kotlin
// PagingSource
class ItemPagingSource(private val api: ApiService) : PagingSource<Int, Item>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Item> {
        val page = params.key ?: 1
        return try {
            val response = api.getItems(page = page, pageSize = params.loadSize)
            LoadResult.Page(
                data = response.items,
                prevKey = if (page == 1) null else page - 1,
                nextKey = if (response.items.isEmpty()) null else page + 1
            )
        } catch (e: Exception) {
            LoadResult.Error(e)
        }
    }

    override fun getRefreshKey(state: PagingState<Int, Item>): Int? {
        return state.anchorPosition?.let { anchorPosition ->
            state.closestPageToPosition(anchorPosition)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchorPosition)?.nextKey?.minus(1)
        }
    }
}

// Repository
class ItemRepository(private val api: ApiService) {
    fun getItemStream(): Flow<PagingData<Item>> = Pager(
        config = PagingConfig(pageSize = 20, enablePlaceholders = false),
        pagingSourceFactory = { ItemPagingSource(api) }
    ).flow
}

// ViewModel
class ItemViewModel(private val repo: ItemRepository) : ViewModel() {
    val items = repo.getItemStream().cachedIn(viewModelScope)
}

// Adapter
class ItemPagingAdapter : PagingDataAdapter<Item, ItemPagingAdapter.VH>(ItemDiff()) {
    inner class VH(val b: ItemStandardBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
        VH(ItemStandardBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position) ?: return
        holder.b.tvTitle.text = item.title
    }
}

// Fragment
lifecycleScope.launch {
    viewModel.items.collectLatest { pagingData ->
        adapter.submitData(pagingData)
    }
}

// Loading state footer
binding.rv.adapter = adapter.withLoadStateFooter(
    footer = LoadStateAdapter { adapter.retry() }
)
```

---

## 9. Chat / Messaging List

```kotlin
class ChatAdapter(private val currentUserId: String) :
    ListAdapter<Message, RecyclerView.ViewHolder>(MessageDiff()) {

    companion object {
        const val VIEW_TYPE_SENT = 1
        const val VIEW_TYPE_RECEIVED = 2
        const val VIEW_TYPE_DATE_HEADER = 3
    }

    override fun getItemViewType(position: Int): Int {
        val msg = getItem(position)
        return when {
            msg.isDateHeader -> VIEW_TYPE_DATE_HEADER
            msg.senderId == currentUserId -> VIEW_TYPE_SENT
            else -> VIEW_TYPE_RECEIVED
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = when (viewType) {
        VIEW_TYPE_SENT -> SentVH(ItemMsgSentBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
        VIEW_TYPE_RECEIVED -> ReceivedVH(ItemMsgReceivedBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
        else -> DateVH(ItemDateHeaderBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        val msg = getItem(position)
        when (holder) {
            is SentVH -> holder.bind(msg)
            is ReceivedVH -> holder.bind(msg)
            is DateVH -> holder.bind(msg)
        }
    }

    class SentVH(val b: ItemMsgSentBinding) : RecyclerView.ViewHolder(b.root) {
        fun bind(msg: Message) {
            b.tvMessage.text = msg.text
            b.tvTimestamp.text = msg.formattedTime
            b.ivStatus.setImageResource(
                if (msg.isRead) R.drawable.ic_read else R.drawable.ic_delivered
            )
        }
    }

    class ReceivedVH(val b: ItemMsgReceivedBinding) : RecyclerView.ViewHolder(b.root) {
        fun bind(msg: Message) {
            b.tvMessage.text = msg.text
            b.tvTimestamp.text = msg.formattedTime
            Glide.with(b.ivAvatar).load(msg.senderAvatar)
                .placeholder(R.drawable.avatar_placeholder).into(b.ivAvatar)
        }
    }
}
```

### Chat Bubble Drawables
```xml
<!-- res/drawable/bubble_sent.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="?attr/colorPrimaryContainer" />
    <corners
        android:topLeftRadius="16dp"
        android:topRightRadius="16dp"
        android:bottomLeftRadius="16dp"
        android:bottomRightRadius="4dp" />
</shape>

<!-- res/drawable/bubble_received.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="?attr/colorSurfaceVariant" />
    <corners
        android:topLeftRadius="16dp"
        android:topRightRadius="16dp"
        android:bottomLeftRadius="4dp"
        android:bottomRightRadius="16dp" />
</shape>
```

---

## 10. Timeline Layout

```xml
<!-- res/layout/item_timeline.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:paddingStart="16dp">

    <!-- Timeline indicator column -->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <!-- Top line -->
        <View
            android:id="@+id/lineTop"
            android:layout_width="2dp"
            android:layout_height="16dp"
            android:background="?attr/colorOutlineVariant" />

        <!-- Dot -->
        <View
            android:id="@+id/dot"
            android:layout_width="12dp"
            android:layout_height="12dp"
            android:background="@drawable/circle_primary" />

        <!-- Bottom line -->
        <View
            android:id="@+id/lineBottom"
            android:layout_width="2dp"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:background="?attr/colorOutlineVariant" />
    </LinearLayout>

    <!-- Content -->
    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginStart="12dp"
        android:orientation="vertical"
        android:paddingBottom="16dp">

        <TextView
            android:id="@+id/tvEventTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleSmall" />

        <TextView
            android:id="@+id/tvEventTime"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>
</LinearLayout>
```

---

## 11. Settings / Preference List

```kotlin
// Use AndroidX Preference Library
class SettingsFragment : PreferenceFragmentCompat() {
    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.preferences, rootKey)

        // Programmatic preference
        findPreference<SwitchPreferenceCompat>("notifications")?.apply {
            setOnPreferenceChangeListener { _, newValue ->
                val enabled = newValue as Boolean
                // handle change
                true
            }
        }

        findPreference<ListPreference>("theme")?.apply {
            setOnPreferenceChangeListener { _, newValue ->
                when (newValue as String) {
                    "light" -> AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO)
                    "dark" -> AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES)
                    else -> AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM)
                }
                true
            }
        }
    }
}
```

```xml
<!-- res/xml/preferences.xml -->
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <PreferenceCategory android:title="Notifications">
        <SwitchPreferenceCompat
            android:key="notifications"
            android:title="Push Notifications"
            android:summary="Receive alerts for new activity"
            android:defaultValue="true" />
    </PreferenceCategory>

    <PreferenceCategory android:title="Appearance">
        <ListPreference
            android:key="theme"
            android:title="Theme"
            android:entries="@array/theme_entries"
            android:entryValues="@array/theme_values"
            android:defaultValue="system" />
    </PreferenceCategory>

    <PreferenceCategory android:title="Account">
        <Preference
            android:key="profile"
            android:title="Edit Profile"
            android:icon="@drawable/ic_person" />
        <Preference
            android:key="logout"
            android:title="Sign Out"
            android:icon="@drawable/ic_logout" />
    </PreferenceCategory>
</PreferenceScreen>
```

---

## 12. Swipe Actions on List Items

```kotlin
// Using ItemTouchHelper with custom swipe drawing
abstract class SwipeToActionCallback(
    context: Context,
    private val leftLabel: String = "Delete",
    private val rightLabel: String = "Archive"
) : ItemTouchHelper.Callback() {

    private val deleteBackground = ColorDrawable(Color.parseColor("#F44336"))
    private val archiveBackground = ColorDrawable(Color.parseColor("#2196F3"))
    private val deleteIcon = ContextCompat.getDrawable(context, R.drawable.ic_delete)!!
    private val archiveIcon = ContextCompat.getDrawable(context, R.drawable.ic_archive)!!

    override fun getMovementFlags(rv: RecyclerView, vh: RecyclerView.ViewHolder) =
        makeMovementFlags(0, ItemTouchHelper.LEFT or ItemTouchHelper.RIGHT)

    override fun onMove(rv: RecyclerView, vh: RecyclerView.ViewHolder,
                        target: RecyclerView.ViewHolder) = false

    override fun onChildDraw(
        c: Canvas, rv: RecyclerView, vh: RecyclerView.ViewHolder,
        dX: Float, dY: Float, actionState: Int, isCurrentlyActive: Boolean
    ) {
        val itemView = vh.itemView
        val iconMargin = (itemView.height - deleteIcon.intrinsicHeight) / 2
        val iconTop = itemView.top + iconMargin
        val iconBottom = iconTop + deleteIcon.intrinsicHeight

        if (dX > 0) { // Right swipe — Archive
            archiveBackground.setBounds(itemView.left, itemView.top,
                itemView.left + dX.toInt(), itemView.bottom)
            archiveBackground.draw(c)
            val iconLeft = itemView.left + iconMargin
            val iconRight = iconLeft + archiveIcon.intrinsicWidth
            archiveIcon.setBounds(iconLeft, iconTop, iconRight, iconBottom)
            archiveIcon.draw(c)
        } else if (dX < 0) { // Left swipe — Delete
            deleteBackground.setBounds(itemView.right + dX.toInt(), itemView.top,
                itemView.right, itemView.bottom)
            deleteBackground.draw(c)
            val iconLeft = itemView.right - iconMargin - deleteIcon.intrinsicWidth
            val iconRight = itemView.right - iconMargin
            deleteIcon.setBounds(iconLeft, iconTop, iconRight, iconBottom)
            deleteIcon.draw(c)
        }
        super.onChildDraw(c, rv, vh, dX, dY, actionState, isCurrentlyActive)
    }
}
```

---

## 13. Multi-Selection List

```kotlin
class MultiSelectAdapter : ListAdapter<SelectableItem, MultiSelectAdapter.VH>(Diff()) {

    val selectedIds = mutableSetOf<Int>()
    var isSelectionMode = false

    inner class VH(val b: ItemSelectableBinding) : RecyclerView.ViewHolder(b.root)

    override fun onBindViewHolder(holder: VH, position: Int) {
        val item = getItem(position)
        val isSelected = selectedIds.contains(item.id)

        with(holder.b) {
            tvTitle.text = item.title
            checkbox.isVisible = isSelectionMode
            checkbox.isChecked = isSelected
            root.isSelected = isSelected
            root.setBackgroundColor(
                if (isSelected) ContextCompat.getColor(root.context, R.color.selection_overlay)
                else Color.TRANSPARENT
            )

            root.setOnClickListener {
                if (isSelectionMode) toggleSelection(item, position)
                else onItemClick(item)
            }
            root.setOnLongClickListener {
                if (!isSelectionMode) {
                    isSelectionMode = true
                    toggleSelection(item, position)
                    onSelectionModeStart()
                }
                true
            }
        }
    }

    private fun toggleSelection(item: SelectableItem, position: Int) {
        if (selectedIds.contains(item.id)) selectedIds.remove(item.id)
        else selectedIds.add(item.id)
        notifyItemChanged(position)
        onSelectionChanged(selectedIds.size)
    }

    fun clearSelection() {
        selectedIds.clear()
        isSelectionMode = false
        notifyDataSetChanged()
    }
}
```

---

## 14. Fast Scrolling

```kotlin
// Enable fast scroller using AndroidX RecyclerView built-in
// (Available through UI customization, not direct API)

// Alternative: Custom fast scroller
binding.recyclerView.apply {
    addOnScrollListener(object : RecyclerView.OnScrollListener() {
        override fun onScrollStateChanged(rv: RecyclerView, newState: Int) {
            if (newState == RecyclerView.SCROLL_STATE_DRAGGING) {
                binding.fastScroller.isVisible = true
            }
        }
    })
}

// Using third-party: RecyclerView-FastScroll
// implementation 'me.zhanghai.android.fastscroll:library:1.3.0'
FastScrollerBuilder(binding.recyclerView)
    .useMd2Style()
    .build()
```
