# Navigation Systems — Android UI Encyclopedia

> **File:** `06_navigation_systems.md` | **API Target:** 35

---

## Table of Contents

1. [Bottom Navigation](#1-bottom-navigation)
2. [Navigation Rail](#2-navigation-rail)
3. [Navigation Drawer](#3-navigation-drawer)
4. [Tab Navigation](#4-tab-navigation)
5. [Bottom Sheet Navigation](#5-bottom-sheet-navigation)
6. [Gesture Navigation](#6-gesture-navigation)
7. [Multi-Pane Navigation](#7-multi-pane-navigation)
8. [Foldable Navigation](#8-foldable-navigation)
9. [Master-Detail Layout](#9-master-detail-layout)
10. [Top Navigation Bar](#10-top-navigation-bar)
11. [Mega Navigation Drawer](#11-mega-navigation-drawer)
12. [Jetpack Navigation Component](#12-jetpack-navigation-component)

---

## 1. Bottom Navigation

### Description
3–5 destination tabs at the bottom of the screen. The standard navigation pattern for primary app sections.

### `res/layout/activity_main.xml`
```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:title="App Name" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="80dp"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_nav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        app:menu="@menu/bottom_nav_menu"
        app:labelVisibilityMode="labeled"
        style="@style/Widget.Material3.BottomNavigationView" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### `res/menu/bottom_nav_menu.xml`
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/nav_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item android:id="@+id/nav_explore"
        android:icon="@drawable/ic_explore"
        android:title="Explore" />
    <item android:id="@+id/nav_notifications"
        android:icon="@drawable/ic_notifications"
        android:title="Alerts" />
    <item android:id="@+id/nav_profile"
        android:icon="@drawable/ic_person"
        android:title="Profile" />
</menu>
```

### Java — Full Navigation Setup
```java
public class MainActivity extends AppCompatActivity {
    private BottomNavigationView bottomNav;
    private Fragment currentFragment;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        bottomNav = findViewById(R.id.bottom_nav);
        setupNavigation();

        if (savedInstanceState == null) {
            loadFragment(new HomeFragment(), R.id.nav_home);
        }
    }

    private void setupNavigation() {
        bottomNav.setOnItemSelectedListener(item -> {
            int id = item.getItemId();
            if (id == R.id.nav_home) return loadFragment(new HomeFragment(), id);
            if (id == R.id.nav_explore) return loadFragment(new ExploreFragment(), id);
            if (id == R.id.nav_notifications) return loadFragment(new NotificationsFragment(), id);
            if (id == R.id.nav_profile) return loadFragment(new ProfileFragment(), id);
            return false;
        });

        bottomNav.setOnItemReselectedListener(item -> {
            // Scroll to top of current fragment's list
            if (currentFragment instanceof HomeFragment) {
                ((HomeFragment) currentFragment).scrollToTop();
            }
        });
    }

    private boolean loadFragment(Fragment fragment, int navId) {
        currentFragment = fragment;
        getSupportFragmentManager()
            .beginTransaction()
            .setReorderingAllowed(true)
            .replace(R.id.nav_host_fragment, fragment, String.valueOf(navId))
            .commit();
        return true;
    }

    // Badge management
    public void showNotificationBadge(int count) {
        BadgeDrawable badge = bottomNav.getOrCreateBadge(R.id.nav_notifications);
        badge.setNumber(count);
        badge.setVisible(true);
    }

    public void clearNotificationBadge() {
        bottomNav.removeBadge(R.id.nav_notifications);
    }
}
```

### BottomNav + Jetpack Navigation (Java)
```java
// Navigation graph approach
NavController navController = Navigation.findNavController(this, R.id.nav_host_fragment);
NavigationUI.setupWithNavController(bottomNav, navController);

// Hide bottom nav on specific destinations
navController.addOnDestinationChangedListener((controller, destination, arguments) -> {
    if (destination.getId() == R.id.detailFragment
            || destination.getId() == R.id.settingsFragment) {
        bottomNav.setVisibility(View.GONE);
    } else {
        bottomNav.setVisibility(View.VISIBLE);
    }
});
```

---

## 2. Navigation Rail

### Description
Vertical navigation for medium-width screens (600dp+). Items listed vertically on the left edge.

### `res/layout/activity_rail.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
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
        style="@style/Widget.Material3.NavigationRailView" />

    <View
        android:layout_width="1dp"
        android:layout_height="match_parent"
        android:background="?attr/colorOutlineVariant" />

    <androidx.fragment.app.FragmentContainerView
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
    android:padding="12dp">

    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/rail_logo"
        android:layout_width="32dp"
        android:layout_height="32dp"
        android:src="@drawable/ic_logo"
        android:layout_marginBottom="8dp" />

    <com.google.android.material.floatingactionbutton.ExtendedFloatingActionButton
        android:id="@+id/rail_fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="New"
        app:icon="@drawable/ic_edit"
        app:shrinkActionItem="true" />
</LinearLayout>
```

### Java — Adaptive Navigation
```java
public class AdaptiveActivity extends AppCompatActivity {
    private BottomNavigationView bottomNav;
    private NavigationRailView navRail;
    private boolean isWideScreen;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_adaptive);

        bottomNav = findViewById(R.id.bottom_nav);
        navRail = findViewById(R.id.nav_rail);

        isWideScreen = isWideLayout();
        setupAdaptiveNav();
    }

    private boolean isWideLayout() {
        Configuration config = getResources().getConfiguration();
        return config.screenWidthDp >= 600;
    }

    private void setupAdaptiveNav() {
        if (isWideScreen) {
            bottomNav.setVisibility(View.GONE);
            navRail.setVisibility(View.VISIBLE);
            navRail.setOnItemSelectedListener(this::handleNavigation);
        } else {
            navRail.setVisibility(View.GONE);
            bottomNav.setVisibility(View.VISIBLE);
            bottomNav.setOnItemSelectedListener(this::handleNavigation);
        }
    }

    private boolean handleNavigation(MenuItem item) {
        int id = item.getItemId();
        if (id == R.id.nav_home) { loadFragment(new HomeFragment()); return true; }
        if (id == R.id.nav_explore) { loadFragment(new ExploreFragment()); return true; }
        return false;
    }

    private void loadFragment(Fragment f) {
        getSupportFragmentManager().beginTransaction()
            .replace(R.id.rail_content, f).commit();
    }

    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        boolean newWide = newConfig.screenWidthDp >= 600;
        if (newWide != isWideScreen) {
            isWideScreen = newWide;
            setupAdaptiveNav();
        }
    }
}
```

---

## 3. Navigation Drawer

### Description
A side panel that slides in from the left (start) edge. Contains navigation links and account info.

### `res/layout/activity_drawer.xml`
```xml
<androidx.drawerlayout.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <!-- Content -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <com.google.android.material.appbar.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <com.google.android.material.appbar.MaterialToolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:navigationIcon="@drawable/ic_menu" />
        </com.google.android.material.appbar.AppBarLayout>

        <androidx.fragment.app.FragmentContainerView
            android:id="@+id/content_frame"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>

    <!-- Drawer -->
    <com.google.android.material.navigation.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/nav_drawer_header"
        app:menu="@menu/nav_drawer_menu" />
</androidx.drawerlayout.widget.DrawerLayout>
```

### `res/menu/nav_drawer_menu.xml`
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item android:id="@+id/nav_home"
            android:icon="@drawable/ic_home"
            android:title="Home" />
        <item android:id="@+id/nav_dashboard"
            android:icon="@drawable/ic_dashboard"
            android:title="Dashboard" />
        <item android:id="@+id/nav_analytics"
            android:icon="@drawable/ic_analytics"
            android:title="Analytics" />
    </group>
    <item android:title="Account">
        <menu>
            <item android:id="@+id/nav_profile"
                android:icon="@drawable/ic_person"
                android:title="Profile" />
            <item android:id="@+id/nav_settings"
                android:icon="@drawable/ic_settings"
                android:title="Settings" />
            <item android:id="@+id/nav_logout"
                android:icon="@drawable/ic_logout"
                android:title="Logout" />
        </menu>
    </item>
</menu>
```

### Java — Full Drawer Setup
```java
DrawerLayout drawerLayout = findViewById(R.id.drawer_layout);
NavigationView navView = findViewById(R.id.nav_view);
MaterialToolbar toolbar = findViewById(R.id.toolbar);

setSupportActionBar(toolbar);

ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
    this, drawerLayout, toolbar,
    R.string.nav_drawer_open, R.string.nav_drawer_close);
drawerLayout.addDrawerListener(toggle);
toggle.syncState();

navView.setNavigationItemSelectedListener(item -> {
    int id = item.getItemId();
    if (id == R.id.nav_home) loadFragment(new HomeFragment());
    else if (id == R.id.nav_settings) {
        startActivity(new Intent(this, SettingsActivity.class));
    } else if (id == R.id.nav_logout) {
        showLogoutDialog();
    }
    item.setChecked(true);
    drawerLayout.closeDrawer(GravityCompat.START);
    return true;
});

// Set default checked item
navView.setCheckedItem(R.id.nav_home);

// Update header
View header = navView.getHeaderView(0);
TextView tvName = header.findViewById(R.id.tv_header_name);
TextView tvEmail = header.findViewById(R.id.tv_header_email);
ShapeableImageView avatar = header.findViewById(R.id.img_header_avatar);
tvName.setText("John Doe");
tvEmail.setText("john@example.com");

@Override
public void onBackPressed() {
    if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
        drawerLayout.closeDrawer(GravityCompat.START);
    } else {
        super.onBackPressed();
    }
}
```

---

## 4. Tab Navigation

### Description
Horizontal tabs for switching between related sections of the same screen.

### `res/layout/activity_tabs.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:title="Sections" />

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tab_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:tabMode="fixed"
            app:tabGravity="fill"
            style="@style/Widget.Material3.TabLayout" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/view_pager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

### Java — Full TabLayout + ViewPager2 Setup
```java
public class TabsActivity extends AppCompatActivity {

    private static final String[] TAB_TITLES = {"Feed", "Trending", "Following", "Library"};
    private static final int[] TAB_ICONS = {
        R.drawable.ic_home, R.drawable.ic_trending,
        R.drawable.ic_group, R.drawable.ic_library
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tabs);

        TabLayout tabLayout = findViewById(R.id.tab_layout);
        ViewPager2 viewPager = findViewById(R.id.view_pager);

        viewPager.setAdapter(new FragmentStateAdapter(this) {
            @NonNull @Override
            public Fragment createFragment(int pos) {
                return ContentFragment.newInstance(TAB_TITLES[pos]);
            }
            @Override public int getItemCount() { return TAB_TITLES.length; }
        });

        viewPager.setOffscreenPageLimit(1);

        new TabLayoutMediator(tabLayout, viewPager, (tab, position) -> {
            tab.setText(TAB_TITLES[position]);
            tab.setIcon(TAB_ICONS[position]);
        }).attach();
    }
}
```

### Scrollable Tabs (many tabs)
```xml
<com.google.android.material.tabs.TabLayout
    android:id="@+id/tab_layout_scroll"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabMode="scrollable"
    app:tabGravity="start"
    app:tabIndicatorFullWidth="false"
    app:tabIndicatorHeight="3dp"
    app:tabMinWidth="80dp" />
```

### Custom Tab View (Java)
```java
for (int i = 0; i < TAB_TITLES.length; i++) {
    TabLayout.Tab tab = tabLayout.getTabAt(i);
    if (tab != null) {
        View customTab = LayoutInflater.from(this)
            .inflate(R.layout.custom_tab, null);
        ((ImageView) customTab.findViewById(R.id.tab_icon))
            .setImageResource(TAB_ICONS[i]);
        ((TextView) customTab.findViewById(R.id.tab_text))
            .setText(TAB_TITLES[i]);
        tab.setCustomView(customTab);
    }
}
```

---

## 5. Bottom Sheet Navigation

### Description
Navigation panel that slides up from the bottom. Used for contextual menus, action lists, and secondary navigation.

### Java — Navigation Bottom Sheet
```java
public class NavBottomSheet extends BottomSheetDialogFragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        return inflater.inflate(R.layout.sheet_navigation, container, false);
    }

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        // Set full-height if needed
        BottomSheetBehavior<View> behavior = BottomSheetBehavior.from((View) view.getParent());
        behavior.setState(BottomSheetBehavior.STATE_EXPANDED);
        behavior.setSkipCollapsed(true);

        view.findViewById(R.id.nav_sheet_home).setOnClickListener(v -> {
            requireActivity().getSupportFragmentManager()
                .beginTransaction()
                .replace(R.id.container, new HomeFragment())
                .commit();
            dismiss();
        });

        view.findViewById(R.id.nav_sheet_settings).setOnClickListener(v -> {
            startActivity(new Intent(requireContext(), SettingsActivity.class));
            dismiss();
        });
    }
}
```

### `res/layout/sheet_navigation.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:background="?attr/colorSurface"
    android:paddingBottom="24dp">

    <com.google.android.material.bottomsheet.BottomSheetDragHandleView
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Navigate to"
        android:textAppearance="?attr/textAppearanceTitleMedium"
        android:paddingHorizontal="24dp"
        android:paddingBottom="8dp" />

    <LinearLayout android:id="@+id/nav_sheet_home"
        android:layout_width="match_parent"
        android:layout_height="56dp"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:paddingHorizontal="24dp"
        android:clickable="true" android:focusable="true"
        android:background="?attr/selectableItemBackground">
        <ImageView android:layout_width="24dp" android:layout_height="24dp"
            android:src="@drawable/ic_home" android:layout_marginEnd="16dp" />
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Home" android:textAppearance="?attr/textAppearanceBodyLarge" />
    </LinearLayout>

    <LinearLayout android:id="@+id/nav_sheet_settings"
        android:layout_width="match_parent"
        android:layout_height="56dp"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:paddingHorizontal="24dp"
        android:clickable="true" android:focusable="true"
        android:background="?attr/selectableItemBackground">
        <ImageView android:layout_width="24dp" android:layout_height="24dp"
            android:src="@drawable/ic_settings" android:layout_marginEnd="16dp" />
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Settings" android:textAppearance="?attr/textAppearanceBodyLarge" />
    </LinearLayout>
</LinearLayout>
```

---

## 6. Gesture Navigation

### Description
Android 10+ system gesture navigation (back swipe, home swipe). Apps should handle edge-to-edge and insets correctly.

### `res/values/themes.xml` additions
```xml
<style name="Theme.App" parent="Theme.Material3.DayNight.NoActionBar">
    <!-- Edge-to-edge -->
    <item name="android:windowLayoutInDisplayCutoutMode">shortEdges</item>
</style>
```

### Java — Edge-to-edge + WindowInsets
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // Enable edge-to-edge
    WindowCompat.setDecorFitsSystemWindows(getWindow(), false);

    setContentView(R.layout.activity_main);

    // Handle insets
    ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main_layout),
        (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            Insets displayCutout = insets.getInsets(
                WindowInsetsCompat.Type.displayCutout());
            v.setPadding(
                Math.max(systemBars.left, displayCutout.left),
                systemBars.top,
                Math.max(systemBars.right, displayCutout.right),
                systemBars.bottom);
            return WindowInsetsCompat.CONSUMED;
        });
}
```

### Kotlin — Edge-to-edge
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    WindowCompat.setDecorFitsSystemWindows(window, false)
    setContentView(R.layout.activity_main)

    ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main_layout)) { v, insets ->
        val bars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
        v.setPadding(bars.left, bars.top, bars.right, bars.bottom)
        WindowInsetsCompat.CONSUMED
    }
}
```

### Back gesture handling (Predictive Back — API 33+)
```java
// Register predictive back callback
getOnBackPressedDispatcher().addCallback(this, new OnBackPressedCallback(true) {
    @Override
    public void handleOnBackPressed() {
        if (drawerLayout.isDrawerOpen(GravityCompat.START)) {
            drawerLayout.closeDrawer(GravityCompat.START);
        } else if (getSupportFragmentManager().getBackStackEntryCount() > 0) {
            getSupportFragmentManager().popBackStack();
        } else {
            setEnabled(false);
            getOnBackPressedDispatcher().onBackPressed();
        }
    }
});
```

---

## 7. Multi-Pane Navigation

### Description
Two panels side-by-side on large screens. Left pane = list, right pane = detail.

### `res/layout/activity_multi_pane.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/multi_pane_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <!-- List pane -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/list_pane"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:name="com.example.app.ListFragment" />

    <!-- Divider -->
    <com.google.android.material.divider.MaterialDivider
        android:layout_width="1dp"
        android:layout_height="match_parent" />

    <!-- Detail pane (hidden on narrow screens) -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/detail_pane"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="2"
        android:visibility="gone" />
</LinearLayout>
```

### Java — Multi-Pane Logic
```java
boolean isWide = getResources().getConfiguration().screenWidthDp >= 600;

if (isWide) {
    // Show detail pane
    findViewById(R.id.detail_pane).setVisibility(View.VISIBLE);
    // When list item clicked, load in detail pane
    listFragment.setOnItemClickListener(item -> {
        getSupportFragmentManager().beginTransaction()
            .replace(R.id.detail_pane, DetailFragment.newInstance(item.getId()))
            .commit();
    });
} else {
    // Navigate to detail as new screen
    listFragment.setOnItemClickListener(item -> {
        getSupportFragmentManager().beginTransaction()
            .replace(R.id.list_pane, DetailFragment.newInstance(item.getId()))
            .addToBackStack(null)
            .commit();
    });
}
```

---

## 8. Foldable Navigation

### Description
Adapts layout for foldable phones in half-open (table-top) mode or book mode.

### Dependency
```groovy
implementation 'androidx.window:window:1.3.0'
```

### Java — Foldable Layout Handling
```java
private WindowInfoTracker windowInfoTracker;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    windowInfoTracker = WindowInfoTracker.getOrCreate(this);

    lifecycleScope.launch(() ->
        windowInfoTracker.windowLayoutInfo(this).collect(info -> {
            for (DisplayFeature feature : info.getDisplayFeatures()) {
                if (feature instanceof FoldingFeature) {
                    FoldingFeature fold = (FoldingFeature) feature;
                    handleFoldingFeature(fold);
                }
            }
        })
    );
}

private void handleFoldingFeature(FoldingFeature fold) {
    FoldingFeature.State state = fold.getState();
    FoldingFeature.Orientation orientation = fold.getOrientation();

    if (state == FoldingFeature.State.FLAT) {
        // Fully open — show wide layout
        showWideLayout();
    } else if (state == FoldingFeature.State.HALF_OPENED) {
        if (orientation == FoldingFeature.Orientation.HORIZONTAL) {
            // Table-top mode — show content above fold, controls below
            showTableTopLayout(fold.getBounds());
        } else {
            // Book mode — show two side-by-side panels
            showBookLayout();
        }
    }
}
```

---

## 9. Master-Detail Layout

### Description
Classic two-pane pattern: list of items on the left, selected item detail on the right.

### Java — SlidingPaneLayout
```java
SlidingPaneLayout slidingPane = findViewById(R.id.sliding_pane_layout);
ListDetailPaneScaffold scaffold = null; // For Jetpack Compose; for views use SlidingPaneLayout

// SlidingPaneLayout approach
slidingPane.addPanelSlideListener(new SlidingPaneLayout.PanelSlideListener() {
    @Override public void onPanelSlide(@NonNull View panel, float slideOffset) {}
    @Override public void onPanelOpened(@NonNull View panel) {
        // Detail panel opened — update back button
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
    }
    @Override public void onPanelClosed(@NonNull View panel) {
        getSupportActionBar().setDisplayHomeAsUpEnabled(false);
    }
});

// Open detail when list item selected
adapter.setOnItemClickListener(item -> {
    getSupportFragmentManager().beginTransaction()
        .replace(R.id.detail_container, DetailFragment.newInstance(item.getId()))
        .commit();
    slidingPane.open();
});

@Override
public boolean onSupportNavigateUp() {
    if (slidingPane.isOpen()) {
        slidingPane.close();
        return true;
    }
    return super.onSupportNavigateUp();
}
```

### `res/layout/activity_master_detail.xml`
```xml
<androidx.slidingpanelayout.widget.SlidingPaneLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/sliding_pane_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Master (list) -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/list_container"
        android:layout_width="280dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <!-- Detail -->
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/detail_container"
        android:layout_width="300dp"
        android:layout_height="match_parent"
        android:layout_weight="2" />
</androidx.slidingpanelayout.widget.SlidingPaneLayout>
```

---

## 10. Top Navigation Bar

### Description
Top app bar with navigation links as tab-like buttons. Common in web-style apps and dashboards.

### `res/layout/layout_top_nav.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:background="?attr/colorSurface"
    android:paddingVertical="8dp"
    android:elevation="4dp">

    <ImageView
        android:id="@+id/top_nav_logo"
        android:layout_width="32dp"
        android:layout_height="32dp"
        android:layout_marginHorizontal="16dp"
        android:src="@drawable/ic_logo" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/top_nav_home"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Home"
        style="@style/Widget.Material3.Button.TextButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/top_nav_products"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Products"
        style="@style/Widget.Material3.Button.TextButton" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/top_nav_about"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="About"
        style="@style/Widget.Material3.Button.TextButton" />

    <View android:layout_width="0dp" android:layout_height="1dp" android:layout_weight="1" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/top_nav_login"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Login"
        android:layout_marginEnd="16dp"
        style="@style/Widget.Material3.Button" />
</LinearLayout>
```

---

## 11. Mega Navigation Drawer

### Description
An extended navigation drawer with groupings, icons, badges, section headers, and a user account area.

### `res/layout/nav_mega_drawer.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="320dp"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="?attr/colorSurface">

    <!-- Account header -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp"
        android:background="?attr/colorPrimaryContainer">

        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/mega_avatar"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            app:strokeColor="?attr/colorOnPrimaryContainer"
            app:strokeWidth="2dp"
            android:layout_marginBottom="12dp" />

        <TextView
            android:id="@+id/mega_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            android:textColor="?attr/colorOnPrimaryContainer" />

        <TextView
            android:id="@+id/mega_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnPrimaryContainer" />

        <!-- Account switcher -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_switch_account"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Switch account"
            android:layout_marginTop="8dp"
            style="@style/Widget.Material3.Button.TextButton" />
    </LinearLayout>

    <!-- Navigation items -->
    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:paddingVertical="8dp">

            <!-- Will be populated by NavigationView -->
            <com.google.android.material.navigation.NavigationView
                android:id="@+id/mega_nav_view"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:menu="@menu/nav_drawer_menu"
                app:insetTop="0dp"
                app:insetBottom="0dp" />
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <!-- Footer -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="16dp"
        android:gravity="center_vertical">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/mega_btn_settings"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:icon="@drawable/ic_settings"
            style="@style/Widget.Material3.Button.IconButton" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/mega_btn_help"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:icon="@drawable/ic_help"
            style="@style/Widget.Material3.Button.IconButton" />

        <View android:layout_width="0dp" android:layout_height="1dp" android:layout_weight="1" />

        <com.google.android.material.materialswitch.MaterialSwitch
            android:id="@+id/mega_dark_mode"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>
</LinearLayout>
```

---

## 12. Jetpack Navigation Component

### Description
The recommended navigation framework for Android. Manages back stack, arguments, transitions, and deep links.

### Dependency
```groovy
implementation 'androidx.navigation:navigation-fragment:2.7.7'
implementation 'androidx.navigation:navigation-ui:2.7.7'
```

### `res/navigation/nav_graph.xml`
```xml
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/homeFragment">

    <fragment
        android:id="@+id/homeFragment"
        android:name="com.example.app.HomeFragment"
        android:label="Home"
        tools:layout="@layout/fragment_home">

        <action
            android:id="@+id/action_home_to_detail"
            app:destination="@id/detailFragment"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left"
            app:popEnterAnim="@anim/slide_in_left"
            app:popExitAnim="@anim/slide_out_right" />
    </fragment>

    <fragment
        android:id="@+id/detailFragment"
        android:name="com.example.app.DetailFragment"
        android:label="Detail"
        tools:layout="@layout/fragment_detail">

        <argument
            android:name="itemId"
            app:argType="string" />
        <argument
            android:name="itemTitle"
            app:argType="string"
            android:defaultValue="Item" />
    </fragment>

    <fragment
        android:id="@+id/settingsFragment"
        android:name="com.example.app.SettingsFragment"
        android:label="Settings" />

    <!-- Deep link -->
    <fragment
        android:id="@+id/notificationFragment"
        android:name="com.example.app.NotificationFragment">
        <deepLink app:uri="myapp://notification/{id}" />
    </fragment>
</navigation>
```

### `res/layout/activity_nav.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        app:navGraph="@navigation/nav_graph"
        app:defaultNavHost="true" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_nav"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:menu="@menu/bottom_nav_menu" />
</LinearLayout>
```

### Java — NavController Setup
```java
NavHostFragment navHostFragment = (NavHostFragment) getSupportFragmentManager()
    .findFragmentById(R.id.nav_host_fragment);
NavController navController = navHostFragment.getNavController();

// Connect with BottomNavigationView
NavigationUI.setupWithNavController(bottomNav, navController);

// Connect with Toolbar
NavigationUI.setupWithNavController(toolbar, navController);

// Navigate programmatically
navController.navigate(R.id.action_home_to_detail);

// Navigate with arguments
Bundle args = new Bundle();
args.putString("itemId", "123");
args.putString("itemTitle", "My Item");
navController.navigate(R.id.action_home_to_detail, args);

// Safe Args (after adding plugin)
// HomeFragmentDirections.ActionHomeToDetail action =
//     HomeFragmentDirections.actionHomeToDetail("123", "My Item");
// navController.navigate(action);

// Back
navController.popBackStack();
navController.navigateUp();

// Deep link
navController.navigate(Uri.parse("myapp://notification/42"));
```

### Safe Args Plugin
```groovy
// build.gradle (project)
classpath 'androidx.navigation:navigation-safe-args-gradle-plugin:2.7.7'

// build.gradle (app)
apply plugin: 'androidx.navigation.safeargs'
```

### Fragment — Receive arguments
```java
// In DetailFragment
@Override
public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    String itemId = DetailFragmentArgs.fromBundle(getArguments()).getItemId();
    String title = DetailFragmentArgs.fromBundle(getArguments()).getItemTitle();
    // use itemId and title
}
```

### Navigation with Shared Element Transition
```java
// From HomeFragment
View sharedView = itemView.findViewById(R.id.img_item);
String transitionName = "item_image_" + item.getId();
sharedView.setTransitionName(transitionName);

FragmentNavigator.Extras extras = new FragmentNavigatorExtras(
    sharedView, transitionName);

Bundle args = new Bundle();
args.putString("itemId", item.getId());
NavHostFragment.findNavController(this)
    .navigate(R.id.action_home_to_detail, args, null, extras);

// In DetailFragment onCreate
postponeEnterTransition();
ViewCompat.setTransitionName(detailImage, "item_image_" + itemId);
TransitionInflater inflater = TransitionInflater.from(requireContext());
setSharedElementEnterTransition(
    inflater.inflateTransition(R.transition.shared_image));

// After image load complete
startPostponedEnterTransition();
```

### `res/transition/shared_image.xml`
```xml
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android">
    <changeImageTransform />
    <changeBounds />
    <changeTransform />
</transitionSet>
```
