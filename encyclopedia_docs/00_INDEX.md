# Android UI/UX Component Encyclopedia
### Complete Implementation Reference for Modern Android Development

> **Version:** 2025 Edition | **API Level:** 21–35+ | **Material:** MD2 + MD3

---

## How to Use This Encyclopedia

This encyclopedia is split into **18 focused modules** for fast lookup. Each module is a standalone `.md` file with complete XML, Kotlin, and Java examples.

| Symbol | Meaning |
|--------|---------|
| 🟢 | Material 3 (recommended) |
| 🟡 | Material 2 (legacy, still valid) |
| 🔵 | AndroidX / Jetpack |
| ⚠️ | Deprecated or avoid |
| ✅ | Best practice |
| 🧪 | Experimental / Beta |

---

## Table of Contents

| # | Module | File | Components Covered |
|---|--------|------|--------------------|
| 01 | **Material Design 2 Components** | `01_material2.md` | Buttons, Cards, TextInput, Chips, FAB, BottomNav, AppBar, Snackbar, Dialogs, Sliders, Tabs, Progress, Pickers |
| 02 | **Material Design 3 Components** | `02_material3.md` | M3 Buttons, SearchBar, MaterialSwitch, Segmented Buttons, Side Sheets, Nav Rail, Dynamic Color, M3 Dialogs, M3 Menus |
| 03 | **AndroidX Components** | `03_androidx.md` | RecyclerView, ViewPager2, SwipeRefreshLayout, NestedScrollView, ConstraintLayout, MotionLayout, CoordinatorLayout, DrawerLayout |
| 04 | **Modern List Structures** | `04_lists.md` | Grid, Staggered, Horizontal, Nested, Expandable, Sectioned, Sticky Headers, Infinite Scroll, Chat Lists, Timeline |
| 05–10 | **Cards, Navigation, Search, Inputs, Selection, Media** | `05_to_10_cards_nav_search_inputs_selection_media.md` | Elevated/Outlined/Expandable/Tinder/Glassmorphism/Neumorphism/Dashboard Cards, **Carousel** ✅; Bottom Nav/Rail/Drawer/Bottom Sheet/Gesture Nav, **Tabs, Multi-Pane, Master-Detail, Tablet Adaptive Nav** ✅; SearchBar+SearchView+Filter, **Floating Search, Voice Search, Expandable Search, AI Search** ✅; Text Fields/OTP/PIN/Password/Dropdown/AutoComplete/Tags, **Rich Text Editor, Emoji Picker** ✅; Switch/Checkbox/Radio/Date Picker, **Segmented Controls, Toggle Groups, Time Selector, Chips Selection** ✅; ShapeableImageView/Stories/Gallery/ExoPlayer, **Avatar System, Audio Player, Infinite Carousel** ✅ |
| 11–18 | **Effects, Animations, Loading, Dashboard, Settings, Auth, AI, Patterns** | `11_to_18_effects_animations_loading_dashboard_settings_auth_ai_patterns.md` | Blur/Glass/Gradients/Parallax/Shadows, **Neumorphism, Particles, Glow, Dynamic Color** ✅; MotionLayout/Shared Elements/Lottie/Spring/Page Transforms, **Physics Fling+Spring** ✅; Shimmer/Skeleton, **Paging 3, Lazy Loading, Pull-to-Refresh** ✅; KPI Cards/Charts, **Statistic Widgets, Timeline Panel, Finance Dashboard, Line+Bar+Pie Graphs** ✅; Google Style Settings, **One UI Style, Account Page, Security Page, Appearance Page, AndroidX Preference Screen** ✅; Login/OTP/Biometric, **Sign Up Screen, Passkey/FIDO2, Face Auth, MFA UI** ✅; Chat UI/Streaming/Typing Indicator, **AI Input Area (Voice+File+Text)** ✅; Banking/Social/E-Commerce/Streaming, **Pixel App Pattern, Samsung One UI Pattern, Messaging Chat Pattern** ✅ |
| 19–23 | **Drawables, Accessibility, Architecture, Dimens, Libraries** | `19_to_23_drawables_accessibility_architecture_dimens_libraries.md` | Shape Drawables, Selectors, Canvas API, ShapeAppearance, Accessibility, MVVM, Navigation, dimens.xml, Typography, Extensions, Library Alternatives, Full Gradle deps |
| 24–27 | **Advanced Patterns, Foldables, Performance, Cheat Sheets** | `24_to_27_advanced_foldable_performance_cheatsheets.md` | Collapsing Profile, Parallax Detail, Wizard Forms, Drag & Drop, Foldable APIs, Window Size Classes, RecyclerView performance, Memory leaks, 10 Quick-Reference Cheat Sheets, Component Decision Tree |

---

## Quick Reference: Dependencies

```gradle
// build.gradle (app level)

// Material Design 3
implementation 'com.google.android.material:material:1.12.0'

// AndroidX Core
implementation 'androidx.core:core-ktx:1.13.1'
implementation 'androidx.appcompat:appcompat:1.7.0'
implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
implementation 'androidx.recyclerview:recyclerview:1.3.2'
implementation 'androidx.viewpager2:viewpager2:1.1.0'
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'

// Navigation Component
implementation 'androidx.navigation:navigation-fragment-ktx:2.7.7'
implementation 'androidx.navigation:navigation-ui-ktx:2.7.7'

// Lifecycle
implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.8.2'
implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.8.2'

// Lottie Animations
implementation 'com.airbnb.android:lottie:6.4.0'

// Shimmer (Facebook)
implementation 'com.facebook.shimmer:shimmer:0.5.0'

// Glide (Image Loading)
implementation 'com.github.bumptech.glide:glide:4.16.0'

// Coil (Kotlin-first image loading)
implementation 'io.coil-kt:coil:2.6.0'

// FlexboxLayout
implementation 'com.google.android.flexbox:flexbox:3.0.0'

// ViewBinding
buildFeatures { viewBinding true }
```

