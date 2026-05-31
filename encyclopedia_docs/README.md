# 📖 Android UI/UX Encyclopedia Docs

> Complete offline reference for every Android UI component — XML, Kotlin, and Java.

This folder contains all **module documentation files** that make up the Android UI/UX Encyclopedia. Each `.md` file is a self-contained, fully-coded reference module covering a specific area of Android UI development.

---

## 📂 Files in This Folder

| File | Module | What's Inside |
|------|--------|---------------|
| `00_INDEX.md` | Master Index | Full table of contents, dependencies quick-ref, base theme setup, color tokens, typography scale, Android version compatibility table |
| `01_material2.md` | Material Design 2 | Buttons, Cards, TextInput, Chips, FAB, BottomNav, AppBar, Snackbar, Dialogs, Sliders, Tabs, Progress Indicators, Date/Time Pickers |
| `02_material3.md` | Material Design 3 | Dynamic Color (Material You), SearchBar & SearchView, MaterialSwitch, Segmented Buttons, Navigation Rail, Side Sheets, Top App Bars, Bottom Sheets, ShapeableImageView |
| `03_androidx.md` | AndroidX / Jetpack | RecyclerView & ViewPager2, ConstraintLayout & MotionLayout, CoordinatorLayout, SwipeRefreshLayout, ViewBinding, Edge-to-Edge |
| `04_lists.md` | List Structures | Vertical, Grid, Staggered, Horizontal, Nested, Expandable, Sectioned lists, Sticky Headers, Infinite Scroll, Chat Lists, Swipe Actions, Drag & Drop, Multi-select |
| `05_to_10_cards_nav_search_inputs_selection_media.md` | Cards → Media | **Cards:** Elevated, Outlined, Expandable, Tinder swipe, Glassmorphism, Neumorphism, Dashboard, Carousel · **Navigation:** Bottom Nav, Rail, Drawer, Bottom Sheet Nav, Tabs, Multi-Pane, Master-Detail, Tablet Adaptive · **Search:** SearchBar, Floating, Voice, AI-Assisted, Expandable · **Inputs:** OTP, PIN, Password, Dropdown, AutoComplete, Tags, Rich Text Editor, Emoji Picker · **Selection:** Switch, Checkbox, Radio, Segmented Control, Toggle Groups, Time Selector, Chips · **Media:** ShapeableImageView, Avatars (single/group/status), Stories, Gallery, Audio Player, Infinite Carousel, ExoPlayer |
| `11_to_18_effects_animations_loading_dashboard_settings_auth_ai_patterns.md` | Effects → Patterns | **Effects:** Blur, Glassmorphism, Gradients, Neumorphism, Particles, Glow/Neon, Dynamic Color · **Animations:** MotionLayout, Shared Elements, Lottie, Spring, Fling/Physics, Page Transforms · **Loading:** Shimmer, Skeleton, Paging 3, Lazy Load (Glide), Pull-to-Refresh · **Dashboard:** KPI Cards, Charts, Statistic Widgets, Timeline, Finance Dashboard, Line/Bar/Pie Graphs · **Settings:** Google-style, Samsung One UI-style, Account, Security, Appearance, AndroidX Preferences · **Auth:** Login, Sign Up, OTP, Passkey/FIDO2, Biometric, Face Auth, MFA · **AI Components:** Chat UI, Streaming, Typing Indicator, Suggestion Chips, AI Input Area (Voice+File+Text) · **App Patterns:** Pixel/Stock Android, Samsung One UI, Banking, Social Media, Streaming, Messaging, E-Commerce |
| `19_to_23_drawables_accessibility_architecture_dimens_libraries.md` | Drawables → Libraries | All drawable types, Canvas & custom views, ShapeAppearance system, Accessibility guidelines, MVVM + StateFlow architecture, Navigation graph, dimens.xml, Typography reference, Kotlin extensions, Library alternatives, Full Gradle dependencies |
| `24_to_27_advanced_foldable_performance_cheatsheets.md` | Advanced → Cheat Sheets | Collapsing profile header, Multi-step wizard forms, Drag & drop reordering, Foldable & hinge APIs, Window Size Classes, Performance checklists, 10 quick-reference cheat sheets, Component Decision Tree |

---

## 🚀 How to Use

1. **Start with `00_INDEX.md`** — it has the full table of contents and all quick-reference snippets (dependencies, themes, colors, typography).
2. **Jump to the relevant module file** — every section is self-contained with XML layout, Kotlin implementation, and Java implementation.
3. **Copy-paste ready** — all code blocks are complete, no placeholder comments, no `// TODO` stubs.

---

## 🏷️ Symbol Legend

| Symbol | Meaning |
|--------|---------|
| 🟢 | Material 3 (recommended) |
| 🟡 | Material 2 (legacy, still valid) |
| 🔵 | AndroidX / Jetpack |
| ⚠️ | Deprecated or avoid |
| ✅ | Best practice |
| 🧪 | Experimental / Beta |

---

## 📋 Coverage Summary

- **18 UI/UX modules** fully documented
- **XML + Kotlin + Java** for every component
- **No Jetpack Compose** — pure View system only
- **API 21–35+** compatible
- **Material Design 2 & 3** both covered
- **Gap-free** — every feature listed in the index is implemented
