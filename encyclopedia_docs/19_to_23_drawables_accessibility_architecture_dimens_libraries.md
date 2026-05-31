# Module 19 — Drawables, Shapes & Custom Styling
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Table of Contents
1. [Shape Drawables](#1-shape-drawables)
2. [Layer List Drawables](#2-layer-list-drawables)
3. [State List Drawables (Selectors)](#3-state-list-drawables-selectors)
4. [Vector Drawables](#4-vector-drawables)
5. [Animated Vector Drawables](#5-animated-vector-drawables)
6. [Ripple Drawables](#6-ripple-drawables)
7. [Gradient Drawables (Programmatic)](#7-gradient-drawables-programmatic)
8. [Custom View Drawing (Canvas API)](#8-custom-view-drawing-canvas-api)
9. [ShapeAppearance System](#9-shapeappearance-system)
10. [9-Patch Drawables](#10-9-patch-drawables)

---

## 1. Shape Drawables

### All Shape Types
```xml
<!-- Rectangle with rounded corners -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="?attr/colorPrimaryContainer" />
    <corners android:radius="16dp" />
    <stroke android:width="1dp" android:color="?attr/colorOutline" />
    <padding android:left="8dp" android:top="4dp"
             android:right="8dp" android:bottom="4dp" />
    <size android:width="100dp" android:height="40dp" />
</shape>

<!-- Oval / Circle -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="?attr/colorPrimary" />
    <size android:width="48dp" android:height="48dp" />
</shape>

<!-- Ring (donut shape) -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="ring"
    android:innerRadiusRatio="3"
    android:thicknessRatio="9"
    android:useLevel="false">
    <solid android:color="?attr/colorPrimary" />
</shape>

<!-- Line (horizontal divider) -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="line">
    <stroke android:width="1dp" android:color="?attr/colorOutlineVariant" />
</shape>

<!-- Asymmetric corners -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="?attr/colorSurfaceVariant" />
    <corners
        android:topLeftRadius="20dp"
        android:topRightRadius="20dp"
        android:bottomLeftRadius="4dp"
        android:bottomRightRadius="4dp" />
</shape>

<!-- Gradient background -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient
        android:startColor="#6750A4"
        android:centerColor="#9965F4"
        android:endColor="#C3A7FF"
        android:angle="135"
        android:type="linear" />
    <corners android:radius="12dp" />
</shape>

<!-- Radial gradient (glow effect) -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <gradient
        android:type="radial"
        android:gradientRadius="60dp"
        android:startColor="#806750A4"
        android:endColor="#006750A4" />
</shape>
```

---

## 2. Layer List Drawables

```xml
<!-- Stacked card shadow effect -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Shadow layer -->
    <item android:left="4dp" android:top="4dp">
        <shape android:shape="rectangle">
            <solid android:color="#20000000" />
            <corners android:radius="16dp" />
        </shape>
    </item>
    <!-- Main card -->
    <item android:right="4dp" android:bottom="4dp">
        <shape android:shape="rectangle">
            <solid android:color="?attr/colorSurface" />
            <corners android:radius="16dp" />
            <stroke android:width="1dp" android:color="?attr/colorOutlineVariant" />
        </shape>
    </item>
</layer-list>

<!-- Badge on top of icon -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/ic_notifications" />
    <item android:gravity="top|right"
        android:right="2dp" android:top="2dp">
        <shape android:shape="oval">
            <solid android:color="#F44336" />
            <size android:width="10dp" android:height="10dp" />
        </shape>
    </item>
</layer-list>

<!-- Tab indicator (bottom line) -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <color android:color="@android:color/transparent" />
    </item>
    <item android:top="-4dp" android:left="-4dp" android:right="-4dp">
        <shape android:shape="rectangle">
            <stroke android:width="4dp" android:color="?attr/colorPrimary" />
            <corners android:radius="2dp" />
        </shape>
    </item>
</layer-list>
```

---

## 3. State List Drawables (Selectors)

```xml
<!-- Button background with all states -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Disabled -->
    <item android:state_enabled="false">
        <shape android:shape="rectangle">
            <solid android:color="#1F000000" />
            <corners android:radius="100dp" />
        </shape>
    </item>
    <!-- Pressed -->
    <item android:state_pressed="true">
        <shape android:shape="rectangle">
            <solid android:color="?attr/colorPrimaryContainer" />
            <corners android:radius="100dp" />
        </shape>
    </item>
    <!-- Focused -->
    <item android:state_focused="true">
        <shape android:shape="rectangle">
            <solid android:color="?attr/colorSecondaryContainer" />
            <corners android:radius="100dp" />
            <stroke android:width="2dp" android:color="?attr/colorPrimary" />
        </shape>
    </item>
    <!-- Default -->
    <item>
        <shape android:shape="rectangle">
            <solid android:color="?attr/colorPrimary" />
            <corners android:radius="100dp" />
        </shape>
    </item>
</selector>

<!-- Text color selector -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="false" android:color="#61000000" />
    <item android:state_checked="true" android:color="?attr/colorPrimary" />
    <item android:color="?attr/colorOnSurfaceVariant" />
</selector>

<!-- Icon tint selector (checked/unchecked) -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true" android:color="?attr/colorOnPrimaryContainer" />
    <item android:color="?attr/colorOnSurfaceVariant" />
</selector>
```

---

## 4. Vector Drawables

```xml
<!-- res/drawable/ic_custom.xml -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorOnSurface">

    <path
        android:fillColor="@android:color/transparent"
        android:pathData="M12,2L2,7l10,5 10,-5 -10,-5z"
        android:strokeColor="?attr/colorOnSurface"
        android:strokeWidth="2"
        android:strokeLineCap="round"
        android:strokeLineJoin="round" />

    <path
        android:fillColor="@android:color/transparent"
        android:pathData="M2,17l10,5 10,-5"
        android:strokeColor="?attr/colorOnSurface"
        android:strokeWidth="2" />
</vector>

<!-- Animated vector — morphing hamburger to X -->
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt"
    android:drawable="@drawable/ic_menu">

    <target android:name="top_line">
        <aapt:attr name="android:animation">
            <objectAnimator
                android:propertyName="pathData"
                android:duration="300"
                android:valueFrom="M3,6 L21,6"
                android:valueTo="M6,6 L18,18"
                android:valueType="pathType"
                android:interpolator="@android:interpolator/fast_out_slow_in" />
        </aapt:attr>
    </target>
</animated-vector>
```

---

## 5. Animated Vector Drawables

```kotlin
// Programmatic AVD control
class AnimatedIconButton @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : AppCompatImageButton(context, attrs) {

    private var isChecked = false
    private val toCheckedAvd by lazy {
        AnimatedVectorDrawableCompat.create(context, R.drawable.avd_heart_fill)
    }
    private val toUncheckedAvd by lazy {
        AnimatedVectorDrawableCompat.create(context, R.drawable.avd_heart_unfill)
    }

    init {
        setImageDrawable(AnimatedVectorDrawableCompat.create(context, R.drawable.ic_heart_outline))
        setOnClickListener { toggle() }
    }

    fun toggle() {
        isChecked = !isChecked
        val avd = if (isChecked) toCheckedAvd else toUncheckedAvd
        setImageDrawable(avd)
        avd?.start()
    }
}
```

---

## 6. Ripple Drawables

```xml
<!-- Bounded ripple (respects shape) -->
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorPrimary">
    <item android:id="@android:id/mask">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <corners android:radius="12dp" />
        </shape>
    </item>
    <!-- Optional background behind ripple -->
    <item>
        <shape android:shape="rectangle">
            <solid android:color="?attr/colorSurface" />
            <corners android:radius="12dp" />
        </shape>
    </item>
</ripple>

<!-- Circular ripple (for icon buttons) -->
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorPrimary">
    <item android:id="@android:id/mask">
        <shape android:shape="oval">
            <solid android:color="#FFFFFF" />
        </shape>
    </item>
</ripple>

<!-- Unbounded ripple (spreads beyond bounds) -->
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorPrimary" />
```

---

## 7. Gradient Drawables (Programmatic)

```kotlin
// Build any gradient at runtime
fun buildGradientDrawable(
    colors: IntArray,
    orientation: GradientDrawable.Orientation = GradientDrawable.Orientation.TL_BR,
    cornerRadiusDp: Float = 0f,
    strokeWidthDp: Float = 0f,
    strokeColor: Int = Color.TRANSPARENT
): GradientDrawable {
    return GradientDrawable(orientation, colors).apply {
        gradientType = GradientDrawable.LINEAR_GRADIENT
        cornerRadius = cornerRadiusDp.dpToPx
        if (strokeWidthDp > 0) {
            setStroke(strokeWidthDp.dpToPx.toInt(), strokeColor)
        }
    }
}

// Animated gradient background
class AnimatedGradientBackground(private val view: View) {
    private val colors = arrayOf(
        intArrayOf(Color.parseColor("#6750A4"), Color.parseColor("#9965F4")),
        intArrayOf(Color.parseColor("#9965F4"), Color.parseColor("#FF6B6B")),
        intArrayOf(Color.parseColor("#FF6B6B"), Color.parseColor("#6750A4"))
    )
    private var colorIndex = 0

    fun start() {
        val animator = ValueAnimator.ofFloat(0f, 1f).apply {
            duration = 3000
            repeatCount = ValueAnimator.INFINITE
            repeatMode = ValueAnimator.REVERSE
            addUpdateListener { anim ->
                val fraction = anim.animatedFraction
                val currentColors = colors[colorIndex % colors.size]
                val nextColors = colors[(colorIndex + 1) % colors.size]
                val blended = currentColors.mapIndexed { i, c ->
                    interpolateColor(c, nextColors[i], fraction)
                }.toIntArray()
                view.background = buildGradientDrawable(blended)
            }
            doOnRepeat { colorIndex++ }
        }
        animator.start()
    }

    private fun interpolateColor(c1: Int, c2: Int, fraction: Float): Int {
        val r = (Color.red(c1) + (Color.red(c2) - Color.red(c1)) * fraction).toInt()
        val g = (Color.green(c1) + (Color.green(c2) - Color.green(c1)) * fraction).toInt()
        val b = (Color.blue(c1) + (Color.blue(c2) - Color.blue(c1)) * fraction).toInt()
        return Color.rgb(r, g, b)
    }
}
```

---

## 8. Custom View Drawing (Canvas API)

```kotlin
// Custom progress ring view
class CircularProgressView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {

    var progress: Float = 0f
        set(value) { field = value.coerceIn(0f, 100f); invalidate() }

    var trackColor: Int = ContextCompat.getColor(context, R.color.md_theme_surfaceVariant)
    var progressColor: Int = ContextCompat.getColor(context, R.color.md_theme_primary)
    var strokeWidth: Float = 12f.dpToPx(context)
    var labelText: String = ""

    private val trackPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        style = Paint.Style.STROKE
        strokeCap = Paint.Cap.ROUND
    }
    private val progressPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        style = Paint.Style.STROKE
        strokeCap = Paint.Cap.ROUND
    }
    private val textPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        textAlign = Paint.Align.CENTER
    }
    private val oval = RectF()

    override fun onDraw(canvas: Canvas) {
        val cx = width / 2f
        val cy = height / 2f
        val radius = (minOf(width, height) / 2f) - strokeWidth / 2f

        oval.set(cx - radius, cy - radius, cx + radius, cy + radius)

        // Draw track
        trackPaint.color = trackColor
        trackPaint.strokeWidth = strokeWidth
        canvas.drawCircle(cx, cy, radius, trackPaint)

        // Draw progress arc (starts at -90deg = 12 o'clock)
        progressPaint.color = progressColor
        progressPaint.strokeWidth = strokeWidth
        val sweepAngle = 360f * (progress / 100f)
        canvas.drawArc(oval, -90f, sweepAngle, false, progressPaint)

        // Draw label
        if (labelText.isNotEmpty()) {
            textPaint.color = ContextCompat.getColor(context, R.color.md_theme_onSurface)
            textPaint.textSize = radius * 0.4f
            canvas.drawText(labelText, cx, cy - (textPaint.descent() + textPaint.ascent()) / 2, textPaint)
        }
    }

    fun animateTo(targetProgress: Float) {
        ValueAnimator.ofFloat(progress, targetProgress).apply {
            duration = 800
            interpolator = DecelerateInterpolator()
            addUpdateListener { progress = it.animatedValue as Float }
        }.start()
    }
}
```

```kotlin
// Custom waveform / audio visualizer
class WaveformView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : View(context, attrs) {

    private val bars = 30
    private val amplitudes = FloatArray(bars) { Random.nextFloat() }
    private val paint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = ContextCompat.getColor(context, R.color.md_theme_primary)
        strokeCap = Paint.Cap.ROUND
    }
    private var isAnimating = false

    override fun onDraw(canvas: Canvas) {
        val barWidth = width.toFloat() / (bars * 2 - 1)
        val maxHeight = height * 0.9f
        paint.strokeWidth = barWidth

        amplitudes.forEachIndexed { i, amp ->
            val x = i * barWidth * 2 + barWidth / 2
            val barHeight = maxHeight * amp.coerceAtLeast(0.05f)
            val top = (height - barHeight) / 2
            val bottom = top + barHeight
            canvas.drawLine(x, top, x, bottom, paint)
        }
    }

    fun startAnimation() {
        isAnimating = true
        val animator = ValueAnimator.ofFloat(0f, 1f).apply {
            duration = 100
            repeatCount = ValueAnimator.INFINITE
            addUpdateListener {
                amplitudes.forEachIndexed { i, _ ->
                    amplitudes[i] = amplitudes[i] * 0.7f + Random.nextFloat() * 0.3f
                }
                invalidate()
            }
        }
        animator.start()
    }
}
```

---

## 9. ShapeAppearance System

```xml
<!-- res/values/shape_appearances.xml — Full reference -->

<!-- Circle -->
<style name="ShapeAppearance.App.Circle" parent="ShapeAppearance.Material3.Corner.Full">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">50%</item>
</style>

<!-- Pill / Stadium -->
<style name="ShapeAppearance.App.Pill" parent="">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">100dp</item>
</style>

<!-- Clipped corners (cut) -->
<style name="ShapeAppearance.App.Cut" parent="">
    <item name="cornerFamily">cut</item>
    <item name="cornerSize">12dp</item>
</style>

<!-- Mixed corners -->
<style name="ShapeAppearance.App.TopRounded" parent="">
    <item name="cornerFamilyTopLeft">rounded</item>
    <item name="cornerFamilyTopRight">rounded</item>
    <item name="cornerFamilyBottomLeft">cut</item>
    <item name="cornerFamilyBottomRight">cut</item>
    <item name="cornerSizeTopLeft">16dp</item>
    <item name="cornerSizeTopRight">16dp</item>
    <item name="cornerSizeBottomLeft">4dp</item>
    <item name="cornerSizeBottomRight">4dp</item>
</style>

<!-- Large rounded (card) -->
<style name="ShapeAppearance.App.LargeRounded" parent="ShapeAppearance.Material3.LargeComponent">
    <item name="cornerFamily">rounded</item>
    <item name="cornerSize">28dp</item>
</style>
```

```kotlin
// Apply programmatically to any view backed by MaterialShapeDrawable
val shapeModel = ShapeAppearanceModel.builder()
    .setTopLeftCorner(CornerFamily.ROUNDED, 24f.dpToPx)
    .setTopRightCorner(CornerFamily.ROUNDED, 24f.dpToPx)
    .setBottomLeftCorner(CornerFamily.CUT, 8f.dpToPx)
    .setBottomRightCorner(CornerFamily.CUT, 8f.dpToPx)
    .build()

val drawable = MaterialShapeDrawable(shapeModel).apply {
    fillColor = ColorStateList.valueOf(
        ContextCompat.getColor(context, R.color.md_theme_primaryContainer)
    )
    elevation = 8f.dpToPx
}
binding.cardView.background = drawable

// ShapeableImageView
binding.ivAvatar.shapeAppearanceModel = shapeModel
```

---

## 10. 9-Patch Drawables

```
# 9-patch rules:
# - Left & top pixels define STRETCH zones
# - Right & bottom pixels define CONTENT padding
# - Use Android Studio's 9-patch tool (double-click .9.png in res/drawable)

# Create programmatically using NinePatchDrawable:
val bitmap = BitmapFactory.decodeResource(resources, R.drawable.my_bubble)
val chunk = bitmap.ninePatchChunk
val ninePatch = NinePatchDrawable(resources, bitmap, chunk, Rect(), null)
binding.tvBubble.background = ninePatch
```

---

# Module 20 — Accessibility & Inclusive Design
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Content Descriptions

```xml
<!-- Descriptive (for non-obvious icons) -->
<ImageButton
    android:contentDescription="Share post"
    android:src="@drawable/ic_share" />

<!-- Decorative (hide from accessibility) -->
<ImageView
    android:contentDescription="@null"
    android:importantForAccessibility="no"
    android:src="@drawable/ic_decorative" />

<!-- Dynamic content descriptions -->
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:id="@+id/fab"
    android:contentDescription="@string/fab_description" />
```

```kotlin
// Update description dynamically
binding.btnFavorite.contentDescription = if (isFavorited)
    "Remove from favorites" else "Add to favorites"

// Announce changes to screen reader
binding.tvStatus.announceForAccessibility("Upload complete")

// Accessibility event
ViewCompat.performAccessibilityAction(binding.root,
    AccessibilityNodeInfoCompat.ACTION_ACCESSIBILITY_FOCUS, null)
```

## 2. Touch Targets

```kotlin
// Extend touch target using TouchDelegate
val parent = binding.btnSmall.parent as View
parent.post {
    val rect = Rect()
    binding.btnSmall.getHitRect(rect)
    rect.inset(-24.dpToPx.toInt(), -24.dpToPx.toInt()) // expand 24dp each side
    parent.touchDelegate = TouchDelegate(rect, binding.btnSmall)
}

// Or use padding to meet 48dp minimum
```

```xml
<!-- Minimum 48dp touch target -->
<ImageButton
    android:layout_width="24dp"
    android:layout_height="24dp"
    android:padding="12dp"
    android:minWidth="48dp"
    android:minHeight="48dp" />
```

## 3. Focus Order

```xml
<!-- Custom focus order for D-pad/keyboard navigation -->
<EditText
    android:id="@+id/etFirst"
    android:nextFocusDown="@id/etSecond"
    android:nextFocusRight="@id/etSecond" />

<EditText
    android:id="@+id/etSecond"
    android:nextFocusUp="@id/etFirst"
    android:nextFocusDown="@id/btnSubmit" />

<!-- Accessibility traversal order -->
<View
    android:accessibilityTraversalBefore="@id/tvSummary"
    android:accessibilityTraversalAfter="@id/tvTitle" />
```

## 4. Color Contrast

```xml
<!-- Always ensure WCAG AA contrast ratio ≥ 4.5:1 for normal text, 3:1 for large -->
<!-- Use Android Studio's Accessibility Scanner or the WCAG contrast checker -->

<!-- Good contrast examples from M3 color system: -->
<!-- Primary (#6750A4) on white: 7.0:1 ✅ -->
<!-- onPrimary (white) on Primary: 7.0:1 ✅ -->
<!-- onSurfaceVariant (#49454F) on Surface (#FFFBFE): 5.9:1 ✅ -->
<!-- NEVER use: gray text on white without checking ratio -->
```

## 5. Semantic Roles

```kotlin
// Set semantic role for custom views
ViewCompat.setAccessibilityDelegate(binding.customCard,
    object : AccessibilityDelegateCompat() {
        override fun onInitializeAccessibilityNodeInfo(
            host: View, info: AccessibilityNodeInfoCompat
        ) {
            super.onInitializeAccessibilityNodeInfo(host, info)
            info.roleDescription = "Card"
            info.isClickable = true
            info.addAction(AccessibilityNodeInfoCompat.AccessibilityActionCompat(
                AccessibilityNodeInfoCompat.ACTION_CLICK, "Open details"
            ))
        }
    }
)

// Live regions for dynamic content
binding.tvStatus.apply {
    ViewCompat.setAccessibilityLiveRegion(this, ViewCompat.ACCESSIBILITY_LIVE_REGION_POLITE)
}
// Now any text change is announced automatically

// Assertive live region (interrupts current speech)
ViewCompat.setAccessibilityLiveRegion(
    binding.tvError, ViewCompat.ACCESSIBILITY_LIVE_REGION_ASSERTIVE
)
```

## 6. TalkBack Testing Checklist

```
✅ Every interactive element has a meaningful contentDescription
✅ All buttons announce their state (checked/unchecked, enabled/disabled)
✅ Form fields describe their purpose AND their error state
✅ Custom views define their role
✅ Focus order is logical (top-to-bottom, left-to-right)
✅ Images that convey info have descriptions; decorative ones are hidden
✅ Color is never the ONLY way to convey information
✅ All text meets minimum contrast ratio
✅ Dynamic content changes are announced via live regions
✅ Loading states are announced ("Loading..." / "Loaded")
```

---

# Module 21 — Architecture Patterns for UI
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. MVVM + StateFlow Pattern

```kotlin
// UiState sealed class — single source of truth
sealed class UiState<out T> {
    object Idle : UiState<Nothing>()
    object Loading : UiState<Nothing>()
    data class Success<T>(val data: T) : UiState<T>()
    data class Error(val message: String, val cause: Throwable? = null) : UiState<Nothing>()
}

// ViewModel
class ItemListViewModel(private val repo: ItemRepository) : ViewModel() {

    private val _state = MutableStateFlow<UiState<List<Item>>>(UiState.Idle)
    val state: StateFlow<UiState<List<Item>>> = _state.asStateFlow()

    private val _effect = Channel<UiEffect>(Channel.BUFFERED)
    val effect = _effect.receiveAsFlow()

    init { loadItems() }

    fun loadItems() {
        viewModelScope.launch {
            _state.value = UiState.Loading
            repo.getItems()
                .onSuccess { _state.value = UiState.Success(it) }
                .onFailure { _state.value = UiState.Error(it.message ?: "Unknown error", it) }
        }
    }

    fun onItemClicked(item: Item) {
        viewModelScope.launch {
            _effect.send(UiEffect.NavigateToDetail(item.id))
        }
    }
}

sealed class UiEffect {
    data class NavigateToDetail(val id: Int) : UiEffect()
    data class ShowSnackbar(val message: String) : UiEffect()
}

// Fragment — observe state
class ItemListFragment : Fragment() {
    private val viewModel: ItemListViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Observe state
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.state.collect { state ->
                    when (state) {
                        is UiState.Idle -> Unit
                        is UiState.Loading -> showLoading()
                        is UiState.Success -> showItems(state.data)
                        is UiState.Error -> showError(state.message)
                    }
                }
            }
        }

        // Observe effects (one-time events)
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.effect.collect { effect ->
                    when (effect) {
                        is UiEffect.NavigateToDetail ->
                            findNavController().navigate(
                                ItemListFragmentDirections.toDetail(effect.id)
                            )
                        is UiEffect.ShowSnackbar ->
                            Snackbar.make(binding.root, effect.message, Snackbar.LENGTH_SHORT).show()
                    }
                }
            }
        }
    }

    private fun showLoading() {
        binding.shimmerLayout.isVisible = true
        binding.shimmerLayout.startShimmer()
        binding.rvItems.isVisible = false
        binding.errorLayout.isVisible = false
    }

    private fun showItems(items: List<Item>) {
        binding.shimmerLayout.stopShimmer()
        binding.shimmerLayout.isVisible = false
        binding.rvItems.isVisible = true
        binding.errorLayout.isVisible = false
        adapter.submitList(items)
    }

    private fun showError(message: String) {
        binding.shimmerLayout.stopShimmer()
        binding.shimmerLayout.isVisible = false
        binding.rvItems.isVisible = false
        binding.errorLayout.isVisible = true
        binding.tvError.text = message
        binding.btnRetry.setOnClickListener { viewModel.loadItems() }
    }
}
```

## 2. Navigation Graph Setup

```xml
<!-- res/navigation/nav_graph.xml -->
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/nav_graph"
    app:startDestination="@id/homeFragment">

    <fragment
        android:id="@+id/homeFragment"
        android:name="com.example.HomeFragment"
        android:label="Home">
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
        android:name="com.example.DetailFragment"
        android:label="Detail">
        <argument
            android:name="itemId"
            app:argType="integer" />
    </fragment>

    <!-- Deep link -->
    <fragment android:id="@+id/profileFragment"
        android:name="com.example.ProfileFragment">
        <deepLink app:uri="myapp://profile/{userId}" />
    </fragment>
</navigation>
```

## 3. Safe Args Navigation

```kotlin
// Fragment to Fragment with type-safe args
// In HomeFragment:
val action = HomeFragmentDirections.actionHomeToDetail(itemId = item.id)
findNavController().navigate(action)

// In DetailFragment:
val args: DetailFragmentArgs by navArgs()
val itemId = args.itemId
```

## 4. ViewBinding + Lifecycle Best Practices

```kotlin
// Base Fragment with safe ViewBinding
abstract class BaseFragment<VB : ViewBinding>(
    private val inflate: (LayoutInflater, ViewGroup?, Boolean) -> VB
) : Fragment() {

    private var _binding: VB? = null
    protected val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = inflate(inflater, container, false)
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}

// Usage
class HomeFragment : BaseFragment<FragmentHomeBinding>(FragmentHomeBinding::inflate) {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        binding.tvTitle.text = "Home"
    }
}
```

---

# Module 22 — Dimens, Spacing & Typography Reference
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Complete dimens.xml Reference

```xml
<!-- res/values/dimens.xml -->
<resources>
    <!-- Spacing scale -->
    <dimen name="spacing_2">2dp</dimen>
    <dimen name="spacing_4">4dp</dimen>
    <dimen name="spacing_8">8dp</dimen>
    <dimen name="spacing_12">12dp</dimen>
    <dimen name="spacing_16">16dp</dimen>
    <dimen name="spacing_20">20dp</dimen>
    <dimen name="spacing_24">24dp</dimen>
    <dimen name="spacing_32">32dp</dimen>
    <dimen name="spacing_40">40dp</dimen>
    <dimen name="spacing_48">48dp</dimen>
    <dimen name="spacing_56">56dp</dimen>
    <dimen name="spacing_64">64dp</dimen>

    <!-- Corner radius scale -->
    <dimen name="corner_none">0dp</dimen>
    <dimen name="corner_xs">4dp</dimen>
    <dimen name="corner_sm">8dp</dimen>
    <dimen name="corner_md">12dp</dimen>
    <dimen name="corner_lg">16dp</dimen>
    <dimen name="corner_xl">20dp</dimen>
    <dimen name="corner_xxl">28dp</dimen>
    <dimen name="corner_full">100dp</dimen>

    <!-- Elevation scale -->
    <dimen name="elevation_0">0dp</dimen>
    <dimen name="elevation_1">1dp</dimen>
    <dimen name="elevation_2">2dp</dimen>
    <dimen name="elevation_4">4dp</dimen>
    <dimen name="elevation_6">6dp</dimen>
    <dimen name="elevation_8">8dp</dimen>
    <dimen name="elevation_12">12dp</dimen>
    <dimen name="elevation_16">16dp</dimen>
    <dimen name="elevation_24">24dp</dimen>

    <!-- Icon sizes -->
    <dimen name="icon_xs">16dp</dimen>
    <dimen name="icon_sm">20dp</dimen>
    <dimen name="icon_md">24dp</dimen>
    <dimen name="icon_lg">32dp</dimen>
    <dimen name="icon_xl">40dp</dimen>
    <dimen name="icon_xxl">48dp</dimen>

    <!-- Avatar sizes -->
    <dimen name="avatar_sm">32dp</dimen>
    <dimen name="avatar_md">40dp</dimen>
    <dimen name="avatar_lg">48dp</dimen>
    <dimen name="avatar_xl">56dp</dimen>
    <dimen name="avatar_story">64dp</dimen>

    <!-- Component heights -->
    <dimen name="toolbar_height">56dp</dimen>
    <dimen name="bottom_nav_height">80dp</dimen>
    <dimen name="fab_size">56dp</dimen>
    <dimen name="fab_size_mini">40dp</dimen>
    <dimen name="input_height">56dp</dimen>
    <dimen name="button_height">40dp</dimen>
    <dimen name="chip_height">32dp</dimen>
    <dimen name="touch_target_min">48dp</dimen>

    <!-- Card dimensions -->
    <dimen name="card_margin">8dp</dimen>
    <dimen name="card_padding">16dp</dimen>
    <dimen name="card_corner">12dp</dimen>
    <dimen name="card_elevation">2dp</dimen>

    <!-- Screen padding -->
    <dimen name="screen_padding_horizontal">16dp</dimen>
    <dimen name="screen_padding_vertical">16dp</dimen>
    <dimen name="content_max_width">840dp</dimen>
</resources>

<!-- res/values-sw600dp/dimens.xml (Tablet overrides) -->
<resources>
    <dimen name="screen_padding_horizontal">24dp</dimen>
    <dimen name="card_margin">12dp</dimen>
</resources>
```

## 2. Typography System Reference

```xml
<!-- res/values/type.xml -->
<resources>
    <!-- Display -->
    <style name="TextAppearance.App.DisplayLarge" parent="TextAppearance.Material3.DisplayLarge">
        <item name="android:textSize">57sp</item>
        <item name="android:letterSpacing">-0.025</item>
        <item name="android:lineHeight">64sp</item>
    </style>
    <style name="TextAppearance.App.DisplayMedium" parent="TextAppearance.Material3.DisplayMedium">
        <item name="android:textSize">45sp</item>
        <item name="android:lineHeight">52sp</item>
    </style>
    <style name="TextAppearance.App.DisplaySmall" parent="TextAppearance.Material3.DisplaySmall">
        <item name="android:textSize">36sp</item>
        <item name="android:lineHeight">44sp</item>
    </style>

    <!-- Headline -->
    <style name="TextAppearance.App.HeadlineLarge" parent="TextAppearance.Material3.HeadlineLarge">
        <item name="android:textSize">32sp</item>
        <item name="android:lineHeight">40sp</item>
    </style>
    <style name="TextAppearance.App.HeadlineMedium" parent="TextAppearance.Material3.HeadlineMedium">
        <item name="android:textSize">28sp</item>
        <item name="android:lineHeight">36sp</item>
    </style>
    <style name="TextAppearance.App.HeadlineSmall" parent="TextAppearance.Material3.HeadlineSmall">
        <item name="android:textSize">24sp</item>
        <item name="android:lineHeight">32sp</item>
    </style>

    <!-- Title -->
    <style name="TextAppearance.App.TitleLarge" parent="TextAppearance.Material3.TitleLarge">
        <item name="android:textSize">22sp</item>
        <item name="android:fontFamily">@font/roboto_medium</item>
    </style>
    <style name="TextAppearance.App.TitleMedium" parent="TextAppearance.Material3.TitleMedium">
        <item name="android:textSize">16sp</item>
        <item name="android:letterSpacing">0.009</item>
    </style>
    <style name="TextAppearance.App.TitleSmall" parent="TextAppearance.Material3.TitleSmall">
        <item name="android:textSize">14sp</item>
        <item name="android:letterSpacing">0.006</item>
    </style>

    <!-- Body -->
    <style name="TextAppearance.App.BodyLarge" parent="TextAppearance.Material3.BodyLarge">
        <item name="android:textSize">16sp</item>
        <item name="android:lineHeight">24sp</item>
        <item name="android:letterSpacing">0.031</item>
    </style>
    <style name="TextAppearance.App.BodyMedium" parent="TextAppearance.Material3.BodyMedium">
        <item name="android:textSize">14sp</item>
        <item name="android:lineHeight">20sp</item>
        <item name="android:letterSpacing">0.016</item>
    </style>
    <style name="TextAppearance.App.BodySmall" parent="TextAppearance.Material3.BodySmall">
        <item name="android:textSize">12sp</item>
        <item name="android:lineHeight">16sp</item>
        <item name="android:letterSpacing">0.033</item>
    </style>

    <!-- Label -->
    <style name="TextAppearance.App.LabelLarge" parent="TextAppearance.Material3.LabelLarge">
        <item name="android:textSize">14sp</item>
        <item name="android:letterSpacing">0.006</item>
        <item name="android:fontFamily">@font/roboto_medium</item>
    </style>
    <style name="TextAppearance.App.LabelMedium" parent="TextAppearance.Material3.LabelMedium">
        <item name="android:textSize">12sp</item>
        <item name="android:letterSpacing">0.042</item>
    </style>
    <style name="TextAppearance.App.LabelSmall" parent="TextAppearance.Material3.LabelSmall">
        <item name="android:textSize">11sp</item>
        <item name="android:letterSpacing">0.045</item>
    </style>
</resources>
```

## 3. Kotlin Extension Functions Reference

```kotlin
// res/utils/Extensions.kt — Utility extensions used throughout this encyclopedia

// dp/sp conversions
val Int.dp: Float get() = this * Resources.getSystem().displayMetrics.density
val Float.dp: Float get() = this * Resources.getSystem().displayMetrics.density
val Int.sp: Float get() = this * Resources.getSystem().displayMetrics.scaledDensity
fun Float.dpToPx(context: Context): Float = this * context.resources.displayMetrics.density
fun Float.dpToPx(): Float = this * Resources.getSystem().displayMetrics.density
val Float.dpToPx: Float get() = this * Resources.getSystem().displayMetrics.density
val Int.dpToPx: Int get() = (this * Resources.getSystem().displayMetrics.density).toInt()

// View visibility
fun View.show() { visibility = View.VISIBLE }
fun View.hide() { visibility = View.GONE }
fun View.invisible() { visibility = View.INVISIBLE }
fun View.isVisible(visible: Boolean) { visibility = if (visible) View.VISIBLE else View.GONE }

// Context helpers
fun Context.colorAttr(@AttrRes attr: Int): Int {
    val typedValue = TypedValue()
    theme.resolveAttribute(attr, typedValue, true)
    return typedValue.data
}

// String extensions
fun String.toEditable(): Editable = Editable.Factory.getInstance().newEditable(this)

// View click with debounce
fun View.setOnSingleClickListener(debounceMs: Long = 600L, action: (View) -> Unit) {
    var lastClickTime = 0L
    setOnClickListener { view ->
        val now = System.currentTimeMillis()
        if (now - lastClickTime >= debounceMs) {
            lastClickTime = now
            action(view)
        }
    }
}

// Coroutines helpers
suspend fun <T> withLoading(
    showLoading: () -> Unit,
    hideLoading: () -> Unit,
    block: suspend () -> T
): T {
    showLoading()
    return try { block() } finally { hideLoading() }
}

// RecyclerView helpers
fun RecyclerView.addVerticalDivider(context: Context) {
    addItemDecoration(MaterialDividerItemDecoration(context, LinearLayoutManager.VERTICAL))
}

fun RecyclerView.scrollToBottomSmooth() {
    adapter?.itemCount?.let { count ->
        if (count > 0) smoothScrollToPosition(count - 1)
    }
}

// ImageView + Glide helpers
fun ImageView.loadCircle(url: String?) {
    Glide.with(this).load(url)
        .apply(RequestOptions.circleCropTransform())
        .placeholder(R.drawable.ic_person_circle)
        .into(this)
}

fun ImageView.loadRounded(url: String?, radiusDp: Float = 12f) {
    Glide.with(this).load(url)
        .apply(RequestOptions.bitmapTransform(RoundedCorners(radiusDp.dpToPx.toInt())))
        .placeholder(R.drawable.placeholder_image)
        .into(this)
}

// Fragment extensions
fun Fragment.showToast(message: String, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(requireContext(), message, duration).show()
}

fun Fragment.showSnackbar(message: String, actionLabel: String? = null, action: (() -> Unit)? = null) {
    Snackbar.make(requireView(), message, Snackbar.LENGTH_SHORT).apply {
        if (actionLabel != null && action != null) setAction(actionLabel) { action() }
    }.show()
}

// Keyboard
fun View.hideKeyboard() {
    val imm = context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
    imm.hideSoftInputFromWindow(windowToken, 0)
}

fun View.showKeyboard() {
    requestFocus()
    val imm = context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
    imm.showSoftInput(this, InputMethodManager.SHOW_IMPLICIT)
}
```

---

# Module 23 — Library Alternatives Reference
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## Image Loading Libraries

| Library | Language | Features | Best For |
|---------|----------|----------|----------|
| **Glide** | Java/Kotlin | Caching, transformations, GIF | General use |
| **Coil** | Kotlin | Coroutines-native, lightweight | Kotlin-first apps |
| **Picasso** | Java/Kotlin | Simple API, lightweight | Simple image loading |
| **Fresco** | Java | Memory management, animated | Facebook-scale apps |

## Chart Libraries

| Library | Stars | Features |
|---------|-------|----------|
| **MPAndroidChart** | 37k+ | Line, Bar, Pie, Radar, Bubble, Candlestick |
| **AnyChart** | 2k+ | 60+ chart types, interactive |
| **HelloCharts** | 7k+ | Line, Column, Pie, Bubble |
| **Vico** | Growing | Compose + Views, modern |

## Animation Libraries

| Library | Use Case |
|---------|----------|
| **Lottie** | JSON animations (Adobe After Effects) |
| **Rebound** | Facebook spring physics |
| **Android Interpolators** | Built-in, no dep needed |
| **Rive** | Real-time interactive animations |

## UI Component Libraries

| Library | Description |
|---------|-------------|
| **Material Components** | Official Google, recommended |
| **Epoxy (Airbnb)** | Complex RecyclerView with multiple view types |
| **Groupie** | Simpler RecyclerView grouping |
| **FlexboxLayout** | CSS Flexbox for Android |
| **Konfetti** | Confetti animations |
| **Shimmer (Facebook)** | Loading shimmer effect |
| **Balloon** | Tooltip/popup balloons |
| **Alerter** | Top alert banners |

## Pagination

| Library | Description |
|---------|-------------|
| **Paging 3** | Official Jetpack, recommended |
| **Endless RecyclerView** | Simple scroll listener approach |

## Bottom Sheets / Dialogs

| Library | Description |
|---------|-------------|
| **BottomSheetDialogFragment** | Built-in, recommended |
| **MaterialDialogs (Afollestad)** | Extended dialog types |
| **Sheets (Maximilian)** | Opinionated sheet styles |

## Image Cropping / Editing

| Library | Description |
|---------|-------------|
| **uCrop** | Image cropping with gestures |
| **Android Image Cropper** | Simple crop |
| **PhotoView** | Zoomable ImageView |
| **Subsampling Scale ImageView** | Large image tiling |

## Dependency Injection

| Library | Description |
|---------|-------------|
| **Hilt** | Official Jetpack, built on Dagger |
| **Koin** | Kotlin DSL, lightweight |
| **Dagger 2** | Full DI, complex |

## Networking (for data-driven UI)

| Library | Description |
|---------|-------------|
| **Retrofit** | Type-safe HTTP, recommended |
| **OkHttp** | Low-level HTTP client |
| **Ktor Client** | Kotlin-multiplatform |

---

## Complete Gradle Dependencies Reference

```gradle
// app/build.gradle — All libraries referenced in this encyclopedia

dependencies {
    // Core Android
    implementation 'androidx.core:core-ktx:1.13.1'
    implementation 'androidx.appcompat:appcompat:1.7.0'
    implementation 'androidx.activity:activity-ktx:1.9.0'
    implementation 'androidx.fragment:fragment-ktx:1.8.0'

    // Material Design 3
    implementation 'com.google.android.material:material:1.12.0'

    // Layouts
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'com.google.android.flexbox:flexbox:3.0.0'

    // RecyclerView & ViewPager
    implementation 'androidx.recyclerview:recyclerview:1.3.2'
    implementation 'androidx.viewpager2:viewpager2:1.1.0'
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'

    // Navigation
    implementation 'androidx.navigation:navigation-fragment-ktx:2.7.7'
    implementation 'androidx.navigation:navigation-ui-ktx:2.7.7'

    // Lifecycle + ViewModel
    implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.8.2'
    implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.8.2'
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.8.2'

    // Paging 3
    implementation 'androidx.paging:paging-runtime-ktx:3.3.0'

    // Preferences / Settings
    implementation 'androidx.preference:preference-ktx:1.2.1'

    // Biometric
    implementation 'androidx.biometric:biometric:1.2.0-alpha05'

    // Media3 / ExoPlayer
    implementation 'androidx.media3:media3-exoplayer:1.3.1'
    implementation 'androidx.media3:media3-ui:1.3.1'

    // Spring Animations
    implementation 'androidx.dynamicanimation:dynamicanimation-ktx:1.0.0-alpha03'

    // WindowManager (foldables, size classes)
    implementation 'androidx.window:window:1.3.0'
    implementation 'androidx.window:window-java:1.3.0'

    // Image Loading
    implementation 'com.github.bumptech.glide:glide:4.16.0'
    kapt 'com.github.bumptech.glide:compiler:4.16.0'
    // OR Coil
    implementation 'io.coil-kt:coil:2.6.0'
    implementation 'io.coil-kt:coil-svg:2.6.0'

    // Animations
    implementation 'com.airbnb.android:lottie:6.4.0'

    // Shimmer
    implementation 'com.facebook.shimmer:shimmer:0.5.0'

    // Charts
    implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'

    // Networking
    implementation 'com.squareup.retrofit2:retrofit:2.11.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.11.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:4.12.0'

    // Coroutines
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.8.0'
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.8.0'

    // Hilt DI
    implementation 'com.google.dagger:hilt-android:2.51'
    kapt 'com.google.dagger:hilt-compiler:2.51'

    // Room (for offline / local data)
    implementation 'androidx.room:room-runtime:2.6.1'
    implementation 'androidx.room:room-ktx:2.6.1'
    kapt 'androidx.room:room-compiler:2.6.1'

    // DataStore (replace SharedPreferences)
    implementation 'androidx.datastore:datastore-preferences:1.1.1'

    // Testing
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.6.1'
    androidTestImplementation 'androidx.test.ext:junit:1.2.1'
}
```
