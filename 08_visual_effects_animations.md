# Visual Effects & Animation Systems — Android UI Encyclopedia

> **File:** `08_visual_effects_animations.md` | **API Target:** 35

---

## Table of Contents

### Visual Effects
1. [Blur Effects](#1-blur-effects)
2. [Frosted Glass / Glassmorphism](#2-frosted-glass--glassmorphism)
3. [Neumorphism](#3-neumorphism)
4. [Dynamic Color (Material You)](#4-dynamic-color-material-you)
5. [Shadows & Elevation](#5-shadows--elevation)
6. [Gradient Backgrounds](#6-gradient-backgrounds)
7. [Animated Backgrounds](#7-animated-backgrounds)
8. [Glow Effects](#8-glow-effects)
9. [Parallax Effects](#9-parallax-effects)

### Animation Systems
10. [ViewPropertyAnimator](#10-viewpropertyanimator)
11. [ObjectAnimator & ValueAnimator](#11-objectanimator--valueanimator)
12. [AnimatorSet](#12-animatorset)
13. [Spring Animations](#13-spring-animations)
14. [Physics Animations (FlingAnimation)](#14-physics-animations-flinganimation)
15. [MotionLayout Animations](#15-motionlayout-animations)
16. [Shared Element Transitions](#16-shared-element-transitions)
17. [Lottie Animations](#17-lottie-animations)
18. [Vector Animations (AnimatedVectorDrawable)](#18-vector-animations-animatedvectordrawable)
19. [Page Transformers (ViewPager2)](#19-page-transformers-viewpager2)
20. [Transition Framework](#20-transition-framework)
21. [Circular Reveal](#21-circular-reveal)
22. [Ripple & State Drawables](#22-ripple--state-drawables)

---

## 1. Blur Effects

### Description
Applies a Gaussian blur to a view or bitmap. API 31+ supports hardware-accelerated `RenderEffect`. Older APIs need Glide transformations or custom bitmap blurring.

### Java — RenderEffect (API 31+)
```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    // Simple blur
    RenderEffect blurEffect = RenderEffect.createBlurEffect(
        25f, 25f, Shader.TileMode.CLAMP);
    view.setRenderEffect(blurEffect);

    // Chained effect: blur + colorFilter
    RenderEffect blur = RenderEffect.createBlurEffect(20f, 20f, Shader.TileMode.CLAMP);
    ColorFilter dim = new PorterDuffColorFilter(
        Color.argb(100, 0, 0, 0), PorterDuff.Mode.SRC_OVER);
    RenderEffect colorEffect = RenderEffect.createColorFilterEffect(dim, blur);
    view.setRenderEffect(colorEffect);

    // Remove effect
    view.setRenderEffect(null);
}
```

### Kotlin — RenderEffect (API 31+)
```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    val blur = RenderEffect.createBlurEffect(25f, 25f, Shader.TileMode.CLAMP)
    view.setRenderEffect(blur)
}
```

### Java — Legacy Blur via RenderScript (deprecated) → use Glide
```java
// Add to build.gradle:
// implementation 'jp.wasabeef:glide-transformations:4.3.0'
// implementation 'jp.co.cyberagent.android:gpuimage:2.1.0'

Glide.with(context)
    .load(imageUrl)
    .apply(RequestOptions.bitmapTransform(new BlurTransformation(context, 25)))
    .into(imageView);
```

### Java — Custom bitmap blur (no library)
```java
public static Bitmap blurBitmap(Context context, Bitmap input, float radius) {
    Bitmap output = Bitmap.createBitmap(input.getWidth(), input.getHeight(),
        Bitmap.Config.ARGB_8888);

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
        Canvas canvas = new Canvas(output);
        Paint paint = new Paint();
        paint.setMaskFilter(new BlurMaskFilter(radius, BlurMaskFilter.Blur.NORMAL));
        canvas.drawBitmap(input, 0, 0, paint);
    } else {
        // Stack blur algorithm
        output = stackBlur(input, (int) radius);
    }
    return output;
}
```

### Window Blur (background blur) — API 31+
```java
// Blur everything behind the window (for dialogs, bottom sheets)
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    getWindow().setBackgroundBlurRadius(40);
    getWindow().getDecorView().setBackgroundColor(Color.argb(180, 255, 255, 255));
}
```

---

## 2. Frosted Glass / Glassmorphism

### Description
A frosted glass panel: semi-transparent background over a blurred backdrop. Achieved by combining `RenderEffect` blur on background + semi-transparent overlay.

### `res/drawable/bg_frosted_glass.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#60FFFFFF" />
    <corners android:radius="24dp" />
    <stroke android:width="1dp" android:color="#80FFFFFF" />
</shape>
```

### `res/layout/layout_frosted_card.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <!-- Full background image -->
    <ImageView
        android:id="@+id/img_blur_bg"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop"
        android:src="@drawable/hero_bg" />

    <!-- Frosted glass overlay -->
    <LinearLayout
        android:id="@+id/frosted_panel"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:layout_gravity="bottom"
        android:orientation="vertical"
        android:background="@drawable/bg_frosted_glass"
        android:padding="20dp">

        <TextView
            android:id="@+id/tv_glass_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Glass Title"
            android:textColor="#FFFFFF"
            android:textAppearance="?attr/textAppearanceTitleLarge" />

        <TextView
            android:id="@+id/tv_glass_body"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Supporting text on glass surface"
            android:textColor="#B3FFFFFF"
            android:textAppearance="?attr/textAppearanceBodyMedium" />
    </LinearLayout>
</FrameLayout>
```

### Java — Apply blur + glass overlay
```java
ImageView bgImage = view.findViewById(R.id.img_blur_bg);
LinearLayout frostedPanel = view.findViewById(R.id.frosted_panel);

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    bgImage.setRenderEffect(
        RenderEffect.createBlurEffect(30f, 30f, Shader.TileMode.CLAMP));
} else {
    Glide.with(this)
        .load(R.drawable.hero_bg)
        .apply(RequestOptions.bitmapTransform(new BlurTransformation(this, 25)))
        .into(bgImage);
}

// Animate panel in
frostedPanel.setAlpha(0f);
frostedPanel.setTranslationY(50f);
frostedPanel.animate()
    .alpha(1f)
    .translationY(0f)
    .setDuration(500)
    .setInterpolator(new DecelerateInterpolator())
    .start();
```

---

## 3. Neumorphism

See `05_card_systems.md` § Neumorphism Cards for full implementation.

### Pressed state drawable
```xml
<!-- res/drawable/bg_neumorphic_pressed.xml -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Inverted shadows for pressed state -->
    <item android:bottom="4dp" android:right="4dp">
        <shape>
            <solid android:color="#C8C8C8" />
            <corners android:radius="20dp" />
        </shape>
    </item>
    <item android:top="4dp" android:left="4dp">
        <shape>
            <solid android:color="#FFFFFF" />
            <corners android:radius="20dp" />
        </shape>
    </item>
    <item>
        <shape>
            <solid android:color="#E0E5EC" />
            <corners android:radius="20dp" />
        </shape>
    </item>
</layer-list>
```

### State selector
```xml
<!-- res/drawable/bg_neumorphic_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@drawable/bg_neumorphic_pressed" />
    <item android:drawable="@drawable/bg_neumorphic_raised" />
</selector>
```

---

## 4. Dynamic Color (Material You)

See `02_material_design_3.md` § Dynamic Color for full setup.

### Java — Extract palette from bitmap
```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.wallpaper);
Palette.from(bitmap).generate(palette -> {
    if (palette != null) {
        int dominant = palette.getDominantColor(
            ContextCompat.getColor(this, R.color.md3_primary));
        int vibrant = palette.getVibrantColor(dominant);
        int muted = palette.getMutedColor(dominant);
        int darkVibrant = palette.getDarkVibrantColor(dominant);

        // Apply to UI
        toolbar.setBackgroundColor(dominant);
        fab.setBackgroundTintList(ColorStateList.valueOf(vibrant));
        cardView.setCardBackgroundColor(muted);
    }
});
```

### Kotlin — Dynamic color from Glide-loaded image
```kotlin
Glide.with(this).asBitmap().load(imageUrl).into(object : CustomTarget<Bitmap>() {
    override fun onResourceReady(resource: Bitmap, t: Transition<in Bitmap>?) {
        Palette.from(resource).generate { palette ->
            val vibrant = palette?.getVibrantColor(
                ContextCompat.getColor(this@MainActivity, R.color.md3_primary))
            vibrant?.let { fab.backgroundTintList = ColorStateList.valueOf(it) }
        }
    }
    override fun onLoadCleared(placeholder: Drawable?) {}
})
```

---

## 5. Shadows & Elevation

### XML — Standard elevation
```xml
<!-- MD3 tonal elevation (surface tint applied) -->
<com.google.android.material.card.MaterialCardView
    app:cardElevation="6dp"
    app:cardMaxElevation="8dp" />

<!-- AppBarLayout elevation -->
<com.google.android.material.appbar.AppBarLayout
    android:elevation="4dp"
    app:liftOnScroll="true"
    app:liftOnScrollTargetViewId="@id/recycler" />
```

### Java — Custom shadow
```java
// Elevation on any view
view.setElevation(dpToPx(8));

// Outline provider for custom shape shadow
view.setOutlineProvider(new ViewOutlineProvider() {
    @Override
    public void getOutline(View view, Outline outline) {
        outline.setRoundRect(0, 0, view.getWidth(), view.getHeight(), dpToPx(16));
    }
});
view.setClipToOutline(true);
view.setElevation(dpToPx(8));
```

### Custom colored shadow (API 28+)
```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
    view.setOutlineAmbientShadowColor(Color.parseColor("#806750A4"));
    view.setOutlineSpotShadowColor(Color.parseColor("#806750A4"));
    view.setElevation(dpToPx(12));
    view.setOutlineProvider(new ViewOutlineProvider() {
        @Override public void getOutline(View v, Outline outline) {
            outline.setRoundRect(0, 0, v.getWidth(), v.getHeight(), dpToPx(16));
        }
    });
    view.setClipToOutline(true);
}
```

---

## 6. Gradient Backgrounds

### `res/drawable/bg_gradient_primary.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:angle="135"
        android:startColor="#6750A4"
        android:centerColor="#7965AF"
        android:endColor="#9C89C4"
        android:type="linear" />
    <corners android:radius="0dp" />
</shape>
```

### `res/drawable/bg_gradient_radial.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:type="radial"
        android:gradientRadius="200dp"
        android:startColor="#806750A4"
        android:endColor="#00000000"
        android:centerX="0.5"
        android:centerY="0.5" />
</shape>
```

### `res/drawable/bg_gradient_card.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:angle="180"
        android:startColor="#FF6B35"
        android:endColor="#F7C59F"
        android:type="linear" />
    <corners android:radius="16dp" />
</shape>
```

### Java — Programmatic gradient
```java
GradientDrawable gradient = new GradientDrawable(
    GradientDrawable.Orientation.TOP_BOTTOM,
    new int[]{
        Color.parseColor("#6750A4"),
        Color.parseColor("#9C89C4")
    });
gradient.setCornerRadius(dpToPx(16));
view.setBackground(gradient);

// Animated gradient (ValueAnimator between two palettes)
int[] from = {Color.parseColor("#6750A4"), Color.parseColor("#9C89C4")};
int[] to = {Color.parseColor("#E91E63"), Color.parseColor("#FF6B35")};

ValueAnimator animator = ValueAnimator.ofFloat(0f, 1f);
animator.setDuration(3000);
animator.setRepeatCount(ValueAnimator.INFINITE);
animator.setRepeatMode(ValueAnimator.REVERSE);
animator.addUpdateListener(anim -> {
    float fraction = (float) anim.getAnimatedValue();
    int color1 = (Integer) new ArgbEvaluator().evaluate(fraction, from[0], to[0]);
    int color2 = (Integer) new ArgbEvaluator().evaluate(fraction, from[1], to[1]);
    gradient.setColors(new int[]{color1, color2});
    view.setBackground(gradient);
});
animator.start();
```

---

## 7. Animated Backgrounds

### `res/drawable/animated_gradient_bg.xml`
```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/bg_gradient_blue" android:duration="3000" />
    <item android:drawable="@drawable/bg_gradient_purple" android:duration="3000" />
    <item android:drawable="@drawable/bg_gradient_teal" android:duration="3000" />
</animation-list>
```

### Java — Animate gradient with ValueAnimator
```java
int[] colors = {
    Color.parseColor("#6750A4"),
    Color.parseColor("#E91E63"),
    Color.parseColor("#00BCD4"),
    Color.parseColor("#4CAF50")
};

ValueAnimator gradientAnimator = ValueAnimator.ofFloat(0f, colors.length - 1);
gradientAnimator.setDuration(6000);
gradientAnimator.setRepeatCount(ValueAnimator.INFINITE);
gradientAnimator.setRepeatMode(ValueAnimator.RESTART);
gradientAnimator.setInterpolator(new LinearInterpolator());

GradientDrawable gradientDrawable = new GradientDrawable(
    GradientDrawable.Orientation.BL_TR,
    new int[]{colors[0], colors[1]});
gradientDrawable.setCornerRadius(0f);
view.setBackground(gradientDrawable);

ArgbEvaluator evaluator = new ArgbEvaluator();
gradientAnimator.addUpdateListener(anim -> {
    float value = (float) anim.getAnimatedValue();
    int index = (int) value % (colors.length - 1);
    float fraction = value - index;

    int color1 = (int) evaluator.evaluate(fraction, colors[index], colors[index + 1]);
    int color2 = (int) evaluator.evaluate(fraction,
        colors[(index + 1) % colors.length],
        colors[(index + 2) % colors.length]);

    gradientDrawable.setColors(new int[]{color1, color2});
});
gradientAnimator.start();
```

---

## 8. Glow Effects

### Java — Paint glow (Canvas)
```java
public class GlowView extends View {
    private Paint glowPaint;
    private Paint solidPaint;
    private float glowRadius = 30f;

    public GlowView(Context context, AttributeSet attrs) {
        super(context, attrs);
        setLayerType(LAYER_TYPE_SOFTWARE, null); // required for MaskFilter

        glowPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        glowPaint.setColor(Color.parseColor("#6750A4"));
        glowPaint.setMaskFilter(new BlurMaskFilter(glowRadius, BlurMaskFilter.Blur.OUTER));

        solidPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        solidPaint.setColor(Color.parseColor("#6750A4"));
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        float cx = getWidth() / 2f;
        float cy = getHeight() / 2f;
        float radius = Math.min(cx, cy) - glowRadius;

        // Draw glow first
        canvas.drawCircle(cx, cy, radius, glowPaint);
        // Draw solid on top
        canvas.drawCircle(cx, cy, radius, solidPaint);
    }
}
```

### Animated glow pulse (Java)
```java
ValueAnimator glowPulse = ValueAnimator.ofFloat(20f, 50f);
glowPulse.setDuration(1000);
glowPulse.setRepeatMode(ValueAnimator.REVERSE);
glowPulse.setRepeatCount(ValueAnimator.INFINITE);
glowPulse.addUpdateListener(anim -> {
    float radius = (float) anim.getAnimatedValue();
    glowView.setGlowRadius(radius);
    glowView.invalidate();
});
glowPulse.start();
```

---

## 9. Parallax Effects

### Java — Parallax on scroll
```java
NestedScrollView scrollView = findViewById(R.id.nested_scroll);
ImageView parallaxImage = findViewById(R.id.img_parallax);
float parallaxFactor = 0.5f;

scrollView.setOnScrollChangeListener(
    (NestedScrollView.OnScrollChangeListener) (v, scrollX, scrollY, oldX, oldY) -> {
        parallaxImage.setTranslationY(scrollY * parallaxFactor);
    });
```

### MotionLayout Parallax
```xml
<!-- In MotionScene -->
<KeyFrameSet>
    <KeyAttribute
        motion:motionTarget="@id/img_parallax"
        motion:framePosition="100"
        android:translationY="-100dp" />
</KeyFrameSet>
```

### AppBarLayout Parallax (CollapsingToolbar)
```xml
<ImageView
    android:id="@+id/img_header"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:scaleType="centerCrop"
    app:layout_collapseMode="parallax"
    app:layout_collapseParallaxMultiplier="0.7" />
```

---

## 10. ViewPropertyAnimator

### Description
Fluent API for animating view properties. The simplest animation approach for common effects.

### Java Examples
```java
// Fade in
view.setAlpha(0f);
view.setVisibility(View.VISIBLE);
view.animate()
    .alpha(1f)
    .setDuration(300)
    .setInterpolator(new DecelerateInterpolator())
    .start();

// Fade out + hide
view.animate()
    .alpha(0f)
    .setDuration(200)
    .withEndAction(() -> view.setVisibility(View.GONE))
    .start();

// Slide in from bottom
view.setTranslationY(200f);
view.setVisibility(View.VISIBLE);
view.animate()
    .translationY(0f)
    .alpha(1f)
    .setDuration(400)
    .setInterpolator(new DecelerateInterpolator(2f))
    .start();

// Scale bounce
view.animate()
    .scaleX(1.2f).scaleY(1.2f)
    .setDuration(100)
    .withEndAction(() ->
        view.animate().scaleX(1f).scaleY(1f).setDuration(100).start())
    .start();

// Rotation
view.animate().rotation(360f).setDuration(500).start();

// Chained sequence
view.animate()
    .translationX(200f)
    .setDuration(300)
    .withEndAction(() -> view.animate()
        .alpha(0f)
        .setDuration(200)
        .withEndAction(() -> view.setVisibility(View.GONE))
        .start())
    .start();
```

### Kotlin Examples
```kotlin
// Fade in
view.alpha = 0f
view.visibility = View.VISIBLE
view.animate().alpha(1f).setDuration(300).setInterpolator(DecelerateInterpolator()).start()

// Slide in from bottom
view.translationY = 200f
view.animate()
    .translationY(0f)
    .alpha(1f)
    .setDuration(400)
    .setInterpolator(DecelerateInterpolator(2f))
    .start()

// Scale + fade out
view.animate()
    .scaleX(0f).scaleY(0f).alpha(0f)
    .setDuration(300)
    .withEndAction { view.visibility = View.GONE }
    .start()
```

### Common Interpolators
| Interpolator | Effect |
|---|---|
| `LinearInterpolator` | Constant speed |
| `AccelerateInterpolator` | Starts slow, ends fast |
| `DecelerateInterpolator` | Starts fast, ends slow |
| `AccelerateDecelerateInterpolator` | Ease in/out |
| `OvershootInterpolator` | Overshoots then settles |
| `BounceInterpolator` | Bounces at end |
| `AnticipateInterpolator` | Pulls back then shoots |
| `FastOutSlowInInterpolator` | MD standard easing |
| `LinearOutSlowInInterpolator` | MD enter easing |
| `FastOutLinearInInterpolator` | MD exit easing |

---

## 11. ObjectAnimator & ValueAnimator

### Java — ObjectAnimator
```java
// Property animation
ObjectAnimator rotateAnim = ObjectAnimator.ofFloat(view, "rotation", 0f, 360f);
rotateAnim.setDuration(1000);
rotateAnim.setRepeatCount(ObjectAnimator.INFINITE);
rotateAnim.setInterpolator(new LinearInterpolator());
rotateAnim.start();

// Custom property
ObjectAnimator colorAnim = ObjectAnimator.ofArgb(
    view, "backgroundColor",
    Color.parseColor("#6750A4"),
    Color.parseColor("#E91E63"));
colorAnim.setDuration(1500);
colorAnim.setRepeatMode(ObjectAnimator.REVERSE);
colorAnim.setRepeatCount(ObjectAnimator.INFINITE);
colorAnim.start();

// Path animation
Path path = new Path();
path.moveTo(0f, 0f);
path.quadTo(200f, 0f, 200f, 200f);
ObjectAnimator pathAnim = ObjectAnimator.ofFloat(view, View.X, View.Y, path);
pathAnim.setDuration(1000);
pathAnim.start();

// Cancel
rotateAnim.cancel();
rotateAnim.pause();
rotateAnim.resume();
```

### Java — ValueAnimator
```java
ValueAnimator counterAnim = ValueAnimator.ofInt(0, 1500);
counterAnim.setDuration(2000);
counterAnim.setInterpolator(new DecelerateInterpolator());
counterAnim.addUpdateListener(anim -> {
    int value = (int) anim.getAnimatedValue();
    textView.setText(String.format(Locale.getDefault(), "$%,d", value));
});
counterAnim.start();

// Float value
ValueAnimator floatAnim = ValueAnimator.ofFloat(0f, 100f);
floatAnim.setDuration(1000);
floatAnim.addUpdateListener(anim -> {
    float progress = (float) anim.getAnimatedValue();
    progressBar.setProgress((int) progress, true);
});
floatAnim.start();

// Color
ValueAnimator colorValueAnim = ValueAnimator.ofObject(
    new ArgbEvaluator(),
    Color.parseColor("#6750A4"),
    Color.parseColor("#E91E63"));
colorValueAnim.setDuration(1000);
colorValueAnim.addUpdateListener(anim -> {
    int color = (int) anim.getAnimatedValue();
    view.setBackgroundColor(color);
});
colorValueAnim.start();
```

---

## 12. AnimatorSet

### Description
Combine multiple animators to run simultaneously or sequentially.

### Java
```java
// Sequential
ObjectAnimator fadeIn = ObjectAnimator.ofFloat(view, "alpha", 0f, 1f);
ObjectAnimator slideUp = ObjectAnimator.ofFloat(view, "translationY", 100f, 0f);
ObjectAnimator scaleIn = ObjectAnimator.ofFloat(view, "scaleX", 0.8f, 1f);

AnimatorSet set = new AnimatorSet();
set.play(fadeIn).with(slideUp).with(scaleIn); // simultaneous
set.setDuration(400);
set.setInterpolator(new DecelerateInterpolator());
set.start();

// Sequential with stagger
AnimatorSet sequence = new AnimatorSet();
List<Animator> staggerAnims = new ArrayList<>();
for (int i = 0; i < childViews.size(); i++) {
    View child = childViews.get(i);
    child.setAlpha(0f);
    child.setTranslationY(40f);

    ObjectAnimator fade = ObjectAnimator.ofFloat(child, "alpha", 0f, 1f);
    ObjectAnimator slide = ObjectAnimator.ofFloat(child, "translationY", 40f, 0f);
    AnimatorSet childSet = new AnimatorSet();
    childSet.play(fade).with(slide);
    childSet.setDuration(300);
    childSet.setStartDelay(i * 80L);
    staggerAnims.add(childSet);
}
sequence.playTogether(staggerAnims);
sequence.start();

// Listen to events
set.addListener(new AnimatorListenerAdapter() {
    @Override public void onAnimationStart(Animator animation) {}
    @Override public void onAnimationEnd(Animator animation) {}
    @Override public void onAnimationCancel(Animator animation) {}
});
```

### Kotlin
```kotlin
val fadeIn = ObjectAnimator.ofFloat(view, "alpha", 0f, 1f)
val slideUp = ObjectAnimator.ofFloat(view, "translationY", 100f, 0f)

AnimatorSet().apply {
    play(fadeIn).with(slideUp)
    duration = 400
    interpolator = DecelerateInterpolator()
    start()
}
```

---

## 13. Spring Animations

### Dependency
```groovy
implementation 'androidx.dynamicanimation:dynamicanimation:1.0.0'
```

### Java — Spring animation
```java
import androidx.dynamicanimation.animation.SpringAnimation;
import androidx.dynamicanimation.animation.SpringForce;

// Basic spring
SpringAnimation springY = new SpringAnimation(view, SpringAnimation.TRANSLATION_Y, 0f);
springY.getSpring().setStiffness(SpringForce.STIFFNESS_MEDIUM);
springY.getSpring().setDampingRatio(SpringForce.DAMPING_RATIO_MEDIUM_BOUNCY);
springY.setStartValue(300f);
springY.start();

// Custom spring
SpringForce force = new SpringForce(0f)
    .setStiffness(800f)
    .setDampingRatio(0.4f);

SpringAnimation scaleX = new SpringAnimation(view, SpringAnimation.SCALE_X)
    .setSpring(force)
    .setStartValue(0.5f);

SpringAnimation scaleY = new SpringAnimation(view, SpringAnimation.SCALE_Y)
    .setSpring(force)
    .setStartValue(0.5f);

scaleX.start();
scaleY.start();

// On touch — follow finger with spring
view.setOnTouchListener((v, event) -> {
    if (event.getAction() == MotionEvent.ACTION_DOWN) {
        springX.cancel();
        springY.cancel();
        v.setX(event.getRawX() - v.getWidth() / 2f);
        v.setY(event.getRawY() - v.getHeight() / 2f);
    } else if (event.getAction() == MotionEvent.ACTION_UP) {
        springX.setStartValue(v.getX());
        springX.animateToFinalPosition(originalX);
        springY.setStartValue(v.getY());
        springY.animateToFinalPosition(originalY);
    }
    return true;
});
```

### Spring Parameters Guide
| Parameter | Low Value | High Value |
|---|---|---|
| `STIFFNESS` | Slow, wobbly | Fast, stiff |
| `DAMPING_RATIO` | High bounce (0.0) | No bounce (1.0) |
| Recommended stiffness | `STIFFNESS_LOW` = 200 | `STIFFNESS_HIGH` = 10000 |

---

## 14. Physics Animations (FlingAnimation)

### Java — Fling after swipe
```java
import androidx.dynamicanimation.animation.FlingAnimation;

GestureDetector gestureDetector = new GestureDetector(this,
    new GestureDetector.SimpleOnGestureListener() {
        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2,
                float velocityX, float velocityY) {
            FlingAnimation flingX = new FlingAnimation(view, DynamicAnimation.X)
                .setStartVelocity(velocityX)
                .setMaxValue(maxX)
                .setMinValue(minX)
                .setFriction(1.1f);
            flingX.start();

            FlingAnimation flingY = new FlingAnimation(view, DynamicAnimation.Y)
                .setStartVelocity(velocityY)
                .setFriction(1.1f);
            flingY.start();
            return true;
        }
    });
view.setOnTouchListener((v, event) -> {
    gestureDetector.onTouchEvent(event);
    return true;
});
```

---

## 15. MotionLayout Animations

### See `03_androidx_components.md` § MotionLayout for full reference.

### Quick patterns

#### Scroll-driven collapse
```xml
<OnSwipe
    motion:touchAnchorId="@id/header"
    motion:dragDirection="dragUp"
    motion:touchAnchorSide="top" />
```

#### Click-triggered flip
```xml
<OnClick
    motion:targetId="@id/card_front"
    motion:clickAction="toggle" />
```

#### KeyFrameSet with arc motion
```xml
<KeyFrameSet>
    <KeyPosition
        motion:motionTarget="@id/icon"
        motion:framePosition="50"
        motion:keyPositionType="parentRelative"
        motion:percentY="0.1"
        motion:percentX="0.5" />
</KeyFrameSet>
```

---

## 16. Shared Element Transitions

### `res/transition/shared_element.xml`
```xml
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300"
    android:interpolator="@android:interpolator/fast_out_slow_in">
    <changeImageTransform />
    <changeBounds />
    <changeTransform />
    <changeClipBounds />
</transitionSet>
```

### Java — Activity-level shared element
```java
// In source Activity
Intent intent = new Intent(this, DetailActivity.class);
intent.putExtra("itemId", item.getId());

ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(
    this,
    Pair.create(imageView, "shared_image"),
    Pair.create(titleView, "shared_title")
);

startActivity(intent, options.toBundle());

// In DetailActivity
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    postponeEnterTransition(); // wait for data load

    getWindow().setSharedElementEnterTransition(
        TransitionInflater.from(this).inflateTransition(R.transition.shared_element));

    setContentView(R.layout.activity_detail);

    imageView.setTransitionName("shared_image");
    titleView.setTransitionName("shared_title");

    // After loading data
    Glide.with(this).load(imageUrl).listener(new RequestListener<Drawable>() {
        @Override public boolean onResourceReady(Drawable r, Object m,
                Target<Drawable> t, DataSource s, boolean i) {
            startPostponedEnterTransition();
            return false;
        }
        @Override public boolean onLoadFailed(@Nullable GlideException e,
                Object m, Target<Drawable> t, boolean i) {
            startPostponedEnterTransition();
            return false;
        }
    }).into(imageView);
}
```

### Java — Fragment-level shared element
```java
// Source fragment
Bundle args = new Bundle();
args.putString("itemId", item.getId());

Fragment detail = DetailFragment.newInstance(args);
detail.setSharedElementEnterTransition(
    TransitionInflater.from(requireContext())
        .inflateTransition(R.transition.shared_element));

requireActivity().getSupportFragmentManager()
    .beginTransaction()
    .addSharedElement(imageView, "shared_image")
    .replace(R.id.container, detail)
    .addToBackStack(null)
    .commit();

// In DetailFragment
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    postponeEnterTransition();
    setSharedElementEnterTransition(
        TransitionInflater.from(requireContext())
            .inflateTransition(R.transition.shared_element));
}
```

---

## 17. Lottie Animations

### Dependency
```groovy
implementation 'com.airbnb.android:lottie:6.4.0'
```

### XML
```xml
<com.airbnb.lottie.LottieAnimationView
    android:id="@+id/lottie_view"
    android:layout_width="200dp"
    android:layout_height="200dp"
    app:lottie_fileName="animation.json"
    app:lottie_autoPlay="true"
    app:lottie_loop="true"
    app:lottie_speed="1.0"
    app:lottie_colorFilter="?attr/colorPrimary" />
```

### Java
```java
LottieAnimationView lottieView = findViewById(R.id.lottie_view);

// From assets
lottieView.setAnimation("success_animation.json");
lottieView.setRepeatCount(0);
lottieView.playAnimation();

// From URL
lottieView.setAnimationFromUrl("https://assets.lottiefiles.com/example.json");
lottieView.playAnimation();

// From raw resource
lottieView.setAnimation(R.raw.loading_animation);
lottieView.setSpeed(1.5f);
lottieView.loop(true);
lottieView.playAnimation();

// Control
lottieView.pauseAnimation();
lottieView.resumeAnimation();
lottieView.cancelAnimation();
lottieView.setProgress(0.5f); // jump to 50%

// Listener
lottieView.addAnimatorListener(new AnimatorListenerAdapter() {
    @Override public void onAnimationEnd(Animator animation) {
        lottieView.setVisibility(View.GONE);
        showContent();
    }
});

// Color override
LottieProperty property = LottieProperty.COLOR;
KeyPath keyPath = new KeyPath("**");
lottieView.addValueCallback(keyPath, LottieProperty.COLOR,
    frameInfo -> ContextCompat.getColor(this, R.color.md3_primary));

// Programmatic composition
LottieComposition.Factory.fromAssetFileName(this, "anim.json", composition -> {
    lottieView.setComposition(composition);
    lottieView.playAnimation();
});
```

### Kotlin
```kotlin
val lottie: LottieAnimationView = findViewById(R.id.lottie_view)
lottie.setAnimation("success.json")
lottie.repeatCount = 0
lottie.addAnimatorListener(object : AnimatorListenerAdapter() {
    override fun onAnimationEnd(animation: Animator) {
        showSuccessState()
    }
})
lottie.playAnimation()
```

---

## 18. Vector Animations (AnimatedVectorDrawable)

### `res/drawable/ic_play_to_pause.xml` (VectorDrawable)
```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">

    <group android:name="play_group">
        <path
            android:name="play_path"
            android:fillColor="?attr/colorOnSurface"
            android:pathData="M8,5v14l11,-7z" />
    </group>
</vector>
```

### `res/drawable/avd_play_to_pause.xml`
```xml
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/ic_play_to_pause">

    <target
        android:name="play_path"
        android:animation="@animator/anim_play_to_pause" />
</animated-vector>
```

### `res/animator/anim_play_to_pause.xml`
```xml
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <objectAnimator
        android:duration="300"
        android:propertyName="pathData"
        android:valueFrom="M8,5v14l11,-7z"
        android:valueTo="M6,19h4V5H6v14zm8-14v14h4V5h-4z"
        android:valueType="pathType"
        android:interpolator="@android:interpolator/fast_out_slow_in" />
</set>
```

### Java — Trigger AVD
```java
ImageView iconButton = findViewById(R.id.icon_button);
boolean isPlaying = false;

iconButton.setOnClickListener(v -> {
    isPlaying = !isPlaying;
    AnimatedVectorDrawable avd = (AnimatedVectorDrawable)
        ContextCompat.getDrawable(this,
            isPlaying ? R.drawable.avd_play_to_pause : R.drawable.avd_pause_to_play);
    iconButton.setImageDrawable(avd);
    avd.start();
});
```

### Common AVD Uses
| Use Case | Drawable |
|---|---|
| Hamburger → Back arrow | `avd_hamburger_to_arrow` |
| Play → Pause | `avd_play_to_pause` |
| Search → Close | `avd_search_to_close` |
| Checkbox state | `avd_check_empty_to_filled` |
| Loading spinner | `avd_loading_rotate` |

---

## 19. Page Transformers (ViewPager2)

### Java — All common transformers
```java
// Zoom out (Google Play style)
viewPager.setPageTransformer((page, position) -> {
    float MIN_SCALE = 0.85f;
    float MIN_ALPHA = 0.5f;
    if (position < -1 || position > 1) {
        page.setAlpha(MIN_ALPHA);
    } else {
        float scaleFactor = Math.max(MIN_SCALE, 1 - Math.abs(position));
        page.setScaleX(scaleFactor);
        page.setScaleY(scaleFactor);
        page.setAlpha(MIN_ALPHA + (scaleFactor - MIN_SCALE) / (1 - MIN_SCALE) * (1 - MIN_ALPHA));
    }
});

// Depth (cube-like)
viewPager.setPageTransformer((page, position) -> {
    if (position < -1) page.setAlpha(0f);
    else if (position <= 0) {
        page.setAlpha(1f); page.setScaleX(1f); page.setScaleY(1f); page.setTranslationX(0f);
    } else if (position <= 1) {
        page.setAlpha(1 - position);
        page.setTranslationX(page.getWidth() * -position);
        float scale = 0.75f + (1 - 0.75f) * (1 - Math.abs(position));
        page.setScaleX(scale); page.setScaleY(scale);
    } else page.setAlpha(0f);
});

// Fan-out
viewPager.setPageTransformer((page, position) -> {
    page.setTranslationX(-position * page.getWidth() * 0.25f);
    page.setScaleX(1 - 0.15f * Math.abs(position));
    page.setScaleY(1 - 0.15f * Math.abs(position));
    page.setAlpha(1 - 0.5f * Math.abs(position));
});

// Carousel with peek
viewPager.setOffscreenPageLimit(3);
int pageMargin = dpToPx(8);
int pageOffset = dpToPx(24);
viewPager.setPageTransformer((page, position) -> {
    float offset = position * -(2 * pageOffset + pageMargin);
    page.setTranslationX(offset);
});
```

---

## 20. Transition Framework

### `res/transition/slide_right.xml`
```xml
<slide xmlns:android="http://schemas.android.com/apk/res/android"
    android:slideEdge="end"
    android:duration="300"
    android:interpolator="@android:interpolator/fast_out_slow_in" />
```

### `res/transition/fade_through.xml`
```xml
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300">
    <fade android:fadingMode="fade_out" />
    <fade android:fadingMode="fade_in" />
</transitionSet>
```

### Java — Activity transitions
```java
// Enter/exit transitions
getWindow().setEnterTransition(
    TransitionInflater.from(this).inflateTransition(R.transition.slide_right));
getWindow().setExitTransition(
    TransitionInflater.from(this).inflateTransition(android.R.transition.fade));

// Start activity with transitions
ActivityOptions options = ActivityOptions.makeSceneTransitionAnimation(this);
startActivity(intent, options.toBundle());

// Exclude status/nav bar from transition
getWindow().getEnterTransition()
    .excludeTarget(android.R.id.statusBarBackground, true);
getWindow().getEnterTransition()
    .excludeTarget(android.R.id.navigationBarBackground, true);
```

### Java — Fragment transitions
```java
Fragment detail = new DetailFragment();
detail.setEnterTransition(new Slide(Gravity.END).setDuration(300));
detail.setExitTransition(new Fade().setDuration(200));
detail.setReturnTransition(new Slide(Gravity.END).setDuration(300));

getSupportFragmentManager().beginTransaction()
    .replace(R.id.container, detail)
    .addToBackStack(null)
    .commit();
```

### MD3 Transition Patterns
```java
// MD3 Container Transform (shared element expand)
MaterialContainerTransform transform = new MaterialContainerTransform();
transform.setDrawingViewId(R.id.root);
transform.setDuration(400);
transform.setAllContainerColors(ContextCompat.getColor(this, R.color.md3_surface));

// MD3 Shared Axis (spatial navigation)
MaterialSharedAxis axisX = new MaterialSharedAxis(MaterialSharedAxis.X, true);
axisX.setDuration(300);
fragment.setEnterTransition(axisX);
fragment.setExitTransition(new MaterialSharedAxis(MaterialSharedAxis.X, false));

// MD3 Fade Through (tab switching)
MaterialFadeThrough fadeThrough = new MaterialFadeThrough();
fadeThrough.setDuration(300);
fragment.setEnterTransition(fadeThrough);

// Requires: implementation 'com.google.android.material:material:1.12.0'
```

---

## 21. Circular Reveal

### Java — Expand from FAB
```java
View content = findViewById(R.id.content_view);
FloatingActionButton fab = findViewById(R.id.fab);

int[] fabLoc = new int[2];
fab.getLocationOnScreen(fabLoc);
int cx = fabLoc[0] + fab.getWidth() / 2;
int cy = fabLoc[1] + fab.getHeight() / 2;

// Reveal
float finalRadius = (float) Math.hypot(
    content.getWidth(), content.getHeight());
Animator reveal = ViewAnimationUtils.createCircularReveal(
    content, cx, cy, 0f, finalRadius);
reveal.setDuration(400);
reveal.setInterpolator(new FastOutSlowInInterpolator());
content.setVisibility(View.VISIBLE);
reveal.start();

// Hide
float initialRadius = (float) Math.hypot(content.getWidth(), content.getHeight());
Animator hide = ViewAnimationUtils.createCircularReveal(
    content, cx, cy, initialRadius, 0f);
hide.setDuration(300);
hide.addListener(new AnimatorListenerAdapter() {
    @Override public void onAnimationEnd(Animator animation) {
        content.setVisibility(View.INVISIBLE);
    }
});
hide.start();
```

---

## 22. Ripple & State Drawables

### `res/drawable/ripple_primary.xml`
```xml
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorPrimary">
    <item android:id="@android:id/mask">
        <shape>
            <solid android:color="@android:color/white" />
            <corners android:radius="8dp" />
        </shape>
    </item>
</ripple>
```

### `res/drawable/ripple_borderless.xml`
```xml
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorControlHighlight" />
```

### State list drawable
```xml
<!-- res/drawable/bg_button_state.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <shape>
            <solid android:color="?attr/colorPrimaryVariant" />
            <corners android:radius="8dp" />
        </shape>
    </item>
    <item android:state_focused="true">
        <shape>
            <solid android:color="?attr/colorPrimaryContainer" />
            <corners android:radius="8dp" />
            <stroke android:width="2dp" android:color="?attr/colorPrimary" />
        </shape>
    </item>
    <item android:state_enabled="false">
        <shape>
            <solid android:color="#1F000000" />
            <corners android:radius="8dp" />
        </shape>
    </item>
    <item>
        <shape>
            <solid android:color="?attr/colorPrimary" />
            <corners android:radius="8dp" />
        </shape>
    </item>
</selector>
```

### Java — Apply ripple programmatically
```java
// Create ripple
int[] attrs = new int[]{android.R.attr.colorControlHighlight};
TypedArray ta = obtainStyledAttributes(attrs);
int rippleColor = ta.getColor(0, Color.GRAY);
ta.recycle();

RippleDrawable ripple = new RippleDrawable(
    ColorStateList.valueOf(rippleColor),
    view.getBackground(),
    null);
view.setBackground(ripple);
```
