# Material Design 2 Components — Android UI Encyclopedia

> **File:** `01_material_design_2.md` | **API Target:** 35 | **MDC Version:** 1.12.x

---

## Table of Contents

1. [Setup & Dependencies](#setup--dependencies)
2. [MaterialButton](#1-materialbutton)
3. [MaterialCardView](#2-materialcardview)
4. [TextInputLayout & TextInputEditText](#3-textinputlayout--textinputedittext)
5. [Chip & ChipGroup](#4-chip--chipgroup)
6. [FloatingActionButton (FAB)](#5-floatingactionbutton-fab)
7. [ExtendedFloatingActionButton](#6-extendedfloatingactionbutton)
8. [BottomNavigationView](#7-bottomnavigationview)
9. [NavigationView (Drawer)](#8-navigationview-drawer)
10. [AppBarLayout](#9-appbarlayout)
11. [CollapsingToolbarLayout](#10-collapsingtoolbarlayout)
12. [Snackbar](#11-snackbar)
13. [MaterialAlertDialog](#12-materialalertdialog)
14. [Slider & RangeSlider](#13-slider--rangeslider)
15. [TabLayout](#14-tablayout)
16. [MaterialToolbar](#15-materialtoolbar)
17. [Progress Indicators](#16-progress-indicators)
18. [DatePicker & TimePicker](#17-datepicker--timepicker)
19. [MaterialDivider](#18-materialdivider)
20. [BottomSheetDialog](#19-bottomsheetdialog)
21. [MaterialBanner](#20-materialbanner)
22. [Themes & Colors Reference](#themes--colors-reference)

---

## Setup & Dependencies

### `build.gradle (app)`
```groovy
dependencies {
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
}
```

### `res/values/themes.xml`
```xml
<resources>
    <style name="Theme.App" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
        <item name="colorPrimary">@color/md2_primary</item>
        <item name="colorPrimaryVariant">@color/md2_primary_variant</item>
        <item name="colorOnPrimary">@color/white</item>
        <item name="colorSecondary">@color/md2_secondary</item>
        <item name="colorSecondaryVariant">@color/md2_secondary_variant</item>
        <item name="colorOnSecondary">@color/black</item>
        <item name="colorSurface">@color/white</item>
        <item name="colorOnSurface">@color/black</item>
        <item name="colorError">@color/md2_error</item>
        <item name="colorOnError">@color/white</item>
        <item name="colorBackground">@color/md2_background</item>
        <item name="colorOnBackground">@color/black</item>
        <item name="android:statusBarColor">?attr/colorPrimaryVariant</item>
    </style>
</resources>
```

### `res/values/colors.xml`
```xml
<resources>
    <color name="md2_primary">#6200EE</color>
    <color name="md2_primary_variant">#3700B3</color>
    <color name="md2_secondary">#03DAC6</color>
    <color name="md2_secondary_variant">#018786</color>
    <color name="md2_background">#FFFFFF</color>
    <color name="md2_surface">#FFFFFF</color>
    <color name="md2_error">#B00020</color>
    <color name="white">#FFFFFF</color>
    <color name="black">#000000</color>
</resources>
```

### `res/values/dimens.xml`
```xml
<resources>
    <dimen name="spacing_small">8dp</dimen>
    <dimen name="spacing_medium">16dp</dimen>
    <dimen name="spacing_large">24dp</dimen>
    <dimen name="card_corner_radius">12dp</dimen>
    <dimen name="card_elevation">4dp</dimen>
    <dimen name="button_corner_radius">8dp</dimen>
    <dimen name="fab_size">56dp</dimen>
</resources>
```

---

## 1. MaterialButton

### Description
A customizable button component following Material Design 2 guidelines. Supports text, icon, outlined, and text-only variants.

### Visual Use Case
Primary actions, form submissions, dialogs, inline actions.

### Material Version: MD2

### XML Layout
```xml
<!-- Filled (default) -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_filled"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Filled Button"
    style="@style/Widget.MaterialComponents.Button" />

<!-- Outlined -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_outlined"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Outlined"
    style="@style/Widget.MaterialComponents.Button.OutlinedButton" />

<!-- Text -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_text"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Text Button"
    style="@style/Widget.MaterialComponents.Button.TextButton" />

<!-- Icon Button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btn_icon"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Icon Button"
    app:icon="@drawable/ic_add"
    app:iconGravity="textStart"
    app:iconPadding="8dp"
    style="@style/Widget.MaterialComponents.Button" />

<!-- Toggle Button Group -->
<com.google.android.material.button.MaterialButtonToggleGroup
    android:id="@+id/toggle_group"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    app:selectionRequired="true">
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_day"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Day"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_week"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Week"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_month"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Month"
        style="@style/Widget.MaterialComponents.Button.OutlinedButton" />
</com.google.android.material.button.MaterialButtonToggleGroup>
```

### Java Example
```java
MaterialButton btnFilled = findViewById(R.id.btn_filled);
btnFilled.setOnClickListener(v -> {
    // handle click
});

// Programmatic styling
btnFilled.setCornerRadius(16);
btnFilled.setStrokeWidth(2);
btnFilled.setStrokeColor(ColorStateList.valueOf(Color.BLUE));
btnFilled.setIconResource(R.drawable.ic_check);
btnFilled.setIconGravity(MaterialButton.ICON_GRAVITY_TEXT_START);

// Toggle group
MaterialButtonToggleGroup toggleGroup = findViewById(R.id.toggle_group);
toggleGroup.addOnButtonCheckedListener((group, checkedId, isChecked) -> {
    if (isChecked) {
        if (checkedId == R.id.btn_day) { /* day selected */ }
        else if (checkedId == R.id.btn_week) { /* week selected */ }
    }
});
```

### Kotlin Example
```kotlin
val btnFilled: MaterialButton = findViewById(R.id.btn_filled)
btnFilled.setOnClickListener {
    // handle click
}
btnFilled.cornerRadius = 16
btnFilled.strokeWidth = 2
btnFilled.strokeColor = ColorStateList.valueOf(Color.BLUE)

val toggleGroup: MaterialButtonToggleGroup = findViewById(R.id.toggle_group)
toggleGroup.addOnButtonCheckedListener { group, checkedId, isChecked ->
    if (isChecked) {
        when (checkedId) {
            R.id.btn_day -> { /* day */ }
            R.id.btn_week -> { /* week */ }
        }
    }
}
```

### Attributes
| Attribute | Description | Default |
|---|---|---|
| `app:cornerRadius` | Corner radius in dp | 4dp |
| `app:strokeColor` | Border color | transparent |
| `app:strokeWidth` | Border width | 0dp |
| `app:icon` | Leading icon drawable | none |
| `app:iconGravity` | Icon position | `textStart` |
| `app:iconPadding` | Space between icon and text | 4dp |
| `app:rippleColor` | Ripple color on press | primary tint |
| `app:backgroundTint` | Background color | colorPrimary |

### Customization via Theme
```xml
<style name="Widget.App.Button" parent="Widget.MaterialComponents.Button">
    <item name="cornerRadius">24dp</item>
    <item name="android:paddingLeft">32dp</item>
    <item name="android:paddingRight">32dp</item>
    <item name="android:textAllCaps">false</item>
    <item name="android:letterSpacing">0</item>
</style>
```

### Best Practices
- Use `style` attribute rather than overriding individual attributes for consistency.
- Minimum touch target: 48x48dp — set `android:minWidth` and `android:minHeight`.
- Use `app:enabled="false"` state for disabled buttons rather than hiding them.
- Prefer `MaterialButtonToggleGroup` over `RadioGroup` for segmented selections.

### Common Mistakes
- Forgetting `style="@style/Widget.MaterialComponents.Button"` loses Material behavior.
- Setting `android:background` overrides MDC tinting system — use `app:backgroundTint`.
- Using `Button` parent tag instead of `MaterialButton` in XML loses ripple & elevation.

### Accessibility
- Always set `android:contentDescription` for icon-only buttons.
- Ensure 4.5:1 contrast ratio between text and button background.
- Test with TalkBack — the button label must be descriptive.

### Performance
- Avoid inflating many buttons in a loop — use RecyclerView with ViewHolder.
- Icon drawables: use VectorDrawable (24dp) for scalability without bitmap decode cost.

---

## 2. MaterialCardView

### Description
A container component with rounded corners, elevation/shadow, and stroke support. Used for grouping related content into visually distinct units.

### Visual Use Case
Product cards, profile cards, article previews, dashboard tiles.

### XML Layout
```xml
<!-- Basic Card -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_basic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="12dp"
    app:cardElevation="4dp"
    app:strokeColor="@color/md2_primary"
    app:strokeWidth="1dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Card Title"
            android:textAppearance="?attr/textAppearanceHeadline6" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Card subtitle text with supporting content"
            android:textAppearance="?attr/textAppearanceBody2" />

    </LinearLayout>
</com.google.android.material.card.MaterialCardView>

<!-- Clickable / Checkable Card -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_clickable"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="12dp"
    app:cardElevation="2dp"
    app:checkedIcon="@drawable/ic_check"
    app:checkable="true">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>

<!-- Outlined Card -->
<com.google.android.material.card.MaterialCardView
    android:id="@+id/card_outlined"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="0dp"
    app:strokeColor="?attr/colorOnSurface"
    app:strokeWidth="1dp"
    app:cardCornerRadius="12dp">
    <!-- content -->
</com.google.android.material.card.MaterialCardView>
```

### Java Example
```java
MaterialCardView card = findViewById(R.id.card_basic);
card.setRadius(dpToPx(12));
card.setCardElevation(dpToPx(4));
card.setStrokeColor(ContextCompat.getColor(this, R.color.md2_primary));
card.setStrokeWidth(dpToPx(1));
card.setChecked(true);

card.setOnClickListener(v -> {
    card.setChecked(!card.isChecked());
});

// Programmatic card creation
MaterialCardView dynamicCard = new MaterialCardView(this);
dynamicCard.setRadius(dpToPx(16));
dynamicCard.setCardElevation(dpToPx(6));
FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(
    ViewGroup.LayoutParams.MATCH_PARENT,
    ViewGroup.LayoutParams.WRAP_CONTENT
);
params.setMargins(dpToPx(8), dpToPx(8), dpToPx(8), dpToPx(8));
dynamicCard.setLayoutParams(params);

private int dpToPx(int dp) {
    return (int) (dp * getResources().getDisplayMetrics().density);
}
```

### Kotlin Example
```kotlin
val card: MaterialCardView = findViewById(R.id.card_basic)
card.radius = 12f.dpToPx(this)
card.cardElevation = 4f.dpToPx(this)
card.strokeColor = ContextCompat.getColor(this, R.color.md2_primary)
card.strokeWidth = 1.dpToPx(this).toInt()

card.setOnClickListener {
    card.isChecked = !card.isChecked
}

fun Float.dpToPx(context: Context): Float =
    this * context.resources.displayMetrics.density
```

### Attributes
| Attribute | Description |
|---|---|
| `app:cardCornerRadius` | Corner rounding radius |
| `app:cardElevation` | Shadow depth |
| `app:strokeColor` | Outline color |
| `app:strokeWidth` | Outline width |
| `app:cardBackgroundColor` | Background fill |
| `app:rippleColor` | Ripple on click |
| `app:checkable` | Enable checked state |
| `app:checkedIcon` | Icon shown when checked |
| `app:checkedIconTint` | Tint for checked icon |

### Best Practices
- Keep card content padding at minimum 16dp.
- Use `app:cardElevation="0dp"` + `app:strokeWidth="1dp"` for outlined variant.
- Set `android:clickable="true"` + `android:focusable="true"` for interactive cards.
- Avoid nesting scrollable content inside cards.

### Common Mistakes
- Using `FrameLayout` root inside card wastes nesting — use `MaterialCardView` as direct parent.
- Forgetting `android:clickable="true"` means ripple never shows.

### Accessibility
- If the whole card is clickable, set `android:contentDescription` on the card itself.

---

## 3. TextInputLayout & TextInputEditText

### Description
A wrapper around `EditText` that provides floating label, helper text, error state, character counter, and prefix/suffix support.

### Visual Use Case
Login forms, registration forms, search bars, filter inputs.

### XML Layout
```xml
<!-- Filled style (default) -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_email"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Email Address"
    app:helperText="We'll never share your email"
    app:helperTextEnabled="true"
    app:counterEnabled="true"
    app:counterMaxLength="100"
    app:startIconDrawable="@drawable/ic_email"
    app:endIconMode="clear_text"
    style="@style/Widget.MaterialComponents.TextInputLayout.FilledBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/et_email"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textEmailAddress"
        android:imeOptions="actionNext" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Outlined style -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_password"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Password"
    app:endIconMode="password_toggle"
    style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/et_password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPassword" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Exposed Dropdown -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_dropdown"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Country"
    style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox.ExposedDropdownMenu">

    <AutoCompleteTextView
        android:id="@+id/act_country"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="none" />
</com.google.android.material.textfield.TextInputLayout>

<!-- Prefix / Suffix -->
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_price"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Amount"
    app:prefixText="$"
    app:suffixText=".00"
    style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="numberDecimal" />
</com.google.android.material.textfield.TextInputLayout>
```

### Java Example
```java
TextInputLayout tilEmail = findViewById(R.id.til_email);
TextInputEditText etEmail = findViewById(R.id.et_email);

// Set error
tilEmail.setError("Invalid email format");
tilEmail.setErrorEnabled(true);

// Clear error
tilEmail.setErrorEnabled(false);

// Get text
String email = Objects.requireNonNull(etEmail.getText()).toString().trim();

// Validate
if (email.isEmpty()) {
    tilEmail.setError("Email required");
    return;
}
if (!Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
    tilEmail.setError("Enter a valid email");
    return;
}
tilEmail.setError(null);

// Exposed Dropdown
AutoCompleteTextView actCountry = findViewById(R.id.act_country);
String[] countries = {"United States", "United Kingdom", "Canada", "Australia"};
ArrayAdapter<String> adapter = new ArrayAdapter<>(this,
    android.R.layout.simple_dropdown_item_1line, countries);
actCountry.setAdapter(adapter);

// Custom end icon click
tilEmail.setEndIconOnClickListener(v -> {
    etEmail.setText("");
});
```

### Kotlin Example
```kotlin
val tilEmail: TextInputLayout = findViewById(R.id.til_email)
val etEmail: TextInputEditText = findViewById(R.id.et_email)

val email = etEmail.text?.toString()?.trim() ?: ""

when {
    email.isEmpty() -> tilEmail.error = "Email required"
    !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> tilEmail.error = "Invalid email"
    else -> {
        tilEmail.error = null
        // proceed
    }
}

// Dropdown
val actCountry: AutoCompleteTextView = findViewById(R.id.act_country)
val countries = arrayOf("United States", "United Kingdom", "Canada")
val adapter = ArrayAdapter(this, android.R.layout.simple_dropdown_item_1line, countries)
actCountry.setAdapter(adapter)
```

### Attributes
| Attribute | Description |
|---|---|
| `android:hint` | Floating label text |
| `app:helperText` | Helper text below field |
| `app:errorEnabled` | Enable error state |
| `app:counterEnabled` | Show character count |
| `app:counterMaxLength` | Max characters |
| `app:startIconDrawable` | Leading icon |
| `app:endIconMode` | `clear_text`, `password_toggle`, `custom`, `none` |
| `app:prefixText` | Text prefix inside field |
| `app:suffixText` | Text suffix inside field |
| `app:boxCornerRadius*` | Corner radii (OutlinedBox) |
| `app:boxStrokeColor` | Outline color |

### Custom Error Style
```xml
<style name="Widget.App.TextInputLayout" parent="Widget.MaterialComponents.TextInputLayout.OutlinedBox">
    <item name="boxStrokeColor">@color/text_input_stroke_color</item>
    <item name="hintTextColor">@color/text_input_hint_color</item>
    <item name="errorTextColor">@color/md2_error</item>
    <item name="boxCornerRadiusTopStart">12dp</item>
    <item name="boxCornerRadiusTopEnd">12dp</item>
    <item name="boxCornerRadiusBottomStart">12dp</item>
    <item name="boxCornerRadiusBottomEnd">12dp</item>
</style>
```

### `res/color/text_input_stroke_color.xml`
```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="@color/md2_error" android:state_activated="true"/>
    <item android:color="@color/md2_primary" android:state_focused="true"/>
    <item android:color="#80000000"/>
</selector>
```

---

## 4. Chip & ChipGroup

### Description
Compact, rounded elements representing attributes, filters, tags, or actions. Four types: Action, Filter, Choice, Input.

### XML Layout
```xml
<!-- Choice Chip Group -->
<com.google.android.material.chip.ChipGroup
    android:id="@+id/chip_group_choice"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    app:selectionRequired="false"
    android:padding="8dp">

    <com.google.android.material.chip.Chip
        android:id="@+id/chip_all"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="All"
        android:checked="true"
        style="@style/Widget.MaterialComponents.Chip.Choice" />

    <com.google.android.material.chip.Chip
        android:id="@+id/chip_music"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Music"
        app:chipIcon="@drawable/ic_music"
        style="@style/Widget.MaterialComponents.Chip.Choice" />

    <com.google.android.material.chip.Chip
        android:id="@+id/chip_video"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Video"
        style="@style/Widget.MaterialComponents.Chip.Choice" />
</com.google.android.material.chip.ChipGroup>

<!-- Filter Chips -->
<com.google.android.material.chip.ChipGroup
    android:id="@+id/chip_group_filter"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:singleLine="true">

    <com.google.android.material.chip.Chip
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Under $50"
        app:checkedIconVisible="true"
        style="@style/Widget.MaterialComponents.Chip.Filter" />
</com.google.android.material.chip.ChipGroup>

<!-- Input Chip (closeable) -->
<com.google.android.material.chip.Chip
    android:id="@+id/chip_input"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Android"
    app:closeIconVisible="true"
    style="@style/Widget.MaterialComponents.Chip.Entry" />
```

### Java Example
```java
// Dynamic chip creation
ChipGroup chipGroup = findViewById(R.id.chip_group_filter);
String[] filters = {"Color", "Size", "Brand", "Price", "Rating"};
for (String filter : filters) {
    Chip chip = new Chip(this);
    chip.setText(filter);
    chip.setCheckable(true);
    chip.setChipBackgroundColorResource(R.color.chip_bg_selector);
    chipGroup.addView(chip);
}

// Get selected chips
chipGroup.setOnCheckedStateChangeListener((group, checkedIds) -> {
    List<String> selected = new ArrayList<>();
    for (int id : checkedIds) {
        Chip c = group.findViewById(id);
        if (c != null) selected.add(c.getText().toString());
    }
    Log.d("Filter", "Selected: " + selected);
});

// Input chip removal
Chip inputChip = findViewById(R.id.chip_input);
inputChip.setOnCloseIconClickListener(v -> {
    ((ViewGroup) inputChip.getParent()).removeView(inputChip);
});
```

### Kotlin Example
```kotlin
val chipGroup: ChipGroup = findViewById(R.id.chip_group_filter)
val filters = listOf("Color", "Size", "Brand", "Price")
filters.forEach { filter ->
    val chip = Chip(this).apply {
        text = filter
        isCheckable = true
    }
    chipGroup.addView(chip)
}

chipGroup.setOnCheckedStateChangeListener { group, checkedIds ->
    val selected = checkedIds.map { id ->
        (group.findViewById<Chip>(id)).text.toString()
    }
    Log.d("Filter", "Selected: $selected")
}
```

### Chip Styles Comparison
| Style | Purpose | Checkable | Closeable |
|---|---|---|---|
| `Chip.Action` | Triggers action | No | No |
| `Chip.Filter` | Multi-filter | Yes | Optional |
| `Chip.Choice` | Single select | Yes | No |
| `Chip.Entry` | Input tag | No | Yes |

---

## 5. FloatingActionButton (FAB)

### Description
A circular button representing the primary action of a screen. Animates on scroll and supports mini/normal/large sizes.

### XML Layout
```xml
<!-- Standard FAB -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:id="@+id/fab_add"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:contentDescription="Add item"
    app:srcCompat="@drawable/ic_add"
    app:fabSize="normal"
    app:tint="@color/white"
    app:backgroundTint="?attr/colorSecondary" />

<!-- Mini FAB -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:id="@+id/fab_mini"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:fabSize="mini"
    app:srcCompat="@drawable/ic_edit" />
```

### Java Example
```java
FloatingActionButton fab = findViewById(R.id.fab_add);

// Show/hide with animation
fab.show();
fab.hide();

// With CoordinatorLayout behavior on scroll
fab.setOnClickListener(v ->
    Snackbar.make(v, "Item added", Snackbar.LENGTH_SHORT).show()
);

// Animate FAB rotation
fab.setOnClickListener(v -> {
    fab.animate().rotation(fab.getRotation() + 45f).setDuration(200).start();
});
```

### Kotlin Example
```kotlin
val fab: FloatingActionButton = findViewById(R.id.fab_add)
fab.setOnClickListener { view ->
    Snackbar.make(view, "Item added", Snackbar.LENGTH_SHORT).show()
}

// Hide on scroll in CoordinatorLayout
fab.show()
fab.hide()
```

### FAB + CoordinatorLayout (scroll behavior)
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        app:srcCompat="@drawable/ic_add"
        app:layout_behavior="com.google.android.material.floatingactionbutton.FloatingActionButton$Behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

---

## 6. ExtendedFloatingActionButton

### Description
A FAB variant that includes text and optionally an icon. Can expand/shrink with animation.

### XML Layout
```xml
<com.google.android.material.floatingactionbutton.ExtendedFloatingActionButton
    android:id="@+id/efab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:text="Compose"
    app:icon="@drawable/ic_edit"
    style="@style/Widget.MaterialComponents.ExtendedFloatingActionButton.Icon" />
```

### Java Example
```java
ExtendedFloatingActionButton efab = findViewById(R.id.efab);
efab.extend();   // show text
efab.shrink();   // hide text, show icon only

// Toggle on scroll
recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrolled(@NonNull RecyclerView rv, int dx, int dy) {
        if (dy > 0 && efab.isExtended()) efab.shrink();
        else if (dy < 0 && !efab.isExtended()) efab.extend();
    }
});
```

---

## 7. BottomNavigationView

### Description
A navigation bar at the bottom of the screen with 3–5 destinations. Follows Material Design tab/navigation patterns.

### XML Layout
```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:id="@+id/bottom_nav"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom"
    app:menu="@menu/bottom_nav_menu"
    app:labelVisibilityMode="labeled"
    app:itemIconTint="@color/bottom_nav_color"
    app:itemTextColor="@color/bottom_nav_color"
    app:backgroundTint="?attr/colorSurface" />
```

### `res/menu/bottom_nav_menu.xml`
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/nav_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item android:id="@+id/nav_search"
        android:icon="@drawable/ic_search"
        android:title="Search" />
    <item android:id="@+id/nav_notifications"
        android:icon="@drawable/ic_bell"
        android:title="Alerts" />
    <item android:id="@+id/nav_profile"
        android:icon="@drawable/ic_person"
        android:title="Profile" />
</menu>
```

### `res/color/bottom_nav_color.xml`
```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="?attr/colorPrimary" android:state_checked="true"/>
    <item android:color="?attr/colorOnSurface" android:state_checked="false"/>
</selector>
```

### Java Example
```java
BottomNavigationView bottomNav = findViewById(R.id.bottom_nav);

bottomNav.setOnItemSelectedListener(item -> {
    int id = item.getItemId();
    if (id == R.id.nav_home) {
        loadFragment(new HomeFragment());
        return true;
    } else if (id == R.id.nav_search) {
        loadFragment(new SearchFragment());
        return true;
    } else if (id == R.id.nav_notifications) {
        loadFragment(new NotificationsFragment());
        return true;
    } else if (id == R.id.nav_profile) {
        loadFragment(new ProfileFragment());
        return true;
    }
    return false;
});

// Badge
BadgeDrawable badge = bottomNav.getOrCreateBadge(R.id.nav_notifications);
badge.setNumber(5);
badge.setVisible(true);

// Remove badge
bottomNav.removeBadge(R.id.nav_notifications);

private void loadFragment(Fragment fragment) {
    getSupportFragmentManager()
        .beginTransaction()
        .replace(R.id.fragment_container, fragment)
        .commit();
}
```

### Kotlin Example
```kotlin
val bottomNav: BottomNavigationView = findViewById(R.id.bottom_nav)
bottomNav.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_home -> { loadFragment(HomeFragment()); true }
        R.id.nav_search -> { loadFragment(SearchFragment()); true }
        R.id.nav_profile -> { loadFragment(ProfileFragment()); true }
        else -> false
    }
}

val badge = bottomNav.getOrCreateBadge(R.id.nav_notifications)
badge.number = 5
badge.isVisible = true
```

---

## 8. NavigationView (Drawer)

### Description
Side navigation drawer with header and menu items. Used in `DrawerLayout`.

### XML Layout
```xml
<androidx.drawerlayout.widget.DrawerLayout
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Main content -->
    <FrameLayout
        android:id="@+id/content_frame"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Drawer -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/nav_header"
        app:menu="@menu/nav_menu"
        app:itemIconTint="?attr/colorPrimary"
        app:itemTextColor="?attr/colorOnSurface" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### `res/layout/nav_header.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="176dp"
    android:background="?attr/colorPrimary"
    android:orientation="vertical"
    android:padding="16dp"
    android:gravity="bottom">

    <ImageView
        android:layout_width="56dp"
        android:layout_height="56dp"
        android:src="@drawable/ic_avatar"
        android:layout_marginBottom="8dp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="John Doe"
        android:textColor="@color/white"
        android:textAppearance="?attr/textAppearanceSubtitle1" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="john@example.com"
        android:textColor="#B3FFFFFF"
        android:textAppearance="?attr/textAppearanceBody2" />
</LinearLayout>
```

### Java Example
```java
DrawerLayout drawerLayout = findViewById(R.id.drawer_layout);
NavigationView navView = findViewById(R.id.nav_view);

ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
    this, drawerLayout, toolbar,
    R.string.nav_open, R.string.nav_close);
drawerLayout.addDrawerListener(toggle);
toggle.syncState();

navView.setNavigationItemSelectedListener(item -> {
    int id = item.getItemId();
    if (id == R.id.nav_home) { /* load home */ }
    else if (id == R.id.nav_settings) { /* load settings */ }
    drawerLayout.closeDrawer(GravityCompat.START);
    return true;
});

// Programmatic open/close
drawerLayout.openDrawer(GravityCompat.START);
drawerLayout.closeDrawer(GravityCompat.START);
```

---

## 9. AppBarLayout

### Description
A `LinearLayout` that implements `AppBar` behavior for `CoordinatorLayout`. Manages scrolling, collapsing, and toolbar transitions.

### XML Layout
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/app_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/ThemeOverlay.MaterialComponents.Dark.ActionBar">

        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways"
            app:title="My App"
            app:menu="@menu/toolbar_menu" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Scroll Flags Reference
| Flag | Behavior |
|---|---|
| `scroll` | Scrolls off-screen with content |
| `enterAlways` | Re-enters when scrolling up |
| `enterAlwaysCollapsed` | Re-enters only when reaching top |
| `exitUntilCollapsed` | Collapses but stays visible |
| `snap` | Snaps to either fully shown or hidden |
| `snapMargins` | Includes margins in snap calculation |

---

## 10. CollapsingToolbarLayout

### Description
A toolbar that collapses as the user scrolls, transitioning from a large header to a compact toolbar.

### XML Layout
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/app_bar"
        android:layout_width="match_parent"
        android:layout_height="250dp">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:contentScrim="?attr/colorPrimary"
            app:title="Profile"
            app:titleEnabledAnimated="true"
            app:expandedTitleTextAppearance="@style/TextAppearance.App.Expanded"
            app:collapsedTitleTextAppearance="@style/TextAppearance.App.Collapsed">

            <ImageView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                android:src="@drawable/header_image"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.7" />

            <com.google.android.material.appbar.MaterialToolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        <!-- Scrollable content -->
    </androidx.core.widget.NestedScrollView>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### Java Example
```java
CollapsingToolbarLayout collapsingToolbar = findViewById(R.id.collapsing_toolbar);
collapsingToolbar.setTitle("John Doe");
collapsingToolbar.setExpandedTitleColor(Color.WHITE);
collapsingToolbar.setCollapsedTitleTextColor(Color.WHITE);
collapsingToolbar.setContentScrimColor(
    ContextCompat.getColor(this, R.color.md2_primary));
```

---

## 11. Snackbar

### Description
Lightweight feedback message at the bottom of the screen. Supports action buttons and custom duration.

### Java Example
```java
// Basic
Snackbar.make(view, "Message sent", Snackbar.LENGTH_SHORT).show();

// With action
Snackbar.make(view, "Item deleted", Snackbar.LENGTH_LONG)
    .setAction("UNDO", v -> { /* restore */ })
    .setActionTextColor(Color.YELLOW)
    .show();

// Custom duration
Snackbar snackbar = Snackbar.make(view, "Processing...", Snackbar.LENGTH_INDEFINITE);
snackbar.setAction("Cancel", v -> snackbar.dismiss());
snackbar.show();

// Custom background/text
Snackbar customSnack = Snackbar.make(view, "Custom snackbar", Snackbar.LENGTH_LONG);
View snackView = customSnack.getView();
snackView.setBackgroundColor(ContextCompat.getColor(this, R.color.md2_primary));
TextView textView = snackView.findViewById(com.google.android.material.R.id.snackbar_text);
textView.setTextColor(Color.WHITE);
customSnack.show();
```

### Kotlin Example
```kotlin
Snackbar.make(view, "Item deleted", Snackbar.LENGTH_LONG)
    .setAction("UNDO") { /* restore */ }
    .setActionTextColor(Color.YELLOW)
    .show()
```

---

## 12. MaterialAlertDialog

### Description
Material-styled dialog for alerts, confirmations, and user input. Replaces the native `AlertDialog`.

### Java Example
```java
// Confirmation dialog
new MaterialAlertDialogBuilder(this)
    .setTitle("Delete item?")
    .setMessage("This action cannot be undone.")
    .setIcon(R.drawable.ic_delete)
    .setNegativeButton("Cancel", (dialog, which) -> dialog.dismiss())
    .setPositiveButton("Delete", (dialog, which) -> { /* delete */ })
    .show();

// List dialog
String[] items = {"Option A", "Option B", "Option C"};
new MaterialAlertDialogBuilder(this)
    .setTitle("Choose option")
    .setItems(items, (dialog, which) -> {
        Toast.makeText(this, "Selected: " + items[which], Toast.LENGTH_SHORT).show();
    })
    .show();

// Single choice dialog
int[] selectedIndex = {0};
new MaterialAlertDialogBuilder(this)
    .setTitle("Sort by")
    .setSingleChoiceItems(items, 0, (dialog, which) -> selectedIndex[0] = which)
    .setPositiveButton("OK", (dialog, which) -> { /* apply */ })
    .setNegativeButton("Cancel", null)
    .show();

// Multi choice
boolean[] checked = {true, false, false};
new MaterialAlertDialogBuilder(this)
    .setTitle("Filters")
    .setMultiChoiceItems(items, checked, (dialog, which, isChecked) -> checked[which] = isChecked)
    .setPositiveButton("Apply", (dialog, which) -> { /* apply filters */ })
    .show();

// Custom view dialog
View customView = getLayoutInflater().inflate(R.layout.dialog_custom, null);
new MaterialAlertDialogBuilder(this)
    .setTitle("Custom Dialog")
    .setView(customView)
    .setPositiveButton("Submit", (dialog, which) -> { /* get values from customView */ })
    .show();
```

### Custom Dialog Style
```xml
<style name="AlertDialog.App" parent="ThemeOverlay.MaterialComponents.MaterialAlertDialog">
    <item name="materialAlertDialogTitleTextStyle">@style/MaterialAlertDialog.App.Title</item>
    <item name="buttonBarPositiveButtonStyle">@style/Widget.App.Button</item>
    <item name="shapeAppearanceMediumComponent">@style/ShapeAppearance.App.Dialog</item>
</style>

<style name="ShapeAppearance.App.Dialog" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">16dp</item>
</style>
```

---

## 13. Slider & RangeSlider

### Description
Material-styled seekbar supporting single-thumb (Slider) and dual-thumb (RangeSlider) for selecting values within a range.

### XML Layout
```xml
<!-- Slider -->
<com.google.android.material.slider.Slider
    android:id="@+id/slider"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:value="40"
    android:valueFrom="0"
    android:valueTo="100"
    android:stepSize="5"
    app:labelBehavior="floating"
    app:haloColor="?attr/colorPrimary" />

<!-- Range Slider -->
<com.google.android.material.slider.RangeSlider
    android:id="@+id/range_slider"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:valueFrom="0"
    android:valueTo="1000"
    app:values="@array/range_slider_initial"
    android:stepSize="10" />
```

### `res/values/arrays.xml`
```xml
<resources>
    <array name="range_slider_initial">
        <item>200</item>
        <item>800</item>
    </array>
</resources>
```

### Java Example
```java
Slider slider = findViewById(R.id.slider);
slider.addOnChangeListener((s, value, fromUser) -> {
    textView.setText("Value: " + (int) value);
});

RangeSlider rangeSlider = findViewById(R.id.range_slider);
rangeSlider.addOnChangeListener((s, value, fromUser) -> {
    List<Float> values = s.getValues();
    String range = "$" + values.get(0).intValue() + " - $" + values.get(1).intValue();
    textView.setText(range);
});

// Programmatic value setting
slider.setValue(65f);
rangeSlider.setValues(100f, 500f);
```

### Label Formatter
```java
slider.setLabelFormatter(value -> "$" + (int) value);
```

---

## 14. TabLayout

### Description
Horizontal tabs for navigating between sections. Usually paired with `ViewPager2`.

### XML Layout
```xml
<com.google.android.material.tabs.TabLayout
    android:id="@+id/tab_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabMode="fixed"
    app:tabGravity="fill"
    app:tabSelectedTextColor="?attr/colorPrimary"
    app:tabTextColor="?attr/colorOnSurface"
    app:tabIndicatorColor="?attr/colorPrimary"
    app:tabIndicatorFullWidth="false"
    app:tabIndicatorHeight="3dp"
    app:tabRippleColor="?attr/colorPrimary" />

<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/view_pager"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1" />
```

### Java Example
```java
ViewPager2 viewPager = findViewById(R.id.view_pager);
TabLayout tabLayout = findViewById(R.id.tab_layout);

String[] titles = {"Home", "Trending", "Following", "Library"};
viewPager.setAdapter(new FragmentStateAdapter(this) {
    @NonNull @Override
    public Fragment createFragment(int position) {
        return TabFragment.newInstance(position);
    }
    @Override public int getItemCount() { return titles.length; }
});

new TabLayoutMediator(tabLayout, viewPager, (tab, position) -> {
    tab.setText(titles[position]);
    tab.setIcon(getTabIcon(position));
}).attach();
```

### Tab with Icons
```java
tabLayout.getTabAt(0).setIcon(R.drawable.ic_home);
tabLayout.getTabAt(1).setIcon(R.drawable.ic_trending);
// or via TabLayoutMediator:
// tab.setIcon(R.drawable.selector_tab_icon);
```

### Tab Mode Options
| Mode | Description |
|---|---|
| `fixed` | Equal-width tabs, good for 2–4 items |
| `scrollable` | Scrollable tabs for many items |
| `auto` | Auto-selects based on screen width |

---

## 15. MaterialToolbar

### Description
An enhanced `Toolbar` implementing `AppBarLayout` integration and Material theming.

### XML Layout
```xml
<com.google.android.material.appbar.MaterialToolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    app:title="My App"
    app:subtitle="Subtitle text"
    app:navigationIcon="@drawable/ic_menu"
    app:menu="@menu/toolbar_menu"
    app:titleTextColor="@color/white"
    app:subtitleTextColor="#B3FFFFFF" />
```

### `res/menu/toolbar_menu.xml`
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item android:id="@+id/action_search"
        android:title="Search"
        android:icon="@drawable/ic_search"
        app:showAsAction="ifRoom" />
    <item android:id="@+id/action_settings"
        android:title="Settings"
        app:showAsAction="never" />
    <item android:id="@+id/action_about"
        android:title="About"
        app:showAsAction="never" />
</menu>
```

### Java Example
```java
MaterialToolbar toolbar = findViewById(R.id.toolbar);
setSupportActionBar(toolbar);
getSupportActionBar().setDisplayHomeAsUpEnabled(true);

toolbar.setOnMenuItemClickListener(item -> {
    if (item.getItemId() == R.id.action_search) {
        // handle search
        return true;
    }
    return false;
});

toolbar.setNavigationOnClickListener(v -> onBackPressed());
```

---

## 16. Progress Indicators

### Description
Circular and linear indicators for determinate (known progress) and indeterminate (unknown duration) loading states.

### XML Layout
```xml
<!-- Circular Indeterminate -->
<com.google.android.material.progressindicator.CircularProgressIndicator
    android:id="@+id/progress_circular"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:indicatorColor="?attr/colorPrimary"
    app:trackColor="#E0E0E0"
    app:indicatorSize="48dp"
    app:trackThickness="4dp" />

<!-- Circular Determinate -->
<com.google.android.material.progressindicator.CircularProgressIndicator
    android:id="@+id/progress_circular_det"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:progress="65"
    android:max="100"
    app:indicatorSize="64dp"
    app:trackThickness="6dp" />

<!-- Linear Indeterminate -->
<com.google.android.material.progressindicator.LinearProgressIndicator
    android:id="@+id/progress_linear"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:indicatorColor="?attr/colorPrimary"
    app:trackColor="#E0E0E0"
    app:trackThickness="4dp"
    app:indeterminate="true" />

<!-- Linear Determinate -->
<com.google.android.material.progressindicator.LinearProgressIndicator
    android:id="@+id/progress_linear_det"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:progress="40"
    android:max="100" />
```

### Java Example
```java
CircularProgressIndicator circular = findViewById(R.id.progress_circular_det);
circular.setProgress(75, true); // animated

LinearProgressIndicator linear = findViewById(R.id.progress_linear_det);
linear.setProgress(50, true);
linear.setVisibility(View.GONE);

// Show/hide with fade
circular.show();
circular.hide();
```

---

## 17. DatePicker & TimePicker

### Description
Material-styled dialogs for selecting dates and times. Supports single date, range, and time modes.

### Java Example
```java
// Date Picker (Material)
MaterialDatePicker<Long> datePicker = MaterialDatePicker.Builder.datePicker()
    .setTitleText("Select date")
    .setSelection(MaterialDatePicker.todayInUtcMilliseconds())
    .build();

datePicker.show(getSupportFragmentManager(), "DATE_PICKER");

datePicker.addOnPositiveButtonClickListener(selection -> {
    SimpleDateFormat sdf = new SimpleDateFormat("MMM dd, yyyy", Locale.getDefault());
    String date = sdf.format(new Date(selection));
    textView.setText(date);
});

// Date Range Picker
MaterialDatePicker<Pair<Long, Long>> rangePicker =
    MaterialDatePicker.Builder.dateRangePicker()
        .setTitleText("Select date range")
        .build();

rangePicker.show(getSupportFragmentManager(), "RANGE_PICKER");
rangePicker.addOnPositiveButtonClickListener(selection -> {
    Long start = selection.first;
    Long end = selection.second;
    // use start and end
});

// Time Picker
MaterialTimePicker timePicker = new MaterialTimePicker.Builder()
    .setTimeFormat(TimeFormat.CLOCK_12H)
    .setHour(12)
    .setMinute(0)
    .setTitleText("Select appointment time")
    .build();

timePicker.show(getSupportFragmentManager(), "TIME_PICKER");
timePicker.addOnPositiveButtonClickListener(v -> {
    int hour = timePicker.getHour();
    int minute = timePicker.getMinute();
    String time = String.format(Locale.getDefault(), "%02d:%02d", hour, minute);
    textView.setText(time);
});
```

### Kotlin Example
```kotlin
val datePicker = MaterialDatePicker.Builder.datePicker()
    .setTitleText("Select date")
    .setSelection(MaterialDatePicker.todayInUtcMilliseconds())
    .build()
datePicker.show(supportFragmentManager, "DATE_PICKER")
datePicker.addOnPositiveButtonClickListener { selection ->
    val sdf = SimpleDateFormat("MMM dd, yyyy", Locale.getDefault())
    textView.text = sdf.format(Date(selection))
}
```

---

## 18. MaterialDivider

### Description
A thin horizontal or vertical separator line between list items or sections.

### XML Layout
```xml
<!-- Horizontal divider -->
<com.google.android.material.divider.MaterialDivider
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:dividerColor="?attr/colorOutline"
    app:dividerThickness="1dp"
    app:dividerInsetStart="16dp"
    app:dividerInsetEnd="16dp" />

<!-- Vertical divider -->
<com.google.android.material.divider.MaterialDivider
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    app:dividerColor="?attr/colorOutline"
    app:dividerThickness="1dp" />
```

---

## 19. BottomSheetDialog

### Description
A modal bottom sheet that slides up from the bottom. Used for contextual actions or mini-forms.

### Java Example
```java
BottomSheetDialog dialog = new BottomSheetDialog(this);
View view = getLayoutInflater().inflate(R.layout.bottom_sheet_layout, null);
dialog.setContentView(view);

// Access BottomSheetBehavior
BottomSheetBehavior<FrameLayout> behavior = dialog.getBehavior();
behavior.setPeekHeight(400);
behavior.setState(BottomSheetBehavior.STATE_EXPANDED);
behavior.setDraggable(true);

dialog.show();
```

### Persistent BottomSheet (not dialog)
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Main content -->
    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Bottom Sheet -->
    <LinearLayout
        android:id="@+id/bottom_sheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:background="@drawable/bg_bottom_sheet"
        app:layout_behavior="com.google.android.material.bottomsheet.BottomSheetBehavior"
        app:behavior_peekHeight="80dp"
        app:behavior_hideable="false">
        <!-- content -->
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

---

## 20. MaterialBanner

### Description
A persistent message that appears below the top app bar. Used for important but non-blocking information.

### Java Example
```java
new MaterialBanner(this,
    "No internet connection. Changes won't be saved.",
    R.drawable.ic_wifi_off,
    "Dismiss",
    (dialog, which) -> { /* dismiss */ })
    .show();
```

---

## Themes & Colors Reference

### Complete `themes.xml` for MD2
```xml
<resources>
    <style name="Theme.App.MD2" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
        <item name="colorPrimary">@color/md2_primary</item>
        <item name="colorPrimaryVariant">@color/md2_primary_variant</item>
        <item name="colorOnPrimary">@color/white</item>
        <item name="colorSecondary">@color/md2_secondary</item>
        <item name="colorSecondaryVariant">@color/md2_secondary_variant</item>
        <item name="colorOnSecondary">@color/black</item>
        <item name="colorSurface">@color/md2_surface</item>
        <item name="colorOnSurface">@color/md2_on_surface</item>
        <item name="colorError">@color/md2_error</item>
        <item name="colorOnError">@color/white</item>
        <item name="colorBackground">@color/md2_background</item>
        <item name="colorOnBackground">@color/black</item>
        <item name="android:statusBarColor">?attr/colorPrimaryVariant</item>
        <item name="android:navigationBarColor">?attr/colorSurface</item>
        <item name="shapeAppearanceSmallComponent">@style/ShapeAppearance.App.SmallComponent</item>
        <item name="shapeAppearanceMediumComponent">@style/ShapeAppearance.App.MediumComponent</item>
        <item name="shapeAppearanceLargeComponent">@style/ShapeAppearance.App.LargeComponent</item>
    </style>

    <style name="ShapeAppearance.App.SmallComponent" parent="ShapeAppearance.MaterialComponents.SmallComponent">
        <item name="cornerFamily">rounded</item>
        <item name="cornerSize">4dp</item>
    </style>

    <style name="ShapeAppearance.App.MediumComponent" parent="ShapeAppearance.MaterialComponents.MediumComponent">
        <item name="cornerFamily">rounded</item>
        <item name="cornerSize">12dp</item>
    </style>

    <style name="ShapeAppearance.App.LargeComponent" parent="ShapeAppearance.MaterialComponents.LargeComponent">
        <item name="cornerFamily">rounded</item>
        <item name="cornerSize">16dp</item>
    </style>
</resources>
```

### Android Version Compatibility
| Component | Min API |
|---|---|
| MaterialButton | API 14 |
| MaterialCardView | API 14 |
| TextInputLayout | API 14 |
| BottomNavigationView | API 14 |
| CollapsingToolbarLayout | API 14 |
| MaterialDatePicker | API 16 |
| MaterialTimePicker | API 16 |
| Slider / RangeSlider | API 16 |
| CircularProgressIndicator | API 14 |

> All MDC components require `Theme.MaterialComponents.*` parent in your theme.
