# 📱 Android UI/UX Encyclopedia

<p align="center">
  <img src="https://img.shields.io/badge/Android-API%2021--35%2B-brightgreen?logo=android" />
  <img src="https://img.shields.io/badge/Material-Design%202%20%26%203-6750A4?logo=material-design" />
  <img src="https://img.shields.io/badge/Language-Kotlin%20%26%20Java-orange?logo=kotlin" />
  <img src="https://img.shields.io/badge/UI%20System-View%20%2F%20XML-blue" />
  <img src="https://img.shields.io/badge/Compose-Not%20included-lightgrey" />
</p>

> **The complete offline reference for Android UI development.**  
> Every component. Every pattern. XML + Kotlin + Java. No Jetpack Compose. No placeholders.

---

## 🧭 What This Is

This encyclopedia is a **self-contained, offline-ready documentation set** covering every major Android UI/UX component, layout pattern, and design system available in the View-based (XML) Android development world.

Built for developers who:
- Want production-ready code they can copy directly into a project
- Need both **Kotlin and Java** implementations side-by-side
- Work with **Material Design 2 and/or Material Design 3**
- Prefer XML layouts over Jetpack Compose
- Want a single reference that doesn't require an internet connection

---

## 📂 Repository Structure

```
android-ui-encyclopedia/
│
├── README.md                          ← You are here
│
└── encyclopedia_docs/                 ← All module documentation
    ├── README.md                      ← Docs folder guide
    ├── 00_INDEX.md                    ← Master TOC + quick-reference
    ├── 01_material2.md                ← Material Design 2 components
    ├── 02_material3.md                ← Material Design 3 components
    ├── 03_androidx.md                 ← AndroidX / Jetpack components
    ├── 04_lists.md                    ← All list & RecyclerView patterns
    ├── 05_to_10_cards_nav_search_inputs_selection_media.md
    ├── 11_to_18_effects_animations_loading_dashboard_settings_auth_ai_patterns.md
    ├── 19_to_23_drawables_accessibility_architecture_dimens_libraries.md
    └── 24_to_27_advanced_foldable_performance_cheatsheets.md
```

---

## 📋 Module Overview

### Core Modules (01–04)
| Module | File | Coverage |
|--------|------|----------|
| 01 — Material Design 2 | `01_material2.md` | Buttons, Cards, TextInput, Chips, FAB, BottomNav, AppBar, Snackbar, Dialogs, Sliders, Tabs, Progress, Pickers |
| 02 — Material Design 3 | `02_material3.md` | Dynamic Color, SearchBar, MaterialSwitch, Segmented Buttons, Nav Rail, Side Sheets, M3 Dialogs, Shape system |
| 03 — AndroidX | `03_androidx.md` | RecyclerView, ViewPager2, ConstraintLayout, MotionLayout, CoordinatorLayout, DrawerLayout, ViewBinding, Edge-to-Edge |
| 04 — Lists | `04_lists.md` | Grid, Staggered, Horizontal, Nested, Expandable, Sectioned, Sticky Headers, Infinite Scroll, Chat Lists, Swipe Actions, Drag & Drop |

### Extended Modules (05–10)
> File: `05_to_10_cards_nav_search_inputs_selection_media.md`

| Module | Coverage |
|--------|----------|
| 05 — Cards | Elevated, Outlined, Expandable, Tinder Swipe, Glassmorphism, Neumorphism, Dashboard, **Carousel (ViewPager2 + peek + auto-scroll)** |
| 06 — Navigation | Bottom Nav, Rail, Drawer, Bottom Sheet Nav, **Tabs + ViewPager2**, Gesture Nav, **Multi-Pane Layout**, **Master-Detail Pattern**, **Tablet Adaptive Navigation** |
| 07 — Search | SearchBar + SearchView + Filter + Debounce, **Floating/Toolbar Search**, **Voice Search**, **AI-Assisted Search with chips**, **Expandable Search Bar** |
| 08 — Inputs | OTP, PIN, Password, Dropdown, AutoComplete, Tag Editor, **Rich Text Editor (Spannable)**, **Emoji Picker Bottom Sheet** |
| 09 — Selection | Switch, Checkbox, Radio, Date Picker, **Segmented Control (M3)**, **Multi-Select Toggle Group**, **MaterialTimePicker (12h/24h)**, **Chips Selection (single + multi)** |
| 10 — Media | ShapeableImageView, Stories, Gallery, ExoPlayer, **Avatar System (single + status + initials + group stack)**, **Audio Player UI (MediaPlayer)**, **Infinite Carousel (RecyclerView + SnapHelper)** |

### Premium Modules (11–18)
> File: `11_to_18_effects_animations_loading_dashboard_settings_auth_ai_patterns.md`