---

## Quick Reference: Base Theme Setup

```xml
<!-- res/values/themes.xml — Material 3 Base -->
<style name="AppTheme" parent="Theme.Material3.DayNight.NoActionBar">
    <item name="colorPrimary">@color/md_theme_primary</item>
    <item name="colorOnPrimary">@color/md_theme_onPrimary</item>
    <item name="colorPrimaryContainer">@color/md_theme_primaryContainer</item>
    <item name="colorOnPrimaryContainer">@color/md_theme_onPrimaryContainer</item>
    <item name="colorSecondary">@color/md_theme_secondary</item>
    <item name="colorTertiary">@color/md_theme_tertiary</item>
    <item name="colorSurface">@color/md_theme_surface</item>
    <item name="colorOnSurface">@color/md_theme_onSurface</item>
    <item name="colorSurfaceVariant">@color/md_theme_surfaceVariant</item>
    <item name="colorError">@color/md_theme_error</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    <item name="android:navigationBarColor">@android:color/transparent</item>
    <item name="android:windowLightStatusBar">true</item>
</style>
```

---

## Quick Reference: Color System

```xml
<!-- res/values/colors.xml — Material 3 Color Tokens -->
<resources>
    <!-- Primary -->
    <color name="md_theme_primary">#6750A4</color>
    <color name="md_theme_onPrimary">#FFFFFF</color>
    <color name="md_theme_primaryContainer">#EADDFF</color>
    <color name="md_theme_onPrimaryContainer">#21005D</color>

    <!-- Secondary -->
    <color name="md_theme_secondary">#625B71</color>
    <color name="md_theme_onSecondary">#FFFFFF</color>
    <color name="md_theme_secondaryContainer">#E8DEF8</color>

    <!-- Tertiary -->
    <color name="md_theme_tertiary">#7D5260</color>
    <color name="md_theme_onTertiary">#FFFFFF</color>
    <color name="md_theme_tertiaryContainer">#FFD8E4</color>

    <!-- Error -->
    <color name="md_theme_error">#B3261E</color>
    <color name="md_theme_onError">#FFFFFF</color>
    <color name="md_theme_errorContainer">#F9DEDC</color>

    <!-- Surface -->
    <color name="md_theme_surface">#FFFBFE</color>
    <color name="md_theme_onSurface">#1C1B1F</color>
    <color name="md_theme_surfaceVariant">#E7E0EC</color>
    <color name="md_theme_onSurfaceVariant">#49454F</color>
    <color name="md_theme_outline">#79747E</color>
    <color name="md_theme_outlineVariant">#CAC4D0</color>

    <!-- Background -->
    <color name="md_theme_background">#FFFBFE</color>
    <color name="md_theme_onBackground">#1C1B1F</color>

    <!-- Inverse -->
    <color name="md_theme_inverseSurface">#313033</color>
    <color name="md_theme_inverseOnSurface">#F4EFF4</color>
    <color name="md_theme_inversePrimary">#D0BCFF</color>
</resources>
```

---

## Quick Reference: Typography

```xml
<!-- res/values/type.xml — Material 3 Type Scale -->
<style name="TextAppearance.App.DisplayLarge" parent="TextAppearance.Material3.DisplayLarge">
    <item name="fontFamily">@font/roboto</item>
</style>
<!-- Scale reference:
    DisplayLarge:   57sp / Regular
    DisplayMedium:  45sp / Regular
    DisplaySmall:   36sp / Regular
    HeadlineLarge:  32sp / Regular
    HeadlineMedium: 28sp / Regular
    HeadlineSmall:  24sp / Regular
    TitleLarge:     22sp / Regular
    TitleMedium:    16sp / Medium
    TitleSmall:     14sp / Medium
    BodyLarge:      16sp / Regular
    BodyMedium:     14sp / Regular
    BodySmall:      12sp / Regular
    LabelLarge:     14sp / Medium
    LabelMedium:    12sp / Medium
    LabelSmall:     11sp / Medium
-->
```

---

## Android Version Compatibility Overview

| Feature | Min API | Notes |
|---------|---------|-------|
| Material 3 | 21 | Via MDC-Android library |
| Dynamic Color (Material You) | 31 | Android 12+ only |
| Predictive Back Gesture | 33 | Android 13+ |
| Splash Screen API | 31 | AndroidX backport to 23 |
| WindowInsets (edge-to-edge) | 21 | Jetpack WindowManager |
| ConstraintLayout | 14 | Via AndroidX |
| Navigation Component | 14 | Via AndroidX |
| ViewBinding | 14 | Via build config |
| DataBinding | 14 | Via build config |
| Compose | 21 | Separate system |
| BlurMaskFilter (RenderEffect) | 31 | Android 12+ native blur |
| PictureInPicture | 26 | Android 8+ |
| Foldable APIs | 30 | Via Jetpack WindowManager |
