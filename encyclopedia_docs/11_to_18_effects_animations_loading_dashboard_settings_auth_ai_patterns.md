# Module 11 — Visual Effects
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Blur Effect (API 31+ RenderEffect)

```kotlin
// Native blur — Android 12+
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    binding.viewToBlur.setRenderEffect(
        RenderEffect.createBlurEffect(25f, 25f, Shader.TileMode.CLAMP)
    )
    // Remove blur
    binding.viewToBlur.setRenderEffect(null)
}

// Pre-API 31 blur via Glide (blur a bitmap)
Glide.with(this)
    .load(imageUrl)
    .apply(RequestOptions.bitmapTransform(BlurTransformation(25, 3)))
    .into(binding.ivBlurred)
```

## 2. Frosted Glass / Glassmorphism

```kotlin
// Full glassmorphism setup
class GlassmorphismView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : FrameLayout(context, attrs) {

    private val backgroundPaint = Paint().apply {
        color = Color.argb(60, 255, 255, 255) // 24% white
    }
    private val strokePaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.argb(80, 255, 255, 255)
        style = Paint.Style.STROKE
        strokeWidth = 1f.dpToPx(context)
    }
    private val cornerRadius = 20f.dpToPx(context)

    init {
        setWillNotDraw(false)
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            background = null
            setRenderEffect(
                RenderEffect.createBlurEffect(20f, 20f, Shader.TileMode.CLAMP)
            )
        }
    }

    override fun onDraw(canvas: Canvas) {
        // Draw frosted background
        val rect = RectF(0f, 0f, width.toFloat(), height.toFloat())
        canvas.drawRoundRect(rect, cornerRadius, cornerRadius, backgroundPaint)
        canvas.drawRoundRect(rect, cornerRadius, cornerRadius, strokePaint)
        super.onDraw(canvas)
    }
}
```

## 3. Gradient Backgrounds

```xml
<!-- res/drawable/gradient_primary.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:startColor="#6750A4"
        android:endColor="#9A82DB"
        android:angle="135"
        android:type="linear" />
    <corners android:radius="16dp" />
</shape>

<!-- Radial gradient -->
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:startColor="#80000000"
        android:endColor="#00000000"
        android:gradientRadius="200dp"
        android:type="radial"
        android:centerX="0.5"
        android:centerY="0.5" />
</shape>
```

```kotlin
// Dynamic gradient drawable
fun createGradient(vararg colors: Int): GradientDrawable {
    return GradientDrawable(GradientDrawable.Orientation.TL_BR, colors).apply {
        cornerRadius = 16f.dpToPx
        gradientType = GradientDrawable.LINEAR_GRADIENT
    }
}
binding.cardHeader.background = createGradient(
    ContextCompat.getColor(context, R.color.md_theme_primary),
    ContextCompat.getColor(context, R.color.md_theme_tertiary)
)
```

## 4. Elevation & Shadows

```xml
<!-- Material elevation via cardElevation -->
<com.google.android.material.card.MaterialCardView
    app:cardElevation="8dp"
    app:cardMaxElevation="12dp" />

<!-- View elevation (API 21+) -->
<View
    android:elevation="4dp"
    android:outlineProvider="background"
    android:stateListAnimator="@animator/elevation_on_press" />
```

```xml
<!-- res/animator/elevation_on_press.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <objectAnimator android:propertyName="translationZ"
            android:valueTo="8dp" android:duration="100" android:valueType="floatType" />
    </item>
    <item>
        <objectAnimator android:propertyName="translationZ"
            android:valueTo="2dp" android:duration="100" android:valueType="floatType" />
    </item>
</selector>
```

## 5. Ripple Effect (Custom)

```xml
<!-- res/drawable/ripple_primary.xml -->
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorPrimary">
    <item android:id="@android:id/mask">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <corners android:radius="12dp" />
        </shape>
    </item>
</ripple>
```

## 6. Parallax Scroll Effect

```kotlin
// Parallax on RecyclerView scroll
binding.recyclerView.addOnScrollListener(object : RecyclerView.OnScrollListener() {
    override fun onScrolled(rv: RecyclerView, dx: Int, dy: Int) {
        val scrollY = rv.computeVerticalScrollOffset()
        // Move header image at 50% speed (parallax)
        binding.ivHeader.translationY = scrollY * 0.5f
        // Fade header text as scroll increases
        binding.tvHeaderTitle.alpha = 1f - (scrollY / 300f).coerceIn(0f, 1f)
    }
})
```

## 4. Neumorphism Effect (Drawable + Programmatic)

**Visual Use Case:** Soft UI design, calculator buttons, music player, dark-free interfaces.

```xml
<!-- res/drawable/neumorph_raised.xml — raised state -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Dark shadow: offset bottom-right -->
    <item android:left="6dp" android:top="6dp">
        <shape android:shape="rectangle">
            <solid android:color="#C8D0DC" />
            <corners android:radius="12dp" />
        </shape>
    </item>
    <!-- Light shadow: offset top-left -->
    <item android:right="6dp" android:bottom="6dp">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <corners android:radius="12dp" />
        </shape>
    </item>
    <!-- Main surface -->
    <item>
        <shape android:shape="rectangle">
            <solid android:color="#E0E5EC" />
            <corners android:radius="12dp" />
        </shape>
    </item>
</layer-list>
```

```xml
<!-- res/drawable/neumorph_pressed.xml — inset/concave pressed state -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:right="4dp" android:bottom="4dp">
        <shape android:shape="rectangle">
            <solid android:color="#C8D0DC" />
            <corners android:radius="12dp" />
        </shape>
    </item>
    <item android:left="4dp" android:top="4dp">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <corners android:radius="12dp" />
        </shape>
    </item>
    <item>
        <shape android:shape="rectangle">
            <solid android:color="#D6DCE8" />
            <corners android:radius="12dp" />
        </shape>
    </item>
</layer-list>
```

```xml
<!-- res/drawable/neumorph_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/neumorph_pressed" android:state_pressed="true" />
    <item android:drawable="@drawable/neumorph_raised" />
</selector>
```

```xml
<!-- Apply to any View -->
<TextView
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    android:padding="16dp" android:text="Neumorphic"
    android:background="@drawable/neumorph_selector" />
```

```kotlin
// Programmatic neumorphism with BlurMaskFilter (requires software layer)
class NeumorphView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : View(context, attrs) {

    private val surface = Color.parseColor("#E0E5EC")

    private val darkPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.parseColor("#A3B1C6")
        maskFilter = BlurMaskFilter(20f, BlurMaskFilter.Blur.NORMAL)
    }
    private val lightPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = Color.WHITE
        maskFilter = BlurMaskFilter(20f, BlurMaskFilter.Blur.NORMAL)
    }
    private val bgPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply { color = surface }

    init { setLayerType(LAYER_TYPE_SOFTWARE, null) }

    override fun onDraw(canvas: Canvas) {
        val r = 24f
        val rect = RectF(0f, 0f, width.toFloat(), height.toFloat())
        canvas.drawRoundRect(RectF(rect).apply { offset(8f, 8f) }, r, r, darkPaint)
        canvas.drawRoundRect(RectF(rect).apply { offset(-8f, -8f) }, r, r, lightPaint)
        canvas.drawRoundRect(rect, r, r, bgPaint)
    }
}
```

### Best Practices
- ✅ Works best on a mid-gray background (#E0E5EC). Avoid on dark themes.
- ✅ Use `setLayerType(LAYER_TYPE_SOFTWARE, null)` for `BlurMaskFilter` to work.
- ✅ Always provide a pressed/inset variant for tactile feedback.

---

## 5. Particle Burst Effect (Canvas + ValueAnimator)

**Visual Use Case:** Celebration screens, success state, confetti on completion.

```kotlin
class ParticleView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : View(context, attrs) {

    data class Particle(
        var x: Float, var y: Float, var vx: Float, var vy: Float,
        var radius: Float, var color: Int, var life: Float = 1f
    )

    private val particles = mutableListOf<Particle>()
    private val paint = Paint(Paint.ANTI_ALIAS_FLAG)
    private val rng = Random()
    private val colors = intArrayOf(
        Color.parseColor("#E91E63"), Color.parseColor("#9C27B0"),
        Color.parseColor("#2196F3"), Color.parseColor("#4CAF50"),
        Color.parseColor("#FF9800")
    )

    init { setLayerType(LAYER_TYPE_HARDWARE, null) }

    fun burst(count: Int = 60) {
        val cx = width / 2f; val cy = height / 2f
        repeat(count) {
            val angle = rng.nextFloat() * 2 * Math.PI
            val speed = rng.nextFloat() * 10 + 3
            particles += Particle(
                x = cx, y = cy,
                vx = (Math.cos(angle) * speed).toFloat(),
                vy = (Math.sin(angle) * speed).toFloat(),
                radius = rng.nextFloat() * 8 + 4,
                color = colors[rng.nextInt(colors.size)]
            )
        }
        ValueAnimator.ofFloat(0f, 1f).apply {
            duration = 1800
            addUpdateListener { update(); invalidate() }
            addListener(onEnd = { particles.clear(); invalidate() })
            start()
        }
    }

    private fun update() {
        val it = particles.iterator()
        while (it.hasNext()) {
            val p = it.next()
            p.x += p.vx; p.y += p.vy; p.vy += 0.25f
            p.life -= 0.018f
            if (p.life <= 0f) it.remove()
        }
    }

    override fun onDraw(canvas: Canvas) {
        particles.forEach { p ->
            paint.color = p.color
            paint.alpha = (p.life * 255).toInt().coerceIn(0, 255)
            canvas.drawCircle(p.x, p.y, p.radius * p.life, paint)
        }
    }
}
```

```xml
<!-- Add to layout -->
<com.yourpackage.ParticleView
    android:id="@+id/particleView"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

```kotlin
binding.btnSuccess.setOnClickListener { binding.particleView.burst(80) }
```

---

## 6. Glow / Neon Effect (BlurMaskFilter + RenderEffect)

**Visual Use Case:** Dark-themed apps, gaming UI, highlighted selection, neon text.

```xml
<!-- Neon text via XML shadow (works on all API levels) -->
<TextView
    android:layout_width="wrap_content" android:layout_height="wrap_content"
    android:text="NEON"
    android:textColor="#00E5FF"
    android:textSize="48sp" android:textStyle="bold"
    android:shadowColor="#00E5FF"
    android:shadowDx="0" android:shadowDy="0"
    android:shadowRadius="20" />
```

```kotlin
// API 31+ hardware-accelerated glow on any View
fun View.applyGlow(glowColor: Int, radius: Float = 20f) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
        setRenderEffect(
            RenderEffect.createBlurEffect(radius, radius, Shader.TileMode.CLAMP)
        )
    }
}

// Pulsing glow animation (all API)
fun View.startGlowPulse(glowColor: Int) {
    setLayerType(View.LAYER_TYPE_SOFTWARE, null)
    val paint = Paint().apply {
        color = glowColor
        maskFilter = BlurMaskFilter(30f, BlurMaskFilter.Blur.NORMAL)
    }
    ValueAnimator.ofFloat(10f, 50f).apply {
        duration = 900
        repeatMode = ValueAnimator.REVERSE
        repeatCount = ValueAnimator.INFINITE
        addUpdateListener { anim ->
            paint.maskFilter = BlurMaskFilter(
                anim.animatedValue as Float, BlurMaskFilter.Blur.NORMAL
            )
            invalidate()
        }
        start()
    }
}
```

```kotlin
// Custom pulsing glow view
class GlowCircleView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : View(context, attrs) {

    private var glowRadius = 30f
    private val glowColor = Color.parseColor("#00E5FF")

    private val glowPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {
        color = glowColor
        maskFilter = BlurMaskFilter(glowRadius, BlurMaskFilter.Blur.NORMAL)
    }
    private val solidPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply { color = glowColor }

    init {
        setLayerType(LAYER_TYPE_SOFTWARE, null)
        ValueAnimator.ofFloat(15f, 45f).apply {
            duration = 900; repeatMode = ValueAnimator.REVERSE
            repeatCount = ValueAnimator.INFINITE
            addUpdateListener {
                glowRadius = it.animatedValue as Float
                glowPaint.maskFilter = BlurMaskFilter(glowRadius, BlurMaskFilter.Blur.NORMAL)
                invalidate()
            }
            start()
        }
    }

    override fun onDraw(canvas: Canvas) {
        val cx = width / 2f; val cy = height / 2f
        val r  = minOf(cx, cy) - glowRadius
        canvas.drawCircle(cx, cy, r, glowPaint)
        canvas.drawCircle(cx, cy, r * 0.55f, solidPaint)
    }
}
```

---

## 7. Dynamic Color (Material You — API 31+)

**Visual Use Case:** Apps that theme themselves from wallpaper color on Android 12+.

```gradle
implementation 'com.google.android.material:material:1.12.0'
```

```kotlin
// Application class — apply to all activities automatically
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        DynamicColors.applyToActivitiesIfAvailable(this)
    }
}
```

```xml
<!-- AndroidManifest.xml -->
<application android:name=".MyApp" ...>
```

```xml
<!-- res/values/themes.xml — use Material3 theme; Dynamic Color activates on API 31+ automatically -->
<style name="AppTheme" parent="Theme.Material3.DayNight.NoActionBar">
    <!-- Fallback static colors for API < 31 -->
    <item name="colorPrimary">@color/md_theme_primary</item>
    <item name="colorSecondary">@color/md_theme_secondary</item>
</style>
```

```kotlin
// Per-activity override (before super.onCreate)
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        DynamicColors.applyToActivityIfAvailable(this)
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
    }
}
```

```kotlin
// Read dynamic color slots at runtime (API 31+)
fun getDynamicPrimary(context: Context): Int =
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S)
        context.getColor(android.R.color.system_accent1_500)
    else ContextCompat.getColor(context, R.color.md_theme_primary)

// Dynamic color system slots reference:
// system_accent1_0 .. system_accent1_1000  → primary tones
// system_accent2_0 .. system_accent2_1000  → secondary tones
// system_accent3_0 .. system_accent3_1000  → tertiary tones
// system_neutral1_*                         → neutral tones
// system_neutral2_*                         → neutral variant tones
```

### Best Practices
- ✅ Always provide static fallback colors — Dynamic Color only activates on API 31+
- ✅ Use `?attr/colorPrimary` in XML; it resolves to dynamic or static automatically
- ✅ Test with different wallpapers in the emulator to verify contrast

---

# Module 12 — Animation Systems
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. ViewPropertyAnimator

```kotlin
// Fade in
view.alpha = 0f
view.animate().alpha(1f).setDuration(300).start()

// Scale pulse
view.animate()
    .scaleX(1.1f).scaleY(1.1f)
    .setDuration(150)
    .withEndAction {
        view.animate().scaleX(1f).scaleY(1f).setDuration(150).start()
    }.start()

// Slide up from bottom
view.translationY = view.height.toFloat()
view.animate()
    .translationY(0f)
    .setDuration(350)
    .setInterpolator(DecelerateInterpolator())
    .start()

// Chain animations
view.animate()
    .alpha(0f).setDuration(200)
    .withEndAction {
        view.text = "New Content"
        view.animate().alpha(1f).setDuration(200).start()
    }.start()
```

## 2. Spring Animations

```kotlin
// Spring-based bounce animation
val springAnim = SpringAnimation(binding.fab, DynamicAnimation.SCALE_X, 1f).apply {
    spring.dampingRatio = SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY
    spring.stiffness = SpringForce.STIFFNESS_LOW
}

binding.fab.scaleX = 0f
springAnim.start()

