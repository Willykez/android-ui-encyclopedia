# Module 02 — Material Design 3 Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Table of Contents
1. [M3 Theme Setup & Dynamic Color](#1-m3-theme-setup--dynamic-color)
2. [M3 Buttons](#2-m3-buttons)
3. [M3 Cards](#3-m3-cards)
4. [SearchBar & SearchView](#4-searchbar--searchview)
5. [MaterialSwitch](#5-materialswitch)
6. [Segmented Buttons](#6-segmented-buttons)
7. [Navigation Rail](#7-navigation-rail)
8. [Top App Bars (M3)](#8-top-app-bars-m3)
9. [Bottom Sheets (M3)](#9-bottom-sheets-m3)
10. [Side Sheets](#10-side-sheets)
11. [M3 Text Fields](#11-m3-text-fields)
12. [M3 Dialogs](#12-m3-dialogs)
13. [M3 Menus](#13-m3-menus)
14. [M3 Navigation Drawer](#14-m3-navigation-drawer)
15. [ShapeableImageView](#15-shapeableimageview)
16. [Material Dividers (M3)](#16-material-dividers-m3)
17. [M3 Progress Indicators](#17-m3-progress-indicators)
18. [M3 Badges](#18-m3-badges)
19. [M3 Tooltips](#19-m3-tooltips)
20. [M3 Color Roles Reference](#20-m3-color-roles-reference)

---

## 1. M3 Theme Setup & Dynamic Color

**Description:** Material 3 (Material You) uses a full color token system with 30+ roles derived from a seed color. On Android 12+ (API 31), colors can be extracted dynamically from the user's wallpaper.

**Material Version:** 🟢 MD3

### themes.xml — Full M3 Setup
```xml
<!-- res/values/themes.xml -->
<style name="AppTheme" parent="Theme.Material3.DayNight.NoActionBar">
    <!-- Color scheme from colors.xml -->
    <item name="colorPrimary">@color/md_theme_primary</item>
    <item name="colorOnPrimary">@color/md_theme_onPrimary</item>
    <item name="colorPrimaryContainer">@color/md_theme_primaryContainer</item>
    <item name="colorOnPrimaryContainer">@color/md_theme_onPrimaryContainer</item>
    <item name="colorSecondary">@color/md_theme_secondary</item>
    <item name="colorOnSecondary">@color/md_theme_onSecondary</item>
    <item name="colorSecondaryContainer">@color/md_theme_secondaryContainer</item>
    <item name="colorOnSecondaryContainer">@color/md_theme_onSecondaryContainer</item>
    <item name="colorTertiary">@color/md_theme_tertiary</item>
    <item name="colorOnTertiary">@color/md_theme_onTertiary</item>
    <item name="colorTertiaryContainer">@color/md_theme_tertiaryContainer</item>
    <item name="colorOnTertiaryContainer">@color/md_theme_onTertiaryContainer</item>
    <item name="colorError">@color/md_theme_error</item>
    <item name="colorOnError">@color/md_theme_onError</item>
    <item name="colorErrorContainer">@color/md_theme_errorContainer</item>
    <item name="colorOnErrorContainer">@color/md_theme_onErrorContainer</item>
    <item name="android:colorBackground">@color/md_theme_background</item>
    <item name="colorOnBackground">@color/md_theme_onBackground</item>
    <item name="colorSurface">@color/md_theme_surface</item>
    <item name="colorOnSurface">@color/md_theme_onSurface</item>
    <item name="colorSurfaceVariant">@color/md_theme_surfaceVariant</item>
    <item name="colorOnSurfaceVariant">@color/md_theme_onSurfaceVariant</item>
    <item name="colorOutline">@color/md_theme_outline</item>
    <item name="colorOutlineVariant">@color/md_theme_outlineVariant</item>
    <item name="colorSurfaceInverse">@color/md_theme_inverseSurface</item>
    <item name="colorOnSurfaceInverse">@color/md_theme_inverseOnSurface</item>
    <item name="colorPrimaryInverse">@color/md_theme_inversePrimary</item>

    <!-- Edge-to-edge -->
    <item name="android:statusBarColor">@android:color/transparent</item>
    <item name="android:navigationBarColor">@android:color/transparent</item>
    <item name="android:windowLayoutInDisplayCutoutMode">shortEdges</item>
</style>

<!-- Night theme override -->
<style name="AppTheme.Night" parent="AppTheme">
    <item name="colorPrimary">@color/md_theme_dark_primary</item>
    <!-- ... dark variants ... -->
</style>
```

### Dynamic Color (Material You) — Kotlin
```kotlin
// Apply dynamic color on API 31+
class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        // Apply dynamic colors from wallpaper (API 31+)
        DynamicColors.applyToActivitiesIfAvailable(this)
    }
}

// Or apply per-activity
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        DynamicColors.applyToActivityIfAvailable(this)
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
    }
}

// Check if dynamic color is available
val isDynamicColorAvailable = DynamicColors.isDynamicColorAvailable()
```

### Dynamic Color — Java
```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        DynamicColors.applyToActivitiesIfAvailable(this);
    }
}
```

### Custom Dynamic Color Overlay
```xml
<!-- res/values/themes.xml — override dynamic color with brand accent -->
<style name="AppTheme.DynamicOverlay">
    <!-- This style is applied as overlay on top of dynamic theme -->
    <item name="colorTertiary">@color/brand_accent</item>
</style>
```
```kotlin
DynamicColors.applyToActivityIfAvailable(
    this,
    DynamicColorsOptions.Builder()
        .setThemeOverlay(R.style.AppTheme_DynamicOverlay)
        .build()
)
```

---

## 2. M3 Buttons

**Description:** Material 3 introduces 5 button variants with updated shape (fully rounded), elevation tokens, and container/content color roles.

### Button Variants Comparison
| Style | Emphasis | Use Case |
|-------|----------|----------|
| Filled | Highest | Primary action |
| Filled Tonal | High | Secondary primary action |
| Elevated | Medium | On surface backgrounds |
| Outlined | Medium | Alternative/secondary |
| Text | Lowest | Tertiary / inline |

### XML Example
```xml
<!-- Filled Button (M3) -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Filled"
    style="@style/Widget.Material3.Button" />

<!-- Filled Tonal Button -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Tonal"
    style="@style/Widget.Material3.Button.TonalButton" />

<!-- Elevated Button -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Elevated"
    style="@style/Widget.Material3.Button.ElevatedButton" />

<!-- Outlined Button (M3) -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Outlined"
    style="@style/Widget.Material3.Button.OutlinedButton" />

<!-- Text Button (M3) -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Text"
    style="@style/Widget.Material3.Button.TextButton" />

<!-- Icon Button (standalone, no text) -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_favorite"
    style="@style/Widget.Material3.Button.IconButton" />

<!-- Filled Icon Button -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_add"
    style="@style/Widget.Material3.Button.IconButton.Filled" />

<!-- Tonal Icon Button -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_add"
    style="@style/Widget.Material3.Button.IconButton.FilledTonal" />

<!-- Outlined Icon Button -->
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_add"
    style="@style/Widget.Material3.Button.IconButton.Outlined" />
```

---

## 3. M3 Cards

**Description:** Material 3 cards use updated surface tones and elevation overlays instead of drop shadows.

### XML Example
```xml
<!-- Elevated Card (M3) -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.CardView.Elevated"
    app:cardCornerRadius="12dp">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>

<!-- Filled Card (M3) — uses surfaceVariant -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.CardView.Filled"
    app:cardCornerRadius="12dp">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>

<!-- Outlined Card (M3) -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.CardView.Outlined"
    app:cardCornerRadius="12dp">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>
```

### M3 Card Styles
| Style | Background | Border | Shadow |
|-------|------------|--------|--------|
| `CardView.Elevated` | surfaceContainerLow | None | Yes (tonal) |
| `CardView.Filled` | surfaceContainerHighest | None | None |
| `CardView.Outlined` | surface | outlineVariant | None |

---

## 4. SearchBar & SearchView

**Description:** M3 introduces `SearchBar` (persistent) and `SearchView` (expanded search experience) as a replacement for toolbar-based search.

**Visual Use Case:** Main search entry point, full-screen search overlay.

### XML Example
```xml
<!-- SearchBar — sits in layout like a toolbar -->
<com.google.android.material.search.SearchBar
    android:id="@+id/searchBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Search anything..."
    app:navigationIcon="@drawable/ic_menu"
    app:menu="@menu/search_bar_menu"
    app:defaultModeText="Search"
    style="@style/Widget.Material3.SearchBar" />

<!-- SearchView — expanded overlay, connects to SearchBar -->
<com.google.android.material.search.SearchView
    android:id="@+id/searchView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:hint="Search..."
    app:setupWithSearchBar="@id/searchBar">

    <!-- Search results content goes here -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/searchResultsRv"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</com.google.android.material.search.SearchView>
```

### Kotlin Example
```kotlin
// SearchBar click opens SearchView automatically via app:setupWithSearchBar
// But you can also control manually:

binding.searchView.addTransitionListener { _, _, newState ->
    when (newState) {
        SearchView.TransitionState.SHOWN -> {
            // Search view is open
        }
        SearchView.TransitionState.HIDDEN -> {
            // Search view is closed
        }
        else -> {}
    }
}

// Listen for query changes
binding.searchView.editText.addTextChangedListener { editable ->
    val query = editable.toString()
    viewModel.search(query)
}

// Listen for search submit
binding.searchView.editText.setOnEditorActionListener { _, actionId, _ ->
    if (actionId == EditorInfo.IME_ACTION_SEARCH) {
        val query = binding.searchView.text.toString()
        viewModel.submitSearch(query)
        binding.searchView.hide()
        binding.searchBar.setText(query)
    }
    false
}

// Show/hide programmatically
binding.searchView.show()
binding.searchView.hide()
```

### Java Example
```java
binding.searchView.getEditText().addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
    @Override public void onTextChanged(CharSequence s, int start, int before, int count) {
        viewModel.search(s.toString());
    }
    @Override public void afterTextChanged(Editable s) {}
});
```

---

## 5. MaterialSwitch

**Description:** M3 replacement for `SwitchMaterial` with updated thumb/track design and better haptics.

### XML Example
```xml
<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/switchNotifications"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Enable Notifications"
    android:checked="true"
    style="@style/Widget.Material3.CompoundButton.MaterialSwitch" />

<!-- Switch with custom colors -->
<com.google.android.material.materialswitch.MaterialSwitch
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Dark Mode"
    app:thumbTint="@color/switch_thumb_selector"
    app:trackTint="@color/switch_track_selector" />
```

```xml
<!-- res/color/switch_thumb_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="?attr/colorOnPrimary" android:state_checked="true" />
    <item android:color="?attr/colorOutline" />
</selector>

<!-- res/color/switch_track_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="?attr/colorPrimary" android:state_checked="true" />
    <item android:color="?attr/colorSurfaceVariant" />
</selector>
```

### Kotlin Example
```kotlin
binding.switchNotifications.setOnCheckedChangeListener { _, isChecked ->
    if (isChecked) enableNotifications() else disableNotifications()
}

// Programmatic
binding.switchNotifications.isChecked = true

// With preference saving
binding.switchDarkMode.apply {
    isChecked = prefs.getBoolean("dark_mode", false)
    setOnCheckedChangeListener { _, checked ->
        prefs.edit { putBoolean("dark_mode", checked) }
        AppCompatDelegate.setDefaultNightMode(
            if (checked) AppCompatDelegate.MODE_NIGHT_YES
            else AppCompatDelegate.MODE_NIGHT_NO
        )
    }
}
```

---

## 6. Segmented Buttons

**Description:** M3's replacement for `MaterialButtonToggleGroup` — a more visually cohesive single-line selector.

### XML Example
```xml
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/segmentedGroup"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    app:selectionRequired="true">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnList"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:icon="@drawable/ic_list"
        style="@style/Widget.Material3.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnGrid"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:icon="@drawable/ic_grid"
        style="@style/Widget.Material3.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnMap"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:icon="@drawable/ic_map"
        style="@style/Widget.Material3.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

### Kotlin Example
```kotlin
binding.segmentedGroup.check(R.id.btnList) // default selection

binding.segmentedGroup.addOnButtonCheckedListener { _, checkedId, isChecked ->
    if (isChecked) {
        when (checkedId) {
            R.id.btnList -> showList()
            R.id.btnGrid -> showGrid()
            R.id.btnMap -> showMap()
        }
    }
}
```

---

## 7. Navigation Rail

**Description:** Vertical navigation for medium-width screens (tablets, foldables). Sits on the left edge.

**Visual Use Case:** Tablet layouts, split-screen, foldable devices.

### XML Example
```xml
<com.google.android.material.navigationrail.NavigationRailView
    android:id="@+id/navRail"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    app:menu="@menu/nav_rail_menu"
    app:headerLayout="@layout/nav_rail_header"
    app:labelVisibilityMode="labeled"
    style="@style/Widget.Material3.NavigationRailView" />
```

```xml
<!-- res/layout/nav_rail_header.xml — FAB inside rail -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center_horizontal"
    android:layout_margin="12dp"
    app:srcCompat="@drawable/ic_compose" />
```

### Kotlin Example
```kotlin
binding.navRail.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_home -> showHome()
        R.id.nav_search -> showSearch()
    }
    true
}

// With Navigation Component
val navController = findNavController(R.id.navHostFragment)
binding.navRail.setupWithNavController(navController)
```

### Adaptive Layout (Phone + Tablet)
```kotlin
// Use WindowSizeClass to decide between BottomNav and NavRail
val windowMetrics = WindowMetricsCalculator.getOrCreate()
    .computeCurrentWindowMetrics(this)
val widthDp = windowMetrics.bounds.width() / resources.displayMetrics.density

if (widthDp >= 600) {
    // Show Navigation Rail
    binding.navRail.isVisible = true
    binding.bottomNav.isVisible = false
} else {
    // Show Bottom Navigation
    binding.navRail.isVisible = false
    binding.bottomNav.isVisible = true
}
```

---

## 8. Top App Bars (M3)

**Description:** M3 introduces 4 top app bar variants with different sizes and scroll behaviors.

### Top App Bar Variants
| Variant | Height | Use Case |
|---------|--------|----------|
| Small | 64dp | Standard screens |
| Center-aligned | 64dp | Simple screens, iOS-like |
| Medium | 112dp | Content-heavy screens |
| Large | 152dp | Landing pages, profiles |

### XML Example
```xml
<!-- Small Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/topAppBar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:title="Inbox"
    app:navigationIcon="@drawable/ic_menu"
    app:menu="@menu/top_app_bar_menu"
    style="@style/Widget.Material3.Toolbar" />

<!-- Center-Aligned Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:title="Title"
    app:titleCentered="true"
    app:navigationIcon="@drawable/ic_arrow_back"
    style="@style/Widget.Material3.Toolbar" />

<!-- Medium Top App Bar (inside AppBarLayout) -->
<com.google.android.material.appbar.AppBarLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.AppBarLayout">

    <com.google.android.material.appbar.MaterialToolbar
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:title="Page Title"
        app:titleTextAppearance="@style/TextAppearance.Material3.HeadlineSmall"
        app:layout_scrollFlags="scroll|enterAlways|snap"
        style="@style/Widget.Material3.Toolbar.Medium" />
</com.google.android.material.appbar.AppBarLayout>

<!-- Large Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:title="Large Title"
    style="@style/Widget.Material3.Toolbar.Large" />
```

### Kotlin Example
```kotlin
setSupportActionBar(binding.topAppBar)

// Handle navigation icon
binding.topAppBar.setNavigationOnClickListener {
    onBackPressedDispatcher.onBackPressed()
}

// Handle menu items
binding.topAppBar.setOnMenuItemClickListener { item ->
    when (item.itemId) {
        R.id.action_settings -> { startSettingsActivity(); true }
        R.id.action_search -> { openSearch(); true }
        else -> false
    }
}
```

---

## 9. Bottom Sheets (M3)

**Description:** Panel that slides up from the bottom. Two types: Modal (overlay) and Standard (embedded).

### Modal Bottom Sheet — Kotlin
```kotlin
class MyBottomSheet : BottomSheetDialogFragment() {

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View = inflater.inflate(R.layout.fragment_bottom_sheet, container, false)

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Expand to full screen
        val behavior = (dialog as BottomSheetDialog).behavior
        behavior.state = BottomSheetBehavior.STATE_EXPANDED
        behavior.skipCollapsed = true

        // Peek height
        behavior.peekHeight = resources.getDimensionPixelSize(R.dimen.bottom_sheet_peek)
    }
}

// Show it
MyBottomSheet().show(supportFragmentManager, "MyBottomSheet")
```

### Standard Bottom Sheet (Embedded)
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Main content -->
    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Standard Bottom Sheet -->
    <LinearLayout
        android:id="@+id/bottomSheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp"
        app:layout_behavior="com.google.android.material.bottomsheet.BottomSheetBehavior"
        app:behavior_peekHeight="96dp"
        app:behavior_hideable="false">

        <!-- Drag handle -->
        <com.google.android.material.bottomsheet.BottomSheetDragHandleView
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <!-- Sheet content -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Sheet Title"
            android:textAppearance="?attr/textAppearanceTitleLarge" />
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Kotlin — BottomSheetBehavior Control
```kotlin
val behavior = BottomSheetBehavior.from(binding.bottomSheet)

// States
behavior.state = BottomSheetBehavior.STATE_EXPANDED
behavior.state = BottomSheetBehavior.STATE_COLLAPSED
behavior.state = BottomSheetBehavior.STATE_HIDDEN
behavior.state = BottomSheetBehavior.STATE_HALF_EXPANDED

// Callbacks
behavior.addBottomSheetCallback(object : BottomSheetBehavior.BottomSheetCallback() {
    override fun onStateChanged(view: View, newState: Int) {
        when (newState) {
            BottomSheetBehavior.STATE_EXPANDED -> { /* fully open */ }
            BottomSheetBehavior.STATE_COLLAPSED -> { /* peeking */ }
            BottomSheetBehavior.STATE_HIDDEN -> { /* hidden */ }
        }
    }

    override fun onSlide(view: View, slideOffset: Float) {
        // slideOffset: 0.0 = collapsed, 1.0 = expanded
        binding.scrim.alpha = slideOffset * 0.4f
    }
})
```

### Java Example
```java
BottomSheetBehavior<LinearLayout> behavior =
    BottomSheetBehavior.from(binding.bottomSheet);
behavior.setState(BottomSheetBehavior.STATE_EXPANDED);
behavior.addBottomSheetCallback(new BottomSheetBehavior.BottomSheetCallback() {
    @Override
    public void onStateChanged(@NonNull View view, int newState) { }

    @Override
    public void onSlide(@NonNull View view, float slideOffset) { }
});
```

---

## 10. Side Sheets

**Description:** M3 addition — panel that slides in from the right edge. Used for detail panes, filters, or secondary content.

**Dependency:** Material 1.8.0+

### XML Example
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Side Sheet -->
    <LinearLayout
        android:id="@+id/sideSheet"
        android:layout_width="320dp"
        android:layout_height="match_parent"
        android:layout_gravity="end"
        android:orientation="vertical"
        android:padding="24dp"
        android:background="?attr/colorSurface"
        app:layout_behavior="com.google.android.material.sidesheet.SideSheetBehavior">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Filters"
            android:textAppearance="?attr/textAppearanceTitleLarge" />
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Kotlin Example
```kotlin
val behavior = SideSheetBehavior.from(binding.sideSheet)
behavior.state = SideSheetBehavior.STATE_EXPANDED
behavior.state = SideSheetBehavior.STATE_HIDDEN

// Modal Side Sheet Dialog
class MySideSheet : SideSheetDialogFragment() {
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View = inflater.inflate(R.layout.sheet_filters, container, false)
}
MySideSheet().show(supportFragmentManager, "SideSheet")
```

---

## 11. M3 Text Fields

**Description:** Material 3 text fields use updated colors and shape tokens. Two styles: Filled and Outlined.

### XML Example
```xml
<!-- M3 Filled Text Field -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Username"
    app:startIconDrawable="@drawable/ic_person"
    style="@style/Widget.Material3.TextInputLayout.FilledBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPersonName" />
</com.google.android.material.textfield.TextInputLayout>

<!-- M3 Outlined Text Field -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Email"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textEmailAddress" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Dense M3 Text Field -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Search"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</com.google.android.material.textfield.TextInputLayout>
```

### Custom Box Shape
```xml
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Rounded Field"
    app:boxCornerRadiusTopStart="28dp"
    app:boxCornerRadiusTopEnd="28dp"
    app:boxCornerRadiusBottomStart="28dp"
    app:boxCornerRadiusBottomEnd="28dp"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</com.google.android.material.textfield.TextInputLayout>
```

---

## 12. M3 Dialogs

**Description:** M3 dialogs have updated corner radius (28dp), surface tones, and dividers only when needed.

### Kotlin Example
```kotlin
// Basic M3 Dialog
MaterialAlertDialogBuilder(context, R.style.ThemeOverlay_App_MaterialAlertDialog)
    .setTitle("Confirm Action")
    .setMessage("Are you sure you want to proceed?")
    .setIcon(R.drawable.ic_warning)
    .setPositiveButton("Confirm") { _, _ -> proceed() }
    .setNegativeButton("Cancel", null)
    .show()

// Full-screen dialog (for complex forms)
class FullScreenDialog : DialogFragment() {
    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        return MaterialAlertDialogBuilder(requireContext())
            .setView(R.layout.dialog_fullscreen)
            .create()
            .also { dialog ->
                dialog.window?.setLayout(
                    ViewGroup.LayoutParams.MATCH_PARENT,
                    ViewGroup.LayoutParams.MATCH_PARENT
                )
            }
    }
}
```

```xml
<!-- Custom M3 dialog style -->
<style name="ThemeOverlay.App.MaterialAlertDialog"
    parent="ThemeOverlay.Material3.MaterialAlertDialog">
    <item name="shapeAppearanceMediumComponent">@style/ShapeAppearance.App.Dialog</item>
</style>

<style name="ShapeAppearance.App.Dialog" parent="ShapeAppearance.Material3.MediumComponent">
    <item name="cornerSize">28dp</item>
</style>
```

---

## 13. M3 Menus

**Description:** M3 menus (dropdown, context, overflow) with updated shape and elevation.

### XML Example
```xml
<!-- Exposed Dropdown Menu (M3) -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Sort by"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox.ExposedDropdownMenu">

    <AutoCompleteTextView
        android:id="@+id/autoCompleteSortBy"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="none"
        android:text="Newest First" />
</com.google.android.material.textfield.TextInputLayout>
```

### Kotlin Example
```kotlin
// Dropdown items
val sortOptions = listOf("Newest First", "Oldest First", "A–Z", "Z–A")
val adapter = ArrayAdapter(context, R.layout.list_item_dropdown, sortOptions)
binding.autoCompleteSortBy.setAdapter(adapter)
binding.autoCompleteSortBy.setOnItemClickListener { _, _, position, _ ->
    viewModel.setSortOrder(sortOptions[position])
}

// Popup menu (context menu)
val popup = PopupMenu(context, binding.btnMore)
popup.menuInflater.inflate(R.menu.context_menu, popup.menu)
popup.setOnMenuItemClickListener { item ->
    when (item.itemId) {
        R.id.menu_edit -> edit()
        R.id.menu_delete -> confirmDelete()
    }
    true
}
popup.show()
```

---

## 14. M3 Navigation Drawer

**Description:** M3 navigation drawer uses modal (overlay) style by default, with updated indicator pills and surface colors.

### XML Example
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:id="@+id/drawerLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Main content -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/navHostFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- M3 Navigation View -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navView"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:menu="@menu/drawer_menu"
        app:headerLayout="@layout/nav_header_main"
        style="@style/Widget.Material3.NavigationView" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### Kotlin Example
```kotlin
val navController = findNavController(R.id.navHostFragment)
val appBarConfig = AppBarConfiguration(
    setOf(R.id.homeFragment, R.id.searchFragment),
    binding.drawerLayout
)
binding.topAppBar.setupWithNavController(navController, appBarConfig)
binding.navView.setupWithNavController(navController)
```

---

## 15. ShapeableImageView

**Description:** ImageView with Material shape system support — circles, rounded rects, cut corners, and custom shapes.

### XML Example
```xml
<!-- Circle avatar -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/ivAvatar"
    android:layout_width="56dp"
    android:layout_height="56dp"
    android:src="@drawable/avatar_placeholder"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearance.Circle"
    app:strokeWidth="2dp"
    app:strokeColor="?attr/colorPrimary" />

<!-- Rounded rect card image -->
<com.google.android.material.imageview.ShapeableImageView
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearance.RoundedTop" />

<!-- Diamond shape -->
<com.google.android.material.imageview.ShapeableImageView
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearance.Diamond" />
```

```xml
<!-- res/values/shape_appearances.xml -->
<style name="ShapeAppearance.Circle" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">50%</item>
</style>

<style name="ShapeAppearance.RoundedTop" parent="">
    <item name="cornerFamilyTopLeft">rounded</item>
    <item name="cornerFamilyTopRight">rounded</item>
    <item name="cornerSizeTopLeft">12dp</item>
    <item name="cornerSizeTopRight">12dp</item>
    <item name="cornerSizeBottomLeft">0dp</item>
    <item name="cornerSizeBottomRight">0dp</item>
</style>

<style name="ShapeAppearance.Diamond" parent="">
    <item name="cornerFamily">cut</item>
    <item name="cornerSize">50%</item>
</style>

<style name="ShapeAppearance.Hexagon" parent="">
    <item name="cornerFamily">cut</item>
    <item name="cornerSize">30%</item>
</style>
```

### Kotlin Example
```kotlin
// Load with Glide into ShapeableImageView
Glide.with(this)
    .load(userAvatarUrl)
    .placeholder(R.drawable.avatar_placeholder)
    .into(binding.ivAvatar)

// Change shape programmatically
val shapeAppearanceModel = ShapeAppearanceModel.builder()
    .setAllCorners(CornerFamily.ROUNDED, 24f.dpToPx)
    .build()
binding.ivAvatar.shapeAppearanceModel = shapeAppearanceModel

// Story ring effect
binding.ivAvatar.strokeWidth = 3f.dpToPx
binding.ivAvatar.strokeColor = ColorStateList.valueOf(
    ContextCompat.getColor(context, R.color.story_gradient_start)
)
```

---

## 16. Material Dividers (M3)

```xml
<com.google.android.material.divider.MaterialDivider
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:dividerInsetStart="72dp"
    app:dividerInsetEnd="16dp"
    app:dividerColor="?attr/colorOutlineVariant" />
```

---

## 17. M3 Progress Indicators

```xml
<!-- Circular — M3 style -->
<com.google.android.material.progressindicator.CircularProgressIndicator
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:indeterminate="true"
    app:indicatorSize="48dp"
    app:trackThickness="4dp"
    style="@style/Widget.Material3.CircularProgressIndicator" />

<!-- Linear — M3 style -->
<com.google.android.material.progressindicator.LinearProgressIndicator
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:indeterminate="true"
    app:trackCornerRadius="8dp"
    style="@style/Widget.Material3.LinearProgressIndicator" />
```

---

## 18. M3 Badges

```kotlin
// On any view using BadgeDrawable
val badge = BadgeDrawable.createFromResource(context, R.xml.badge)
BadgeUtils.attachBadgeDrawable(badge, binding.ivIcon, binding.frameIcon)

// Customize
badge.apply {
    number = 12
    maxCharacterCount = 3
    backgroundColor = ContextCompat.getColor(context, R.color.md_theme_error)
    badgeTextColor = Color.WHITE
    verticalOffset = 8
    horizontalOffset = 8
}
```

---

## 19. M3 Tooltips

```xml
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnHelp"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_help"
    android:tooltipText="Get help"
    style="@style/Widget.Material3.Button.IconButton" />
```

### Kotlin Example
```kotlin
// Show tooltip programmatically
TooltipCompat.setTooltipText(binding.btnHelp, "Tap for help")
```

---

## 20. M3 Color Roles Reference

| Token | Attribute | Role |
|-------|-----------|------|
| Primary | `?attr/colorPrimary` | Key brand color, main buttons |
| OnPrimary | `?attr/colorOnPrimary` | Text/icons on primary |
| PrimaryContainer | `?attr/colorPrimaryContainer` | Tonal button bg, selected states |
| OnPrimaryContainer | `?attr/colorOnPrimaryContainer` | Text on primary container |
| Secondary | `?attr/colorSecondary` | Less prominent UI elements |
| Tertiary | `?attr/colorTertiary` | Contrasting accent |
| Surface | `?attr/colorSurface` | Card, sheet, dialog backgrounds |
| SurfaceVariant | `?attr/colorSurfaceVariant` | Chip bg, text field bg |
| OnSurface | `?attr/colorOnSurface` | Main text on surfaces |
| OnSurfaceVariant | `?attr/colorOnSurfaceVariant` | Secondary text, icons |
| Outline | `?attr/colorOutline` | Borders, dividers (emphasis) |
| OutlineVariant | `?attr/colorOutlineVariant` | Subtle borders, dividers |
| Error | `?attr/colorError` | Error states, destructive actions |
| InverseSurface | `?attr/colorSurfaceInverse` | Snackbar bg |
| InverseOnSurface | `?attr/colorOnSurfaceInverse` | Snackbar text |
| InversePrimary | `?attr/colorPrimaryInverse` | Snackbar action text |

---

## M2 vs M3 Quick Migration Guide

| M2 Component/Style | M3 Equivalent |
|--------------------|---------------|
| `Theme.MaterialComponents.*` | `Theme.Material3.*` |
| `Widget.MaterialComponents.Button` | `Widget.Material3.Button` |
| `Widget.MaterialComponents.CardView` | `Widget.Material3.CardView.*` |
| `SwitchMaterial` | `MaterialSwitch` |
| `Widget.MaterialComponents.Toolbar` | `Widget.Material3.Toolbar` |
| `Widget.MaterialComponents.TextInputLayout.FilledBox` | `Widget.Material3.TextInputLayout.FilledBox` |
| `BottomNavigationView` (no indicator) | `BottomNavigationView` (auto indicator in M3 theme) |
| `Widget.MaterialComponents.NavigationView` | `Widget.Material3.NavigationView` |
| `colorPrimaryVariant` | `colorPrimaryContainer` |
| `colorSecondaryVariant` | `colorSecondaryContainer` |
| `colorSurface1–5` (tonal) | `colorSurfaceContainerLowest–Highest` |
