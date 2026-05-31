# Material Design 3 Components — Android UI Encyclopedia

> **File:** `02_material_design_3.md` | **API Target:** 35 | **MDC-Android:** 1.12.x | **Theme parent:** `Theme.Material3.*`

---

## Table of Contents

1. [Setup & Dependencies](#setup--dependencies)
2. [Material3 Themes & Dynamic Color](#1-material3-themes--dynamic-color)
3. [Material3 Buttons](#2-material3-buttons)
4. [Material3 Cards](#3-material3-cards)
5. [SearchBar & SearchView](#4-searchbar--searchview)
6. [MaterialSwitch](#5-materialswitch)
7. [Segmented Buttons](#6-segmented-buttons)
8. [Navigation Rail](#7-navigation-rail)
9. [Top App Bars (MD3)](#8-top-app-bars-md3)
10. [Bottom Sheets (MD3)](#9-bottom-sheets-md3)
11. [Side Sheets](#10-side-sheets)
12. [Material3 Text Fields](#11-material3-text-fields)
13. [Material3 Dialogs](#12-material3-dialogs)
14. [Material3 Menus](#13-material3-menus)
15. [ShapeableImageView](#14-shapeableimageview)
16. [Material Dividers (MD3)](#15-material-dividers-md3)
17. [Material3 Navigation Components](#16-material3-navigation-components)
18. [Material3 Lists](#17-material3-lists)
19. [Material3 Badges](#18-material3-badges)
20. [Material3 Tooltips](#19-material3-tooltips)
21. [Color Schemes Reference](#color-schemes-reference)

---

## Setup & Dependencies

### `build.gradle (app)`
```groovy
dependencies {
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.core:core-splashscreen:1.0.1'
}
```

### `AndroidManifest.xml`
```xml
<application
    android:theme="@style/Theme.App.Material3">
```

---

## 1. Material3 Themes & Dynamic Color

### Description
Material You (MD3) introduces dynamic color — the system extracts a color palette from the user's wallpaper and applies it to the app automatically (Android 12+). On older devices, a fallback static palette is used.

### `res/values/themes.xml`
```xml
<resources>
    <style name="Theme.App.Material3" parent="Theme.Material3.DayNight.NoActionBar">
        <!-- MD3 color roles -->
        <item name="colorPrimary">@color/md3_primary</item>
        <item name="colorOnPrimary">@color/md3_on_primary</item>
        <item name="colorPrimaryContainer">@color/md3_primary_container</item>
        <item name="colorOnPrimaryContainer">@color/md3_on_primary_container</item>
        <item name="colorSecondary">@color/md3_secondary</item>
        <item name="colorOnSecondary">@color/md3_on_secondary</item>
        <item name="colorSecondaryContainer">@color/md3_secondary_container</item>
        <item name="colorOnSecondaryContainer">@color/md3_on_secondary_container</item>
        <item name="colorTertiary">@color/md3_tertiary</item>
        <item name="colorOnTertiary">@color/md3_on_tertiary</item>
        <item name="colorTertiaryContainer">@color/md3_tertiary_container</item>
        <item name="colorOnTertiaryContainer">@color/md3_on_tertiary_container</item>
        <item name="colorError">@color/md3_error</item>
        <item name="colorOnError">@color/md3_on_error</item>
        <item name="colorErrorContainer">@color/md3_error_container</item>
        <item name="colorOnErrorContainer">@color/md3_on_error_container</item>
        <item name="colorSurface">@color/md3_surface</item>
        <item name="colorOnSurface">@color/md3_on_surface</item>
        <item name="colorSurfaceVariant">@color/md3_surface_variant</item>
        <item name="colorOnSurfaceVariant">@color/md3_on_surface_variant</item>
        <item name="colorOutline">@color/md3_outline</item>
        <item name="colorOutlineVariant">@color/md3_outline_variant</item>
        <item name="colorBackground">@color/md3_background</item>
        <item name="colorOnBackground">@color/md3_on_background</item>
        <item name="android:statusBarColor">?attr/colorSurface</item>
        <item name="android:navigationBarColor">?attr/colorSurface</item>
        <item name="android:windowLightStatusBar">true</item>
    </style>
</resources>
```

### `res/values/colors.xml` (MD3 static seed)
```xml
<resources>
    <!-- Generated from seed color #6750A4 (Material You default) -->
    <color name="md3_primary">#6750A4</color>
    <color name="md3_on_primary">#FFFFFF</color>
    <color name="md3_primary_container">#EADDFF</color>
    <color name="md3_on_primary_container">#21005D</color>
    <color name="md3_secondary">#625B71</color>
    <color name="md3_on_secondary">#FFFFFF</color>
    <color name="md3_secondary_container">#E8DEF8</color>
    <color name="md3_on_secondary_container">#1D192B</color>
    <color name="md3_tertiary">#7D5260</color>
    <color name="md3_on_tertiary">#FFFFFF</color>
    <color name="md3_tertiary_container">#FFD8E4</color>
    <color name="md3_on_tertiary_container">#31111D</color>
    <color name="md3_error">#B3261E</color>
    <color name="md3_on_error">#FFFFFF</color>
    <color name="md3_error_container">#F9DEDC</color>
    <color name="md3_on_error_container">#410E0B</color>
    <color name="md3_surface">#FFFBFE</color>
    <color name="md3_on_surface">#1C1B1F</color>
    <color name="md3_surface_variant">#E7E0EC</color>
    <color name="md3_on_surface_variant">#49454F</color>
    <color name="md3_outline">#79747E</color>
    <color name="md3_outline_variant">#CAC4D0</color>
    <color name="md3_background">#FFFBFE</color>
    <color name="md3_on_background">#1C1B1F</color>
    <color name="md3_surface_tint">#6750A4</color>
</resources>
```

### Dynamic Color — Java
```java
// In Application class or Activity onCreate (before setContentView)
// Requires compileSdk 31+ and MDC 1.7+
import com.google.android.material.color.DynamicColors;

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        DynamicColors.applyToActivitiesIfAvailable(this);
    }
}

// Apply to single activity
DynamicColors.applyToActivityIfAvailable(this);

// Apply with conditions
DynamicColors.applyToActivityIfAvailable(this,
    new DynamicColorsOptions.Builder()
        .setThemeOverlay(R.style.ThemeOverlay_App_DynamicColors)
        .setPrecondition((activity, theme) -> 
            Build.VERSION.SDK_INT >= Build.VERSION_CODES.S)
        .build());
```

### Dynamic Color — Kotlin
```kotlin
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        DynamicColors.applyToActivitiesIfAvailable(this)
    }
}
```

### Color Roles Table (MD3)
| Role | Usage |
|---|---|
| `colorPrimary` | Main brand color, key UI elements |
| `colorOnPrimary` | Text/icons on primary |
| `colorPrimaryContainer` | Container backgrounds |
| `colorOnPrimaryContainer` | Content in primary container |
| `colorSecondary` | Secondary actions, chips |
| `colorTertiary` | Contrasting accent |
| `colorSurface` | Card/sheet backgrounds |
| `colorSurfaceVariant` | Alt surface (chips, text fields) |
| `colorOutline` | Borders, dividers |
| `colorOutlineVariant` | Subtle borders |

---

## 2. Material3 Buttons

### Description
MD3 introduces five button variants with updated typography, shape, and color roles. The filled variant uses `colorPrimary`, tonal uses `colorSecondaryContainer`.

### XML Layout
```xml
<!-- Filled -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_filled"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Filled"
    style="@style/Widget.Material3.Button" />

<!-- Filled Tonal -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_tonal"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Filled Tonal"
    style="@style/Widget.Material3.Button.TonalButton" />

<!-- Outlined -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_outlined"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Outlined"
    style="@style/Widget.Material3.Button.OutlinedButton" />

<!-- Text -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_text"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Text"
    style="@style/Widget.Material3.Button.TextButton" />

<!-- Elevated -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_elevated"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Elevated"
    style="@style/Widget.Material3.Button.ElevatedButton" />

<!-- Icon Button (square) -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_icon_only"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_favorite"
    style="@style/Widget.Material3.Button.IconButton" />

<!-- Tonal Icon Button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_tonal_icon"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:icon="@drawable/ic_bookmark"
    style="@style/Widget.Material3.Button.IconButton.Tonal" />
```

### MD3 vs MD2 Buttons Comparison
| Variant | MD2 Style | MD3 Style |
|---|---|---|
| Filled | `Widget.MaterialComponents.Button` | `Widget.Material3.Button` |
| Outlined | `...Button.OutlinedButton` | `Widget.Material3.Button.OutlinedButton` |
| Text | `...Button.TextButton` | `Widget.Material3.Button.TextButton` |
| Tonal | N/A | `Widget.Material3.Button.TonalButton` |
| Elevated | N/A | `Widget.Material3.Button.ElevatedButton` |

### Java Example
```java
MaterialButton btnFilled = findViewById(R.id.btn_filled);
btnFilled.setOnClickListener(v -> {
    // primary action
});

// Programmatic MD3 button
MaterialButton btn = new MaterialButton(this, null, 
    com.google.android.material.R.attr.materialButtonTonalStyle);
btn.setText("Dynamic Button");
btn.setIcon(ContextCompat.getDrawable(this, R.drawable.ic_add));
```

### Kotlin Example
```kotlin
val btnFilled: MaterialButton = findViewById(R.id.btn_filled)
btnFilled.setOnClickListener { /* primary action */ }
```

---

## 3. Material3 Cards

### Description
MD3 cards come in three variants with updated color tones, tonal surface colors, and shape tokens.

### XML Layout
```xml
<!-- Elevated Card (MD3) -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_elevated"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    style="@style/Widget.Material3.CardView.Elevated">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Elevated Card"
            android:textAppearance="?attr/textAppearanceTitleMedium" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Supporting text below the headline"
            android:textAppearance="?attr/textAppearanceBodyMedium" />

    </LinearLayout>
</com.google.android.material.card.MaterialCardView>

<!-- Filled Card (MD3) -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_filled"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.CardView.Filled">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>

<!-- Outlined Card (MD3) -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_outlined"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.CardView.Outlined">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>
```

### MD3 Card Styles
| Style | Elevation | Background | Border |
|---|---|---|---|
| `Elevated` | 1dp tonal | `colorSurfaceContainerLow` | None |
| `Filled` | 0dp | `colorSurfaceContainerHighest` | None |
| `Outlined` | 0dp | `colorSurface` | `colorOutlineVariant` |

### Java Example
```java
MaterialCardView card = findViewById(R.id.card_elevated);
card.setOnClickListener(v -> {
    // ripple included
});
// Check state
card.setChecked(true);
card.setCheckable(true);
```

---

## 4. SearchBar & SearchView

### Description
MD3 introduces `SearchBar` (persistent) and `SearchView` (expandable overlay). Together they provide a modern search experience.

### XML Layout
```xml
<!-- SearchBar (persistent, top of screen) -->
<com.google.android.material.search.SearchBar
    android:id="@+id/search_bar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Search"
    app:navigationIcon="@drawable/ic_menu"
    app:trailingIcon="@drawable/ic_avatar"
    app:menu="@menu/searchbar_menu"
    android:layout_marginHorizontal="8dp"
    android:layout_marginTop="8dp" />

<!-- SearchView (expandable overlay) -->
<com.google.android.material.search.SearchView
    android:id="@+id/search_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:hint="Search"
    app:searchBar="@id/search_bar">

    <!-- Results list shown inside SearchView -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/search_results_rv"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</com.google.android.material.search.SearchView>
```

### Full Screen Search Layout
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.search.SearchBar
            android:id="@+id/search_bar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Search anything..." />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/main_rv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.search.SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:hint="Search anything..."
        app:searchBar="@id/search_bar">

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/search_rv"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </com.google.android.material.search.SearchView>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Java Example
```java
SearchBar searchBar = findViewById(R.id.search_bar);
SearchView searchView = findViewById(R.id.search_view);

// SearchView opens when SearchBar is clicked (auto-linked via app:searchBar)
searchView.addTransitionListener((sv, previousState, newState) -> {
    if (newState == SearchView.TransitionState.SHOWING) {
        // animate other content out
    } else if (newState == SearchView.TransitionState.HIDDEN) {
        // restore
    }
});

// Query listener
searchView.getEditText().addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
    @Override public void onTextChanged(CharSequence s, int start, int before, int count) {
        filterResults(s.toString());
    }
    @Override public void afterTextChanged(Editable s) {}
});

searchView.getEditText().setOnEditorActionListener((v, actionId, event) -> {
    String query = searchView.getText().toString();
    searchBar.setText(query);
    searchView.hide();
    performSearch(query);
    return false;
});

// Manually show/hide
searchView.show();
searchView.hide();
```

### Kotlin Example
```kotlin
val searchBar: SearchBar = findViewById(R.id.search_bar)
val searchView: SearchView = findViewById(R.id.search_view)

searchView.editText.doAfterTextChanged { text ->
    filterResults(text.toString())
}

searchView.editText.setOnEditorActionListener { _, _, _ ->
    val query = searchView.text.toString()
    searchBar.text = query
    searchView.hide()
    performSearch(query)
    false
}
```

---

## 5. MaterialSwitch

### Description
MD3 replacement for `SwitchMaterial`. Larger thumb, updated track shape, and thumb icon support.

### XML Layout
```xml
<!-- Basic -->
<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/switch_notifications"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Enable notifications"
    android:checked="true" />

<!-- With thumb icon -->
<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/switch_dark_mode"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Dark mode"
    app:thumbIcon="@drawable/selector_switch_icon"
    app:thumbIconSize="16dp" />
```

### `res/drawable/selector_switch_icon.xml`
```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true" android:drawable="@drawable/ic_check" />
    <item android:drawable="@drawable/ic_close" />
</selector>
```

### Java Example
```java
MaterialSwitch switchNoti = findViewById(R.id.switch_notifications);
switchNoti.setOnCheckedChangeListener((btn, isChecked) -> {
    if (isChecked) enableNotifications();
    else disableNotifications();
});

// Programmatic
switchNoti.setChecked(true);
switchNoti.toggle();

boolean state = switchNoti.isChecked();
```

### Kotlin Example
```kotlin
val switchNoti: MaterialSwitch = findViewById(R.id.switch_notifications)
switchNoti.setOnCheckedChangeListener { _, isChecked ->
    if (isChecked) enableNotifications() else disableNotifications()
}
```

---

## 6. Segmented Buttons

### Description
MD3's replacement for toggle button groups. A horizontal row of mutually exclusive or multi-select options with shared border.

### XML Layout
```xml
<!-- Single Selection -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/segmented_single"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    app:selectionRequired="true">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/seg_list"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="List"
        app:icon="@drawable/ic_list"
        style="@style/Widget.Material3.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/seg_grid"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Grid"
        app:icon="@drawable/ic_grid"
        style="@style/Widget.Material3.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/seg_map"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Map"
        app:icon="@drawable/ic_map"
        style="@style/Widget.Material3.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>

<!-- Multi Selection -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/segmented_multi"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="false">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/seg_bold"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:icon="@drawable/ic_format_bold"
        style="@style/Widget.Material3.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/seg_italic"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:icon="@drawable/ic_format_italic"
        style="@style/Widget.Material3.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

### Java Example
```java
MaterialButtonToggleGroup segmented = findViewById(R.id.segmented_single);
segmented.check(R.id.seg_list); // preselect

segmented.addOnButtonCheckedListener((group, checkedId, isChecked) -> {
    if (!isChecked) return;
    if (checkedId == R.id.seg_list) showList();
    else if (checkedId == R.id.seg_grid) showGrid();
    else if (checkedId == R.id.seg_map) showMap();
});

// Get checked
int checkedId = segmented.getCheckedButtonId();

// Multi-select
MaterialButtonToggleGroup multi = findViewById(R.id.segmented_multi);
List<Integer> checked = multi.getCheckedButtonIds();
```

---

## 7. Navigation Rail

### Description
Vertical side navigation for medium-screen layouts (tablets, landscape phones). Typically replaces `BottomNavigationView` on wider screens.

### XML Layout
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <com.google.android.material.navigationrail.NavigationRailView
        android:id="@+id/nav_rail"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        app:menu="@menu/nav_rail_menu"
        app:headerLayout="@layout/nav_rail_header"
        app:labelVisibilityMode="labeled"
        app:itemIconTint="@color/nav_rail_color"
        app:itemTextColor="@color/nav_rail_color" />

    <FrameLayout
        android:id="@+id/rail_content"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
</LinearLayout>
```

### `res/layout/nav_rail_header.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="8dp">

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/rail_fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:srcCompat="@drawable/ic_edit"
        app:fabSize="mini" />
</LinearLayout>
```

### Java Example
```java
NavigationRailView navRail = findViewById(R.id.nav_rail);
navRail.setOnItemSelectedListener(item -> {
    int id = item.getItemId();
    if (id == R.id.rail_home) loadFragment(new HomeFragment());
    else if (id == R.id.rail_explore) loadFragment(new ExploreFragment());
    return true;
});

// Badge
BadgeDrawable badge = navRail.getOrCreateBadge(R.id.rail_inbox);
badge.setNumber(12);
```

### Kotlin Example
```kotlin
val navRail: NavigationRailView = findViewById(R.id.nav_rail)
navRail.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.rail_home -> { loadFragment(HomeFragment()); true }
        R.id.rail_explore -> { loadFragment(ExploreFragment()); true }
        else -> false
    }
}
```

### Responsive Navigation Pattern
```java
// In Activity — switch between BottomNav and NavigationRail based on screen width
int widthDp = (int) (getResources().getDisplayMetrics().widthPixels /
    getResources().getDisplayMetrics().density);

if (widthDp >= 600) {
    // Use NavigationRail
    navRail.setVisibility(View.VISIBLE);
    bottomNav.setVisibility(View.GONE);
} else {
    // Use BottomNavigation
    navRail.setVisibility(View.GONE);
    bottomNav.setVisibility(View.VISIBLE);
}
```

---

## 8. Top App Bars (MD3)

### Description
MD3 defines four top app bar variants based on scroll behavior and size.

### XML Layout
```xml
<!-- Small Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/top_app_bar_small"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    style="@style/Widget.Material3.Toolbar"
    app:title="Page Title"
    app:navigationIcon="@drawable/ic_back"
    app:menu="@menu/top_app_bar_menu" />

<!-- Medium Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/top_app_bar_medium"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.Toolbar.Medium"
    app:title="Page Title"
    app:navigationIcon="@drawable/ic_back" />

<!-- Large Top App Bar -->
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/top_app_bar_large"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/Widget.Material3.Toolbar.Large"
    app:title="Page Title"
    app:navigationIcon="@drawable/ic_back" />
```

### With Scroll Behavior (MD3 lifted)
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fitsSystemWindows="true">

        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            style="@style/Widget.Material3.Toolbar"
            app:layout_scrollFlags="scroll|enterAlways|snap"
            app:title="Home" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Top App Bar Variants
| Style | Height | Title Position | Use Case |
|---|---|---|---|
| `Toolbar` (Small) | 64dp | Center/Start | Default, detail screens |
| `Toolbar.Medium` | 112dp | Bottom | Section pages |
| `Toolbar.Large` | 152dp | Bottom | Home/main screens |
| `Toolbar.Surface` | 64dp | Center | Modal overlays |

---

## 9. Bottom Sheets (MD3)

### Description
MD3 bottom sheets use rounded top corners, surface tonal color, and drag handle. Two types: Modal and Standard.

### Modal Bottom Sheet — Java
```java
public class MyBottomSheet extends BottomSheetDialogFragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_bottom_sheet, container, false);
    }

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        BottomSheetBehavior<View> behavior = BottomSheetBehavior.from(
            (View) view.getParent());
        behavior.setState(BottomSheetBehavior.STATE_EXPANDED);
        behavior.setPeekHeight(400);
    }
}

// Show
MyBottomSheet sheet = new MyBottomSheet();
sheet.show(getSupportFragmentManager(), sheet.getTag());
```

### `res/layout/fragment_bottom_sheet.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:background="?attr/colorSurface"
    android:paddingBottom="24dp">

    <!-- Drag Handle -->
    <com.google.android.material.bottomsheet.BottomSheetDragHandleView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="12dp"
        android:paddingBottom="8dp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sheet Title"
        android:textAppearance="?attr/textAppearanceHeadlineSmall"
        android:paddingHorizontal="24dp"
        android:paddingBottom="16dp" />

    <com.google.android.material.button.MaterialButton
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Primary Action"
        android:layout_marginHorizontal="24dp"
        style="@style/Widget.Material3.Button" />
</LinearLayout>
```

### Sheet Theme (rounded corners)
```xml
<style name="Theme.App.BottomSheetDialog" 
       parent="ThemeOverlay.Material3.BottomSheetDialog">
    <item name="bottomSheetStyle">@style/Widget.App.BottomSheet</item>
</style>

<style name="Widget.App.BottomSheet" 
       parent="Widget.Material3.BottomSheet">
    <item name="shapeAppearance">@style/ShapeAppearance.App.BottomSheet</item>
</style>

<style name="ShapeAppearance.App.BottomSheet" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSizeTopLeft">28dp</item>
    <item name="cornerSizeTopRight">28dp</item>
    <item name="cornerSizeBottomLeft">0dp</item>
    <item name="cornerSizeBottomRight">0dp</item>
</style>
```

---

## 10. Side Sheets

### Description
New in MD3 — a sheet that slides in from the right edge. Used on wider screens for panels, filters, details.

### Java Example
```java
// Standard Side Sheet
SideSheetDialog sideSheet = new SideSheetDialog(this);
View view = getLayoutInflater().inflate(R.layout.layout_side_sheet, null);
sideSheet.setContentView(view);
sideSheet.show();

// Side Sheet Fragment
public class MySideSheet extends SideSheetDialogFragment {
    @Nullable @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
            @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.layout_side_sheet, container, false);
    }
}

// Show
MySideSheet sideSheetFrag = new MySideSheet();
sideSheetFrag.show(getSupportFragmentManager(), "SIDE_SHEET");
```

### Standard (non-modal) Side Sheet
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <LinearLayout
        android:id="@+id/side_sheet"
        android:layout_width="256dp"
        android:layout_height="match_parent"
        android:layout_gravity="end"
        android:background="?attr/colorSurfaceContainerLow"
        app:layout_behavior="com.google.android.material.sidesheet.SideSheetBehavior"
        app:behavior_hideable="true">
        <!-- side sheet content -->
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

---

## 11. Material3 Text Fields

### Description
MD3 text fields use updated color roles, typography, and shape. The filled variant uses `colorSurfaceVariant` background.

### XML Layout
```xml
<!-- Filled TextField (MD3) -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_name"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Full name"
    app:startIconDrawable="@drawable/ic_person"
    app:endIconMode="clear_text"
    style="@style/Widget.Material3.TextInputLayout.FilledBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPersonName" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Outlined TextField (MD3) -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_bio"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Bio"
    app:helperText="Tell us about yourself"
    app:counterEnabled="true"
    app:counterMaxLength="160"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textMultiLine"
        android:minLines="3"
        android:maxLines="6"
        android:gravity="top|start" />
</com.google.android.material.textfield.TextInputLayout>
```

### MD3 vs MD2 TextField Styles
| Variant | MD2 | MD3 |
|---|---|---|
| Filled | `TextInputLayout.FilledBox` | `Widget.Material3.TextInputLayout.FilledBox` |
| Outlined | `TextInputLayout.OutlinedBox` | `Widget.Material3.TextInputLayout.OutlinedBox` |
| Dense | `TextInputLayout.FilledBox.Dense` | `Widget.Material3.TextInputLayout.FilledBox.Dense` |

---

## 12. Material3 Dialogs

### Description
MD3 dialogs use rounded 28dp corners, updated button layout, and dividers for long content.

### Java Example
```java
new MaterialAlertDialogBuilder(this,
        com.google.android.material.R.style.ThemeOverlay_Material3_MaterialAlertDialog)
    .setTitle("Discard draft?")
    .setMessage("Your draft will be permanently deleted.")
    .setNeutralButton("Cancel", (dialog, which) -> dialog.dismiss())
    .setNegativeButton("Discard", (dialog, which) -> { /* delete */ })
    .setPositiveButton("Keep", (dialog, which) -> { /* keep */ })
    .show();

// Full-screen dialog
AlertDialog dialog = new MaterialAlertDialogBuilder(this,
        com.google.android.material.R.style.ThemeOverlay_Material3_MaterialAlertDialog_Centered)
    .setTitle("Centered Title")
    .setIcon(R.drawable.ic_info)
    .setMessage("Centered icon dialog content.")
    .setPositiveButton("OK", null)
    .create();
dialog.show();
```

### Custom Dialog Style (MD3)
```xml
<style name="Dialog.App" 
       parent="ThemeOverlay.Material3.MaterialAlertDialog">
    <item name="shapeAppearanceMediumComponent">
        @style/ShapeAppearance.App.Dialog
    </item>
</style>

<style name="ShapeAppearance.App.Dialog" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">28dp</item>
</style>
```

---

## 13. Material3 Menus

### Description
MD3 menus use surface tonal elevation, rounded corners, and updated item styles.

### XML Layout (Popup Menu)
```xml
<!-- Via menu resource -->
```

### Java Example
```java
// Popup Menu
MaterialButton menuBtn = findViewById(R.id.btn_menu);
menuBtn.setOnClickListener(v -> {
    PopupMenu popup = new PopupMenu(this, v);
    popup.getMenuInflater().inflate(R.menu.context_menu, popup.getMenu());
    popup.setOnMenuItemClickListener(item -> {
        int id = item.getItemId();
        if (id == R.id.menu_edit) { /* edit */ }
        else if (id == R.id.menu_delete) { /* delete */ }
        return true;
    });
    popup.show();
});

// Exposed Dropdown (Menu style)
String[] options = {"Option 1", "Option 2", "Option 3"};
ArrayAdapter<String> adapter = new ArrayAdapter<>(this,
    R.layout.list_item_dropdown, options);
AutoCompleteTextView dropdown = findViewById(R.id.act_menu);
dropdown.setAdapter(adapter);
dropdown.setOnItemClickListener((parent, view, pos, id) -> {
    String selected = (String) parent.getItemAtPosition(pos);
});
```

### `res/layout/list_item_dropdown.xml`
```xml
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:textAppearance="?attr/textAppearanceBodyLarge" />
```

---

## 14. ShapeableImageView

### Description
`ImageView` subclass that supports Material shape appearances — circles, rounded rectangles, cut corners, diamonds.

### XML Layout
```xml
<!-- Circle avatar -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_avatar"
    android:layout_width="56dp"
    android:layout_height="56dp"
    android:src="@drawable/avatar"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
    app:strokeColor="?attr/colorOutline"
    app:strokeWidth="2dp" />

<!-- Rounded rectangle -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_banner"
    android:layout_width="match_parent"
    android:layout_height="180dp"
    android:src="@drawable/banner"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.RoundedRect" />

<!-- Cut corner -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_cut"
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:src="@drawable/photo"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.CutCorner" />

<!-- Diamond -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_diamond"
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:src="@drawable/photo"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Diamond" />
```

### Shape Overlays in `themes.xml`
```xml
<style name="ShapeAppearanceOverlay.Circle" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">50%</item>
</style>

<style name="ShapeAppearanceOverlay.RoundedRect" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">16dp</item>
</style>

<style name="ShapeAppearanceOverlay.CutCorner" parent="">
    <item name="cornerFamily">cut</item>
    <item name="cornerSize">16dp</item>
</style>

<style name="ShapeAppearanceOverlay.Diamond" parent="">
    <item name="cornerFamily">cut</item>
    <item name="cornerSize">50%</item>
</style>
```

### Java Example
```java
ShapeableImageView img = findViewById(R.id.img_avatar);

// Load with Glide
Glide.with(this)
    .load(avatarUrl)
    .placeholder(R.drawable.ic_avatar_placeholder)
    .into(img);

// Programmatic shape
ShapeAppearanceModel model = ShapeAppearanceModel.builder()
    .setAllCorners(CornerFamily.ROUNDED, dpToPx(24))
    .build();
img.setShapeAppearanceModel(model);

// Stroke
img.setStrokeColorResource(R.color.md3_primary);
img.setStrokeWidth(dpToPx(2));
```

### Kotlin Example
```kotlin
val img: ShapeableImageView = findViewById(R.id.img_avatar)
val model = ShapeAppearanceModel.builder()
    .setAllCorners(CornerFamily.ROUNDED, 24f.dpToPx(this))
    .build()
img.shapeAppearanceModel = model
img.strokeColor = ColorStateList.valueOf(
    ContextCompat.getColor(this, R.color.md3_primary))
img.strokeWidth = 2f.dpToPx(this)
```

---

## 15. Material Dividers (MD3)

### XML Layout
```xml
<com.google.android.material.divider.MaterialDivider
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:dividerColor="?attr/colorOutlineVariant"
    app:dividerThickness="1dp"
    app:dividerInsetStart="16dp"
    app:dividerInsetEnd="16dp" />
```

### RecyclerView Divider
```java
RecyclerView recyclerView = findViewById(R.id.recycler);
MaterialDividerItemDecoration divider = new MaterialDividerItemDecoration(
    this, LinearLayoutManager.VERTICAL);
divider.setDividerInsetStart(dpToPx(16));
divider.setDividerInsetEnd(dpToPx(16));
divider.setDividerColor(ContextCompat.getColor(this, R.color.md3_outline_variant));
divider.setLastItemDecorated(false);
recyclerView.addItemDecoration(divider);
```

---

## 16. Material3 Navigation Components

### NavigationDrawer (MD3 Modal)
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <com.google.android.material.navigation.NavigationView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:menu="@menu/nav_drawer_menu"
        style="@style/Widget.Material3.NavigationView" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### Bottom Navigation (MD3)
```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:id="@+id/bottom_nav"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:menu="@menu/bottom_nav_menu"
    style="@style/Widget.Material3.BottomNavigationView" />
```

---

## 17. Material3 Lists

### Description
MD3 list items use `MaterialListItem` (one, two, three line variants) with leading/trailing elements.

### XML Layout
```xml
<!-- One-line list item -->
<com.google.android.material.list.MaterialListItem
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:headlineText="Single line item"
    app:leadingIcon="@drawable/ic_inbox" />

<!-- Two-line list item -->
<com.google.android.material.list.MaterialListItem
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:headlineText="Two line item"
    app:supportingText="Supporting text below headline"
    app:leadingElement="icon"
    app:leadingIcon="@drawable/ic_folder" />
```

> **Note:** For full RecyclerView-based lists, see `04_list_structures.md`.

---

## 18. Material3 Badges

### Description
Small count indicators attached to icons or navigation items.

### Java Example
```java
// On BottomNavigationView
BottomNavigationView nav = findViewById(R.id.bottom_nav);
BadgeDrawable badge = nav.getOrCreateBadge(R.id.nav_messages);
badge.setNumber(99);
badge.setMaxCharacterCount(3); // shows "99+"
badge.setVisible(true);
badge.setBackgroundColor(ContextCompat.getColor(this, R.color.md3_error));
badge.setBadgeTextColor(Color.WHITE);

// Remove
nav.removeBadge(R.id.nav_messages);

// On any view
BadgeDrawable standalone = BadgeDrawable.create(this);
standalone.setNumber(5);
BadgeUtils.attachBadgeDrawable(standalone, toolbar, R.id.action_cart);
```

---

## 19. Material3 Tooltips

### Description
Brief informational messages triggered by long-press or hover.

### XML Layout
```xml
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_tooltip"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hover me"
    app:tooltipText="This is a tooltip message" />
```

### Java Example
```java
TooltipCompat.setTooltipText(view, "Tooltip text");
```

---

## Color Schemes Reference

### Generating a MD3 palette from seed color (Java)
```java
// Using Material Color Utilities (add dependency)
// implementation 'com.google.material:material-color-utilities:0.2.0'

import com.google.material.color.MaterialTheme;
import com.google.material.color.Scheme;

int seedColor = Color.parseColor("#6750A4");
Scheme scheme = Scheme.light(seedColor);
int primary = scheme.getPrimary();
int onPrimary = scheme.getOnPrimary();
int primaryContainer = scheme.getPrimaryContainer();
// ... etc
```

### `res/values/colors.xml` — Night Mode Override
```xml
<!-- res/values-night/colors.xml -->
<resources>
    <color name="md3_primary">#D0BCFF</color>
    <color name="md3_on_primary">#381E72</color>
    <color name="md3_primary_container">#4F378B</color>
    <color name="md3_on_primary_container">#EADDFF</color>
    <color name="md3_surface">#141218</color>
    <color name="md3_on_surface">#E6E1E5</color>
    <color name="md3_background">#141218</color>
    <color name="md3_on_background">#E6E1E5</color>
    <color name="md3_outline">#938F99</color>
    <color name="md3_outline_variant">#49454F</color>
</resources>
```

### MD3 Typography Scale
```xml
<resources>
    <!-- Override in themes.xml -->
    <style name="Theme.App.Material3" parent="Theme.Material3.DayNight.NoActionBar">
        <item name="textAppearanceDisplayLarge">@style/TextAppearance.App.DisplayLarge</item>
        <item name="textAppearanceHeadlineLarge">@style/TextAppearance.App.HeadlineLarge</item>
        <item name="textAppearanceTitleLarge">@style/TextAppearance.App.TitleLarge</item>
        <item name="textAppearanceBodyLarge">@style/TextAppearance.App.BodyLarge</item>
        <item name="textAppearanceLabelLarge">@style/TextAppearance.App.LabelLarge</item>
    </style>

    <style name="TextAppearance.App.DisplayLarge" parent="TextAppearance.Material3.DisplayLarge">
        <item name="fontFamily">@font/roboto</item>
        <item name="android:textSize">57sp</item>
    </style>
    <!-- etc. for each scale token -->
</resources>
```

### Typography Scale Reference
| Token | Size | Weight | Use Case |
|---|---|---|---|
| `displayLarge` | 57sp | Regular | Hero text |
| `displayMedium` | 45sp | Regular | Feature headlines |
| `displaySmall` | 36sp | Regular | Promo headers |
| `headlineLarge` | 32sp | Regular | Page titles |
| `headlineMedium` | 28sp | Regular | Section titles |
| `headlineSmall` | 24sp | Regular | Card titles |
| `titleLarge` | 22sp | Regular | App bar title |
| `titleMedium` | 16sp | Medium | List titles |
| `titleSmall` | 14sp | Medium | Subtitles |
| `bodyLarge` | 16sp | Regular | Primary text |
| `bodyMedium` | 14sp | Regular | Secondary text |
| `bodySmall` | 12sp | Regular | Captions |
| `labelLarge` | 14sp | Medium | Buttons |
| `labelMedium` | 12sp | Medium | Chips/badges |
| `labelSmall` | 11sp | Medium | Overlines |

### Android Version Compatibility (MD3)
| Feature | Min API |
|---|---|
| Dynamic Color (wallpaper-based) | API 31 (Android 12) |
| Dynamic Color (static fallback) | API 21 |
| SearchBar / SearchView | API 21 |
| MaterialSwitch | API 21 |
| SideSheet | API 21 |
| NavigationRail | API 21 |
| All other MD3 components | API 21 |

> All MD3 components require `Theme.Material3.*` parent. Mixing MD2 and MD3 themes in the same activity may produce visual inconsistencies.