// Spring with velocity (e.g., after fling)
val springY = SpringAnimation(binding.card, DynamicAnimation.TRANSLATION_Y).apply {
    spring.dampingRatio = SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY
    spring.stiffness = SpringForce.STIFFNESS_MEDIUM
    spring.finalPosition = 0f
}
springY.animateToFinalPosition(0f)
```

## 3. Shared Element Transitions

```kotlin
// Fragment A — start transition
val extras = FragmentNavigatorExtras(
    binding.ivThumbnail to "shared_image",
    binding.tvTitle to "shared_title"
)
findNavController().navigate(
    R.id.action_list_to_detail,
    bundleOf("itemId" to item.id),
    null,
    extras
)

// Fragment B — receive transition
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    sharedElementEnterTransition = TransitionInflater.from(context)
        .inflateTransition(android.R.transition.move)
}

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    ViewCompat.setTransitionName(binding.ivDetail, "shared_image")
    ViewCompat.setTransitionName(binding.tvDetailTitle, "shared_title")

    // Postpone until image is loaded
    postponeEnterTransition()
    Glide.with(this)
        .load(item.imageUrl)
        .listener(object : RequestListener<Drawable> {
            override fun onResourceReady(...): Boolean {
                startPostponedEnterTransition()
                return false
            }
            override fun onLoadFailed(...): Boolean {
                startPostponedEnterTransition()
                return false
            }
        })
        .into(binding.ivDetail)
}
```

## 4. Lottie Animations

```gradle
implementation 'com.airbnb.android:lottie:6.4.0'
```

```xml
<com.airbnb.lottie.LottieAnimationView
    android:id="@+id/lottieLoading"
    android:layout_width="120dp"
    android:layout_height="120dp"
    app:lottie_fileName="loading.json"
    app:lottie_autoPlay="true"
    app:lottie_loop="true" />
```

```kotlin
// Play once then hide
binding.lottieSuccess.apply {
    playAnimation()
    addAnimatorListener(object : Animator.AnimatorListener {
        override fun onAnimationEnd(animation: Animator) {
            isVisible = false
        }
        override fun onAnimationStart(animation: Animator) {}
        override fun onAnimationCancel(animation: Animator) {}
        override fun onAnimationRepeat(animation: Animator) {}
    })
}

// Control playback
binding.lottiePulse.apply {
    speed = 1.5f
    playAnimation()
    pauseAnimation()
    resumeAnimation()
    cancelAnimation()
    progress = 0.5f // seek to 50%
}

// Load from URL
binding.lottieView.setAnimationFromUrl("https://assets.example.com/animation.json")
```

## 5. Vector Animations (AnimatedVectorDrawable)

```xml
<!-- res/drawable/avd_play_to_pause.xml -->
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/vd_play">
    <target android:name="triangle"
        android:animation="@animator/play_to_pause_morph" />
</animated-vector>
```

```kotlin
val avd = AnimatedVectorDrawableCompat.create(context, R.drawable.avd_play_to_pause)
binding.ivPlayPause.setImageDrawable(avd)
avd?.start()

// Toggle
var isPlaying = false
binding.btnPlayPause.setOnClickListener {
    isPlaying = !isPlaying
    val drawableRes = if (isPlaying) R.drawable.avd_play_to_pause else R.drawable.avd_pause_to_play
    val newAvd = AnimatedVectorDrawableCompat.create(context, drawableRes)
    binding.ivPlayPause.setImageDrawable(newAvd)
    newAvd?.start()
}
```

## 6. Page Transformations (ViewPager2)

```kotlin
// Cube rotation
class CubeTransformer : ViewPager2.PageTransformer {
    override fun transformPage(page: View, position: Float) {
        page.pivotX = if (position < 0) page.width.toFloat() else 0f
        page.pivotY = page.height * 0.5f
        page.rotationY = 90f * position
    }
}

// Fade + scale
class FadeScaleTransformer : ViewPager2.PageTransformer {
    override fun transformPage(page: View, position: Float) {
        val absPos = Math.abs(position)
        page.alpha = 1 - absPos
        page.scaleX = 1 - 0.2f * absPos
        page.scaleY = 1 - 0.2f * absPos
    }
}
binding.viewPager.setPageTransformer(FadeScaleTransformer())
```

## 7. Physics-Based Animations (SpringAnimation + FlingAnimation)

**Description:** Natural feel for drag, throw, bounce using `DynamicAnimation`.

```gradle
implementation 'androidx.dynamicanimation:dynamicanimation:1.0.0'
```

```kotlin
// Spring — bounce card to rest position
fun springTo(view: View, targetY: Float = 0f) {
    SpringAnimation(view, DynamicAnimation.TRANSLATION_Y, targetY).apply {
        spring.dampingRatio = SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY  // 0.5
        spring.stiffness   = SpringForce.STIFFNESS_LOW                 // 200
    }.start()
}

// Fling — throw a view with velocity, then spring back
fun flingThenSpring(view: View, startVelocityY: Float) {
    FlingAnimation(view, DynamicAnimation.TRANSLATION_Y).apply {
        setStartVelocity(startVelocityY)
        setMinValue(-view.height.toFloat())
        setMaxValue(view.height.toFloat())
        friction = 1.2f
        addEndListener { _, _, _, _ -> springTo(view) }
    }.start()
}
```

```kotlin
// Full draggable view: drag by touch → fling → spring back to origin
class PhysicsDragView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : FrameLayout(context, attrs) {

    private val velocityTracker = VelocityTracker.obtain()
    private var downX = 0f; private var downY = 0f

    private val springX = SpringAnimation(this, DynamicAnimation.TRANSLATION_X, 0f).apply {
        spring.dampingRatio = SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY
        spring.stiffness    = SpringForce.STIFFNESS_MEDIUM
    }
    private val springY = SpringAnimation(this, DynamicAnimation.TRANSLATION_Y, 0f).apply {
        spring.dampingRatio = SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY
        spring.stiffness    = SpringForce.STIFFNESS_MEDIUM
    }

    override fun onTouchEvent(event: MotionEvent): Boolean {
        velocityTracker.addMovement(event)
        when (event.actionMasked) {
            MotionEvent.ACTION_DOWN -> {
                springX.cancel(); springY.cancel()
                downX = event.rawX - translationX
                downY = event.rawY - translationY
            }
            MotionEvent.ACTION_MOVE -> {
                translationX = event.rawX - downX
                translationY = event.rawY - downY
            }
            MotionEvent.ACTION_UP, MotionEvent.ACTION_CANCEL -> {
                velocityTracker.computeCurrentVelocity(1000)
                val vx = velocityTracker.xVelocity
                val vy = velocityTracker.yVelocity

                if (kotlin.math.abs(vx) > 300 || kotlin.math.abs(vy) > 300) {
                    FlingAnimation(this, DynamicAnimation.TRANSLATION_X).apply {
                        setStartVelocity(vx); friction = 2f
                        addEndListener { _,_,_,_ -> springX.start() }
                    }.start()
                    FlingAnimation(this, DynamicAnimation.TRANSLATION_Y).apply {
                        setStartVelocity(vy); friction = 2f
                        addEndListener { _,_,_,_ -> springY.start() }
                    }.start()
                } else {
                    springX.start(); springY.start()
                }
            }
        }
        return true
    }
}
```

```java
// Java — spring animation
SpringAnimation spring = new SpringAnimation(view, DynamicAnimation.TRANSLATION_Y, 0f);
spring.getSpring()
    .setDampingRatio(SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY)
    .setStiffness(SpringForce.STIFFNESS_LOW);
spring.start();

// Java — fling animation
FlingAnimation fling = new FlingAnimation(view, DynamicAnimation.TRANSLATION_Y);
fling.setStartVelocity(2000f).setFriction(1.1f);
fling.start();
```

### Physics Constants Reference
| Constant | Value | Feel |
|---------|-------|------|
| `DAMPING_RATIO_NO_BOUNCY` | 1.0 | Smooth, no bounce |
| `DAMPING_RATIO_LOW_BOUNCY` | 0.75 | Slight bounce |
| `DAMPING_RATIO_MEDIUM_BOUNCY` | 0.5 | Natural bounce |
| `DAMPING_RATIO_HIGH_BOUNCY` | 0.2 | Strong bounce |
| `STIFFNESS_VERY_LOW` | 50 | Very slow spring |
| `STIFFNESS_LOW` | 200 | Slow spring |
| `STIFFNESS_MEDIUM` | 1500 | Normal speed |
| `STIFFNESS_HIGH` | 10000 | Snappy |

---

# Module 13 — Loading Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Shimmer Effect

```gradle
implementation 'com.facebook.shimmer:shimmer:0.5.0'
```

```xml
<com.facebook.shimmer.ShimmerFrameLayout
    android:id="@+id/shimmerLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:shimmer_duration="1000"
    app:shimmer_angle="20">

    <!-- Skeleton placeholder layout -->
    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Repeat skeleton items -->
        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
        <include layout="@layout/skeleton_item_card" />
    </LinearLayout>
</com.facebook.shimmer.ShimmerFrameLayout>
```

```xml
<!-- res/layout/skeleton_item_card.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp">

    <!-- Avatar placeholder -->
    <View android:layout_width="48dp"
        android:layout_height="48dp"
        android:background="@drawable/skeleton_circle" />

    <LinearLayout android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginStart="12dp"
        android:orientation="vertical">

        <!-- Title placeholder -->
        <View android:layout_width="match_parent"
            android:layout_height="14dp"
            android:layout_marginBottom="8dp"
            android:background="@drawable/skeleton_rect" />

        <!-- Subtitle placeholder -->
        <View android:layout_width="160dp"
            android:layout_height="12dp"
            android:background="@drawable/skeleton_rect" />
    </LinearLayout>
</LinearLayout>
```

```xml
<!-- res/drawable/skeleton_rect.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="#E0E0E0" />
    <corners android:radius="4dp" />
</shape>

<!-- res/drawable/skeleton_circle.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="#E0E0E0" />
</shape>
```

```kotlin
// Show shimmer while loading
binding.shimmerLayout.startShimmer()
binding.shimmerLayout.isVisible = true
binding.contentLayout.isVisible = false

// Hide shimmer when data arrives
viewModel.data.observe(viewLifecycleOwner) { data ->
    binding.shimmerLayout.stopShimmer()
    binding.shimmerLayout.isVisible = false
    binding.contentLayout.isVisible = true
    adapter.submitList(data)
}
```

## 2. Skeleton Screen (Manual)

```kotlin
// Show skeleton items in adapter before real data
sealed class ListContent<out T> {
    object Loading : ListContent<Nothing>()
    data class Success<T>(val data: List<T>) : ListContent<T>()
    data class Error(val msg: String) : ListContent<Nothing>()
}

class SmartAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
    private var state: ListContent<Item> = ListContent.Loading

    companion object {
        const val TYPE_SKELETON = 0
        const val TYPE_ITEM = 1
    }

    override fun getItemViewType(position: Int) = when (state) {
        is ListContent.Loading -> TYPE_SKELETON
        else -> TYPE_ITEM
    }

    override fun getItemCount() = when (val s = state) {
        is ListContent.Loading -> 6 // show 6 skeleton items
        is ListContent.Success -> s.data.size
        is ListContent.Error -> 0
    }

    fun setState(newState: ListContent<Item>) {
        state = newState
        notifyDataSetChanged()
    }
}
```

## 4. Pagination Loader (Paging 3 — Infinite Scroll)

```gradle
implementation 'androidx.paging:paging-runtime-ktx:3.3.0'
```

```kotlin
// PagingSource
class ItemPagingSource(private val api: ApiService) : PagingSource<Int, Item>() {

    override fun getRefreshKey(state: PagingState<Int, Item>): Int? =
        state.anchorPosition?.let { anchor ->
            state.closestPageToPosition(anchor)?.prevKey?.plus(1)
                ?: state.closestPageToPosition(anchor)?.nextKey?.minus(1)
        }

    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Item> {
        val page = params.key ?: 1
        return try {
            val response = api.getItems(page = page, pageSize = params.loadSize)
            LoadResult.Page(
                data     = response.items,
                prevKey  = if (page == 1) null else page - 1,
                nextKey  = if (response.items.isEmpty()) null else page + 1
            )
        } catch (e: Exception) { LoadResult.Error(e) }
    }
}

// ViewModel
class ItemViewModel : ViewModel() {
    val items: Flow<PagingData<Item>> = Pager(
        config = PagingConfig(pageSize = 20, enablePlaceholders = false, prefetchDistance = 5),
        pagingSourceFactory = { ItemPagingSource(ApiService.create()) }
    ).flow.cachedIn(viewModelScope)
}

// Adapter
class ItemPagingAdapter : PagingDataAdapter<Item, ItemPagingAdapter.VH>(ItemDiff()) {
    inner class VH(val b: ItemRowBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) =
        VH(ItemRowBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        getItem(position)?.let { holder.b.tvTitle.text = it.title }
    }
}

class ItemDiff : DiffUtil.ItemCallback<Item>() {
    override fun areItemsTheSame(old: Item, new: Item) = old.id == new.id
    override fun areContentsTheSame(old: Item, new: Item) = old == new
}

// Fragment wiring
val adapter = ItemPagingAdapter()
binding.recyclerView.adapter = adapter.withLoadStateFooter(
    footer = LoadStateFooterAdapter { adapter.retry() }
)

lifecycleScope.launch {
    viewModel.items.collectLatest { adapter.submitData(it) }
}

// Show/hide full-screen loading
lifecycleScope.launch {
    adapter.loadStateFlow.collectLatest { states ->
        binding.progressBar.isVisible = states.refresh is LoadState.Loading
        if (states.refresh is LoadState.Error) {
            val error = (states.refresh as LoadState.Error).error
            Snackbar.make(binding.root, error.message ?: "Error", Snackbar.LENGTH_LONG)
                .setAction("Retry") { adapter.retry() }.show()
        }
    }
}
```

```kotlin
// Load state footer adapter
class LoadStateFooterAdapter(
    private val retry: () -> Unit
) : LoadStateAdapter<LoadStateFooterAdapter.VH>() {

    inner class VH(val b: ItemLoadStateBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, loadState: LoadState) =
        VH(ItemLoadStateBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, loadState: LoadState) {
        holder.b.progressBar.isVisible = loadState is LoadState.Loading
        holder.b.btnRetry.isVisible    = loadState is LoadState.Error
        holder.b.btnRetry.setOnClickListener { retry() }
    }
}
```

---

## 5. Lazy Loading Images (Glide — Deferred, Pause on Fast Scroll)

```gradle
implementation 'com.github.bumptech.glide:glide:4.16.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.16.0'  // Java
ksp 'com.github.bumptech.glide:ksp:4.16.0'                       // Kotlin KSP
```

```kotlin
// Extension helper
fun ImageView.loadImage(url: String?, @DrawableRes placeholder: Int = R.drawable.ic_image_placeholder) {
    Glide.with(this)
        .load(url)
        .placeholder(placeholder)
        .error(R.drawable.ic_broken_image)
        .transition(DrawableTransitionOptions.withCrossFade(300))
        .into(this)
}

fun ImageView.loadCircle(url: String?) {
    Glide.with(this).load(url).circleCrop()
        .placeholder(R.drawable.ic_person_placeholder)
        .transition(DrawableTransitionOptions.withCrossFade())
        .into(this)
}

// In RecyclerView adapter
override fun onBindViewHolder(holder: VH, position: Int) {
    holder.binding.ivThumb.loadImage(items[position].imageUrl)
    holder.binding.ivAvatar.loadCircle(items[position].authorAvatar)
}

// Pause Glide on fast fling to reduce jank
binding.recyclerView.addOnScrollListener(object : RecyclerView.OnScrollListener() {
    override fun onScrollStateChanged(rv: RecyclerView, newState: Int) {
        val glide = Glide.with(requireContext())
        if (newState == RecyclerView.SCROLL_STATE_SETTLING) glide.pauseRequests()
        else glide.resumeRequests()
    }
})
```

```java
// Java
Glide.with(holder.itemView.getContext())
    .load(item.getImageUrl())
    .placeholder(R.drawable.ic_image_placeholder)
    .error(R.drawable.ic_broken_image)
    .transition(DrawableTransitionOptions.withCrossFade())
    .into(holder.binding.ivThumb);
