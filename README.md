# App Theme Engine

App Theme Engine is a library that makes it easy for developers to implement a theme system in 
their apps, similar to what's seen in [Cabinet](https://play.google.com/store/apps/details?id=com.afollestad.cabinet) 
and [Impression](https://github.com/afollestad/impression).

# Table of Contents

1. [Gradle Dependency](https://github.com/afollestad/app-theme-engine#gradle-dependency)
    1. [Repository](https://github.com/afollestad/app-theme-engine#repository)
    2. [Dependency](https://github.com/afollestad/app-theme-engine#dependency)
2. [Applying](https://github.com/afollestad/app-theme-engine#applying)
    1. [ATEActivity](https://github.com/afollestad/app-theme-engine#ateactivity)
    2. [Custom Activities and Fragments](https://github.com/afollestad/app-theme-engine#custom-activities-and-fragments) 
2. [Config](https://github.com/afollestad/app-theme-engine#config)
3. [Tags](https://github.com/afollestad/app-theme-engine#tags)
    1. [Background Colors](https://github.com/afollestad/app-theme-engine#background-colors) 
    2. [Text Colors](https://github.com/afollestad/app-theme-engine#text-colors)
    3. [Tint Colors](https://github.com/afollestad/app-theme-engine#tint-colors)
4. [Pre-made Views](https://github.com/afollestad/app-theme-engine#pre-made-views)

---

# Gradle Dependency

[ ![JitPack](https://img.shields.io/github/release/afollestad/app-theme-engine.svg?label=jitpack) ](https://jitpack.io/#afollestad/app-theme-engine)
[![Build Status](https://travis-ci.org/afollestad/app-theme-engine.svg)](https://travis-ci.org/afollestad/app-theme-engine)
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg?style=flat-square)](https://www.apache.org/licenses/LICENSE-2.0.html)

### Repository

Add this in your root `build.gradle` file (**not** your module `build.gradle` file):

```gradle
allprojects {
	repositories {
		...
		maven { url "https://jitpack.io" }
	}
}
```

### Dependency

Add this to your module's `build.gradle` file:

```gradle
dependencies {
	...
	compile 'com.github.afollestad:app-theme-engine:0.1.0'
}
```

---

# Applying

Before we go into details of how you can configure theme colors, you need to know how the theme engine is applied.

### ATEActivity

As seen in the sample project, you can have all Activities in your app extends `ATEActivity`. This will do
all the heavy lifting for you, all that you have to worry about is theme configuration.

```java
public class MyActivity extends ATEActivity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
```

If you were to change theme colors from a visible `ATEActivity`, the changes are reflected automatically
if you use the `apply()` methods discussed in the next two sections.

### Custom Activities and Fragments

If you don't use `ATEActivity`, there's a few things you have to do:

```java
public class MyActivity extends AppCompatActivity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        ATE.preApply(this); // apply primary color to status bar and nav bar
        super.onCreate(savedInstanceState);
        ATE.apply(this);    // apply colors to other views in the Activity
    }
    
    @Override
    protected void onResume() {
        super.onResume();
        if (ATE.didValuesChange())
            recreate();
    }
}
```

You can also apply theming to views in a Fragment:

```java
public class MyFragment extends Fragment {

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        ATE.apply(this);
    }
}
```

---

# Config

By default, Android app themes are static. They cannot be changed dynamically after an APK is built. This 
library allows you to dynamically change theme colors at runtime.

All configuration options are persisted using SharedPreferences, meaning once you set them, you don't have 
to set them again unless you want the value to be changed from what it was previously.

Here are a few configuration methods that can be used:

```java
ATE.config(this) // context
    .primaryColor(color)
    .primaryColorDark(color)
    .accentColor(color)
    .textColorPrimary(color)
    .textColorSecondary(color)
    .coloredStatusBar(true)
    .coloredActionBar(true)
    .coloredNavigationBar(false)
    .autoGeneratePrimaryDark(true)
    .apply(this); // activity, fragment, or view
```

There's also color resource and color attribute variations of the color modifiers. For an example: 
rather than using `primaryColor(int)`, you could use `primaryColorRes(int)` or `primaryColorAttr(int)` 
in order to pass a value in the format `R.color.resourceValue` or `R.attr.attributeValue`.

---

If you want to setup a default configuration the first time your app is run, you can use code like this:

```java
if (!ATE.config(this).isConfigured()) {
    // Setup default options
}
```

---

# Tags

If you haven't used tags before, they can be applied to views directly from your XML layouts:

```xml
<View
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:tag="tag-value-here"
    />
```

The theme engine allows you to apply theme colors to any view using tags. **You can even use multiple tags, separated by commas**:

```xml
<View
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:tag="tag-one,tag-two,tag-three"
    />
```

Here's a list of available tag values:

### Background Colors

You can change the background of any type of view.

1. `bg_primary_color` - sets the background to the primary color.
2. `bg_primary_color_dark` - sets the background to the primary dark color.
3. `bg_accent_color` - sets the background to the accent color.
4. `bg_text_primary` - sets the background to the primary text color.
5. `bg_text_secondary` - sets the background to the secondary text color.

### Text Colors

You can only change the text color of a view that extends `TextView`, which includes `Button`'s.

1. `text_primary_color` - sets the text color to the primary color.
2. `text_primary_color_dark` - sets the text color to the primary dark color.
3. `text_accent_color` - sets the text color to the accent color.
4. `text_primary` - sets the text color to the primary text color.
5. `text_secondary` - sets the text color to the secondary text color.

### Tint Colors

You can tint `CheckBox`'s, `RadioButton`'s, `ProgressBar`'s, `EditText`'s, and `SeekBar`'s. 

1. `tint_primary_color` - tints the view with the primary color.
2. `tint_primary_color_dark` - tints the view with the primary dark color.
3. `tint_accent_color` - tints the view with the accent color.
4. `tint_text_primary` - tints the view with the primary text color.
5. `tint_text_secondary` - tints the view with the secondary text color.

---

# Pre-made Views

Seven views come stock with this library:

1. `ATECheckBox` - tints itself to the accent color.
2. `ATERadioButton` - tints itself to the accent color.
3. `ATEEditText` - tints itself to the accent color
4. `ATEProgressBar` - tints itself to the accent color.
5. `ATESeekBar` - tints itself to the accent color.
6. `ATEPrimaryTextView` - sets its text color to the primary text color.
7. `ATESecondaryTextView` - sets its text color to the secondary text color.

All that they really do is set their own tag to one of the tag values in the previous section,
and then apply theming to themselves using the individual view `apply()` method.
