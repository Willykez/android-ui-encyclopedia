# Modern Card Systems — Android UI Encyclopedia

> **File:** `05_card_systems.md` | **API Target:** 35

---

## Table of Contents

1. [Material Elevated Cards](#1-material-elevated-cards)
2. [Outlined Cards](#2-outlined-cards)
3. [Filled / Tonal Cards](#3-filled--tonal-cards)
4. [Expandable Cards](#4-expandable-cards)
5. [Swipe Cards (Tinder-style)](#5-swipe-cards-tinder-style)
6. [Carousel Cards](#6-carousel-cards)
7. [Stacked Cards](#7-stacked-cards)
8. [Dashboard / KPI Cards](#8-dashboard--kpi-cards)
9. [Statistic Cards](#9-statistic-cards)
10. [Glassmorphism Cards](#10-glassmorphism-cards)
11. [Neumorphism Cards](#11-neumorphism-cards)
12. [Floating Cards](#12-floating-cards)
13. [Interactive / Animated Cards](#13-interactive--animated-cards)
14. [Profile Cards](#14-profile-cards)
15. [Media Cards](#15-media-cards)

---

## 1. Material Elevated Cards

### Description
Standard card with shadow elevation following MD3 surface tonal elevation. The most common container pattern.

### `res/layout/card_elevated.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="16dp"
    app:cardElevation="2dp"
    app:cardBackgroundColor="?attr/colorSurface"
    style="@style/Widget.Material3.CardView.Elevated">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <!-- Header image -->
        <ImageView
            android:id="@+id/img_card_header"
            android:layout_width="match_parent"
            android:layout_height="180dp"
            android:scaleType="centerCrop"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

        <!-- Tag badge -->
        <com.google.android.material.chip.Chip
            android:id="@+id/chip_tag"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Featured"
            app:chipBackgroundColor="?attr/colorPrimaryContainer"
            app:layout_constraintTop_toTopOf="@id/img_card_header"
            app:layout_constraintStart_toStartOf="parent"
            android:layout_margin="8dp"
            style="@style/Widget.Material3.Chip.Assist" />

        <!-- Content -->
        <TextView
            android:id="@+id/tv_card_title"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            android:paddingHorizontal="16dp"
            android:paddingTop="12dp"
            app:layout_constraintTop_toBottomOf="@id/img_card_header"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

        <TextView
            android:id="@+id/tv_card_body"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:paddingHorizontal="16dp"
            android:paddingTop="4dp"
            android:maxLines="2"
            android:ellipsize="end"
            app:layout_constraintTop_toBottomOf="@id/tv_card_title"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

        <!-- Footer actions -->
        <LinearLayout
            android:id="@+id/card_actions"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:paddingHorizontal="8dp"
            android:paddingVertical="8dp"
            app:layout_constraintTop_toBottomOf="@id/tv_card_body"
            app:layout_constraintBottom_toBottomOf="parent">

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_card_action"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Read more"
                style="@style/Widget.Material3.Button.TextButton" />

            <View android:layout_width="0dp" android:layout_height="1dp"
                android:layout_weight="1" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_card_share"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                app:icon="@drawable/ic_share"
                style="@style/Widget.Material3.Button.IconButton" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_card_bookmark"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                app:icon="@drawable/ic_bookmark_outline"
                style="@style/Widget.Material3.Button.IconButton" />
        </LinearLayout>
    </androidx.constraintlayout.widget.ConstraintLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Example
```java
MaterialCardView card = itemView.findViewById(R.id.card);

// Ripple on click
card.setOnClickListener(v -> openDetail(item));

// Elevation animation on press
card.setOnTouchListener((v, event) -> {
    switch (event.getAction()) {
        case MotionEvent.ACTION_DOWN:
            card.animate().scaleX(0.97f).scaleY(0.97f).setDuration(100).start();
            break;
        case MotionEvent.ACTION_UP:
        case MotionEvent.ACTION_CANCEL:
            card.animate().scaleX(1f).scaleY(1f).setDuration(100).start();
            break;
    }
    return false;
});

// Checked state
card.setCheckable(true);
card.setChecked(item.isSelected());
card.setOnClickListener(v -> {
    card.setChecked(!card.isChecked());
    item.setSelected(card.isChecked());
});
```

---

## 2. Outlined Cards

### `res/layout/card_outlined.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardElevation="0dp"
    app:strokeColor="?attr/colorOutlineVariant"
    app:strokeWidth="1dp"
    app:cardCornerRadius="12dp"
    app:cardBackgroundColor="?attr/colorSurface"
    style="@style/Widget.Material3.CardView.Outlined">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center_vertical">

            <com.google.android.material.imageview.ShapeableImageView
                android:id="@+id/img_icon"
                android:layout_width="40dp"
                android:layout_height="40dp"
                android:scaleType="centerCrop"
                app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.RoundedRect"
                android:src="@drawable/ic_placeholder" />

            <LinearLayout
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginStart="12dp"
                android:orientation="vertical">

                <TextView
                    android:id="@+id/tv_title"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleSmall" />

                <TextView
                    android:id="@+id/tv_subtitle"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceBodySmall"
                    android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>

            <com.google.android.material.materialswitch.MaterialSwitch
                android:id="@+id/switch_card"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />
        </LinearLayout>

        <com.google.android.material.divider.MaterialDivider
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginVertical="12dp" />

        <TextView
            android:id="@+id/tv_description"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyMedium" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 3. Filled / Tonal Cards

### `res/layout/card_filled_tonal.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="0dp"
    app:cardBackgroundColor="?attr/colorSecondaryContainer"
    style="@style/Widget.Material3.CardView.Filled">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="20dp">

        <ImageView
            android:id="@+id/img_card_icon"
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:src="@drawable/ic_analytics"
            android:layout_marginBottom="12dp" />

        <TextView
            android:id="@+id/tv_tonal_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleLarge"
            android:textColor="?attr/colorOnSecondaryContainer" />

        <TextView
            android:id="@+id/tv_tonal_value"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceDisplaySmall"
            android:textColor="?attr/colorOnSecondaryContainer" />

        <TextView
            android:id="@+id/tv_tonal_change"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSecondaryContainer" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 4. Expandable Cards

### `res/layout/card_expandable.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/card_expandable"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    android:clickable="true"
    android:focusable="true"
    app:cardCornerRadius="12dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Always-visible header -->
        <LinearLayout
            android:id="@+id/card_header_row"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:padding="16dp"
            android:gravity="center_vertical">

            <TextView
                android:id="@+id/tv_expand_title"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:textAppearance="?attr/textAppearanceTitleMedium" />

            <ImageView
                android:id="@+id/iv_expand_arrow"
                android:layout_width="24dp"
                android:layout_height="24dp"
                android:src="@drawable/ic_expand_more"
                android:rotation="0" />
        </LinearLayout>

        <!-- Expandable content -->
        <LinearLayout
            android:id="@+id/card_expand_content"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:visibility="gone"
            android:paddingHorizontal="16dp"
            android:paddingBottom="16dp">

            <com.google.android.material.divider.MaterialDivider
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp" />

            <TextView
                android:id="@+id/tv_expand_content"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodyMedium" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java Example
```java
boolean isExpanded = false;

LinearLayout header = card.findViewById(R.id.card_header_row);
LinearLayout content = card.findViewById(R.id.card_expand_content);
ImageView arrow = card.findViewById(R.id.iv_expand_arrow);

header.setOnClickListener(v -> {
    isExpanded = !isExpanded;

    // Animate arrow
    arrow.animate()
        .rotation(isExpanded ? 180f : 0f)
        .setDuration(250)
        .start();

    // Animate content
    if (isExpanded) {
        content.setVisibility(View.VISIBLE);
        content.setAlpha(0f);
        content.animate().alpha(1f).setDuration(250).start();
    } else {
        content.animate()
            .alpha(0f)
            .setDuration(200)
            .withEndAction(() -> content.setVisibility(View.GONE))
            .start();
    }

    // Animate card elevation change
    card.animate()
        .translationZ(isExpanded ? dpToPx(6) : dpToPx(2))
        .setDuration(200)
        .start();
});
```

### Kotlin Example
```kotlin
var isExpanded = false

header.setOnClickListener {
    isExpanded = !isExpanded
    arrow.animate().rotation(if (isExpanded) 180f else 0f).setDuration(250).start()

    if (isExpanded) {
        content.visibility = View.VISIBLE
        content.alpha = 0f
        content.animate().alpha(1f).setDuration(250).start()
    } else {
        content.animate().alpha(0f).setDuration(200)
            .withEndAction { content.visibility = View.GONE }.start()
    }
}
```

---

## 5. Swipe Cards (Tinder-style)

### Description
A stack of draggable cards; dragging left/right triggers accept/reject actions.

### Java — Drag-and-swipe implementation
```java
public class SwipeCardHelper {

    public interface SwipeListener {
        void onSwipedRight(int position);
        void onSwipedLeft(int position);
    }

    private static final float SWIPE_THRESHOLD = 0.4f;
    private static final float MAX_ROTATION_DEGREES = 20f;

    public static void attach(View cardView, int position, SwipeListener listener) {
        cardView.setOnTouchListener(new View.OnTouchListener() {
            float initialX, initialY, initialTouchX, initialTouchY;
            boolean swiped = false;

            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()) {
                    case MotionEvent.ACTION_DOWN:
                        initialX = v.getX();
                        initialY = v.getY();
                        initialTouchX = event.getRawX();
                        initialTouchY = event.getRawY();
                        swiped = false;
                        break;

                    case MotionEvent.ACTION_MOVE:
                        float dx = event.getRawX() - initialTouchX;
                        float dy = event.getRawY() - initialTouchY;
                        v.setX(initialX + dx);
                        v.setY(initialY + dy);
                        float rotation = MAX_ROTATION_DEGREES * dx / v.getWidth();
                        v.setRotation(rotation);
                        break;

                    case MotionEvent.ACTION_UP:
                        float finalDx = v.getX() - initialX;
                        float screenWidth = v.getResources()
                            .getDisplayMetrics().widthPixels;
                        if (Math.abs(finalDx) > screenWidth * SWIPE_THRESHOLD) {
                            float direction = finalDx > 0 ? 1f : -1f;
                            v.animate()
                                .x(direction * screenWidth * 2)
                                .rotation(direction * MAX_ROTATION_DEGREES * 2)
                                .alpha(0f)
                                .setDuration(300)
                                .withEndAction(() -> {
                                    if (direction > 0) listener.onSwipedRight(position);
                                    else listener.onSwipedLeft(position);
                                    v.setAlpha(1f);
                                    v.setX(initialX);
                                    v.setY(initialY);
                                    v.setRotation(0f);
                                })
                                .start();
                        } else {
                            // Snap back
                            v.animate().x(initialX).y(initialY)
                                .rotation(0f).setDuration(200).start();
                        }
                        break;
                }
                return true;
            }
        });
    }
}
```

### `res/layout/card_swipe.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/swipe_card"
    android:layout_width="300dp"
    android:layout_height="400dp"
    android:layout_gravity="center"
    app:cardCornerRadius="24dp"
    app:cardElevation="8dp">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <ImageView
            android:id="@+id/img_swipe"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scaleType="centerCrop" />

        <!-- Gradient overlay -->
        <View
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@drawable/gradient_card_bottom" />

        <!-- Info overlay -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom"
            android:orientation="vertical"
            android:padding="20dp">

            <TextView
                android:id="@+id/tv_swipe_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceHeadlineMedium"
                android:textColor="@color/white" />

            <TextView
                android:id="@+id/tv_swipe_info"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodyLarge"
                android:textColor="#B3FFFFFF" />
        </LinearLayout>
    </FrameLayout>
</com.google.android.material.card.MaterialCardView>
```

### `res/drawable/gradient_card_bottom.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:angle="270"
        android:startColor="#00000000"
        android:centerColor="#80000000"
        android:endColor="#CC000000"
        android:type="linear" />
</shape>
```

---

## 6. Carousel Cards

### Description
A horizontally scrollable strip of cards with snap behavior. MD3 has a built-in `CarouselLayoutManager`.

### Dependency
```groovy
implementation 'com.google.android.material:material:1.12.0'
```

### XML
```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/carousel_rv"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clipToPadding="false"
    android:clipChildren="false" />
```

### Java — MD3 CarouselLayoutManager
```java
RecyclerView carouselRv = findViewById(R.id.carousel_rv);

CarouselLayoutManager layoutManager = new CarouselLayoutManager();
layoutManager.setCarouselAlignment(CarouselLayoutManager.ALIGNMENT_CENTER);
carouselRv.setLayoutManager(layoutManager);
carouselRv.addItemDecoration(new CarouselItemDecoration(dpToPx(8), dpToPx(8)));

CarouselSnapHelper snapHelper = new CarouselSnapHelper();
snapHelper.attachToRecyclerView(carouselRv);

carouselRv.setAdapter(new CarouselAdapter(items));
```

### `res/layout/item_carousel.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="@dimen/carousel_card_width"
    android:layout_height="@dimen/carousel_card_height"
    app:cardCornerRadius="16dp"
    app:cardElevation="3dp">

    <ImageView
        android:id="@+id/img_carousel"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop" />

    <!-- Mask for non-focused cards (handled by CarouselLayoutManager) -->
</com.google.android.material.card.MaterialCardView>
```

### `res/values/dimens.xml` additions
```xml
<dimen name="carousel_card_width">280dp</dimen>
<dimen name="carousel_card_height">200dp</dimen>
```

---

## 7. Stacked Cards

### Description
Layered cards that peek behind the top card, suggesting more content beneath.

### `res/layout/layout_card_stack.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:paddingBottom="16dp">

    <!-- Bottom card (most behind) -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="24dp"
        android:layout_marginTop="16dp"
        app:cardCornerRadius="16dp"
        app:cardElevation="1dp"
        app:cardBackgroundColor="?attr/colorSurfaceVariant">
        <View android:layout_width="match_parent" android:layout_height="120dp" />
    </com.google.android.material.card.MaterialCardView>

    <!-- Middle card -->
    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="12dp"
        android:layout_marginTop="8dp"
        app:cardCornerRadius="16dp"
        app:cardElevation="2dp"
        app:cardBackgroundColor="?attr/colorSurface">
        <View android:layout_width="match_parent" android:layout_height="120dp" />
    </com.google.android.material.card.MaterialCardView>

    <!-- Top card (front) -->
    <com.google.android.material.card.MaterialCardView
        android:id="@+id/card_top"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:cardCornerRadius="16dp"
        app:cardElevation="4dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="20dp">

            <TextView
                android:id="@+id/tv_stack_title"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleLarge" />

            <TextView
                android:id="@+id/tv_stack_subtitle"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceBodyMedium"
                android:textColor="?attr/colorOnSurfaceVariant" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>
</FrameLayout>
```

---

## 8. Dashboard / KPI Cards

### `res/layout/card_kpi.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_weight="1"
    android:layout_margin="6dp"
    app:cardCornerRadius="20dp"
    app:cardElevation="0dp"
    app:cardBackgroundColor="?attr/colorPrimaryContainer">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Icon + trend badge -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center_vertical"
            android:layout_marginBottom="12dp">

            <ImageView
                android:id="@+id/kpi_icon"
                android:layout_width="32dp"
                android:layout_height="32dp"
                android:src="@drawable/ic_revenue" />

            <View android:layout_width="0dp"
                android:layout_height="1dp"
                android:layout_weight="1" />

            <TextView
                android:id="@+id/tv_kpi_trend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="↑ 12%"
                android:textColor="#4CAF50"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:background="@drawable/bg_trend_badge"
                android:paddingHorizontal="8dp"
                android:paddingVertical="4dp" />
        </LinearLayout>

        <!-- Value -->
        <TextView
            android:id="@+id/tv_kpi_value"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceHeadlineMedium"
            android:textColor="?attr/colorOnPrimaryContainer"
            android:textStyle="bold" />

        <!-- Label -->
        <TextView
            android:id="@+id/tv_kpi_label"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnPrimaryContainer" />

        <!-- Mini sparkline (just a View placeholder — use MPAndroidChart for real) -->
        <View
            android:id="@+id/sparkline"
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginTop="8dp" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### `res/drawable/bg_trend_badge.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#1A4CAF50" />
    <corners android:radius="100dp" />
</shape>
```

### Java — Dashboard grid
```java
// In a GridLayout (2 columns)
LinearLayout grid = findViewById(R.id.kpi_grid);
String[][] kpiData = {
    {"$24,580", "Revenue", "+12%"},
    {"1,284", "Orders", "+5%"},
    {"94%", "Rating", "+2%"},
    {"3,421", "Users", "+18%"}
};

for (String[] kpi : kpiData) {
    View kpiCard = LayoutInflater.from(this).inflate(R.layout.card_kpi, grid, false);
    ((TextView) kpiCard.findViewById(R.id.tv_kpi_value)).setText(kpi[0]);
    ((TextView) kpiCard.findViewById(R.id.tv_kpi_label)).setText(kpi[1]);
    ((TextView) kpiCard.findViewById(R.id.tv_kpi_trend)).setText(kpi[2]);
    grid.addView(kpiCard);
}
```

---

## 9. Statistic Cards

### `res/layout/card_statistic.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Title row -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center_vertical">

            <TextView
                android:id="@+id/tv_stat_label"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:textAppearance="?attr/textAppearanceLabelLarge"
                android:textColor="?attr/colorOnSurfaceVariant" />

            <ImageView
                android:id="@+id/btn_stat_more"
                android:layout_width="24dp"
                android:layout_height="24dp"
                android:src="@drawable/ic_more_vert" />
        </LinearLayout>

        <!-- Main value -->
        <TextView
            android:id="@+id/tv_stat_value"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceDisplaySmall"
            android:textStyle="bold"
            android:paddingVertical="8dp" />

        <!-- Progress bar -->
        <com.google.android.material.progressindicator.LinearProgressIndicator
            android:id="@+id/stat_progress"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:progress="68"
            android:max="100"
            app:trackThickness="6dp"
            app:trackCornerRadius="3dp"
            android:layout_marginBottom="8dp" />

        <!-- Sub-stats row -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <LinearLayout
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:orientation="vertical">
                <TextView android:id="@+id/tv_stat_sub1_label"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:textColor="?attr/colorOnSurfaceVariant" />
                <TextView android:id="@+id/tv_stat_sub1_value"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleSmall" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:orientation="vertical">
                <TextView android:id="@+id/tv_stat_sub2_label"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:textColor="?attr/colorOnSurfaceVariant" />
                <TextView android:id="@+id/tv_stat_sub2_value"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleSmall" />
            </LinearLayout>
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 10. Glassmorphism Cards

### Description
Frosted glass effect: semi-transparent background, blur, subtle border. Achieved via custom drawable + RenderEffect (API 31+) or WindowManager blur.

### `res/drawable/bg_glass_card.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#40FFFFFF" />
    <corners android:radius="24dp" />
    <stroke android:width="1dp" android:color="#60FFFFFF" />
</shape>
```

### `res/layout/card_glass.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="16dp">

    <!-- Blurred background image -->
    <ImageView
        android:id="@+id/img_glass_blur_bg"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop" />

    <!-- Glass card overlay -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@drawable/bg_glass_card"
        android:orientation="vertical"
        android:padding="20dp">

        <TextView
            android:id="@+id/tv_glass_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="@color/white"
            android:textAppearance="?attr/textAppearanceTitleLarge" />

        <TextView
            android:id="@+id/tv_glass_subtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#B3FFFFFF"
            android:textAppearance="?attr/textAppearanceBodyMedium" />
    </LinearLayout>
</FrameLayout>
```

### Java — RenderEffect blur (API 31+)
```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    RenderEffect blurEffect = RenderEffect.createBlurEffect(
        20f, 20f, Shader.TileMode.CLAMP);
    imageView.setRenderEffect(blurEffect);
}

// Legacy blur — use Glide blur transformation
Glide.with(this)
    .load(R.drawable.bg_image)
    .apply(RequestOptions.bitmapTransform(new BlurTransformation(25, 3)))
    .into(blurBgImage);
// Requires: implementation 'jp.wasabeef:glide-transformations:4.3.0'
```

### Kotlin Example
```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    val blur = RenderEffect.createBlurEffect(20f, 20f, Shader.TileMode.CLAMP)
    imageView.setRenderEffect(blur)
}
```

---

## 11. Neumorphism Cards

### Description
Soft UI with light/dark shadows on the same background color to create a pressed-into-surface or raised effect. Achieved with layered shadow drawables.

### `res/drawable/bg_neumorphic_raised.xml`
```xml
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Dark shadow (bottom-right) -->
    <item android:top="4dp" android:left="4dp">
        <shape>
            <solid android:color="#C8C8C8" />
            <corners android:radius="20dp" />
        </shape>
    </item>
    <!-- Light shadow (top-left) -->
    <item android:bottom="4dp" android:right="4dp">
        <shape>
            <solid android:color="#FFFFFF" />
            <corners android:radius="20dp" />
        </shape>
    </item>
    <!-- Main surface -->
    <item>
        <shape>
            <solid android:color="#E0E5EC" />
            <corners android:radius="20dp" />
        </shape>
    </item>
</layer-list>
```

### `res/layout/card_neumorphic.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="16dp"
    android:background="@drawable/bg_neumorphic_raised"
    android:padding="24dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <TextView
            android:id="@+id/tv_neu_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textColor="#4A4A6A"
            android:textAppearance="?attr/textAppearanceTitleMedium" />

        <TextView
            android:id="@+id/tv_neu_value"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textColor="#6C63FF"
            android:textAppearance="?attr/textAppearanceDisplaySmall" />
    </LinearLayout>
</FrameLayout>
```

> **Note:** Neumorphism works best on light backgrounds (#E0E5EC range). Dark mode requires a separate drawable with adjusted shadow colors.

---

## 12. Floating Cards

### Description
Cards that appear to float above the content with large shadows and subtle animations. Used in onboarding, features highlights.

### `res/layout/card_floating.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/card_floating"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="24dp"
    app:cardCornerRadius="24dp"
    app:cardElevation="16dp"
    app:cardMaxElevation="24dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp">

        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/img_float"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:scaleType="centerCrop"
            android:layout_gravity="center"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            android:layout_marginBottom="16dp" />

        <TextView
            android:id="@+id/tv_float_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="?attr/textAppearanceHeadlineSmall" />

        <TextView
            android:id="@+id/tv_float_desc"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:layout_marginTop="8dp" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_float_action"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Get started"
            android:layout_marginTop="20dp"
            style="@style/Widget.Material3.Button" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java — Float animation loop
```java
MaterialCardView floatingCard = findViewById(R.id.card_floating);

ObjectAnimator floatAnim = ObjectAnimator.ofFloat(floatingCard, "translationY",
    -16f, 16f);
floatAnim.setDuration(2000);
floatAnim.setRepeatMode(ObjectAnimator.REVERSE);
floatAnim.setRepeatCount(ObjectAnimator.INFINITE);
floatAnim.setInterpolator(new AccelerateDecelerateInterpolator());
floatAnim.start();
```

---

## 13. Interactive / Animated Cards

### Java — Card flip animation
```java
public void flipCard(View front, View back) {
    float scale = getResources().getDisplayMetrics().density;
    front.setCameraDistance(8000 * scale);
    back.setCameraDistance(8000 * scale);

    boolean isFront = front.getVisibility() == View.VISIBLE;

    ObjectAnimator frontOut = ObjectAnimator.ofFloat(
        isFront ? front : back, "rotationY", 0f, 90f);
    ObjectAnimator backIn = ObjectAnimator.ofFloat(
        isFront ? back : front, "rotationY", -90f, 0f);

    frontOut.setDuration(200);
    backIn.setDuration(200);

    frontOut.addListener(new AnimatorListenerAdapter() {
        @Override public void onAnimationEnd(Animator animation) {
            (isFront ? front : back).setVisibility(View.GONE);
            (isFront ? back : front).setVisibility(View.VISIBLE);
            backIn.start();
        }
    });
    frontOut.start();
}
```

### Kotlin — Card flip
```kotlin
fun flipCard(front: View, back: View) {
    val scale = resources.displayMetrics.density
    front.cameraDistance = 8000 * scale
    back.cameraDistance = 8000 * scale

    val isFront = front.visibility == View.VISIBLE
    val frontOut = ObjectAnimator.ofFloat(if (isFront) front else back, "rotationY", 0f, 90f)
    val backIn = ObjectAnimator.ofFloat(if (isFront) back else front, "rotationY", -90f, 0f)

    frontOut.duration = 200
    backIn.duration = 200

    frontOut.addListener(object : AnimatorListenerAdapter() {
        override fun onAnimationEnd(animation: Animator) {
            (if (isFront) front else back).visibility = View.GONE
            (if (isFront) back else front).visibility = View.VISIBLE
            backIn.start()
        }
    })
    frontOut.start()
}
```

---

## 14. Profile Cards

### `res/layout/card_profile.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="16dp"
    app:cardCornerRadius="24dp"
    app:cardElevation="4dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Cover photo -->
        <ImageView
            android:id="@+id/img_cover"
            android:layout_width="match_parent"
            android:layout_height="120dp"
            android:scaleType="centerCrop"
            android:src="@drawable/placeholder_cover" />

        <!-- Avatar (overlapping) -->
        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/img_avatar"
            android:layout_width="72dp"
            android:layout_height="72dp"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="-36dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            app:strokeColor="?attr/colorSurface"
            app:strokeWidth="3dp" />

        <!-- Info -->
        <TextView
            android:id="@+id/tv_profile_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:paddingTop="8dp"
            android:textAppearance="?attr/textAppearanceTitleLarge" />

        <TextView
            android:id="@+id/tv_profile_bio"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:paddingHorizontal="24dp"
            android:paddingBottom="16dp" />

        <!-- Stats row -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:paddingHorizontal="16dp"
            android:paddingBottom="16dp">

            <!-- Each stat -->
            <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:orientation="vertical" android:gravity="center">
                <TextView android:id="@+id/tv_posts_count"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleMedium" android:text="120" />
                <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:text="Posts" android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>

            <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:orientation="vertical" android:gravity="center">
                <TextView android:id="@+id/tv_followers_count"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleMedium" android:text="4.2K" />
                <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:text="Followers" android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>

            <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:orientation="vertical" android:gravity="center">
                <TextView android:id="@+id/tv_following_count"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleMedium" android:text="310" />
                <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:text="Following" android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>
        </LinearLayout>

        <!-- Actions -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:paddingHorizontal="16dp"
            android:paddingBottom="20dp"
            android:gravity="center">

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_follow"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="Follow"
                android:layout_marginEnd="8dp"
                style="@style/Widget.Material3.Button" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btn_message"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="Message"
                style="@style/Widget.Material3.Button.OutlinedButton" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

---

## 15. Media Cards

### `res/layout/card_media_video.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="16dp"
    app:cardElevation="2dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Thumbnail + overlay -->
        <FrameLayout
            android:layout_width="match_parent"
            android:layout_height="200dp">

            <ImageView
                android:id="@+id/img_video_thumb"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop" />

            <!-- Play button overlay -->
            <ImageView
                android:id="@+id/btn_play"
                android:layout_width="56dp"
                android:layout_height="56dp"
                android:layout_gravity="center"
                android:src="@drawable/ic_play_circle"
                android:alpha="0.85" />

            <!-- Duration badge -->
            <TextView
                android:id="@+id/tv_duration"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="bottom|end"
                android:layout_margin="8dp"
                android:background="@drawable/bg_duration_badge"
                android:paddingHorizontal="6dp"
                android:paddingVertical="2dp"
                android:text="12:34"
                android:textColor="@color/white"
                android:textAppearance="?attr/textAppearanceLabelSmall" />
        </FrameLayout>

        <!-- Meta -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:padding="12dp">

            <com.google.android.material.imageview.ShapeableImageView
                android:id="@+id/img_channel_avatar"
                android:layout_width="36dp"
                android:layout_height="36dp"
                android:scaleType="centerCrop"
                app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
                android:layout_marginEnd="12dp" />

            <LinearLayout
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:orientation="vertical">

                <TextView
                    android:id="@+id/tv_video_title"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:maxLines="2"
                    android:ellipsize="end"
                    android:textAppearance="?attr/textAppearanceTitleSmall" />

                <TextView
                    android:id="@+id/tv_video_meta"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceLabelSmall"
                    android:textColor="?attr/colorOnSurfaceVariant" />
            </LinearLayout>

            <ImageView
                android:id="@+id/btn_video_more"
                android:layout_width="24dp"
                android:layout_height="24dp"
                android:src="@drawable/ic_more_vert" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### `res/drawable/bg_duration_badge.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#CC000000" />
    <corners android:radius="4dp" />
</shape>
```

### Card Best Practices
| Practice | Reason |
|---|---|
| Use `cardElevation` + `cardMaxElevation` together | Prevents clipping during elevation animations |
| Always set `android:clickable="true"` | Required for ripple feedback |
| Use `setChecked()` for selection states | Proper MD3 state visual |
| Avoid deep nesting inside cards | Increases measure/layout time |
| Use `app:cardPreventCornerOverlap="true"` | Prevents content from showing through corners |
| Limit to 1 card per ConstraintLayout chain | Multiple spanning cards cause jank |
