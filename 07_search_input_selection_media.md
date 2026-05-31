# Search, Input, Selection & Media Components — Android UI Encyclopedia

> **File:** `07_search_input_selection_media.md` | **API Target:** 35

---

## Table of Contents

### Search Systems
1. [SearchBar & SearchView (MD3)](#1-searchbar--searchview-md3)
2. [Floating Search](#2-floating-search)
3. [Voice Search UI](#3-voice-search-ui)
4. [Filter Search UI](#4-filter-search-ui)

### Input Components
5. [Text Fields (all variants)](#5-text-fields-all-variants)
6. [OTP / PIN Input](#6-otp--pin-input)
7. [Password Fields](#7-password-fields)
8. [AutoComplete / Dropdown](#8-autocomplete--dropdown)
9. [Chips Input (Tag Editor)](#9-chips-input-tag-editor)
10. [Rich Text / Markdown Input](#10-rich-text--markdown-input)

### Selection Components
11. [Switches](#11-switches)
12. [Checkboxes](#12-checkboxes)
13. [Radio Buttons](#13-radio-buttons)
14. [Segmented Controls](#14-segmented-controls)
15. [Date & Time Selectors](#15-date--time-selectors)
16. [Multi-Select Chips](#16-multi-select-chips)

### Media Components
17. [ImageView & ShapeableImageView](#17-imageview--shapeableimageview)
18. [Avatar Views](#18-avatar-views)
19. [Story Views](#19-story-views)
20. [Image Sliders / Carousels](#20-image-sliders--carousels)
21. [Video Player UI](#21-video-player-ui)
22. [Audio Player UI](#22-audio-player-ui)

---

## 1. SearchBar & SearchView (MD3)

See `02_material_design_3.md` § SearchBar & SearchView for the full implementation.

### Quick Reference
```xml
<!-- Persistent bar -->
<com.google.android.material.search.SearchBar
    android:id="@+id/search_bar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Search"
    app:navigationIcon="@drawable/ic_menu" />

<!-- Expandable overlay -->
<com.google.android.material.search.SearchView
    android:id="@+id/search_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:searchBar="@id/search_bar">
    <!-- result views here -->
</com.google.android.material.search.SearchView>
```

---

## 2. Floating Search

### Description
A FAB-like search button that expands into a full search bar on click.

### `res/layout/layout_floating_search.xml`
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <!-- Collapsed state: icon button -->
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab_search"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        app:srcCompat="@drawable/ic_search"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <!-- Expanded state: full search bar (initially gone) -->
    <com.google.android.material.card.MaterialCardView
        android:id="@+id/search_bar_card"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:visibility="gone"
        app:cardCornerRadius="28dp"
        app:cardElevation="8dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="56dp"
            android:orientation="horizontal"
            android:gravity="center_vertical"
            android:paddingHorizontal="16dp">

            <ImageView android:layout_width="24dp" android:layout_height="24dp"
                android:src="@drawable/ic_search" android:layout_marginEnd="12dp" />

            <EditText
                android:id="@+id/et_floating_search"
                android:layout_width="0dp"
                android:layout_height="match_parent"
                android:layout_weight="1"
                android:hint="Search..."
                android:background="@null"
                android:textAppearance="?attr/textAppearanceBodyLarge"
                android:imeOptions="actionSearch"
                android:inputType="text"
                android:singleLine="true" />

            <ImageView android:id="@+id/btn_clear_search"
                android:layout_width="24dp" android:layout_height="24dp"
                android:src="@drawable/ic_close"
                android:visibility="gone" />
        </LinearLayout>
    </com.google.android.material.card.MaterialCardView>
</androidx.constraintlayout.widget.ConstraintLayout>
```

### Java Example
```java
FloatingActionButton fabSearch = view.findViewById(R.id.fab_search);
MaterialCardView searchCard = view.findViewById(R.id.search_bar_card);
EditText etSearch = view.findViewById(R.id.et_floating_search);
ImageView btnClear = view.findViewById(R.id.btn_clear_search);

fabSearch.setOnClickListener(v -> {
    // Circular reveal animation
    int cx = (searchCard.getLeft() + searchCard.getRight()) / 2;
    int cy = searchCard.getBottom();
    float radius = Math.max(searchCard.getWidth(), searchCard.getHeight());
    Animator reveal = ViewAnimationUtils.createCircularReveal(
        searchCard, cx, cy, 0f, radius);
    reveal.setDuration(300);
    searchCard.setVisibility(View.VISIBLE);
    reveal.start();
    fabSearch.setVisibility(View.GONE);
    etSearch.requestFocus();
    InputMethodManager imm = (InputMethodManager) requireContext()
        .getSystemService(Context.INPUT_METHOD_SERVICE);
    imm.showSoftInput(etSearch, InputMethodManager.SHOW_IMPLICIT);
});

etSearch.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int st, int c, int a) {}
    @Override public void onTextChanged(CharSequence s, int st, int b, int c) {
        btnClear.setVisibility(s.length() > 0 ? View.VISIBLE : View.GONE);
        performSearch(s.toString());
    }
    @Override public void afterTextChanged(Editable s) {}
});

btnClear.setOnClickListener(v -> {
    etSearch.setText("");
    searchCard.setVisibility(View.GONE);
    fabSearch.setVisibility(View.VISIBLE);
});
```

---

## 3. Voice Search UI

### Description
Microphone button with animated waveform/ripple while recording. Integrates with `SpeechRecognizer`.

### `res/layout/layout_voice_search.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="32dp">

    <!-- Mic button with ripple background -->
    <FrameLayout
        android:layout_width="120dp"
        android:layout_height="120dp"
        android:layout_gravity="center">

        <!-- Animated ripple rings -->
        <View android:id="@+id/ripple_outer"
            android:layout_width="120dp" android:layout_height="120dp"
            android:alpha="0.3"
            android:background="@drawable/shape_circle_primary"
            android:layout_gravity="center" />

        <View android:id="@+id/ripple_inner"
            android:layout_width="96dp" android:layout_height="96dp"
            android:alpha="0.5"
            android:background="@drawable/shape_circle_primary"
            android:layout_gravity="center" />

        <com.google.android.material.floatingactionbutton.FloatingActionButton
            android:id="@+id/fab_mic"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            app:fabSize="normal"
            app:srcCompat="@drawable/ic_mic"
            app:backgroundTint="?attr/colorPrimary" />
    </FrameLayout>

    <TextView
        android:id="@+id/tv_voice_status"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Tap to speak"
        android:textAppearance="?attr/textAppearanceTitleMedium"
        android:layout_marginTop="24dp" />
</LinearLayout>
```

### Java — SpeechRecognizer
```java
private SpeechRecognizer speechRecognizer;
private Intent recognizerIntent;

private void initSpeechRecognizer() {
    speechRecognizer = SpeechRecognizer.createSpeechRecognizer(this);
    recognizerIntent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
    recognizerIntent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL,
        RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
    recognizerIntent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, Locale.getDefault());
    recognizerIntent.putExtra(RecognizerIntent.EXTRA_PARTIAL_RESULTS, true);

    speechRecognizer.setRecognitionListener(new RecognitionListener() {
        @Override public void onReadyForSpeech(Bundle params) {
            tvStatus.setText("Listening...");
            startRippleAnimation();
        }
        @Override public void onPartialResults(Bundle partialResults) {
            ArrayList<String> partial = partialResults
                .getStringArrayList(SpeechRecognizer.RESULTS_RECOGNITION);
            if (partial != null && !partial.isEmpty()) {
                tvStatus.setText(partial.get(0));
            }
        }
        @Override public void onResults(Bundle results) {
            stopRippleAnimation();
            ArrayList<String> matches = results
                .getStringArrayList(SpeechRecognizer.RESULTS_RECOGNITION);
            if (matches != null && !matches.isEmpty()) {
                String text = matches.get(0);
                onVoiceResult(text);
            }
        }
        @Override public void onError(int error) {
            stopRippleAnimation();
            tvStatus.setText("Try again");
        }
        @Override public void onBeginningOfSpeech() {}
        @Override public void onRmsChanged(float rmsdB) {
            // Scale ripple based on volume
            float scale = 1f + (rmsdB / 20f);
            rippleOuter.setScaleX(scale);
            rippleOuter.setScaleY(scale);
        }
        @Override public void onBufferReceived(byte[] buffer) {}
        @Override public void onEndOfSpeech() {}
        @Override public void onEvent(int eventType, Bundle params) {}
    });
}

private void startListening() {
    speechRecognizer.startListening(recognizerIntent);
}

private void startRippleAnimation() {
    rippleOuter.animate().scaleX(1.3f).scaleY(1.3f)
        .setDuration(600).setRepeatMode(ValueAnimator.REVERSE)
        .setRepeatCount(ValueAnimator.INFINITE).start();
}

private void stopRippleAnimation() {
    rippleOuter.animate().scaleX(1f).scaleY(1f).setDuration(200).start();
}

@Override protected void onDestroy() {
    super.onDestroy();
    if (speechRecognizer != null) speechRecognizer.destroy();
}
```

---

## 4. Filter Search UI

### Description
Search bar combined with filter chips/bottom sheet for narrowing results.

### `res/layout/layout_filter_search.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <!-- Search bar with filter button -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="8dp">

        <com.google.android.material.textfield.TextInputLayout
            android:id="@+id/til_search"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            app:startIconDrawable="@drawable/ic_search"
            app:endIconMode="clear_text"
            style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense">
            <com.google.android.material.textfield.TextInputEditText
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:hint="Search products..."
                android:imeOptions="actionSearch" />
        </com.google.android.material.textfield.TextInputLayout>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn_filter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            app:icon="@drawable/ic_filter_list"
            style="@style/Widget.Material3.Button.OutlinedButton.Icon" />
    </LinearLayout>

    <!-- Active filter chips -->
    <HorizontalScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:scrollbars="none">
        <com.google.android.material.chip.ChipGroup
            android:id="@+id/active_filters_group"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingHorizontal="8dp"
            app:singleLine="true" />
    </HorizontalScrollView>
</LinearLayout>
```

### Java — Filter management
```java
private List<String> activeFilters = new ArrayList<>();

private void addFilter(String filter) {
    if (activeFilters.contains(filter)) return;
    activeFilters.add(filter);

    Chip chip = new Chip(this);
    chip.setText(filter);
    chip.setCloseIconVisible(true);
    chip.setChipBackgroundColorResource(R.color.chip_selected_bg);
    chip.setOnCloseIconClickListener(v -> removeFilter(filter, chip));
    activeFiltersGroup.addView(chip);

    applyFilters();
}

private void removeFilter(String filter, Chip chip) {
    activeFilters.remove(filter);
    activeFiltersGroup.removeView(chip);
    applyFilters();
}

private void showFilterBottomSheet() {
    FilterBottomSheet sheet = FilterBottomSheet.newInstance(activeFilters,
        selectedFilters -> {
            activeFiltersGroup.removeAllViews();
            activeFilters.clear();
            for (String f : selectedFilters) addFilter(f);
        });
    sheet.show(getSupportFragmentManager(), "FILTERS");
}
```

---

## 5. Text Fields (all variants)

### Description
All Material text field styles with helper, error, counter, prefix/suffix.

See `01_material_design_2.md` § TextInputLayout for full reference.

### Quick Variants Summary
```xml
<!-- Dense filled -->
<com.google.android.material.textfield.TextInputLayout
    style="@style/Widget.Material3.TextInputLayout.FilledBox.Dense" ... />

<!-- Dense outlined -->
<com.google.android.material.textfield.TextInputLayout
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox.Dense" ... />

<!-- Exposed dropdown -->
<com.google.android.material.textfield.TextInputLayout
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox.ExposedDropdownMenu" ... >
    <AutoCompleteTextView ... />
</com.google.android.material.textfield.TextInputLayout>
```

---

## 6. OTP / PIN Input

### Description
A row of single-character boxes for OTP/PIN code entry.

### `res/layout/layout_otp_input.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:gravity="center"
    android:padding="16dp">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_1"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:textAppearance="?attr/textAppearanceHeadlineSmall"
        android:background="@drawable/bg_otp_box" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_2"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:background="@drawable/bg_otp_box" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_3"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:background="@drawable/bg_otp_box" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_4"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:background="@drawable/bg_otp_box" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_5"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:background="@drawable/bg_otp_box" />

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/otp_6"
        android:layout_width="48dp"
        android:layout_height="56dp"
        android:layout_margin="6dp"
        android:gravity="center"
        android:inputType="number"
        android:maxLength="1"
        android:textSize="24sp"
        android:background="@drawable/bg_otp_box" />
</LinearLayout>
```

### `res/drawable/bg_otp_box.xml`
```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_focused="true">
        <shape>
            <solid android:color="?attr/colorSurface" />
            <corners android:radius="12dp" />
            <stroke android:width="2dp" android:color="?attr/colorPrimary" />
        </shape>
    </item>
    <item>
        <shape>
            <solid android:color="?attr/colorSurfaceVariant" />
            <corners android:radius="12dp" />
            <stroke android:width="1dp" android:color="?attr/colorOutline" />
        </shape>
    </item>
</selector>
```

### Java — OTP auto-advance logic
```java
private EditText[] otpFields;

private void setupOtpFields() {
    otpFields = new EditText[]{
        findViewById(R.id.otp_1), findViewById(R.id.otp_2),
        findViewById(R.id.otp_3), findViewById(R.id.otp_4),
        findViewById(R.id.otp_5), findViewById(R.id.otp_6)
    };

    for (int i = 0; i < otpFields.length; i++) {
        final int index = i;
        otpFields[i].addTextChangedListener(new TextWatcher() {
            @Override public void beforeTextChanged(CharSequence s, int st, int c, int a) {}
            @Override public void onTextChanged(CharSequence s, int st, int b, int c) {
                if (s.length() == 1 && index < otpFields.length - 1) {
                    otpFields[index + 1].requestFocus();
                } else if (s.length() == 0 && index > 0) {
                    otpFields[index - 1].requestFocus();
                }
                if (isOtpComplete()) onOtpComplete(getOtpString());
            }
            @Override public void afterTextChanged(Editable s) {}
        });

        // Handle backspace on empty field
        otpFields[i].setOnKeyListener((v, keyCode, event) -> {
            if (keyCode == KeyEvent.KEYCODE_DEL
                    && event.getAction() == KeyEvent.ACTION_DOWN
                    && ((EditText) v).getText().toString().isEmpty()
                    && index > 0) {
                otpFields[index - 1].requestFocus();
                otpFields[index - 1].setText("");
                return true;
            }
            return false;
        });
    }
}

private boolean isOtpComplete() {
    for (EditText field : otpFields) {
        if (field.getText().toString().isEmpty()) return false;
    }
    return true;
}

private String getOtpString() {
    StringBuilder sb = new StringBuilder();
    for (EditText field : otpFields) sb.append(field.getText().toString());
    return sb.toString();
}

// Auto-fill from SMS (API 26+)
SmsRetriever.getClient(this).startSmsRetriever();
// Register broadcast receiver to parse and fill OTP
```

---

## 7. Password Fields

### XML
```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_password"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Password"
    app:endIconMode="password_toggle"
    app:helperText="At least 8 characters"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/et_password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textPassword"
        android:imeOptions="actionDone" />
</com.google.android.material.textfield.TextInputLayout>
```

### Java — Strength Indicator
```java
TextInputEditText etPassword = findViewById(R.id.et_password);
LinearProgressIndicator strengthBar = findViewById(R.id.password_strength_bar);
TextView tvStrength = findViewById(R.id.tv_strength_label);

etPassword.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int st, int c, int a) {}
    @Override public void afterTextChanged(Editable s) {}
    @Override
    public void onTextChanged(CharSequence s, int st, int b, int c) {
        int strength = calculatePasswordStrength(s.toString());
        strengthBar.setProgress(strength * 25, true);
        switch (strength) {
            case 0: case 1:
                strengthBar.setIndicatorColor(Color.RED);
                tvStrength.setText("Weak");
                break;
            case 2:
                strengthBar.setIndicatorColor(Color.YELLOW);
                tvStrength.setText("Fair");
                break;
            case 3:
                strengthBar.setIndicatorColor(Color.parseColor("#FF9800"));
                tvStrength.setText("Good");
                break;
            case 4:
                strengthBar.setIndicatorColor(Color.parseColor("#4CAF50"));
                tvStrength.setText("Strong");
                break;
        }
    }
});

private int calculatePasswordStrength(String password) {
    int score = 0;
    if (password.length() >= 8) score++;
    if (password.matches(".*[A-Z].*")) score++;
    if (password.matches(".*[0-9].*")) score++;
    if (password.matches(".*[!@#$%^&*].*")) score++;
    return score;
}
```

---

## 8. AutoComplete / Dropdown

### XML — Exposed Dropdown
```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_category"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Category"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox.ExposedDropdownMenu">

    <AutoCompleteTextView
        android:id="@+id/act_category"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="none" />
</com.google.android.material.textfield.TextInputLayout>
```

### Java — Dropdown with filtering
```java
String[] categories = {"Electronics", "Fashion", "Food", "Books", "Sports", "Toys"};
ArrayAdapter<String> adapter = new ArrayAdapter<>(this,
    R.layout.list_item_dropdown, categories);

AutoCompleteTextView actCategory = findViewById(R.id.act_category);
actCategory.setAdapter(adapter);
actCategory.setOnItemClickListener((parent, view, pos, id) -> {
    String selected = (String) parent.getItemAtPosition(pos);
    onCategorySelected(selected);
});

// Custom filtered adapter
actCategory.setThreshold(1); // start filtering from 1 char
actCategory.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int st, int c, int a) {}
    @Override public void afterTextChanged(Editable s) {}
    @Override public void onTextChanged(CharSequence s, int st, int b, int c) {
        adapter.getFilter().filter(s);
    }
});
```

---

## 9. Chips Input (Tag Editor)

### `res/layout/layout_chip_input.xml`
```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/til_tags"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Add tags"
    style="@style/Widget.Material3.TextInputLayout.OutlinedBox">

    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/et_tags"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:imeOptions="actionDone"
        android:inputType="text"
        android:singleLine="true" />
</com.google.android.material.textfield.TextInputLayout>

<com.google.android.material.chip.ChipGroup
    android:id="@+id/tag_chip_group"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="8dp"
    app:chipSpacingHorizontal="4dp"
    app:chipSpacingVertical="4dp" />
```

### Java — Tag editor
```java
TextInputEditText etTags = findViewById(R.id.et_tags);
ChipGroup chipGroup = findViewById(R.id.tag_chip_group);
Set<String> tags = new LinkedHashSet<>();

etTags.setOnEditorActionListener((v, actionId, event) -> {
    if (actionId == EditorInfo.IME_ACTION_DONE) {
        String tag = etTags.getText().toString().trim();
        if (!tag.isEmpty() && !tags.contains(tag)) {
            addTag(tag);
            etTags.setText("");
        }
        return true;
    }
    return false;
});

private void addTag(String tagText) {
    tags.add(tagText);
    Chip chip = new Chip(this);
    chip.setText(tagText);
    chip.setCloseIconVisible(true);
    chip.setCheckable(false);
    chip.setChipBackgroundColorResource(R.color.chip_tag_bg);
    chip.setOnCloseIconClickListener(v -> {
        tags.remove(tagText);
        chipGroup.removeView(chip);
    });
    chipGroup.addView(chip);
}

public List<String> getTags() { return new ArrayList<>(tags); }
```

---

## 10. Rich Text / Markdown Input

### Dependency
```groovy
// Markwon for rendering Markdown
implementation 'io.noties.markwon:core:4.6.2'
implementation 'io.noties.markwon:editor:4.6.2'
```

### Java — Markwon Editor
```java
Markwon markwon = Markwon.builder(this)
    .usePlugin(HtmlPlugin.create())
    .build();

MarkwonEditor editor = MarkwonEditor.create(markwon);

EditText editText = findViewById(R.id.et_markdown);
editText.addTextChangedListener(MarkwonEditorTextWatcher.withProcess(editor, editText));

// Preview
TextView preview = findViewById(R.id.tv_preview);
editText.addTextChangedListener(new TextWatcher() {
    @Override public void beforeTextChanged(CharSequence s, int st, int c, int a) {}
    @Override public void afterTextChanged(Editable s) {}
    @Override public void onTextChanged(CharSequence s, int st, int b, int c) {
        markwon.setMarkdown(preview, s.toString());
    }
});

// Toolbar actions
btnBold.setOnClickListener(v -> wrapSelection(editText, "**", "**"));
btnItalic.setOnClickListener(v -> wrapSelection(editText, "_", "_"));
btnCode.setOnClickListener(v -> wrapSelection(editText, "`", "`"));

private void wrapSelection(EditText et, String prefix, String suffix) {
    int start = et.getSelectionStart();
    int end = et.getSelectionEnd();
    String text = et.getText().toString();
    String selected = text.substring(start, end);
    et.getText().replace(start, end, prefix + selected + suffix);
    et.setSelection(start + prefix.length(), end + prefix.length());
}
```

---

## 11. Switches

### XML
```xml
<!-- Material Switch (MD3) -->
<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/switch_wifi"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Wi-Fi"
    android:checked="true"
    app:thumbIcon="@drawable/selector_switch_thumb" />

<!-- Legacy SwitchMaterial (MD2) -->
<com.google.android.material.switchmaterial.SwitchMaterial
    android:id="@+id/switch_legacy"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Toggle" />
```

### Java
```java
MaterialSwitch sw = findViewById(R.id.switch_wifi);
sw.setChecked(SharedPrefs.getWifiEnabled());
sw.setOnCheckedChangeListener((btn, isChecked) -> {
    SharedPrefs.setWifiEnabled(isChecked);
    updateWifi(isChecked);
});
```

---

## 12. Checkboxes

### XML
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <com.google.android.material.checkbox.MaterialCheckBox
        android:id="@+id/cb_option_a"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Option A"
        android:checked="true" />

    <com.google.android.material.checkbox.MaterialCheckBox
        android:id="@+id/cb_option_b"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Option B" />

    <!-- Indeterminate (parent checkbox for select-all) -->
    <com.google.android.material.checkbox.MaterialCheckBox
        android:id="@+id/cb_select_all"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Select all"
        app:buttonCompat="@null" />
</LinearLayout>
```

### Java — Tri-state (indeterminate)
```java
MaterialCheckBox cbAll = findViewById(R.id.cb_select_all);
MaterialCheckBox cbA = findViewById(R.id.cb_option_a);
MaterialCheckBox cbB = findViewById(R.id.cb_option_b);

// Parent checkbox
cbAll.setOnCheckedChangeListener((btn, isChecked) -> {
    cbA.setChecked(isChecked);
    cbB.setChecked(isChecked);
});

// Child checkboxes update parent state
CompoundButton.OnCheckedChangeListener childListener = (btn, isChecked) -> {
    boolean allChecked = cbA.isChecked() && cbB.isChecked();
    boolean noneChecked = !cbA.isChecked() && !cbB.isChecked();

    cbAll.setOnCheckedChangeListener(null); // prevent loop
    if (allChecked) {
        cbAll.setChecked(true);
        cbAll.setIndeterminate(false);
    } else if (noneChecked) {
        cbAll.setChecked(false);
        cbAll.setIndeterminate(false);
    } else {
        cbAll.setIndeterminate(true);
    }
    cbAll.setOnCheckedChangeListener(parentListener);
};

cbA.setOnCheckedChangeListener(childListener);
cbB.setOnCheckedChangeListener(childListener);
```

---

## 13. Radio Buttons

### XML
```xml
<RadioGroup
    android:id="@+id/radio_group_gender"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <com.google.android.material.radiobutton.MaterialRadioButton
        android:id="@+id/rb_male"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Male" />

    <com.google.android.material.radiobutton.MaterialRadioButton
        android:id="@+id/rb_female"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Female" />

    <com.google.android.material.radiobutton.MaterialRadioButton
        android:id="@+id/rb_other"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Other" />
</RadioGroup>
```

### Java
```java
RadioGroup radioGroup = findViewById(R.id.radio_group_gender);
radioGroup.setOnCheckedChangeListener((group, checkedId) -> {
    String gender = "";
    if (checkedId == R.id.rb_male) gender = "male";
    else if (checkedId == R.id.rb_female) gender = "female";
    else if (checkedId == R.id.rb_other) gender = "other";
    onGenderSelected(gender);
});

// Get selected
int selectedId = radioGroup.getCheckedRadioButtonId();
MaterialRadioButton selected = radioGroup.findViewById(selectedId);
String selectedText = selected.getText().toString();
```

---

## 14. Segmented Controls

See `02_material_design_3.md` § Segmented Buttons for full reference.

```java
MaterialButtonToggleGroup group = findViewById(R.id.segmented_group);
group.check(R.id.seg_option_1);

group.addOnButtonCheckedListener((g, checkedId, isChecked) -> {
    if (isChecked) {
        if (checkedId == R.id.seg_option_1) showView1();
        else if (checkedId == R.id.seg_option_2) showView2();
    }
});
```

---

## 15. Date & Time Selectors

See `01_material_design_2.md` § DatePicker & TimePicker for full reference.

### Combined DateTime picker (Java)
```java
private void showDateTimePicker(Consumer<Long> onResult) {
    MaterialDatePicker<Long> datePicker = MaterialDatePicker.Builder.datePicker()
        .setTitleText("Select date").build();

    datePicker.show(getSupportFragmentManager(), "DATE");
    datePicker.addOnPositiveButtonClickListener(dateMs -> {
        MaterialTimePicker timePicker = new MaterialTimePicker.Builder()
            .setTimeFormat(TimeFormat.CLOCK_12H)
            .setTitleText("Select time").build();
        timePicker.show(getSupportFragmentManager(), "TIME");
        timePicker.addOnPositiveButtonClickListener(v -> {
            Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("UTC"));
            cal.setTimeInMillis(dateMs);
            cal.set(Calendar.HOUR_OF_DAY, timePicker.getHour());
            cal.set(Calendar.MINUTE, timePicker.getMinute());
            onResult.accept(cal.getTimeInMillis());
        });
    });
}
```

---

## 16. Multi-Select Chips

### Java — Multi-select filter chips
```java
ChipGroup chipGroup = findViewById(R.id.chip_group_filter);
String[] filters = {"All", "Electronics", "Fashion", "Books", "Food"};

for (String filter : filters) {
    Chip chip = new Chip(this);
    chip.setText(filter);
    chip.setCheckable(true);
    chip.setChipBackgroundColor(createChipColorStateList());
    chip.setTextColor(createChipTextColorStateList());
    chipGroup.addView(chip);
}

chipGroup.setOnCheckedStateChangeListener((group, checkedIds) -> {
    List<String> selected = new ArrayList<>();
    for (int id : checkedIds) {
        Chip c = group.findViewById(id);
        if (c != null) selected.add(c.getText().toString());
    }
    applyFilters(selected);
});

private ColorStateList createChipColorStateList() {
    return new ColorStateList(
        new int[][]{ new int[]{android.R.attr.state_checked}, new int[]{} },
        new int[]{ getColor(R.color.md3_primary_container), getColor(R.color.md3_surface_variant) }
    );
}
```

---

## 17. ImageView & ShapeableImageView

### XML — All common variants
```xml
<!-- Fit to bounds -->
<ImageView
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:scaleType="centerCrop"
    android:src="@drawable/placeholder" />

<!-- Circle avatar -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_circle"
    android:layout_width="56dp"
    android:layout_height="56dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
    app:strokeColor="?attr/colorPrimary"
    app:strokeWidth="2dp" />

<!-- Rounded rectangle -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_rounded"
    android:layout_width="match_parent"
    android:layout_height="180dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.RoundedRect" />

<!-- Diamond -->
<com.google.android.material.imageview.ShapeableImageView
    android:id="@+id/img_diamond"
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:scaleType="centerCrop"
    app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Diamond" />
```

### Java — Glide loading with transitions
```java
Glide.with(this)
    .load(imageUrl)
    .placeholder(R.drawable.ic_placeholder)
    .error(R.drawable.ic_error)
    .transition(DrawableTransitionOptions.withCrossFade(300))
    .diskCacheStrategy(DiskCacheStrategy.ALL)
    .centerCrop()
    .into(imageView);

// Rounded corners via Glide
Glide.with(this)
    .load(imageUrl)
    .apply(RequestOptions.bitmapTransform(new RoundedCorners(dpToPx(16))))
    .into(imageView);
```

---

## 18. Avatar Views

### `res/layout/view_avatar.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <!-- Avatar image -->
    <com.google.android.material.imageview.ShapeableImageView
        android:id="@+id/avatar_image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:scaleType="centerCrop"
        app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle" />

    <!-- Online indicator dot -->
    <View
        android:id="@+id/avatar_status_dot"
        android:layout_width="12dp"
        android:layout_height="12dp"
        android:layout_gravity="bottom|end"
        android:background="@drawable/shape_online_dot" />
</FrameLayout>
```

### `res/drawable/shape_online_dot.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="#4CAF50" />
    <stroke android:width="2dp" android:color="?attr/colorSurface" />
</shape>
```

### Avatar Group (stacked)
```java
// Create overlapping avatar stack programmatically
FrameLayout avatarGroup = findViewById(R.id.avatar_group);
int[] avatarUrls = getAvatarUrls(); // array of image URLs
int overlapDp = dpToPx(20);

for (int i = 0; i < Math.min(4, avatarUrls.length); i++) {
    ShapeableImageView avatar = new ShapeableImageView(this);
    FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(
        dpToPx(36), dpToPx(36));
    params.setMarginStart(i * overlapDp);
    avatar.setLayoutParams(params);
    avatar.setShapeAppearanceModel(ShapeAppearanceModel.builder()
        .setAllCorners(CornerFamily.ROUNDED, dpToPx(50)).build());
    avatar.setStrokeColor(ColorStateList.valueOf(
        ContextCompat.getColor(this, R.color.md3_surface)));
    avatar.setStrokeWidth(dpToPx(2));
    Glide.with(this).load(avatarUrls[i]).into(avatar);
    avatarGroup.addView(avatar);
}
```

---

## 19. Story Views

### `res/layout/item_story.xml`
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="8dp">

    <!-- Story ring + avatar -->
    <FrameLayout
        android:layout_width="72dp"
        android:layout_height="72dp">

        <!-- Gradient ring (seen = grey, unseen = gradient) -->
        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/story_ring"
            android:layout_width="72dp"
            android:layout_height="72dp"
            android:padding="3dp"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            android:background="@drawable/bg_story_ring_unseen" />

        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/story_avatar"
            android:layout_width="64dp"
            android:layout_height="64dp"
            android:layout_gravity="center"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.Circle"
            app:strokeColor="?attr/colorSurface"
            app:strokeWidth="3dp" />
    </FrameLayout>

    <TextView
        android:id="@+id/tv_story_name"
        android:layout_width="72dp"
        android:layout_height="wrap_content"
        android:maxLines="1"
        android:ellipsize="end"
        android:gravity="center"
        android:textAppearance="?attr/textAppearanceLabelSmall"
        android:layout_marginTop="4dp" />
</LinearLayout>
```

### `res/drawable/bg_story_ring_unseen.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <gradient
        android:type="sweep"
        android:startColor="#F9A825"
        android:centerColor="#E91E63"
        android:endColor="#9C27B0" />
</shape>
```

---

## 20. Image Sliders / Carousels

### `res/layout/layout_image_slider.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/slider_pager"
        android:layout_width="match_parent"
        android:layout_height="220dp" />

    <!-- Dots indicator -->
    <LinearLayout
        android:id="@+id/dots_container"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|center_horizontal"
        android:orientation="horizontal"
        android:padding="8dp" />
</FrameLayout>
```

### Java — Auto-scrolling slider with dots
```java
ViewPager2 sliderPager = view.findViewById(R.id.slider_pager);
LinearLayout dotsContainer = view.findViewById(R.id.dots_container);

List<Integer> bannerImages = Arrays.asList(
    R.drawable.banner1, R.drawable.banner2, R.drawable.banner3);

sliderPager.setAdapter(new BannerAdapter(bannerImages));
sliderPager.setClipToPadding(false);
sliderPager.setClipChildren(false);
sliderPager.setOffscreenPageLimit(3);

// Create dots
setupDots(bannerImages.size(), dotsContainer);

// Auto-scroll
Handler handler = new Handler(Looper.getMainLooper());
Runnable autoScroll = new Runnable() {
    @Override public void run() {
        int next = (sliderPager.getCurrentItem() + 1) % bannerImages.size();
        sliderPager.setCurrentItem(next, true);
        handler.postDelayed(this, 3000);
    }
};
handler.postDelayed(autoScroll, 3000);

sliderPager.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
    @Override public void onPageSelected(int pos) {
        updateDots(pos, dotsContainer);
    }
});

private void setupDots(int count, LinearLayout container) {
    container.removeAllViews();
    for (int i = 0; i < count; i++) {
        View dot = new View(requireContext());
        LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(
            dpToPx(i == 0 ? 20 : 8), dpToPx(8));
        params.setMargins(4, 0, 4, 0);
        dot.setLayoutParams(params);
        dot.setBackground(ContextCompat.getDrawable(requireContext(),
            i == 0 ? R.drawable.dot_active : R.drawable.dot_inactive));
        container.addView(dot);
    }
}
```

---

## 21. Video Player UI

### `res/layout/layout_video_player.xml`
```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/video_container"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@android:color/black">

    <!-- Surface/TextureView for actual video -->
    <TextureView
        android:id="@+id/texture_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <!-- Controls overlay -->
    <LinearLayout
        android:id="@+id/controls_overlay"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:orientation="vertical"
        android:background="@drawable/gradient_player_controls"
        android:padding="8dp">

        <!-- Progress bar -->
        <SeekBar
            android:id="@+id/video_seekbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <!-- Controls row -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center_vertical">

            <ImageButton android:id="@+id/btn_play_pause"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_pause"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:tint="@color/white" />

            <ImageButton android:id="@+id/btn_skip_back"
                android:layout_width="32dp" android:layout_height="32dp"
                android:src="@drawable/ic_replay_10"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:tint="@color/white" />

            <TextView android:id="@+id/tv_time"
                android:layout_width="0dp" android:layout_height="wrap_content"
                android:layout_weight="1" android:gravity="center"
                android:text="0:00 / 0:00" android:textColor="@color/white"
                android:textAppearance="?attr/textAppearanceLabelMedium" />

            <ImageButton android:id="@+id/btn_skip_forward"
                android:layout_width="32dp" android:layout_height="32dp"
                android:src="@drawable/ic_forward_10"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:tint="@color/white" />

            <ImageButton android:id="@+id/btn_fullscreen"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_fullscreen"
                android:background="?attr/selectableItemBackgroundBorderless"
                android:tint="@color/white" />
        </LinearLayout>
    </LinearLayout>
</FrameLayout>
```

> For production video: use **ExoPlayer** (`implementation 'androidx.media3:media3-exoplayer:1.4.0'`).

---

## 22. Audio Player UI

### `res/layout/layout_audio_player.xml`
```xml
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="24dp"
    app:cardElevation="8dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp">

        <!-- Album art -->
        <com.google.android.material.imageview.ShapeableImageView
            android:id="@+id/img_album_art"
            android:layout_width="200dp"
            android:layout_height="200dp"
            android:layout_gravity="center"
            android:scaleType="centerCrop"
            app:shapeAppearanceOverlay="@style/ShapeAppearanceOverlay.RoundedRect"
            android:layout_marginBottom="24dp" />

        <!-- Track info -->
        <TextView android:id="@+id/tv_track_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceTitleLarge"
            android:gravity="center" android:maxLines="1" android:ellipsize="end" />

        <TextView android:id="@+id/tv_artist"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?attr/textAppearanceBodyMedium"
            android:textColor="?attr/colorOnSurfaceVariant"
            android:gravity="center" android:layout_marginBottom="16dp" />

        <!-- Seek bar -->
        <com.google.android.material.slider.Slider
            android:id="@+id/audio_seekbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:value="0"
            android:valueFrom="0"
            android:valueTo="100" />

        <!-- Time labels -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">
            <TextView android:id="@+id/tv_current_time"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="0:00" android:textAppearance="?attr/textAppearanceLabelSmall" />
            <View android:layout_width="0dp" android:layout_height="1dp" android:layout_weight="1" />
            <TextView android:id="@+id/tv_total_time"
                android:layout_width="wrap_content" android:layout_height="wrap_content"
                android:text="3:45" android:textAppearance="?attr/textAppearanceLabelSmall" />
        </LinearLayout>

        <!-- Controls -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:gravity="center"
            android:layout_marginTop="8dp">

            <ImageButton android:id="@+id/btn_shuffle"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_shuffle"
                android:background="?attr/selectableItemBackgroundBorderless" />

            <ImageButton android:id="@+id/btn_previous"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_skip_previous"
                android:background="?attr/selectableItemBackgroundBorderless" />

            <com.google.android.material.floatingactionbutton.FloatingActionButton
                android:id="@+id/fab_play_pause"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                app:srcCompat="@drawable/ic_pause"
                app:fabSize="normal" />

            <ImageButton android:id="@+id/btn_next"
                android:layout_width="48dp" android:layout_height="48dp"
                android:src="@drawable/ic_skip_next"
                android:background="?attr/selectableItemBackgroundBorderless" />

            <ImageButton android:id="@+id/btn_repeat"
                android:layout_width="40dp" android:layout_height="40dp"
                android:src="@drawable/ic_repeat"
                android:background="?attr/selectableItemBackgroundBorderless" />
        </LinearLayout>
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```

### Java — MediaPlayer integration
```java
MediaPlayer mediaPlayer = new MediaPlayer();
Slider seekBar = findViewById(R.id.audio_seekbar);

mediaPlayer.setOnPreparedListener(mp -> {
    seekBar.setValueTo(mp.getDuration());
    mp.start();
    startSeekBarUpdate();
});

mediaPlayer.setOnCompletionListener(mp -> {
    fabPlayPause.setImageResource(R.drawable.ic_play);
    seekBar.setValue(0);
});

fabPlayPause.setOnClickListener(v -> {
    if (mediaPlayer.isPlaying()) {
        mediaPlayer.pause();
        fabPlayPause.setImageResource(R.drawable.ic_play);
    } else {
        mediaPlayer.start();
        fabPlayPause.setImageResource(R.drawable.ic_pause);
    }
});

seekBar.addOnChangeListener((slider, value, fromUser) -> {
    if (fromUser) mediaPlayer.seekTo((int) value);
});

private Handler seekHandler = new Handler(Looper.getMainLooper());
private void startSeekBarUpdate() {
    seekHandler.postDelayed(new Runnable() {
        @Override public void run() {
            if (mediaPlayer.isPlaying()) {
                seekBar.setValue(mediaPlayer.getCurrentPosition());
                updateTimeLabels(mediaPlayer.getCurrentPosition(),
                    mediaPlayer.getDuration());
            }
            seekHandler.postDelayed(this, 500);
        }
    }, 500);
}

private void updateTimeLabels(int current, int total) {
    tvCurrentTime.setText(formatTime(current));
    tvTotalTime.setText(formatTime(total));
}

private String formatTime(int ms) {
    int seconds = (ms / 1000) % 60;
    int minutes = ms / 1000 / 60;
    return String.format(Locale.getDefault(), "%d:%02d", minutes, seconds);
}
```