```

---

## 6. Pull-to-Refresh (SwipeRefreshLayout + ViewModel)

```gradle
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'
```

```xml
<androidx.swiperefreshlayout.widget.SwipeRefreshLayout
    android:id="@+id/swipeRefresh"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</androidx.swiperefreshlayout.widget.SwipeRefreshLayout>
```

```kotlin
// Set color scheme to match theme
binding.swipeRefresh.setColorSchemeColors(
    ContextCompat.getColor(requireContext(), R.color.md_theme_primary),
    ContextCompat.getColor(requireContext(), R.color.md_theme_secondary)
)

binding.swipeRefresh.setOnRefreshListener { viewModel.refresh() }

// Auto-dismiss spinner when done
viewModel.isLoading.observe(viewLifecycleOwner) { binding.swipeRefresh.isRefreshing = it }
```

```kotlin
// ViewModel
class MyViewModel : ViewModel() {
    private val _isLoading = MutableLiveData(false)
    val isLoading: LiveData<Boolean> = _isLoading

    fun refresh() {
        _isLoading.value = true
        viewModelScope.launch {
            try { _items.value = repository.fetchFresh() }
            finally { _isLoading.value = false }
        }
    }
}
```

```java
// Java
binding.swipeRefresh.setColorSchemeResources(R.color.md_theme_primary);
binding.swipeRefresh.setOnRefreshListener(() -> viewModel.refresh());
viewModel.getIsLoading().observe(getViewLifecycleOwner(),
    loading -> binding.swipeRefresh.setRefreshing(loading));
```

---

# Module 14 — Dashboard Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. KPI / Statistic Card Grid

```xml
<!-- 2x2 grid of stat cards using ConstraintLayout -->
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp">

    <!-- Revenue card -->
    <com.google.android.material.card.MaterialCardView
        android:id="@+id/cardRevenue"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="4dp"
        app:cardCornerRadius="16dp"
        app:cardBackgroundColor="?attr/colorPrimaryContainer"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toStartOf="@id/cardUsers"
        app:layout_constraintTop_toTopOf="parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="16dp">
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="Revenue" android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorOnPrimaryContainer" android:alpha="0.7" />
            <TextView android:id="@+id/tvRevenue" android:layout_width="wrap_content"
                android:layout_height="wrap_content" android:text="$12,450"
                android:textAppearance="?attr/textAppearanceHeadlineSmall"
                android:textColor="?attr/colorOnPrimaryContainer" />
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="↑ 12.5%" android:textColor="#4CAF50"
                android:textAppearance="?attr/textAppearanceLabelSmall" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>

    <!-- More cards in grid... -->
</androidx.constraintlayout.widget.ConstraintLayout>
```

## 2. Charts (MPAndroidChart)

```gradle
implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
```

```xml
<com.github.mikephil.charting.charts.LineChart
    android:id="@+id/lineChart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />

<com.github.mikephil.charting.charts.BarChart
    android:id="@+id/barChart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />

<com.github.mikephil.charting.charts.PieChart
    android:id="@+id/pieChart"
    android:layout_width="match_parent"
    android:layout_height="200dp" />
```

```kotlin
// Line Chart setup
val entries = listOf(
    Entry(0f, 10f), Entry(1f, 25f), Entry(2f, 18f),
    Entry(3f, 32f), Entry(4f, 28f), Entry(5f, 45f)
)
val dataSet = LineDataSet(entries, "Revenue").apply {
    color = ContextCompat.getColor(context, R.color.md_theme_primary)
    valueTextColor = ContextCompat.getColor(context, R.color.md_theme_onSurface)
    lineWidth = 2f
    circleRadius = 4f
    setCircleColor(ContextCompat.getColor(context, R.color.md_theme_primary))
    mode = LineDataSet.Mode.CUBIC_BEZIER
    setDrawFilled(true)
    fillDrawable = ContextCompat.getDrawable(context, R.drawable.gradient_chart_fill)
}

binding.lineChart.apply {
    data = LineData(dataSet)
    description.isEnabled = false
    legend.isEnabled = false
    xAxis.position = XAxis.XAxisPosition.BOTTOM
    axisRight.isEnabled = false
    animateX(1000)
}

// Bar Chart
val barEntries = listOf(BarEntry(0f, 30f), BarEntry(1f, 50f), BarEntry(2f, 40f))
val barDataSet = BarDataSet(barEntries, "Sales").apply {
    colors = listOf(
        ContextCompat.getColor(context, R.color.md_theme_primary),
        ContextCompat.getColor(context, R.color.md_theme_secondary),
        ContextCompat.getColor(context, R.color.md_theme_tertiary)
    )
    barBorderRadius = 8f
}
binding.barChart.data = BarData(barDataSet)
binding.barChart.animateY(800)

// Pie Chart
val pieEntries = listOf(
    PieEntry(40f, "Mobile"),
    PieEntry(35f, "Desktop"),
    PieEntry(25f, "Tablet")
)
val pieDataSet = PieDataSet(pieEntries, "Traffic").apply {
    colors = ColorTemplate.MATERIAL_COLORS.toList()
    sliceSpace = 3f
    selectionShift = 8f
}
binding.pieChart.apply {
    data = PieData(pieDataSet)
    isDrawHoleEnabled = true
    holeRadius = 58f
    transparentCircleRadius = 61f
    description.isEnabled = false
    animateY(1000)
}
```

## 4. Statistic Widget (Value + Trend Delta)

```xml
<!-- res/layout/widget_stat.xml -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="0dp" android:layout_height="wrap_content"
    app:cardCornerRadius="16dp" app:cardElevation="0dp"
    app:strokeWidth="1dp" app:strokeColor="?attr/colorOutlineVariant">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" android:padding="16dp">

        <LinearLayout android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal" android:gravity="center_vertical">

            <ImageView android:id="@+id/ivStatIcon"
                android:layout_width="20dp" android:layout_height="20dp"
                android:tint="?attr/colorPrimary" />

            <TextView android:id="@+id/tvStatLabel"
                android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:layout_marginStart="8dp"
                android:textAppearance="?attr/textAppearanceLabelMedium"
                android:textColor="?attr/colorOnSurfaceVariant" />
        </LinearLayout>

        <TextView android:id="@+id/tvStatValue"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:textAppearance="?attr/textAppearanceHeadlineSmall" />

        <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:orientation="horizontal" android:gravity="center_vertical"
            android:layout_marginTop="4dp">
            <ImageView android:id="@+id/ivTrend"
                android:layout_width="16dp" android:layout_height="16dp" />
            <TextView android:id="@+id/tvDelta"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:layout_marginStart="4dp"
                android:textAppearance="?attr/textAppearanceLabelSmall" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

```kotlin
data class StatItem(val icon: Int, val label: String, val value: String, val delta: Float, val deltaLabel: String)

fun bindStat(binding: WidgetStatBinding, stat: StatItem) {
    binding.ivStatIcon.setImageResource(stat.icon)
    binding.tvStatLabel.text = stat.label
    binding.tvStatValue.text = stat.value
    binding.tvDelta.text     = stat.deltaLabel

    val (arrow, colorRes) = if (stat.delta >= 0)
        R.drawable.ic_trending_up   to R.color.trend_positive
    else
        R.drawable.ic_trending_down to R.color.trend_negative

    val color = ContextCompat.getColor(binding.root.context, colorRes)
    binding.ivTrend.setImageResource(arrow)
    binding.ivTrend.imageTintList = ColorStateList.valueOf(color)
    binding.tvDelta.setTextColor(color)
}
```

---

## 5. Activity / Timeline Panel

```xml
<!-- res/layout/item_timeline.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="horizontal" android:padding="8dp">

    <!-- Left: icon + connecting line -->
    <LinearLayout android:layout_width="40dp" android:layout_height="match_parent"
        android:orientation="vertical" android:gravity="center_horizontal">

        <View android:id="@+id/lineTop"
            android:layout_width="2dp" android:layout_height="16dp"
            android:background="?attr/colorOutlineVariant" />

        <ImageView android:id="@+id/ivIcon"
            android:layout_width="32dp" android:layout_height="32dp"
            android:padding="6dp"
            android:background="@drawable/circle_primary_container" />

        <View android:id="@+id/lineBottom"
            android:layout_width="2dp" android:layout_height="0dp"
            android:layout_weight="1"
            android:background="?attr/colorOutlineVariant" />
    </LinearLayout>

    <!-- Right: text content -->
    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
        android:layout_weight="1" android:layout_marginStart="12dp"
        android:orientation="vertical" android:paddingTop="4dp">

        <TextView android:id="@+id/tvEventTitle"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleSmall" />
        <TextView android:id="@+id/tvEventDesc"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
        <TextView android:id="@+id/tvTimestamp"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginTop="4dp"
            android:textAppearance="?attr/textAppearanceLabelSmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>
</LinearLayout>
```

```kotlin
data class TimelineEvent(
    val icon: Int, val title: String, val desc: String,
    val timestamp: String, val isLast: Boolean = false
)

class TimelineAdapter : RecyclerView.Adapter<TimelineAdapter.VH>() {
    private val events = mutableListOf<TimelineEvent>()
    fun submit(list: List<TimelineEvent>) { events.clear(); events.addAll(list); notifyDataSetChanged() }

    inner class VH(val b: ItemTimelineBinding) : RecyclerView.ViewHolder(b.root)
    override fun onCreateViewHolder(p: ViewGroup, v: Int) =
        VH(ItemTimelineBinding.inflate(LayoutInflater.from(p.context), p, false))

    override fun onBindViewHolder(h: VH, pos: Int) {
        val e = events[pos]
        h.b.ivIcon.setImageResource(e.icon)
        h.b.tvEventTitle.text = e.title
        h.b.tvEventDesc.text  = e.desc
        h.b.tvTimestamp.text  = e.timestamp
        h.b.lineTop.visibility    = if (pos == 0) View.INVISIBLE else View.VISIBLE
        h.b.lineBottom.visibility = if (e.isLast)   View.INVISIBLE else View.VISIBLE
    }
    override fun getItemCount() = events.size
}
```

---

## 6. Finance Dashboard Pattern

```xml
<!-- res/layout/fragment_finance_dashboard.xml — balance card + transactions -->
<androidx.core.widget.NestedScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content" android:orientation="vertical">

        <!-- Balance card -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_margin="16dp"
            app:cardCornerRadius="20dp"
            app:cardBackgroundColor="?attr/colorPrimary"
            app:cardElevation="8dp">

            <LinearLayout android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical" android:padding="24dp">

                <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Total Balance" android:textColor="?attr/colorOnPrimary"
                    android:alpha="0.7" android:textAppearance="?attr/textAppearanceLabelLarge" />

                <TextView android:id="@+id/tvBalance"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textColor="?attr/colorOnPrimary"
                    android:textAppearance="?attr/textAppearanceDisplaySmall"
                    android:textStyle="bold" android:layout_marginTop="4dp" />

                <!-- Income / Expenses row -->
                <LinearLayout android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="20dp" android:orientation="horizontal">

                    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:orientation="vertical">
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Income" android:textColor="#B0FFFFFF"
                            android:textAppearance="?attr/textAppearanceLabelSmall" />
                        <TextView android:id="@+id/tvIncome" android:layout_width="wrap_content"
                            android:layout_height="wrap_content" android:textColor="#FFFFFF"
                            android:textAppearance="?attr/textAppearanceTitleMedium" />
                    </LinearLayout>

                    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:orientation="vertical">
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Expenses" android:textColor="#B0FFFFFF"
                            android:textAppearance="?attr/textAppearanceLabelSmall" />
                        <TextView android:id="@+id/tvExpenses" android:layout_width="wrap_content"
                            android:layout_height="wrap_content" android:textColor="#FFCDD2"
                            android:textAppearance="?attr/textAppearanceTitleMedium" />
                    </LinearLayout>
                </LinearLayout>
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Section header -->
        <LinearLayout android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:paddingStart="16dp" android:paddingEnd="16dp"
            android:orientation="horizontal">
            <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:text="Recent Transactions"
                android:textAppearance="?attr/textAppearanceTitleMedium" />
            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnSeeAll" android:layout_width="wrap_content"
                android:layout_height="wrap_content" android:text="See All"
                android:textAllCaps="false"
                style="@style/Widget.Material3.Button.TextButton" />
        </LinearLayout>

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/rvTransactions"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:nestedScrollingEnabled="false" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

```kotlin
data class Transaction(
    val icon: Int, val merchant: String, val category: String,
    val amount: String, val date: String, val isCredit: Boolean
)

class TransactionAdapter : RecyclerView.Adapter<TransactionAdapter.VH>() {
    private val items = mutableListOf<Transaction>()
    fun submit(list: List<Transaction>) { items.clear(); items.addAll(list); notifyDataSetChanged() }

    inner class VH(val b: ItemTransactionBinding) : RecyclerView.ViewHolder(b.root)
    override fun onCreateViewHolder(p: ViewGroup, v: Int) =
        VH(ItemTransactionBinding.inflate(LayoutInflater.from(p.context), p, false))

    override fun onBindViewHolder(h: VH, pos: Int) {
        val t = items[pos]
        h.b.ivMerchantIcon.setImageResource(t.icon)
        h.b.tvMerchant.text  = t.merchant
        h.b.tvCategory.text  = t.category
        h.b.tvDate.text      = t.date
        h.b.tvAmount.text    = t.amount
        h.b.tvAmount.setTextColor(ContextCompat.getColor(h.b.root.context,
            if (t.isCredit) R.color.trend_positive else R.color.md_theme_onSurface))
    }
    override fun getItemCount() = items.size
}
```

---

## 7. Graph Types (Line, Bar, Pie — MPAndroidChart)

```gradle
implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'
// settings.gradle: maven { url 'https://jitpack.io' }
```

```xml
<com.github.mikephil.charting.charts.LineChart
    android:id="@+id/lineChart" android:layout_width="match_parent" android:layout_height="200dp" />
<com.github.mikephil.charting.charts.BarChart
    android:id="@+id/barChart" android:layout_width="match_parent" android:layout_height="200dp" />
<com.github.mikephil.charting.charts.PieChart
    android:id="@+id/pieChart" android:layout_width="match_parent" android:layout_height="200dp" />
```

```kotlin
fun setupLineChart(chart: LineChart, values: List<Float>) {
    val entries = values.mapIndexed { i, v -> Entry(i.toFloat(), v) }
    val dataSet = LineDataSet(entries, "Revenue").apply {
        color = ContextCompat.getColor(chart.context, R.color.md_theme_primary)
        fillColor = ContextCompat.getColor(chart.context, R.color.md_theme_primaryContainer)
        setDrawFilled(true); lineWidth = 2f; circleRadius = 4f
        setCircleColor(ContextCompat.getColor(chart.context, R.color.md_theme_primary))
        mode = LineDataSet.Mode.CUBIC_BEZIER; setDrawValues(false)
    }
    chart.apply {
        data = LineData(dataSet)
        description.isEnabled = false; axisRight.isEnabled = false
        xAxis.position = XAxis.XAxisPosition.BOTTOM
        animateX(500); invalidate()
    }
}

fun setupBarChart(chart: BarChart, labels: List<String>, values: List<Float>) {
    val entries = values.mapIndexed { i, v -> BarEntry(i.toFloat(), v) }
    val dataSet = BarDataSet(entries, "Monthly").apply {
        color = ContextCompat.getColor(chart.context, R.color.md_theme_secondary)
        setDrawValues(false)
    }
    chart.apply {
        data = BarData(dataSet).also { it.barWidth = 0.6f }
        xAxis.valueFormatter = IndexAxisValueFormatter(labels)
        xAxis.position = XAxis.XAxisPosition.BOTTOM
        axisRight.isEnabled = false; description.isEnabled = false
        setFitBars(true); animateY(600); invalidate()
    }
}

