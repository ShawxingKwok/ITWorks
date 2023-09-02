---
title: Android
weight: 1
---

# Abstract
`MVB` is a new architecture standing for `Model-View-Bridge`. It has only an Android implementation at present, 
but the pattern is applicable to every platform that supports Kotlin. The UI part uses the traditional `View` without 
`DataBinding`, and the Kt part abandons `ViewModel` and `outState`, resulting in much less code, looser coupling and 
clearer logic.

Other points:
- Compose is the most concise declarative UI framework, and also the most flexible in terms of custom views. However, 
its recombination mechanism inevitably reconstructs massive objects, resulting in its low upper performance limit.

- Regarding `navigation parameters` for `Activity` / `Fragment`, I have a proposal on 
{{< newTab Reddit "https://www.reddit.com/r/androiddev/comments/15pa5qf/proposal_android_could_support_activityfragment/" >}}.

- `RecyclerView.Adapter` is most common among components with complex writing styles, whereas my encapsulated 
{{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} arranges sub-items as Compose. 
Therefore, MVB does not fall short compared to Compose apart from custom views. 

- As for the UI part, I have a new pattern that requires official assistance to design visual operation interfaces. 
The goal is to make view source code from the UI department directly usable by Android developers, including visually 
designed animations, of which the details are introduced at last. If this could be implemented, it could reduce most code 
as well as negotiations between UI personnel and developers, which is revolutionary without doubt.

# Setup
{{< tabs >}}
{{< tab "Groovy" >}}
```groovy
repositories {
    maven{ url "https://s01.oss.sonatype.org/content/repositories/snapshots/" }
}
```

```groovy
android{
    buildFeatures {
        viewBinding true
    }
}

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:android-util-view:1.0.8'
    implementation 'io.github.shawxingkwok:mvb-android:1.0.0-SNAPSHOT'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```kotlin
repositories {
    maven("https://s01.oss.sonatype.org/content/repositories/snapshots/")
}
```

```kotlin
android{
    buildFeatures {
        viewBinding = true
    }
}

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:android-util-view:1.0.8")
    implementation("io.github.shawxingkwok:mvb-android:1.0.0-SNAPSHOT")
}
```
{{< /tab >}}
{{< /tabs >}}

# Usage
## rmb
This is the abbreviation of `remember`, meaning remembering the value across the device rotation in 
`ComponentActivity` / `Fragment`. 
{{< codeImg rmb.png >}}

{{< hint info >}}
The initialization of `val` property is `synchronized`. 
{{< /hint >}}

## save
Similar to `rmb`, the values are alive across the device rotation, and furthermore restorable from the process death. 
Saved value types should follow {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg save.png >}}

Or you could append `transform` after `delegator` with the converted values following {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg transform.png >}}

Inner parcelable components couldn't be distinguished unless they are put in `Array`. Pass the arg of 
`KClass<out Parcelable>` in these cases.
{{< codeImg parcelableComponent.png >}}

`MutableStateFlow`, `MutableShardFlow`, and `MutableLiveData` are so common that I made some shortcut functions for 
them.
<br>Take the example of `saveMutableStateFlow`. 
{{< codeImg saveMutableStateFlow.png >}} 

{{< hint info >}}
You could reference the short source code to customize functions to save values of which the types are common but not 
directly savable.
{{< /hint >}}

## observe
The delegated values via `rmb` / `save` are easily observable if it's `Flow` / `LiveData`. The latter observe lambdas 
are active between every `onStart` and `onStop`, which is generally used for linking `value` to `UI state`.
{{< codeImg observe.png >}}

`LiveData` is also observable. However, I strongly suggest using `Flow` instead though it takes some time to learn.    

## Format  
Take the example of simulating the stopwatch page in the IOS clock with concise code. 

<video height="400" controls>
  <source src="stopwatch.mov" type="video/mp4">
</video>

### Components
{{< codeImg stopwatchUtil.png >}}
<br> See {{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} if it's new to you.
{{< codeImg stopwatchAdapter.png >}}

### Static processing
Declare components and enable them in `Activity.onCreate` / `Fragment.onViewCreated` 
{{< codeImg stopwatchFragment.png >}}

### Bridge
Bind variable data source to `view`. 
{{< codeImg stopwatchBridge1.png >}}
{{< codeImg stopwatchBridge2.png >}}

### Actions
I suggest putting fixed listeners here or below.
{{< codeImg stopwatchSetFixedListeners.png >}}