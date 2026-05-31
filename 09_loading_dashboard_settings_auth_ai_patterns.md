# Loading, Dashboard, Settings, Auth, AI & Premium Patterns — Android UI Encyclopedia

> **File:** `09_loading_dashboard_settings_auth_ai_patterns.md` | **API Target:** 35

---

## Table of Contents

### Loading Components
1. [Skeleton Screens](#1-skeleton-screens)
2. [Shimmer Effects](#2-shimmer-effects)
3. [Circular & Linear Progress](#3-circular--linear-progress)
4. [Pull-to-Refresh](#4-pull-to-refresh)
5. [Lazy Loading / Pagination Loaders](#5-lazy-loading--pagination-loaders)

### Dashboard Components
6. [KPI & Analytics Cards](#6-kpi--analytics-cards)
7. [Charts & Graphs](#7-charts--graphs)
8. [Activity Panels & Quick Actions](#8-activity-panels--quick-actions)
9. [Finance Dashboard](#9-finance-dashboard)

### Settings Screens
10. [Google-Style Settings](#10-google-style-settings)
11. [Samsung One UI Style Settings](#11-samsung-one-ui-style-settings)
12. [Preference Screens (Jetpack)](#12-preference-screens-jetpack)
13. [Account & Security Pages](#13-account--security-pages)

### Authentication UI
14. [Login Screen](#14-login-screen)
15. [Signup Screen](#15-signup-screen)
16. [OTP / Verification Screen](#16-otp--verification-screen)
17. [Biometric / Fingerprint UI](#17-biometric--fingerprint-ui)
18. [Passkey / MFA UI](#18-passkey--mfa-ui)

### AI Application Components
19. [AI Chat Interface](#19-ai-chat-interface)
20. [Streaming Response UI](#20-streaming-response-ui)
21. [AI Suggestion Chips](#21-ai-suggestion-chips)
22. [AI Prompt Input Area](#22-ai-prompt-input-area)
23. [AI Side Panel](#23-ai-side-panel)

### Premium Mobile App Patterns
24. [Google Pixel App Style](#24-google-pixel-app-style)
25. [Banking App Pattern](#25-banking-app-pattern)
26. [Social Media Feed Pattern](#26-social-media-feed-pattern)
27. [Streaming App Pattern](#27-streaming-app-pattern)
28. [E-Commerce Pattern](#28-e-commerce-pattern)

---

## 1. Skeleton Screens

### Description
Placeholder UI that mirrors the shape of the content that is loading. Provides a better perceived performance than a spinner.

### `res/layout/skeleton_item_card.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="16dp"
    android:layout_marginVertical="6dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="1dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="12dp"
        android:gravity="center_vertical">

        <!-- Avatar skeleton -->
        <View
            android:layout_width="52dp"
            android:layout_height="52dp"
            android:background="@drawable/skeleton_shape_circle" />

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="12dp"
            android:orientation="vertical">

            <!-- Title skeleton -->
            <View
                android:layout_width="match_parent"
                android:layout_height="14dp"
                android:layout_marginBottom="8dp"
                android:background="@drawable/skeleton_shape_rect" />

            <!-- Subtitle skeleton (shorter) -->
            <View
                android:layout_width="60%"
                android:layout_height="12dp"
                android:background="@drawable/skeleton_shape_rect" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### `res/drawable/skeleton_shape_rect.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#E0E0E0" />
    <corners android:radius="8dp" />
</shape>
```

### `res/drawable/skeleton_shape_circle.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="#E0E0E0" />
</shape>
```

### Java — Skeleton with pulse animation
```java
public class SkeletonAnimator {
    private static final int ANIM_DURATION = 1000;

    public static void startPulse(View... views) {
        for (View view : views) {
            ObjectAnimator anim = ObjectAnimator.ofFloat(view, "alpha", 1f, 0.4f);
            anim.setDuration(ANIM_DURATION);
            anim.setRepeatMode(ObjectAnimator.REVERSE);
            anim.setRepeatCount(ObjectAnimator.INFINITE);
            anim.setInterpolator(new AccelerateDecelerateInterpolator());
            anim.start();
            view.setTag(anim);
        }
    }

    public static void stopPulse(View... views) {
        for (View view : views) {
            Object tag = view.getTag();
            if (tag instanceof ObjectAnimator) {
                ((ObjectAnimator) tag).cancel();
            }
            view.setAlpha(1f);
        }
    }
}

// Usage
View skeletonLayout = inflater.inflate(R.layout.skeleton_item_card, parent, false);
SkeletonAnimator.startPulse(
    skeletonLayout.findViewById(R.id.skeleton_avatar),
    skeletonLayout.findViewById(R.id.skeleton_title),
    skeletonLayout.findViewById(R.id.skeleton_subtitle)
);

// Replace with real content after loading
dataRepository.fetchItems().observe(this, items -> {
    if (items != null) {
        skeletonLayout.setVisibility(View.GONE);
        realContent.setVisibility(View.VISIBLE);
        adapter.submitList(items);
    }
});
```

---

## 2. Shimmer Effects

### Dependency
```groovy
implementation 'com.facebook.shimmer:shimmer:0.5.0'
```

### `res/layout/layout_shimmer.xml`
```xml
<com.facebook.shimmer.ShimmerFrameLayout
    android:id="@+id/shimmer_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    shimmer:shimmer_auto_start="true"
    shimmer:shimmer_base_color="#E0E0E0"
    shimmer:shimmer_highlight_color="#F5F5F5"
    shimmer:shimmer_duration="1200"
    shimmer:shimmer_angle="15"
    shimmer:shimmer_shape="linear"
    shimmer:shimmer_direction="left_to_right">

    <!-- Skeleton items -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
    </LinearLayout>
</com.facebook.shimmer.ShimmerFrameLayout>
```

### Java
```java
ShimmerFrameLayout shimmer = findViewById(R.id.shimmer_layout);
RecyclerView recycler = findViewById(R.id.recycler);

// Show shimmer
shimmer.setVisibility(View.VISIBLE);
shimmer.startShimmer();
recycler.setVisibility(View.GONE);

// After data loaded
dataRepository.fetch().observe(this, data -> {
    shimmer.stopShimmer();
    shimmer.setVisibility(View.GONE);
    recycler.setVisibility(View.VISIBLE);
    adapter.submitList(data);
});
```

---

## 3. Circular & Linear Progress

### See `01_material_design_2.md` § Progress Indicators for full reference.

### Java — Animated progress update
```java
CircularProgressIndicator circular = findViewById(R.id.progress_circular);
LinearProgressIndicator linear = findViewById(R.id.progress_linear);

// Animate to value
ValueAnimator anim = ValueAnimator.ofInt(0, 75);
anim.setDuration(1500);
anim.setInterpolator(new DecelerateInterpolator());
anim.addUpdateListener(va -> {
    int value = (int) va.getAnimatedValue();
    circular.setProgress(value, false);
    linear.setProgress(value, false);
    tvPercent.setText(value + "%");
});
anim.start();

// Indeterminate with hide
circular.show(); // fades in
circular.hide(); // fades out with end action
```

---

## 4. Pull-to-Refresh

### See `03_androidx_components.md` § SwipeRefreshLayout for full reference.

### Java — Custom pull-to-refresh indicator
```java
SwipeRefreshLayout swipeRefresh = findViewById(R.id.swipe_refresh);
swipeRefresh.setColorSchemeResources(
    R.color.md3_primary, R.color.md3_secondary, R.color.md3_tertiary);
swipeRefresh.setProgressBackgroundColorSchemeResource(R.color.md3_surface);
swipeRefresh.setDistanceToTriggerSync(200); // pixels before trigger

swipeRefresh.setOnRefreshListener(() -> {
    loadData(result -> {
        adapter.submitList(result);
        swipeRefresh.setRefreshing(false);
    });
});
```

---

## 5. Lazy Loading / Pagination Loaders

### Java — Footer states (Loading, Error, End)
```java
public class PaginationAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private static final int TYPE_ITEM = 0;
    private static final int TYPE_LOADING = 1;
    private static final int TYPE_ERROR = 2;
    private static final int TYPE_END = 3;

    public enum State { LOADING, ERROR, END, DONE }

    private List<Item> items = new ArrayList<>();
    private State footerState = State.DONE;
    private Runnable onRetry;

    public void setState(State state) {
        this.footerState = state;
        notifyItemChanged(getItemCount() - 1);
    }

    @Override public int getItemCount() {
        return items.size() + (footerState != State.DONE ? 1 : 0);
    }

    @Override public int getItemViewType(int pos) {
        if (pos < items.size()) return TYPE_ITEM;
        switch (footerState) {
            case LOADING: return TYPE_LOADING;
            case ERROR: return TYPE_ERROR;
            case END: return TYPE_END;
            default: return TYPE_ITEM;
        }
    }

    @NonNull @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int type) {
        LayoutInflater inf = LayoutInflater.from(parent.getContext());
        switch (type) {
            case TYPE_LOADING:
                return new LoadingVH(inf.inflate(R.layout.item_footer_loading, parent, false));
            case TYPE_ERROR:
                return new ErrorVH(inf.inflate(R.layout.item_footer_error, parent, false));
            case TYPE_END:
                return new EndVH(inf.inflate(R.layout.item_footer_end, parent, false));
            default:
                return new ItemVH(inf.inflate(R.layout.item_list_standard, parent, false));
        }
    }

    static class LoadingVH extends RecyclerView.ViewHolder {
        LoadingVH(@NonNull View v) { super(v); }
    }
    static class ErrorVH extends RecyclerView.ViewHolder {
        Button btnRetry;
        ErrorVH(@NonNull View v) {
            super(v);
            btnRetry = v.findViewById(R.id.btn_retry);
        }
    }
    static class EndVH extends RecyclerView.ViewHolder {
        EndVH(@NonNull View v) { super(v); }
    }
}
```

### `res/layout/item_footer_loading.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="72dp"
    android:gravity="center"
    android:orientation="vertical">

    <com.google.android.material.progressindicator.CircularProgressIndicator
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:indicatorSize="32dp"
        app:trackThickness="3dp"
        android:indeterminate="true" />
</LinearLayout>
```

---

## 6. KPI & Analytics Cards

### See `05_card_systems.md` § Dashboard / KPI Cards for full reference.

### Java — Dashboard grid builder
```java
LinearLayout dashGrid = findViewById(R.id.dash_grid);

List<KpiData> kpis = Arrays.asList(
    new KpiData("Revenue", "$48,290", "+12.5%", true, R.drawable.ic_revenue),
    new KpiData("Orders", "1,842", "+5.2%", true, R.drawable.ic_orders),
    new KpiData("Returns", "142", "-3.1%", false, R.drawable.ic_returns),
    new KpiData("Users", "24,580", "+18.9%", true, R.drawable.ic_users)
);

LinearLayout row1 = new LinearLayout(this);
LinearLayout row2 = new LinearLayout(this);
row1.setOrientation(LinearLayout.HORIZONTAL);
row2.setOrientation(LinearLayout.HORIZONTAL);

for (int i = 0; i < kpis.size(); i++) {
    View card = LayoutInflater.from(this).inflate(R.layout.card_kpi, null);
    LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(
        0, ViewGroup.LayoutParams.WRAP_CONTENT, 1f);
    params.setMargins(8, 8, 8, 8);
    card.setLayoutParams(params);

    KpiData kpi = kpis.get(i);
    ((TextView)card.findViewById(R.id.tv_kpi_label)).setText(kpi.getLabel());
    ((TextView)card.findViewById(R.id.tv_kpi_value)).setText(kpi.getValue());
    TextView trendTv = card.findViewById(R.id.tv_kpi_trend);
    trendTv.setText(kpi.getTrend());
    trendTv.setTextColor(kpi.isPositive()
        ? Color.parseColor("#4CAF50")
        : Color.parseColor("#F44336"));

    if (i < 2) row1.addView(card);
    else row2.addView(card);
}
dashGrid.addView(row1);
dashGrid.addView(row2);
```

---

## 7. Charts & Graphs

### Dependency (MPAndroidChart)
```groovy
implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
// In settings.gradle:
// maven { url "https://jitpack.io" }
```

### `res/layout/layout_charts.xml`
```xml
<!-- Line Chart -->
<com.github.mikephil.charting.charts.LineChart
    android:id="@+id/line_chart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />

<!-- Bar Chart -->
<com.github.mikephil.charting.charts.BarChart
    android:id="@+id/bar_chart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />

<!-- Pie Chart -->
<com.github.mikephil.charting.charts.PieChart
    android:id="@+id/pie_chart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />
```

### Java — Line Chart
```java
LineChart lineChart = findViewById(R.id.line_chart);
lineChart.setTouchEnabled(true);
lineChart.setDragEnabled(true);
lineChart.setScaleEnabled(true);
lineChart.getDescription().setEnabled(false);
lineChart.getLegend().setEnabled(true);
lineChart.setNoDataText("No data available");

List<Entry> entries = new ArrayList<>();
entries.add(new Entry(0f, 120f));
entries.add(new Entry(1f, 185f));
entries.add(new Entry(2f, 150f));
entries.add(new Entry(3f, 220f));
entries.add(new Entry(4f, 195f));
entries.add(new Entry(5f, 270f));

LineDataSet dataSet = new LineDataSet(entries, "Revenue");
dataSet.setColor(ContextCompat.getColor(this, R.color.md3_primary));
dataSet.setLineWidth(2f);
dataSet.setCircleColor(ContextCompat.getColor(this, R.color.md3_primary));
dataSet.setCircleRadius(4f);
dataSet.setMode(LineDataSet.Mode.CUBIC_BEZIER);
dataSet.setDrawFilled(true);
dataSet.setFillColor(ContextCompat.getColor(this, R.color.md3_primary_container));
dataSet.setFillAlpha(80);
dataSet.setDrawValues(false);
dataSet.setHighLightColor(ContextCompat.getColor(this, R.color.md3_tertiary));

LineData lineData = new LineData(dataSet);
lineChart.setData(lineData);
lineChart.animateX(1000);
lineChart.invalidate();

// Style axes
XAxis xAxis = lineChart.getXAxis();
xAxis.setPosition(XAxis.XAxisPosition.BOTTOM);
xAxis.setDrawGridLines(false);
xAxis.setValueFormatter(new IndexAxisValueFormatter(
    new String[]{"Jan", "Feb", "Mar", "Apr", "May", "Jun"}));

YAxis leftAxis = lineChart.getAxisLeft();
leftAxis.setDrawGridLines(true);
leftAxis.setGridColor(Color.parseColor("#20000000"));
lineChart.getAxisRight().setEnabled(false);
```

### Java — Pie Chart
```java
PieChart pieChart = findViewById(R.id.pie_chart);
pieChart.getDescription().setEnabled(false);
pieChart.setHoleRadius(50f);
pieChart.setTransparentCircleRadius(55f);
pieChart.setHoleColor(ContextCompat.getColor(this, R.color.md3_surface));
pieChart.setCenterText("Sales");
pieChart.setCenterTextSize(18f);

List<PieEntry> entries = new ArrayList<>();
entries.add(new PieEntry(40f, "Electronics"));
entries.add(new PieEntry(25f, "Fashion"));
entries.add(new PieEntry(20f, "Food"));
entries.add(new PieEntry(15f, "Books"));

PieDataSet dataSet = new PieDataSet(entries, "Categories");
dataSet.setColors(new int[]{
    R.color.md3_primary, R.color.md3_secondary,
    R.color.md3_tertiary, R.color.md3_error
}, this);
dataSet.setSliceSpace(3f);
dataSet.setSelectionShift(8f);
dataSet.setDrawValues(true);
dataSet.setValueTextSize(12f);
dataSet.setValueTextColor(Color.WHITE);

PieData data = new PieData(dataSet);
pieChart.setData(data);
pieChart.animateY(1200, Easing.EaseInOutCubic);
pieChart.invalidate();
```

---

## 8. Activity Panels & Quick Actions

### `res/layout/panel_quick_actions.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp">

    <!-- Quick action card -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginEnd="8dp"
        android:clickable="true"
        android:focusable="true"
        app:cardCornerRadius="16dp"
        app:cardBackgroundColor="?attr/colorPrimaryContainer">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:gravity="center"
            android:padding="16dp">
            <ImageView android:layout_width="32dp" android:layout_height="32dp"
                android:src="@drawable/ic_send"
                android:layout_marginBottom="8dp" />
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="Send"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorOnPrimaryContainer" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>

    <com.google.android.material.card.MaterialCardView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginEnd="8dp"
        android:clickable="true"
        android:focusable="true"
        app:cardCornerRadius="16dp"
        app:cardBackgroundColor="?attr/colorSecondaryContainer">
        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="vertical" android:gravity="center" android:padding="16dp">
            <ImageView android:layout_width="32dp" android:layout_height="32dp"
                android:src="@drawable/ic_receive" android:layout_marginBottom="8dp" />
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="Receive"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorOnSecondaryContainer" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>

    <com.google.android.material.card.MaterialCardView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:clickable="true"
        android:focusable="true"
        app:cardCornerRadius="16dp"
        app:cardBackgroundColor="?attr/colorTertiaryContainer">
        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="vertical" android:gravity="center" android:padding="16dp">
            <ImageView android:layout_width="32dp" android:layout_height="32dp"
                android:src="@drawable/ic_history" android:layout_marginBottom="8dp" />
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="History"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorOnTertiaryContainer" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>
</LinearLayout>
```

---

## 9. Finance Dashboard

### `res/layout/layout_finance_dashboard.xml`
```xml
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:paddingBottom="80dp">

        <!-- Balance Card -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_margin="16dp"
            app:cardCornerRadius="24dp"
            app:cardBackgroundColor="?attr/colorPrimary">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                android:padding="24dp">

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Total Balance"
                    android:textColor="#B3FFFFFF"
                    android:textAppearance="?attr/textAppearanceBodyMedium" />

                <TextView
                    android:id="@+id/tv_balance"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="$24,582.50"
                    android:textColor="@color/white"
                    android:textAppearance="?attr/textAppearanceDisplaySmall"
                    android:textStyle="bold"
                    android:paddingVertical="8dp" />

                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:orientation="horizontal">

                    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:orientation="vertical">
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Income" android:textColor="#80FFFFFF"
                            android:textAppearance="?attr/textAppearanceLabelSmall" />
                        <TextView android:id="@+id/tv_income"
                            android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="↑ $8,200" android:textColor="@color/white"
                            android:textAppearance="?attr/textAppearanceTitleSmall" />
                    </LinearLayout>

                    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:orientation="vertical">
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Expenses" android:textColor="#80FFFFFF"
                            android:textAppearance="?attr/textAppearanceLabelSmall" />
                        <TextView android:id="@+id/tv_expense"
                            android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="↓ $3,420" android:textColor="@color/white"
                            android:textAppearance="?attr/textAppearanceTitleSmall" />
                    </LinearLayout>
                </LinearLayout>
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Quick actions -->
        <include layout="@layout/panel_quick_actions" />

        <!-- Chart -->
        <com.github.mikephil.charting.charts.LineChart
            android:id="@+id/spending_chart"
            android:layout_width="match_parent"
            android:layout_height="200dp"
            android:layout_marginHorizontal="16dp"
            android:layout_marginBottom="16dp" />

        <!-- Recent transactions title -->
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Recent Transactions"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            android:paddingHorizontal="16dp"
            android:paddingBottom="8dp" />

        <!-- Transactions RecyclerView -->
        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/rv_transactions"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:nestedScrollingEnabled="false" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

---

## 10. Google-Style Settings

### `res/layout/activity_settings_google.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:title="Settings"
        app:navigationIcon="@drawable/ic_back" />

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/settings_fragment_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

### `res/xml/preferences_google_style.xml`
```xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <!-- Profile header (custom) -->
    <Preference
        android:key="pref_account_header"
        android:layout="@layout/pref_account_header"
        android:selectable="true" />

    <PreferenceCategory
        android:title="General"
        app:iconSpaceReserved="false">

        <Preference
            android:key="pref_notifications"
            android:title="Notifications"
            android:summary="Banners, sounds, badges"
            android:icon="@drawable/ic_notifications" />

        <Preference
            android:key="pref_privacy"
            android:title="Privacy &amp; Security"
            android:icon="@drawable/ic_lock" />

        <ListPreference
            android:key="pref_language"
            android:title="Language"
            android:icon="@drawable/ic_language"
            android:entries="@array/language_names"
            android:entryValues="@array/language_codes"
            android:defaultValue="en" />
    </PreferenceCategory>

    <PreferenceCategory
        android:title="Display"
        app:iconSpaceReserved="false">

        <ListPreference
            android:key="pref_theme"
            android:title="Theme"
            android:icon="@drawable/ic_palette"
            android:entries="@array/theme_entries"
            android:entryValues="@array/theme_values"
            android:defaultValue="system" />

        <SwitchPreferenceCompat
            android:key="pref_dynamic_color"
            android:title="Dynamic color"
            android:summary="Use wallpaper colors (Android 12+)"
            android:icon="@drawable/ic_color" />
    </PreferenceCategory>

    <PreferenceCategory
        android:title="About"
        app:iconSpaceReserved="false">

        <Preference
            android:key="pref_version"
            android:title="App version"
            android:summary="2.4.1 (Build 241)"
            android:selectable="false" />

        <Preference
            android:key="pref_feedback"
            android:title="Send feedback"
            android:icon="@drawable/ic_feedback" />

        <Preference
            android:key="pref_rate"
            android:title="Rate this app"
            android:icon="@drawable/ic_star" />
    </PreferenceCategory>
</PreferenceScreen>
```

---

## 11. Samsung One UI Style Settings

### Description
Rounded headers, large icon tiles, card-grouped sections, and pastel icon backgrounds.

### `res/layout/item_oneui_setting.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="16dp"
    android:layout_marginVertical="4dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="16dp"
    app:cardElevation="0dp"
    app:strokeWidth="0dp"
    app:cardBackgroundColor="?attr/colorSurface">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="72dp"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:paddingHorizontal="16dp">

        <!-- Pastel icon container -->
        <FrameLayout
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:background="@drawable/bg_oneui_icon"
            android:layout_marginEnd="16dp">

            <ImageView
                android:id="@+id/oneui_icon"
                android:layout_width="24dp"
                android:layout_height="24dp"
                android:layout_gravity="center"
                android:src="@drawable/ic_wifi" />
        </FrameLayout>

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:orientation="vertical">

            <TextView
                android:id="@+id/oneui_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleSmall" />

            <TextView
                android:id="@+id/oneui_subtitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodySmall"
                android:textColor="?attr/colorOnSurfaceVariant" />
        </LinearLayout>

        <ImageView
            android:layout_width="20dp"
            android:layout_height="20dp"
            android:src="@drawable/ic_chevron_right"
            android:alpha="0.4" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### `res/drawable/bg_oneui_icon.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#1A6750A4" />
    <corners android:radius="14dp" />
</shape>
```

---

## 12. Preference Screens (Jetpack)

### See `04_list_structures.md` § Settings / Preference Lists for full reference.

---

## 13. Account & Security Pages

### `res/layout/activity_account.xml`
```xml
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:paddingBottom="80dp">

        <!-- Profile section -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:gravity="center"
            android:padding="32dp"
            android:background="?attr/colorPrimaryContainer">

            <FrameLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content">
                <com.google.android.material.imageview.ShapeableImageView
                    android:id="@+id/img_account_avatar"
                    android:layout_width="96dp"
                    android:layout_height="96dp"
                    android:scaleType="centerCrop"
                    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
                    app:strokeColor="?attr/colorSurface"
                    app:strokeWidth="3dp" />
                <com.google.android.material.floatingactionbutton.FloatingActionButton
                    android:id="@+id/fab_edit_photo"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_gravity="bottom|end"
                    app:fabSize="mini"
                    app:srcCompat="@drawable/ic_camera" />
            </FrameLayout>

            <TextView android:id="@+id/tv_account_name"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleLarge"
                android:textColor="?attr/colorOnPrimaryContainer"
                android:paddingTop="12dp" />

            <TextView android:id="@+id/tv_account_email"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodyMedium"
                android:textColor="?attr/colorOnPrimaryContainer" />
        </LinearLayout>

        <!-- Edit fields -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="16dp">

            <com.google.android.material.textfield.TextInputLayout
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:hint="Display name" android:layout_marginBottom="12dp"
                style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
                <com.google.android.material.textfield.TextInputEditText
                    android:id="@+id/et_display_name"
                    android:layout_width="match_parent" android:layout_height="wrap_content" />
            </com.google.android.material.textfield.TextInputLayout>

            <com.google.android.material.textfield.TextInputLayout
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:hint="Email" android:layout_marginBottom="12dp"
                style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
                <com.google.android.material.textfield.TextInputEditText
                    android:id="@+id/et_email"
                    android:layout_width="match_parent" android:layout_height="wrap_content"
                    android:inputType="textEmailAddress" />
            </com.google.android.material.textfield.TextInputLayout>

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_save_profile"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="Save changes"
                style="@style/Widget.Material3.Button" />
        </LinearLayout>
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

---

## 14. Login Screen

### `res/layout/activity_login.xml`
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="24dp"
    android:background="?attr/colorBackground">

    <!-- Logo -->
    <ImageView
        android:id="@+id/img_logo"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:src="@drawable/ic_logo"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="48dp" />

    <!-- Welcome text -->
    <TextView
        android:id="@+id/tv_welcome"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Welcome back"
        android:textAppearance="?attr/textAppearanceHeadlineMedium"
        android:gravity="center"
        app:layout_constraintTop_toBottomOf="@id/img_logo"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="24dp" />

    <TextView
        android:id="@+id/tv_welcome_sub"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sign in to continue"
        android:textAppearance="?attr/textAppearanceBodyLarge"
        android:textColor="?attr/colorOnSurfaceVariant"
        android:gravity="center"
        app:layout_constraintTop_toBottomOf="@id/tv_welcome"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="8dp" />

    <!-- Email field -->
    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/til_login_email"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Email"
        app:startIconDrawable="@drawable/ic_email"
        style="@style/Widget.Material3.TextInputLayout.OutlinedBox"
        app:layout_constraintTop_toBottomOf="@id/tv_welcome_sub"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="32dp">
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/et_login_email"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textEmailAddress"
            android:imeOptions="actionNext" />
    </com.google.android.material.textfield.TextInputLayout>

    <!-- Password field -->
    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/til_login_password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Password"
        app:startIconDrawable="@drawable/ic_lock"
        app:endIconMode="password_toggle"
        style="@style/Widget.Material3.TextInputLayout.OutlinedBox"
        app:layout_constraintTop_toBottomOf="@id/til_login_email"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="12dp">
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/et_login_password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="textPassword"
            android:imeOptions="actionDone" />
    </com.google.android.material.textfield.TextInputLayout>

    <!-- Forgot password -->
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_forgot_password"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Forgot password?"
        app:layout_constraintTop_toBottomOf="@id/til_login_password"
        app:layout_constraintEnd_toEndOf="parent"
        style="@style/Widget.Material3.Button.TextButton" />

    <!-- Login button -->
    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_login"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Sign In"
        app:layout_constraintTop_toBottomOf="@id/btn_forgot_password"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="8dp"
        style="@style/Widget.Material3.Button" />

    <!-- Divider -->
    <TextView
        android:id="@+id/tv_or"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="or continue with"
        android:textAppearance="?attr/textAppearanceBodyMedium"
        android:textColor="?attr/colorOnSurfaceVariant"
        app:layout_constraintTop_toBottomOf="@id/btn_login"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="24dp" />

    <!-- Social login -->
    <LinearLayout
        android:id="@+id/social_login_row"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        app:layout_constraintTop_toBottomOf="@id/tv_or"
        app:layout_constraintStart_toStartOf="parent"
        android:layout_marginTop="16dp">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_google"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginEnd="8dp"
            android:text="Google"
            app:icon="@drawable/ic_google"
            style="@style/Widget.Material3.Button.OutlinedButton" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_apple"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Apple"
            app:icon="@drawable/ic_apple"
            style="@style/Widget.Material3.Button.OutlinedButton" />
    </LinearLayout>

    <!-- Sign up link -->
    <TextView
        android:id="@+id/tv_signup_link"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Don't have an account? Sign up"
        android:textAppearance="?attr/textAppearanceBodyMedium"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginBottom="16dp" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### Java — Login validation
```java
MaterialButton btnLogin = findViewById(R.id.btn_login);
TextInputLayout tilEmail = findViewById(R.id.til_login_email);
TextInputLayout tilPassword = findViewById(R.id.til_login_password);
TextInputEditText etEmail = findViewById(R.id.et_login_email);
TextInputEditText etPassword = findViewById(R.id.et_login_password);

btnLogin.setOnClickListener(v -> {
    String email = etEmail.getText().toString().trim();
    String password = etPassword.getText().toString();
    boolean valid = true;

    if (email.isEmpty()) {
        tilEmail.setError("Email is required");
        valid = false;
    } else if (!Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
        tilEmail.setError("Enter a valid email");
        valid = false;
    } else {
        tilEmail.setError(null);
    }

    if (password.isEmpty()) {
        tilPassword.setError("Password is required");
        valid = false;
    } else if (password.length() < 8) {
        tilPassword.setError("Password must be at least 8 characters");
        valid = false;
    } else {
        tilPassword.setError(null);
    }

    if (valid) performLogin(email, password);
});

private void performLogin(String email, String password) {
    btnLogin.setEnabled(false);
    btnLogin.setText("Signing in...");

    authRepository.login(email, password)
        .addOnSuccessListener(user -> {
            startActivity(new Intent(this, MainActivity.class));
            finish();
        })
        .addOnFailureListener(e -> {
            btnLogin.setEnabled(true);
            btnLogin.setText("Sign In");
            Snackbar.make(btnLogin, "Login failed: " + e.getMessage(),
                Snackbar.LENGTH_LONG).show();
        });
}
```

---

## 15. Signup Screen

### `res/layout/activity_signup.xml`
```xml
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp">

        <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
            android:text="Create account" android:gravity="center"
            android:textAppearance="?attr/textAppearanceHeadlineMedium"
            android:paddingBottom="8dp" />

        <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
            android:text="Join us today" android:gravity="center"
            android:textAppearance="?attr/textAppearanceBodyLarge"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:paddingBottom="32dp" />

        <!-- Name -->
        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:hint="Full name" android:layout_marginBottom="12dp"
            app:startIconDrawable="@drawable/ic_person"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/et_name"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPersonName" android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Email -->
        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/til_signup_email"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:hint="Email" android:layout_marginBottom="12dp"
            app:startIconDrawable="@drawable/ic_email"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/et_signup_email"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textEmailAddress" android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Password -->
        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/til_signup_password"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:hint="Password" android:layout_marginBottom="12dp"
            app:endIconMode="password_toggle"
            app:helperTextEnabled="true"
            app:helperText="Min. 8 characters with number and symbol"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/et_signup_password"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPassword" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Terms checkbox -->
        <com.google.android.material.checkbox.MaterialCheckBox
            android:id="@+id/cb_terms"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="I agree to Terms of Service and Privacy Policy"
            android:layout_marginBottom="20dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_signup"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Create Account"
            style="@style/Widget.Material3.Button" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

---

## 16. OTP / Verification Screen

### See `07_search_input_selection_media.md` § OTP / PIN Input for full reference.

---

## 17. Biometric / Fingerprint UI

### Java — BiometricPrompt
```java
import androidx.biometric.BiometricManager;
import androidx.biometric.BiometricPrompt;
import androidx.core.content.ContextCompat;

private void showBiometricPrompt() {
    Executor executor = ContextCompat.getMainExecutor(this);
    BiometricPrompt biometricPrompt = new BiometricPrompt(this, executor,
        new BiometricPrompt.AuthenticationCallback() {
            @Override
            public void onAuthenticationSucceeded(@NonNull BiometricPrompt.AuthenticationResult result) {
                // Authenticated
                proceedAfterAuth();
            }
            @Override
            public void onAuthenticationFailed() {
                Snackbar.make(rootView, "Authentication failed", Snackbar.LENGTH_SHORT).show();
            }
            @Override
            public void onAuthenticationError(int code, @NonNull CharSequence errString) {
                if (code != BiometricPrompt.ERROR_USER_CANCELED &&
                        code != BiometricPrompt.ERROR_NEGATIVE_BUTTON) {
                    Snackbar.make(rootView, errString, Snackbar.LENGTH_LONG).show();
                }
            }
        });

    BiometricPrompt.PromptInfo promptInfo = new BiometricPrompt.PromptInfo.Builder()
        .setTitle("Biometric Authentication")
        .setSubtitle("Verify your identity")
        .setDescription("Use your fingerprint or face to authenticate")
        .setAllowedAuthenticators(
            BiometricManager.Authenticators.BIOMETRIC_STRONG |
            BiometricManager.Authenticators.DEVICE_CREDENTIAL)
        .build();

    biometricPrompt.authenticate(promptInfo);
}

// Check availability
BiometricManager biometricManager = BiometricManager.from(this);
switch (biometricManager.canAuthenticate(BiometricManager.Authenticators.BIOMETRIC_STRONG)) {
    case BiometricManager.BIOMETRIC_SUCCESS:
        showBiometricPrompt();
        break;
    case BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE:
        tvBioStatus.setText("No biometric hardware");
        break;
    case BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED:
        // Prompt user to enroll
        Intent enrollIntent = new Intent(Settings.ACTION_BIOMETRIC_ENROLL);
        enrollIntent.putExtra(Settings.EXTRA_BIOMETRIC_AUTHENTICATORS_ALLOWED,
            BiometricManager.Authenticators.BIOMETRIC_STRONG);
        startActivity(enrollIntent);
        break;
}
```

### `res/layout/activity_biometric.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="32dp">

    <!-- Lottie fingerprint animation -->
    <com.airbnb.lottie.LottieAnimationView
        android:id="@+id/lottie_biometric"
        android:layout_width="180dp"
        android:layout_height="180dp"
        app:lottie_fileName="fingerprint.json"
        app:lottie_autoPlay="true"
        app:lottie_loop="true" />

    <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="Touch the fingerprint sensor"
        android:textAppearance="?attr/textAppearanceTitleMedium"
        android:gravity="center" android:paddingTop="16dp" />

    <TextView android:layout_width="match_parent" android:layout_height="wrap_content"
        android:text="or"
        android:textAppearance="?attr/textAppearanceBodyMedium"
        android:textColor="?attr/colorOnSurfaceVariant"
        android:gravity="center" android:paddingVertical="12dp" />

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btn_use_pin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Use PIN instead"
        style="@style/Widget.Material3.Button.OutlinedButton" />
</LinearLayout>
```

---

## 18. Passkey / MFA UI

### Java — Passkey (Credential Manager API — API 28+)
```java
// Requires: implementation 'androidx.credentials:credentials:1.2.2'
// implementation 'androidx.credentials:credentials-play-services-auth:1.2.2'

CredentialManager credentialManager = CredentialManager.create(this);

// Create passkey
CreatePublicKeyCredentialRequest request = new CreatePublicKeyCredentialRequest(
    "{\"rp\":{\"name\":\"My App\",\"id\":\"myapp.com\"}," +
    "\"user\":{\"id\":\"user123\",\"name\":\"john@example.com\",\"displayName\":\"John\"}," +
    "\"challenge\":\"base64-challenge\"," +
    "\"pubKeyCredParams\":[{\"type\":\"public-key\",\"alg\":-7}]}");

credentialManager.createCredentialAsync(this, request,
    new CancellationSignal(),
    Executors.newSingleThreadExecutor(),
    new CredentialManagerCallback<CreateCredentialResponse, CreateCredentialException>() {
        @Override
        public void onResult(CreateCredentialResponse result) {
            // Passkey created — send to server
            String responseJson = ((CreatePublicKeyCredentialResponse) result)
                .getRegistrationResponseJson();
            sendToServer(responseJson);
        }
        @Override
        public void onError(@NonNull CreateCredentialException e) {
            runOnUiThread(() -> showError(e.getMessage()));
        }
    });
```

---

## 19. AI Chat Interface

### `res/layout/activity_ai_chat.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:fitsSystemWindows="true">

    <!-- App bar -->
    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/ai_toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        app:title="AI Assistant"
        app:subtitle="Powered by Claude"
        app:navigationIcon="@drawable/ic_back"
        app:menu="@menu/ai_chat_menu" />

    <!-- Messages list -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_messages"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:clipToPadding="false"
        android:paddingHorizontal="8dp"
        android:paddingVertical="8dp" />

    <!-- Suggestion chips -->
    <HorizontalScrollView
        android:id="@+id/scroll_suggestions"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:scrollbars="none"
        android:paddingHorizontal="8dp"
        android:paddingVertical="4dp">

        <com.google.android.material.chip.ChipGroup
            android:id="@+id/chip_group_suggestions"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:singleLine="true" />
    </HorizontalScrollView>

    <!-- Input area -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="8dp"
        android:gravity="bottom"
        android:background="?attr/colorSurface"
        android:elevation="8dp">

        <!-- Attachment button -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_attach"
            android:layout_width="48dp"
            android:layout_height="48dp"
            app:icon="@drawable/ic_attach"
            style="@style/Widget.Material3.Button.IconButton" />

        <!-- Text input -->
        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/til_message"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginHorizontal="4dp"
            app:endIconMode="none"
            app:boxCornerRadiusTopStart="24dp"
            app:boxCornerRadiusTopEnd="24dp"
            app:boxCornerRadiusBottomStart="24dp"
            app:boxCornerRadiusBottomEnd="24dp"
            style="@style/Widget.Material3.TextInputLayout.FilledBox.Dense">

            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/et_message"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:hint="Message"
                android:maxLines="4"
                android:inputType="textMultiLine|textCapSentences"
                android:imeOptions="actionSend" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Send / Voice button -->
        <com.google.android.material.floatingactionbutton.FloatingActionButton
            android:id="@+id/fab_send"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:fabSize="mini"
            app:srcCompat="@drawable/ic_send"
            app:backgroundTint="?attr/colorPrimary" />
    </LinearLayout>
</LinearLayout>
```

---

## 20. Streaming Response UI

### Description
Displays AI-generated text as it arrives token by token, with typing animation.

### Java — Streaming text display
```java
public class StreamingTextView {
    private final TextView textView;
    private final StringBuilder buffer = new StringBuilder();
    private final Handler handler = new Handler(Looper.getMainLooper());

    public StreamingTextView(TextView tv) { this.textView = tv; }

    // Call this for each token/chunk received
    public void appendChunk(String chunk) {
        handler.post(() -> {
            buffer.append(chunk);
            textView.setText(buffer.toString());
            // Auto-scroll if in ScrollView
        });
    }

    public void complete() {
        handler.post(() -> {
            // Stop cursor blink if any
            textView.setCompoundDrawables(null, null, null, null);
        });
    }
}

// Message bubble with streaming cursor
public class AiMessageVH extends RecyclerView.ViewHolder {
    private final TextView tvContent;
    private final View typingCursor;

    AiMessageVH(@NonNull View v) {
        super(v);
        tvContent = v.findViewById(R.id.tv_ai_content);
        typingCursor = v.findViewById(R.id.typing_cursor);
    }

    void bindStreaming(AiMessage msg) {
        tvContent.setText(msg.getCurrentText());
        if (msg.isStreaming()) {
            typingCursor.setVisibility(View.VISIBLE);
            startCursorBlink();
        } else {
            typingCursor.setVisibility(View.GONE);
        }
    }

    private void startCursorBlink() {
        ObjectAnimator blink = ObjectAnimator.ofFloat(typingCursor, "alpha", 1f, 0f);
        blink.setDuration(530);
        blink.setRepeatMode(ObjectAnimator.REVERSE);
        blink.setRepeatCount(ObjectAnimator.INFINITE);
        blink.start();
        typingCursor.setTag(blink);
    }
}
```

---

## 21. AI Suggestion Chips

### Java — Dynamic suggestion chips
```java
ChipGroup chipGroup = findViewById(R.id.chip_group_suggestions);
String[] suggestions = {
    "Summarize this", "Explain in detail",
    "Give me examples", "What are the pros and cons?",
    "Translate to Spanish"
};

chipGroup.removeAllViews();
for (String suggestion : suggestions) {
    Chip chip = new Chip(this);
    chip.setText(suggestion);
    chip.setCheckable(false);
    chip.setChipBackgroundColorResource(R.color.md3_surface_variant);
    chip.setTextColor(ContextCompat.getColor(this, R.color.md3_on_surface_variant));
    chip.setOnClickListener(v -> {
        etMessage.setText(suggestion);
        etMessage.setSelection(suggestion.length());
    });
    chipGroup.addView(chip);
}
```

---

## 22. AI Prompt Input Area

### `res/layout/layout_ai_prompt_input.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="16dp"
    app:cardCornerRadius="28dp"
    app:cardElevation="4dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:paddingVertical="8dp">

        <!-- Attachment previews (when files added) -->
        <HorizontalScrollView
            android:id="@+id/scroll_attachments"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:scrollbars="none"
            android:visibility="gone"
            android:paddingHorizontal="12dp">
            <LinearLayout android:id="@+id/ll_attachments"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:orientation="horizontal" />
        </HorizontalScrollView>

        <!-- Input row -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="bottom"
            android:paddingHorizontal="8dp">

            <ImageButton android:id="@+id/btn_tools"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_add_circle"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:padding="8dp" />

            <EditText android:id="@+id/et_ai_prompt"
                android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1"
                android:hint="Ask anything..."
                android:background="@null"
                android:textAppearance="?attr/textAppearanceBodyLarge"
                android:inputType="textMultiLine|textCapSentences"
                android:minLines="1" android:maxLines="6"
                android:padding="8dp" />

            <ImageButton android:id="@+id/btn_voice"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_mic"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:padding="8dp" />

            <com.google.android.material.floatingactionbutton.FloatingActionButton
                android:id="@+id/fab_ai_send"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginStart="4dp"
                app:fabSize="mini"
                app:srcCompat="@drawable/ic_arrow_up"
                app:backgroundTint="?attr/colorPrimary" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 23. AI Side Panel

### `res/layout/layout_ai_side_panel.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="320dp"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="?attr/colorSurface"
    android:elevation="8dp">

    <!-- Panel header -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="16dp"
        android:gravity="center_vertical"
        android:background="?attr/colorPrimaryContainer">

        <com.airbnb.lottie.LottieAnimationView
            android:id="@+id/lottie_ai_thinking"
            android:layout_width="32dp"
            android:layout_height="32dp"
            app:lottie_fileName="ai_pulse.json"
            app:lottie_autoPlay="true"
            app:lottie_loop="true"
            android:layout_marginEnd="8dp" />

        <TextView android:layout_width="0dp" android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="AI Assistant"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            android:textColor="?attr/colorOnPrimaryContainer" />

        <ImageButton android:id="@+id/btn_close_panel"
            android:layout_width="32dp" android:layout_height="32dp"
            android:src="@drawable/ic_close"
            android:background="?attr/selectableItemBackgroundBorderless" />
    </LinearLayout>

    <!-- AI context chips -->
    <com.google.android.material.chip.ChipGroup
        android:id="@+id/panel_context_chips"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="8dp" />

    <!-- Response area -->
    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:padding="16dp">

        <TextView android:id="@+id/tv_ai_response"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:lineSpacingMultiplier="1.5" />
    </androidx.core.widget.NestedScrollView>

    <!-- Actions -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="8dp">

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_copy_response"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Copy"
            app:icon="@drawable/ic_copy"
            style="@style/Widget.Material3.Button.OutlinedButton" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_regenerate"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="8dp"
            android:text="Retry"
            app:icon="@drawable/ic_refresh"
            style="@style/Widget.Material3.Button.TonalButton" />
    </LinearLayout>
</LinearLayout>
```

---

## 24. Google Pixel App Style

### Key Characteristics
- Clean, white backgrounds with Material You dynamic color
- Large bold headlines
- Rounded cards, rounded icons
- Subtle dividers, no heavy outlines
- Material3 components throughout

```xml
<!-- Pixel-style list item -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="72dp"
    android:orientation="horizontal"
    android:gravity="center_vertical"
    android:paddingHorizontal="16dp"
    android:background="?attr/selectableItemBackground">

    <FrameLayout android:layout_width="40dp" android:layout_height="40dp"
        android:background="@drawable/bg_oneui_icon" android:layout_marginEnd="16dp">
        <ImageView android:id="@+id/pixel_item_icon"
            android:layout_width="24dp" android:layout_height="24dp"
            android:layout_gravity="center" />
    </FrameLayout>

    <TextView android:id="@+id/pixel_item_title"
        android:layout_width="0dp" android:layout_height="wrap_content"
        android:layout_weight="1"
        android:textAppearance="?attr/textAppearanceBodyLarge" />

    <TextView android:id="@+id/pixel_item_value"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:textAppearance="?attr/textAppearanceBodyMedium"
        android:textColor="?attr/colorOnSurfaceVariant" />

    <ImageView android:layout_width="20dp" android:layout_height="20dp"
        android:src="@drawable/ic_chevron_right" android:alpha="0.3"
        android:layout_marginStart="4dp" />
</LinearLayout>
```

---

## 25. Banking App Pattern

### Key Characteristics
- Dark card with balance
- Masked card number display
- Transaction list with category icons
- Biometric auth integration
- Security badges

### Java — Masked card number display
```java
TextView tvCardNumber = findViewById(R.id.tv_card_number);
String cardNumber = "4532015112830366";
String masked = "**** **** **** " + cardNumber.substring(cardNumber.length() - 4);
tvCardNumber.setText(masked);

// Toggle mask on tap
tvCardNumber.setOnClickListener(v -> {
    if (tvCardNumber.getText().toString().startsWith("*")) {
        tvCardNumber.setText(formatCardNumber(cardNumber));
    } else {
        tvCardNumber.setText(masked);
    }
});

private String formatCardNumber(String number) {
    return number.replaceAll("(.{4})", "$1 ").trim();
}
```

---

## 26. Social Media Feed Pattern

### Key Characteristics
- Story row (horizontal scroll)
- Feed posts (image, text, video)
- Like/comment/share actions
- InfiniteScroll
- Pull-to-refresh

### See `04_list_structures.md` § Feed Layouts for full reference.

---

## 27. Streaming App Pattern

### Key Characteristics
- Dark theme, high-contrast
- Hero banner with gradient overlay
- Horizontal content rows by category
- Large thumbnail cards
- Lottie loading animation

### Java — Content rows builder
```java
private void buildContentRows(List<ContentCategory> categories) {
    LinearLayout container = findViewById(R.id.content_container);
    LayoutInflater inflater = LayoutInflater.from(this);

    for (ContentCategory cat : categories) {
        View row = inflater.inflate(R.layout.layout_content_row, container, false);
        ((TextView) row.findViewById(R.id.tv_row_title)).setText(cat.getTitle());

        RecyclerView rv = row.findViewById(R.id.rv_row_items);
        rv.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.HORIZONTAL, false));
        rv.setAdapter(new ContentTileAdapter(cat.getItems(), item -> openDetail(item)));

        container.addView(row);
    }
}
```

---

## 28. E-Commerce Pattern

### Key Characteristics
- Product grid with image, title, price, rating
- Cart with item count badge
- Product detail with image carousel
- Filter/sort bottom sheet
- Size/color selector chips
- Add to cart FAB

### Java — Product detail price display
```java
TextView tvPrice = findViewById(R.id.tv_price);
TextView tvOriginalPrice = findViewById(R.id.tv_original_price);
TextView tvDiscount = findViewById(R.id.tv_discount);

double originalPrice = 89.99;
double discountPercent = 20;
double finalPrice = originalPrice * (1 - discountPercent / 100);

tvPrice.setText(String.format(Locale.getDefault(), "$%.2f", finalPrice));
tvOriginalPrice.setText(String.format(Locale.getDefault(), "$%.2f", originalPrice));
tvOriginalPrice.setPaintFlags(tvOriginalPrice.getPaintFlags() | Paint.STRIKE_THRU_TEXT_FLAG);
tvDiscount.setText((int) discountPercent + "% OFF");
```