fun setupPieChart(chart: PieChart, segments: Map<String, Float>) {
    val entries = segments.map { (label, value) -> PieEntry(value, label) }
    val colors = listOf(R.color.md_theme_primary, R.color.md_theme_secondary,
        R.color.md_theme_tertiary, R.color.md_theme_error)
    val dataSet = PieDataSet(entries, "").apply {
        this.colors = colors.map { ContextCompat.getColor(chart.context, it) }
        sliceSpace = 3f; selectionShift = 8f
    }
    chart.apply {
        data = PieData(dataSet).also { it.setValueTextSize(12f) }
        isDrawHoleEnabled = true; holeRadius = 55f
        setHoleColor(Color.TRANSPARENT)
        setCenterText("Breakdown"); setCenterTextSize(14f)
        description.isEnabled = false; animateY(800); invalidate()
    }
}
```

```java
// Java — line chart (abbreviated)
List<Entry> entries = new ArrayList<>();
for (int i = 0; i < values.size(); i++) entries.add(new Entry(i, values.get(i)));
LineDataSet dataSet = new LineDataSet(entries, "Data");
dataSet.setColor(ContextCompat.getColor(chart.getContext(), R.color.md_theme_primary));
chart.setData(new LineData(dataSet));
chart.animateX(500);
chart.invalidate();
```

---

# Module 15 — Settings Screens
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Google-Style Settings

```gradle
implementation 'androidx.preference:preference-ktx:1.2.1'
```

```kotlin
class AppSettingsFragment : PreferenceFragmentCompat() {
    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.app_preferences, rootKey)

        // Dynamic summary
        findPreference<ListPreference>("language")?.summaryProvider =
            ListPreference.SimpleSummaryProvider.getInstance()

        // Custom click
        findPreference<Preference>("clear_cache")?.setOnPreferenceClickListener {
            showClearCacheDialog()
            true
        }

        // Show app version
        findPreference<Preference>("app_version")?.summary =
            BuildConfig.VERSION_NAME
    }
}
```

## 2. Custom Settings Item Layout

```xml
<!-- Custom settings item with toggle and description -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp"
    android:gravity="center_vertical"
    android:background="?attr/selectableItemBackground">

    <ImageView
        android:id="@+id/ivSettingIcon"
        android:layout_width="24dp"
        android:layout_height="24dp"
        android:tint="?attr/colorOnSurfaceVariant" />

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_marginHorizontal="16dp"
        android:orientation="vertical">

        <TextView
            android:id="@+id/tvSettingTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleMedium"
            android:textColor="?attr/colorOnSurface" />

        <TextView
            android:id="@+id/tvSettingSummary"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>

    <com.google.android.material.materialswitch.MaterialSwitch
        android:id="@+id/switchSetting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```

## 3. Samsung One UI-Style Settings (Large Cards + Colored Icons)

**Visual Use Case:** Apps targeting Samsung users or wanting a premium card-grouped settings feel.

```xml
<!-- res/layout/fragment_oneui_settings.xml -->
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:background="?attr/colorBackground">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" android:paddingBottom="24dp">

        <!-- Search bar top -->
        <com.google.android.material.search.SearchBar
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_margin="16dp" android:hint="Search settings" />

        <!-- Profile card -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginStart="16dp" android:layout_marginEnd="16dp"
            android:layout_marginBottom="16dp"
            app:cardCornerRadius="20dp"
            app:cardBackgroundColor="?attr/colorPrimaryContainer"
            app:cardElevation="0dp">

            <LinearLayout android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="horizontal" android:padding="20dp"
                android:gravity="center_vertical">

                <com.google.android.material.imageview.ShapeableImageView
                    android:id="@+id/ivProfileAvatar"
                    android:layout_width="56dp" android:layout_height="56dp"
                    android:scaleType="centerCrop"
                    app:shapeAppearanceOverlay="@style/CircleImageView" />

                <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                    android:layout_weight="1" android:layout_marginStart="16dp"
                    android:orientation="vertical">
                    <TextView android:id="@+id/tvProfileName"
                        android:layout_width="wrap_content" android:layout_height="wrap_content"
                        android:textAppearance="?attr/textAppearanceTitleMedium" />
                    <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                        android:text="Tap to manage account"
                        android:textAppearance="?attr/textAppearanceBodySmall"
                        android:textColor="?attr/colorOnSurfaceVariant" />
                </LinearLayout>

                <ImageView android:layout_width="20dp" android:layout_height="20dp"
                    android:src="@drawable/ic_chevron_right"
                    android:tint="?attr/colorOnSurfaceVariant" />
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <!-- Section label -->
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginStart="24dp" android:layout_marginBottom="8dp"
            android:text="Connections"
            android:textAppearance="?attr/textAppearanceLabelLarge"
            android:textColor="?attr/colorPrimary" />

        <!-- Settings group card (reusable pattern) -->
        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginStart="16dp" android:layout_marginEnd="16dp"
            android:layout_marginBottom="16dp"
            app:cardCornerRadius="20dp" app:cardElevation="0dp" app:strokeWidth="0dp">

            <LinearLayout android:layout_width="match_parent"
                android:layout_height="wrap_content" android:orientation="vertical">

                <!-- Reusable row — include layout_setting_row.xml multiple times -->
                <include layout="@layout/layout_setting_row" />
                <com.google.android.material.divider.MaterialDivider
                    android:layout_width="match_parent" android:layout_height="wrap_content"
                    app:dividerInsetStart="72dp" />
                <include layout="@layout/layout_setting_row" />
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

```xml
<!-- res/layout/layout_setting_row.xml — colored icon + title + optional toggle/chevron -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="64dp"
    android:orientation="horizontal" android:gravity="center_vertical"
    android:paddingStart="16dp" android:paddingEnd="16dp"
    android:background="?attr/selectableItemBackground">

    <!-- Colored circle icon background -->
    <FrameLayout android:layout_width="40dp" android:layout_height="40dp"
        android:background="@drawable/circle_icon_bg_blue">
        <ImageView android:id="@+id/ivSettingIcon"
            android:layout_width="22dp" android:layout_height="22dp"
            android:layout_gravity="center" android:tint="#FFFFFF" />
    </FrameLayout>

    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
        android:layout_weight="1" android:layout_marginStart="16dp"
        android:orientation="vertical">
        <TextView android:id="@+id/tvSettingTitle"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyLarge" />
        <TextView android:id="@+id/tvSettingSubtitle"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:visibility="gone" />
    </LinearLayout>

    <com.google.android.material.materialswitch.MaterialSwitch
        android:id="@+id/switchSetting"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:visibility="gone" />

    <ImageView android:id="@+id/ivChevron"
        android:layout_width="20dp" android:layout_height="20dp"
        android:src="@drawable/ic_chevron_right"
        android:tint="?attr/colorOnSurfaceVariant" />
</LinearLayout>
```

```xml
<!-- res/drawable/circle_icon_bg_blue.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="oval">
    <solid android:color="#2196F3" />
    <size android:width="40dp" android:height="40dp" />
</shape>
```

---

## 4. Account / Profile Page

**Visual Use Case:** Edit display name, change avatar, update phone number.

```xml
<!-- res/layout/fragment_account.xml -->
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" android:padding="24dp">

        <!-- Avatar + change button -->
        <FrameLayout android:layout_width="96dp" android:layout_height="96dp"
            android:layout_gravity="center_horizontal">
            <com.google.android.material.imageview.ShapeableImageView
                android:id="@+id/ivAccountAvatar"
                android:layout_width="96dp" android:layout_height="96dp"
                android:scaleType="centerCrop"
                app:shapeAppearanceOverlay="@style/CircleImageView" />
            <com.google.android.material.floatingactionbutton.FloatingActionButton
                android:id="@+id/fabChangePhoto"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:layout_gravity="bottom|end"
                app:fabSize="mini" app:srcCompat="@drawable/ic_camera_alt"
                app:backgroundTint="?attr/colorPrimaryContainer" />
        </FrameLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilDisplayName"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="24dp" android:hint="Display Name"
            app:startIconDrawable="@drawable/ic_person"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etDisplayName"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPersonName|textCapWords"
                android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="12dp" android:hint="Email (read-only)"
            app:startIconDrawable="@drawable/ic_email"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etEmail"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:enabled="false" android:inputType="textEmailAddress" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilPhone"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="12dp" android:hint="Phone Number"
            app:startIconDrawable="@drawable/ic_phone"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etPhone"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="phone" android:imeOptions="actionDone" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSaveProfile"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="24dp"
            android:text="Save Changes" android:textAllCaps="false" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDeleteAccount"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="Delete Account" android:textAllCaps="false"
            app:backgroundTint="?attr/colorErrorContainer"
            style="@style/Widget.Material3.Button.TonalButton" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

```kotlin
class AccountFragment : Fragment(R.layout.fragment_account) {

    private val photoPicker = registerForActivityResult(ActivityResultContracts.GetContent()) { uri ->
        uri?.let { binding.ivAccountAvatar.setImageURI(it); viewModel.updateAvatar(it) }
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.profile.observe(viewLifecycleOwner) { p ->
            binding.etDisplayName.setText(p.displayName)
            binding.etEmail.setText(p.email)
            binding.etPhone.setText(p.phone)
            Glide.with(this).load(p.avatarUrl).circleCrop().into(binding.ivAccountAvatar)
        }

        binding.fabChangePhoto.setOnClickListener { photoPicker.launch("image/*") }

        binding.btnSaveProfile.setOnClickListener {
            val name = binding.etDisplayName.text.toString().trim()
            if (name.isEmpty()) { binding.tilDisplayName.error = "Name required"; return@setOnClickListener }
            binding.tilDisplayName.error = null
            viewModel.updateProfile(name, binding.etPhone.text.toString().trim())
        }

        binding.btnDeleteAccount.setOnClickListener {
            MaterialAlertDialogBuilder(requireContext())
                .setTitle("Delete Account")
                .setMessage("This permanently deletes your data. Cannot be undone.")
                .setPositiveButton("Delete") { _, _ -> viewModel.deleteAccount() }
                .setNegativeButton("Cancel", null)
                .show()
        }
    }
}
```

```java
// Java — save profile
binding.btnSaveProfile.setOnClickListener(v -> {
    String name = binding.etDisplayName.getText().toString().trim();
    if (name.isEmpty()) {
        binding.tilDisplayName.setError("Name required");
        return;
    }
    binding.tilDisplayName.setError(null);
    viewModel.updateProfile(name, binding.etPhone.getText().toString().trim());
});
```

---

## 5. Security & Privacy Settings Page

**Visual Use Case:** Biometric toggle, PIN change, location/analytics toggles.

```xml
<!-- res/layout/fragment_security_settings.xml -->
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" android:padding="16dp">

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Sign-In &amp; Security"
            android:textAppearance="?attr/textAppearanceLabelLarge"
            android:textColor="?attr/colorPrimary" android:layout_marginBottom="8dp" />

        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            app:cardCornerRadius="16dp" app:cardElevation="0dp"
            app:strokeWidth="1dp" app:strokeColor="?attr/colorOutlineVariant">

            <LinearLayout android:layout_width="match_parent"
                android:layout_height="wrap_content" android:orientation="vertical">

                <!-- Biometric row -->
                <LinearLayout android:layout_width="match_parent" android:layout_height="64dp"
                    android:orientation="horizontal" android:gravity="center_vertical"
                    android:paddingStart="16dp" android:paddingEnd="16dp"
                    android:background="?attr/selectableItemBackground">
                    <ImageView android:layout_width="24dp" android:layout_height="24dp"
                        android:src="@drawable/ic_fingerprint" android:tint="?attr/colorPrimary" />
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:layout_marginStart="16dp"
                        android:text="Biometric Unlock"
                        android:textAppearance="?attr/textAppearanceBodyLarge" />
                    <com.google.android.material.materialswitch.MaterialSwitch
                        android:id="@+id/switchBiometric"
                        android:layout_width="wrap_content" android:layout_height="wrap_content" />
                </LinearLayout>

                <com.google.android.material.divider.MaterialDivider
                    android:layout_width="match_parent" android:layout_height="wrap_content"
                    app:dividerInsetStart="56dp" />

                <!-- Change PIN row -->
                <LinearLayout android:id="@+id/rowChangePin"
                    android:layout_width="match_parent" android:layout_height="64dp"
                    android:orientation="horizontal" android:gravity="center_vertical"
                    android:paddingStart="16dp" android:paddingEnd="16dp"
                    android:background="?attr/selectableItemBackground">
                    <ImageView android:layout_width="24dp" android:layout_height="24dp"
                        android:src="@drawable/ic_lock" android:tint="?attr/colorPrimary" />
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:layout_marginStart="16dp"
                        android:text="Change PIN"
                        android:textAppearance="?attr/textAppearanceBodyLarge" />
                    <ImageView android:layout_width="20dp" android:layout_height="20dp"
                        android:src="@drawable/ic_chevron_right"
                        android:tint="?attr/colorOnSurfaceVariant" />
                </LinearLayout>
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Privacy"
            android:textAppearance="?attr/textAppearanceLabelLarge"
            android:textColor="?attr/colorPrimary" android:layout_marginBottom="8dp" />

        <com.google.android.material.card.MaterialCardView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            app:cardCornerRadius="16dp" app:cardElevation="0dp"
            app:strokeWidth="1dp" app:strokeColor="?attr/colorOutlineVariant">

            <LinearLayout android:layout_width="match_parent"
                android:layout_height="wrap_content" android:orientation="vertical">

                <!-- Location toggle -->
                <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
                    android:minHeight="64dp" android:orientation="horizontal"
                    android:gravity="center_vertical"
                    android:paddingStart="16dp" android:paddingEnd="16dp"
                    android:background="?attr/selectableItemBackground">
                    <ImageView android:layout_width="24dp" android:layout_height="24dp"
                        android:src="@drawable/ic_location_on" android:tint="?attr/colorPrimary" />
                    <LinearLayout android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:layout_marginStart="16dp"
                        android:orientation="vertical">
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Location Access"
                            android:textAppearance="?attr/textAppearanceBodyLarge" />
                        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                            android:text="Allow while using the app"
                            android:textAppearance="?attr/textAppearanceBodySmall"
                            android:textColor="?attr/colorOnSurfaceVariant" />
                    </LinearLayout>
                    <com.google.android.material.materialswitch.MaterialSwitch
                        android:id="@+id/switchLocation"
                        android:layout_width="wrap_content" android:layout_height="wrap_content" />
                </LinearLayout>

                <com.google.android.material.divider.MaterialDivider
                    android:layout_width="match_parent" android:layout_height="wrap_content"
                    app:dividerInsetStart="56dp" />

                <!-- Analytics toggle -->
                <LinearLayout android:layout_width="match_parent" android:layout_height="64dp"
                    android:orientation="horizontal" android:gravity="center_vertical"
                    android:paddingStart="16dp" android:paddingEnd="16dp"
                    android:background="?attr/selectableItemBackground">
                    <ImageView android:layout_width="24dp" android:layout_height="24dp"
                        android:src="@drawable/ic_analytics" android:tint="?attr/colorPrimary" />
                    <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                        android:layout_weight="1" android:layout_marginStart="16dp"
                        android:text="Share Analytics"
                        android:textAppearance="?attr/textAppearanceBodyLarge" />
                    <com.google.android.material.materialswitch.MaterialSwitch
                        android:id="@+id/switchAnalytics"
                        android:layout_width="wrap_content" android:layout_height="wrap_content" />
                </LinearLayout>
            </LinearLayout>
        </com.google.android.material.card.MaterialCardView>
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    val prefs = requireContext().getSharedPreferences("security", Context.MODE_PRIVATE)

    binding.switchBiometric.isChecked = prefs.getBoolean("biometric", false)
    binding.switchLocation.isChecked  = prefs.getBoolean("location", true)
    binding.switchAnalytics.isChecked = prefs.getBoolean("analytics", true)

    binding.switchBiometric.setOnCheckedChangeListener { _, checked ->
        prefs.edit().putBoolean("biometric", checked).apply()
        if (checked) verifyBiometricAvailability()
    }
    binding.switchLocation.setOnCheckedChangeListener { _, checked ->
        prefs.edit().putBoolean("location", checked).apply()
    }
    binding.rowChangePin.setOnClickListener {
        findNavController().navigate(R.id.action_security_to_changePin)
    }
}
```

