# Module 01 — Material Design 2 Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Table of Contents
1. [MaterialButton](#1-materialbutton)
2. [MaterialCardView](#2-materialcardview)
3. [TextInputLayout & TextInputEditText](#3-textinputlayout--textinputeditttext)
4. [Chip & ChipGroup](#4-chip--chipgroup)
5. [FloatingActionButton (FAB)](#5-floatingactionbutton-fab)
6. [ExtendedFloatingActionButton](#6-extendedfloatingactionbutton)
7. [BottomNavigationView](#7-bottomnavigationview)
8. [NavigationView (Drawer)](#8-navigationview-drawer)
9. [AppBarLayout](#9-appbarlayout)
10. [CollapsingToolbarLayout](#10-collapsingtoolbarlayout)
11. [Snackbar](#11-snackbar)
12. [MaterialAlertDialog](#12-materialalertdialog)
13. [Slider & RangeSlider](#13-slider--rangeslider)
14. [TabLayout](#14-tablayout)
15. [MaterialToolbar](#15-materialtoolbar)
16. [Progress Indicators](#16-progress-indicators)
17. [DatePicker & TimePicker](#17-datepicker--timepicker)
18. [MaterialDivider](#18-materialdivider)
19. [BadgeDrawable](#19-badgedrawable)
20. [MaterialButtonToggleGroup](#20-materialbuttontogglegroupup)

---

## 1. MaterialButton

**Description:** The Material-styled button. Replaces the legacy `Button` widget with proper ripple, shape, and color theming.

**Visual Use Case:** Primary actions (Submit, Continue), secondary actions (Cancel), icon-only actions.

**Material Version:** 🟡 MD2 (also works in MD3 themes)

**Required Dependency:**
```gradle
implementation 'com.google.android.material:material:1.12.0'
```

### XML Example
```xml
<!-- Filled (default) -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnPrimary"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Submit"
    android:textAllCaps="false"
    style="@style/Widget.MaterialComponents.Button" />

<!-- Outlined -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnOutlined"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Cancel"
    style="@style/Widget.MaterialComponents.Button.OutlinedButton" />

<!-- Text button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnText"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Learn More"
    style="@style/Widget.MaterialComponents.Button.TextButton" />

<!-- Icon button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnIcon"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Share"
    app:icon="@drawable/ic_share"
    app:iconGravity="textStart"
    style="@style/Widget.MaterialComponents.Button" />
```

### Kotlin Example
```kotlin
binding.btnPrimary.setOnClickListener {
    // Handle click
}

// Programmatic styling
binding.btnPrimary.apply {
    setBackgroundColor(ContextCompat.getColor(context, R.color.md_theme_primary))
    setTextColor(ContextCompat.getColor(context, R.color.md_theme_onPrimary))
    cornerRadius = resources.getDimensionPixelSize(R.dimen.btn_corner_radius)
    isEnabled = false // disable
}

// Toggle loading state
binding.btnPrimary.apply {
    isEnabled = false
    text = "Loading..."
    // Re-enable after operation
}
```

### Java Example
```java
binding.btnPrimary.setOnClickListener(v -> {
    // Handle click
});

// Programmatic corner radius
binding.btnPrimary.setCornerRadius(
    getResources().getDimensionPixelSize(R.dimen.btn_corner_radius)
);

// Icon programmatically
binding.btnIcon.setIcon(
    ContextCompat.getDrawable(this, R.drawable.ic_share)
);
```

### Key Attributes
| Attribute | Description | Values |
|-----------|-------------|--------|
| `style` | Button variant | Filled, Outlined, TextButton, Icon |
| `app:cornerRadius` | Rounded corners | dp value |
| `app:icon` | Leading icon drawable | @drawable |
| `app:iconGravity` | Icon position | `start`, `end`, `top`, `textStart` |
| `app:iconSize` | Icon size | dp value |
| `app:rippleColor` | Ripple color | @color |
| `android:insetTop/Bottom` | Vertical insets | dp (set 0 to remove extra padding) |

### Customization
```xml
<!-- Custom style in themes.xml -->
<style name="App.Button.Primary" parent="Widget.MaterialComponents.Button">
    <item name="android:textAllCaps">false</item>
    <item name="cornerRadius">12dp</item>
    <item name="android:paddingStart">24dp</item>
    <item name="android:paddingEnd">24dp</item>
    <item name="android:minHeight">48dp</item>
</style>
```

### Best Practices
- ✅ Use `android:textAllCaps="false"` for better readability
- ✅ Minimum touch target: 48dp height
- ✅ Use `android:insetTop="0dp"` and `android:insetBottom="0dp"` to remove unwanted vertical spacing
- ✅ Prefer `MaterialButton` over legacy `Button`

### Common Mistakes
- ❌ Using `Button` instead of `MaterialButton` (loses Material theming)
- ❌ Ignoring `insetTop`/`insetBottom` (causes unexpected spacing)
- ❌ Manually setting `background` drawable (breaks ripple)

### Accessibility
- Set `android:contentDescription` for icon-only buttons
- Ensure color contrast ratio ≥ 4.5:1 for text
- Keep min touch target 48×48dp

---

## 2. MaterialCardView

**Description:** A card container with built-in elevation, shape, and ripple support. Extends `CardView` with Material styling.

**Visual Use Case:** Content containers, list items, product cards, info panels.

**Material Version:** 🟡 MD2

### XML Example
```xml
<!-- Elevated card (default) -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="12dp"
    app:cardElevation="4dp"
    app:cardBackgroundColor="@color/md_theme_surface">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Card Title"
            android:textAppearance="?attr/textAppearanceTitleMedium" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Supporting text goes here."
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>

<!-- Outlined card -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="0dp"
    app:strokeWidth="1dp"
    app:strokeColor="?attr/colorOutline"
    app:cardCornerRadius="12dp" />

<!-- Clickable card with ripple -->
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clickable="true"
    android:focusable="true"
    app:rippleColor="?attr/colorPrimaryContainer" />
```

### Kotlin Example
```kotlin
// Click listener
binding.card.setOnClickListener {
    // Navigate or handle
}

// Programmatic checked state (for selectable cards)
binding.card.isChecked = true

// Dynamic elevation on press
binding.card.apply {
    cardElevation = 8f
    maxCardElevation = 12f
}

// Checked state with stroke
binding.card.apply {
    isCheckable = true
    setOnCheckedChangeListener { card, isChecked ->
        card.strokeWidth = if (isChecked) 2 else 0
        card.strokeColor = ContextCompat.getColor(context, R.color.md_theme_primary)
    }
}
```

### Java Example
```java
binding.card.setOnClickListener(v -> {
    // Navigate
});

// Checkable card
binding.card.setCheckable(true);
binding.card.setChecked(true);
```

### Key Attributes
| Attribute | Description |
|-----------|-------------|
| `app:cardCornerRadius` | Corner radius |
| `app:cardElevation` | Shadow elevation |
| `app:cardBackgroundColor` | Background color |
| `app:strokeWidth` | Border width (outlined style) |
| `app:strokeColor` | Border color |
| `app:rippleColor` | Ripple overlay color |
| `android:checkable` | Enable checked state |

---

## 3. TextInputLayout & TextInputEditText

**Description:** Material text field with animated label, error states, helper text, and icons.

**Visual Use Case:** Forms, login screens, search fields, data entry.

### XML Example
```xml
<!-- Filled text field -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/tilEmail"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Email Address"
    app:startIconDrawable="@drawable/ic_email"
    app:endIconMode="clear_text"
    app:helperText="We'll never share your email"
    style="@style/Widget.MaterialComponents.TextInputLayout.FilledBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/etEmail"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textEmailAddress"
        android:imeOptions="actionNext" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Outlined text field -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Password"
    app:endIconMode="password_toggle"
    style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPassword" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Exposed Dropdown (Spinner replacement) -->
<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Country"
    style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox.ExposedDropdownMenu">

    <AutoCompleteTextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="none" />
</com.google.android.material.textfield.TextInputLayout>
```

### Kotlin Example
```kotlin
// Show error
binding.tilEmail.error = "Invalid email address"
binding.tilEmail.isErrorEnabled = true

// Clear error on typing
binding.etEmail.addTextChangedListener {
    binding.tilEmail.error = null
}

// Dropdown adapter
val countries = listOf("Tanzania", "Kenya", "Uganda")
val adapter = ArrayAdapter(this, R.layout.list_item, countries)
(binding.tilCountry.editText as? AutoCompleteTextView)?.setAdapter(adapter)

// Get text
val email = binding.etEmail.text.toString().trim()

// Character counter
binding.tilBio.counterMaxLength = 200
binding.tilBio.isCounterEnabled = true
```

### Java Example
```java
// Show error
binding.tilEmail.setError("Invalid email address");

// Dropdown
String[] items = {"Option 1", "Option 2"};
ArrayAdapter<String> adapter = new ArrayAdapter<>(
    this, R.layout.list_item, items
);
AutoCompleteTextView autoComplete =
    (AutoCompleteTextView) binding.tilDropdown.getEditText();
if (autoComplete != null) {
    autoComplete.setAdapter(adapter);
}
```

### Key Attributes
| Attribute | Description |
|-----------|-------------|
| `android:hint` | Floating label text |
| `app:endIconMode` | `clear_text`, `password_toggle`, `custom`, `none` |
| `app:startIconDrawable` | Leading icon |
| `app:helperText` | Helper text below field |
| `app:errorText` | Error message (programmatic) |
| `app:counterEnabled` | Show character counter |
| `app:counterMaxLength` | Max characters |
| `app:boxCornerRadius*` | Individual corner radii |
| `app:boxStrokeColor` | Border color |

---

## 4. Chip & ChipGroup

**Description:** Compact interactive elements for filters, tags, selection, and input.

**Visual Use Case:** Filter bars, tag lists, multi-select options, search suggestions.

### XML Example
```xml
<!-- ChipGroup with filter chips -->
<com.google.android.material.chip.ChipGroup
    android:id="@+id/chipGroup"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:singleSelection="false"
    app:chipSpacingHorizontal="8dp">

    <com.google.android.material.chip.Chip
        android:id="@+id/chipAll"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="All"
        android:checkable="true"
        style="@style/Widget.MaterialComponents.Chip.Filter" />

    <com.google.android.material.chip.Chip
        android:id="@+id/chipNews"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="News"
        android:checkable="true"
        style="@style/Widget.MaterialComponents.Chip.Filter" />
</com.google.android.material.chip.ChipGroup>

<!-- Input chip (closable) -->
<com.google.android.material.chip.Chip
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="android@dev.com"
    app:closeIconVisible="true"
    style="@style/Widget.MaterialComponents.Chip.Entry" />

<!-- Action chip -->
<com.google.android.material.chip.Chip
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Add to Calendar"
    app:chipIcon="@drawable/ic_calendar"
    style="@style/Widget.MaterialComponents.Chip.Action" />
```

### Kotlin Example
```kotlin
// Add chips programmatically
val tags = listOf("Kotlin", "Android", "Material")
tags.forEach { tag ->
    val chip = Chip(context).apply {
        text = tag
        isCloseIconVisible = true
        setChipBackgroundColorResource(R.color.md_theme_secondaryContainer)
        setOnCloseIconClickListener { binding.chipGroup.removeView(this) }
    }
    binding.chipGroup.addView(chip)
}

// Listen for selection
binding.chipGroup.setOnCheckedStateChangeListener { group, checkedIds ->
    val selectedChips = checkedIds.map {
        group.findViewById<Chip>(it).text.toString()
    }
    // Use selectedChips
}
```

### Java Example
```java
Chip chip = new Chip(context);
chip.setText("New Tag");
chip.setCloseIconVisible(true);
chip.setOnCloseIconClickListener(v -> binding.chipGroup.removeView(chip));
binding.chipGroup.addView(chip);
```

### Chip Styles Comparison
| Style | Use Case | Checkable | Close Icon |
|-------|----------|-----------|------------|
| `Chip.Filter` | Category filtering | ✅ | ❌ |
| `Chip.Entry` | Input/tags | ❌ | ✅ |
| `Chip.Action` | Actions | ❌ | ❌ |
| `Chip.Choice` | Single select | ✅ | ❌ |

---

## 5. FloatingActionButton (FAB)

**Description:** Circular button for the primary action on a screen.

**Visual Use Case:** Compose message, add item, take photo, navigate.

### XML Example
```xml
<!-- Standard FAB -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:contentDescription="Add new item"
    app:srcCompat="@drawable/ic_add"
    app:fabSize="normal"
    app:backgroundTint="?attr/colorPrimaryContainer"
    app:tint="?attr/colorOnPrimaryContainer" />

<!-- Small FAB -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:fabSize="mini"
    app:srcCompat="@drawable/ic_add" />
```

### Kotlin Example
```kotlin
binding.fab.setOnClickListener {
    // Primary action
}

// Show/hide with animation
binding.fab.show()
binding.fab.hide()

// Animate icon change
binding.fab.setImageResource(R.drawable.ic_edit)

// Coordinate with scroll
val scrollListener = object : RecyclerView.OnScrollListener() {
    override fun onScrolled(rv: RecyclerView, dx: Int, dy: Int) {
        if (dy > 0) binding.fab.hide() else binding.fab.show()
    }
}
binding.recyclerView.addOnScrollListener(scrollListener)
```

---

## 6. ExtendedFloatingActionButton

**Description:** FAB that can show both icon and text label; can shrink/expand.

### XML Example
```xml
<com.google.android.material.floatingactionbutton.ExtendedFloatingActionButton
    android:id="@+id/extFab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:text="Compose"
    android:textAllCaps="false"
    app:icon="@drawable/ic_edit" />
```

### Kotlin Example
```kotlin
// Shrink on scroll, extend when at top
binding.recyclerView.addOnScrollListener(object : RecyclerView.OnScrollListener() {
    override fun onScrolled(rv: RecyclerView, dx: Int, dy: Int) {
        if (dy > 10) binding.extFab.shrink()
        else if (dy < -10) binding.extFab.extend()
    }
})
```

---

## 7. BottomNavigationView

**Description:** Bottom bar with 3–5 destination tabs. Standard Android nav pattern.

**Visual Use Case:** Main app navigation (Home, Search, Library, Profile).

### XML Example
```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:id="@+id/bottomNav"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom"
    app:menu="@menu/bottom_nav_menu"
    app:labelVisibilityMode="labeled"
    app:itemIconTint="@color/bottom_nav_selector"
    app:itemTextColor="@color/bottom_nav_selector"
    app:itemActiveIndicatorStyle="@style/App.BottomNavIndicator" />
```

```xml
<!-- res/menu/bottom_nav_menu.xml -->
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/nav_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item android:id="@+id/nav_search"
        android:icon="@drawable/ic_search"
        android:title="Search" />
    <item android:id="@+id/nav_profile"
        android:icon="@drawable/ic_person"
        android:title="Profile" />
</menu>
```

### Kotlin Example
```kotlin
// With Navigation Component
val navController = findNavController(R.id.navHostFragment)
binding.bottomNav.setupWithNavController(navController)

// Manual selection listener
binding.bottomNav.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_home -> { /* show home */ true }
        R.id.nav_search -> { /* show search */ true }
        else -> false
    }
}

// Add badge
val badge = binding.bottomNav.getOrCreateBadge(R.id.nav_notifications)
badge.number = 5
badge.isVisible = true

// Remove badge
binding.bottomNav.removeBadge(R.id.nav_notifications)
```

---

## 8. NavigationView (Drawer)

**Description:** Side navigation panel inside a `DrawerLayout`.

### XML Example
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:id="@+id/drawerLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Main content -->
    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Drawer -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/navView"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:menu="@menu/drawer_menu"
        app:headerLayout="@layout/nav_header" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### Kotlin Example
```kotlin
val toggle = ActionBarDrawerToggle(
    this, binding.drawerLayout,
    binding.toolbar, R.string.open, R.string.close
)
binding.drawerLayout.addDrawerListener(toggle)
toggle.syncState()

binding.navView.setNavigationItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_settings -> startActivity(Intent(this, SettingsActivity::class.java))
    }
    binding.drawerLayout.closeDrawers()
    true
}
```

---

## 9. AppBarLayout

**Description:** Vertical `LinearLayout` that supports scroll behavior with `CoordinatorLayout`.

### XML Example
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:liftOnScroll="true">

        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:title="My App"
            app:layout_scrollFlags="scroll|enterAlways|snap" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Scroll Flags Reference
| Flag | Behavior |
|------|----------|
| `scroll` | Scroll off screen with content |
| `enterAlways` | Re-appears on any upward scroll |
| `enterAlwaysCollapsed` | Re-appears only when scroll reaches top |
| `exitUntilCollapsed` | Collapses but stays visible |
| `snap` | Snaps to fully visible or fully hidden |

---

## 10. CollapsingToolbarLayout

**Description:** Toolbar that expands with a large image/background and collapses to a regular toolbar on scroll.

### XML Example
```xml
<com.google.android.material.appbar.AppBarLayout
    android:layout_width="match_parent"
    android:layout_height="250dp">

    <com.google.android.material.appbar.CollapsingToolbarLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_scrollFlags="scroll|exitUntilCollapsed"
        app:contentScrim="?attr/colorPrimary"
        app:expandedTitleTextAppearance="@style/TextAppearance.App.DisplaySmall"
        app:collapsedTitleTextAppearance="@style/TextAppearance.App.TitleLarge">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scaleType="centerCrop"
            android:src="@drawable/header_image"
            app:layout_collapseMode="parallax"
            app:layout_collapseParallaxMultiplier="0.7" />

        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_collapseMode="pin" />
    </com.google.android.material.appbar.CollapsingToolbarLayout>
</com.google.android.material.appbar.AppBarLayout>
```

### Kotlin Example
```kotlin
binding.collapsingToolbar.apply {
    title = "Profile"
    setExpandedTitleColor(Color.WHITE)
    setCollapsedTitleTextColor(ContextCompat.getColor(context, R.color.md_theme_onPrimary))
}
```

---

## 11. Snackbar

**Description:** Lightweight feedback message at the bottom of the screen. Replaces `Toast` for interactive feedback.

### Kotlin Example
```kotlin
// Simple
Snackbar.make(binding.root, "Item saved", Snackbar.LENGTH_SHORT).show()

// With action
Snackbar.make(binding.root, "Item deleted", Snackbar.LENGTH_LONG)
    .setAction("UNDO") { viewModel.undoDelete() }
    .setActionTextColor(ContextCompat.getColor(this, R.color.md_theme_primary))
    .show()

// Anchor above FAB
Snackbar.make(binding.root, "Message sent", Snackbar.LENGTH_SHORT)
    .setAnchorView(binding.fab)
    .show()

// Custom duration
Snackbar.make(binding.root, "Processing...", 5000).show()
```

### Java Example
```java
Snackbar.make(binding.root, "Item saved", Snackbar.LENGTH_SHORT)
    .setAction("UNDO", v -> viewModel.undoDelete())
    .show();
```

---

## 12. MaterialAlertDialog

**Description:** Material-styled dialog for confirmations, selections, and input.

### Kotlin Example
```kotlin
// Confirmation dialog
MaterialAlertDialogBuilder(context)
    .setTitle("Delete item?")
    .setMessage("This action cannot be undone.")
    .setPositiveButton("Delete") { _, _ -> viewModel.delete() }
    .setNegativeButton("Cancel", null)
    .show()

// Single choice dialog
val options = arrayOf("Option A", "Option B", "Option C")
var selectedIndex = 0
MaterialAlertDialogBuilder(context)
    .setTitle("Choose option")
    .setSingleChoiceItems(options, selectedIndex) { _, which ->
        selectedIndex = which
    }
    .setPositiveButton("OK") { _, _ -> /* use selectedIndex */ }
    .setNegativeButton("Cancel", null)
    .show()

// Multi-choice dialog
val checkedItems = booleanArrayOf(true, false, true)
MaterialAlertDialogBuilder(context)
    .setTitle("Select features")
    .setMultiChoiceItems(options, checkedItems) { _, which, isChecked ->
        checkedItems[which] = isChecked
    }
    .setPositiveButton("Apply") { _, _ -> }
    .show()

// Custom view dialog
val dialogView = LayoutInflater.from(context).inflate(R.layout.dialog_custom, null)
MaterialAlertDialogBuilder(context)
    .setView(dialogView)
    .show()
```

---

## 13. Slider & RangeSlider

**Description:** Continuous or discrete value selector.

**Visual Use Case:** Volume control, price range filter, rating, brightness.

### XML Example
```xml
<!-- Continuous Slider -->
<com.google.android.material.slider.Slider
    android:id="@+id/slider"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:valueFrom="0"
    android:valueTo="100"
    android:stepSize="0"
    android:value="50" />

<!-- Discrete Slider (steps) -->
<com.google.android.material.slider.Slider
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:valueFrom="0"
    android:valueTo="5"
    android:stepSize="1"
    app:labelBehavior="floating" />

<!-- Range Slider -->
<com.google.android.material.slider.RangeSlider
    android:id="@+id/rangeSlider"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:valueFrom="0"
    android:valueTo="1000"
    app:values="@array/range_slider_values" />
```

### Kotlin Example
```kotlin
// Read value
val value = binding.slider.value

// Listen for changes
binding.slider.addOnChangeListener { slider, value, fromUser ->
    binding.tvValue.text = "Volume: ${value.toInt()}%"
}

// Range slider
val (min, max) = binding.rangeSlider.values
binding.tvRange.text = "$$min – $$max"

// Format label
binding.slider.setLabelFormatter { value ->
    "$${value.toInt()}"
}
```

---

## 14. TabLayout

**Description:** Horizontal tab strip for switching between views or fragments.

### XML Example
```xml
<com.google.android.material.tabs.TabLayout
    android:id="@+id/tabLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabMode="fixed"
    app:tabGravity="fill"
    app:tabIndicatorColor="?attr/colorPrimary"
    app:tabTextColor="?attr/colorOnSurfaceVariant"
    app:tabSelectedTextColor="?attr/colorPrimary" />

<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1" />
```

### Kotlin Example
```kotlin
// Connect to ViewPager2
val adapter = MyPagerAdapter(this)
binding.viewPager.adapter = adapter

TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, position ->
    tab.text = when (position) {
        0 -> "Posts"
        1 -> "About"
        2 -> "Photos"
        else -> ""
    }
    tab.setIcon(tabIcons[position])
}.attach()
```

### Tab Mode Options
| Mode | Description |
|------|-------------|
| `fixed` | Equal width tabs, fills width |
| `scrollable` | Tabs scroll horizontally |
| `auto` | Fixed for ≤4 tabs, scrollable otherwise |

---

## 15. MaterialToolbar

**Description:** Material-styled `Toolbar` replacement with proper typography and icon handling.

### XML Example
```xml
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:title="Screen Title"
    app:subtitle="Subtitle"
    app:navigationIcon="@drawable/ic_arrow_back"
    app:menu="@menu/toolbar_menu" />
```

### Kotlin Example
```kotlin
setSupportActionBar(binding.toolbar)
supportActionBar?.setDisplayHomeAsUpEnabled(true)
binding.toolbar.setNavigationOnClickListener { onBackPressedDispatcher.onBackPressed() }

// Dynamic title
binding.toolbar.title = "New Title"
```

---

## 16. Progress Indicators

**Description:** Circular and linear loaders showing determinate or indeterminate progress.

### XML Example
```xml
<!-- Circular indeterminate -->
<com.google.android.material.progressindicator.CircularProgressIndicator
    android:id="@+id/progressCircular"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:indeterminate="true"
    app:indicatorColor="?attr/colorPrimary"
    app:trackColor="?attr/colorSurfaceVariant" />

<!-- Linear determinate -->
<com.google.android.material.progressindicator.LinearProgressIndicator
    android:id="@+id/progressLinear"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:indeterminate="false"
    android:progress="60"
    app:trackCornerRadius="4dp" />

<!-- Linear indeterminate -->
<com.google.android.material.progressindicator.LinearProgressIndicator
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:indeterminate="true" />
```

### Kotlin Example
```kotlin
// Show/hide
binding.progressCircular.show()
binding.progressCircular.hide()

// Update progress
binding.progressLinear.progress = 75
binding.progressLinear.setProgressCompat(75, true) // animated

// Buffer progress
binding.progressLinear.secondaryProgress = 90 // buffer indicator
```

---

## 17. DatePicker & TimePicker

**Description:** Material-styled dialogs for date and time selection.

### Kotlin Example (MaterialDatePicker)
```kotlin
// Single date picker
val datePicker = MaterialDatePicker.Builder.datePicker()
    .setTitleText("Select Date")
    .setSelection(MaterialDatePicker.todayInUtcMilliseconds())
    .build()

datePicker.addOnPositiveButtonClickListener { selectedDate ->
    val calendar = Calendar.getInstance(TimeZone.getTimeZone("UTC"))
    calendar.timeInMillis = selectedDate
    binding.tvDate.text = datePicker.headerText
}
datePicker.show(supportFragmentManager, "DATE_PICKER")

// Date range picker
val rangePicker = MaterialDatePicker.Builder.dateRangePicker()
    .setTitleText("Select Range")
    .setSelection(Pair(
        MaterialDatePicker.thisMonthInUtcMilliseconds(),
        MaterialDatePicker.todayInUtcMilliseconds()
    ))
    .build()

rangePicker.addOnPositiveButtonClickListener { selection ->
    val start = selection.first
    val end = selection.second
}
rangePicker.show(supportFragmentManager, "RANGE_PICKER")
```

### Kotlin Example (MaterialTimePicker)
```kotlin
val timePicker = MaterialTimePicker.Builder()
    .setTimeFormat(TimeFormat.CLOCK_12H)
    .setHour(12)
    .setMinute(0)
    .setTitleText("Select Alarm Time")
    .build()

timePicker.addOnPositiveButtonClickListener {
    val hour = timePicker.hour
    val minute = timePicker.minute
    binding.tvTime.text = "%02d:%02d".format(hour, minute)
}
timePicker.show(supportFragmentManager, "TIME_PICKER")
```

---

## 18. MaterialDivider

**Description:** Thin horizontal or vertical line separator between content sections.

### XML Example
```xml
<!-- Horizontal divider -->
<com.google.android.material.divider.MaterialDivider
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:dividerColor="?attr/colorOutlineVariant"
    app:dividerInsetStart="16dp"
    app:dividerInsetEnd="16dp" />

<!-- In RecyclerView as ItemDecoration -->
```

### Kotlin Example
```kotlin
// As RecyclerView divider
val divider = MaterialDividerItemDecoration(context, LinearLayoutManager.VERTICAL).apply {
    dividerInsetStart = resources.getDimensionPixelSize(R.dimen.divider_inset)
    isLastItemDecorated = false
}
binding.recyclerView.addItemDecoration(divider)
```

---

## 19. BadgeDrawable

**Description:** Notification count badge overlaid on icons or views.

### Kotlin Example
```kotlin
// On a view
val badge = BadgeDrawable.create(context).apply {
    number = 99
    isVisible = true
    backgroundColor = ContextCompat.getColor(context, R.color.md_theme_error)
    badgeTextColor = ContextCompat.getColor(context, R.color.md_theme_onError)
    maxCharacterCount = 3 // Shows "99+" at 100+
}
BadgeUtils.attachBadgeDrawable(badge, binding.ivNotification, binding.frameContainer)

// On BottomNavigationView
val navBadge = binding.bottomNav.getOrCreateBadge(R.id.nav_messages)
navBadge.number = 3

// Remove badge
binding.bottomNav.removeBadge(R.id.nav_messages)
```

---

## 20. MaterialButtonToggleGroup

**Description:** Group of buttons where one or more can be selected (like segmented control in MD2).

### XML Example
```xml
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/toggleGroup"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    app:selectionRequired="true">

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnDay"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Day"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnWeek"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Week"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnMonth"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Month"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

### Kotlin Example
```kotlin
binding.toggleGroup.addOnButtonCheckedListener { group, checkedId, isChecked ->
    if (isChecked) {
        when (checkedId) {
            R.id.btnDay -> showDayView()
            R.id.btnWeek -> showWeekView()
            R.id.btnMonth -> showMonthView()
        }
    }
}

// Programmatic selection
binding.toggleGroup.check(R.id.btnWeek)
```

---

## Component Comparison Table

| Component | MD2 Class | MD3 Equivalent | Notes |
|-----------|-----------|----------------|-------|
| Button | `MaterialButton` | `MaterialButton` (M3 theme) | Same class, different styles |
| Card | `MaterialCardView` | `MaterialCardView` | Same class |
| Text Field | `TextInputLayout` | `TextInputLayout` (M3 styles) | Add M3 theme |
| FAB | `FloatingActionButton` | `FloatingActionButton` (M3) | M3 adds new sizes |
| Bottom Nav | `BottomNavigationView` | `BottomNavigationView` (M3) | M3 adds indicators |
| Progress | `CircularProgressIndicator` | Same | Unchanged |
| Dialog | `MaterialAlertDialogBuilder` | Same | Add M3 theme |
| Chip | `Chip` | `Chip` (M3 styles) | M3 has elevated style |