| Module | Coverage |
|--------|----------|
| 11 — Visual Effects | Blur, Frosted Glass, Gradients, Parallax, **Neumorphism (drawable + programmatic)**, **Particle Burst (Canvas)**, **Glow/Neon Effect**, **Dynamic Color (Material You)** |
| 12 — Animations | MotionLayout, Shared Elements, Lottie, Vector Animations, ViewPropertyAnimator, Spring, Page Transforms, **FlingAnimation + SpringAnimation (Physics Engine)** |
| 13 — Loading | Shimmer, Skeleton Screens, Progress Indicators, **Paging 3 (infinite scroll + load state footer)**, **Glide Lazy Loading (pause on fast scroll)**, **Pull-to-Refresh + ViewModel** |
| 14 — Dashboard | KPI Cards, Analytics Cards, **Statistic Widgets (value + trend delta)**, **Activity/Timeline Panel**, **Finance Dashboard (balance card + transactions)**, **Line + Bar + Pie Charts (MPAndroidChart)** |
| 15 — Settings | Google-Style Settings, **Samsung One UI-Style (large cards + colored icons)**, **Account/Profile Page**, **Security & Privacy Page**, **Appearance/Theme Page (dark mode + font size)**, **AndroidX Preference Screen (XML-driven)** |
| 16 — Authentication | Login Screen, **Sign Up Screen (validation + password strength)**, OTP Input, **Passkey/FIDO2 (Credential Manager)**, Biometric Prompt, **Face Authentication**, **MFA UI (6-box OTP + resend timer)** |
| 17 — AI Components | AI Chat Interface, Message Bubbles, Streaming Text, Typing Indicator, Suggestion Chips, **AI Input Area (text + voice swap + file attachment)** |
| 18 — App Patterns | Banking, Social Media, E-Commerce, Streaming, **Pixel/Stock Android Pattern (Dynamic Color + edge-to-edge + ExtendedFAB)**, **Samsung One UI Pattern (collapsing title + card groups)**, **Messaging Chat Pattern (bubbles + status ticks + typing indicator)** |

### Architecture & Advanced (19–27)
> Files: `19_to_23_...md` · `24_to_27_...md`

| Modules | Coverage |
|---------|----------|
| 19 — Drawables | Shape drawables, selectors, layer-lists, Canvas API, custom views |
| 20 — Accessibility | Content descriptions, focus order, TalkBack, contrast ratios |
| 21 — Architecture | MVVM + StateFlow, Navigation graph, SavedStateHandle, Repository pattern |
| 22 — Dimens & Typography | dimens.xml system, Material 3 type scale, Kotlin extension functions |
| 23 — Libraries | Full Gradle dependencies, library alternatives comparison |
| 24 — Advanced Patterns | Collapsing profile header, Multi-step wizard, Drag & drop reordering |
| 25 — Foldables | Hinge API, SlidingPaneLayout, Window Size Classes |
| 26 — Performance | RecyclerView optimization, memory leak checklist, bitmap handling |
| 27 — Cheat Sheets | 10 quick-reference sheets + Component Decision Tree |

---

## ⚡ Quick Start

### 1. Clone the repo
```bash
git clone https://github.com/Willykez/android-ui-encyclopedia.git
```

### 2. Open in any Markdown viewer
All files render perfectly in:
- **Android Studio** (Markdown plugin)
- **VS Code** (built-in Markdown preview)
- **Obsidian** (recommended for offline use)
- **GitHub** (online browsing)
- Any browser with a Markdown extension

### 3. Start with the index
Open `encyclopedia_docs/00_INDEX.md` — it has every quick-reference snippet you need to bootstrap a new project (dependencies, theme setup, color tokens, typography scale).

---

## 🛠️ Tech Stack Covered

| Category | Details |
|----------|---------|
| **Languages** | Kotlin + Java (side-by-side for every component) |
| **UI System** | XML View system (no Jetpack Compose) |
| **Design** | Material Design 2 (MDC) + Material Design 3 (MDC 1.6+) |
| **Architecture** | MVVM + LiveData + StateFlow + ViewBinding |
| **Min API** | 21 (Android 5.0 Lollipop) |
| **Target API** | 35 (Android 15) |
| **Image Loading** | Glide 4.x |
| **Charts** | MPAndroidChart v3.1.0 |
| **Animations** | Lottie + DynamicAnimation + MotionLayout |
| **Auth** | BiometricPrompt + Credential Manager (Passkey/FIDO2) |
| **Pagination** | Paging 3 |

---

## 📦 Key Dependencies (from `00_INDEX.md`)

```gradle
// Material Design 3
implementation 'com.google.android.material:material:1.12.0'

// AndroidX Core
implementation 'androidx.core:core-ktx:1.13.1'
implementation 'androidx.recyclerview:recyclerview:1.3.2'
implementation 'androidx.viewpager2:viewpager2:1.1.0'
implementation 'androidx.paging:paging-runtime-ktx:3.3.0'
implementation 'androidx.credentials:credentials:1.3.0'
implementation 'androidx.dynamicanimation:dynamicanimation:1.0.0'
implementation 'androidx.preference:preference-ktx:1.2.1'

// Navigation
implementation 'androidx.navigation:navigation-fragment-ktx:2.7.7'
implementation 'androidx.navigation:navigation-ui-ktx:2.7.7'

// Image Loading
implementation 'com.github.bumptech.glide:glide:4.16.0'

// Charts
implementation 'com.github.PhilJay:MPAndroidChart:v3.1.0'

// Animations
implementation 'com.airbnb.android:lottie:6.4.0'

// Shimmer
implementation 'com.facebook.shimmer:shimmer:0.5.0'
```

---

## 🗺️ Navigation Tips

- **Looking for a specific component?** → `00_INDEX.md` has the full TOC with file references.
- **Need XML only?** → Every section leads with the XML layout block.
- **Need Java?** → Every Kotlin section has a `// Java` equivalent block below it.
- **Checking dependencies?** → See the "Quick Reference: Dependencies" section in `00_INDEX.md`.
- **Dark mode support?** → All XML uses `?attr/` theme attributes — inherits dark mode automatically.

---

## 📝 Notes

- All code is **production-quality** — no placeholder comments, no `TODO` stubs.
- This is a **reference document**, not a runnable project. Copy the relevant files/classes into your own Android project.
- Designed for **offline use** — no internet required once cloned.
- Covers **pure View/XML system only** — Jetpack Compose is intentionally excluded.

---

## 👤 Author

**Willykez** — Android Developer  
Built as a personal offline reference for production Android development.

---

<p align="center">
  <sub>Android UI/UX Encyclopedia · 2025 Edition · API 21–35+ · Material 2 + 3 · XML + Kotlin + Java</sub>
</p>