---

## 6. Appearance / Theme Settings Page

**Visual Use Case:** Light/Dark/Auto toggle, font size slider, Dynamic Color switch.

```xml
<!-- res/layout/fragment_appearance_settings.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical" android:padding="16dp">

    <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="Theme" android:textAppearance="?attr/textAppearanceLabelLarge"
        android:textColor="?attr/colorPrimary" android:layout_marginBottom="8dp" />

    <com.google.android.material.card.MaterialCardView
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        app:cardCornerRadius="16dp" app:cardElevation="0dp"
        app:strokeWidth="1dp" app:strokeColor="?attr/colorOutlineVariant">

        <com.google.android.material.button.MaterialButtonToggleGroup
            android:id="@+id/toggleTheme"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_margin="16dp"
            app:singleSelection="true" app:selectionRequired="true">

            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnThemeLight" android:layout_width="0dp"
                android:layout_height="wrap_content" android:layout_weight="1"
                android:text="Light" android:textAllCaps="false"
                style="@style/Widget.Material3.Button.OutlinedButton" />
            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnThemeDark" android:layout_width="0dp"
                android:layout_height="wrap_content" android:layout_weight="1"
                android:text="Dark" android:textAllCaps="false"
                style="@style/Widget.Material3.Button.OutlinedButton" />
            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnThemeAuto" android:layout_width="0dp"
                android:layout_height="wrap_content" android:layout_weight="1"
                android:text="Auto" android:textAllCaps="false"
                style="@style/Widget.Material3.Button.OutlinedButton" />
        </com.google.android.material.button.MaterialButtonToggleGroup>
    </com.google.android.material.card.MaterialCardView>

    <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="Font Size" android:textAppearance="?attr/textAppearanceLabelLarge"
        android:textColor="?attr/colorPrimary" android:layout_marginBottom="8dp" />

    <com.google.android.material.slider.Slider
        android:id="@+id/sliderFontSize"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:valueFrom="0.8" android:valueTo="1.4" android:stepSize="0.2"
        android:value="1.0" app:labelBehavior="floating" />

    <TextView android:id="@+id/tvFontPreview"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="Preview: The quick brown fox"
        android:textAppearance="?attr/textAppearanceBodyLarge" />
</LinearLayout>
```

```kotlin
class AppearanceFragment : Fragment(R.layout.fragment_appearance_settings) {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val prefs = requireContext().getSharedPreferences("settings", Context.MODE_PRIVATE)
        val saved = prefs.getInt("theme_mode", AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM)

        binding.toggleTheme.check(when (saved) {
            AppCompatDelegate.MODE_NIGHT_NO  -> R.id.btnThemeLight
            AppCompatDelegate.MODE_NIGHT_YES -> R.id.btnThemeDark
            else                             -> R.id.btnThemeAuto
        })

        binding.toggleTheme.addOnButtonCheckedListener { _, checkedId, isChecked ->
            if (!isChecked) return@addOnButtonCheckedListener
            val mode = when (checkedId) {
                R.id.btnThemeLight -> AppCompatDelegate.MODE_NIGHT_NO
                R.id.btnThemeDark  -> AppCompatDelegate.MODE_NIGHT_YES
                else               -> AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM
            }
            prefs.edit().putInt("theme_mode", mode).apply()
            AppCompatDelegate.setDefaultNightMode(mode)
        }

        val savedScale = prefs.getFloat("font_scale", 1.0f)
        binding.sliderFontSize.value = savedScale

        binding.sliderFontSize.addOnChangeListener { _, value, fromUser ->
            if (fromUser) {
                binding.tvFontPreview.textSize = 16f * value
                prefs.edit().putFloat("font_scale", value).apply()
            }
        }
    }
}
```

```java
// Java — theme toggle
binding.toggleTheme.addOnButtonCheckedListener((group, checkedId, isChecked) -> {
    if (!isChecked) return;
    int mode;
    if (checkedId == R.id.btnThemeLight)     mode = AppCompatDelegate.MODE_NIGHT_NO;
    else if (checkedId == R.id.btnThemeDark) mode = AppCompatDelegate.MODE_NIGHT_YES;
    else                                     mode = AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM;
    prefs.edit().putInt("theme_mode", mode).apply();
    AppCompatDelegate.setDefaultNightMode(mode);
});
```

---

## 7. AndroidX Preference Screen (XML-Driven — Native Settings)

```gradle
implementation 'androidx.preference:preference-ktx:1.2.1'
```

```xml
<!-- res/xml/preferences_root.xml -->
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <PreferenceCategory android:title="Notifications" app:iconSpaceReserved="false">
        <SwitchPreferenceCompat
            android:key="pref_notifications"
            android:title="Enable Notifications"
            android:summary="Receive push notifications"
            android:defaultValue="true" />
        <SwitchPreferenceCompat
            android:key="pref_sound"
            android:title="Notification Sound"
            android:defaultValue="true"
            android:dependency="pref_notifications" />
    </PreferenceCategory>

    <PreferenceCategory android:title="General" app:iconSpaceReserved="false">
        <ListPreference
            android:key="pref_language"
            android:title="Language"
            android:entries="@array/language_labels"
            android:entryValues="@array/language_values"
            android:defaultValue="en"
            app:useSimpleSummaryProvider="true" />
        <EditTextPreference
            android:key="pref_username"
            android:title="Display Name"
            app:useSimpleSummaryProvider="true" />
        <Preference
            android:key="pref_clear_cache"
            android:title="Clear Cache"
            android:summary="Free up storage space" />
    </PreferenceCategory>

    <PreferenceCategory android:title="About" app:iconSpaceReserved="false">
        <Preference android:key="pref_version" android:title="App Version" android:summary="1.0.0" />
        <Preference android:key="pref_privacy"  android:title="Privacy Policy" />
    </PreferenceCategory>
</PreferenceScreen>
```

```xml
<!-- res/values/arrays.xml -->
<resources>
    <string-array name="language_labels">
        <item>English</item><item>Kiswahili</item><item>French</item>
    </string-array>
    <string-array name="language_values">
        <item>en</item><item>sw</item><item>fr</item>
    </string-array>
</resources>
```

```kotlin
class AppPreferenceFragment : PreferenceFragmentCompat() {

    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.preferences_root, rootKey)

        findPreference<SwitchPreferenceCompat>("pref_notifications")
            ?.setOnPreferenceChangeListener { _, newValue ->
                if (newValue as Boolean) scheduleNotifications() else cancelNotifications()
                true
            }

        findPreference<Preference>("pref_clear_cache")?.setOnPreferenceClickListener {
            clearCache()
            it.summary = "Cache cleared"
            true
        }

        findPreference<Preference>("pref_privacy")?.setOnPreferenceClickListener {
            startActivity(Intent(Intent.ACTION_VIEW, Uri.parse("https://example.com/privacy")))
            true
        }
    }
}
```

```java
// Java
public class AppPreferenceFragment extends PreferenceFragmentCompat {
    @Override
    public void onCreatePreferences(Bundle savedInstanceState, String rootKey) {
        setPreferencesFromResource(R.xml.preferences_root, rootKey);
        Preference clearCache = findPreference("pref_clear_cache");
        if (clearCache != null) {
            clearCache.setOnPreferenceClickListener(pref -> {
                clearCache();
                return true;
            });
        }
    }
}
```

```kotlin
// Host in Activity/Fragment
supportFragmentManager.commit {
    replace(R.id.settingsContainer, AppPreferenceFragment())
}
```

---

# Module 16 — Authentication UI
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Login Screen Layout

```xml
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp"
        android:gravity="center">

        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/ic_logo"
            android:layout_marginBottom="32dp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Welcome Back"
            android:textAppearance="?attr/textAppearanceHeadlineMedium"
            android:layout_marginBottom="8dp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Sign in to continue"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:layout_marginBottom="32dp" />

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilEmail"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="12dp"
            android:hint="Email"
            app:startIconDrawable="@drawable/ic_email"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:inputType="textEmailAddress"
                android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilPassword"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="8dp"
            android:hint="Password"
            app:endIconMode="password_toggle"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:inputType="textPassword"
                android:imeOptions="actionDone" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnForgotPassword"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="end"
            android:text="Forgot password?"
            style="@style/Widget.Material3.Button.TextButton" />

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnLogin"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="Sign In"
            android:textAllCaps="false"
            style="@style/Widget.Material3.Button" />

        <com.google.android.material.divider.MaterialDivider
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginVertical="24dp" />

        <!-- Social sign-in buttons -->
        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnGoogleSignIn"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Continue with Google"
            app:icon="@drawable/ic_google"
            style="@style/Widget.Material3.Button.OutlinedButton" />
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

## 2. Biometric Authentication

```gradle
implementation 'androidx.biometric:biometric:1.2.0-alpha05'
```

```kotlin
class BiometricAuthHelper(private val activity: FragmentActivity) {

    private val executor = ContextCompat.getMainExecutor(activity)

    fun canAuthenticate(): Boolean {
        val biometricManager = BiometricManager.from(activity)
        return biometricManager.canAuthenticate(
            BiometricManager.Authenticators.BIOMETRIC_STRONG or
            BiometricManager.Authenticators.DEVICE_CREDENTIAL
        ) == BiometricManager.BIOMETRIC_SUCCESS
    }

    fun authenticate(
        title: String = "Authenticate",
        subtitle: String = "Use biometric or device PIN",
        onSuccess: () -> Unit,
        onError: (String) -> Unit
    ) {
        val promptInfo = BiometricPrompt.PromptInfo.Builder()
            .setTitle(title)
            .setSubtitle(subtitle)
            .setAllowedAuthenticators(
                BiometricManager.Authenticators.BIOMETRIC_STRONG or
                BiometricManager.Authenticators.DEVICE_CREDENTIAL
            )
            .build()

        val biometricPrompt = BiometricPrompt(
            activity, executor,
            object : BiometricPrompt.AuthenticationCallback() {
                override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
                    onSuccess()
                }
                override fun onAuthenticationError(errorCode: Int, errString: CharSequence) {
                    onError(errString.toString())
                }
                override fun onAuthenticationFailed() {
                    onError("Authentication failed")
                }
            }
        )
        biometricPrompt.authenticate(promptInfo)
    }
}

// Usage
biometricHelper.authenticate(
    onSuccess = { navigateToHome() },
    onError = { msg -> showError(msg) }
)
```

## 3. OTP Verification Screen

```kotlin
class OtpFragment : Fragment() {
    private lateinit var otpHelper: OtpInputHelper

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        otpHelper = OtpInputHelper(listOf(
            binding.otp1, binding.otp2, binding.otp3,
            binding.otp4, binding.otp5, binding.otp6
        ))

        // Auto-verify when all digits entered
        otpHelper.setOnCompleteListener { otp ->
            viewModel.verifyOtp(otp)
        }

        // Countdown timer for resend
        object : CountDownTimer(60000, 1000) {
            override fun onTick(millisUntilFinished: Long) {
                binding.tvResend.text = "Resend in ${millisUntilFinished / 1000}s"
            }
            override fun onFinish() {
                binding.tvResend.text = "Resend OTP"
                binding.tvResend.isEnabled = true
            }
        }.start()

        binding.btnVerify.setOnClickListener {
            val otp = otpHelper.getOtp()
            if (otpHelper.isComplete()) viewModel.verifyOtp(otp)
            else binding.tilOtp.error = "Please enter all digits"
        }
    }
}
```

## 4. Sign Up / Registration Screen

```xml
<!-- res/layout/fragment_sign_up.xml -->
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" android:padding="24dp">

        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Create Account"
            android:textAppearance="?attr/textAppearanceHeadlineMedium"
            android:layout_marginBottom="4dp" />
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Sign up to get started"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:layout_marginBottom="28dp" />

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilFullName"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="12dp" android:hint="Full Name"
            app:startIconDrawable="@drawable/ic_person"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etFullName"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPersonName|textCapWords"
                android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilEmailSignup"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="12dp" android:hint="Email Address"
            app:startIconDrawable="@drawable/ic_email"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etEmailSignup"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textEmailAddress" android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilPasswordSignup"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="12dp" android:hint="Password"
            app:endIconMode="password_toggle"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etPasswordSignup"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPassword" android:imeOptions="actionNext" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/tilConfirmPassword"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="12dp" android:hint="Confirm Password"
            app:endIconMode="password_toggle"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etConfirmPassword"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:inputType="textPassword" android:imeOptions="actionDone" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Password strength bar -->
        <com.google.android.material.progressindicator.LinearProgressIndicator
            android:id="@+id/progressPasswordStrength"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="4dp"
            android:indeterminate="false" android:progress="0"
            app:trackCornerRadius="4dp" />

        <TextView android:id="@+id/tvPasswordStrength"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            android:textAppearance="?attr/textAppearanceLabelSmall" />

        <!-- Terms checkbox -->
        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="horizontal" android:gravity="center_vertical"
            android:layout_marginBottom="24dp">
            <com.google.android.material.checkbox.MaterialCheckBox
                android:id="@+id/checkTerms"
                android:layout_width="wrap_content" android:layout_height="wrap_content" />
            <TextView android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:layout_marginStart="8dp"
                android:text="I agree to the Terms of Service and Privacy Policy"
                android:textAppearance="?attr/textAppearanceBodySmall" />
        </LinearLayout>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSignUp"
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_marginBottom="16dp"
            android:text="Create Account" android:textAllCaps="false" />

        <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal" android:orientation="horizontal">
            <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="Already have an account? " />
            <TextView android:id="@+id/tvLoginLink"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="Sign In" android:textColor="?attr/colorPrimary"
                android:textStyle="bold" android:clickable="true" android:focusable="true"
                android:background="?attr/selectableItemBackgroundBorderless" />
        </LinearLayout>
    </LinearLayout>
</androidx.core.widget.NestedScrollView>
```

```kotlin
class SignUpFragment : Fragment(R.layout.fragment_sign_up) {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        binding.etPasswordSignup.addTextChangedListener { text ->
            val s = passwordStrength(text.toString())
            binding.progressPasswordStrength.setProgressCompat(s.score, true)
            binding.progressPasswordStrength.setIndicatorColor(s.color)
            binding.tvPasswordStrength.text = s.label
            binding.tvPasswordStrength.setTextColor(s.color)
        }
        binding.btnSignUp.setOnClickListener { validateAndSubmit() }
        binding.tvLoginLink.setOnClickListener { findNavController().navigateUp() }
    }

    private fun validateAndSubmit() {
        var valid = true
        val name     = binding.etFullName.text.toString().trim()
        val email    = binding.etEmailSignup.text.toString().trim()
        val password = binding.etPasswordSignup.text.toString()
        val confirm  = binding.etConfirmPassword.text.toString()

        if (name.isEmpty())  { binding.tilFullName.error = "Required"; valid = false }
        else binding.tilFullName.error = null

        if (!android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches())
            { binding.tilEmailSignup.error = "Invalid email"; valid = false }
        else binding.tilEmailSignup.error = null

        if (password.length < 8)
            { binding.tilPasswordSignup.error = "Min 8 characters"; valid = false }
        else binding.tilPasswordSignup.error = null

        if (password != confirm)
            { binding.tilConfirmPassword.error = "Passwords do not match"; valid = false }
        else binding.tilConfirmPassword.error = null

        if (!binding.checkTerms.isChecked) {
            Snackbar.make(binding.root, "Please accept the terms", Snackbar.LENGTH_SHORT).show()
            valid = false
        }

        if (valid) { binding.btnSignUp.isEnabled = false; viewModel.signUp(name, email, password) }
    }

    data class Strength(val score: Int, val color: Int, val label: String)

    private fun passwordStrength(pw: String): Strength {
        var score = 0
        if (pw.length >= 8)              score += 25
        if (pw.length >= 12)             score += 25
        if (pw.any { it.isDigit() })     score += 25
        if (pw.any { !it.isLetterOrDigit() }) score += 25
        return when {
            score <= 25 -> Strength(score, Color.RED,                         "Weak")
            score <= 50 -> Strength(score, Color.parseColor("#FF9800"), "Fair")
            score <= 75 -> Strength(score, Color.parseColor("#2196F3"),  "Good")
            else        -> Strength(score, Color.parseColor("#4CAF50"), "Strong")
        }
    }
}
```

```java
// Java — validation
private boolean validateForm() {
    boolean valid = true;
    String name  = binding.etFullName.getText().toString().trim();
    String email = binding.etEmailSignup.getText().toString().trim();
    String pass  = binding.etPasswordSignup.getText().toString();
    String conf  = binding.etConfirmPassword.getText().toString();

    if (name.isEmpty())  { binding.tilFullName.setError("Required"); valid = false; }
    else                   binding.tilFullName.setError(null);

    if (!android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches())
        { binding.tilEmailSignup.setError("Invalid email"); valid = false; }
    else  binding.tilEmailSignup.setError(null);

    if (!pass.equals(conf))
        { binding.tilConfirmPassword.setError("Passwords do not match"); valid = false; }
    else  binding.tilConfirmPassword.setError(null);

    return valid;
}
```

---

## 5. Passkey / FIDO2 UI (Credential Manager — API 28+)

```gradle
implementation 'androidx.credentials:credentials:1.3.0'
implementation 'androidx.credentials:credentials-play-services-auth:1.3.0'
```

```xml
<!-- Passkey sign-in button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnPasskey"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:text="Sign in with Passkey"
    app:icon="@drawable/ic_key"
    android:textAllCaps="false"
    style="@style/Widget.Material3.Button.OutlinedButton" />
```

```kotlin
class PasskeyFragment : Fragment(R.layout.fragment_passkey_auth) {

    private val credentialManager by lazy { CredentialManager.create(requireContext()) }

    // SIGN IN with existing passkey
    fun signInWithPasskey() {
        val request = GetCredentialRequest(
            credentialOptions = listOf(
                GetPublicKeyCredentialOption(requestJson = buildGetJson())
            )
        )
        lifecycleScope.launch {
            try {
                val result = credentialManager.getCredential(requireActivity(), request)
                handleCredential(result.credential)
            } catch (e: GetCredentialException) {
                when (e) {
                    is GetCredentialCancellationException -> { /* user cancelled */ }
                    is NoCredentialException -> showSnack("No passkey found — create one first")
                    else -> showSnack("Sign-in error: ${e.message}")
                }
            }
        }
    }

    // REGISTER new passkey
    fun createPasskey(userId: String, userName: String) {
        val request = CreatePublicKeyCredentialRequest(
            requestJson = buildCreateJson(userId, userName)
        )
        lifecycleScope.launch {
            try {
                credentialManager.createCredential(requireActivity(), request)
                showSnack("Passkey created!")
            } catch (e: CreateCredentialException) {
                showSnack("Error: ${e.message}")
            }
        }
    }

    private fun handleCredential(credential: Credential) {
        when (credential) {
            is PublicKeyCredential ->
                viewModel.verifyPasskey(credential.authenticationResponseJson)
            is PasswordCredential ->
                viewModel.signIn(credential.id, credential.password)
            else -> showSnack("Unsupported credential type")
        }
    }

    // Replace challenge with your server-generated Base64 challenge
    private fun buildGetJson() = """
        {"challenge":"SERVER_CHALLENGE_BASE64","rpId":"your.domain.com","userVerification":"required"}
    """.trimIndent()

    private fun buildCreateJson(userId: String, userName: String) = """
        {
          "challenge":"SERVER_CHALLENGE_BASE64",
          "rp":{"id":"your.domain.com","name":"Your App"},
          "user":{"id":"$userId","name":"$userName","displayName":"$userName"},
          "pubKeyCredParams":[{"type":"public-key","alg":-7}],
          "authenticatorSelection":{"authenticatorAttachment":"platform","requireResidentKey":true,"userVerification":"required"}
        }
    """.trimIndent()

    private fun showSnack(msg: String) =
        Snackbar.make(binding.root, msg, Snackbar.LENGTH_SHORT).show()
}
```

---

## 6. Face Authentication UI (BiometricPrompt — Face + Fingerprint Fallback)

```kotlin
class FaceAuthFragment : Fragment(R.layout.fragment_face_auth) {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        binding.btnFaceAuth.setOnClickListener { authenticate() }
    }

    private fun authenticate() {
        val biometricManager = BiometricManager.from(requireContext())
        val authenticators = BiometricManager.Authenticators.BIOMETRIC_STRONG or
                             BiometricManager.Authenticators.DEVICE_CREDENTIAL

        when (biometricManager.canAuthenticate(authenticators)) {
            BiometricManager.BIOMETRIC_SUCCESS           -> launchPrompt(authenticators)
            BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE -> showError("No biometric hardware")
            BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED -> enrollBiometrics()
            else -> showError("Biometric unavailable")
        }
    }

    private fun launchPrompt(authenticators: Int) {
        val promptInfo = BiometricPrompt.PromptInfo.Builder()
            .setTitle("Face Authentication")
            .setSubtitle("Verify it's you to continue")
            .setAllowedAuthenticators(authenticators)
            .build()

        BiometricPrompt(this, ContextCompat.getMainExecutor(requireContext()),
            object : BiometricPrompt.AuthenticationCallback() {
                override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
                    viewModel.onAuthSuccess()
                }
                override fun onAuthenticationError(code: Int, msg: CharSequence) {
                    if (code != BiometricPrompt.ERROR_USER_CANCELED &&
                        code != BiometricPrompt.ERROR_NEGATIVE_BUTTON)
                        showError("Auth error: $msg")
                }
                override fun onAuthenticationFailed() {
                    showError("Face not recognized. Try again.")
                }
            }
        ).authenticate(promptInfo)
    }

    private fun enrollBiometrics() {
        val intent = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R)
            Intent(Settings.ACTION_BIOMETRIC_ENROLL).apply {
                putExtra(Settings.EXTRA_BIOMETRIC_AUTHENTICATORS_ALLOWED,
                    BiometricManager.Authenticators.BIOMETRIC_STRONG)
            }
        else Intent(Settings.ACTION_SECURITY_SETTINGS)
        startActivity(intent)
    }

    private fun showError(msg: String) =
        Snackbar.make(binding.root, msg, Snackbar.LENGTH_LONG).show()
}
```

```xml
<!-- Biometric prompt trigger button -->
<com.google.android.material.button.MaterialButton
    android:id="@+id/btnFaceAuth"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:text="Use Face ID / Fingerprint"
    app:icon="@drawable/ic_face"
    android:textAllCaps="false"
    style="@style/Widget.Material3.Button.TonalButton" />
```

```java
// Java — BiometricPrompt
BiometricPrompt.PromptInfo promptInfo = new BiometricPrompt.PromptInfo.Builder()
    .setTitle("Authenticate")
    .setSubtitle("Use biometrics to continue")
    .setAllowedAuthenticators(
        BiometricManager.Authenticators.BIOMETRIC_STRONG |
        BiometricManager.Authenticators.DEVICE_CREDENTIAL)
    .build();

BiometricPrompt biometricPrompt = new BiometricPrompt(this,
    ContextCompat.getMainExecutor(this),
    new BiometricPrompt.AuthenticationCallback() {
        @Override
        public void onAuthenticationSucceeded(@NonNull BiometricPrompt.AuthenticationResult r) {
            navigateToHome();
        }
        @Override
        public void onAuthenticationError(int code, @NonNull CharSequence msg) {
            showError(msg.toString());
        }
    });

biometricPrompt.authenticate(promptInfo);
```

---

## 7. Multi-Factor Authentication (MFA) — OTP Verify Screen

```xml
<!-- res/layout/fragment_mfa.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical" android:gravity="center" android:padding="32dp">

    <ImageView android:layout_width="72dp" android:layout_height="72dp"
        android:src="@drawable/ic_shield_lock"
        android:tint="?attr/colorPrimary" android:layout_marginBottom="20dp" />

    <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="Two-Step Verification"
        android:textAppearance="?attr/textAppearanceHeadlineSmall"
        android:textAlignment="center" />

    <TextView android:id="@+id/tvMfaHint"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:layout_marginTop="8dp" android:layout_marginBottom="28dp"
        android:text="Enter the 6-digit code sent to your phone"
        android:textAlignment="center"
        android:textAppearance="?attr/textAppearanceBodyMedium"
        android:textColor="?attr/colorOnSurfaceVariant" />

    <!-- 6 OTP boxes -->
    <LinearLayout android:id="@+id/otpContainer"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:orientation="horizontal" android:gravity="center">

        <!-- Repeat this for otpField1 through otpField6 -->
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/otpField1"
            android:layout_width="44dp" android:layout_height="52dp"
            android:layout_margin="4dp"
            android:inputType="number" android:maxLength="1"
            android:gravity="center" android:textSize="20sp"
            android:textAlignment="center"
            android:background="@drawable/otp_box_bg" />
        <!-- ... repeat for otpField2 - otpField6 ... -->
    </LinearLayout>

    <com.google.android.material.button.MaterialButton
        android:id="@+id/btnVerifyMfa"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_marginTop="28dp"
        android:text="Verify" android:textAllCaps="false"
        android:enabled="false" />

    <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:layout_marginTop="16dp" android:orientation="horizontal"
        android:gravity="center_vertical">
        <TextView android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Didn't receive it? "
            android:textAppearance="?attr/textAppearanceBodySmall" />
        <TextView android:id="@+id/tvResend"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:text="Resend (60s)"
            android:textColor="?attr/colorPrimary"
            android:textAppearance="?attr/textAppearanceBodySmall"
            android:textStyle="bold" />
    </LinearLayout>
</LinearLayout>
```

```xml
<!-- res/drawable/otp_box_bg.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_focused="true">
        <shape android:shape="rectangle">
            <stroke android:width="2dp" android:color="?attr/colorPrimary" />
            <corners android:radius="8dp" />
            <solid android:color="?attr/colorSurface" />
        </shape>
    </item>
    <item>
        <shape android:shape="rectangle">
            <stroke android:width="1dp" android:color="?attr/colorOutline" />
            <corners android:radius="8dp" />
            <solid android:color="?attr/colorSurface" />
        </shape>
    </item>
</selector>
```

```kotlin
class MfaFragment : Fragment(R.layout.fragment_mfa) {

    private val otpFields by lazy {
        listOf(binding.otpField1, binding.otpField2, binding.otpField3,
               binding.otpField4, binding.otpField5, binding.otpField6)
    }
    private var resendTimer: CountDownTimer? = null

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        setupOtpInputs()
        startResendTimer(60)

        binding.btnVerifyMfa.setOnClickListener {
            val code = otpFields.joinToString("") { it.text.toString() }
            viewModel.verifyOtp(code)
        }

        binding.tvResend.setOnClickListener {
            if (binding.tvResend.isEnabled) {
                viewModel.resendOtp()
                startResendTimer(60)
            }
        }

        viewModel.otpResult.observe(viewLifecycleOwner) { success ->
            if (success) findNavController().navigate(R.id.action_mfa_to_home)
            else {
                otpFields.forEach { it.setText("") }
                otpFields[0].requestFocus()
                binding.btnVerifyMfa.isEnabled = false
                Snackbar.make(binding.root, "Invalid code. Try again.", Snackbar.LENGTH_SHORT).show()
            }
        }
    }

    private fun setupOtpInputs() {
        otpFields.forEachIndexed { index, field ->
            field.addTextChangedListener {
                if (it?.length == 1 && index < otpFields.lastIndex)
                    otpFields[index + 1].requestFocus()
                binding.btnVerifyMfa.isEnabled = otpFields.all { f -> f.text?.length == 1 }
            }
            field.setOnKeyListener { _, keyCode, event ->
                if (keyCode == KeyEvent.KEYCODE_DEL
                    && event.action == KeyEvent.ACTION_DOWN
                    && field.text.isNullOrEmpty() && index > 0) {
                    otpFields[index - 1].requestFocus()
                    otpFields[index - 1].setText("")
                }
                false
            }
        }
        otpFields.first().requestFocus()
    }

    private fun startResendTimer(seconds: Int) {
        binding.tvResend.isEnabled = false
        resendTimer?.cancel()
        resendTimer = object : CountDownTimer(seconds * 1000L, 1000L) {
            override fun onTick(ms: Long)  { binding.tvResend.text = "Resend (${ms / 1000}s)" }
            override fun onFinish()        { binding.tvResend.text = "Resend Code"; binding.tvResend.isEnabled = true }
        }.start()
    }

    override fun onDestroyView() { super.onDestroyView(); resendTimer?.cancel() }
}
```

```java
// Java — OTP auto-advance
field.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
    @Override public void onTextChanged(CharSequence s, int start, int before, int count) {}
    @Override public void afterTextChanged(Editable s) {
        if (s.length() == 1 && index < otpFields.size() - 1)
            otpFields.get(index + 1).requestFocus();
        boolean allFilled = true;
        for (EditText f : otpFields) if (f.getText().length() != 1) allFilled = false;
        binding.btnVerifyMfa.setEnabled(allFilled);
    }
});
```

---

# Module 17 — AI Application Components
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. AI Chat Interface

```xml
<!-- activity_chat.xml -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Top App Bar -->
    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:title="AI Assistant"
            app:subtitle="Online" />
    </com.google.android.material.appbar.AppBarLayout>

    <!-- Chat Messages -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvChat"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:paddingBottom="80dp"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <!-- Input Bar (anchored to bottom) -->
    <LinearLayout
        android:id="@+id/inputBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:orientation="horizontal"
        android:padding="8dp"
        android:background="?attr/colorSurface"
        android:elevation="4dp">

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="Message AI..."
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etMessage"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:maxLines="4"
                android:inputType="textMultiLine|textCapSentences" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnSend"
            android:layout_width="48dp"
            android:layout_height="48dp"
            android:layout_marginStart="8dp"
            app:icon="@drawable/ic_send"
            app:iconSize="20dp"
            style="@style/Widget.Material3.Button.IconButton.Filled" />
    </LinearLayout>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

## 2. AI Message Bubbles

```kotlin
class ChatAdapter(private val currentUserId: String) :
    ListAdapter<ChatMessage, RecyclerView.ViewHolder>(MessageDiff()) {

    companion object {
        const val TYPE_USER = 0
        const val TYPE_AI = 1
        const val TYPE_TYPING = 2
    }

    override fun getItemViewType(position: Int) = when {
        getItem(position).isTypingIndicator -> TYPE_TYPING
        getItem(position).isFromUser -> TYPE_USER
        else -> TYPE_AI
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = when (viewType) {
        TYPE_USER -> UserMsgVH(ItemChatUserBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
        TYPE_TYPING -> TypingVH(ItemChatTypingBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
        else -> AIMsgVH(ItemChatAiBinding.inflate(
            LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        val msg = getItem(position)
        when (holder) {
            is UserMsgVH -> holder.bind(msg)
            is AIMsgVH -> holder.bind(msg)
            is TypingVH -> holder.startTypingAnimation()
        }
    }
}
```

## 3. Streaming Response

```kotlin
// Simulate streaming AI response (typewriter effect)
fun streamResponse(textView: TextView, fullText: String, delayMs: Long = 20) {
    textView.text = ""
    var index = 0
    val handler = Handler(Looper.getMainLooper())
    val runnable = object : Runnable {
        override fun run() {
            if (index <= fullText.length) {
                textView.text = fullText.substring(0, index)
                index++
                handler.postDelayed(this, delayMs)
            }
        }
    }
    handler.post(runnable)
}

// With Kotlin coroutines
fun streamResponseCo(textView: TextView, textFlow: Flow<String>) {
    lifecycleScope.launch {
        val sb = StringBuilder()
        textFlow.collect { chunk ->
            sb.append(chunk)
            textView.text = sb
            // Auto-scroll to bottom
            binding.rvChat.scrollToPosition(adapter.itemCount - 1)
        }
    }
}
```

## 4. Typing Indicator (Three Dots Animation)

```kotlin
class TypingIndicatorView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null
) : LinearLayout(context, attrs) {

    private val dots = mutableListOf<View>()
    private val animators = mutableListOf<ObjectAnimator>()

    init {
        orientation = HORIZONTAL
        gravity = Gravity.CENTER_VERTICAL
        val dotSize = 8f.dpToPx(context).toInt()
        val dotMargin = 4f.dpToPx(context).toInt()

        repeat(3) { i ->
            val dot = View(context).apply {
                layoutParams = MarginLayoutParams(dotSize, dotSize).apply {
                    marginStart = if (i == 0) 0 else dotMargin
                }
                background = GradientDrawable().apply {
                    shape = GradientDrawable.OVAL
                    setColor(ContextCompat.getColor(context, R.color.md_theme_onSurfaceVariant))
                }
            }
            dots.add(dot)
            addView(dot)
        }
    }

    fun startAnimation() {
        dots.forEachIndexed { i, dot ->
            val anim = ObjectAnimator.ofFloat(dot, View.TRANSLATION_Y, 0f, -8f, 0f).apply {
                duration = 600
                startDelay = i * 150L
                repeatCount = ObjectAnimator.INFINITE
                interpolator = AccelerateDecelerateInterpolator()
            }
            animators.add(anim)
            anim.start()
        }
    }

    fun stopAnimation() {
        animators.forEach { it.cancel() }
        animators.clear()
        dots.forEach { it.translationY = 0f }
    }
}
```

## 5. AI Suggestion Chips

```kotlin
class SuggestionChipAdapter(
    private val onSuggestionClick: (String) -> Unit
) : RecyclerView.Adapter<SuggestionChipAdapter.VH>() {

    private var suggestions: List<String> = emptyList()

    inner class VH(val chip: Chip) : RecyclerView.ViewHolder(chip)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): VH {
        val chip = Chip(parent.context).apply {
            isCheckable = false
            style { chipStyle ->
                // Apply suggestion chip style
            }
            setOnClickListener { onSuggestionClick(text.toString()) }
        }
        return VH(chip)
    }

    override fun onBindViewHolder(holder: VH, position: Int) {
        holder.chip.text = suggestions[position]
    }

    override fun getItemCount() = suggestions.size

    fun setSuggestions(list: List<String>) {
        suggestions = list
        notifyDataSetChanged()
    }
}
```

## 6. AI Input Area (Text + Voice + File Attachment)

**Visual Use Case:** Full-featured AI prompt bar — attach files, speak, or type; mic swaps to send icon.

```xml
<!-- res/layout/layout_ai_input_area.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="vertical"
    android:background="?attr/colorSurface"
    android:elevation="4dp">

    <!-- Attachment chip preview row (shown when files are attached) -->
    <HorizontalScrollView android:id="@+id/attachPreviewScroll"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:visibility="gone" android:scrollbars="none"
        android:paddingStart="12dp" android:paddingEnd="12dp" android:paddingTop="8dp">
        <com.google.android.material.chip.ChipGroup
            android:id="@+id/chipGroupAttachments"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            app:singleLine="true" />
    </HorizontalScrollView>

    <!-- Input row -->
    <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
        android:orientation="horizontal" android:gravity="bottom"
        android:paddingStart="8dp" android:paddingEnd="8dp"
        android:paddingTop="8dp" android:paddingBottom="8dp">

        <ImageButton android:id="@+id/btnAttach"
            android:layout_width="40dp" android:layout_height="40dp"
            android:src="@drawable/ic_attach_file"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:tint="?attr/colorOnSurfaceVariant"
            android:contentDescription="Attach file" />

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="0dp" android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="4dp" android:layout_marginEnd="4dp"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etAiInput"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:hint="Ask anything…"
                android:maxLines="5" android:minLines="1"
                android:inputType="textMultiLine|textCapSentences"
                android:scrollbars="vertical" />
        </com.google.android.material.textfield.TextInputLayout>

        <!-- Mic when empty → Send when text present -->
        <ImageButton android:id="@+id/btnVoiceOrSend"
            android:layout_width="40dp" android:layout_height="40dp"
            android:src="@drawable/ic_mic"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:tint="?attr/colorPrimary"
            android:contentDescription="Voice input" />
    </LinearLayout>
</LinearLayout>
```

```kotlin
class AiInputAreaFragment : Fragment(R.layout.layout_ai_input_area) {

    private val attachedFiles = mutableListOf<Uri>()

    private val filePicker = registerForActivityResult(ActivityResultContracts.GetContent()) { uri ->
        uri?.let { addAttachmentChip(it) }
    }

    private val voiceLauncher = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) { result ->
        if (result.resultCode == Activity.RESULT_OK) {
            val text = result.data
                ?.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS)
                ?.firstOrNull() ?: return@registerForActivityResult
            val cursor = binding.etAiInput.selectionStart.coerceAtLeast(0)
            binding.etAiInput.text?.insert(cursor, text)
        }
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Swap mic ↔ send icon based on whether input has text
        binding.etAiInput.addTextChangedListener { text ->
            val hasText = !text.isNullOrBlank()
            binding.btnVoiceOrSend.setImageResource(
                if (hasText) R.drawable.ic_send else R.drawable.ic_mic
            )
            binding.btnVoiceOrSend.contentDescription =
                if (hasText) "Send message" else "Voice input"
        }

        binding.btnVoiceOrSend.setOnClickListener {
            val text = binding.etAiInput.text.toString().trim()
            if (text.isNotEmpty()) sendMessage(text)
            else startVoiceInput()
        }

        binding.btnAttach.setOnClickListener { filePicker.launch("*/*") }
    }

    private fun sendMessage(text: String) {
        viewModel.sendMessage(text, attachedFiles.toList())
        binding.etAiInput.setText("")
        attachedFiles.clear()
        binding.chipGroupAttachments.removeAllViews()
        binding.attachPreviewScroll.visibility = View.GONE
    }

    private fun startVoiceInput() {
        val intent = Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH).apply {
            putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM)
            putExtra(RecognizerIntent.EXTRA_PROMPT, "Speak your message…")
        }
        try { voiceLauncher.launch(intent) }
        catch (e: ActivityNotFoundException) {
            Snackbar.make(binding.root, "Voice input not available", Snackbar.LENGTH_SHORT).show()
        }
    }

    private fun addAttachmentChip(uri: Uri) {
        attachedFiles.add(uri)
        binding.attachPreviewScroll.visibility = View.VISIBLE
        val chip = Chip(requireContext()).apply {
            text = getFileName(uri)
            isCloseIconVisible = true
            setOnCloseIconClickListener {
                attachedFiles.remove(uri)
                binding.chipGroupAttachments.removeView(this)
                if (attachedFiles.isEmpty()) binding.attachPreviewScroll.visibility = View.GONE
            }
        }
        binding.chipGroupAttachments.addView(chip)
    }

    private fun getFileName(uri: Uri): String {
        return requireContext().contentResolver
            .query(uri, null, null, null, null)?.use { c ->
                val i = c.getColumnIndex(OpenableColumns.DISPLAY_NAME)
                c.moveToFirst(); c.getString(i)
            } ?: "file"
    }
}
```

```java
// Java — swap mic/send icon
binding.etAiInput.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
    @Override public void onTextChanged(CharSequence s, int start, int before, int count) {}
    @Override public void afterTextChanged(Editable s) {
        boolean hasText = s != null && s.toString().trim().length() > 0;
        binding.btnVoiceOrSend.setImageResource(hasText ? R.drawable.ic_send : R.drawable.ic_mic);
    }
});
binding.btnVoiceOrSend.setOnClickListener(v -> {
    String text = binding.etAiInput.getText().toString().trim();
    if (!text.isEmpty()) sendMessage(text);
    else startVoiceInput();
});
```

---

# Module 18 — Premium App Patterns
> Part of the Android UI/UX Encyclopedia | See `00_INDEX.md` for full TOC

---

## 1. Banking App Dashboard Pattern

```kotlin
// Structure: CoordinatorLayout + CollapsingToolbar + Grid cards
class BankingDashboardFragment : Fragment() {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        setupBalanceCard()
        setupQuickActions()
        setupTransactionList()
    }

    private fun setupBalanceCard() {
        // Animated balance counter
        ValueAnimator.ofFloat(0f, accountBalance).apply {
            duration = 1200
            interpolator = DecelerateInterpolator()
            addUpdateListener { animator ->
                val value = animator.animatedValue as Float
                binding.tvBalance.text = "$%.2f".format(value)
            }
        }.start()
    }

    private fun setupQuickActions() {
        val actions = listOf(
            QuickAction(R.drawable.ic_send, "Send"),
            QuickAction(R.drawable.ic_receive, "Receive"),
            QuickAction(R.drawable.ic_pay, "Pay Bills"),
            QuickAction(R.drawable.ic_more, "More")
        )
        binding.rvQuickActions.adapter = QuickActionAdapter(actions)
        binding.rvQuickActions.layoutManager =
            LinearLayoutManager(context, LinearLayoutManager.HORIZONTAL, false)
    }
}
```

## 2. Social Media Feed Pattern

```kotlin
// Feed with mixed content types
class FeedAdapter : ListAdapter<FeedItem, RecyclerView.ViewHolder>(FeedDiff()) {
    companion object {
        const val TYPE_STORY_ROW = 0
        const val TYPE_POST_IMAGE = 1
        const val TYPE_POST_VIDEO = 2
        const val TYPE_POST_TEXT = 3
        const val TYPE_AD = 4
        const val TYPE_SUGGESTED = 5
    }

    override fun getItemViewType(position: Int) = when (getItem(position).type) {
        FeedItemType.STORY_ROW -> TYPE_STORY_ROW
        FeedItemType.IMAGE_POST -> TYPE_POST_IMAGE
        FeedItemType.VIDEO_POST -> TYPE_POST_VIDEO
        FeedItemType.TEXT_POST -> TYPE_POST_TEXT
        FeedItemType.AD -> TYPE_AD
        FeedItemType.SUGGESTED -> TYPE_SUGGESTED
    }
}
```

## 3. E-Commerce Product Card

```xml
<!-- Product card with image, title, price, rating, and add-to-cart -->
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="12dp"
    android:clickable="true" android:focusable="true">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <!-- Product image with badge -->
        <FrameLayout android:layout_width="match_parent" android:layout_height="200dp">
            <ImageView android:id="@+id/ivProduct"
                android:layout_width="match_parent" android:layout_height="match_parent"
                android:scaleType="centerCrop" />
            <com.google.android.material.chip.Chip
                android:id="@+id/chipDiscount"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:layout_margin="8dp" android:text="-20%"
                android:textColor="#FFFFFF"
                app:chipBackgroundColor="#F44336"
                style="@style/Widget.Material3.Chip.Suggestion" />
            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnWishlist"
                android:layout_width="36dp" android:layout_height="36dp"
                android:layout_gravity="top|end" android:layout_margin="8dp"
                app:icon="@drawable/ic_favorite_border"
                style="@style/Widget.Material3.Button.IconButton" />
        </FrameLayout>

        <!-- Product info -->
        <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
            android:orientation="vertical" android:padding="12dp">
            <TextView android:id="@+id/tvProductName"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceTitleSmall"
                android:maxLines="2" android:ellipsize="end" />
            <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
                android:orientation="horizontal" android:gravity="center_vertical"
                android:layout_marginTop="4dp">
                <TextView android:id="@+id/tvPrice"
                    android:layout_width="0dp" android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:textAppearance="?attr/textAppearanceTitleMedium"
                    android:textColor="?attr/colorPrimary" />
                <RatingBar android:id="@+id/ratingBar"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:numStars="5" android:stepSize="0.5"
                    style="@style/Widget.AppCompat.RatingBar.Small" />
            </LinearLayout>
            <com.google.android.material.button.MaterialButton
                android:id="@+id/btnAddToCart"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:layout_marginTop="8dp"
                android:text="Add to Cart"
                app:icon="@drawable/ic_cart"
                style="@style/Widget.Material3.Button.TonalButton" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

## 4. Streaming App Home (Netflix-style)

```kotlin
class StreamingHomeAdapter : ListAdapter<ContentRow, StreamingHomeAdapter.VH>(RowDiff()) {
    inner class VH(val b: ItemContentRowBinding) : RecyclerView.ViewHolder(b.root)

    override fun onCreateViewHolder(parent: ViewGroup, type: Int) =
        VH(ItemContentRowBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: VH, position: Int) {
        val row = getItem(position)
        with(holder.b) {
            tvRowTitle.text = row.title
            // Nested horizontal RecyclerView
            rvRowItems.apply {
                adapter = ThumbnailAdapter(row.items) { item -> onContentClick(item) }
                layoutManager = LinearLayoutManager(context, LinearLayoutManager.HORIZONTAL, false)
                setRecycledViewPool(sharedPool) // reuse across rows for performance
            }
        }
    }

    private val sharedPool = RecyclerView.RecycledViewPool()
}
```

## 5. Productivity App (Task Manager)

```kotlin
// Task item with priority color, due date, and completion state
class TaskAdapter : ListAdapter<Task, TaskAdapter.VH>(TaskDiff()) {
    inner class VH(val b: ItemTaskBinding) : RecyclerView.ViewHolder(b.root)

    override fun onBindViewHolder(holder: VH, position: Int) {
        val task = getItem(position)
        with(holder.b) {
            checkTask.isChecked = task.isCompleted
            tvTaskTitle.apply {
                text = task.title
                paintFlags = if (task.isCompleted)
                    paintFlags or Paint.STRIKE_THRU_TEXT_FLAG
                else
                    paintFlags and Paint.STRIKE_THRU_TEXT_FLAG.inv()
                alpha = if (task.isCompleted) 0.5f else 1f
            }

            // Priority indicator
            viewPriority.setBackgroundColor(when (task.priority) {
                Priority.HIGH -> Color.parseColor("#F44336")
                Priority.MEDIUM -> Color.parseColor("#FF9800")
                Priority.LOW -> Color.parseColor("#4CAF50")
            })

            // Due date
            tvDueDate.text = task.dueDate?.format(DateTimeFormatter.ofPattern("MMM d"))
            tvDueDate.setTextColor(
                if (task.isOverdue) ContextCompat.getColor(root.context, R.color.md_theme_error)
                else ContextCompat.getColor(root.context, R.color.md_theme_onSurfaceVariant)
            )

            checkTask.setOnCheckedChangeListener { _, isChecked ->
                onTaskChecked(task, isChecked)
            }
        }
    }
}
```

---

## Performance Cheat Sheet

| Pattern | Optimization |
|---------|-------------|
| Large RecyclerView | Use `setHasFixedSize(true)`, `DiffUtil`, view recycling |
| Nested RecyclerViews | Share `RecycledViewPool`, set `isNestedScrollingEnabled = false` |
| Images | Use Glide/Coil with placeholder, cache strategy |
| Heavy layouts | Use `ConstraintLayout` to flatten hierarchy |
| Fragment recreation | Use `ViewModel` + `SavedStateHandle` |
| Animations | Use hardware layers `LAYER_TYPE_HARDWARE` |
| Lists in NestedScrollView | Avoid when possible; use `ConcatAdapter` instead |
| Bitmaps | Use `inSampleSize`, `inPreferredConfig = RGB_565` for non-alpha |
| ViewBinding | Always null `_binding` in `onDestroyView()` |
| Coroutines | Use `lifecycleScope` for UI, `viewModelScope` for data |

---

## 6. Pixel / Stock Android App Pattern

**Description:** Google Pixel-style: edge-to-edge, Dynamic Color, large M3 SearchBar header, ExtendedFAB that shrinks on scroll.

```xml
<!-- res/layout/activity_pixel_style.xml -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:fitsSystemWindows="true"
        app:liftOnScroll="true"
        android:background="?attr/colorBackground">

        <!-- Large title collapses on scroll -->
        <com.google.android.material.appbar.MaterialToolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:minHeight="?attr/actionBarSize"
            app:title="Photos"
            app:titleTextAppearance="?attr/textAppearanceHeadlineSmall"
            app:navigationIcon="@drawable/ic_menu"
            app:layout_scrollFlags="scroll|enterAlways|snap" />

        <!-- Pixel-style inline SearchBar under title -->
        <com.google.android.material.search.SearchBar
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:layout_margin="12dp"
            android:hint="Search in Photos"
            app:navigationIcon="@drawable/ic_search" />
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent" android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <!-- ExtendedFAB shrinks to icon on scroll -->
    <com.google.android.material.floatingactionbutton.ExtendedFloatingActionButton
        android:id="@+id/extFab"
        android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp"
        android:text="Add" android:textAllCaps="false"
        app:icon="@drawable/ic_add" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```kotlin
class PixelStyleActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        DynamicColors.applyToActivityIfAvailable(this)   // Material You
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()                               // Edge-to-edge
        setContentView(binding.root)

        ViewCompat.setOnApplyWindowInsetsListener(binding.root) { v, insets ->
            val bars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(bars.left, bars.top, bars.right, bars.bottom)
            insets
        }

        setSupportActionBar(binding.toolbar)

        // Shrink ExtendedFAB when scrolling down, expand when scrolling up
        binding.recyclerView.addOnScrollListener(object : RecyclerView.OnScrollListener() {
            override fun onScrolled(rv: RecyclerView, dx: Int, dy: Int) {
                if (dy > 8)       binding.extFab.shrink()
                else if (dy < -8) binding.extFab.extend()
            }
        })
    }
}
```

```java
// Java
DynamicColors.applyToActivityIfAvailable(this);
super.onCreate(savedInstanceState);
EdgeToEdge.enable(this);
setContentView(binding.getRoot());

binding.recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrolled(@NonNull RecyclerView rv, int dx, int dy) {
        if (dy > 8)       binding.extFab.shrink();
        else if (dy < -8) binding.extFab.extend();
    }
});
```

### Key Characteristics
| Feature | Implementation |
|---------|---------------|
| Dynamic Color | `DynamicColors.applyToActivityIfAvailable(this)` before `super.onCreate` |
| Edge-to-edge | `enableEdgeToEdge()` + WindowInsets padding |
| Large title | `MaterialToolbar` with `textAppearanceHeadlineSmall` + scroll flags |
| Search | `SearchBar` below toolbar, no separate activity |
| FAB behavior | `ExtendedFloatingActionButton` shrinks/extends on scroll |

---

## 7. Samsung One UI App Pattern

**Description:** Large collapsing title, rounded card groups, bottom navigation with labels always visible.

```xml
<!-- res/layout/activity_oneui_style.xml -->
<androidx.coordinatorlayout.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:background="?attr/colorBackground">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:background="?attr/colorBackground" app:elevation="0dp">

        <!-- One UI: extra-large title, collapses on scroll -->
        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent" android:layout_height="120dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:contentScrim="?attr/colorBackground"
            app:titleEnabled="true"
            app:expandedTitleTextAppearance="?attr/textAppearanceDisplaySmall"
            app:collapsedTitleTextAppearance="?attr/textAppearanceTitleLarge"
            app:expandedTitleMarginStart="24dp"
            app:expandedTitleMarginBottom="12dp">

            <com.google.android.material.appbar.MaterialToolbar
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:title="Home"
                app:layout_collapseMode="pin"
                app:menu="@menu/menu_oneui" />
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <!-- Scrollable content with card groups -->
    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent" android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:paddingBottom="80dp" android:clipToPadding="false">

        <LinearLayout android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical" android:padding="16dp">

            <!-- Rounded cards (One UI style: cornerRadius=20dp, elevation=0, stroke=1dp) -->
            <com.google.android.material.card.MaterialCardView
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:layout_marginBottom="16dp"
                app:cardCornerRadius="20dp" app:cardElevation="0dp"
                app:strokeWidth="1dp" app:strokeColor="?attr/colorOutlineVariant">
                <!-- card content -->
            </com.google.android.material.card.MaterialCardView>
        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <!-- Bottom Navigation — always show labels (One UI style) -->
    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNav"
        android:layout_width="match_parent" android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        app:menu="@menu/bottom_nav_menu"
        app:labelVisibilityMode="labeled"
        app:itemIconSize="24dp" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

```kotlin
class OneUiStyleActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)

        val navController = findNavController(R.id.navHostFragment)
        binding.bottomNav.setupWithNavController(navController)

        // Keep toolbar title in sync with collapsing layout
        binding.collapsingToolbar.title = "Home"
    }
}
```

### One UI Design Rules
| Rule | Value |
|------|-------|
| Card corner radius | `20dp` |
| Card elevation | `0dp` (flat) |
| Card stroke | `1dp`, `colorOutlineVariant` |
| Section labels | Bold, colored (`colorPrimary`), above each card group |
| Bottom nav | `labelVisibilityMode="labeled"` (always show labels) |
| Collapsed title | `textAppearanceTitleLarge` |
| Expanded title | `textAppearanceDisplaySmall` |

---

## 8. Messaging App Pattern (Full Chat Screen)

**Description:** Chat activity with contact header, message bubbles (sent/received), typing indicator, and full input bar.

```xml
<!-- res/layout/activity_chat.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent" android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- Header toolbar with avatar + name + status -->
    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/chatToolbar"
        android:layout_width="match_parent" android:layout_height="?attr/actionBarSize"
        app:navigationIcon="@drawable/ic_arrow_back">

        <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:orientation="horizontal" android:gravity="center_vertical">

            <com.google.android.material.imageview.ShapeableImageView
                android:id="@+id/ivChatAvatar"
                android:layout_width="36dp" android:layout_height="36dp"
                android:scaleType="centerCrop"
                app:shapeAppearanceOverlay="@style/CircleImageView" />

            <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:orientation="vertical" android:layout_marginStart="10dp">
                <TextView android:id="@+id/tvContactName"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:textAppearance="?attr/textAppearanceTitleMedium" />
                <TextView android:id="@+id/tvContactStatus"
                    android:layout_width="wrap_content" android:layout_height="wrap_content"
                    android:text="Online" android:textColor="#4CAF50"
                    android:textAppearance="?attr/textAppearanceLabelSmall" />
            </LinearLayout>
        </LinearLayout>
    </com.google.android.material.appbar.MaterialToolbar>

    <!-- Message list -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvMessages"
        android:layout_width="match_parent" android:layout_height="0dp"
        android:layout_weight="1" android:padding="8dp"
        android:clipToPadding="false" android:scrollbars="none" />

    <!-- Typing indicator -->
    <LinearLayout android:id="@+id/typingIndicator"
        android:layout_width="wrap_content" android:layout_height="36dp"
        android:layout_marginStart="16dp" android:layout_marginBottom="4dp"
        android:orientation="horizontal" android:gravity="center_vertical"
        android:background="@drawable/bubble_received_bg"
        android:paddingStart="14dp" android:paddingEnd="14dp"
        android:visibility="gone">
        <!-- Three animated dots from Module 17 Typing Indicator -->
    </LinearLayout>

    <!-- Input bar -->
    <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content"
        android:orientation="horizontal" android:gravity="bottom"
        android:paddingStart="8dp" android:paddingEnd="8dp"
        android:paddingTop="8dp" android:paddingBottom="8dp"
        android:background="?attr/colorSurface">

        <ImageButton android:id="@+id/btnChatAttach"
            android:layout_width="40dp" android:layout_height="40dp"
            android:src="@drawable/ic_attach_file"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:tint="?attr/colorOnSurfaceVariant" />

        <com.google.android.material.textfield.TextInputLayout
            android:layout_width="0dp" android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_marginStart="4dp" android:layout_marginEnd="4dp"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/etChatInput"
                android:layout_width="match_parent" android:layout_height="wrap_content"
                android:hint="Message…" android:maxLines="4"
                android:inputType="textMultiLine|textCapSentences" />
        </com.google.android.material.textfield.TextInputLayout>

        <ImageButton android:id="@+id/btnChatSend"
            android:layout_width="40dp" android:layout_height="40dp"
            android:src="@drawable/ic_send"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:tint="?attr/colorPrimary"
            android:contentDescription="Send" />
    </LinearLayout>
</LinearLayout>
```

```xml
<!-- res/layout/item_chat_sent.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="horizontal" android:gravity="end" android:padding="4dp">

    <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:orientation="vertical" android:maxWidth="280dp"
        android:background="@drawable/bubble_sent_bg" android:padding="10dp">

        <TextView android:id="@+id/tvMessage"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textColor="?attr/colorOnPrimaryContainer"
            android:textAppearance="?attr/textAppearanceBodyMedium" />

        <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:orientation="horizontal" android:layout_gravity="end"
            android:layout_marginTop="4dp" android:gravity="center_vertical">
            <TextView android:id="@+id/tvTime"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:textAppearance="?attr/textAppearanceLabelSmall"
                android:textColor="?attr/colorOnPrimaryContainer" android:alpha="0.6" />
            <ImageView android:id="@+id/ivStatus"
                android:layout_width="14dp" android:layout_height="14dp"
                android:layout_marginStart="4dp" />
        </LinearLayout>
    </LinearLayout>
</LinearLayout>

<!-- res/layout/item_chat_received.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" android:layout_height="wrap_content"
    android:orientation="horizontal" android:padding="4dp">

    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/ivSenderAvatar"
        android:layout_width="32dp" android:layout_height="32dp"
        android:scaleType="centerCrop" android:layout_marginEnd="8dp"
        app:shapeAppearanceOverlay="@style/CircleImageView" />

    <LinearLayout android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:orientation="vertical" android:maxWidth="280dp"
        android:background="@drawable/bubble_received_bg" android:padding="10dp">

        <TextView android:id="@+id/tvMessage"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyMedium" />

        <TextView android:id="@+id/tvTime"
            android:layout_width="wrap_content" android:layout_height="wrap_content"
            android:layout_gravity="end" android:layout_marginTop="4dp"
            android:textAppearance="?attr/textAppearanceLabelSmall"
            android:textColor="?attr/colorOnSurfaceVariant" />
    </LinearLayout>
</LinearLayout>
```

```xml
<!-- res/drawable/bubble_sent_bg.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="?attr/colorPrimaryContainer" />
    <corners android:radius="18dp" android:bottomRightRadius="4dp" />
</shape>

<!-- res/drawable/bubble_received_bg.xml -->
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="?attr/colorSurfaceVariant" />
    <corners android:radius="18dp" android:bottomLeftRadius="4dp" />
</shape>
```

```kotlin
data class ChatMessage(
    val id: String, val text: String, val time: String,
    val isSent: Boolean, val status: MsgStatus = MsgStatus.DELIVERED
)
enum class MsgStatus { SENDING, SENT, DELIVERED, READ }

class ChatAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {

    private val messages = mutableListOf<ChatMessage>()

    fun add(msg: ChatMessage)           { messages.add(msg);          notifyItemInserted(messages.lastIndex) }
    fun submitAll(list: List<ChatMessage>) { messages.clear(); messages.addAll(list); notifyDataSetChanged() }

    override fun getItemViewType(pos: Int) = if (messages[pos].isSent) 1 else 2

    override fun onCreateViewHolder(parent: ViewGroup, type: Int): RecyclerView.ViewHolder {
        val inf = LayoutInflater.from(parent.context)
        return if (type == 1)
            SentVH(ItemChatSentBinding.inflate(inf, parent, false))
        else
            ReceivedVH(ItemChatReceivedBinding.inflate(inf, parent, false))
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, pos: Int) {
        val m = messages[pos]
        when (holder) {
            is SentVH -> {
                holder.b.tvMessage.text = m.text
                holder.b.tvTime.text    = m.time
                holder.b.ivStatus.setImageResource(when (m.status) {
                    MsgStatus.SENDING   -> R.drawable.ic_check_gray
                    MsgStatus.SENT      -> R.drawable.ic_check
                    MsgStatus.DELIVERED -> R.drawable.ic_check_double
                    MsgStatus.READ      -> R.drawable.ic_check_double_blue
                })
            }
            is ReceivedVH -> {
                holder.b.tvMessage.text = m.text
                holder.b.tvTime.text    = m.time
            }
        }
    }

    override fun getItemCount() = messages.size
    inner class SentVH(val b: ItemChatSentBinding) : RecyclerView.ViewHolder(b.root)
    inner class ReceivedVH(val b: ItemChatReceivedBinding) : RecyclerView.ViewHolder(b.root)
}

// Fragment wiring
class ChatFragment : Fragment(R.layout.activity_chat) {

    private val adapter = ChatAdapter()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val lm = LinearLayoutManager(requireContext()).apply { stackFromEnd = true }
        binding.rvMessages.layoutManager = lm
        binding.rvMessages.adapter = adapter

        // Scroll to bottom when keyboard opens
        binding.rvMessages.addOnLayoutChangeListener { _, _, _, _, bottom, _, _, _, oldBottom ->
            if (bottom < oldBottom) binding.rvMessages.post {
                binding.rvMessages.scrollToPosition(adapter.itemCount - 1)
            }
        }

        binding.btnChatSend.setOnClickListener {
            val text = binding.etChatInput.text.toString().trim()
            if (text.isNotEmpty()) { viewModel.send(text); binding.etChatInput.setText("") }
        }

        viewModel.messages.observe(viewLifecycleOwner) { list ->
            adapter.submitAll(list)
            binding.rvMessages.scrollToPosition(list.lastIndex)
        }

        viewModel.contactTyping.observe(viewLifecycleOwner) { typing ->
            binding.typingIndicator.visibility = if (typing) View.VISIBLE else View.GONE
        }
    }
}
```

```java
// Java — send button
binding.btnChatSend.setOnClickListener(v -> {
    String text = binding.etChatInput.getText().toString().trim();
    if (!text.isEmpty()) {
        viewModel.send(text);
        binding.etChatInput.setText("");
    }
});

// Scroll to bottom on new message
viewModel.getMessages().observe(getViewLifecycleOwner(), messages -> {
    adapter.submitAll(messages);
    if (!messages.isEmpty())
        binding.rvMessages.scrollToPosition(messages.size() - 1);
});
```

### Key Patterns
| Element | Implementation |
|---------|---------------|
| Newest at bottom | `LinearLayoutManager(stackFromEnd = true)` |
| Bubble shape | Rounded corners, one corner flat (sent = bottom-right, received = bottom-left) |
| Message status icons | Single check → double check → blue double check |
| Keyboard scroll fix | `addOnLayoutChangeListener` to detect keyboard open |
| Typing indicator | `visibility = VISIBLE/GONE` on contact typing state |
